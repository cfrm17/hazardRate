# Hazard Rate Curve Construction

The hazard rate curve defines the default probability of an obligor, serving as one of the fundamental components of all credit derivatives models.  Within the current GSP credit derivatives modelling framework, the hazard rate curve is calibrated using the market information of the credit default swap (CDS).  

By definition the hazard rate has to be positive to ensure a larger than zero default probability within any period of time. It is a function of credit spread curve, recovery rate, and interest rate.  The credit spread curve is defined as a set of the market quoted CDS spreads with different terms to maturity. We assume one constant recovery rate for each obligor. Normally we assume 0.35 or 0.4 for most of the obligor and 0.15 for subordinate names. 

Given all the input parameters, the hazard rate curve is calculated via bootstrapping. There exists a possibility of failing to building a valid one, either because 1) the input implies a negative hazard rate or 2) a value outside of root finding range. In the GSP model, a root finding scheme is used in which we set the searching range to be 0 and 20.  

Note that this range is very large and a hazard rate 20 implies that the average default time would be within a month. From the viewpoint of modelling, this assumes almost an instant default of the underlying obligor and there is no need to build a hazard rate curve.  However, technically this restriction puts an upper limit on the valid credit spread that the model can handle. As analyzed in the next section, with the conventional recovery rate assumption we set this limit be 100, 000bps. 

The recovery rate can not be exactly one for any non-zero credit spread level. Because Eq.(6) can not be solved explicitly to check if the hazard rate is larger 20, we use an approximation to estimate the maximum recovery rate. If we assume a flat credit spread curve and ignore the discount factor, the hazard rate curve reads:

(1)		 

For a given credit spread level , the maximum recovery rate would be

(2)		 

Note that the above limitations are technical and if, for some reason, a hazard rate curve larger than 20 is needed, we could increase the upper root searching boundary at the expense of the computation efficiency. 

The calibration of the hazard rate curve may still fail even if the input credit spreads and recovery rate are within the range. In this case, the input credit spreads are internally inconsistent. There exists at least one quote, which is dramatically smaller than its prior quote, implying a negative default probability. 

Practically it is almost impossible to observe this phenomenon, given the fact that current CDS market is quite liquid. For example, if a CDS with one year term has market quote 1000 bps while the same CDS with two year term is quoted only 10bps, the implied value of protection for the two year term CDS would be smaller than that of the one year term. It is clearly an arbitrage chance because the two year term CDS has to cover the protection over the first year period. If this happens, the first thing to check is that if it is an operational error to have the incorrect market information. 

If the market quotes for an obligor are indeed inconsistent and we do need a hazard rate curve, the following procedure is proposed:
 
1)	Find the CDS quote which has the maximum decrease in the spread level compared to its prior quotes,
2)	Replace this credit spread quote with its prior one and recalculate, 
3)	If we still encounter an error message, repeat the step one. until a valid hazard rate curve is successfully built.

Note that the above procedure ensures a positive default probability for any period of time and bears the assumption that the CDS spreads with longer term to maturity should include the information implied by those with shorter terms. 

Once a valid hazard rate curve is built, it can be readily used for the valuation of credit default swap trade. Note that the CDS valuation model is for vanilla products and no counterparty risk is considered. In order to consider the counterparty risk, the correlation between the counterparty and the reference name should be modelled. 


Appendix 


Model Overview

In the present phase of re-review we investigate the calibration of the hazard rate curve and the Mark-to-Market (MTM) of the single name credit default swap (CDS) in the GCP pricing model.  Within the whole GCP credit derivative pricing model, the hazard rate curve serves as a fundamental component for the pricing of all credit derivatives. The single name CDS is the product used for the calibration of the hazard rate curve.

The foremost task of credit derivative pricing is to model the default probability of the underlying reference entity.  The current industry standard of modelling the default probability of an entity is based on the reduced form approach. Let the default time of an entity be , a deterministic risk-neutral hazard rate,  , is defined such that the default probability between t and t+dt  . With this definition the default probability functions built upon the hazard rate are
(1)	 = survival probability.
(2)	  = default probability density function seen at time zero
(3)	  = cumulative default probability function.

A CDS is a product that protects buyers against possible default of a reference entity. The buyer of a CDS pays the seller a premium periodically in exchange for a contingent payment in an event that the reference entity defaults before the maturity of the contract. The contingent payment is the difference between the notional of the CDS and the recovery value from the reference entity. If the default event happens, the buyer of the swap receives the contingent payment from the seller and stops any future premium payments.

There are two valuation legs for a CDS: fee and protection. If we assume a risk free counterparty, the expected present value (PV) of the fee leg is calculated by:
 (4)	 ,
where s, quoted as the credit spread, is the fixed fee paid by the CDS buyer. The cash flows associated with the fixed fee occur at intervals  (maturity). Payment for the fee during the period   is made at   if default has not occurred;   is the day count fraction; D(t) is the discount factor; the notional is set to be 1.
The value of the protection leg can be expressed as 
(5)	 ,
where R is the recovery rate and   is maturity of the contract.

•	Calibration of the Hazard Rate Curve  

The hazard rate curve   can be calibrated by the default probability of the entity, which can be retrieved from the market information of CDS. This has become a standard business practise . 

Currently market provides the breakeven credit spreads of the CDS with different terms and the recovery rate (R) of their corresponding reference entities. Let  be the quoted credit spread of the CDS with term of maturity  , we could have

(6)	 

We assume a piecewise constant hazard rate curve at nodes . By employing Eqs (1), (2), and (6), one proceeds iteratively from the shortest-term credit spread ( with term ) to longer terms. At each step, the hazard rates implied by the shorter terms of maturity are used in the computation of hazard rates farther out on the curve. 

•	Mark-to-Market of a Single Name CDS

Once the hazard rate curve h(t) is determined, the MTM for a CDS deal, with term T (  ) and traded spread  , is calculated by
(7)	 

•	Benchmarking/Comparison to Other Models

Although the above approach of the hazard rate calibration has been well established, there are other options with different underlying assumptions, as discussed below:

1.	Implied Default Probability 

The default probability of an entity can be calculated using other methods such as Merton’s option theoretical approach, historical default information, and market price of defaultable bond. However, a well-established CDS market with high liquidity enables us to get the implied default probability of an entity from the CDS market directly2 , as described in this report.  It is more appropriate to use this default probability than those implied by the above mentioned three methods.
 
2.	Recovery Rate and Correlation with Default Probability

An independent constant recovery rate (usually 0.4) is usually assumed for the reference entity. In general measuring recovery on defaulted debt is difficult.  

The CDS trade is a synthetic contract in which there is no physical underlying asset. Moreover, the value of a CDS trade is a combined result of the implied default probability and recovery. This enables us to avoid the uncertainty of the recovery with a good approximation by assuming a reasonable, independent, and constant recovery rate to get the implied default probability and, then, using this implied default probability and same recovery to price the CDS trade. In fact the value of CDS trade calculated in this way is not sensitive to the recovery rate assumption. 

3.	Dependence Between Interest Rate and Hazard Rate Curve

A deterministic credit curve is assumed in the current modelling approach outlined in this report. The latest research endeavours tend to introduce a stochastic dynamic process of the hazard rate as well as a correlation between the interest rates and the default probability of the reference entity. These new models are especially useful in the pricing of the options on the CDS contract, for example, a CDS with a cancellable structure linked to interest rates. Implied by the market directly, a deterministic hazard rate curve independent of interest rates still remains the pertinent model to price the standard CDS.

Reference:

https://finpricing.com/FinPricing-ProductBrochure.pdf
