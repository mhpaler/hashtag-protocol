## Top Hashtags

V1 - [GraphQL API](https://api.thegraph.com/subgraphs/name/hashtag-protocol/hashtag-polygon-mumbai/)

```
query {
    hashtags(first: 10, orderBy: tagCount, orderDirection: desc) {
        id
        name
        displayHashtag
        owner
        publisher
        tagCount
    }
}
```


## What's a Hashtag Token (HASHTAG)?

Hashtag Tokens are digital representations of hashtag text strings stored as
non-fungible tokens (NFT) on the Ethereum blockchain.
