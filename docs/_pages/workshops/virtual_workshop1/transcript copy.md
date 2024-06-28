
Jonathan Waldrop: All right, good enough. So. I don't have a slide that's sort
of been about me thing, but After the previous talk, I think it's a good place
to start actually because by training I'm an electronic structure theorist. So

Jonathan Waldrop: all the work on the CI related to the nwmx projects. Was kind
of a necessary evil that somebody had to deal with and I didn't duck.

00:30:00

Jonathan Waldrop: so just want to make that clear it I coming at this list of a
point of view from maybe a

Jonathan Waldrop: solid software development background Also, so some of that I
didn't know what the order was going to be here. So some of this is probably
going to be

Jonathan Waldrop: a sort of backing up a little bit more fundamental to start
with and then moving on to more specific stuff I think we're also going to see
some pieces that are pretty similar which makes sense.

Jonathan Waldrop: So that said I'm gonna be talking about how we're currently
handling the ci/cd issues inside the NW Chemex stack. So for those who maybe
don't know and it became access one of the projects that was funded under the X
scale Computing project. it's intended to be the successor for the previous
thing that you can which was monolithic package primarily written in Fortran.
And we wanted to the emphasis for this project. there's a circle different
components but one of the things just a more modern rewrite of the fundamental
pieces using a combination of C++ python with a focus on modularity, which I'll
talk a little bit more about just a moment.

Jonathan Waldrop: So this was aimed at the new generation of X scale
supercomputers. But it's also designed in such a ways that we intended to be
hopefully used by. other machines not just leadership super computers and to
facilitate part of that we utilize this plugin system pretty heavily, which is
a whole other talk, but the key component here is that the NW Chemex

Jonathan Waldrop: stack is very segmented. It's very modular to reuse the word.
which so this is a really simplified ecosystem sort of drawing. and it's very
simplified in the sense that the central sort of pinkish box. This is
internally developed plugins would be

Jonathan Waldrop: a lot of different boxes that mostly represent Different
electronic structure methods that you could apply so for those who are
familiar. They would have mods plugins to handle an stf calculation to handle
an mp2 calculation to handle a couple cluster calculations DFT calculations on
and on and you could have multiple of those that could be developed
differently. So everything inside of the central blue box is the sort of
canonical.

Jonathan Waldrop: nwmx stack these are the things that who identify ourselves
as being part of that project maintain and control and intend to be
interoperable with each other and all of these things we basically require
should be able to reuse the CI stuff that we're going to talk about in that.
But each one of these is a separate repo on GitHub. And so there becomes
questions of how to sync that and maximally reuse things and minimally repeat
things so on so the yellow box here denotes plugins that

Jonathan Waldrop: through utilization of this MD which is the simulation
development environment sort of the jumping off point in our stack for anybody
who wants to start writing

Jonathan Waldrop: some sort of module that does. Something in the chemical
space usually method calculations kind of the idea. But if you yourself wanted
to write your own implementation of SCF using whatever cool or interesting
technology you would start from the same D's. 's box and then you would
probably be one of these yellow boxes of externally developed plugins. And so
part of that is with regards to the CI is we want whatever we've designed. To
also be usable by those people. So as to lower the barrier to entry for them to
be able to contribute and extend and use our components.

00:35:00

Jonathan Waldrop: so I didn't say this before feel free to interrupt but with
this being said, that's our reasoning for why we need sort of this.

Jonathan Waldrop: D's centralized it's a deal centralized. There we go. Sorry I
set up. So this is the part that's gonna get a little bit basic because we kind
of jumped into the CI stuff now backing up a little bit to what is CI and CD. I
can probably go over this pretty quickly.

Jonathan Waldrop: the basic idea is that you're closing the loop between sort
of. development and application or usage of an application. So if you search
the ICD and you read any number of blogs, you will see probably it doesn't
different variations on this image I have on the right.

Jonathan Waldrop: I just picked the one that I thought had the pieces in the
most reasonable places. To a lot of people I think cicd means automation.

Jonathan Waldrop: Which is definitely a key component to the way that it's
done. It's not the singular definition of it, but it is Pretty heavily
associated with the automation of building and testing and deploying code and
continuous fashions that somebody raises an issue with your current version of
your code. You come up with some way to fix it you coat that up and then it's
tested and as soon as it passes test, it's released and everybody has access to
the fix. It's also useful for enforcing different quality controls that you
want to have on your code bases. Things for people are keeping with style
guides making sure they're not introducing warnings or whatever into your build
system.

Jonathan Waldrop: Theory so this last Point kind of doubles back to my leading
statement about my role in this so in theory another useful aspect of sort of
putting together the ci/cd stuff is that a lot of these cases for science
related code feel like it's usually tends to be smaller teams. And so
theoretically you've got a good cd system in place. You can kind of take up
some of the slack of people having to Build and test the code that other people
are putting together. But that's in theory because the problem is right. This
is never just that easy. Everybody's had that experience where they think to
themselves. I have to do this all the time. I should just automate it and then
you start trying to write something to automate it and it becomes more
complicated than even. the original problem that you were trying to solve was.

Jonathan Waldrop: So there's so many different tools that you can use to try to
solve this and every one of them is going to say that. They're the best for
whatever reason or another.

Jonathan Waldrop: and then yeah, like I said you're always going my primary
role is to implement and develop electronic Structure Theory, but we need this
stuff to not break and so somebody has to do that and that's me.

