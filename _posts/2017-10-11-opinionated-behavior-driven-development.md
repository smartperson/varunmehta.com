---
layout: post
published: true
title: "Opinionated Behavior-Driven Development"
author: Varun
image: /img/personal.jpg
categories: Technology
---
After having a few conversations on this topic recently, I've decided to put down my thoughts on behavior-driven development (BDD): what it's for, how to use it, and how *not* to use it.

* TOC
{:toc}

#### Behavior-driven development

There is a good overview of BDD on [Toptal's blog](https://www.toptal.com/freelance/your-boss-won-t-appreciate-tdd-try-bdd), especially as it compares with test-driven development. My perspective: behavioral testing uses 100% implementation-neutral descriptions of what a user does with your software. BDD is writing those tests before any code is written, and using those tests to gauge the implementation's progress. When all of the behavioral tests pass, the feature is complete.

#### My context

Working on Disqovery is what exposed me to BDD and behavioral testing in the first place. Because of that, my opinions and experiences are based on:

* Web and mobile web users
* Ruby on Rails backend, angular.js frontend
* Existing unit testing framework
* Agile-style project management
* Using [cucumber](https://cucumber.io) with [phantomJS](http://phantomjs.org) set up with [capybara](https://github.com/teamcapybara/capybara) via [poltergeist](https://github.com/teampoltergeist/poltergeist). *Yes, these project names are all very amusing.*

I won't explain how to get cucumber or similar systems working. There are plenty of great tutorials and videos that will explain it better than I could. As you could imagine, some setup is required to get all of these pieces working together, but the benefits are numerous:

1. Every test is written in plain English, so every team member and stakeholder can understand what is being tested.
2. Tests are run automatically while developers are working on their code. We can catch regressions before any changes even submitted for code review.
3. The headless browser system sees *exactly* what the end user sees, including all javascript and css. This is end-to-end testing in its purest form.
4. Not only are any test failures reported, but screenshots are automatically generated to aid in debugging and development.

#### BDD flow

I incorporated behavioral testing and BDD into a pretty standard PM workflow. This works easily with cucumber since it breaks tests down into "Features" which consist of multiple "Scenarios."

1. Perform customer research, including an understanding of customer challenges and needs.
2. Synthesize feature descriptions, use cases, requirements statements, and priorities. This is the start of your functional spec. Get it signed off by stakeholders.
3. Convert spec features to cucumber features, use cases to cucumber scenarios, and requirements to cucumber steps, as appropriate. Tag scenarios according to milestones.
4. Work with development team to get features created. Behavioral tests will help track progress, as well as catch regressions.
5. Reprioritize as needed during development. Moving a failing test to another milestone makes it easy to identify exactly which features and use cases are affected.
6. Go back to step 1 once you've shipped a milestone to the customer.

#### BDD tips

* **Developers don't write behavioral tests. You do.** Let developers write the code-centric tests. The level of customer understanding for behavioral testing *requires* they be written by the PM and UX designers. However developers are stakeholders, and should be part of the conversation.
* **Involve your stakeholders early and often.** Cucumber's tests are written in plain English; use that to your advantage. Involve your stakeholders in the feedback process early, to make sure every sentence of the test agrees with their assessment of what a user should experience.
* **Think of scenario steps at the most basic levels.** Steps in cucumber should include verbs, and I focus on them being the simple possible verbs. The user 'sees' something, 'clicks' or 'taps' a button, 'drags' a picture. In some cases maybe they even 'hear' a sound or 'select' a file from their computer.
* **Cucumber is not a way to validate 100s of different devices/browsers.** Cucumber, along with the tools I described above, is a way of validating behaviors during development, but it is limited to a few browser frameworks. Tools like [Xamarin's Test Cloud](https://www.xamarin.com/test-cloud) may provide the diversity of devices you're looking for, at the expense of test complexity. There may be a way of connecting a behavioral testing framework with Xamarin Test Cloud; I'm not sure.
* **Close development feedback loops as tightly as possible. Automate test runs.** I set up our Rails test environment so that every time a developer saved a source file, the relevant tests would automatically run and report their results onscreen. Within seconds, developers would have feedback on whether they fixed and issue, or made it worse. Tighter feedback loops can speed development.

If you've never heard of BDD before, this post may have piqued your interest. If you're considering it, I hope my experience and implementation will prove helpful. Tools like cucumber are so incredibly flexible, that having some opinion on the "right way" to use them is better than none. Completely disagreeing with me is just as valid as completely agreeing.

&mdash;&nbsp;Varun

_Who's Varun? I most recently was the founder of an HR tech startup, [Disqovery](http://disqovery.com). I have worn many hats, and I like making things. I also like talking business. You can reach me at [me@varunmehta.com](mailto:me@varunmehta.com), [Mastodon](https://fosstodon.org/@smartperson), [Github](https://github.com/smartperson), and [LinkedIn](https://linkedin.com/in/varunkmehta)._
