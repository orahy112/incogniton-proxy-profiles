# Incogniton Proxy: Give Every Profile Its Own IP, Pick the Right Proxy Type, and Pay Only for the Traffic You Use

Incogniton's pitch is that ten profiles on one laptop look like ten separate machines. That only holds if each profile comes out of a different IP. Without a proxy, every profile you launch shares your home connection, and platforms that link accounts by IP can tie them together no matter how clean the fingerprints are. Incogniton's own documentation says this outright: profiles share your real IP, and platforms correlating accounts by IP will connect them regardless of how different their fingerprints look.

So the proxy is not a side purchase. It's the second half of the setup, and it's usually where people either overspend or get flagged. This guide covers both halves: which proxy type fits which kind of profile, how to wire third-party credentials into Incogniton, the ports and session settings that actually matter, and what the whole thing costs at DataImpulse, which prices residential traffic at $1/GB with no subscription.

## What Incogniton does with a proxy, and what it leaves to you

Open any profile and the Proxy tab gives you four routes: Proxy Library, Buy proxy, Custom proxy, and Free proxy.

Incogniton isolates cookies, local storage, canvas, fonts, WebGL and the rest of the fingerprint surface for each profile. It does not supply identity for your traffic unless you attach it. Its built-in free shared proxies are usable for smoke-testing a fingerprint or checking whether a setting behaves, but the documentation is blunt that they're a bad fit for account management: the IPs are shared with other users and can change between sessions. That's fine for testing a WebRTC leak. It's not fine for a Facebook profile you intend to keep.

The Buy proxy option inside the app routes you to partner providers so you can get residential, ISP, datacenter, or mobile IPs without opening an account elsewhere. Convenient, and the markup is baked into the price. The Custom proxy option is the one most people end up using long-term: you keep an account with a provider you chose, paste the host, port, username and password, hit Check, and Incogniton stores the entry in its Proxy Library for reuse across profiles.

One limitation worth knowing before you shop: Incogniton supports HTTP, HTTPS and SOCKS5, and it accepts a rotation API URL under its Rotating proxy option. DataImpulse rotates by port instead. HTTP and HTTPS rotate on port 823, SOCKS5 rotates on port 824, and sticky sessions live on ports 10000 through 20000. Nothing to build, nothing to script. You type the gateway and the port and you're done.

## Which proxy type fits which profile

Incogniton's docs sort hosting types by IP source, and the categories line up closely with how providers sell them. The short version:

| Incogniton's category | What the IP actually is | Good for | Detection risk |
| --- | --- | --- | --- |
| Static residential | Real home devices | Account management, ecommerce, ad verification | Low |
| ISP | Hosted on servers but registered to real ISPs | Long-lived logins that need speed plus legitimacy | Low |
| Datacenter | Server farm subnets | Scraping, SEO monitoring, bulk non-sensitive requests | Higher |
| Mobile | Carrier IPs, 4G/5G/LTE | Hardest targets, mobile-first platforms | Lowest |
| Free shared | Unknown, shared | Fingerprint testing only | Not suitable for accounts |

Incogniton's guidance that static residential suits roughly 80% of use cases is a reasonable rule of thumb. If you're managing store accounts, social profiles, or anything with a login, residential is the default answer. Datacenter IPs are cheaper and faster, and you will feel that in a scraping job, but they come from subnets that anti-fraud systems flag on sight. Spending datacenter money on account profiles is a false economy: the account goes down and you've saved nothing.

Mobile is the expensive end and only makes sense when the target genuinely behaves differently for carrier traffic, which is mostly mobile app automation and ad verification.

## The full DataImpulse lineup, price by price

DataImpulse runs four products on one pay-as-you-go balance. There is no subscription, no monthly minimum, and unused traffic doesn't expire, so leftover GB carry into next month instead of vanishing on the 1st. Plan tiers are named Intro, Basic, Advanced and Custom+, and the per-GB rate drops as you commit to more volume.

| Product | Entry package | Per-GB rate | Volume pricing | Coverage & targeting | Buy |
| --- | --- | --- | --- | --- | --- |
| Residential Proxies | $5 for 5 GB (Intro) | $1/GB | 1 TB for $800 ($0.80/GB) | 90M+ ethically sourced IPs, 195 countries; country targeting included, city/ZIP/ASN are paid add-ons billed at 2× on this product | Buy residential traffic at $1/GB |
| Datacenter Proxies | $5 for 10 GB (Intro) | $0.50/GB | $50/100 GB, $450/1 TB ($0.45/GB), custom from $2,250 for 5 TB+ | Country targeting included; state/city/ZIP/ASN listed as included; 99.9% uptime | Buy datacenter traffic at $0.50/GB |
| Mobile Proxies | $5 for 2.5 GB (Intro) | $2/GB | $50/25 GB, $1,600/1 TB ($1.60/GB), custom from $8,000 for 5 TB+ | Real 4G/5G/LTE device IPs across 195 countries | Buy mobile IPs at $2/GB |
| Premium Residential Proxies | $5 for 1 GB (Intro) | $5/GB | $50/10 GB, custom pricing from $20,000 for 5 TB+ | High-speed residential pool, all targeting options included, dedicated account manager | Compare premium residential pricing |

Two things in that table decide your actual bill more than the headline rates.

The first is targeting. On standard residential, country selection is free, but the moment you need city, ZIP, state or ASN precision, that traffic is billed at double the normal per-GB rate. Premium residential bundles all of it in, which is part of why it costs $5/GB instead of $1. Datacenter lists state, city, ZIP and ASN as included, so a geo-specific scraping job is often cheaper on the datacenter product even before the $0.50/GB rate.