Jonathan Waldrop: So with that in mind the first kind of major point is that
there's so many different options for this stuff. We just heard about using
gitlab for this. We're gonna end up talking quite a bit about GitHub actions.
there's so many others. I just picked a few others, but these are just the top
ones there's An endless list and then down the bottom here. We've got a couple
of options related to containerization, which I'll talk about a little bit
more. They're not necessarily. CI tools and other themselves, but they have
heavy usage in CI potentially. So yeah, the best thing I can say on this one is
to a certain point. You just have to pick whichever one seems the most useful
for you. which one makes the most sense of your brain it just kind of run with
it. and in our case that's going to be GitHub actions, but

00:40:00

Jonathan Waldrop: so our concerns with regards to our project in particular.
like I say we got different repos on GitHub for each one of these different
sort of components and we don't want to have to maintain individual cd
pipelines for each one

Jonathan Waldrop: and so there's a couple of different ways to handle that. You
can try syncing files which in the past when we've done that is always turned
into a mess very quickly. So I'll talk about how we handle that. Currently. We
get operating system differences. Somebody spends a lot of time developing on a
Mac. It's not the same you're gonna find every possible Edge case that you can.

Jonathan Waldrop: So we're C++ project and we have a bunch of dependencies that
are C++ that have to be built from source and those build times take time as
well as we want to be able to cut down on that in some meaningful way of
dealing with multiple programming languages dealing with constantly involving
list of tools to play into every different problem that we have to deal with.

Jonathan Waldrop: So how do we currently handle? sort of the key points of all
this the first one is like I just mentioned containerization. This handles
several of those points from the last slide. This is how we're handling
basically caching dependencies and ensuring at a repeatable. reproducible
environment for each of the tests.

Jonathan Waldrop: Yeah, Dockers the most common version of this. It's the
Dockers fairly synonymous with containers, but I think containers don't
necessarily mean doctor there are other options, but that is what we use so.
This allows us to have a reproducible environment.

Jonathan Waldrop: We know. What circumstances the tests are going to be running
and if they fail it's easy enough to replicate the environment repeat the
Troubleshoot debug whatever and it also serves as a pretty useful. Starting
place for a development environment for individuals who are trying to work on
additions and stuff like that.

Jonathan Waldrop: The other main thing that we use at the moment for sinking
the procedures across the different repos is github's options for reusable
workflows. Which sounds very similar to I believe they Subpipelines I believe
is the term used in the previous talk referring to the gitlab ones. Basically
the key thing here is that

Jonathan Waldrop: you're able to write the procedure one time. and then have
that reference that procedure in a different repositories you don't have to
Fully rewrite it if you make changes in the central location, they propagate
throughout everything that uses it. It's also extensible because You reference
the central workflow inside of a local workflow. Which you can then add
additional components to so that way every repos a little bit different so you
can change up a little bit.

Jonathan Waldrop: so yeah the night what remains is basically just a super
quick tutorial on how that works and get up actions. Like I said, there's got
to be options like this. I think we just heard about one forget lab that's
similar.

Jonathan Waldrop: So as a couple of example cases, these are super toned down
versions of the centralized workflows that we use to handle. Our pull requests
and then the next one will be the merge procedure.

Jonathan Waldrop: So in this case, I say We are taking in a list of compilers
and the version of the documentation it needs to be built and we just Define a
procedure for How that looks like you can see here. GitHub action supports
using containers directly. So we just tell the image that we want to use which
is our build environment one and then it proceeds to run everything inside of
that.

00:45:00

Jonathan Waldrop: And then this unfortunately is considerably more complicated
in the actual versions. But this is the general idea similarly with the merge
workflow simple version bumping and then automate deploying of our
documentation, which is largely generated to this process so the way this looks
inside of each one of the individual repos is just sort of this. So for a pull
request

Jonathan Waldrop: would just specify which compiler versions you currently
worried with and what the name of the document Target would be and it's gonna
run that test automatically and then for the merge case similar thing, but say
you have an additional step that needs to be run on merge. You can add that
here very simply and then it'll do the common sort of required components and
then Do the additional car parts here as well? There's a bunch of other. So
workflows is one way to do this GitHub also has actions in and of themselves
which several of these things could probably be compartmentalized into actions
as well. But

Jonathan Waldrop: that's basically the Crux of it. so at that point we can open
up to questions and

Jeffrey Wagner: I'm kind of curious on the n minus 2 slides from here

Jeffrey Wagner: so you had Yeah,…

Jonathan Waldrop: hand control back to Ryan

Jeffrey Wagner: if you go back to slides. And maybe even one more. Basically,
there was a part where you said. Yeah, here it is built library and run test
here on the right line 34. It's like This is a mess. So I'm hiding a lot of
stuff. and then in the next slide, you're cool, but we already put that big
mess in this other work in this other repo Maybe it's one more ahead. It's
basically wherever you're here line 12 you're like, okay, I'm just grabbing all
the gobbledygook from that other repo from this branch. If you're making a
change to this repo that requires a change in those build steps.

Jonathan Waldrop: Yeah.

Jeffrey Wagner: Do you have to to the workflow repo and make a separate version
to workflow That has a commentary changes in the adult steps.

Jonathan Waldrop: Yeah.

Jonathan Waldrop: Something like that. Yes. So when I say these are super toned
down there's

Jonathan Waldrop: decent amount of switches that are pulled out of these inputs
that it depends on And there's not an explicit rule for this and any sense, but
it depends on how common enough the problem is going to be. I think in our case
as to whether or not it's easy enough to just throw a switch on here that says
don't do X. Whereas if it's a truly.

