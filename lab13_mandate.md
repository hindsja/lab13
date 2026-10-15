# Lab 13 — Portfolio Mandate Defense

## Scope and attribution

This file records the AI-assisted implementation, verification, and proposed
counterexample for the fictional Meridian Foundation Reserve. The case inputs
and A–C reference answers come from the course's Lab 13 instructions. Candidate D,
the code, classifications, judgments, and explanatory analysis below were generated
with Codex on October 15, 2026. The student reported that the professor permits
AI completion of this lab; that reported permission is the basis for AI assistance
on sections otherwise marked AI-off in the published instructions. No separate
instructor communication was inspected. Execution and verification reported here were performed by the
AI through a local Python process; no independent student execution or personal
reflection is claimed. No portfolio optimization was performed.

## S1–S10 classification and IPS mapping

These are AI-assisted classifications under the reported permission, not a
reconstructed pre-AI baseline. Some statements contain multiple policy elements;
the table identifies their main role and explains the distinction.

| Statement | Type | Reason and IPS role |
|---|---|---|
| S1: Seven-year horizon; annual rebalancing | Hard constraint | Treat the stated horizon and rebalancing schedule as binding planning and implementation requirements; a snapshot of weights cannot establish whether future rebalancing occurs. |
| S2: CPI + 4% annually, net of costs, over rolling five-year periods | Objective | Specifies the desired return relative to inflation, the treatment of costs, and the evaluation period; it is a performance target rather than a guaranteed outcome. |
| S3: $0.75 million quarterly payout; twelve months held in eligible assets | Hard constraint | Establishes a funding obligation and minimum payout reserve, computed as 3.0% at the stated portfolio size. |
| S4: 60% global equities / 40% U.S. investment-grade bonds benchmark | Preference | Within the lab's five categories, this is best treated as a reference for performance evaluation, not an instruction to hold those exact weights; the more precise IPS role is benchmark specification. |
| S5: Long-only; no borrowing, short sales, or derivatives | Hard constraint | Restricts permissible investment methods; nonnegative weights check the long-only component in the supplied universe. |
| S6: No single issuer above 8% | Hard constraint | Caps combined exposure to each nonexempt company; Acme's bonds and stock must be aggregated under the case's issuer convention. |
| S7: Annual turnover at most 25% without approval | Hard constraint | Creates a conditional trading limit and an approval process; checking it requires turnover information and any committee authorization. |
| S8: At least 90% redeemable within five business days | Hard constraint | Specifies a measurable portfolio liquidity minimum using the supplied redemption times. |
| S9: No calendar-year loss greater than 10%, with no measure or breach rule | Unverifiable promise | As written, it lacks the operational definition and response needed for an enforceable risk policy; the checker must not turn it into a tested guarantee. |
| S10: No listed preferences; no pilot optimization objective | Missing | The absence of sector, ESG, tax, and home-bias preferences adds no such restrictions; the unresolved element is what criterion the pilot should optimize. |

S4 is the least exact fit to the offered categories: a benchmark is an explicit
evaluation standard, even though it is not necessarily a binding allocation.
The preference label describes its role in selecting holdings, not permission
to disregard it when evaluating performance. S1 also combines two ideas: a
planning horizon and a recurring operating requirement.

## Reserve calculation and checker

Required payout reserve:

\[
\text{Reserve minimum}=
\frac{4\text{ quarters}\times\$0.75\text{ million per quarter}}
{\$100\text{ million}}\times100=3.0\%.
\]

All portfolio weights are percentages of the $100 million portfolio. A weight
of 2 means 2%, equivalent to $2 million at the supplied reserve size.

`mandate.py` calculates five measurements directly from editable inputs:

1. Sum of all holding weights, compared with 100%.
2. Lowest holding weight, compared with zero.
3. Sum of reserve-eligible holding weights, compared with the computed reserve minimum.
4. Sum of weights with redemption times within the specified window, compared with 90%.
5. Largest combined company weight, compared with 8%, after exempting cash, Treasury securities, and diversified funds.

