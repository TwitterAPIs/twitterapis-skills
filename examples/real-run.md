# Real run: `x-account-audit` on @vercel

Run against the live twitterapis.com API on 2026-10-19. Two calls, one page of
timeline, $0.0016 in total. Nothing below is illustrative: every number comes from
the two responses.

## Calls

```
GET /twitter/user/info?username=vercel
GET /twitter/user/tweets?username=vercel
```

The profile: Vercel, @vercel, 464,635 followers, following 10, joined 2016-01-01.

The timeline page returned **17 items** covering Sep 25 to Oct 19, with
`has_more: true`. The page size is set by X, about 13 to 20 per page; a larger
`count` is ignored.

## Classification

| kind | count | how it was told apart |
|---|---|---|
| original posts | 14 | `is_reply`, `is_retweet` and `is_quote` all false |
| thread continuations | 3 | `is_reply: true`, replying to the account's own tweet in the same `conversation_id` |
| replies to others | 0 | |
| retweets | 0 | |

The 3 continuations were two follow-ups to an index announcement and one follow-up
to an enterprise announcement. They drew 3,174, 2,971 and 22,531 views.

## Measures (14 originals)

| measure | value |
|---|---|
| median views | 46,072 |
| mean views | 71,914 |
| median likes | 225.5 |
| median like rate | 0.48% of views |
| top post by views | 277,370 views: a security bounty result with concrete numbers (reports filed, dollars paid, one kernel zero day) |

The mean is 56 percent above the median, so the report names what pulls it: two
posts above 199,000 views, the bounty result and a sports-partnership post.

## The two things this run taught us

1. **Continuations halve the median if you count them.** All 17 items give a median
   of 22,531 views; the 14 originals give 46,072. Same account, same day, a 2x
   difference from one classification step. This is why the skill classifies
   before it measures.
2. **A page is about 20 items, whatever you ask for.** X decides the page size, so
   this page held 17. An audit that reads one page and calls it "the last 20 posts" is
   wrong about its own window. Report the dates the items actually span, and page
   with `next_cursor` until the window you agreed is covered.
