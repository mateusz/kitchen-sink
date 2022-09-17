# How Mateusz does peer reviews

This article captures the things I consider (even if not always apply) during a peer review. Peer reviews can be daunting, horrifying and personal experiences, and this will hopefully give you some reference points, and perhaps even show off some useful practices you can apply in your work.

I’ve split the article into four parts, as that’s how my brain roughly works, and it should make it easier to navigate your way through the prose:

* First pass - context and map
* Second pass - deep review
* The Cutoff 😱
* Third pass - standard checks
* MERGE ON GREEN!

Sidenote - this is also on topic: http://www.dein.fr/2015-02-18-maslows-pyramid-of-code-review.html

## Setting the scene: tone and conduct

Before we start, let me just stress how important the tone is in peer reviews. I have a bloated ego, so it’s easy for me to take things personally. When someone is reviewing my code, I have a flight or fight reaction, even if I don’t want to. Thus, when I’m on the “giving” side, I try to do all I can to prevent antagonisation (I admit I fail quite often - this is hard to do!).

I don’t think there is ever a single “right” solution, and “code beauty” is in the eye of the beholder, so here is my first rule of peer review - **make remarks kindly and steer clear of absolutes**. Instead of saying “this is wrong?!?!?!” (which is almost like saying “you are stupid!”), I try to use softer language (“I think this might be incorrect”). Or even better, I try to make suggestions, involve the requester and ask for opinion (“Do you think we could do X here? It might be better because Y”, “Did you consider Z?”).

So with that firmly lodged in my mind, I dive in.

## First pass - context and map

In first pass, I scan the entire pull request to construct a mental map of the bits that are changing. I try to find out how things interrelate by scanning the directory structure of the repo, names of files, classes and functions - without getting into particulars.

I will also try to get a feel for the API of the components in question. I might look at what is private vs what is public, and which function calls which one. I will also mentally mark off different zones in the peer review - an obvious one is “everything related to tests”, and another one is “non-consequential whitespace changes” (related: ignoring whitespace.)

Finally, I will read through the commit message and any other comments provided by the requester, to see if I got the gist right, and fill in the blanks.

At this stage I will quite frequently find I don’t understand what’s going on. This leads me to my second rule of peer review - **understand what you are reviewing**. There is no successful peer review without context. In particular if you don’t understand why changes are being made, your peer review is going to be superficial.

To understand more, I will sometimes clone the repo, fire up the IDE and start poking around the code. I will also look up any documentation I can find, as well as related tickets. If I still feel I don’t understand, a great way to handle this is to… talk to the submitter (SURPRISE!) I simply ask for a walkthrough - it’s amazing how quickly a context can be explained in a conversation.

Otherwise, especially if I think written documentation will be useful for future maintainers, I ask for additional details in writing. Most frequently I will ask for a bit of an explanation of why the submission is being made (purpose statement). In the heat of the battle, submissions often omit this crucial precursor for building context. In more extreme cases (if I’m completely lost), I will ask for an informal architectural overview or even (gasp!) a SAD.

Another important aspect is numerical understanding. I will ask about non-functional requirements (NFRs) that make sense in the context - numerical values to aim for - such as requests per second, amount of disk needed, runtime and CPU requirements or price.

As I start to understand the context, I might go as far as challenging the very purpose of the change - do we even need to do that? Who requested it and why? Can we even dream of fulfilling the NFRs or it’s too crazy to even start working on it?

Some additional techniques I use to aid understanding:

* I ask the requester to split the submission into parts - mostly when multiple concerns are intermingled in a large pull request, and are moderately easy to split
* I request a screenshot or animated gif of the interface elements in question - if significant UI is involved.

I won’t always use all those techniques - rather, I will bring them in as needed, and more of them the more significant the change.

At the end of this pass, I expect to understand:

* purpose (why is it changing)
* context (what does it relate to)
* numerical constraints (NFRs)
* the structure (where physically things sit)

## Second pass - deep review

With that in mind, I now start piecing the submission apart, trying hard to ignore superficial/idiosyncratic stuff such as particular style choices (e.g. ternary vs plain conditional), formatting, naming choices. This helps avoiding antagonism, but more importantly it reduces noise. Spamming the PR with dozens of minor style comments might cause the submitter to tune out when they finally get to the important stuff. 

So my third rule of peer review is - **important stuff first**. This saves time too - if I think something is wrong with the model and I decide to suggest a re-architecture, it may turn out a lot of the superficial comments are no longer applicable.

As to what I actually do in this pass - I’d like to recommend a very good book on general coding skills - Code Complete. I find it provides a good structure to understand and learn the practice of coding and peer reviews in general.

As for me, I will repeatedly switch between the mental map, and trying to execute the code in my head. The goal is to exactly understand what the code does. To do this I often need to consult the programming language or library documentation.

This can be a very tedious process, and the difficulty is compounded by people’s thought patterns and styles differing wildly. I sometimes find I’m not even able to understand what particular code constructs do, let alone why, so I may ask the requester for help by filling in some code comments, or even providing more meaningful names.

