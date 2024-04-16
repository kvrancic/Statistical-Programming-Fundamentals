---
title: "15 strojno ucenje - linearna regresija"
author: "36541994 Karlo Vrančić"
date: "2024-01-31"
output:
  html_document: default
---






## Jednostavna linearna regresija





## Kolinearnost varijabli podatkovnog okvira


## ZADATAK 15.1 - uočavanje linearne povezanosti varijabli



```r
# u varijablu `df` učitajte podatke iz datoteke `podaci1.csv`
# proučite učitani podatkovni okvir

# nacrtajte točkaste grafove odnosa varijable 
# x sa svakom pojedinom varijablom y iz gornjeg podatkovnog okvira
# svakom grafu dodajte i geometriju zaglađivanja, metoda `lm`

df <- read_csv("podaci1.csv", show_col_types = F)
glimpse(df)

g1 <- ggplot(df, aes(x, y1)) + geom_point() + geom_smooth(formula= y~x, method = lm)
g2 <- ggplot(df, aes(x, y2)) + geom_point() + geom_smooth(method = lm)
g3 <- ggplot(df, aes(x, y3)) + geom_point() + geom_smooth(method = lm)
g4 <- ggplot(df, aes(x, y4)) + geom_point() + geom_smooth(method = lm)

grid.arrange(g1, g2, g3, g4)
```

```
## `geom_smooth()` using formula = 'y ~ x'
## `geom_smooth()` using formula = 'y ~ x'
## `geom_smooth()` using formula = 'y ~ x'
```

<img src="36541994_15_strojno_ucenje_linreg_RB_files/figure-html/unnamed-chunk-2-1.png" width="672" />

```r
# odgovorite na pitanja:
# na kojim grafovima uočavate moguću linearnu povezanost varijabli?
# koji graf predočava nelinearnu povezanost?
# za koji graf biste mogli reći da su varijable nezavisne?
```

```
## Rows: 100
## Columns: 5
## $ x  <dbl> 77.07027, 63.33037, 86.69761, 67.77708, 94.50442, 24.45959, 58.6478…
## $ y1 <dbl> 380.2816, 296.4237, 438.5933, 324.6158, 479.2008, 159.7625, 267.134…
## $ y2 <dbl> 590.92836, 89.40939, 427.32770, 229.01220, 623.72516, 281.81971, 45…
## $ y3 <dbl> 257.89895, 380.91383, 300.12902, 489.92636, 127.15588, 193.72857, 5…
## $ y4 <dbl> 48.479306, 121.197519, 373.190120, -53.868022, -56.801609, 64.00619…
```


## Pearsonov koeficijent korelacije


## ZADATAK 15.2 - izračun koeficijenta korelacije



```r
# za svaki graf iz prethodnog zadatka izračunajte i ispišite
# koeficijent korelacije između prikazanih varijabli (funkcija `cor`)

cor(df$x, df$y1)
cor(df$x, df$y2)
cor(df$x, df$y3)
cor(df$x, df$y4)
```

```
## [1] 0.9758326
## [1] 0.6765991
## [1] -0.04977038
## [1] 0.1783745
```


## Mjera "R-kvadrat"


## Funkcija `lm`



```r
lm(formula, data)
```



```r
linMod <- lm(y ~ x, data = df)
```




## ZADATAK 15.3 - stvaranje jednostavnog linearnog modela


```r
# uz pomoć funkcije `lm` stvorite linearni model podataka iz tablice `df`
# gdje je `x` ulazna a `y1` izlazna varijabla
# rezultat spremite u varijablu `linMod`
linMod <- lm(y1 ~ x, data = df)

# ispišite varijablu `linMod`
linMod
```

```
## 
## Call:
## lm(formula = y1 ~ x, data = df)
## 
## Coefficients:
## (Intercept)            x  
##      46.733        3.999
```



## Objekt klase `lm`

Ovaj objekt sadržava ne samo koeficijente, već i bogati skup informacija vezanih uz stvoreni linearni model, što uključuje čak i sam podatkovni skup pomoću kojeg je model stvoren. 

Nad ovim objektom možemo izvesti sljedeće funkcije:

- `coef` - vraća koeficijente u obliku vektora
- `fitted.values` - vraća vektor predikcijavdobiven primjenom modela na skup za treniranje
- `residuals` - vraća vektor grešaka dobiven primjenom modela na skup za treniranje
- `summary` - daje sažetak najvažnijih informacija o modelu

Isprobajmo funkciju sažetka - `summary` - nad našim linearnim modelom.



## ZADATAK 15.4 -  sažetak linearnog modela


```r
# izvršite funkciju `summary` nad linearnim modelom `linMod`
summary(linMod)
```

```
## 
## Call:
## lm(formula = y1 ~ x, data = df)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -74.010 -15.492  -1.021  15.613  77.371 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 46.73328    5.26089   8.883  3.2e-14 ***
## x            3.99851    0.09045  44.208  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 26.35 on 98 degrees of freedom
## Multiple R-squared:  0.9522,	Adjusted R-squared:  0.9518 
## F-statistic:  1954 on 1 and 98 DF,  p-value: < 2.2e-16
```


## Interpretacija sažetka linearnog modela

Postoji nekoliko različitih "nesigurnosti" u dobiveni rezultat:

1) Da li linearni trend uopće postoji, ili se uočena kolinearnost mogla pojaviti slučajno? 

2) Ako trend postoji, koliko smo sigurni da izračunati koeficijent smjera odgovara "stvarnom"?

