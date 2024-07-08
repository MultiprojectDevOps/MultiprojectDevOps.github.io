
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

