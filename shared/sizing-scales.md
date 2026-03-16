# Sizing Scales Reference

## Overview

A **sizing scale** is the unit system used to estimate work complexity. The po-suite supports three built-in scales and allows custom scales. Teams select their scale once per engagement, and the choice is persisted to `.po/config.md`.

## Available Scales

| Scale | Values | When to use |
|-------|--------|-------------|
| T-shirt | XS, S, M, L, XL | Default. Quick relative sizing. |
| Fibonacci | 1, 2, 3, 5, 8, 13 | Teams that use story points. |
| Custom | User-defined | Skill asks the user to define values at first use. |

### T-shirt Scale (Default)

**Values:** XS, S, M, L, XL

Best for teams that prefer relative, qualitative sizing without numerical abstraction. Useful when:
- The team is new to estimation
- Work involves significant unknowns
- Quick sizing conversations are prioritized over precision
- Burndown charts track by count, not velocity

### Fibonacci Scale

**Values:** 1, 2, 3, 5, 8, 13

The standard story point scale. Useful when:
- The team practices velocity-based planning
- Historical velocity data guides forecasting
- Uncertainty naturally clusters into these intervals
- Backlog refinement benefits from finer granularity

### Custom Scale

**Values:** User-defined on first use

When a team has domain-specific conventions (e.g., hours, "small/medium/large/huge", or abstract complexity levels), they can define a custom scale. The first time a `po-*` skill runs, it prompts:

```
What sizing scale would you like to use?
1. T-shirt (XS, S, M, L, XL)
2. Fibonacci (1, 2, 3, 5, 8, 13)
3. Custom (enter your own values)
```

If the user selects **Custom (3)**, the skill prompts for comma-separated values:

```
Enter your sizing scale values (comma-separated):
```

The user enters values like `small,medium,large` or `1h,2h,4h,8h`, and the scale is persisted.

## Scale Persistence

The active scale is stored in `.po/config.md` under the `scale` field:

```yaml
# .po/config.md (example)
scale: t-shirt
# or
scale: fibonacci
# or
scale: custom
custom_scale_values: small,medium,large
```

Once set, all `po-*` skills use the persisted scale for that engagement. Teams do not re-select the scale on each skill invocation.

## Who References This Standard

- `po-discover` — suggests sizing during feature discovery
- `po-breakdown` — applies the active scale when sizing decomposed issues
- `po-refine` — checks that issue sizes still make sense during refinement
- `po-scope-check` — validates task sizing against the active scale