Jonathan Waldrop: If it's truly unique to the repo in specific here each one of
these steps the way that they're actually coded up in our case. They're Fully
Optical it's even included here. So you just got the simple conditional that
says if it's empty don't do it. So if whatever the test Library case is here.
Isn't sufficient for the specific Library. You can just turn this off use
whatever components of it are actually relevant and then you could extend this
with the more specific version of whatever that repo needs. That makes sense.

Jonathan Waldrop: Yeah.

00:50:00

Adrien Bernede: My question is yeah. Are you using containers in production in
that case? fine

Jonathan Waldrop: Not really. No in our case. Honestly, I think it is a
solution to we don't have dedicated runners for this stuff anymore. And so it's
just a way of

Jonathan Waldrop: replicating of ensuring a reproducible environment for the CI
set up. because you can replace the containerization if you just have a
dedicated machine a self-hosted broader or somewhere The stuff that you want
installed on it to be honest the get ub. Runners themselves have quite a lot of
stuff already installed on them. So some of that is Maybe. I don't say
Overkill. There's other solutions that we just didn't have available to us at
the time and also it does make it easier in the sense again of it's a

Jonathan Waldrop: half put together development environment of sorts. because
like I said before I primarily work on my Mac which sometimes behaves
differently than The Linux machines at the vast majority of the stuff is
actually going to run on. so it has a few different uses but there's definitely
other options that maybe simpler or even more effective. It's just what we're
using for the time being.

Jonathan Waldrop: Yeah, we I mean

Jonathan Waldrop: I think that. the easiest answers that currently we just
don't sort of deploy in a container, but there's really nothing stopping us
from I don't think that's a Believer at this point.

Jonathan Waldrop: This is one thing that I have to do. So there's multiple and
solve necessarily every issue that we've come up with so far. We literally have
a wish list of things that needs to go into the CI. but yeah, no deploying it
as a container is definitely a useful option in some cases.

00:55:00

Jonathan Waldrop: The vast majority of ours are not I don't know. Maybe I made
it seem that way a lot of our stuff is actually Straight it's very similar in
their individual steps. the

Jonathan Waldrop: number of inputs is maybe I should.

Jonathan Waldrop: the number of inputs seems like A lot to me at times, but
maybe not in the sense of you've ever gone and looked at. the yaml files for
some of the GitHub actions and stuff out there, but it's mostly just cases of
Jonathan Waldrop: cases of honestly trying to do maybe too much at one time is
even part of the thing. but now a lot of our repos are fairly straightforward
the

Jonathan Waldrop: probably the most unique one is one called Kim cash, which
Has an additional step where it has such an After changes have been merged into
Master it Otto generates a different. branch that has to have a lot of

Jon R: Maybe I missed it.

Jonathan Waldrop: sort of generated files pulled from

Jon R: But how is it pulling down the source code for each of the projects that
it's building?

Jonathan Waldrop: outside reference sources But not actually a lot of our stuff
is actually pretty similar.

Jonathan Waldrop: Yeah.

Jonathan Waldrop: These lines 31 and 41 44. In terms of GitHub actions see that
the checkout action pulls the source for the current thing…

Jon R: Gotcha. Thanks.

Jonathan Waldrop: if you're talking about for dependencies and stuff like that.

Jonathan Waldrop: Our projects are built with cmake and specifically we use an
additional library for that called C Mays which Ryan can talk about quite a bit
more than I can because he's the author.

Jonathan Waldrop: If I am it wouldn't be by that name apparently.

Jonathan Waldrop: Yeah, okay. So yeah. Okay, but no. I don't know that…

Ryan M. Richard: So this Workshop is being sponsored by them.

Jonathan Waldrop: but I don't know. specifically in that case

Jonathan Waldrop: Yeah, I think that's just warming in that point.

Ryan M. Richard: But let me maybe take this one. So I met as Erik pointed out
in the chat. I'm actually one of the 2024 bssw fellows in. My project is
actually focused on multi-project CI CD and in particular. I'm trying to put
together resources to make this easier for the community and I would say I'm
still sort of in information finding phase. it seems like there's people that
have been out there doing this but they haven't been posting blogs or things
like that. I found a couple resources. I've got a website started. It should
have been linked to in the call that went out. Let me get the website real
quick here and put it in the chat.

01:00:00

Ryan M. Richard: Yeah, so I sort of been collecting resources there as I go and
yeah, I was really surprised was this part why I proposed the project was I was
surprised at how little I was. They finding so I find an example of someone's
here's how you use a reusable workflow or something, but there wasn't really
any best practices. There wasn't sort of tutorials of how to you start setting
this up for a bunch of projects. it was all really written within the context
of you have a single CI CD project and you

Ryan M. Richard: kind of know that you want to scope out then they just left it
up to you. So long-winded explanation would be basically right now. I'm sort of
in a fact-finding phase. And yeah, I'm really hoping that by the end of the one
year research here. I'll have at least some tutorials and there'll be videos
from this Workshop as well as other workshops, and hopefully it'll be a lot of
resources that will make this a lot easier for people going forward.

Jeffrey Wagner: I have a separate question. But if we're done with that last
one.

Adrien Bernede: I was gonna react to Ryan's statements. I'm interested in what
he's doing and I would say the reason why it's for example, haven let's say
published. I gave a talk about this and then 2023 and then it submits. Because
it I mean it was one of the possible and topics so I went there and presented
what we are doing but my impression is that in the Regis case we tailored the
implementation of the CI and shared CI and shared walkthrough and sharing and
soon multi project CI to

