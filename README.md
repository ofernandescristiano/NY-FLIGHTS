# NY-FLIGHTS

## Estudo e analises feitas para o dataset de voôs de NY

## IMPORTANDO PACOTES E ARQUIVO

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import scipy.stats as stats
from matplotlib import pyplot 
from google.colab import drive

drive.mount('/content/drive', force_remount= True)

df = pd.read_csv('/content/drive/MyDrive/pasta x/DAY 6/nyflights.csv')

In [25]: pd.set_option('display.float_format', lambda x: '%.3f' % x)

df.head()

type(df['dep_delay'][0])

## ETAPA 1 - Qual o comportamento médio e a variância dos dados dos voos que atrasam mais de duas horas?

### Filtrar a tabela com os registrados que atrasam mais de duas horas

#Criando coluna com voôs com atraso de saída de mais de 2 horas
df_atrasos = df[df['dep_delay'] > 120]
df_atrasos.head()

df_atrasos = pd.DataFrame(df_atrasos)

df_atrasos.head()

### Filtrar a coluna 'dep_delay'  e aplicar as funções describe() e .T


#Estatisticas descritivas do dataset
df_atrasos.describe()

#Transpondo o dataframe
df_transp = df_atrasos.describe().T

### Adicionar uma coluna para a amplitude


df_transp['range'] = df_transp['max'] - df_transp['min']

df_transp

### Adicionar uma coluna para a variância

df_transp['var'] = pow(df_transp['std'], 2)

df_transp

 ### Plotar um histograma e um boxplot com os dados da coluna 'dep_delay'

fig = plt.figure(figsize= [8,5])

sns.histplot(data=df_atrasos, x=df_atrasos['dep_delay'], bins = 20)

plt.title('Histograma dep_delay')
plt.ylabel('')

fig = plt.figure(figsize= [8,5])

sns.boxplot(data=df_atrasos,x=df_atrasos['dep_delay'])

plt.title('Boxplot dep_delay')
plt.ylabel('')

plt.show()

Com o histograma e o boxplot vemos muitos outliers e uma distribuição não normal

## ETAPA 2 - Quais destinos geraram voos com o maior número de atrasos na partida?



### Filtrar a tabela com os registrados que atrasam mais de 5 minutos

df_atrasos2 = df[df['dep_delay']>5]
df_atrasos2.head()

### Filtrar a coluna de destino, fazer uma contagem dos valores e apresentar os 5 últimos



df_5ultimos = pd.DataFrame(df_atrasos2['dest'].value_counts(ascending = True).head(5)).reset_index()

df_5ultimos.rename(columns = {'index':'Destino','dest':'Atrasos_num'}, inplace = True)

df_5ultimos

df_5atrasos = pd.DataFrame(df_atrasos2['dest'].value_counts().head(5)).reset_index()

df_5atrasos.rename(columns = {'index':'Destino','dest':'Atrasos_num'}, inplace = True)

### Filtrar a coluna de destino, fazer uma contagem dos valores e apresentar os 5 primeiros


df_5atrasos

### Plotar o gráfico dos valores agrupados por diferentes aeroportos  vs quantidade de atrasos

#Gráfico com 5 maiores atrasos
fig = plt.figure(figsize= [8,5])

sns.barplot(x= df_5atrasos['Destino'], y=df_5atrasos['Atrasos_num'])

plt.title('5 maiores atrasos')
plt.ylabel('')
plt.xlabel('Destino')

plt.show()

#Gráfico com 5 menores atrasos
fig = plt.figure(figsize= [8,5])

sns.barplot(x= df_5ultimos['Destino'], y=df_5ultimos['Atrasos_num'])

plt.title('5 menores atrasos')
plt.ylabel('')
plt.xlabel('Destino')

plt.show()

## ETAPA 3 - Quais são as velocidades médias dos voos nos 5 destinos que mais ocorrem atrasos

### Filtrar a tabela com os registrados que mais atrasam.

lista = ['ORD', 'ATL', 'LAX', 'MCO', 'SFO']
df_fim = df[df['dest'].isin(lista)]
df_fim.head()

### Calcular a velocidade média

df_fim['vmedia'] = df_fim['distance']/(df_fim['air_time']/60)

df_fim.head()

df_vmedia = df_fim[['dest','vmedia']].groupby('dest', as_index= False).mean()
df_vmedia

###  Plotar o boxplot para os quatro piores destinos



fig = plt.figure(figsize= [8,5])

ax = sns.boxplot(x=df_fim['dest'], y= df_fim['vmedia'], order = ('ATL', 'ORD', 'MCO', 'LAX'))

plt.title('Boxplot: 4 Piores Destinos')
plt.ylabel('')
plt.xlabel('Destino')

plt.show()
