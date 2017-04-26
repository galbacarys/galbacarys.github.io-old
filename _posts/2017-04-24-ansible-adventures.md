---
layout: post
title: Ansible! or, adventures in trendy software
subtitle: Why Ansible isn't a silver bullet for automation
---
Y'know, as someone who works in automation predominantly I see a *lot* of
mention of software like Ansible. "Manage your infrastructure in code", they
say. "It'll be fun", they say. The existence of Ansible (or software like it)
seems inevitable in this age of fleets of literally hundreds of VMs or
containers running the core of your infrastructure.

And, in some ways, it is fun. I have the privilege of working with some
brilliant Ansible users at ViaSat. The wizardry I've seen internally is
breathtaking in its complexity and functionality, and I definitely prefer
Ansible over pretty much any alternative I've used.

And yet...

---

# I hate Ansible.

To anyone who's ever seen me in person, this seems impossible: I've become the
Ansible wizard for my team, I have a funny-looking "A" sticker on my laptop, and
I probably have the record in my organization for the most YAML written by a
single person (although this has more to do with the fact that I adore YAML, but
that's a topic for another day). Fact is, I use Ansible literally daily at my
job. I find myself using Ansible to provision Vagrant dev environments because
it's so much more flexible than shell scripting, to deploy all the software I
write, and even as a lightweight replacement for our internal provisioning tools
when I just need a quick one-off EC2 box. And every single second is agonizing
fury.

You see, the thing about Ansible is that it's got, in my mind, two big problems,
and both of them stem from its...I don't know a better word for it than
"trendiness". Ansible is "trendy" software in all the worst ways, I would put it
in a similar category with React.js[^1], most of what I see from the Ruby
community[^2], and about 80% of Silicon Valley[^3]. I define Trendy software as:

* Software that is frequently updated, rewritten, and broken without properly
informing its users
* Software that starts out with *great* intentions, and ends up morphing into a
monstrosity that contains a full Turing-complete language inside its constructs
* Sofwtare that fails at one or more of being: performant, maintainable, and
secure

Ansible somehow, miraculously, manages to fail at all of these fronts. And
there's nothing better for my use case (replacing shell scripts and other hacky
bulls%^t to provision machines for services). Let me walk you through the things
I hate most about Ansible.[^4]

# Act 1: In which Ansible breaks critical functionality without telling anyone

A fair few months back, I was working on a different team which uses Ansible to
run automation tests on some network devices we develop.[^5] Basically, Ansible
would run some commands on a network switch with the `raw` module and verify the
output from the switch was sane.

Problem is, during a minor update (from 2.0.2 to 2.1.0 I believe) there was a
regression in the `raw` module that caused `sh -c` to be prefixed to everything
being sent to the switch. Obviously, this caused major havoc as the switch was
only exposing a CLI over SSH, rather than any sort of UNIX shell. In fact,
[here's the relevant bug
report](https://github.com/ansible/ansible-modules-core/issues/3332) on GitHub.

There are a couple of things that really, really bother me about this issue.
First is the proposed fix: rather than fixing the actual bug, the suggestion is
to simply overwite a variable for that host, `ansible_shell_executable`. While
in theory this is a perfectly good workaround, *it doesn't actually fix the
bug*. This is equivalent, in my mind, to asking someone to use IE 6 because your
website doesn't work in Chrome thanks to your 15 year ActiveX controls and
`<blink>` tags. While technically it solves the problem, it's not useful to
anyone who, for example, was autogenerating their inventory and doesn't want to
mess with other parts of their infrastructure for this fix (or, for anyone who
likes internet security. Seriously, screw IE 6.)

The second, more insidious problem I see in this small example is that there was
a *general refusal to offer a better solution* because "another module already
exists" for the specific switch CLI the reporting user was interfacing with (a
Cisco IOS device). Problem is, not only is this a very narrow fix for a general
problem (because sometimes the easiest way to do something really is to just
push raw character input to SSH), but it reveals a culture in the project that
is focused first and foremost on making everyone do things the "clean" way. I
would *love* if there was a better way to do this automated test suite than with
the `raw` module, but unfortunately that is not realistic in this case. The
offered fix was as useless to me as if there was no fix at all.

Ansible's bizarre refusal to fix small issues like this really gets me because
there's no (non-philosophical) reason
1. the change was needed, and
2. the change couldn't be reversed.

And yet, here we are, requiring a non-obvious workaround for a simple bug
because of a philosophical barrier.

# Intermission

I think the reason this particular bug bothered me so is because it was strictly
philosophical. I'm a big believer in "the right way" of doing things, I try to
encourage people to do good by their code as much as I can. That being said, I'm
also a pragmatist. While I would *love* all my little one-off internal apps to
have a full deployment pipeline with CI, regression testing, and fancy
logging/alerting, the fact is that the simple, pragmatic solution was what was
going to get the need addressed most quickly. Sometimes pragmatic solutions are
wrong, but oftentimes when dogma and life collide, life tends to win.

