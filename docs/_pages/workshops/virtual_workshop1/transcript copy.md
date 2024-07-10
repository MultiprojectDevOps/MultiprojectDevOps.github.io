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