Acme's stock and bonds share one issuer key, so their weights are added before
the concentration check. Holdings with zero allocations remain in each candidate.
The script compares unrounded measurements and displays one decimal place; only
the sum-to-100 check uses a small floating-point tolerance of 0.000000001 percentage
points. Negative weights are reported by the long-only check rather than stopped
as invalid input, allowing the required probe to run.

## Verified results

The following values and pass/fail flags were checked against actual execution.
For A–C, every value and flag matched the supplied reference table.

| Check | A | B | C | D |
|---|---:|---:|---:|---:|
| Weights sum to 100 | 100.0 pass | 100.0 pass | 100.0 pass | 100.0 pass |
| Lowest weight is nonnegative | 0.0 pass | 0.0 pass | 0.0 pass | 0.0 pass |
| Reserve weight at least 3.0% | 14.0 pass | 1.0 FAIL | 2.0 FAIL | 100.0 pass |
| Redeemable within five days at least 90% | 100.0 pass | 85.0 FAIL | 100.0 pass | 100.0 pass |
| Largest single company at most 8% | 0.0 pass | 9.0 FAIL | 8.0 pass | 0.0 pass |
| Calendar-year loss | requires CIO clarification | requires CIO clarification | requires CIO clarification | requires CIO clarification |
| Verdict | passes every checkable rule | fails 3 rule(s) | fails 1 rule(s) | passes every checkable rule |

Thus A passes the implemented rules, B fails the reserve, liquidity, and company
concentration checks, and C fails the reserve check. Passing these checks does
not establish full mandate compliance or authorize investment.

For the required probe, A's Cash input was temporarily changed from 2 to -2 and
its global equity fund input from 60 to 64. The actual script was rerun. A's
total remained 100.0, lowest weight became -2.0 FAIL, reserve weight became
10.0 pass, liquid weight remained 100.0 pass, and largest company remained 0.0
pass. Only long-only failed. Both inputs were restored, and the rerun matched the
original output exactly.

Additional changed-input checks confirmed that a quarterly distribution of
$1 million produces a 4.0% reserve requirement and a 90-business-day liquidity
window makes B's liquid share 100.0%. A nonfinite weight was rejected. These
temporary changes were not retained in the final inputs.

## Candidate D — AI-proposed attack

| Holding | Weight (%) |
|---|---:|
| Cash | 100 |
| Treasury bills | 0 |
| Treasury 10-year note | 0 |
| Global equity index fund | 0 |
| Investment-grade bond fund | 0 |
| Private real estate fund | 0 |
| Acme Corp bonds | 0 |
| Acme Corp stock | 0 |

**AI-proposed objection, citing S2:** An all-cash portfolio passes the five
implemented checks but supplies no demonstrated basis for achieving CPI + 4%
annually after costs over rolling five-year periods. The CIO could reasonably
reject it for lacking support for the stated return objective. No cash yield,
inflation path, or cost assumptions are supplied, so this is a suitability
objection, not a calculated proof that D must miss the target. The proposal is
a counterexample for evaluation, not an investment recommendation or the
student's independent judgment.

## Candidate D — assessment and skeptical review

**Two-sentence assessment (AI-assisted):** The objection holds as a reason for
the CIO to withhold approval, because passing static allocation checks does not
demonstrate that an all-cash strategy can support the required return.
S2 decides the objection: D needs a credible case for achieving the stated
inflation-relative, net-of-cost objective, although the supplied information
does not establish that it will necessarily fail.

**Skeptical colleague role, simulated:** The statement to cite is S2, but the
critique should not assume that cash earns zero or that its future real return
is known. The defensible conclusion is that the return objective is unsupported
by the supplied evidence, rather than that a specific return shortfall has been
calculated. This is a simulated second-role review; no actual partner exchange
is claimed.

## One question for the CIO

**Question:** Should the pilot minimize portfolio risk while targeting CPI + 4%
net of costs over rolling five-year periods, or maximize expected return within
agreed risk limits?

**Answer that changes the design:** If the CIO chooses the first approach, risk
becomes the quantity to minimize and the return target becomes a feasibility
requirement under documented forward-looking assumptions. That changes the
selection process from favoring the highest expected return among permitted
portfolios to seeking the lowest-risk portfolio that can credibly support the
target. It could lead to less equity exposure, provided the return requirement
remains feasible; the direction and amount cannot be established without data.
If no portfolio can support the target under the agreed limits, the result must
be reported as infeasible instead of quietly relaxing the mandate.