The second is that all four products draw from the same balance model. You top up, you spend down. If you buy the $5 Intro package and decide residential is wrong for your job, you're out five dollars, not a year of prepaid bandwidth.

For a sense of where that sits in the market, DataImpulse publishes a 99.51% success rate and a G2 rating of 4.8/5, and the Incogniton integration page notes the company has served more than 500,000 customers since launch. The obvious caveat: a cheap IP pool that gets blocked has a higher real cost per usable request than a clean pool at a higher sticker price, which is exactly why you test on your own targets before scaling.

## Adding DataImpulse credentials to an Incogniton profile

This is the part people get wrong, mostly by using the wrong port for the protocol they selected.

1. Create a DataImpulse account and top up. 👉 Grab the $5 / 5 GB Intro package and start testing
2. In your DataImpulse dashboard, copy the login, password and gateway host. Every connection uses `gw.dataimpulse.com`.
3. In Incogniton, open Profile Management and click New profile (or edit an existing one).
4. Click the Proxy tab on the left, then choose Custom proxy.
5. Pick the connection type. HTTP or HTTPS goes with port **823**. SOCKS5 goes with port **824**.
6. Fill in the fields. The credential string looks like `http://login:password@gw.dataimpulse.com:823`.
7. Click Check. Incogniton will return the external IP, its location and the timezone, which is also how you catch a mismatch between the profile's stated timezone and the IP's actual geography.
8. Create the profile and click Start. Incogniton opens an isolated browser window on that IP.

Country targeting happens inside the username field, not in the app. The format is `login__cr.us` for the United States, `login__cr.au` for Australia, and so on. Note the double underscore before `cr`. Getting that wrong is the most common reason a "correct" proxy config fails the check.

If you already have a proxy subscription elsewhere, the same tab accepts those credentials, so you don't have to replace what's working just to try something cheaper for one workload.

## Sticky sessions versus rotation, per profile type

Here's the distinction that saves accounts.

Rotating means a new IP on every request, which is what you want for scraping and price monitoring. Sticky means the same IP is held for a window, and DataImpulse's sticky ports run from 10000 to 20000 with rotation intervals configurable from 1 to 120 minutes. The default, if you don't specify one, is 30 minutes.

For a logged-in account profile, rotation is a liability. Logging in from a US residential IP and then making the next request from a German one is the kind of pattern that triggers a security check, and repeated triggers lead to verification walls or outright bans. Pick one sticky port per profile and keep it. Incogniton stores the proxy in the Proxy Library, so a profile's IP configuration survives restarts instead of being retyped.

There's also a `sessid` parameter if you need to pin a specific IP address from the pool for around 30 minutes. Appending something like `__cr.au;sessid.123` to the username routes you back to the same address labelled 123 in Australia. It's a useful tool when you want two profiles that legitimately share a location to hold still, and a bad idea to use for two profiles that shouldn't look related.

For a scraping profile, the opposite applies. Rotating residential on port 823, no session pinning, and let each request come from somewhere new.

## Where the real cost lands

A browser profile that logs in, browses and posts uses very little bandwidth. A profile running a scraping job can chew through GB in an afternoon. This is why the pay-as-you-go model fits Incogniton work better than a monthly seat plan: your heavy days and light days don't average out, and with DataImpulse nothing expires while you wait for the next campaign. Working with high-concurrency automation? 👉 Check the volume tiers before you buy at sticker price

The practical sequence is the same every time. Buy the $5 Intro package, point it at your real targets, and watch the dashboard. If residential holds and the consumption looks manageable, you'll know your cost per successful request. If you find yourself doing mostly unprotected technical checks, switch that workload to datacenter at $0.50/GB and cut the bill in half.

Premium residential at $5/GB needs a specific justification. It makes sense when you're running something where a failed request is expensive, you need city and ZIP precision without the 2× surcharge, and you want an account manager to escalate to. If you're testing whether Incogniton proxies work for a side project, it's five times the price for a problem you probably don't have yet.

## What breaks, and how it shows up

Most Incogniton proxy failures fall into four buckets.

The proxy check returns an error. Usually the port doesn't match the protocol: SOCKS5 on 823, or HTTP on 824. Sometimes it's the country-string syntax in the username.

The profile loads but shows your real location. The proxy wasn't saved before the profile was created, or the profile was started from a list that still pointed at an older entry. Incogniton shows the assigned proxy in the Proxy column on the profiles list, which is the fastest way to spot a profile running naked.

Everything is technically working but logins keep asking for verification. Almost always rotation on an account profile, or a timezone that doesn't match the IP's city. Incogniton will show you the IP's timezone during the check, and you should set the profile to match it rather than assuming.

Pages load slowly on residential. Real home connections are slower than datacenter IPs by nature. If speed is the constraint and the target isn't defended, you're on the wrong product, not the wrong provider.

## The short version

Incogniton handles fingerprints. You handle IPs. The combination only works when each profile gets its own address that stays put for the life of the account, and when that address looks like a normal home connection rather than a server subnet.

For most Incogniton workflows that means residential proxies on sticky ports, country targeting switched on, and city-level precision only where you actually need it. DataImpulse's $1/GB entry rate with non-expiring traffic keeps the experiment cheap: five dollars tells you whether the pool holds on your targets, and the datacenter and mobile products are there when a workload needs speed or carrier IPs instead.