As understanding builds, I ask myself if the change is fulfilling its purpose. Does it do what the requester intended? Does it solve the problem fully or only partially? Can I think of why this won’t work, or if there are any better solutions? 

I will try to get a feel what impact the change has on other systems. I will sometimes go off and quiz other more clued on people to find out if they/their systems will be affected, and if they need to be involved somehow. In particular, if UI is involved, I consult the UX people.

Another thing I look at is - does it fit the established practices. If new architectural pattern is being used - is it warranted? Is there anything novel that others need to know about?

As I go I fill suggestions and remarks. I will usually attempt to stop after several comments. This saves time - after those are addressed I will either learn more, or the whole landscape will shift anyway.

I will look at the correctness of the code. Are all execution paths taken care of? Is there any crazy edge conditions involved? Off-by-one errors in loops? Are all errors handled? What failure modes can we expect when the system is in operation and interacting with other systems?

I will try to get a feel for performance - look at loops-in-loops and see what is the algorithmic efficiency of different parts. At this stage I might need to go off and learn what inputs and outputs are even possible. I might also ask to do some benchmarks, and fill in the blanks in numerical understanding (NFRs).

I will also try to spot any dangerous hidden assumptions - things the sponsor/customer expects, and are embodied in the code, but not mentioned anywhere, and especially not covered with NFRs (“oh, it’s obvious this job has to finish within 1 day. BTW we have 2 million asset files”). I will request those to be documented explicitly.

Again, I won’t comment or even consider all these issues always. There is a certain balance to be struck - I want to merge the change, not just obstruct it. In some cases I will just gloss over things - a “let’s see how it goes” approach. I keep reminding myself the purpose of the PR is not to prove I’m smart and clever - the purpose is to give constructive feedback.

At the end of this pass, I expect to:

* Have a full understanding of what the code does, and how
* See how it fulfills the purpose
* Have a confidence it fits the surrounding system
* Have a sense if we are likely to hit NFRs
* Find out all the traps

## The Cutoff 😱

This is the point when I sometimes find the change is fundamentally not acceptable to me. I feel refusal should be in the reviewer’s toolbox, but it’s the most difficult decision to make in this context.

Such resolution should be communicated with care, and after careful deliberation, and the reasons should be given clearly. I think the best approach is to talk to the submitter in person - it will hopefully turn out I got something wrong, so that I can change my mind, and stave off the disaster.

Unfortunately, sometimes PRs end up in conflict that cannot be resolved. We are all human [quotation needed], and there is a lot of passion involved in peer reviews - well, at least for me! I try to pin down the moment when a PR descends into adversarial style and back off.

Here how not to react to a PR conflict:

* get someone else who I know will agree with me to underwrite the review, and term it as “second opinion”
* ask manager for advice, preferably on public channel, or aloud in a big room
* roll up a newspaper, and repeatedly hit the submitter with it over the head until they yield.

Unsurprisingly, these passive-aggressive methods backfire horribly, destroying trust and making everyone feel bad.

I think the following two options are the most realistic and practical solutions:

* defer to the submitter to find another peer-reviewer
* change your mind anyway - some changes are low-risk, maybe it’s worth a try?

So we come to my fourth rule of peer review - **treat submitter with utmost respect and let them drive**.

At the end of The Cutoff, I expect to:

* Still be on speaking terms with the submitter

## Third pass - standard checks

Phew! f the PR gets to this stage, it’s just the matter of time and work to get it merged - overall it’s acceptable, and wanted too. We finally come to the standard stuff - which can probably be captured in a checklist. Off the top of my mind, I consider the following things.

I look at security. In my head, I put myself in the shoes of an attacker, and I will try to break in. I will also look at information management - types of private information stored, how it is protected, and what lifecycles are involved (are there backups?). I will note any risks and perhaps suggest controls if I can think of any. 

I will consider the operational aspects. How will we deploy the change? Will the changes cause problems for Ops or 24/7? How will the operational processes look like? How is the observability - do we need to turn NFRs into monitored SLOs? Are there SLAs that need to be advertised? Are there metrics, logs? If not, can they be added easily?

I will also think about documentation. Have all processes been written? Existing pages updated? Do we need an OPC, or a SAD? Do contracts or customer-facing content need to be updated?

Lastly, I will also check the code documentation is present and useful, do unreachable/dead code checks, make sure there is sufficient tests, and that the style guide is being followed (often via automated means). I will also ask submitter to tidy up and squash the PR (unless multiple features are being merged).

And yet again - there is a balance to be struck. If code is correct but idiosyncratic, that’s fine. Minor and strategically even medium problems can just be accepted. I try to pick my battles and keep my ego in check.

At the end of this pass, I expect to:

* Have a clean code that can be merged
* Have a change that when rolled out, doesn’t surprise anyone
* Change that respects the law of the land (conventions, style guides, iso)

MERGE ON GREEN! 🥳