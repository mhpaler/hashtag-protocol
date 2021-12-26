## Top Taggers

V1 - [GraphQL API](https://api.thegraph.com/subgraphs/name/hashtag-protocol/hashtag-polygon-mumbai/)

```
query {
    taggers(first: 10, orderBy: tagCount, orderDirection: desc) {
        id
        tagCount
    }
}
```

## What is a "Tagger"?

A **_Tagger_** is a user or entity, represented by an Ethereum wallet address,
that pays the fee to execute the tagging contract thereby tagging a piece of
digital content with a HASHTAG token.

[Learn more about Taggers](https://docs.hashtag-protocol.org/essentials/participants.html#tagger-phase-1-2).
