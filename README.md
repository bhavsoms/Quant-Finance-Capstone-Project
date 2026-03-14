# Vasicek Short Rate Model — Quantitative Finance Capstone
### IIQF Certification | Fixed Income Derivatives & Interest Rate Modelling

---

## About This Project

This capstone implements the Vasicek (1977) one-factor short rate model end-to-end,
from live market data calibration through to derivative pricing. The work covers
analytical bond pricing, Monte Carlo simulation, interest rate swap valuation,
and swaption pricing under the Black model with a rigorous treatment of numeraire
theory.

The Vasicek model describes the instantaneous short rate as a mean-reverting
stochastic process:

    dr(t) = a(b - r(t)) dt + σ dZ(t)

where a is the speed of mean reversion, b is the long-run equilibrium rate,
σ is the volatility of the short rate, and dZ(t) is a standard Wiener process increment.

---

## Results Summary

| Sub-question | Description | Result |
|---|---|---|
| I(a) | Mean reversion speed a | 0.1500 |
| I(a) | Long-run mean b | 2.00% |
| I(a) | Short rate volatility σ | 0.0064 |
| I(b) | Analytical ZCB price Z(0,5) | $0.8297 |
| I(c) | Monte Carlo ZCB price r(0)=4% | $0.8437 ± 0.000190 |
| I(d) | 5-Year par swap rate | 3.8258% |
| I(e) | European call on ZCB | $58.37 |
| II(a) | SOFR overnight rate fetched | 3.65% |
| II(b) | European swaption price | $4.2119 |

---

## Structure

The entire project lives in a single well-documented Jupyter notebook:
`Vasicek_Capstone.ipynb`

The notebook is divided into nine sequential code cells, each preceded by
a markdown explanation of the theory, formula derivation, and any known
model limitations. The structure is as follows:

Part I covers the Vasicek model applied to US interest rate data. Section (a)
calibrates the three model parameters using OLS regression on the discretized
stochastic differential equation, with live data fetched directly from the
Federal Reserve's FRED API. Section (b) derives the analytical zero-coupon bond
price using the exponential affine formula. Section (c) prices the same bond
using Monte Carlo simulation with antithetic variates as the variance reduction
technique, and validates the result against the analytical price. Section (d)
computes the fixed leg swap rate of a five-year interest rate swap where the
floating leg is linked to SOFR. Section (e) prices a European call option on
a zero-coupon bond using Monte Carlo, with the bond price at the option maturity
computed analytically via the Vasicek affine formula at each simulated short rate.

Part II covers swaption pricing and numeraire theory. Section (a) constructs a
SOFR term structure using live overnight SOFR data from FRED and linear
interpolation across representative tenor points. Section (b) values a European
payer swaption using Black's model under the lognormal forward swap rate
assumption. Section (c) explains the concept of a numeraire and identifies the
annuity measure as the natural numeraire for European swaption pricing, showing
that the forward swap rate is a martingale under this measure which is precisely
what validates Black's formula. Section (d) extends the numeraire discussion to
Bermudan swaptions, where the presence of multiple exercise dates requires a
switch to the rolling money-market account as numeraire, and introduces the
Longstaff-Schwartz least-squares Monte Carlo algorithm as the appropriate
valuation method.

---

## Calibration Note

OLS calibration on the 2000 to 2024 FEDFUNDS series yields a near-zero mean
reversion estimate due to the non-stationarity of the data across two distinct
trending regimes. The pre-2008 stationary subperiod is used for estimating a
and b, with sigma retained from the full sample for a more robust volatility
estimate. A practitioner floor of a = 0.15 is applied consistent with
published Vasicek estimates for USD short rates as documented in Brigo and
Mercurio (2006). This limitation is acknowledged and MLE with a Kalman filter
is noted as a more robust alternative for regime-switching rate environments.

---

## Known Model Limitations

The Vasicek model is an affine Gaussian model which means the short rate can
take negative values with positive probability. This was observed in a subset
of Monte Carlo paths in Part I(c) and Part I(e). In practice, the Cox-Ingersoll-Ross
model or the Hull-White extended Vasicek model are preferred when negative rates
are undesirable. The negative rate paths are retained in this implementation as
they are a known and documented property of the model rather than a coding error.

---

## Data Sources

Federal Reserve Economic Data (FRED), Federal Reserve Bank of St. Louis.
Series used: FEDFUNDS (monthly federal funds effective rate) and SOFR
(secured overnight financing rate). Data accessed programmatically via
the FRED public CSV endpoint, requiring no API key.

---

## References

Brigo, D. and Mercurio, F. (2006). Interest Rate Models Theory and Practice.
Springer Finance. Sections 3.2 and 3.3 for Vasicek model and calibration,
Section 2.3 for numeraire and change of measure, Chapter 6 for swap market models.

Glasserman, P. (2004). Monte Carlo Methods in Financial Engineering.
Springer. Chapters 3 and 4 for path simulation and variance reduction techniques.

Hull, J. (2022). Options Futures and Other Derivatives. Pearson.
Chapter 7 for interest rate swaps, Chapter 29 for Black's model applied to swaptions.

Tuckman, B. and Serrat, A. (2011). Fixed Income Securities. Wiley.
Chapter 2 for yield curve construction and interpolation methods.
