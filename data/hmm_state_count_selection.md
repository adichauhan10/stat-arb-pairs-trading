# HMM state count

|   n_states |   loglik |      BIC |
|-----------:|---------:|---------:|
|          2 | -6075.49 | 12237.9  |
|          3 | -6020.79 | 12199.6  |
|          4 | -4810.36 |  9865.63 |

BIC keeps improving as states are added, which is typical for HMMs - more flexibility always fits the training data better. Going with 2 states anyway: it keeps the trading gate simple (trade / don't trade) and each regime interpretable as calm vs volatile, rather than needing a post-hoc story for 3-4 abstract states. Interpretability over fit, deliberately.