Adrien Bernede: Really what the user? I'm still doing how are users are doing
and if they use spec we use spec and they mostly use cmake they use gitlab. We
use gitlab. So it makes it and basically I'm doing my best to Switch to this. I
mean, to use the same things so that I can be sure that they use they that will
make our libraries work. Well in the user contexts, so if it were in another
context like here if there is no Clear Choice on the package manager on Clear
Choice and something else it becomes and even if there is a clear trusted
speaker, it's just becoming another solution right? And so why writing

Adrien Bernede: A generic tutorial seems difficult. Although discussing best
practices is definitely Interesting in the radius umbrella projects that one of
the first thing we did was to probably publish some common best practices with
our project. We agreed on how although there were mostly about software
developments and testing but not going as fast talking about Moon time projects
CI. so yeah Best Practices about this. Yeah, it's

Ryan M. Richard: Yeah. like I said, I'm sort of still in effect finding part,
and I'm also right now really largely focusing on GitHub, but I mean The best
practices that are starting to emerge are things like these? So if you're the
maintainer where do you actually store these workflows? How do you store them?
Because GitHub has certain restrictions on if you put them all in one repo. you
can't have them version to Parts. What are the directory structures people are
using one of the other things that's interesting and I'm investigating actually
at the moment is how can you do per repo actions and then sort of piggyback off
them? that's a whole other.

01:05:00

Ryan M. Richard: Conversation but imagine that a repo so you have what repo
that's used by a bunch of other repos. Can that repo actually? By the action
you need to build and test it that way everybody can just reuse the action
that's actually maintained by that repo and you can then quickly put together
your own workflow file. These are the sorts of best practices. I've been really
interested in learning trying to figure out what strategies people have adopted
to basically reduce the code duplication and what the drawbacks are. what is
working what isn't working Etc?

Ryan M. Richard: and so I guess yeah, if we're going to switch over to sort of
generic conversation that was actually giving my first question since we did
just care about to actual instances of cicd for multiple projects. I'm curious
if there is best practices that you have encountered all or lessons or even
Lessons Learned is there something you tried? That didn't work This is open to
I guess I just opened everyone not just the two presenters.

Jon R: So before we get into General discussion, are you taking security into
account when you're suggesting that organizations start including? GitHub
actions developed by other people one piece that has been conspicuously absent
from this conversation is build secrets and that seems like a pretty good way
to lose your build secrets. If you're including cicd code from other projects.

Ryan M. Richard: So I will admit that that is not something I have personally
looked into too much. I know GitHub has mechanisms in place for the secrets
everything we've been dealing with is open source, and we don't need access
private repos or anything like that. So we Have No Secrets other than for when
it's deployed and we control that action.

Jon R: If you're building a python module that presumably you're still
uploading the Pi Pi and so you've got to inject account credentials in order to
upload to the public repositories.

Ryan M. Richard: So we to upload to Popeye that would be an action that we
write. That's not an action.

Jon R: right

Ryan M. Richard: We would be taking from a different repo.

Jon R: my concern I guess is that if I'm depending upon actions from another
repo and in the same context that I am pushing to pipei. Those build secrets
are part of the same environment that I'm potentially exposing to the actions
that I'm imported.

Ryan M. Richard: So, I mean I think you can separate your builds to whatever
granularity you want. So if you are worried about Action a seeing action B. You
can have action being a completely separate workflow.

Jon R: All Maybe this is me being more familiar with Jenkins and gitlab than it
is being familiar with and not being as familiar with GitHub.

Ryan M. Richard: Yeah, I get home. again, I don't have to deal with the Seekers
is up much as I know some other people do because everything we have is public
and we're just looking at other public repos and really the secrets only come
in when we're actually doing pushes and stuff that require administrative
access, but GitHub seems to have pretty good system in place for Secrets as far
as I can tell like I said it might be Not as great if you deal with a lot of
private things or secret stuff that you can't let GitHub see because that's the
other thing is I don't know how this really would work. If you're using Runners
on your local computer that aren't allowed to talk to get home. I mean, there's
a lot I don't even know on a calm edge cases. There's a lot of scenarios that I
haven't looked into

Ryan M. Richard: And I mean, I'll be honest. I don't think I'm gonna have time
to worry about all these but even just starting to put together some resources
for how to do this. I think we'll be helpful and I couldn't find any resources
to even begin. and I mean, I would certainly welcome more help on how to do the
cybersecurity aspects of it. But I'm definitely not a cyber security expert.
I'm actually an electronic structure theorist like Jonathan, I sort of
self-taught completely on all these things too. And so yes cyber security is
way outside my wheelhouse.

01:10:00

Jon R: I met Sandia rather than Ames, but I guess let me know. I'll put my
email address in chat.

Ryan M. Richard: Okay, sounds good.

Ryan M. Richard: Any other questions?

Jon R: I guess our sorry one more you're gonna be focused on GitHub. Then it
feels like in the federal land get lab tends to be a lot more. prolific

Ryan M. Richard: I seen sort of both it seems like Get lab is more common when
people are worried about the security but GitHub it seems like is the more
common Choice when you're trying to share things.

Ryan M. Richard: So I think there's a tension between the two. I don't think
they're neat. They're necessarily mutually exclusive. I don't think there's
really anything inherent to get lab that prohibits it from being a useful
platform for sharing with other people, but it also might just be my niche of
doe over in computational chemistry that's how it is for. When you use GitHub
versus gitlab, I not as familiar with other disciplines. So perhaps other
disciplines are doing different things and I guess yeah, the point being are I
am initially focusing on GitHub because that is what I use and that's what I'm
more familiar with. And as far as I know it's also the more popular platform.
So I think that'll hit the most people time allowing. I did want to look in to
get lab and try and put together the equivalence of the tutorials forget label.
We'll see how much time I have. This is already.

