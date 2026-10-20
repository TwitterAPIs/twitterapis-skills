# TwitterAPIs skills

Open-source Claude skills that read X (Twitter) through
[twitterapis.com](https://www.twitterapis.com). MIT licensed.

```bash
npx skills add TwitterAPIs/twitterapis-skills --skill x-account-audit
```

## Skills

### `x-account-audit`

**How is this X account actually doing?**

Reads the profile and the recent timeline, separates original posts from replies,
retweets and the account's own thread continuations, and reports cadence, median
reach and engagement, and the top posts with what they have in common.

See [`examples/real-run.md`](examples/real-run.md) for a complete run against the
live API, including the two things that run taught us.

## Read-only, by design

**These skills never post, like, repost, follow or send DMs, and never draft a
message for you to send.** Every call they make is a GET of public data.

The twitterapis API can also act for an X account you link, so this is a choice
the skills make rather than a limit of the API: they call read endpoints only, and
you should not enable write tools for them.

## The rule worth stealing even if you never install this

**A thread continuation is not a post.**

An X timeline mixes original posts with the account's own thread continuations,
which reach a fraction of the opener's audience. Count them as posts and the
account looks more active and every median drops. On the run in the example, the
same page gives a median of 22,531 views counted naively and 46,072 counted
honestly.

## Setup

Set `TWITTERAPIS_KEY`. Get a key at
[twitterapis.com/signup](https://www.twitterapis.com/signup); new accounts start
with free credit and no card required.

Reads are $0.0008 per call. A three-page audit is four calls.

## Contributing

Issues and pull requests welcome. If a classification or a number looks wrong,
open an issue with the handle and the API response itself, not a description of it.
