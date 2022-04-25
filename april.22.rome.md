rein / adpkd
================
2022-04-27

## variable list

-   **cardiovascula disease (cerbral-associated event excluded)** =
    -   ICn
    -   ICOROn
    -   IDMn
    -   RYTHMn
    -   ANEVn
-   **tobacco** = tabac2
-   **dialysis method** = METHOn *or* MODALn *or* techn
-   **autosomal dominant polycystic kidney disease** = condition coded
    in nephgp (“APKD”)
-   **sex** = sex
-   **age** = age
-   **diabetes** = diabetes
-   **bmi** = bmi

## models

-   **whole population**
    -   diabetic patients included in the model \[1.1\]
    -   only non-diabetic patients included \[1.2\]
-   **haemorrhagic event only**
    -   diabetic patients included in the model \[2.1\]
    -   only non-diabetic patients \[2.2\]
-   **ischemic event only**
    -   diabetic patients included in the model \[3.1\]
    -   only non-diabetic patients \[3.2\]

## \[1.1\] whole population: diabetic patients included

> dans ces modèles, la population entière est incluse dans l’analyse. Le
> meilleur modèle a été choisi par le biais du
> `akaike information criterion` (`AIC`) et les mêmes variables ont été
> gardées dans les modèles suviants.  
> les résultats qui suivent concernent 3 différents modèles : 1. le
> modèle complet, sans que les variables quantiatives (l’âge en
> particulier) sois analysé par le biais des splines ; 2. un modèle où
> la variable *age* sous la forme de *smooth splines* à l’aide de la
> fonction `pspline` à 5 dégrées de liberté (`df=5` représente un choix
> “classique” pour le calcul de splines, où les *knots* sont établi par
> le logiciel même, de façon automatique) et dégrées (cubic, etc.)
> choisis dans chaque modèle en fonction de son AIC et 3. un modèle où
> les variables *age* et *bmi* ont été analysées à l’aide de *smooth
> splines* à 5 et 8 DoF). Je devrais revenir ensuite sur la sélection
> des DF par un approche *FDA* (Functional Data Analysis), mais ce n’est
> pas encore fait !  
> La **représentation graphique** des splines n’est pas montrée dans ce
> document.

### univariate analysis

                 beta     HR (95% CI for HR)  wald.test  p.value
    cardiovasc   0.33     1.4 (1.3-1.5)       46         1.4e-11 *
    tabac2       -0.062   0.94 (0.85-1)       1.5        0.22
    dial         0.022    1 (0.88-1.2)        0.09       0.77
    apkd01       -0.59    0.55 (0.43-0.71)    21         5.2e-06 *
    sex          0.14     1.1 (1-1.3)         8.4        0.0038 *
    age          0.024    1 (1-1)             180        6.1e-42 *
    tabac2.1     -0.062   0.94 (0.85-1)       1.5        0.22
    diabetes     0.31     1.4 (1.2-1.5)       45         1.7e-11 *
    bmi          -0.013   0.99 (0.98-1)       8.4        0.0037 *

### multivariate analysis

#### model selection by `akaike information criterion` [^1]

    predictors      AIC         model
    6               30164.57    ~  cardiovasc + tabac2 + dial + apkd01 + sex + age
    6               30131.78    ~  cardiovasc + tabac2 + dial + apkd01 + sex + diabetes
    6               30051.23    ~  cardiovasc + tabac2 + dial + apkd01 + age + diabetes
    6               30047.56    ~  cardiovasc + tabac2 + dial + sex + age + diabetes
    7               30046.06    ~  cardiovasc + tabac2 + dial + apkd01 + sex + age + diabetes
    6               25750.53    ~  cardiovasc + tabac2 + dial + apkd01 + sex + bmi
    7               25684.48    ~  cardiovasc + tabac2 + dial + apkd01 + sex + age + bmi
    6               25623.11    ~  cardiovasc + tabac2 + dial + apkd01 + diabetes + bmi
    8               25553.13    ~  cardiovasc + tabac2 + dial + apkd01 + sex + age + diabetes + bmi

> le modèle retenu est le modèle complet.

#### full model formula

     ~ cardiovasc + tabac2 + dial +  apkd01 + diabetes + bmi +  sex + age

##### proportional hazards testings

                chisq  df  p
    cardiovasc  1.91   1   0.16681
    tabac2      1.01   1   0.31564
    dial        1.01   1   0.31542
    apkd01      3.28   1   0.07012
    diabetes    2.01   1   0.15615
    bmi         14.53  1   0.00014 *
    sex         1.10   1   0.29366
    age         3.37   1   0.06648 .
    GLOBAL      25.78  8   0.00115 *

the hypothesis [^2] is not verified for `bmi`. `age` follows the
proportional hazard hypothesis, thought modelling its non-linear effect
could further insights. two solutions are proposed for the full model on
the whole population.

> dans toutes ces analyses, la vérification de l’hypothèse des risques
> proportionnels a été vérifiée par la fonction `cox.zph` du package
> `survival`. La vérification graphique (par les Schoenfeld’s residuals)
> n’est pas montrée.

#### full model with splines formula

`~ cardiovasc + tabac2 + dial + apkd01 + diabetes + bmi + sex + pspline(age, df=5)`