Ryan M. Richard: That's a good question. I'm not sure if anyone uses
Enterprises. now I just lost my truth. I'm sorry.

Ryan M. Richard: Okay.
Jeffrey Wagner: and I have a different topic if I can start on that. in the
last talk there was this concept of we have our libraries and…

Ryan M. Richard: Sure.

Jeffrey Wagner: then there is internal plugins and then there's external
plugins and it wasn't clear to me that jurisdiction for the build system wants
to ensure that the internal plugins work. and I guess this is both a technical
and a social question is What's your contract with the external plugins? do you
run the testing on them? But you're allowed to merge step. Even if the external
plugin tests fail is it like you kind of take ownership of the external stuff?
Do you keep in contact with maintainers of that? I'd be interested to know
about that side.

Jonathan Waldrop: You're going to talk about it Ryan.

Ryan M. Richard: Yeah, it was your talk.

Jonathan Waldrop: Yeah.

Jonathan Waldrop: Let me think about it for instance. So the external plugins.
I think the answer is all the above. So. Our intention Okay, so

Jonathan Waldrop: Our intention is more or less for the way that we've built in
WQMX individuals who sort of want to use As much of our infrastructure as they
want and ignore as much of our infrastructure as they want have. Hopefully the
freedom to do that basically and so in terms of what's our relationship within
I think it's going to be incredibly by case basis. so our angle from this side
is more or less anybody who wants to write.

01:15:00

Jeffrey Wagner: Thank you and thanks for everything you do. I've heard great
things about NW.

Ian McInerney: If I can just maybe say about a different ecosystem. I've seen
these multi-ci things in the Julia ecosystem coming out now where they do more
Downstream testing like you were saying specifically in their optimization
world, the math opt interface people actually have a GitHub action that will
install each kind of dependent solver and run the tests of the dependent solver
against their releases. And so essentially they have a actions file that lists
the various solvers. And so I think it's basically if someone wants Theirs to
be tested they can add it to that release or to that basically that GitHub
actions file and

Ian McInerney: it will run through their Pipeline and then they can kind of
either flag up errors to the authors or maybe even send a PR kind of themselves
from the developers of math opt interface. I actually just merged one last
night where they shot they saw crashing their test pipeline of our package and
they just sense of straight to a saying here this fixes the crash.

Ian McInerney: In your package that was Unreal actually unrelated to what we
were changing but we saw it anyway, so that's one thing that works. And so
actually I'll put the kind of workflow in the chat into Julia ecosystem.

Ryan M. Richard: That's really interesting. Do you know of any other
ecosystems…

Ian McInerney: We kind of have been seeing a lot more of this Downstream
testing…

Ryan M. Richard: where that's occurring?

Ian McInerney: where some of these more core packages will actually do tests on
things that Implement their interface or things that kind of tie into them and
are used with them. I haven't seen it in many other. Language ecosystems but I
know so in Julia those kind of two communities the optimization one and they
call it the cyml ecosystem scientific machine learning. They do a lot of
interrelated packages for solving all kinds of things like that that have weird
dependencies kind of factored in there. So those are the two places. I really
seen it. I don't know if people do something similar in python or something. I
can actually maybe have a quick look at some packages that might do that. I
don't think they've gotten as far as doing that yet.

Ian McInerney: I think GitHub actions is free for public repos. No matter what
so because it's a public repo and all these packages are from public repos as
well. They actually don't hit any sort of GitHub CI charging. If you were
trying to do this in private repos on GitHub and using their hosted Runners,
you probably have to, run into a large billing problem. But if you're doing it
on your own actions on your own Runners don't think you would have that problem
either. It's just if you're doing private with them.

Adrien Bernede: On guitar, there is no cost for running the GitHub actions for
open source project, but there are rate limits. I mean so you cannot run as
many as you want. all the time you're limited in terms of resource. So it's
still an interesting question. I think. in that sense

Adrien Bernede: I'm going to add that and a slightly different use case is that
at least So the package manager we use is testing we can add. So they are
building when they call environments where you can add your specs and we have a
radio specific environment. They test each time. There is a change in stock in
the package manager to make sure that there is nothing broken in our package by
changing another package. For example, when they concretize everything so
concreti being resolving the dependencies and building a consistent dependency
tree. So it's not exactly testing new changes in our code, but it's still a way
to test multiple element how Project work with one another and the package
management labor. Never.

01:20:00

Ryan M. Richard: So does anyone else have any sort of topics conversations
follow-ups to that? I don't want to completely derail us if there's more
follow-up there. Otherwise, like I said, I have some charge questions meant to
sort of spur additional conversation so give people a couple seconds here to
cut me off.

Ryan M. Richard: I will admit that that is on the radar for us. we have not
gotten to it yet. But especially this scenario you were just talking about with
the sea triggering the putt the python that is definitely something we're very
interested in.

Jon R: So I've done a little bit of that on Jenkins. I feel like it would be
harder on a gitlab and I'm not familiar with GitHub. Jenkins makes it pretty
easy to call other pipelines from a pipeline.

Adrien Bernede: Okay, I think it's easy to do in gitlab 2 to call another
pipeline, but maybe I don't have a key idea of what would you wear doing
exactly and Jenkins my answer on my side. I would say Multiplex CI comes first
win and CD would be a Consequence on us a nice step forward after that but I
mean a nice next step. But yeah first

Jon R: I do feel like Secrets would come into play more on the CD front as
well.

