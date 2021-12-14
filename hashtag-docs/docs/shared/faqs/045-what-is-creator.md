## Top Creators

V1 - [GraphQL API](https://api.thegraph.com/subgraphs/name/hashtag-protocol/hashtag-polygon-mumbai/)

```
query {
    creators(first: 10, orderBy: tagCount, orderDirection: desc) {
        id
        mintCount
        tagCount
        tagFees
    }
}
```

## What is a "Creator"?

A **_Creator_** is the user or entity, represented by a Ethereum wallet address,
that brings the HASHTAG token into existence by executing the HASHTAG token
minting contract.

This can be a user of a **_Publisher_** platform or the **_Publisher_** itself.

The **_Creator_** wallet address is logged in the token and will accrue revenue
from the Protocol if the token is used for tagging content and if the token is
purchased at auction. See [revenue
sharing](https://docs.hashtag-protocol.org/essentials/protocol-overview.html#revenue-sharing).