##### output

                               coef      se(coef)  se2       Chisq  DF    p      
    cardiovasc1                0.205907  0.060561  0.060558  11.56  1.00  6.7e-04
    tabac21                    0.006978  0.062232  0.062227  0.01   1.00  9.1e-01
    dial2                      0.068531  0.092085  0.092082  0.55   1.00  4.6e-01
    apkd01                    -0.288639  0.163007  0.162920  3.14   1.00  7.7e-02
    diabetes1                  0.329793  0.060743  0.060728  29.48  1.00  5.7e-08
    bmi                       -0.033029  0.005438  0.005437  36.89  1.00  1.3e-09
    sex2                       0.198528  0.062646  0.062643  10.04  1.00  1.5e-03
    pspline(age, df = 5), lin  0.017569  0.002497  0.002497  49.51  1.00  2.0e-12
    pspline(age, df = 5), non                                15.94  4.04  3.2e-03

##### hazard ratios

                   exp(coef)  exp(-coef) lower.95  upper.95
    cardiovasc1    1.2286     0.8139     1.0911    1.3835
    tabac21        1.0070     0.9930     0.8914    1.1376
    dial2          1.0709     0.9338     0.8941    1.2828
    apkd01         0.7493     1.3346     0.5444    1.0313
    diabetes1      1.3907     0.7191     1.2346    1.5665
    bmi            0.9675     1.0336     0.9573    0.9779
    sex2           1.2196     0.8199     1.0787    1.3789
    ps(age)3       1.4008     0.7139     0.5524    3.5521
    ps(age)4       1.9496     0.5129     0.4110    9.2474
    ps(age)5       2.5542     0.3915     0.3864    16.8855
    ps(age)6       3.2330     0.3093     0.4426    23.6151
    ps(age)7       4.1911     0.2386     0.5841    30.0712
    ps(age)8       5.0324     0.1987     0.7260    34.8836
    ps(age)9       6.7742     0.1476     0.9901    46.3490
    ps(age)10      8.1589     0.1226     1.1937    55.7672
    ps(age)11      8.6268     0.1159     1.2618    58.9822
    ps(age)12      9.4867     0.1054     1.3887    64.8074
    ps(age)13      9.1099     0.1098     1.3317    62.3180
    ps(age)14      8.1075     0.1233     1.1578    56.7744
    ps(age)15      7.0958     0.1409     0.8740    57.6116
    ps(age)16      6.1921     0.1615     0.5159    74.3254

> le dégrée des splines est choisi en utilisant le AIC. (Dans la plupart
> des analyses `degree = 3`.)  
> les deux lignes de l’output qui concernent les splines affichent un
> simple *normality check* par rapport à la variable continue qui est
> traitée sous forme de spline. La première ligne (“lin”) est la partie
> linéaire de l’association dans le modèle.  
> la deuxième ligne (“non”) est le résultat d’un test de type Wald qui,
> en utilisant la matrice variance-covariance des coefficients établit
> si un ou plusieurs coefficient de non-linéarité diffèrent
> significativement de 0. Une valeur significative de la p-value indique
> que la relation entre la variable et le risque proportionnel est
> non-significative.

##### proportional hazard hypothesis

the proportional hazards hypothesis is verified for all variables,
except for the `bmi`. therefore, the global hypothesis is not verified

                          chisq   df     p
    cardiovasc            1.94    1.00   0.16379
    tabac2                1.03    1.00   0.30982
    dial                  1.01    1.00   0.31516
    apkd01                3.28    1.00   0.06981
    diabetes              1.98    1.00   0.15906
    bmi                   14.54   1.00   0.00014 *
    sex                   1.12    1.00   0.29077
    pspline(age, df = 5)  4.24    5.04   0.52112
    GLOBAL                26.37   12.04  0.00969 *

> le résultat de l’analyse des risques proportionnels montre que
> l’hypothèse n’est pas respectée par la variable `bmi`. le non-respect
> de l’hypothèse peut être approfondi par deux altérnatives : 1. la
> stratification sur l’imc *ou* 2. la création d’une step function qui
> permette de prendre en compte la variable quantitative à plusieurs
> temps du suivi - or, cette demarche aurait demandé la création de t_n
> qui n’existent pas dans la base, selon *Therneau 2022*.

###### two solutions for the proportional hazards hypothesis

###### 1. `bmi` as qualitative variable for stratification

| value     | WHO class              | n      |
|-----------|------------------------|--------|
| \< 18.5   | underweight            | 1,618  |
| 18.5-24.9 | normal weight          | 15,100 |
| 25.0-29.9 | pre-obesity            | 12,249 |
| \> 30.0   | obesity class I-II-III | 9,404  |

formula

    ~ cardiovasc + tabac2 + dial + apkd01 + diabetes + sex + bmi + strata(bmic) 
                              + pspline(age, df = 5)

output

                               coef      se(coef)  se2       Chisq  DF    p      
    cardiovasc1                0.205873  0.060564  0.060561  11.55  1.00  6.8e-04
    tabac21                    0.006375  0.062246  0.062242  0.01   1.00  9.2e-01
    dial2                      0.067789  0.092202  0.092198  0.54   1.00  4.6e-01
    apkd01                    -0.287448  0.163084  0.162999  3.11   1.00  7.8e-02
    diabetes1                  0.328012  0.060850  0.060835  29.06  1.00  7.0e-08
    sex2                       0.197895  0.063167  0.063164  9.82   1.00  1.7e-03
    bmi                       -0.032139  0.012296  0.012296  6.83   1.00  9.0e-03
    pspline(age, df = 5), lin  0.017590  0.002507  0.002507  49.24  1.00  2.3e-12
    pspline(age, df = 5), non                                15.78  4.04  3.5e-03

