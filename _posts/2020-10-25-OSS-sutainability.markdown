---
title:  "On the psychology and dynamics of OSS: passion, burnout and (im)balance"
date:   2020-10-25 11:10:40 +0700
categories: opensource
classes: wide
---

Opensource is quite a modern social concept. You get to interact with people about technology from anywhere in the world, make friends, and improve your skills fast. Location or timezone don't really matter, which is an added benefit, to me at least.

However, OSS is not a _perfect_ world.

The topic of OSS sustainability has been discussed about more and more in the past few years. So many widely used libraries and programs are understaffed and underfunded, and many maintainers need help while the amount of their users just keeps growing.

This post is more about trying to find solutions than explaning the current issues. If you are not familiar with the concept of "opensource sustainability", and would like to understand the context of the current situation, the impacts and imbalances, these links are a good start:

- http://veridicalsystems.com/blog/of-money-responsibility-and-pride/
- https://andre.arko.net/2016/09/26/a-year-of-ruby-together/
- https://dev.to/erikras/open-source-sustainability-3pjf
- https://techcrunch.com/2018/06/23/open-source-sustainability/
- https://www.youtube.com/watch?v=0t85TyH-h04

Multiple symptoms are well identified in these posts. Solutions to the root causes are harder to come around. There are multifaceted problems which do not seem to have many fast, easy to implement solutions. 

I have been thinking about these dynamics quite a bit in the past few weeks, notably while [writing my book](https://www.libvlcsharpthebook.com/preview) which has a part dedicated to reflections on OSS, and wanted to write some things about it in a dedicated blogpost. I am hoping to suggest some possible solutions to various well identified problems. I have no idea if they would actually work in practice or can really be implemented.

# Free costs

The vast majority of people, both users and developers, have been accustomed to not having to **pay with money** for a lot stuff online... Free browser. Free email account. Free search engine. Free code. Free IDE. Free support. Free maintenance. We take many things for granted.

There is always a cost. Sometimes you pay with your data (Hello Gmail!), sometimes you don't pay - because someone else pays for you. The cost is real and often, maintainers will get negative feedback from their users when they change the license and/or want to start charging for their work. Many people do not realize digital work is still work.

> Some OSS maintainers are not interested in any kind of compensation for their work, for a variety of reasons. That is perfectly fine but this is not what this blogpost is about.

# Making a living from user donations does not work

Like I wrote above, people are used to having support and updates for free, why should they start paying now?

Unless you have somewhat of a huge GitHub/Twitter following or something (and even then), chances of a maintainer being able to live from its user donations are rather low.

In many cases, it does not matter how many millions of users you have, you cannot rely on user donations _only_ to hope to pay a team of maintainers (or just yourself) a salary every month to work on OSS. Consulting and commercial licenses are often necessary, but not always possible for the maintainer.

## Proposal

I believe companies, and not individuals, should be targetted for donations. Asking the occasional hacker to contribute financially hardly works (though it is great when it happens!). The imbalance comes from the silent majority of businesses relying on OSS for their products.

# Maintainers burn out

This one is tricky, because it relates to various interlinked and personal concepts, amongst which the maintainer's approach and relationship with technology and their self-awareness. I believe one of the key solutions to this problem would come from our current and future tooling and code sharing services.

## Proposal 1

### Educate maintainers for a healthier relationship with technology

Tools are partly responsible for this. Github (because that's the popular tool of the moment) is not doing a great job here, you get auto-subbed to issues by default and notification management granularity could definitely be improved. The issues section can also be responsible for a large mental load, product design could step in here as well.

This can all become overwhelming fast. For more info on this, see: https://www.humanetech.com/take-control

## Proposal 2

### As-is

"Software is provided **as-is**" should be written down in more places (e.g. in the bug tracker when someone opens an issue, for example) and explained where programming is taught.

## Proposal 3

### Provide a space for mentorship to happen. 

Yes, that's more work for maintainers, but not only maintainers of a specific project can mentor juniors. Any experienced dev should be able to chime in.

## Proposal 4

### Provide a space for project ownership transfer.

There could be a place that facilitates ownership hand-over of the current maintainer's project to a new maintainer.

People's needs change, work requirements and dependencies evolve. Time availability varies. Interests shift. 

There could be systems and processes in place so that maintainers can give over their code (and deployment keys) to trusted new members, if they choose to. 

## Proposal 5

### Developer tools and collaboration systems could encourage more positive feedback from users to fight [negative bias](https://en.wikipedia.org/wiki/Negativity_bias) through UX and product design. 

Maintainers have more users complaining than complimenting their work. Inversing that trend would go a long way, I think, though it will not be easy.

# OSS and companies responsibility imbalance

Millions of companies in the world critically rely on OSS. A lot of OSS projects are understaffed and underfunded, while the majority of companies are not contributing a dime.

Ignorance and entitlement are not fought the same way. The following part focuses on ignorance.

# Proposal 1

## Educate the tech side, developers, leads, architects, CTOs...

I believe not enough people are aware of the many OSS maintainers struggles in the tech industry. Many SME/large corps likely never thought about contributing to OSS (one way or another). The solution could then be, more education.

How many universities teach classes about the dynamics of opensource? Probably not a lot.

Where do people learn to code? C.S degrees, universities, The Internet, StackOverflow, GitHub, GitLab, whatever. These places, and others, could have a responsibility, in a way, to do this given they are the centerpoint for devs both juniors and seniors, to go to. Twitter is nice and all, but don't believe that your Twitter bubble is in _any_ way representative of the dozens (hundreds?) of millions of developers present in the world today. It is not.

The tech side needs to be made more aware of these issues so they can advocate for this inside _their_ company. They know their software dependencies and which libraries they could donate to, which they rely on **critically**, which they are thankful for, that does the heavy lifting for them, so they can focus on other things. 

Additionally, people in tech are currently in a strong position for negociation with regard to their market demand.

_Make opensource a topic (or even a condition) in your next hiring interview (whether you are hiring or looking for a job) by demonstrating it is in the company's best interests to contribute._

# Proposal 2

## Educate the non-tech side, CEOs, CFOs and other people with company writing-check abilities

The discourse/dialog needs to be different here, because these people are not necessarily techies, so:
- Make it clear to them what are the operational risks, and impacts, for their businesses. 
- Explain the imbalance of the current situation.
- Use analogies to try and describe what would happen to their businesses if a low-critical dependency of their code gets abandoned by the current OSS maintainer. Then, do this with a middle or highly critical dependency of their product.
- Show how giving back will help alleviate some the risks of OSS abandonment happening (project ownership transfer system, global developer care and health).
- Alternative operational ideas for action: give money, give time, give skills, give code, give documentation, give hardware, give recognition, give marketing, hire the maintainers directly.

I believe most people in the tech industry could help with these various actions, in the way they choose, no matter their position. Education will allow change, though it will take time. But if you are wondering what you can do today to help fight this imbalance, I hope some of these ideas can be applicable to you and your company.

Feel free to let me know what you think on [Twitter](https://twitter.com/martz2804).