3) Konačno, ako trend postoji a mi smo uspjeli dovoljno dobro pogoditi "pravi" koeficijent, koliko nam dodatni "šum" utječe na točnost predikcija? 

Prikazani sažetak nam pruža odgovore na ova pitanja. 

## Stvaranje (novih) predikcija

R nam nudi generičku metodu `predict` kojoj u općenitom slučaju kao parametre šaljemo stvoreni prediktivni model i **podatkovni okvir** sa novim podacima, pri čemu moramo voditi računa da podatkovni okvir ima stupce koji odgovaraju očekivanim ulazima modela. 

Budući da prediktivni modeli često u sebi sadrže i skup korišten za stvaranje modela, ovoj funkciji možemo proslijediti i model bez dodatnih podataka - u tom slučaju ona će nam jednostavno vratiti skup predikacija dobivenih nad originalnim skupom (tj. isti rezultat koji bi nam dala funkcija `fitted.values`).



## ZADATAK 15.5 - stvaranje novih predikcija


```r
# sljedeći vektor prikazuje "nove" vrijednosti ulazne varijable `x`
novi_x <- c(-5, 10, 50, 102)

# stvorite i ispišite predikcije za gornji vektor pomoću 
# funkcije `predict` i linearnog modela `linMod`inearnog modela `linMod`
# pripazite da nove podatke šaljete u obliku podatkovnog okvira
predict(linMod, data.frame(x = novi_x))

# izračunajte predikcije "ručno", korištenjem jednadžbe pravca
# i dobivenih koeficijenata linearnog modela

rucnoIzracunato <- sapply(novi_x, function(x) 4*x + 46.733)
# coef(linMod)[1] + novi_x +* coef(linMod)[2]
rucnoIzracunato
```

```
##         1         2         3         4 
##  26.74074  86.71835 246.65864 454.58101 
## [1]  26.733  86.733 246.733 454.733
```



## Vizualizacija reziduala

dva pitanja koje analitičar potencijalno može postaviti vezano uz reziduale su:

- da li postoje očiti uzorci u ponašanju reziduala obzirom na slijed originalnih obzervacija? 
- da li reziduali imaju normalnu razdiobu?

## Paket `broom`

Paket `broom` nudi niz funkcija za uređivanje dobivenih prediktivnih modela i lako izvlačenje informacija iz istih - npr. funkcija `tidy` nam daje rezultate modela (tj. koeficijente) složene u lako čitljiv podatkovni okvir, dok nam funkcija `glance` radi isto ali nad parametrima koji opisuju kvalitetu modela. 

Mi ćemo se u nastavku poslužiti metodom `augment` koja na osnovu prosljeđenog prediktivnog modela vraća originalni podatkovni okvir korišten za stvaranje modela, ali proširen sa nizom korisnih stupaca


## ZADATAK 15.6 - paket `broom`


```r
# primjenite funkciju `augment` nad linearnim modelom `linMod`
# rezultantni podatkovni okvir pohranite u varijablu `predikcije`


# proučite prvih nekoliko redaka okvira `predikcije`
predikcijaDetaljno <- augment(linMod)

predikcijaDetaljno
```

```
## # A tibble: 100 × 8
##       y1     x .fitted .resid   .hat .sigma  .cooksd .std.resid
##    <dbl> <dbl>   <dbl>  <dbl>  <dbl>  <dbl>    <dbl>      <dbl>
##  1  380.  77.1    355.  25.4  0.0184   26.4 0.00887       0.972
##  2  296.  63.3    300.  -3.54 0.0120   26.5 0.000111     -0.135
##  3  439.  86.7    393.  45.2  0.0256   26.1 0.0396        1.74 
##  4  325.  67.8    318.   6.88 0.0136   26.5 0.000475      0.263
##  5  479.  94.5    425.  54.6  0.0330   25.9 0.0757        2.11 
##  6  160.  24.5    145.  15.2  0.0179   26.4 0.00310       0.583
##  7  267.  58.6    281. -14.1  0.0108   26.4 0.00158      -0.538
##  8  194.  32.1    175.  19.2  0.0139   26.4 0.00382       0.736
##  9  372.  71.0    331.  41.0  0.0150   26.2 0.0187        1.57 
## 10  201.  42.4    216. -15.8  0.0107   26.4 0.00198      -0.604
## # ℹ 90 more rows
```



## Funkcija `augment`

Uočite da nam metoda `augment` zapravo proširuje originalni podatkovni okvir nizom stupaca relevantnih za dobiveni linearni model. Ovdje nećemo objašnjavati sve dobivene stupce, no neki od njih su:

- `.fitted` - predikcije dobivene iz modela
- `.se.fit` - standardna greška pojedine predikcije
- `.resid` - iznos reziduala, tj. greške
- `.std.resid` - reziduali standardizirani na interval [0,1]
- `.hat` - mjera "ekstremnosti" ulazne varijable ove obzervacije (engl. *leverage*)
- `.cooksd` - mjera "utjecajnosti" obzervacije (engl. *influential point*); radi se o obzervacijama koju imaju visoku "*leverage*" mjeru i visoki rezidual

Metodu `augment` možemo koristiti i kao alternativu generičkoj metodi `predict` - samo joj moramo proslijediti nove podatke uz pomoć parametra `newdata`.

## Analiza reziduala

Sada kada imamo podatkovni okvir koji sadrži podatke o rezidualima, možemo stvoriti spomenute vizualizacije. Konkretno, stvoriti ćemo

- točkasti grafa sa predikcijama na osi `x` i (standardiziranim) rezidualima na osi `y`
- graf funkcije gustoće razdiobe standardiziranih reziduala
- kvantil-kvantil graf standardiziranih reziduala

