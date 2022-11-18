# Análise Exploratória de Dados da Netflix 

Esta análise tem como objetivo inicial documentar o processo de AED, assim como conhecer a base de dados que estamos trabalhando, tratar e gerar vizualizações que facilitem a compreenção dos dados os quais estamos utilizando.

Informações Iniciais sobre o dataset: Trata-se de uma base de dados com informações dos conteúdos da Netflix que frequentaram o Top 10 em determinado período, iremos conhecer e colher mais informações ao longo do processo.


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

A coluna consta apenas o valor "Yes" enquanto todo o resto é nulo.