hazard ratios

                   exp(coef)  exp(-coef) lower.95    upper.95
    cardiovasc1    1.2286     0.8139      1.0911     1.383 *
    tabac21        1.0064     0.9936      0.8908     1.137
    dial2          1.0701     0.9345      0.8932     1.282
    apkd01         0.7502     1.3330      0.5449     1.033
    diabetes1      1.3882     0.7204      1.2321     1.564 *
    sex2           1.2188     0.8205      1.0769     1.379 *
    bmi            0.9684     1.0327      0.9453     0.992 *
    ps(age)3       1.4035     0.7125      0.5538     3.557
    ps(age)4       1.9571     0.5110      0.4131     9.272
    ps(age)5       2.5670     0.3896      0.3890     16.941
    ps(age)6       3.2483     0.3079      0.4456     23.680
    ps(age)7       4.2056     0.2378      0.5873     30.116
    ps(age)8       5.0417     0.1983      0.7287     34.881
    ps(age)9       6.7801     0.1475      0.9928     46.302
    ps(age)10      8.1738     0.1223      1.1981     55.765
    ps(age)11      8.6499     0.1156      1.2674     59.036
    ps(age)12      9.5061     0.1052      1.3938     64.833
    ps(age)13      9.1372     0.1094      1.3378     62.406
    ps(age)14      8.1465     0.1228      1.1651     56.960
    ps(age)15      7.1455     0.1399      0.8813     57.938
    ps(age)16      6.2491     0.1600      0.5212     74.932

proportional hazards hypothesis for the model with `stratified bmi`

                          chisq   df    p
    cardiovasc            2.391   1.00  0.122
    tabac2                1.194   1.00  0.275
    dial                  1.033   1.00  0.309
    apkd01                3.656   1.00  0.056
    diabetes              0.173   1.00  0.678
    sex                   1.019   1.00  0.313
    bmi                   3.755   1.00  0.053 .
    pspline(age, df = 5)  3.877   5.04  0.573
    GLOBAL                15.713  12.04 0.207

> le test montre une hypothèse qui est verifiée globalement pour le
> modèle, bien que `bmi` reste proche de la significativité.

###### 2. smooth splines for the `BMI`

formula

    ~ cardiovasc + tabac2 + dial + apkd01 + diabetes + sex + pspline(age, df = 5)
                           + pspline(bmi, df= 5)

output

                               coef     se(coef)  se2       Chisq  DF    p      
    cardiovasc1                0.20624  0.060580  0.060576  11.59  1.00  6.6e-04
    tabac21                    0.00840  0.062259  0.062254  0.02   1.00  8.9e-01
    dial2                      0.06680  0.092300  0.092295  0.52   1.00  4.7e-01
    apkd01                    -0.29071  0.163141  0.163051  3.18   1.00  7.5e-02
    diabetes1                  0.32810  0.060894  0.060877  29.03  1.00  7.1e-08
    sex2                       0.20340  0.063511  0.063504  10.26  1.00  1.4e-03
    pspline(age, df = 5), lin  0.01744  0.002514  0.002514  48.08  1.00  4.1e-12
    pspline(age, df = 5), non                               15.68  4.04  3.6e-03
    pspline(bmi, df = 8), lin -0.03081  0.005547  0.005546  30.85  1.00  2.8e-08
    pspline(bmi, df = 8), non                               8.90   7.00  2.6e-01

> l’analyse de la variable `bmi` sous la forme de splines montre une
> non-linéarité de sa rélation avec le HR.

hazard ratios


                exp(coef)     exp(-coef) lower.95     upper.95
    cardiovasc1 1.2290446     0.8136     1.091e+00    1.384 *
    tabac21     1.0084353     0.9916     8.926e-01    1.139
    dial2       1.0690825     0.9354     8.922e-01    1.281
    apkd01      0.7477336     1.3374     5.431e-01    1.029
    diabetes1   1.3883271     0.7203     1.232e+00    1.564 *
    sex2        1.2255589     0.8160     1.082e+00    1.388 *
    ps(age)3    1.3982319     0.7152     5.510e-01    3.548
    ps(age)4    1.9425535     0.5148     4.089e-01    9.228
    ps(age)5    2.5414277     0.3935     3.837e-01    16.834
    ps(age)6    3.2128698     0.3112     4.389e-01    23.518
    ps(age)7    4.1584720     0.2405     5.784e-01    29.900
    ps(age)8    4.9860530     0.2006     7.178e-01    34.636
    ps(age)9    6.6975439     0.1493     9.767e-01    45.925
    ps(age)10   8.0539125     0.1242     1.176e+00    55.175
    [...]
    ps(age)11   8.5017241     0.1176     1.240e+00    58.267
    ps(age)12   9.3561687     0.1069     1.366e+00    64.084
    ps(age)13   8.9863366     0.1113     1.310e+00    61.638
    ps(age)14   7.9957104     0.1251     1.139e+00    56.137
    ps(age)15   6.9943992     0.1430     8.594e-01    56.922
    ps(age)16   6.1002392     0.1639     5.072e-01    73.371
    ps(bmi)3    1.3965876     0.7160     1.898e-01    10.277
    ps(bmi)4    1.9411359     0.5152     7.398e-02    50.929
    ps(bmi)5    2.3432838     0.4268     5.377e-02    102.120
    ps(bmi)6    1.9768971     0.5058     4.619e-02    84.612
    [...]