Jon R: So I could use some feedback on a pattern that I've been using for my
own personal projects before I start alluding too many work projects with So
one of the pieces that I like to do is get sub modules. in order to basically
have a single cicd repository that then sort of federates out get sub modules
to each of the projects that I'm running. CI for So that those projects can
have their own repositories and track their own changes without having to be
part of a single. Monolithic codebase and still share the same CI. I know lots
of people get submodules to be evil because of the way that it can. confuse
folks with forgetting to

Jon R: refresh some of the sub-modules and especially if you forget to do it
recursively potentially. but I feel like in the context of CI where it's almost
always going to be a CI tool using these repositories rather than a human.
Maybe it's acceptable. Does that sort of align with how you guys I mean, I
guess on the GitHub side you're using. An action to sort of pull the code but
is that a common pattern that anybody else is using?

Adrien Bernede: I think that's interesting so my first thought was when using
gitlab you cannot include a German description of pipeline from a sub module in
your parents parent projects. So for example when I designed the shots CI in
gitlab, it was out of the question to use a sub module to share this Yan
implementation, for example Because you cannot go into a certain module to get
your CI configuration in gitlab.

01:25:00

Adrien Bernede: What you can do what I'm doing is I'm sharing this back
configuration as a sub module in the project. So every project has a radio
stock configs with the spec configuration in it. And I'm propagating the
register configs updates as a certain update. However, it's different from CI.
So the way I share the CIA configuration is we remote includes So I'm pointing
at a Maisha I'm specifying your url with. And to my shared repository and
specifying a version I want to use and that's it.

Jon R: so it feels maybe like there's a Continuum here between multi-project cd
where the projects are a loose Federation and multi-project cd where they're a
tight Federation all using the same build tools. And where you have a little
bit more control over how the projects are built and ensuring that it's
consistent.

Jon R: in my case I try to avoid having to include the animals of the sub
projects where I can. And instead try to just Consistently set up higher build
a Docker file. rather than needing to include cicd code from the sub-projects
at that way all of the CI code is managed centrally in that repository that
manages the others but admittedly that requires a lot of tight control over.
How projects are built.

Adrien Bernede: in our case. It would require the project to delegate the CIA
to another bigger project. It seems if Anderson correctly what you're doing and

Jon R: yeah, I think that's accurate as well part of the goal of a structure
like this is to shift the ci/cd processes to the people who care about those
rather than the people writing the python code that just want to release a
package on pipei.

Jon R: But again, I think it depends upon how tightly the projects are couples
and how diverse they are. How big they are maybe as well.

Jon R: So one big benefit there is that my developers working on python
modules. Don't even have to think about cicd They just have to write their
setup. and I can handle it for them.

Adrien Bernede: Yeah, our use cases is I guess different. We have Independence
math libraries that can be used in many contexts. So they are not tight tired
tied to one. Super projects and they all should care about the CIA by themself,
and that's fascinating things.

Adrien Bernede: And something I didn't insist on is that the shared CI
implemented this shell implementation is just one thing you can add to your CI
so you can add set timelines that are predefined in one stage of your CI that
you can still do. Whatever you want in your opinion in your CNN and implement
the rest of your C the way you want. It's just if you want to jump in The
efforts to sharing building the same specs building in the same context to
Target for example being big users. Then our project can add our shots AI to
them to their implementation as an extension. It can be seen as both something.

Adrien Bernede: Central for must protect is the only CI there is in the CI but
many have a CI on GitHub too. So you have this sharing GitHub and then we
mirror the project To run on internal machines and all the CIS described with
the shots CI.

01:30:00

Ryan M. Richard: If no one else has anything else? One question. I've had that
I've been curious to hear and I'd be curious to hear what this group and say,
how do you test your ci/cd?

Jeffrey Wagner: weirdly I have a snide answer to this and I'm not sure that I
have a real answer weirdly code Cubs. We've had instances where we've
accidentally. Earned off a large part of our testing framework and we catch
that one code comes in and it's like hey 30% of your source code is no longer
covered.

Jeffrey Wagner: So that's sort of the boneheaded answer. It's like who watches
the Watchmen cocoa?

Ryan M. Richard: So I guess to expand on that almost a Matt's assessment chat.
What does that mean? So I mean you're gonna treat your I it's infrastructure.
Standard best practices are you should have unit tests or integration tests
something for that left along those lines for your CI also and in our
experience, it's become more important when you start doing multi ci/cd,
because if you now have a bunch of projects depending on that CI CD if you're
going to do a little changes to the CI, you're no longer just bricking the one
project you're breaking the entire ecosystem if there's something breaks. So
having tests in place I think is very important and I haven't found a really
great way. The code coverage is a very interesting idea. And so yeah, I was
curious if anybody else is running into this problem or if they know of
solutions

Matt Thompson: Yeah, actually, I have a couple more thoughts on this. I think
one of them is that we don't really build our CI. I mean we're relying on
infrastructure that Microsoft has put probably internet figures into so just a
lot of really rigorously testing our own CF pipelines. I think is actually not
something that I think about on day to day basis or even maybe like a Sprint
discount.

Matt Thompson: but right and I would jump what Jeff just said in that If our
code coverage is all red then we've completely screw things up. But that's not
a complete solution. I mean, it's super efficient because it's one tool that
gets a pretty good signal of some of the information but I guess I have one
concrete example that. Completes with that is that In one of my test suits. I
have a really really important set of regression tests. And just because of the
way that I wrote them they triggered a particular combination of things in my
job Matrix. And as I updated some upstreams I accidentally turned that off
because those regression tests were always passing. I never thought of checking
to see if they were still running in the future. So I inadvertently just turned
off this really important set of tests.

