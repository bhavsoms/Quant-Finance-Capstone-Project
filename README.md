# Vasicek Short Rate Model — Quantitative Finance Capstone
### Indian Institute of Quantitative Finance (IIQF) | www.iiqf.org
### Short Rate Modelling & Interest Rate Derivatives


## About This Project

This project implements the Vasicek (1977) one-factor short rate model, a mean-reverting
Gaussian process widely used in fixed income trading, risk management, and derivatives
pricing across rates desks globally. Starting from the stochastic differential equation:

    dr(t) = a(b - r(t)) dt + σ dZ(t)

the model is calibrated directly from live Federal Reserve data and applied across the
full derivatives pricing chain — from zero-coupon bonds through interest rate swaps,
bond options, and swaptions — demonstrating an end-to-end quantitative workflow
consistent with industry practice.


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
| II(b) | European receiver swaption price (notional $100, continuous compounding) | $0.3947 |


## Structure

The entire project is implemented in `IIQF_Capstone.ipynb`, a Google Colab
notebook that runs end-to-end with no local setup required. Every code section
is preceded by a markdown cell explaining the theory, formula derivation, and
any known model limitations.

Part I covers the Vasicek model applied to US interest rate data. Section (a)
calibrates the three model parameters using OLS regression on the discretized
stochastic differential equation, with live data fetched directly from the
Federal Reserve FRED API. Section (b) derives the analytical zero-coupon bond
price using the exponential affine formula. Section (c) prices the same bond
using Monte Carlo simulation with antithetic variates as the variance reduction
technique and validates the result against the analytical price. Section (d)
computes the fixed leg swap rate of a five-year interest rate swap where the
floating leg is linked to SOFR. Section (e) prices a European call option on
a zero-coupon bond using Monte Carlo, with the bond price at the option maturity
computed analytically via the Vasicek affine formula at each simulated short rate.

Part II covers swaption pricing and numeraire theory. Section (a) constructs a
SOFR term structure using live overnight SOFR data from FRED and linear
interpolation across representative tenor points. Section (b) values a European
receiver swaption using Black's model under the lognormal forward swap rate
assumption, where the holder has the right to enter a 5-year swap after 2 years,
receiving the floating rate and paying the fixed strike rate of 4.5%, with
semi-annual cash flows, notional of $100, and continuous compounding. The swaption
is out of the money since the forward swap rate S(0) = 5.47% exceeds the fixed
strike of 4.5%, meaning the receiver pays above-market fixed. Section (c) explains
the concept of a numeraire and identifies the annuity measure as the natural
numeraire for European swaption pricing, showing that the forward swap rate is a
martingale under this measure which is precisely what validates Black's formula.
Section (d) extends the numeraire discussion to Bermudan swaptions, where the
presence of multiple exercise dates requires a switch to the rolling money-market
account as numeraire, and introduces the Longstaff-Schwartz least-squares Monte
Carlo algorithm as the appropriate valuation method.


## Calibration Note

OLS calibration on the 2000 to 2024 FEDFUNDS series yields a near-zero mean
reversion estimate due to the non-stationarity of the data across two distinct
trending regimes. The pre-2008 stationary subperiod is used for estimating a
and b, with sigma retained from the full sample for a more robust volatility
estimate. A practitioner floor of a = 0.15 is applied consistent with
published Vasicek estimates for USD short rates as documented in Brigo and
Mercurio (2006). This limitation is acknowledged and MLE with a Kalman filter
is noted as a more robust alternative for regime-switching rate environments.


## Known Model Limitations

The Vasicek model is an affine Gaussian model which means the short rate can
take negative values with positive probability. This was observed in a subset
of Monte Carlo paths in Part I(c) and Part I(e). In practice the Cox-Ingersoll-Ross
model or the Hull-White extended Vasicek model are preferred when negative rates
are undesirable. The negative rate paths are retained in this implementation as
they are a known and documented property of the model rather than a coding error.


## Data Sources

Federal Reserve Economic Data (FRED), Federal Reserve Bank of St. Louis.
Series used: FEDFUNDS (monthly federal funds effective rate) and SOFR
(secured overnight financing rate). Data accessed programmatically via
the FRED public CSV endpoint, requiring no API key.


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