Razlog zašto radimo sa standardiziranim umesto "pravim" rezidualima jest samo lakša interpretacija, tj. jednostavnija usporedba rezultata sa "standardnom" normalnom razdiobom koja ima sredinu 0 i standardnu devijaciju 1.



## ZADATAK 15.7 - provjera "normalnosti" reziduala


```r
# uz pomoć podatkovnog okvira `predikcije`
# stvorite točkasti graf predikcija i std. reziduala
# na grafu nacrtajte i horizontalnu liniju koja prolazi kroz nulu

# stvorite graf gustoće razdiobe standardnih reziduala
# koristite geometriju `geom_density`

# stvorite kvantil-kvantil graf std. reziduala
# koristite geometriju `geom_qq`
# reziduale postavite na estetiku `sample` (ne `x`!)
glimpse(predikcijaDetaljno)

g1 <- ggplot(predikcijaDetaljno, aes(.fitted, .resid)) + geom_point() + geom_hline(yintercept = 0, color = "red")
g2 <- ggplot(predikcijaDetaljno, aes(.std.resid)) + geom_density()
g3 <- ggplot(predikcijaDetaljno, aes(sample = .std.resid)) + geom_qq()

grid.arrange(g1, g2, g3, ncol = 2)
```

<img src="36541994_15_strojno_ucenje_linreg_RB_files/figure-html/unnamed-chunk-10-1.png" width="672" />

```r
# sve ovo i više se dobije s plot(linMod)
plot(linMod)
```

<img src="36541994_15_strojno_ucenje_linreg_RB_files/figure-html/unnamed-chunk-10-2.png" width="672" /><img src="36541994_15_strojno_ucenje_linreg_RB_files/figure-html/unnamed-chunk-10-3.png" width="672" /><img src="36541994_15_strojno_ucenje_linreg_RB_files/figure-html/unnamed-chunk-10-4.png" width="672" /><img src="36541994_15_strojno_ucenje_linreg_RB_files/figure-html/unnamed-chunk-10-5.png" width="672" />

```
## Rows: 100
## Columns: 8
## $ y1         <dbl> 380.2816, 296.4237, 438.5933, 324.6158, 479.2008, 159.7625,…
## $ x          <dbl> 77.07027, 63.33037, 86.69761, 67.77708, 94.50442, 24.45959,…
## $ .fitted    <dbl> 354.8993, 299.9602, 393.3943, 317.7404, 424.6099, 144.5351,…
## $ .resid     <dbl> 25.3822489, -3.5364746, 45.1990145, 6.8753896, 54.5909458, …
## $ .hat       <dbl> 0.01841697, 0.01198738, 0.02557283, 0.01358130, 0.03297923,…
## $ .sigma     <dbl> 26.35694, 26.48253, 26.07376, 26.47567, 25.87825, 26.43900,…
## $ .cooksd    <dbl> 8.868472e-03, 1.106025e-04, 3.962438e-02, 4.751579e-04, 7.5…
## $ .std.resid <dbl> 0.97228561, -0.13502574, 1.73772703, 0.26272049, 2.10683246…
```



## Dijagnostika problema uz pomoć vizualizacije reziduala

Neki od mogućih zaključaka nakon stvaranja vizualizacija reziduala mogu biti sljedeći:

- ako točkasti graf sa predikcijama i rezidualima pokazuje očite uzorke, moguće da linearni model nije dobar za opis odnosa prediktora i cilja te treba posegnuti za modelom koji može opisati složeniju prirodu odnosa
- ako graf reziduala ima oblik "lijevka", tj. ako reziduali rastu sa povećanjem vrijednosti predikcija, možda je potrebno transformirati ulazne i/ili izlazne varijable, npr. uz pomoć funkcije korijena ili logaritma
- ako uočavamo neke vrijednosti koje izrazito "iskaču" u grafu reziduala trebamo ih pažljivije pogledati i potencijalno ukloniti iz skupa za stvaranje modela


## Linearna regresija i kategorijske varijable




Može li kategorijska varijabla biti ulaz u prediktivni model?

Može, uz određenu prilagodbu. Moramo **pretvoriti kategorijsku varijablu u binarnu (indikatorsku) varijablu** koja opisuje pripada li određena obzervacija odabranoj kategoriji (ako ne pripada, onda logično pripada onoj drugoj, referentnoj ili *baseline* kategoriji). 

Linearna regresija će potom odrediti koeficijent koji će definirati pravac na način da se koeficijent pribraja ako je indikatorska varijabla `1`, ili se ne uzima u obzir ako je indikatorska varijabla `0`.

## Dvorazinske i višerazinske kategorijske varijable

Koliko nam treba indikatorskih varijabli za kategorijsku varijablu sa dvije kategorije? 

- **jedna** (druga bi bila inverz prve)

Koliko nam treba indikatorskih varijabli za kategorijsku varijablu sa više od dvije kategorije? 

- **jedna manje od broja kategorija**, budući da "nepripadanje" svim kategorijama osim jedne nužno označava pripadanje toj jednoj, preostaloj kategoriji.


## ZADATAK 15.8  - podatkovni okvir sa kategorijskim prediktorom



```r
# u varijablu `df` učitajte podatke iz datoteke `podaci2.csv`
# proučite učitani podatkovni okvir
df <- read_csv("podaci2.csv")
```