Matt Thompson: And I don't have a solution to that. So I think I'm maybe in the
same shoes as you are with respect to not knowing what to do there.

Ryan M. Richard: I do know that some of the actions on the marketplace have
units or they have testing and also Test passing that might be how they're
doing it. I haven't looked into it yet. This is sort of a question. I've
Ryan M. Richard: only recently started asking Yeah, no, I think that that's a
good place to look for potential Solutions. And that is potentially also how to
go about testing these.

01:35:00

Adrien Bernede: as the core developer of a shelled CI I'm mostly doing testing
the changes I'm making myself but what I'm not showing the sense running things
maybe in separate reposed not to overwrite statues. I mean not to interfere
with the development of the project but

Adrien Bernede: What can happen is when there is a change in gitlab or in the
systems Etc in general? It's catched very. quickly by the CIA constantly
running on the projects so I don't really have a way to test my own CIA tried
for some specific features. when I pull things from the web or something like
that, I try to set up a pipeline test just to test a brand blank project using
my sharing implementation. just test it. but it hasn't proved very useful so
far are very

Adrien Bernede: Strongly. Yeah ntial. It's just one thing I can do but it's not
essentially the essential source of testing of finding the bags. It's a weird
concept Testing the CIA itself it in the sense. You don't build it. It's just
running all the time somehow. So yeah, you your ing impacted by the issues very
quickly.

Jon R: I think at the end of the job looking for the artifacts that you were
supposed to build or generate. and just checking at the end might be a
reasonable step but then you're unit tests are part of your ci/cd pipeline. And
so how do you test those? So I think there's always going to be a Turtles all
the way down element.

Jon R: That is another benefit. I've found of having my CI in its separate from
the original projects. Is that I can very easily just create branches of my CI?

Jon R: Separate from the main branch that is used for most deployments and
builds.

Jon R: And I can test that without sort of overriding or interrupting the
ongoing builds. in a separate branch

Ryan M. Richard: So any other comments on this?

Jeffrey Wagner: Do you have more provocative questions.

Ryan M. Richard: I have more questions If you have something else to ask. I
mean, feel free to go. I mean I hope that this is a opportunity for the
community just to talk also, it doesn't have to only only be answering the
questions I have.

Jeffrey Wagner: I'm actually learning a ton from this.

Ryan M. Richard: Okay.

Jeffrey Wagner: I'd love to have you guide the discussion.

Ryan M. Richard: All The next question I had is I'm just trying to get on idea
of what sorts of pieces of ci/cd people have tried to reuse.

01:40:00

Jeffrey Wagner: Yeah. I'm trying to think we haven't put any I mean, so I think
where you guys are playing the game on normal or hard mode? I think Matt and I
are kind of playing it on much easier mode because all of our code is Python
and all of our dependencies are on contact Orange. and so many of these things
aren't that complex and I would even say there is some level of trust that you
need to be reusing somebody's actions on GitHub. And so we don't go looking all
that hard. when we have a really particular need we'll go up and see if we can
find it in the GitHub actions Marketplace or whatever, but for the most part We
just spin our own because We know that we ship to a lot of Industry. Users
We're partly funded by Pharma companies and so for us security is such a big
deal and a little bit of repetition in RCI. Isn't that big of a deal?

Jeffrey Wagner: And so we actually don't really reuse many of the actions from
the GitHub Marketplace.

Matt Thompson: I think Ryan there might be sort of two classes of answers to
your question or maybe two different ways of asking it one is have you written
something that then you find your reusing at a bunch of your own pipelines and
the other is are you reusing something in general which is to say something
that somebody else wrote and we use tons and tons of stuff to get up themselves
or some of their party wrote a bunch of

Matt Thompson: tons and tons and tons each of our eutorials has upwards of a
dozen over a dozen of those and realistically we actually just have a lot of
repeated code which I think is

Matt Thompson: I think it's fine in some cases. We haven't needed to write more
than one of our own actions.

Matt Thompson: Yeah.

Ryan M. Richard: Any other feedback on that question?

Jon R: Yeah, I put a list of my flavors of deployment in chat.

Ryan M. Richard: Yeah. yeah. we're trying our best to reuse as much as similar
to what Adrien said. We're trying to reuse as much as we can trying to avoid
the code duplication. It also sort of like Matt said I think Jeff said it too.
there's sometimes where you try to look at any this is four lines of code.
Yeah, I guess I could make it in an action but It's gonna take me four lines to
call that action and provide it the inputs anyways, so did I really say
anything? I don't know so I think Some code duplication is just inevitable in
ci/cd and I haven't found a good balance yet or figured out where the balance
is.

Ryan M. Richard: So that's I guess sort of my way in on it.

Jeffrey Wagner: I guess it's kind of interesting to me to hear some of the
confusion for example about security and protecting secrets and about being
able to detect CI failure. It comes with this assumption that the testing
Machinery in the build Machinery are the same machinery. the idea like if the
artifact didn't pop up at the end, then that your test didn't run because the
CI workflow bill. and I wonder does everybody consider that assumption to be
true or for the people on this call Is your testing machinery and your build
machinery and your deployment Machinery or are they all the same Machinery or?
Do you have them kind of separated?

Jon R: In my case,…

Chase Schuette: Thank you.

Jon R: it's the same Machinery but using gitlab environments to map environment
variables to certain ci/cd calls.

Jon R: I also try to separate for security reasons on occasion. for example If
you're trying to do a Docker and Docker and you want to make sure that that's
enabled in some instances, but not in others.

01:45:00

Jon R: Because you don't want to be able to access the docker socket. In
something that's building typescript code.

