# BEBRM: Bayesian Enhanced Baseline-and-Residual Model for Basketball Analytics

A novel framework for evaluating NBA players' on-court impact by integrating non-traditional performance metrics with possession-level adjustments.

## Overview

BEBRM addresses key limitations in existing player impact metrics by combining:
- **Rich feature-based priors** from non-box-score data (hustle stats, defensive matchups, Synergy play-types)
- **Iterative possession-level adjustments** that account for lineup context and opponent strength
- **Bayesian uncertainty quantification** that provides credible intervals alongside point estimates

## Key Features

### 🎯 Multi-Source Data Integration
Unlike traditional metrics that rely heavily on box-score statistics, BEBRM incorporates:
- Defensive matchup outcomes (opponent FG% when guarded, forced turnovers)
- Hustle statistics (deflections, contested shots, screen assists, charges drawn)
- Synergy play-type efficiencies (isolation, pick-and-roll, spot-up, post-up)
- Spatial shot tracking data (spacing metrics, offensive "gravity")
- On/off efficiency splits

### 📊 Two-Stage Modeling Approach

**Stage 1: Bayesian Baseline Prior**
- Fits separate offensive and defensive regressions on multi-source features
- Applies Bayesian ridge regularization to shrink extreme estimates
- Produces initial player ratings with uncertainty estimates

**Stage 2: Iterative Residual Correction**
- Computes possession-level residuals (actual vs. expected outcomes)
- Allocates residuals to players via ridge regression
- Iterates multiple rounds to refine ratings based on lineup context

### 🔄 Small-Sample Stability
- Players with limited minutes are automatically shrunk toward league average
- Wide credible intervals signal uncertainty for small-sample players
- Prevents overreaction to short hot/cold streaks

### 📈 Uncertainty Quantification
- Provides credible intervals for each player's rating
- Distinguishes between confident estimates (veterans) and uncertain ones (rookies)
- Enables more informed decision-making in front-office discussions

## Algorithm

### Baseline Model
For each player $i$, we compute baseline ratings:

$$\beta_{i}^{(\text{base, OFF})} = \hat{\boldsymbol{\theta}}^{(\text{off})\top}\mathbf{X}_{i}^{(\text{off})}$$

where $\hat{\boldsymbol{\theta}}$ is obtained via Bayesian ridge regression with prior $\boldsymbol{\theta} \sim N(\mathbf{0}, \alpha^2 I)$.

### Possession-Level Adjustment
For each possession $\nu$, compute residual:

$$r_{\nu}^{(\text{OFF})} = y_{\nu}^{(\text{OFF})} - \frac{1}{|A_{\nu}|} \sum_{i \in A_{\nu}} \beta_{i}^{(\text{base, OFF})}$$

Then iteratively solve ridge regression to allocate residuals back to players.

### Final Output
- Separate offensive and defensive ratings
- Combined total impact: $\beta_{i}^{(\text{final, TOTAL})} = \beta_{i}^{(\text{final, OFF})} + \beta_{i}^{(\text{final, DEF})}$
- Credible intervals from Bayesian posterior

## Comparison to Other Metrics

| Feature | BPM | RAPM | EPM | LEBRON | BEBRM |
|---------|-----|------|-----|--------|-------|
| Box-score features | ✅ | ❌ | ✅ | ✅ | ✅ |
| On/off adjustments | ❌ | ✅ | ✅ | ✅ | ✅ |
| Hustle/matchup data | ❌ | ❌ | Partial | ❌ | ✅ |
| Synergy play-types | ❌ | ❌ | Partial | ❌ | ✅ |
| Bayesian shrinkage | ❌ | ❌ | Partial | ❌ | ✅ |
| Iterative adjustment | ❌ | ❌ | ❌ | ❌ | ✅ |
| Uncertainty estimates | ❌ | ❌ | ❌ | ❌ | ✅ |

## Data Requirements

BEBRM requires access to multiple NBA data sources:
- **Play-by-play logs**: Possession-level outcomes and player on/off tracking
- **Matchup data**: Defensive assignment statistics (available via NBA Stats API)
- **Hustle stats**: Deflections, contested shots, etc. (available via NBA Stats API)
- **Synergy data**: Play-type efficiencies (requires Synergy Sports subscription)
- **Shot tracking**: Spatial shot data (available via NBA Stats API)

## Project Structure

```
├── data/
│   ├── raw/              # Raw data from NBA APIs
│   └── processed/        # Cleaned feature matrices
├── src/
│   ├── features/         # Feature engineering from multiple sources
│   ├── baseline/         # Bayesian baseline model
│   ├── residual/         # Possession-level residual correction
│   └── utils/            # Helper functions
├── notebooks/            # Analysis notebooks
├── paper/                # LaTeX source for methodology paper
└── README.md
```

## Limitations

- **Data access**: Full implementation requires Synergy Sports subscription
- **Computational cost**: Iterative ridge regression is more expensive than single-pass methods
- **Complexity**: More parameters to tune compared to simpler metrics
- **Validation needed**: Empirical testing on real NBA data required to assess practical utility

## Future Work

- Hierarchical extensions for team/lineup effects
- Rookie vs. veteran prior differentiation
- Predictive validation for game outcomes
- Player development trajectory modeling

## References

1. Rosenbaum, D. (2004). *Measuring how NBA players help their teams win*. 82games.com
2. Kubatko, J., et al. (2007). *A starting point for analyzing basketball statistics*. JQAS
3. Sill, J. (2010). *Improved NBA adjusted +/- using regularization*. MIT SSAC
4. Winston, N. J. (2014). *A Bayesian hierarchical model for adjusted plus-minus*. JSA
5. Guest, D. (2020). *Estimated Plus/Minus*. Dunks & Threes
6. BBall Index (2020). *Introducing LEBRON*. bball-index.com

## Author

**Zhengze Zhang**  
Statistics M.A., Columbia University  
[GitHub](https://github.com/Garthzzz) | [Email](mailto:zhangzhengze2018@gmail.com)

## License

MIT License
