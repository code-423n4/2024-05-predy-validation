# [L-01] Uniswap oracle should not be used as a source of pricing on L2s

## Impact

Ingestion of easily manipulated oracle prices, leading to multiple weird cases, for e.g verification could now go wrong cause the price returned from the Uniswap oracle is way different, or liquidation wouldn't go through cause a wrong price could make the position seem to be safe.

## Proof of Concept

Protocol uses different modes for their pricing logic via oracles which includes Uniswap, however when it comes to L2s, keep in mind that protocol have clearly stated that they would deploy to multiple EVM compatible chain which include different L2s(Arbitrum, Base, Polygon), Uniswap advises developers not to use Uniswap oracle on L2s as price feeds are very much easier to be manipulated.

### Oracles Integrations on Layer 2 Rollups

Optimism On Optimism, every transaction is confirmed as an individual block. The block.timestamp of these blocks, however, reflect the block.timestamp of the last L1 block ingested by the Sequencer. For this reason, Uniswap pools on Optimism are not suitable for providing oracle prices, as this high-latency block.timestamp update process makes the oracle much less costly to manipulate. In the future, it's possible that the Optimism block.timestamp will have much higher granularity (with a small trust assumption in the Sequencer), or that forced inclusion transactions will improve oracle security. For more information on these potential upcoming changes, please see the [Optimistic Specs repo](https://github.com/ethereum-optimism/optimistic-specs/discussions/23). For the time being, usage of the oracle feature on Optimism should be avoided.

This quoted [statement](https://docs.uniswap.org/concepts/protocol/oracle#oracles-integrations-on-layer-2-rollups) made from the Uniswap team is regarding integration on L2 Optimism, where every transaction is a different block, however, it is also valid for Arbitrum, as the average block time on Arbitrum is ~0.25s.

## Recommendation

Do not use Uniswap's oracle on L2s like Optimism/Arbitrum for the time being.