```
## Rows: 100 Columns: 2
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (1): x
## dbl (1): y
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
df$x = as.factor(df$x)
glimpse(df)

# nacrtajte točkasti graf ovisnosti varijable `y` o varijabli `x`
ggplot(df, aes(x, y)) + geom_point()
```

<img src="36541994_15_strojno_ucenje_linreg_RB_files/figure-html/unnamed-chunk-12-1.png" width="672" />

```
## Rows: 100
## Columns: 2
## $ x <fct> A, B, B, A, B, B, B, B, B, A, A, A, A, A, B, A, A, A, B, B, B, B, A,…
## $ y <dbl> 20.341025, 63.613923, 70.445313, 16.628283, 81.565885, 88.678272, 73…
```



## Automatsko stvaranje indikatorske varijable

Jezik R, tj. funkcija `lm` će **automatski stvoriti** indikatorske varijable ako kategorijske varijable postavimo u regresijsku formulu.

OPREZ! Kod stvaranja predikcija za nove podatke moramo biti sigurni da kategorijska varijabla ne sadrži kategorije koje nisu bile zastupljene u podacima korištenim za stvaranje modela.



## ZADATAK 15.9 - stvaranje linearnog modela sa kategorijskim ulazom



```r
# uz pomoć funkcije `lm` stvorite linearni model podataka iz tablice `df`
# gdje je `x` ulazna a `y` izlazna varijabla
# rezultat spremite u varijablu `linMod`
linMod <- lm(y ~ x, df)
summary(linMod)
```

```
## 
## Call:
## lm(formula = y ~ x, data = df)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -19.206  -6.104  -1.367   5.523  28.665 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   20.493      1.398   14.66   <2e-16 ***
## xB            55.652      1.938   28.71   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 9.684 on 98 degrees of freedom
## Multiple R-squared:  0.8938,	Adjusted R-squared:  0.8927 
## F-statistic: 824.4 on 1 and 98 DF,  p-value: < 2.2e-16
```



## Interpretacija linearnog modela sa kategorijskim ulazom

Vidimo da je sažetak linearnog modela vrlo sličan već prikazanom sažetku gdje je ulazna varijabla bila numeričkog tipa. Razlika u interpretaciji je sljedeća - koeficijent smjera veže se uz konkretnu kategoriju (navedenu uz ime varijable), a tiče se **očekivane razlike u iznosu ciljne varijable kad obzervacija ima navedenu kategoriju, u odnosu na referentnu kategoriju**. 

Za kraj ovog dijela naglasimo samo da je kod korištenja kategorijskih varijabli kao ulaze u linearni model bitno voditi računa o zastupljenosti kategorija, tj. da nemamo **kategorije koje su vrlo slabo zastupljene** u podatkovnom skupu za treniranje. Razlog je taj što ovakve obzervacije vrlo često **imaju veliki utjecaj na regresijski pravac**, a što može imati nepovoljne posljedice na kvalitetu linearnog modela.



## Višestruka (multipla) linearna regresija 


Princip jednostavne linearne regresije lako se proširuje na scenarij kada imamo više ulaznih varijabli - jednostavno rečeno, tražimo funkciju koja će ciljnu varijablu izraziti kao linearnu kombinaciju ulaznih varijabli. Problem izgradnje modela opet se svodi na traženje "dobrih" koeficijenata smjera koji će ići uz svaku ulaznu varijablu (plus odsječak), iako formalno sada ne možemo pričati o "pravcu" regresije već se radi o nešto kompleksnijem pojmu "hiper-ravnine".

## Formule za višestruku linearnu regresiju

Kod višestruke linearne regresije pojavljuje se niz dodatnih izazova s kojima se moramo suočiti, no za samo treniranje modela koristimo već upoznatu funkciju `lm`, kojoj je dovoljno proslijediti željenu formulu, npr:


```r
y ~ x1 + x2              # `y` kao linearna kombinacija `x1` i `x2`
y ~ .                    # `y` kao linearna kombinacija svih ostalih varijabli
y ~ . - x1 - x2          # `y` kao linearna kombinacija svih ostalih varijabli OSIM x1 i x2
log(y)  ~ x1 + log(x2)   #  prirodni logaritam od `y` kao linearna kombinacija `x1` i
                              # prirodnog logaritma od `x2`
y ~ x1 + I(x2^2)         # `y` kao linearna kombinacija `x1` i kvadrata od `x2`
```



## Podatkovni skup `mtcars`

Pokušajmo sada stvoriti prediktivni model sa više ulaznih varijabli. U zadatku ćemo koristiti otprije upoznati podatkovni skup `mtcars` (ako je potrebno podsjetite se dodatnih detalja o ovom skupu uz pomoć dokumentacije).


```r
data(mtcars)
# faktoriziramo stupce  `vs` i `am`
cols <- c("vs", "am")
mtcars[, cols] <- lapply(mtcars[, cols], factor)

glimpse(mtcars)
```