This question resolves the missing optimization choice in S10 while retaining
S2's performance objective. Before implementation, the CIO must also define the
risk measure and treatment of S9; no definition or guaranteed loss protection
is assumed here. No optimization has been run.

## Investment policy statement — sourced background

An investment policy statement (IPS) is a written guide connecting the investor's
purpose to investment objectives, restrictions, responsibilities, and oversight.
It supports consistent decisions and accountability. Objectives describe the
outcomes sought; constraints limit the strategies or holdings available to pursue
them. A useful IPS also explains how performance and risk are evaluated, who
reviews results, and how policies are revised. CFA Institute's institutional IPS
paper discusses return and risk requirements, evaluation horizons, liquidity,
leverage, and other investor-specific considerations. This background is an
AI-assisted summary of the assigned reading, not a personal reflection.
([CFA Institute, Introduction and Section 3](https://rpc.cfainstitute.org/sites/default/files/-/media/documents/article/position-paper/investment-policy-statement-institutional-investors.pdf))

Applied to Meridian, the IPS connects the return objective in S2 to the planning
and implementation terms in S1, funding and liquidity requirements in S3 and S8,
investment restrictions in S5 and S6, and turnover governance in S7. S4 supplies
the evaluation benchmark. S9 needs an operational risk policy, and S10 leaves
the pilot's optimization criterion unresolved. This mapping separates the
client's desired outcome, the limits on pursuing it, and the definitions needed
before an investment process can be implemented.

## Reflection — AI-assisted analysis

A portfolio can pass every checkable rule and still be unsuitable because the
checker establishes only a narrow set of conditions at one point in time. It
does not establish that the portfolio can fund future spending while achieving
the intended net return, remain suitable over the stated horizon, or follow
future trading and monitoring requirements. Candidate D exposes this gap:
meeting the liquidity and concentration tests supplies no evidence of achieving
the return objective. Passing is evidence about the implemented rules, not a
complete investment decision.

The classification with the greatest ambiguity in this analysis is S4. A
benchmark is a standard against which results are evaluated, but it becomes a
binding allocation constraint only if the policy also requires those weights
or specifies permissible deviations. Calling it a hard 60/40 allocation rule
would add a requirement absent from the case. This identifies an analytical
ambiguity; it does not claim a student personally experienced uncertainty or
held a particular view before using AI.

## Model assumptions and limitations

The supplied holdings table controls reserve eligibility and redemption days.
In particular, the 10-year Treasury note is reserve-eligible in this exercise.
The checker uses that designation without inferring price stability from it.
No market prices, return forecasts, cost estimates, or inflation series were used.

This is a static allocation checker. It does not simulate payouts through time,
monitor annual rebalancing, calculate turnover, forecast returns, or verify a
future calendar-year loss limit. Turnover would require trading or prior-position
information not supplied here. The calendar-year loss statement is printed as
requiring clarification and is never assigned a pass/fail flag or included in
the failure count. The long-only test examines the supplied holding weights;
it does not audit borrowing or derivatives hidden inside a fund.

## Reproducibility and sources

Run from the folder containing the script:

```powershell
python mandate.py
```

The script uses only Python's standard library. Verification used Python 3.14.7.
The timestamped [execution record](outputs/lab13_verification.txt) contains the
restored A–D output and the temporary A probe output.

- [Lab 13: case, coding prompt, reference results, and AI boundaries](https://github.com/CinderZhang/FIN43900-Fall2026/blob/main/lessons/week-08/lab-13-portfolio-mandate.md), accessed October 15, 2026.
- [Week 8 prework and assigned IPS reading](https://github.com/CinderZhang/FIN43900-Fall2026/blob/main/lessons/week-08/student-prework.md), accessed October 15, 2026.
- [Course AI Boundary Card](https://github.com/CinderZhang/FIN43900-Fall2026/blob/main/projects/AI-BOUNDARY-CARD.md), accessed October 15, 2026.

The controlling lab instructions were also provided directly in the conversation.
The quiz deadline and any additional Brightspace-only instructions were not verified.
