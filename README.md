
<div align="justify">

<h1>A criação de boas variáveis em Séries Temporais</h1> <h2>Previsão de Consumo (Dataset us_change)</h2>

<p align="justify"><h3>1. Visão Geral</h3></p>

<p align="justify">Este projeto tem como objetivo prever a variável <b>Consumption</b> a partir de uma série temporal econômica trimestral. A abordagem adotada transforma o problema em regressão supervisionada, utilizando engenharia de features baseada em séries temporais para capturar a dinâmica intrínseca dos dados e permitir o uso de algoritmos de aprendizado de máquina tradicionais.</p>

<p align="justify"><h3>2. Características da Série</h3></p>

<p align="justify">A série apresenta duas propriedades fundamentais que guiaram a estratégia de modelagem: <ul> <li><b>Série curta:</b> O número limitado de observações reduz a capacidade de generalização de modelos excessivamente complexos e favorece modelos lineares ou com poucos parâmetros.</li> <li><b>Comportamento aproximadamente linear:</b> Apresenta baixa complexidade estrutural e uma forte dependência de seus próprios valores passados (autocorrelação).</li> </ul> </p>

<p align="justify"><h3>3. Engenharia de Features</h3></p>

<p align="justify">A performance do modelo foi fortemente impactada pela criação de variáveis explicativas. Abaixo, detalho as implementações utilizadas no projeto:</p>

<p align="justify"><b>Lags (Memória Temporal):</b> Capturam a dependência temporal direta através do deslocamento da variável alvo.</p>

```
Python

for lag in range(1, 13):
    data[f'Consumption_lag{lag}'] = data['Consumption'].shift(lag)
```

<p align="justify"><b>Diferenças (Dinâmica):</b> Modelam as variações entre períodos em vez dos níveis absolutos, auxiliando na captura da velocidade de mudança.</p>

```
Python

data['diff_1'] = data['Consumption'].diff(1).shift(1)
```
<p align="justify"><b>Estatísticas Móveis:</b> Capturam a tendência local e a volatilidade recente por meio de janelas deslizantes.</p>

```
Python

data['rolling_mean_4'] = data['Consumption'].shift(1).rolling(window=4).mean()
```
<p align="justify"><b>Tendência Global:</b> Um indexador numérico que auxilia o modelo a capturar o crescimento estrutural ao longo do tempo.</p>

```
Python

data['trend'] = np.arange(len(data))
```
<p align="justify"><b>Sazonalidade Cíclica:</b> Representação contínua dos trimestres para garantir que a transição entre ciclos seja suave para o modelo.</p>

```
Python

data['sin_q'] = np.sin(2 * np.pi * data.index.quarter / 4)
data['cos_q'] = np.cos(2 * np.pi * data.index.quarter / 4)
```
<p align="justify"><i>Nota: Todas as features utilizam o método <b>.shift(1)</b> para evitar <b>Data Leakage</b>, garantindo que apenas informações passadas sejam usadas para prever o futuro.</i></p>

<p align="justify"><h3>4. Resultados Comparativos</h3></p>

<table align="center"> <thead> <tr> <th>Modelo</th> <th>MSE (Mean Squared Error)</th> </tr> </thead> <tbody> <tr><td><b>Combinação(Regressao+Xgboost+RandomForest)</b></td><td><b>0.06</b></td></tr> <tr><td>Reg Linear</td><td>0.07</td></tr> <tr><td>Reg. Ridge</td><td>0.07</td></tr> <tr><td>XGBoost</td><td>0.08</td></tr> <tr><td>Random Forest</td><td>0.11</td></tr> <tr><td>ARIMA</td><td>0.25</td></tr> <tr><td>Chronos </td><td>0.43</td></tr> </tbody> </table>

<p align="justify"><h3>5. Conclusões</h3></p>

<p align="justify">A principal conclusão deste estudo é que a <b>simplicidade superou a complexidade</b>. Modelos lineares bem fundamentados em engenharia de features foram superiores a modelos de deep learning ou boosting puro. O ganho obtido com o <b>Combinação(regressao+Xgboost+RandomForest)</b> foi real, confirmando que a combinação de estimadores pode estabilizar as previsões e reduzir o erro em séries curtas onde a variância costuma ser alta.</p>

<p align="center">


<b>"Modelos não resolvem o problema — dados e features resolvem."</b> </p>

</div>