```
## Rows: 32
## Columns: 11
## $ mpg  <dbl> 21.0, 21.0, 22.8, 21.4, 18.7, 18.1, 14.3, 24.4, 22.8, 19.2, 17.8,…
## $ cyl  <dbl> 6, 6, 4, 6, 8, 6, 8, 4, 4, 6, 6, 8, 8, 8, 8, 8, 8, 4, 4, 4, 4, 8,…
## $ disp <dbl> 160.0, 160.0, 108.0, 258.0, 360.0, 225.0, 360.0, 146.7, 140.8, 16…
## $ hp   <dbl> 110, 110, 93, 110, 175, 105, 245, 62, 95, 123, 123, 180, 180, 180…
## $ drat <dbl> 3.90, 3.90, 3.85, 3.08, 3.15, 2.76, 3.21, 3.69, 3.92, 3.92, 3.92,…
## $ wt   <dbl> 2.620, 2.875, 2.320, 3.215, 3.440, 3.460, 3.570, 3.190, 3.150, 3.…
## $ qsec <dbl> 16.46, 17.02, 18.61, 19.44, 17.02, 20.22, 15.84, 20.00, 22.90, 18…
## $ vs   <fct> 0, 0, 1, 1, 0, 1, 0, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 0,…
## $ am   <fct> 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 0, 0,…
## $ gear <dbl> 4, 4, 4, 3, 3, 3, 3, 4, 4, 4, 4, 3, 3, 3, 3, 3, 3, 4, 4, 4, 3, 3,…
## $ carb <dbl> 4, 4, 1, 1, 2, 1, 4, 2, 2, 4, 4, 3, 3, 3, 4, 4, 4, 1, 2, 1, 1, 2,…
```



## ZADATAK 15.10- stvaranje linearnog modela sa više prediktora


```r
# uz pomoć funkcije `lm` stvorite linearni model podataka iz tablice `mtcars`
# koristite varijable `am`,  `cyl` i `wt` kao ulaz
# i varijablu `mpg` kao izlay
#
# proučite sažetak modela

linModAuti <- lm(mpg ~ am + cyl + wt, data = mtcars)

summary(linModAuti)
```

```
## 
## Call:
## lm(formula = mpg ~ am + cyl + wt, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -4.1735 -1.5340 -0.5386  1.5864  6.0812 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  39.4179     2.6415  14.923 7.42e-15 ***
## am1           0.1765     1.3045   0.135  0.89334    
## cyl          -1.5102     0.4223  -3.576  0.00129 ** 
## wt           -3.1251     0.9109  -3.431  0.00189 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.612 on 28 degrees of freedom
## Multiple R-squared:  0.8303,	Adjusted R-squared:  0.8122 
## F-statistic: 45.68 on 3 and 28 DF,  p-value: 6.51e-11
```



## ZADATAK 15.11 - kolinearnost ulaznih varijabli


```r
# u podatkovni okvir `mtcarsNumInputs` ubacite sve numeričke
# varijable podatkovnog okvira `mtcars` osim ciljne varijable `mpg`
mtcars %>% dplyr::select(-mpg) %>% dplyr::select_if(is.numeric) -> mtCarsNumPred

# uz pomoć funkcije `cor` ispišite korelacijsku matricu
# stupaca okvira `mtCarsNumPred`
mtCarsNumPred %>% cor 

ggpairs(mtCarsNumPred)
```

<img src="36541994_15_strojno_ucenje_linreg_RB_files/figure-html/unnamed-chunk-17-1.png" width="672" />

```
##             cyl       disp         hp        drat         wt        qsec
## cyl   1.0000000  0.9020329  0.8324475 -0.69993811  0.7824958 -0.59124207
## disp  0.9020329  1.0000000  0.7909486 -0.71021393  0.8879799 -0.43369788
## hp    0.8324475  0.7909486  1.0000000 -0.44875912  0.6587479 -0.70822339
## drat -0.6999381 -0.7102139 -0.4487591  1.00000000 -0.7124406  0.09120476
## wt    0.7824958  0.8879799  0.6587479 -0.71244065  1.0000000 -0.17471588
## qsec -0.5912421 -0.4336979 -0.7082234  0.09120476 -0.1747159  1.00000000
## gear -0.4926866 -0.5555692 -0.1257043  0.69961013 -0.5832870 -0.21268223
## carb  0.5269883  0.3949769  0.7498125 -0.09078980  0.4276059 -0.65624923
##            gear       carb
## cyl  -0.4926866  0.5269883
## disp -0.5555692  0.3949769
## hp   -0.1257043  0.7498125
## drat  0.6996101 -0.0907898
## wt   -0.5832870  0.4276059
## qsec -0.2126822 -0.6562492
## gear  1.0000000  0.2740728
## carb  0.2740728  1.0000000
```


Još jedan zgodan način vizualizacije kolinearnosti nam pruža funkcija corrplot istoimenog paketa.

## Zadatak 15.12 - funkcija `corrplot`


```r
# učitajte paket `corrplot` (instalirajte ako je potrebno)
# pozovite funkciju `corrplot` kojoj ćete proslijediti korelacijski matricu
# stupaca okvira `mtcarsNumInputs`
#library(ggcorrplot)
ggcorrplot(cor(mtCarsNumPred), type = "lower", outline.color = "white", lab = T)
```

<img src="36541994_15_strojno_ucenje_linreg_RB_files/figure-html/unnamed-chunk-18-1.png" width="672" />



## ZADATAK 15.13 - multikolinearnost



```r
# istrenirajte linearni model `lm_sve` koja za okvir `mtcars`
# gleda ovisnost varijable `mpg` o svim ostalim varijablama
#
# navedeni model proslijedite funkciji `vif` paketa `cars` i ispišite rezultat
lm_sve <- lm(mpg ~ ., data = mtcars)
vif(lm_sve)
```

```
##       cyl      disp        hp      drat        wt      qsec        vs        am 
## 15.373833 21.620241  9.832037  3.374620 15.164887  7.527958  4.965873  4.648487 
##      gear      carb 
##  5.357452  7.908747
```


## Rješavanje problema kolinearnosti i multikolinearnosti

Sad kada znamo da je kolinearnost ulaznih varijabli potencijalni problem, možemo postaviti pitanje - što učiniti kada uočimo navedenu pojavu? Neke od mogućih rješenja su:

