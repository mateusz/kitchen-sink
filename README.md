# The infrastructure kitchen-sink

![This is fine cognac and cigar](logo.png)

This is a collection of random advice collected over the course of my infrastructural struggles.

## Working together

* Make pull requests daily, when working on features. This will save you work by allowing early feedback, and also splits the feature into a more manageable chunks.
* Every code change, docs change and announcement goes through peer review.
* Peer review rule #1: make remarks kindly and steer clear of absolutes
* Peer review rule #2: understand what you are reviewing
* Peer review rule #3: important stuff first
* Peer review rule #4: treat submitter with utmost respect and let them drive
* It's submitter's responsibility to ensure their code gets reviewed and merged. If nobody seems to be picking your pull request up - @ someone directly on slack! Worst case they will hand it over to somebody else. Also, it's ok to remind people to do the follow-up reviews and to merge.
* It's established practice to "LGTM" pull requests, meaning that the peer reviewer will approve your pull request for self-merge. You are free to merge yourself if automated checks succeed and if you are happy with your work.
* One peer review is usually enough. Avoid changing peer-reviewers mid-flight. If you do more than one, you will likely need to rewrite things, not necessarily for the better.
* Peer review is not only for finding bugs. It's also for learning, and for spreading knowledge about new functionality. We would still do peer review even if all code we wrote was perfect.
* A video call using Slack or Zoom or a face-to-face meeting is worth a thousand messages in Slack. If during a peer review or a technical discussion you find yourself typing more than paragraph or "several people are typing", jump onto a call. This is especially important when working with remote staff - nothing looks more aggressive than a braindump in Slack, no matter how many emojis you add.
* Do not merge wrong code. Is something not right? Gut feeling tells you it's wrong? Do not merge that pull request, even if the submitter will need to rewrite the whole thing. Do it until the bad feeling is gone, even if the code must be rewritten 6 times.
* Don't be greedy with your knowledge, if you have the opportunity to share or teach - do.
* Your worth on the team is not decided by your knowledge. First level support can be just as important as architecting a platform, every action taken for the team is a burden off someone else' shoulders.

## Coding craftsmanship

* Work with the assumption that master is shipped continuously. Code merged to master must be prod-worthy. This seems to contravene "make pull requests daily" rule but this is not so: if your feature is huge, split it up. If it's still huge, use [feature flags](https://trunkbaseddevelopment.com/feature-flags/) and still ship it early. By the way, this workflow is variously called [GitHub flow](https://guides.github.com/introduction/flow/) or [Trunk-based development](https://trunkbaseddevelopment.com/).
* Don't live with broken windows if you can help it: thoroughly investigate and fix bugs if you can. This will save someone's day troubleshooting the same thing. If you can't help it then document it or raise a ticket.
* Minimal feature earlier is better than comprehensive feature later. It's easier to turn around if you are building the wrong thing.
* One good name is worth a thousand docs! Take your time inventing good, non-ambiguous names, especially for concepts that are widely used or discussed.
* Execute the code you are submitting at least once before asking for peer review. Even better, execute every single code path you are submitting. Test coverage counts as execution too.
* If you haven't written docs, you haven't finished :-)
* If you haven't written tests, you haven't finished :-D
* The fastest and least buggy code is no code at all. Remove what's not needed, and don't add that feature you think is cool or might be handy (especially if you can't explain the use-case for it).
* Someone asking you to fix a stylistic issue with code? Just do it. It's easier to fix than argue.
* When writing comments, do not repeat what the code does. Instead, document the intention and the reason, because that will make it easier to spot the bug if there is a discrepancy between what you wanted to do and what the code actually does.
* Only fix the bug after you understand what's wrong. As a corrolary, do not submit code you don't understand, and don't merge code you don't understand.
* Don't count on the network being reliable - network failure is a rule. Use timeouts and exponential back-offs.
* If it feels like a hack, it probably is - get a second opinion or some sleep then review.
* Noisy logs can be worse than no logs. Log thoughtfully and the future developer investigating a problem with your work when it inevitably comes up will thank you.

## The view from ivory tower

* You are a software architect now, congratulations :-)
* If something requires collaboration between teams, or you are unsure about the best solution, consider writing an informal design doc first. Describe in plain English the context, the reason for doing the work, the constraints (NFRs), the cross-team APIs. Then make a suggestion on a solution and pass it around for review. For advice regarding design docs, read [this](https://www.industrialempathy.com/posts/design-docs-at-google/).
* Better underarchitect than overarchitect, because both are wrong, but first one is cheaper (both in terms of coding cost, as well as mainteance cost)
* Avoid overspecification. New information will emerge and by the time the work starts, any in-depth spec will be out-of-date. Whoever will be implementing it, you can rely on their ability to figure stuff out as they go - focus on documenting the context, reasoning, constraints and APIs. Also see the doc linked above ("Writing design docs").
* Strive for continuous delivery.
* Code maintenance is >50% of the cost. If you can cut maintenance by half by spending twice the time on development, it may be worth it.
* There are only two hard problems in computer science: naming, caching, syncing, ordering, migrations, escaping, backwards compatibility and dealing with users.
* Be more careful the deeper you dig; the impact of changes grows further down the abstraction level. For example, if you get a controller wrong - it's easy to throw away and rewrite. If you get the model wrong - it's wrong forever.
* Introduce inconsistencies strategically, after discussing with others in the team. In other words, choose consistency over beauty.
* Strive for backwards compatibility whenever possible. This makes rollout and maintenance of multiple inter-dependent systems easier.
* Ensure sync scripts are re-entrant: make no assumptions about state, and ensure that everything fixes itself on a subsequent run.
* If you have distributed systems syncing with each other, make sure only one of them is the source of truth (the data owner). Only this system should be writing the data. Everyone else just holds a local cache.

## SRE

* No, you cannot make a consistent, available and partition-tolerant system no matter how you try :-) (see [CAP theorem](https://en.wikipedia.org/wiki/CAP_theorem))
* Spending extra time on making migration smooth is a good thing: it's either that, or having to troubleshoot it at twice the cost in the middle of the night when the migration is actually executed.
* Fast acting load-balancer health checks should perform shallow checks only (e.g. is the port open?). Deep checks, and especially ones that inspect nested dependencies, should be acted upon carefully or only reported on. This is because deep checks can make many servers "fail" in a synchronised way, bringing the whole service down with it. [(more)](https://aws.amazon.com/builders-library/implementing-health-checks/)
* Always confirm the following areas of SRE interest have been addressed: observability (logs & metrics), security, privacy, backups.
* Don't deploy on Fridays. Especially don't deploy on Friday afternoons.
* Prefer to deploy during business hours, as this is when other people are available to help. Availability of support should weigh on your risk calculation.

## Resources

* Engineering code of ethics: https://www.asce.org/code-of-ethics/ (yes it's for civil engineers, but it is a good ethical base for all engineers anywhere)
* System administration holy book (formerly known as the "red book"): UNIX & Linux System Administration Handbook