Chase Schuette: It's not our workflows. We used to have it all in the same
Machinery, but due to some various. I guess changes in our architecture. We did
move our Jenkins server up to its own, but we're trying to bring that back to
our HBC cluster.

Chase Schuette: One thing I do want to mention here. that also includes our
build process which we actually have a process using an open source piece of
software called ice cream. It's a build server. one my coworkers he took it in.
than is actually a pretty small amount of modifications to it. But he was able
to modify it such that we can pass the necessary. It containerized environment
from users workstations as they're developing our software. and we can actually
Have users use it distributed build resources through our HBC cluster wait, so
it's part of our public historical build process if you will. but

Chase Schuette: Like I said, we used to be in all home. I guess one system for
both our unit. Testing build I guess the whole i/cd process if you will. And
that's kind of what we're trying to get back to now.

Jeffrey Wagner: And Could he remind me are you at a National Lab?

Chase Schuette: But no, I'm one of the exceptions here. I am at caterpillar.
I'm with the autonomous vehicles grouper. I do work closely with Iowa State my
managed relations between Cancer and Iowa State so we work quite a bit with
Just various departments around the university there. I think we're gonna be
working with their AI Center here on some Federated learning initiatives.

Jeffrey Wagner: so I'm curious is your security model I guess when I asked the
question I had in mind. on CI infrastructure anyone on Earth can go and Trigger
RCI infrastructure, but on our build infrastructure, we don't want anyone on
Earth to be able to tamper with and activate that. and so in this setup that
you're describing do you? Have random people from the internet triggering your
infrastructure or is this all kind of lockdown within the company and
designated collaborators?

Chase Schuette: It's all lockdown. So it's all I should say behind caterpillars
firewalls.

Chase Schuette: Another one exception is our clustered that does sit up in
Chicago equinoxes data center facility. but even all their networking
interfaces Their ultimately also somehow I mean, I guess I'm not the networking
guy on but I've been told that there were somehow securely behind calculus
firewalls as well. And then also to your point as far as our repos. that's a
very active discussion. and that the debate is whether or not within our
corporate world whether or not we should treat that as open source, and any
employees have access to this really proprietary software.

Chase Schuette: Or whether or not we just restrict this to just the team within
Capital that's working on. And there's strong arguments both ways going right
now. So we're trying to settle that debate. So

Chase Schuette: but I will say some of our software.

Chase Schuette: I guess is secured with various hashes that are applied to it.
So in some cases, yeah, you just got to have some sort of key pair in order to
launch certain containers and

Jeffrey Wagner: Okay, it's really interesting to think about this. I think this
is a much bigger topic than I expected when I arrived at the workshop today and
it's been really cool to hear how big it is. And now this is the fifth
dimension that I'm learning about is different organizations with different
security requirements and public exposure. and if you have randos opening up or
requests activating your CI, then you have a very different threat model. that

01:50:00

Chase Schuette: Yeah, no, it is a very important. I mean, I'm a bit fortunate
since it's such a large corporation. We have an Enterprise team dedicated to
security so they can take care of the networking and they give us a lot of
protection so our debates are mostly How much do we want to share with a other
teams? But if you're at I don't know a smaller company or something. Yeah.
cyber security can be very important as far as how you lock down your code and
whether or not everything is just clear text or however you can handle. I guess
just making your code available.

Ian McInerney: I mean, I know that there's a larger discussion with even
Security on GitHub Runners for single projects. Because anytime you use
self-hosted Runners you have security implications of what else is on the
machine. you do your secrets on there? Can they somehow get out of a Sandbox
the GitHub actions if it's configured properly might run in and so there's a
lot of cases. I mean, I've looked at it in the past of saying I have a project
that uses Cuda. I want to test GPU but that means any self-hosted runners. So
then I get a whole bunch of security implications kind of with that because I
want to do CI on it, but I don't want to have essentially exposed or have a box
on network. That would be exposed. So, I mean there's actually a lot of that
just for single project CI as well.

Ryan M. Richard: So we're getting to the top of the hour. So I do want to thank
everybody for your participation. I think it's been a really good discussion.
Yeah, I guess sort of that Echo something Jeffrey said yeah when I first
started this project, yeah, I didn't think it was this big of a space and every
time I have one of these workshops, I learned more Dimensions to it and it's
interesting. I'm not gonna be able to put together best practices for the
entire space, but I'm definitely our open to additional contributions. I put
the link to the website in the chat as you should have got it on It's just
multi project devops at if you're on GitHub. if people want to continue to
contribute that way or you can email me again, I think my email should be there
but it's our Richard at Ames lab.gov.

Adrien Bernede: Thank you very of inviting the opportunity Ryan.

Ryan M. Richard: Are yeah, I really appreciate everyone's participation. There
will be a recording of the video or I'll take the recording of the video edit
out some of the downtime Etc and I'll make that available through the website
or bssw. I haven't quite decided exactly who's gonna host it but I'll figure
that out and make links available. There also will be a synopsis of this
workshop and I hoping there will be another opportunity for the community to
come together at supercomputing. I still haven't heard back on whether or not
the birds of a feather I submitted was accepted or not. I don't know what the
time frame is on when they hear back. and other than that there will I'm trying
to put together another virtual Workshop.

01:55:00

Ryan M. Richard: closer to the end of the year, maybe the beginning of the next
year, but I encourage people to reach out if you're interested or stay ned.
thank you again for everyone and participating and I'll talk to you some of you
later.

Ryan M. Richard: Yep. Thank you for your talk.

Meeting ended after 01:54:41 👋

