## Recently Tagged Content

V1 - [GraphQL API](https://api.thegraph.com/subgraphs/name/hashtag-protocol/hashtag-polygon-mumbai/)

```
query {
    tags(first: 5, orderBy: timestamp, orderDirection: desc) {
        id
        hashtagId
        hashtagDisplayHashtag
        nftContract
        nftContractName
        nftImage
        nftName
        nftDescription
        nftId
        tagger            
        timestamp
        publisher
        nftChainId
    }
}
```

## What is "tagged content"?

Any content that has been tagged with a HASHTAG token by a Tagger on an enabled
Publisher platform. This linkage is saved to a decentralized,
database (aka "on chain") making it immutable and globally accessible.
