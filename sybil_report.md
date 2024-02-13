# Sybil filtering on Starknet

In order to determine a fair set of Starknet users recipients, we performed Sybil filtering on the addresses that meet the eligibility criteria of Provisions, in collaboration with TrustaLabs. Here we give an outline of the steps that we took. All the data involved is gathered from databases of traces, transactions and events on Starknet and Ethereum.

## Clustering step

We first generated clusters of addresses that share similar characteristics. Different well-known metrics were chosen to establish similarity between addresses. Some examples are:
- addresses that are strongly connected according to the transfers or approval events from popular ERC20 tokens contracts.
- addresses that received bridged funds from strongly connected addresses on Ethereum.
- addresses with similar initial funders.

## Filtering step

After generating the clusters, we filtered each cluster individually. We first generated a graph in which two addresses in the cluster are connected if their basic on-chain activity is very similar. We further inspected the components with high connectivity while discarding the less connected addresses. To eliminate false positives from those components, we removed addresses that have noticeable differences from the average behavior of the component.

The components that are still large after the previous filtering will be regarded as sybil addresses and excluded from eligibility.