- izbaciti jednu od para problematičnih varijabli
- transformirati kolinearne varijable u alternativnu jedinstvenu ulaznu varijablu


## ZADATAK 15.14 - linearni model sa kolinearnim ulazima


```r
# trenirajte sljedeće linearne modele:
#  `lm1` - `mpg` u ovisnosti o `disp`
#  `lm2` - `mpg` u ovisnosti o `wt`
#  `lm3` - `mpg` u ovisnosti o `disp` i `wt`

#
# proučite sažetke dobivenih linearnih modela,
# poglavito t-vrijednosti parametara i prilagođenu R-kvadrat mjeru

lm1 <- lm(mpg ~ disp, data = mtcars)
lm2 <- lm(mpg ~ wt, data = mtcars)
lm3 <- lm(mpg ~ disp + wt, data = mtcars)

summary(lm1)
summary(lm2)
summary(lm3)
```

```
## 
## Call:
## lm(formula = mpg ~ disp, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -4.8922 -2.2022 -0.9631  1.6272  7.2305 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 29.599855   1.229720  24.070  < 2e-16 ***
## disp        -0.041215   0.004712  -8.747 9.38e-10 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 3.251 on 30 degrees of freedom
## Multiple R-squared:  0.7183,	Adjusted R-squared:  0.709 
## F-statistic: 76.51 on 1 and 30 DF,  p-value: 9.38e-10
## 
## 
## Call:
## lm(formula = mpg ~ wt, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -4.5432 -2.3647 -0.1252  1.4096  6.8727 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  37.2851     1.8776  19.858  < 2e-16 ***
## wt           -5.3445     0.5591  -9.559 1.29e-10 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 3.046 on 30 degrees of freedom
## Multiple R-squared:  0.7528,	Adjusted R-squared:  0.7446 
## F-statistic: 91.38 on 1 and 30 DF,  p-value: 1.294e-10
## 
## 
## Call:
## lm(formula = mpg ~ disp + wt, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3.4087 -2.3243 -0.7683  1.7721  6.3484 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 34.96055    2.16454  16.151 4.91e-16 ***
## disp        -0.01773    0.00919  -1.929  0.06362 .  
## wt          -3.35082    1.16413  -2.878  0.00743 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.917 on 29 degrees of freedom
## Multiple R-squared:  0.7809,	Adjusted R-squared:  0.7658 
## F-statistic: 51.69 on 2 and 29 DF,  p-value: 2.744e-10
```



## Interpretacija dobivenih rezultata


Usporedivši rezultate dobivenih linearnih modela možemo zaključiti kako linearni model `lm3` ima najmanju standardnu grešku reziduala i najveću "R-kvadrat" mjeru te je time najbolja od tri opcije. No potencijalni problem se očituje kada pogledamo p-vrijednosti, koje su obje znatno veće nego kada smo trenirali modele sa svakom varijablom zasebno. Dakle, kolinearnost varijabli ne mora nužno utjecati na prediktivnu moć modela, ali unosi potencijalno veliku nesigurnost u modelu smislu da sve kolinearne prediktore izbacimo iz modela kao irelevantne. To bi se mogao pokazati kao velik problem kada imamo više potencijalnih prediktora i pokušavamo odabrati relevantni podskup, što je tema kojom ćemo se baviti u nastavku.

## Odabir varijabli

**Odabir varijabli (*variable selection*) jedan od ključnih izazova s kojima se suočavamo u izradi prediktivnih modela**, ne samo kod linearne regresije već i općenito.

Očito je da bi dobar model trebao sadržavati ulazne varijable koje dobro "objašnjavaju" ciljnu varijablu a koje su što više međusobno nezavisne. Mogući **kriterij za odluku** koje varijablu odabrati za ugrađivanje u model tako može biti utjecaj na povećanje zajedničke **"R-kvadrat"** mjere, smanjenje **standardne greške reziduala** ili **p-vrijednost** koeficijenta za tu ulaznu varijablu. Pored ovih "standardnih" kriterija postoje i razni drugi, kao npr. popularni **AIC** (engl. *Akaike information criterion*) koji procjenjuje informativnost modela uz penaliziranje većeg broj varijabli.

## Iterativna (*stepwise*) izgradnja prediktivnog modela

Varijable možemo odabirati ručno, no puno je lakše taj posao ostaviti računalu. Statistički alati, uključujući i jezik R, često imaju ugrađene algoritme koji na osnovu zadanog kriterija izgrađuju prediktivni model iterativnim odabirom varijabli. Najčešće strategije izgradnje modela su:

- **"unatrag" od potpunog modela**, npr. iterativno se izbacuju varijable sa najvećom p-vrijednosti
- **"unaprijed" od praznog modela**, npr. iterativno se dodaju varijable koje najviše smanjuju RMSE
- razne **hibridne** metode


## Funkcija `stepAIC`

Jezik R ima funkciju `step` za iterativno (engl. *stepwise*) stvaranje prediktivnih modela, no u praksi se preporučuje puno bolja funkcija `stepAIC` koju možemo naći u paketu `MASS`. Ova funkcija između ostalog očekuje sljedeće parametre:

- `object` - inicijalni (linearni) model 
- `scope` - raspon modela koje uključujemo u strategiju; potreban je samo za izgradnju "unaprijed" a prosljeđujemo joj listu sa "najsiromašnijim" (`lower`) i  "najbogatijim" (`upper`) modelom
- `direction` - inaprijed (`forward`), unatrag (`backward`) ili hibridno (`both`)
- `trace` - binarna varijabla koja opisuje želimo li ispis cijelog procesa odabira varijabli