proportional hazard hypothesis for the model with smoothed splines for
both `bmi` and `age`

                          chisq    df     p
    cardiovasc            1.95     1.00   0.163
    tabac2                1.03     1.00   0.311
    dial                  1.02     1.00   0.313
    apkd01                3.28     1.00   0.070
    diabetes              2.00     1.00   0.157
    sex                   1.13     1.00   0.289
    pspline(age, df = 5)  4.25     5.04   0.520
    pspline(bmi, df = 8)  15.42    8.00   0.051
    GLOBAL                27.36    19.04  0.097

###### 2.1 smooth splines for the `BMI` after imputation

the proportion of the missing data is the following [^3]

| cardiovascular | tabac | dialys | adpkd | diabetes | sex  | bmi   | age  |
|----------------|-------|--------|-------|----------|------|-------|------|
| 10.74          | 18.68 | 3.96   | 0.00  | 4.28     | 0.00 | 14.76 | 0.00 |

missing data pattern for smaller dataset.  
*on the column, the sum for each combination. 36356 lines have no
missing entry. [^4]*

|       | sex | age | dialysis | diabetes | bmi  |       |
|-------|-----|-----|----------|----------|------|-------|
| 36356 | 1   | 1   | 1        | 1        | 1    | 0     |
| 6420  | 1   | 1   | 1        | 1        | 0    | 1     |
| 246   | 1   | 1   | 1        | 0        | 1    | 1     |
| 219   | 1   | 1   | 1        | 0        | 0    | 2     |
| 323   | 1   | 1   | 0        | 1        | 1    | 1     |
| 1457  | 1   | 1   | 0        | 0        | 1    | 2     |
| 5     | 1   | 1   | 0        | 0        | 0    | 3     |
|       | 0   | 0   | 1785     | 1927     | 6644 | 10356 |

**data is MCAR: `MCAR test p-value=0.263`** [^5]

> la méthode de pooling ne semble pas avoir été implémentée en mice pour
> le coxph pour les imputations ‘multiparameter’ ( *Reiter 2007* ).
> D’autres tests sont nécessaires. Néanmoins l’analyse est affecté
> négativement par l’absence du pooling et les résultats ne sont pas
> cohérents entre les différentes bases imputées. Une solution
> alternative est fournie dans le package `Hmisc` de Frank Harrel. Cette
> solution permet de pooler les différents datasets et d’obtenir une
> estimation de la survie par la fonction `fit.multiple.impute`, même
> des variables dont un smooth spline est calculé. De plus, par la
> fonction `transcan`, l’imputation multiple peut être limitée à
> quelques variables. Néanmois il présente différents *downsides* : 1.
> pas de méthode d’estimation graphique de la survie ; 2. pas de méthode
> d’évaluation de l’hypothèse de risques proportionnels. On imagine le
> risque qu’ils le soient ; 3. moins précis que `mice` : il ne calcule
> qu’une approximation (aussi moins fiable que la fonction `aregImpute`
> du même package, malheureusement pas implémentée pour la survie).
> `mice` permettrait d’obtenir une meilleure estimation de la
> distribution bayesienne a posteriori

