# kf-data-assimilation
Data assimilation implementation using Kalman Filter for the CFD simulation of a turbulent wind flow in a finite cylinder. A thorough review of the Kalman Filter theory (along with ARIMA and SSM formulations) can be found in our [article](latex/Kalman_Filter___ML_paper.pdf), submitted to be graded as part of _Machine Learning_ (ML) 2022 course of the Data Science master course by University of Barcelona (UB).
The details of the [implementation](commented.Rmd) can be found in the [commented](commented.pdf) script summary, but it is also highlighted below:

1. Register the observed velocity flow in the point A and store it in a dataframe called $\texttt{velA}$. 
2. Simulate the velocity flow in the point A with $\texttt{COMSOL}$ and store it in a dataframe called $\texttt{mmA}$.
3. Find the best SSM through an ARMA model. Concretely using $\texttt{auto.arima}$ and in our case the best distribution was an $\texttt{ARIMA}(0, 0, 2)$, i.e. $\texttt{MA}(2)$, with parameters $(0.8504, 0.2416)$ for the 'normalized' velocity flow $\texttt{velA\_mit}$ dataframe (being $\texttt{mitjana}$ its only column)
```r
        velA_mit <- mmA$mitjana[59:178] - 1.0566
```
4. Build the SSM model and storing its matrices in an array $\texttt{m1.dlm}$ using $\texttt{dlmModARMA}( ma= c(0.8504, 0.2416))$ with the best parameters found before.
5. Apply the KF and we save it in $\texttt{A3mean}$. We use $\texttt{dlmFilter(y, mod)}$ being $\texttt{y}$ the 'normalized' data at point $A$, i.e. $v_A - \overline{v_A}$; and being $\texttt{mod}= \texttt{m1.dlm}$ the SSM parameters. At practice, it can be an object of class $\texttt{dlm}$ or a list with components m0, C0, FF, V, GG, W.
    - The ``dlm`` [notation](https://www.jstatsoft.org/article/view/v036i12) for the SSM parameters diverges with respect to the used in the article. The disambiguation can be found here: $\texttt{FF} \longleftrightarrow M_t$, $\texttt{GG} \longleftrightarrow F_t$, $\texttt{V} \longleftrightarrow R_t$, $\texttt{W} \longleftrightarrow Q_t$, and $\texttt{m\_0} \longleftrightarrow X_0$ \& $\texttt{C\_0} \longleftrightarrow \Sigma_0$.}