Za kraj ćemo iterativno stvoriti prediktivni model za podatkovni okvir `mtcars` gdje će opet ciljna varijabla biti potrošnja (varijabla `mpg`) dok će kandidati za ulaznu varijablu biti sve ostale varijable.




## Zadatak 15.15 - iterativna selekcija varijabli za linearnu regresiju


```r
#library(MASS) # ako je potrebno

# stvaramo "potpuni" i "prazni" model 
lm_sve <- lm(mpg ~ ., data = mtcars)   
lm_prazan <- lm(mpg ~ 1, data = mtcars)

# pogledajte sažetke gornjih modela kako bi 
# dobili dojam kako rade "ekstremi"

# uz pomoć funkcije `stepAIC` stvorite modele `lm1` i `lm2` 
# na sljedeći način
# `lm1` - nastaje selekcijom "unatrag" od punog modela
#         (parametar direction = "backward")
# `lm2` - nastaje selekcijom "unaprijed" od praznog modela
#         (parametri direction = "forward" , 
#         scope = list(upper = lm_sve, lower = lm_prazan))
#
# proučite sažetke dobivenih modela

summary(lm_sve)

summary(lm_prazan)

lm1 <- stepAIC(lm_sve, direction="backward", trace = 0) 
summary(lm1)

lm2 <- stepAIC(lm_prazan, scope = list(upper = lm_sve, lower = lm_prazan), 
                direction="forward", trace = 0)
summary(lm2)
```

```
## 
## Call:
## lm(formula = mpg ~ ., data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3.4506 -1.6044 -0.1196  1.2193  4.6271 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)  
## (Intercept) 12.30337   18.71788   0.657   0.5181  
## cyl         -0.11144    1.04502  -0.107   0.9161  
## disp         0.01334    0.01786   0.747   0.4635  
## hp          -0.02148    0.02177  -0.987   0.3350  
## drat         0.78711    1.63537   0.481   0.6353  
## wt          -3.71530    1.89441  -1.961   0.0633 .
## qsec         0.82104    0.73084   1.123   0.2739  
## vs1          0.31776    2.10451   0.151   0.8814  
## am1          2.52023    2.05665   1.225   0.2340  
## gear         0.65541    1.49326   0.439   0.6652  
## carb        -0.19942    0.82875  -0.241   0.8122  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.65 on 21 degrees of freedom
## Multiple R-squared:  0.869,	Adjusted R-squared:  0.8066 
## F-statistic: 13.93 on 10 and 21 DF,  p-value: 3.793e-07
## 
## 
## Call:
## lm(formula = mpg ~ 1, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -9.6906 -4.6656 -0.8906  2.7094 13.8094 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   20.091      1.065   18.86   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 6.027 on 31 degrees of freedom
## 
## 
## Call:
## lm(formula = mpg ~ wt + qsec + am, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3.4811 -1.5555 -0.7257  1.4110  4.6610 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   9.6178     6.9596   1.382 0.177915    
## wt           -3.9165     0.7112  -5.507 6.95e-06 ***
## qsec          1.2259     0.2887   4.247 0.000216 ***
## am1           2.9358     1.4109   2.081 0.046716 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.459 on 28 degrees of freedom
## Multiple R-squared:  0.8497,	Adjusted R-squared:  0.8336 
## F-statistic: 52.75 on 3 and 28 DF,  p-value: 1.21e-11
## 
## 
## Call:
## lm(formula = mpg ~ wt + cyl + hp, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3.9290 -1.5598 -0.5311  1.1850  5.8986 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 38.75179    1.78686  21.687  < 2e-16 ***
## wt          -3.16697    0.74058  -4.276 0.000199 ***
## cyl         -0.94162    0.55092  -1.709 0.098480 .  
## hp          -0.01804    0.01188  -1.519 0.140015    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.512 on 28 degrees of freedom
## Multiple R-squared:  0.8431,	Adjusted R-squared:  0.8263 
## F-statistic: 50.17 on 3 and 28 DF,  p-value: 2.184e-11
```


## Domaća zadaća

1. Učitajte skup podataka `iris` u globalno okruženje.

Nacrtajte raspršeni graf koji prikazuje odnos između duljine latice (*Petal*) i duljine čašnog lista (*Sepal*). Korištenjem geometrije za crtanje dijagonalnih referentnih linija (funkcija `geom_abline`) pokušajte pogoditi nagib i presjecište za liniju koja "najbolje odgovara". Nemojte varati korištenjem funkcije `geom_smooth`!

Primjer: `... + geom_abline(slope = 1, intercept = -3)`



```r
data(iris)

glimpse(iris)
head(iris)

iris %>% ggplot(aes(x = Petal.Length, y = Sepal.Length)) + geom_point() + 
  geom_abline(slope = 0.5, intercept = 4.2)
```

<img src="36541994_15_strojno_ucenje_linreg_RB_files/figure-html/unnamed-chunk-22-1.png" width="672" />