formula and results

    d <- data.frame(time, status, cardiovasc, tabac2, dial, apkd01, diabetes, sex, bmi, age, bmic)
    n <- naclus(d)

    f <- transcan(~status + bmi + age + sex, n.impute = 5, shrink = FALSE, data = d)

    Convergence criterion:0.767 0.008 0 
    Convergence in 4 iterations
    R-squared achieved in predicting each variable:

    status    bmi    age    sex 
     0.004  0.045  0.044  0.068 

    Adjusted R-squared:

    status    bmi    age    sex 
     0.004  0.045  0.044  0.068 


    fit.mult.impute(formula = Surv(time, status) ~ pspline(bmi, df = 5) + 
        tabac2 + cardiovasc + dial + apkd01 + diabetes + sex + pspline(age, df = 5), 
        fitter = coxph, xtrans = f, data = d)

      n= 33745, number of events= 1481 
       (11281 observations deleted due to missingness)

                               coef       se(coef)  se2       Chisq  DF    p      
    pspline(bmi, df = 5), lin  -0.048478  0.005306  0.005221  83.47  1.00  6.5e-20
    pspline(bmi, df = 5), non                                 30.31  4.00  4.2e-06
    tabac21                    -0.004243  0.057763  0.057662  0.01   1.00  9.4e-01
    cardiovasc1                0.165430   0.056035  0.055992  8.72   1.00  3.2e-03
    dial2                      0.026543   0.085891  0.085632  0.10   1.00  7.6e-01
    apkd01                     -0.277534  0.156803  0.153714  3.13   1.00  7.7e-02
    diabetes1                  0.333985   0.055774  0.055638  35.86  1.00  2.1e-09
    sex2                       0.198197   0.058566  0.058394  11.45  1.00  7.1e-04
    pspline(age, df = 5), lin  0.016829   0.002291  0.002264  53.98  1.00  2.0e-13
    pspline(age, df = 5), non                                 17.80  4.06  1.4e-03

                 exp(coef)    exp(-coef)   lower.95     upper.95
    ps(bmi)3     1.682761     0.5943       3.352e-01    8.448
    ps(bmi)4     2.810612     0.3558       2.254e-01    35.048
    ps(bmi)5     5.147890     0.1943       3.529e-01    75.097
    ps(bmi)6     4.647093     0.2152       3.394e-01    63.635
    ps(bmi)7     2.496612     0.4005       1.787e-01    34.879
    ps(bmi)8     2.272460     0.4401       1.625e-01    31.779
    ps(bmi)9     2.527991     0.3956       1.800e-01    35.496
    ps(bmi)10    2.053044     0.4871       1.437e-01    29.332
    ps(bmi)11    0.778303     1.2848       5.094e-02    11.892
    ps(bmi)12    0.223893     4.4664       1.081e-02    4.636
    ps(bmi)13    0.062394     16.0273      1.364e-03    2.853
    ps(bmi)14    0.017346     57.6489      1.017e-04    2.957
    ps(bmi)15    0.004822     207.3869     4.973e-06    4.675
    ps(bmi)16    0.001340     746.0596     1.750e-07    10.267
    tabac21      0.995766     1.0043       8.892e-01    1.115
    cardiovasc1  1.179900     0.8475       1.057e+00    1.317 *
    dial2        1.026898     0.9738       8.678e-01    1.215
    apkd01       0.757650     1.3199       5.572e-01    1.030
    diabetes1    1.396523     0.7161       1.252e+00    1.558 *
    sex2         1.219202     0.8202       1.087e+00    1.368 *
    ps(age)3     1.265980     0.7899       5.514e-01    2.907
    ps(age)4     1.590655     0.6287       3.989e-01    6.343
    ps(age)5     1.895347     0.5276       3.576e-01    10.045
    ps(age)6     2.258692     0.4427       3.936e-01    12.961
    ps(age)7     2.900960     0.3447       5.152e-01    16.336
    ps(age)8     3.596729     0.2780       6.586e-01    19.643
    ps(age)9     4.852090     0.2061       8.994e-01    26.176
    ps(age)10    5.813993     0.1720       1.079e+00    31.342
    ps(age)11    6.079379     0.1645       1.126e+00    32.820
    ps(age)12    6.445337     0.1552       1.194e+00    34.780
    ps(age)13    6.386472     0.1566       1.182e+00    34.499
    ps(age)14    6.011467     0.1663       1.091e+00    33.126
    ps(age)15    5.783144     0.1729       9.192e-01    36.386
    ps(age)16    5.573578     0.1794       6.206e-01    50.053

    Iterations: 6 outer, 19 Newton-Raphson
         Theta= 0.7382779 
         Theta= 0.9329086 
    Degrees of freedom for terms= 5.0 1.0 1.0 1.0 1.0 1.0 1.0 5.1 
    Concordance= 0.63  (se = 0.007 )
    Likelihood ratio test= 323.4  on 16.06 df,   p=<2e-16

## \[1.2\] whole population, only non-diabetic patients included

### multivariate analysis

#### full model with splines formula

    ~ cardiovasc + tabac2 + dial + apkd01 + bmi + sex + pspline(age, df=5)

##### output

                               coef      se(coef)  se2       Chisq  DF    p      
    cardiovasc1                0.250614  0.090208  0.090200  7.72   1.00  5.5e-03
    tabac21                   -0.004219  0.091487  0.091474  0.00   1.00  9.6e-01
    dial2                     -0.049367  0.137699  0.137689  0.13   1.00  7.2e-01
    apkd01                    -0.155526  0.176916  0.176786  0.77   1.00  3.8e-01
    bmi                       -0.046351  0.009316  0.009315  24.76  1.00  6.5e-07
    sex2                       0.089442  0.092826  0.092819  0.93   1.00  3.4e-01
    pspline(age, df = 5), lin  0.025523  0.003447  0.003447  54.81  1.00  1.3e-13
    pspline(age, df = 5), non                                12.28  4.09  1.6e-02

##### hazard ratios

                   exp(coef)  exp(-coef)  lower.95  upper.95
    cardiovasc1    1.2848     0.77832     1.0766    1.5333 *
    tabac21        0.9958     1.00423     0.8323    1.1914 
    dial2          0.9518     1.05061     0.7267    1.2467
    apkd01         0.8560     1.16827     0.6052    1.2107 
    bmi            0.9547     1.04744     0.9374    0.9723 *
    sex2           1.0936     0.91444     0.9117    1.3118 
    ps(age)3       1.6219     0.61656     0.4537    5.7982
    ps(age)4       2.6076     0.38349     0.3166    21.4797
    ps(age)5       3.7458     0.26697     0.3000    46.7616
    ps(age)6       4.6515     0.21498     0.3360    64.3898
    ps(age)7       5.7962     0.17253     0.4347    77.2904
    ps(age)8       7.0863     0.14112     0.5554    90.4128
    ps(age)9       9.8129     0.10191     0.7805    123.3653
    ps(age)10      12.6587    0.07900     1.0089    158.8261
    ps(age)11      14.6329    0.06834     1.1667    183.5269
    ps(age)12      19.2027    0.05208     1.5334    240.4695
    ps(age)13      16.7320    0.05977     1.3335    209.9407
    ps(age)14      13.6057    0.07350     1.0552    175.4342
    ps(age)15      11.3156    0.08837     0.7248    176.6613
    ps(age)16      9.3766     0.10665     0.3517    249.9976

