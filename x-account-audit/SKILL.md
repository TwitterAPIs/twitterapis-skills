---
name: x-account-audit
description: Audit one X (Twitter) account from its public data, the honest way. Reads the profile and the recent timeline, separates original posts from replies, retweets and the account's own thread continuations, and reports posting cadence, median reach and engagement, the top posts and what they have in common. Read-only, it never posts, likes, follows or sends DMs. Use when someone asks how an X account is performing, what a competitor posts and what works for them, whether an account's engagement is real, or wants a quick benchmark before a launch.
---

# X account audit

Answers one question: **what does this account post, how often, and what actually lands?**

Most account "audits" average every item on the timeline. That number is wrong
before anyone reads it, because a timeline is not a list of posts. It mixes the
account's original posts with its replies, its retweets of other people, and the
continuation tweets of its own threads. Each of those reaches a different audience
and none of them is a post in the sense the person asking means.

This skill separates them first.

## What it does

1. **Confirms the handle back to you before spending anything.** Read the profile
   with `GET /twitter/user/info?username=<handle>` and state the name, follower
   count and join date. Wait for a yes. A similar handle produces a confident audit
   of the wrong account.
2. **Reads the recent timeline** with `GET /twitter/user/tweets?username=<handle>`,
   following `next_cursor` until it covers the window you agreed (default: the last
   30 days or 3 pages, whichever comes first). Say how many pages and items you read.
3. **Classifies every item** using the flags each one carries:
   - `is_retweet: true` is someone else's post. Report the count, never its metrics.
   - `is_reply: true` with `in_reply_to_status_id` pointing at the account's own
     tweet in the same `conversation_id` is a **thread continuation**.
   - `is_reply: true` to anyone else is a **reply**.
   - everything else is an **original post**. A quote tweet (`is_quote: true`) is the
     account's own post that embeds someone else's, so it counts as original; report
     how many originals were quotes.
4. **Measures originals only**, then reports.

## The measures

| measure | how |
|---|---|
| Cadence | original posts per week over the window, plus the longest gap |
| Reach | MEDIAN `view_count` of originals, with the mean beside it |
| Engagement | median likes, replies, reposts, quotes and bookmarks of originals |
| Like rate | median of likes / views per original, as a percent |
| Top posts | the 3 originals with the most views, each with one line on what it was |
| Thread pull | for each thread, views of the opener against its continuations |

**Median, not mean.** One post that travels moves the mean a long way and the
median hardly at all. Report both and lead with the median; if they differ by more
than 50 percent, say which post is pulling the mean and why.

## The rule worth stealing even if you never install this

**A thread continuation is not a post.** An account that writes 1 post and 3
continuations has posted once. Counting the continuations as posts makes the account
look four times as active and drags every median down, because continuations reach
a fraction of the opener's audience.

That is measured, not hypothetical: see `examples/real-run.md`. On the run there, 3
of the first page's 17 items were the account's own continuations. Counting all 17
puts the median at 22,531 views; counting the 14 originals puts it at 46,072, about
twice as high.

## Hard boundary

**Read-only. This skill never posts, likes, reposts, follows, sends a DM or drafts
anything for you to send.** It calls two GET endpoints and nothing else, even
though the twitterapis API and MCP server can also write for an account you link.
Write tools are not used by this skill and should not be enabled for it.

## Output

The account line (name, handle, followers, join date), then the measures table,
then the top posts, then one paragraph on what the top posts share (format, topic,
media, time of day) that the median posts lack. End with what you could NOT
conclude from public data (for example: impressions from ads, deleted posts, DMs).

Every number states its window and how many originals it rests on. A median of 4
posts is a weak claim; say so.

## Setup

Needs a twitterapis.com API key in `TWITTERAPIS_KEY`, sent as the `x-api-key`
header to `https://api.twitterapis.com`. Get one at
<https://www.twitterapis.com/signup>; new accounts start with free credit and no card.

Calls per run, all reads at $0.0008 each: 1 profile read plus 1 per timeline page.
A 3-page audit is 4 calls, about a third of a cent. Say the call count in the report.