```
## Rows: 150
## Columns: 5
## $ Sepal.Length <dbl> 5.1, 4.9, 4.7, 4.6, 5.0, 5.4, 4.6, 5.0, 4.4, 4.9, 5.4, 4.…
## $ Sepal.Width  <dbl> 3.5, 3.0, 3.2, 3.1, 3.6, 3.9, 3.4, 3.4, 2.9, 3.1, 3.7, 3.…
## $ Petal.Length <dbl> 1.4, 1.4, 1.3, 1.5, 1.4, 1.7, 1.4, 1.5, 1.4, 1.5, 1.5, 1.…
## $ Petal.Width  <dbl> 0.2, 0.2, 0.2, 0.2, 0.2, 0.4, 0.3, 0.2, 0.2, 0.1, 0.2, 0.…
## $ Species      <fct> setosa, setosa, setosa, setosa, setosa, setosa, setosa, s…
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1          5.1         3.5          1.4         0.2  setosa
## 2          4.9         3.0          1.4         0.2  setosa
## 3          4.7         3.2          1.3         0.2  setosa
## 4          4.6         3.1          1.5         0.2  setosa
## 5          5.0         3.6          1.4         0.2  setosa
## 6          5.4         3.9          1.7         0.4  setosa
```
Istrenirajte model linearne regresije s duljinom latice kao prediktorom i duljinom čašnog lista kao ciljnom varijablom. Usporedite dobivene parametre s onima koje ste sami procijenili.


```r
linMod <- lm(Sepal.Length ~ Petal.Length, data = iris)

coef(linMod)
```

```
##  (Intercept) Petal.Length 
##    4.3066034    0.4089223
```



2. Ponovno nacrtajte raspršeni graf iz 1), ali obojite točke na temelju vrste cvijeta. Objasnite zašto jedan regresijski model možda nije najbolji izbor za predviđanje duljine latice na temelju duljine čašnog lista.

```r
# Ponovno nacrtajte raspršeni graf iz 1), ali obojite točke na temelju vrste cvijeta. Objasnite zašto jedan regresijski model možda nije najbolji izbor za predviđanje duljine latice na temelju duljine čašnog lista.

iris %>% ggplot(aes(x = Petal.Length, y = Sepal.Length, color = Species)) + geom_point() + 
  geom_abline(slope = 0.5, intercept = 4.2)
```

<img src="36541994_15_strojno_ucenje_linreg_RB_files/figure-html/unnamed-chunk-24-1.png" width="672" />

```r
iris %>% ggplot(aes(x = Petal.Length, y = Sepal.Length)) + geom_point(aes(color = Species)) + 
  geom_abline(slope = 0.5, intercept = 4.2)
```

<img src="36541994_15_strojno_ucenje_linreg_RB_files/figure-html/unnamed-chunk-24-2.png" width="672" />

```r
# Objasnite zašto jedan regresijski model možda nije najbolji izbor za predviđanje duljine latice na temelju duljine čašnog lista.
# Odgovor: Različite vrste cvijeta imaju različite odnose između duljine latice i čašnog lista.
#           Stoga bi bilo bolje trenirati različite modele za svaku vrstu cvijeta.
```


3. Istrenirajte model postupne linearne regresije gdje je duljina čašnog lista ciljna varijabla, a sve druge varijable potencijalni prediktori (uključujući i varijablu vrste). Usporedite sažetke modela između modela iz 2) i konačnog postupnog modela. Koji model preferirate i zašto?

```r
# Istrenirajte model postupne linearne regresije gdje je duljina čašnog lista ciljna varijabla, a sve druge varijable potencijalni prediktori (uključujući i varijablu vrste). Usporedite sažetke modela između modela iz 2) i konačnog postupnog modela. Koji model preferirate i zašto?

iris_sve <- lm(Sepal.Length ~ ., data = iris)
iris_prazno <- lm(Sepal.Length ~ 1, data = iris)

iris_lm1 <- stepAIC(iris_sve, direction="backward", trace = 0)
iris_lm2 <- stepAIC(iris_prazno, scope = list(upper = iris_sve, lower = iris_prazno), 
                direction="forward", trace = 0)

summary(iris_lm1)
summary(iris_lm2)
```

```
## 
## Call:
## lm(formula = Sepal.Length ~ Sepal.Width + Petal.Length + Petal.Width + 
##     Species, data = iris)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.79424 -0.21874  0.00899  0.20255  0.73103 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)        2.17127    0.27979   7.760 1.43e-12 ***
## Sepal.Width        0.49589    0.08607   5.761 4.87e-08 ***
## Petal.Length       0.82924    0.06853  12.101  < 2e-16 ***
## Petal.Width       -0.31516    0.15120  -2.084  0.03889 *  
## Speciesversicolor -0.72356    0.24017  -3.013  0.00306 ** 
## Speciesvirginica  -1.02350    0.33373  -3.067  0.00258 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.3068 on 144 degrees of freedom
## Multiple R-squared:  0.8673,	Adjusted R-squared:  0.8627 
## F-statistic: 188.3 on 5 and 144 DF,  p-value: < 2.2e-16
## 
## 
## Call:
## lm(formula = Sepal.Length ~ Petal.Length + Sepal.Width + Species + 
##     Petal.Width, data = iris)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.79424 -0.21874  0.00899  0.20255  0.73103 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)        2.17127    0.27979   7.760 1.43e-12 ***
## Petal.Length       0.82924    0.06853  12.101  < 2e-16 ***
## Sepal.Width        0.49589    0.08607   5.761 4.87e-08 ***
## Speciesversicolor -0.72356    0.24017  -3.013  0.00306 ** 
## Speciesvirginica  -1.02350    0.33373  -3.067  0.00258 ** 
## Petal.Width       -0.31516    0.15120  -2.084  0.03889 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.3068 on 144 degrees of freedom
## Multiple R-squared:  0.8673,	Adjusted R-squared:  0.8627 
## F-statistic: 188.3 on 5 and 144 DF,  p-value: < 2.2e-16
```