##### proportional hazard hypothesis of model 1.2

                            chisq     df     p
    cardiovasc              0.35181   1.00   0.553
    tabac2                  1.43453   1.00   0.231
    dial                    0.10838   1.00   0.742
    apkd01                  5.10837   1.00   0.024 *
    bmi                     3.08680   1.00   0.079
    sex                     0.00453   1.00   0.946
    pspline(age, df = 5)    3.02482   5.09   0.707
    GLOBAL                  11.69810  11.09  0.394

> l’hypothèse de risques proportionnels n’est pas respectée pour la
> variable ADPKD. Néanmoins, le résultat global ne semble pas être
> affecté. L’analyse de cette variable par stratification et *step
> function* ne change pas le résultat.

------------------------------------------------------------------------

## \[2.1\] hemorragic event

### univariate analysis

                  beta      HR (95% CI for HR)   wald.test   p.value
    cardiovasc    0.23      1.3 (1.1-1.5)        7.3         0.0068
    tabac2        -0.0016   1 (0.84-1.2)         0           0.99
    dial          -0.15     0.86 (0.65-1.1)      1.2         0.27
    apkd01        -0.21     0.81 (0.56-1.2)      1.2         0.28
    sex           -0.081    0.92 (0.78-1.1)      0.91        0.34
    age           0.015     1 (1-1)              26          2.9e-07 *
    diabetes      0.28      1.3 (1.1-1.5)        12          6e-04 *
    bmi           -0.021    0.98 (0.96-0.99)     6.9         0.0085 *

### multivariate analysis

#### full model with splines formula

    cardiovasc + tabac2 + dial + apkd01 + bmi + sex + pspline(age, df = 5)

#### output

                               coef       se(coef)  se2       Chisq  DF    p      
    cardiovasc1                0.207373   0.108907  0.108901  3.63   1.00  5.7e-02
    tabac21                    -0.050328  0.109665  0.109655  0.21   1.00  6.5e-01
    dial2                      -0.154691  0.179603  0.179597  0.74   1.00  3.9e-01
    apkd01                     0.062841   0.244238  0.244050  0.07   1.00  8.0e-01
    bmi                        -0.040002  0.009808  0.009807  16.63  1.00  4.5e-05
    diabetes1                  0.453326   0.109400  0.109366  17.17  1.00  3.4e-05
    sex2                       -0.040683  0.114055  0.114049  0.13   1.00  7.2e-01
    pspline(age, df = 5), lin  0.007818   0.004048  0.004048  3.73   1.00  5.3e-02
    pspline(age, df = 5), non                                 5.66   4.05  2.3e-01

#### hazard ratios du model 2.1

                   exp(coef)  exp(-coef)  lower.95   upper.95
    cardiovasc1    1.2304     0.8127      0.99394    1.5232
    tabac21        0.9509     1.0516      0.76700    1.1789
    dial2          0.8567     1.1673      0.60248    1.2181
    apkd01         1.0649     0.9391      0.65977    1.7186
    bmi            0.9608     1.0408      0.94249    0.9794 *
    diabetes1      1.5735     0.6355      1.26986    1.9498 *
    sex2           0.9601     1.0415      0.76780    1.2006 
    ps(age)3       1.3663     0.7319      0.31373    5.9498
    ps(age)4       1.8482     0.5411      0.16369    20.8677
    ps(age)5       2.2406     0.4463      0.12499    40.1675
    ps(age)6       2.5862     0.3867      0.12982    51.5198
    ps(age)7       2.9849     0.3350      0.15725    56.6572
    ps(age)8       3.4919     0.2864      0.19368    62.9552
    ps(age)9       4.6846     0.2135      0.26360    83.2517
    ps(age)10      4.2119     0.2374      0.23691    74.8803
    ps(age)11      3.4670     0.2884      0.19483    61.6949
    ps(age)12      4.1184     0.2428      0.23185    73.1589
    ps(age)13      4.8516     0.2061      0.27259    86.3520
    ps(age)14      4.3247     0.2312      0.23172    80.7126
    ps(age)15      3.7102     0.2695      0.15001    91.7661
    ps(age)16      3.1753     0.3149      0.06336    159.1359

###### proportional hazard hypothesis of model 2.1

                           chisq    df      p
    cardiovasc             0.0564   1.00    0.8123
    tabac2                 0.0154   1.00    0.9012
    dial                   0.0780   1.00    0.7800
    apkd01                 2.1762   1.00    0.1399
    bmi                    9.3359   1.00    0.0022 *
    diabetes               0.0157   1.00    0.9001
    sex                    0.0810   1.00    0.7760
    pspline(age, df = 5)   3.3188   5.05    0.6577
    GLOBAL                 14.7359  12.05   0.2594

## \[2.2\] hemorragic events, only non-diabetic patients included

### multivariate analysis

