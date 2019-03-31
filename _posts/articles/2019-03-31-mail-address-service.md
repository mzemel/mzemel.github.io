---
layout: post
title: "Towards better mailing address management"
image: https://static.makeuseof.com/wp-content/uploads/2017/07/Mail-Merge-Featured-670x335.png
header:
  image: /assets/patterns/asanoha-400px.png
tags: ["business"]
keywords: business
ref: post-example-in-english
lang: en
---

People move a lot.  Since leaving college, I've moved 8 or 9 times; each time, I'd have a different mailing address and update banks, healthcare, and other interested parties.  It's a hassle to constantly update your address, and if you forget you risk important mail being sent into the void.

I'm reminded of a domain name service.  When I want to visit a website, I don't need to keep track of its IP address, since those are constantly being reassigned.  Instead, there's a server which maps an entity (michaelzemel.com) to whatever IP address it's currently at.  I, the interested party, don't have to keep track of anything beyond _where_ to look it up, and a unique identifier (in this case, the domain name).

What I propose is a mailing address service.  Each time you move, you update the service with your new mailing address.  Interested parties, such as banks, do not keep your address on file but instead query against this service to your current address.  I'll address problems below:

## Problems

### Unique identifier

Many people share the same name, even middle and suffixes.  There's another Michael Zemel who's apparently quite prolific in researching milk.  So for a service to ask, "Give me the address of Michael Zemel" there's a not-unique-enough problem.  But what guaranteed unique identifier do (almost) all of us have?  A social security number (SSN).  However, in order to allow interested parties to get your mailing address, you would have to give them your SSN, and I don't think very many people would feel comfortable doing this.  However, the notion of "keeping your SSN secret protects against identity theft" is pretty outdated in the face of all of these data breaches.  We need to move to a better method of guaranteed authenticity, but that's a separate article.  The point is: 1 SSN, 1 person; banks could use that SSN to fetch your current mailing address.

### Guaranteeing you cannot update other peoples' records

Ok, so you have a SSN but I know several peoples' SSNs: my wife's, my parents', and even that one guy whose SSN I memorized just to annoy him.  I'm not sure what steps are used during DNS record creation/modification that guarantee they are authenticated by the person who owns them, but that problem would need to be solved here, too.  Unlike websites, if a bank is given a false mailing address they cannot say, "I am on the wrong website!" -- the act of sending mail is fire-and-forget, and there's no way of seeing where that mail goes to ensure that address is correct.

One interesting analog with DNS records is CNAME records, which tell a domain to be redirected to another.  If we consider "domains" to be analogous to a person's identity, then upon death you could update a CNAME to pass along all physical mail to go to one of your next-of-kin.  That would mean "get the mailing address for Michael Zemel" returns "send mail for Michael to Alex Zemel at this address."

### Should mailing addresses _be_ easier to get?

Perhaps it's intentional that you need to update mailing addresses with the consumer parties directly, because having your mailing address discoverable (using potentially public information) would reduce the security of keeping mailing addresses secret.  I would imagine many people don't want to publicly list their home address, and your mailing address is most often going to be your house.  If this service were to be in practice, assuming that your mailing address would be trivially discoverable for all people, similar to finding out the IP address of any domain (or at least the access point for the subnet it's on.)

#### Sub-networks

Perhaps a way to implement this without exposing your mailing address is to have the town act as a routing station.  The mailing address service would return, to the consumer, a town or zip and mail would first travel there.  Once there, it would be routed to your home address.  This places the trust of safe-guarding your specific address to the town, which already has that information (SSN => physical address).
