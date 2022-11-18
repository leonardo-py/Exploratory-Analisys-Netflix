# Análise Exploratória de Dados da Netflix 

Esta análise tem como objetivo inicial documentar o processo de AED, assim como conhecer a base de dados que estamos trabalhando, tratar e gerar vizualizações que facilitem a compreenção dos dados os quais estamos utilizando.

Informações Iniciais sobre o dataset: Trata-se de uma base de dados com informações dos conteúdos da Netflix que frequentaram o Top 10 nos Estados Unidos em determinado período, iremos conhecer e colher mais informações ao longo do processo.

dataset retirado do Kaggle: <https://www.kaggle.com/datasets/prasertk/netflix-daily-top-10-in-us>


Vamos começar importando a base de dados com a biblioteca Pandas, que será utilizada ao longo de toda análise.

```
import pandas as pd
base = pd.read_csv('netflix daily top 10.csv')
```
Vamos gerar uma visualização inicial da nossa base, do seu início e seu final 
```
base.head()
```
![head](https://user-images.githubusercontent.com/68862907/202734032-eb523631-2563-4bc1-8306-74fbf00212b4.PNG)
```
base.tail()
```
![tail](https://user-images.githubusercontent.com/68862907/202733420-6e99a0be-880a-413f-b9d8-b7bf865847cf.PNG)

A partir daqui já conseguimos conhecer um pouco a base, percebemos informações de ranking dos títulos em determinadas datas, score de visualização, quantidade de dias consecutivos no top 10, categoria do conteúdo (TV Show ou Movie), se é exclusivo da Netflix e a data que a plataforma disponibilizou o conteúdo.

Chama a atenção também a ocorrência de um possível problema com a base na coluna Netflix Exclusive onde vemos alguns NaNs. Para entender melhor o que esta ocorrendo iremos colher mais informações.
```
base.info()
```
![info](https://user-images.githubusercontent.com/68862907/202736399-233b66de-b16d-4ef2-ba1d-3400a4b52d10.PNG)

Daqui podemos tirar informações importantes como a quantidade de linhas não nulas da base por coluna, percebe-se a falta de diversos dados da coluna Netflix Exclusive, onde continuaremos a investigação.
```
base['Netflix Exclusive'].value_counts()
```
![value_counts](https://user-images.githubusercontent.com/68862907/202739115-6d82953d-b6b4-4a99-b511-437fbac6dcd3.PNG)
```
base['Netflix Exclusive'].isnull().sum()
```
![isnull](https://user-images.githubusercontent.com/68862907/202740769-1dd1b864-0b04-44b8-8585-4b469f008779.PNG)

A coluna consta apenas o valor "Yes" enquanto todo o resto é nulo. Percebemos que na verdade os valores nulos são títulos da Netflix que não são exclusivos, então faremos esse tratamento para corrigir o problema.
```
base[['Netflix Exclusive']] = base[['Netflix Exclusive']].fillna('No')
base.head()
```
![head tratada](https://user-images.githubusercontent.com/68862907/202744986-4e40c302-b3c5-48f8-a45f-6952f0648a12.PNG)

Todos os valores nulos foram substituídos por "No".
```
base.info()
```
![info edited](https://user-images.githubusercontent.com/68862907/202749597-75fa8545-ae1d-4797-8d53-b9afddb9985a.PNG)

Agora iremos connhecer em qual período de tempo essa base trabalha, porém como vemos acima, a coluna de data 'As of' não esta configurada como data e sim como objeto. Para contornarmos essa questão e conseguirmos descobrir qual a primeira e última data no dataset a própria biblioteca do Pandas já conta com um módulo de tratamento de data e hora.
```
inicio = pd.to_datetime(base['As of']).dt.date.min()
fim = pd.to_datetime(base['As of']).dt.date.max()
print(inicio)
print(fim)
```
![inicio_fim](https://user-images.githubusercontent.com/68862907/202750692-08e6e7c6-18ba-4bf0-a913-64971a2a9825.PNG)

Temos agora a informação que nossa base se inicia em Abril de 2020 até Março de 2022

Através da coluna de títulos podemos contar quantos conteúdos distintos estão presentes na base. Um total de 645 títulos
```
len(base['Title'].unique())
```
![645](https://user-images.githubusercontent.com/68862907/202781637-186f7454-85dd-429a-b2e7-2d096e4af71f.PNG)

A partir de agora daremos um breve tratamento estatístico para a base com intenção de conhece-la ainda mais. Para isso utilizaremos o método describe, que nos da uma série de informações sobre o dataset trabalhado.
```
base.describe()
```
![describe](https://user-images.githubusercontent.com/68862907/202754861-f7107542-ad10-40a0-862f-d236216c8338.PNG)

o método 'describe' nos fornece uma série de informações como contagem de valores, média, mediana, quartis, desvio padrão, valores mínimos e máximos.
Notamos um desvio padrão (std = standard deviation) elevado nas colunas 'Days In Top 10' e 'Viewership Score'

Utilizaremos também um box plot para entender melhor a distribuição desses dados de forma mais visual, onde rapidamente notaremos uma grande quantidade de pontos acima do limite máximo do box plot, configurando outliers. 
```
base.plot(kind = 'box', figsize = (10,6), subplots = True)
```
![boxplot](https://user-images.githubusercontent.com/68862907/202756661-673886bb-c3a5-4aa8-915f-01fea2a9c6e9.PNG)

Para tentar entender esses outliers vamos filtrar a base para que ela nos mostre apenas os títulos que estão a mais de 100 dias no top 10
```
base[base['Days In Top 10'] >= 100]
```
![top100](https://user-images.githubusercontent.com/68862907/202766426-59a9f1c7-3819-4e63-a215-649956052f31.PNG)

Como podemos observar, o título Cocomelon parece ser o responsável pelos outliers que observamos anteriormente. Vamos continuar a análise
```
base.Title.value_counts()
```
![value_counts title](https://user-images.githubusercontent.com/68862907/202767079-f1b4101d-ff2f-44e5-8c3d-c24be474b589.PNG)

Temos então uma resposta que pode soar inesperada paga alguns, o desenho infantil Cocomelon figura no Top 10 por muito mais tempo que os demais conteúdos durante o período de tempo em que a base nos fornece os dados.

Criaremos agora uma tabela mostrando os títulos que por mais tempo figuraram no Top 10. Para isso a base foi agrupada por título, feito o somatório das colunas que dispostas em ordem decrescente para termos o rankeamento, sendo a coluna escolhida para ordenar a base 'Days In Top 10'.
```
top_base = base.groupby(['Title']).sum().sort_values(by = 'Days In Top 10', ascending=False).head(10)
top_base
```
![top10](https://user-images.githubusercontent.com/68862907/202769213-3ba97608-09b0-4674-9100-4bbb45c568ec.PNG)

A partir de agora serão gerados gráficos que também fazem parte da biblioteca do Pandas para seguirmos analisando a base.

Primeiro um gráfico de barra dos Top 10 Títulos da base, com Cocomelon liderando com folga
```
base.Title.value_counts().head(10).plot(kind = 'bar', xlabel ='Títulos', ylabel = 'Contagem de valores')
```
![contagem_barra](https://user-images.githubusercontent.com/68862907/202774423-eb4006b9-f7f5-430e-9075-a5561033a14d.PNG)

Agora outro gráfico de barra com contagem de valores, porém por tipo de conteúdo
```
base.Type.value_counts().plot(kind='bar', xlabel = 'Tipo', ylabel = 'Contagem de Valores')
```
![tipo_barra](https://user-images.githubusercontent.com/68862907/202775213-2f7e3a7e-6ec8-46de-ab56-e2e7e0ab1adc.PNG)

Podemos perceber que as séries (TV Show) estiveram bem mais presente no Top 10 do que os Filmes (Movie), além disso os outros dois tipos de conteúdo pouco figuraram no Top 10 (Stand Up-Commedy e concertor musicais)