#### full model with splines formula

    ~ cardiovasc + tabac2 + dial + apkd01 + bmi + sex + pspline(age, df=5)

#### output

                               coef      se(coef)  se2       Chisq  DF    p     
    cardiovasc1                0.15704   0.164291  0.164270  0.91   1.00  0.3400
    tabac21                    -0.19706  0.163699  0.163671  1.45   1.00  0.2300
    dial2                      -0.22594  0.262592  0.262574  0.74   1.00  0.3900
    apkd01                     0.12334   0.264989  0.264708  0.22   1.00  0.6400
    bmi                        -0.05400  0.016848  0.016846  10.27  1.00  0.0014
    sex2                       -0.13545  0.167559  0.167543  0.65   1.00  0.4200
    pspline(age, df = 5), lin  0.01266   0.005481  0.005481  5.34   1.00  0.0210
    pspline(age, df = 5), non                                4.71   4.04  0.3200

#### hazard ratios

                   exp(coef)  exp(-coef) lower.95  upper.95
    cardiovasc1    1.1700     0.85467    0.84792   1.6145
    tabac21        0.8211     1.21782    0.59577   1.1318
    dial2          0.7978     1.25350    0.47682   1.3347
    apkd01         1.1313     0.88396    0.67299   1.9016
    bmi            0.9474     1.05548    0.91666   0.9792
    sex2           0.8733     1.14505    0.62885   1.2128
    ps(age)3       1.6211     0.61685    0.22837   11.5080
    ps(age)4       2.5962     0.38518    0.10558   63.8401
    ps(age)5       3.6146     0.27665    0.08303   157.3597
    ps(age)6       4.5138     0.22154    0.09338   218.1785
    ps(age)7       5.4738     0.18269    0.12194   245.7159
    ps(age)8       6.8918     0.14510    0.16287   291.6144
    ps(age)9       9.1108     0.10976    0.21817   380.4705
    ps(age)10      8.0366     0.12443    0.19231   335.8427
    ps(age)11      6.3177     0.15829    0.15098   264.3548
    ps(age)12      8.6234     0.11596    0.20673   359.7197
    ps(age)13      10.6705    0.09372    0.25521   446.1412
    ps(age)14      8.2851     0.12070    0.18727   366.5564
    ps(age)15      6.3941     0.15639    0.10064   406.2306
    ps(age)16      4.9229     0.20313    0.03020   802.5188

> dans cette population, la relation entre `age` et HR est linéaire.

#### output of the model without splines

                 coef      exp(coef)  se(coef)   z       Pr(>|z|)   
    age          0.013310  1.013398   0.005218   2.551   0.01075 * 
    cardiovasc1  0.146565  1.157850   0.164289   0.892   0.37233   
    tabac21     -0.165308  0.847633   0.161714   -1.022  0.30667   
    dial2       -0.230698  0.793979   0.262408   -0.879  0.37931   
    apkd01       0.185978  1.204395   0.260780   0.713   0.47575   
    bmi         -0.052192  0.949146   0.016781   -3.110  0.00187 **
    sex2        -0.127940  0.879907   0.167629   -0.763  0.44533   

                   exp(coef)  exp(-coef) lower.95  upper.95
    age            1.0134     0.9868     1.0031    1.0238 *
    cardiovasc1    1.1578     0.8637     0.8391    1.5977
    tabac21        0.8476     1.1798     0.6174    1.1637
    dial2          0.7940     1.2595     0.4747    1.3279
    apkd01         1.2044     0.8303     0.7224    2.0079
    bmi            0.9491     1.0536     0.9184    0.9809 *
    sex2           0.8799     1.1365     0.6335    1.2221

## \[3.1\] ischemic event only

### univariate analysis

                  beta      HR (95% CI for HR)  wald.test  p.value
    cardiovasc    0.41      1.5 (1.3-1.7)       56         7.6e-14 *
    tabac2        -0.057    0.94 (0.84-1.1)     1          0.32
    dial          0.073     1.1 (0.92-1.3)      0.81       0.37
    apkd01        -0.95     0.39 (0.27-0.54)    30         4.2e-08 *
    sex           0.22      1.2 (1.1-1.4)       18         2.3e-05 *
    age           0.028     1 (1-1)             190        3.2e-44 *
    diabetes      0.34      1.4 (1.3-1.6)       43         4.9e-11 *
    bmi           -0.0076   0.99 (0.98-1)       2.5        0.12

### multivariate analysis

#### full model with splines formula

    ~ cardiovasc + tabac2 + dial + apkd01 + diabetes + sex + pspline(age, df = 5)

#### output

                               coef     se(coef)  se2       Chisq  DF    p      
    cardiovasc1                0.17757  0.062751  0.062748  8.01   1.00  4.7e-03
    tabac21                    0.05485  0.065187  0.065184  0.71   1.00  4.0e-01
    dial2                      0.07205  0.094824  0.094821  0.58   1.00  4.5e-01
    apkd01                     -0.58525 0.202035  0.201960  8.39   1.00  3.8e-03
    diabetes1                  0.18158  0.060640  0.060622  8.97   1.00  2.8e-03
    sex2                       0.25992  0.064988  0.064985  16.00  1.00  6.3e-05
    pspline(age, df = 5), lin  0.02327  0.002712  0.002712  73.59  1.00  9.6e-18
    pspline(age, df = 5), non                               19.63  4.07  6.4e-04