# Act 2: In which Ansible fools people into thinking they're programmers

Let me open act 2 with a bold statement:

**Ansible is not a programming language.**

*And scene*.

There are a lot of things I can forgive in a software tool: bad
documentation[^6], bad code[^7], a bad bugfix process[^8]; but something I can't
forgive is a tool that provides half a programming language in its
configuration. I believe this is symptomatic of bad design; if a tool is indeed
supposed to be a full programming environment, then give its users the facility
to actually properly *program* with it. But don't give half a programming
environment and half an API, and expect people to figure out stupid workarounds
to your tool's rough edges.

Ansible's choice to use YAML is a double edged sword: YAML is an extremely
powerful markup language, and combined with Jinja and the Ansible engine behind
it is technically Turing complete (seriously, I checked; I once wrote an Ansible
playbook to calculate Fibonacci numbers just to verify it was possible). And yet
Ansible provides very little facility to do the kinds of things that programmers
often do, namely sorting, iterating until a condition is met, and support for
any concept of a nested module (e.g., a role within a role).

Ansible looks far too close to being a programming language for my comfort. It
leads to three major problems:

1. People who aren't programmers being intimidated by Ansible since it looks
   like coding
2. People who aren't programmers writing a couple of playbooks and suddenly
   believing themselves to be lords of the art of coding
3. Coders believing that they can do things in Ansible playbooks the way they
   would do them in, say, a Python script and either failing or creating a
   workaround so ugly it should be taken out back and shot.

I fall into the third category constantly; one recent example involved hitting a
URL, parsing its JSON response, and registering a resource if it didn't
already exist in the JSON response (since the API didn't perform deduping
automatically). Problem is, the API was paginated. So I had to write an
absolute abomination which used `retry` to create a sort of while loop which
continued until I found the right resource. I shouldn't have to create a
while loop from scratch. This is, in my opinion, TRWTF (the real what the f**k,
a phrase taken from the awesome [thedailywtf](https://thedailywtf.com)) in this
article. This should be a relatively simple task, and I could do it in 10ish
lines of `requests`-ified Python. But it took me over 100 lines of messy, hard
to read YAML.

Worse yet, the first two categories make up a huge portion (in my experience) of
the kinds of people that should be using Ansible. There's a network engineer on
my team right now who's reluctant to learn it because they think it's "coding",
which seems to intimidate them enough to not even try. And I know others who
view Ansible as their magic hammer for every nail; need to build a VM
orchestrator? Just wrap up all its functionality in Ansible! Need a DNS service?
use Ansible to register DNS entries. It's amazing what people can do with
Ansible, but I don't know that I mean that in the best way possible.

# Conclusions

So, what have I learned from my experience using Ansible for the last year or
so? I think that the most important thing to note is that, unintuitively,
you will run into fewer issues if you pick a version and stick with it. If
there's no obvious reason your code can't run with the newer version, it doesn't
hurt to try, but there's definitely no need to upgrade unless there's actually a
bug or missing feature that *forces* you to use another version. This will help
you develop over time your own library of quirks, gotchas, and edge cases, and
sticking with one version if at all possible ensures that that information stays
relevant. The second thing I've learned is that the landscape of automation
tools is missing programmers. I mean real, honest-to-goodness programmers.
Ansible has tried very hard to cement itself as "the language" for automation,
but it isn't even a language: *it's a task runner with YAML config files* that
just happens to look like a programming language if you squint hard enough. 

What would I like to see? I think a tool that allows programmers to do things
the way programmers know how (i.e. through code) combined with a tool that
provides sensible defaults whenever they make sense (and enforces convention
over configuration) would save me (and hopefully others) dozens of hours worth
of time wrestling with playbooks and inventories.

---

[^1]: To be honest, the entire modern JavaScript stack is just a horrifying abomination IMHO.

[^2]: I mean no offense; Ruby is a wonderful, beautiful, horribly misused language. Similar to JavaScript in that regard, actually.

[^3]: Here I mean some offense. Man, some of the crap coming out of SV is embarassing these days.

[^4]: N.B. I don't mean to beat up too much on the guys running the show over at Ansible corp. They work hard and have a lot to contend with. I just want to voice my concerns as someone who runs right up against Ansible's limitations daily and wants more than anything (well, not ANYTHING) to love it.

[^5]: Yes, I know. Using Ansible to *test* software is TRWTF here, but it isn't the point of the story.

[^6]: For Ansible: Check.

[^7]: Cheeeeck.

[^8]: See act 1: check-a-roonie.