#### hazard ratios

                    exp(coef)  exp(-coef)  lower.95  upper.95
    cardiovasc1     1.194      0.83730     1.0561    1.3506 *
    tabac21         1.056      0.94663     0.9297    1.2004
    dial2           1.075      0.93048     0.8924    1.2942
    apkd01          0.557      1.79544     0.3748    0.8276 *
    diabetes1       1.199      0.83396     1.0647    1.3504 *
    sex2            1.297      0.77111     1.1417    1.4730 *
    ps(age)3        1.384      0.72248     0.4882    3.9244
    ps(age)4        1.906      0.52480     0.3297    11.0120
    ps(age)5        2.490      0.40160     0.2914    21.2754
    ps(age)6        3.112      0.32138     0.3197    30.2842
    ps(age)7        4.217      0.23712     0.4382    40.5885
    ps(age)8        5.506      0.18164     0.5951    50.9334
    ps(age)9        7.924      0.12620     0.8715    72.0486
    ps(age)10       10.445     0.09574     1.1505    94.8202
    ps(age)11       12.153     0.08228     1.3380    110.3863
    ps(age)12       12.815     0.07803     1.4117    116.3283
    ps(age)13       12.817     0.07802     1.4099    116.5280
    ps(age)14       12.068     0.08286     1.2999    112.0411
    ps(age)15       10.921     0.09157     1.0189    117.0496
    ps(age)16       9.846      0.10156     0.6158    157.4348

## \[3.2\] ischemic events, only non-diabetic patients included

### multivariate analysis

#### full model with splines formula

    ~ cardiovasc + tabac2 + dial + apkd01 + sex + pspline(age, df = 5)

#### output

                               coef       se(coef)  se2       Chisq  DF    p    
    cardiovasc1                0.285480   0.142312  0.142301  4.02   1.00  0.045 
    tabac21                    -0.099910  0.143902  0.143890  0.48   1.00  0.490
    dial2                      -0.006071  0.215775  0.215766  0.00   1.00  0.980
    apkd01                     0.075621   0.303539  0.303280  0.06   1.00  0.800
    sex2                       -0.088828  0.149003  0.148991  0.36   1.00  0.550
    pspline(age, df = 5), lin  0.009605   0.005224  0.005224  3.38   1.00  0.066
    pspline(age, df = 5), non                                 2.46   4.05  0.660

#### hazard ratios

                   exp(coef)  exp(-coef)  lower.95    upper.95
    cardiovasc1    1.3304     0.7517      1.00657     1.758 *
    tabac21        0.9049     1.1051      0.68253     1.200
    dial2          0.9939     1.0061      0.65117     1.517
    apkd01         1.0786     0.9272      0.59493     1.955
    sex2           0.9150     1.0929      0.68327     1.225
    ps(age)3       1.3205     0.7573      0.21551     8.091
    ps(age)4       1.7177     0.5822      0.09045     32.619
    ps(age)5       1.8810     0.5316      0.05961     59.362
    ps(age)6       1.8691     0.5350      0.05410     64.582
    ps(age)7       1.8859     0.5303      0.05865     60.637
    ps(age)8       2.2967     0.4354      0.07638     69.063
    ps(age)9       3.2694     0.3059      0.11098     96.315
    ps(age)10      3.2084     0.3117      0.10905     94.395
    ps(age)11      3.0372     0.3293      0.10313     89.447
    ps(age)12      3.3436     0.2991      0.11362     98.396
    ps(age)13      3.4185     0.2925      0.11552     101.165
    ps(age)14      2.9114     0.3435      0.09170     92.434
    ps(age)15      2.8491     0.3510      0.06069     133.756
    ps(age)16      2.8557     0.3502      0.02348     347.328

> dans cette population, la relation entre `age` et HR est linéaire.

#### output of the model without splines

                 coef      exp(coef) se(coef)  z       Pr(>|z|)  
    age          0.009785  1.009833  0.005019  1.950   0.0512 .
    cardiovasc1  0.288463  1.334375  0.142806  2.020   0.0434 *
    tabac21     -0.082943  0.920404  0.142908 -0.580   0.5616  
    dial2       -0.017050  0.983095  0.215591 -0.079   0.9370  
    apkd01       0.071140  1.073731  0.301135  0.236   0.8132  
    sex2        -0.089918  0.914006  0.148931 -0.604   0.5460  


                   exp(coef)  exp(-coef)  lower.95   upper .95
    age            1.0098     0.9903      0.9999     1.020 *
    cardiovasc1    1.3344     0.7494      1.0086     1.765 *
    tabac21        0.9204     1.0865      0.6956     1.218
    dial2          0.9831     1.0172      0.6443     1.500
    apkd01         1.0737     0.9313      0.5951     1.937
    sex2           0.9140     1.0941      0.6826     1.224

[^1]: AIC: the lower, the better.

[^2]: linearity test. H0: relation is linear; H1: relation is not
    linear.

[^3]: `pMiss <- function(x){sum(is.na(x))/length(x)*100}` and
    `apply(DATA,2,pMiss)`

[^4]: 1=not missing, 0=missing

[^5]: test is based on chi-squared statistics and evaluates the
    missingness for the stauts-bmi couple.
