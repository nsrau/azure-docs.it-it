---
title: Distribuire ed eseguire stime con ONNX nell'anteprima del database SQL Edge
description: Informazioni su come eseguire il training di un modello, convertirlo in ONNX, distribuirlo nell'anteprima perimetrale del database SQL di Azure e quindi eseguire stime native sui dati usando il modello ONNX caricato.
keywords: distribuire Edge database SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/04/2019
ms.openlocfilehash: 37fc04919b844d1edf87be62a587c34de4a8c4d5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692332"
---
# <a name="deploy-and-make-predictions-with-an-onnx-model-in-sql-database-edge-preview"></a>Distribuire ed eseguire stime con un modello ONNX in anteprima Edge del database SQL

In questa Guida introduttiva si apprenderà come eseguire il training di un modello, convertirlo in ONNX, distribuirlo nell'anteprima Edge del database SQL di Azure e quindi eseguire stime native sui dati usando il modello ONNX caricato. Per altre informazioni, vedere [Machine Learning e intelligenza artificiale con ONNX in SQL database Edge Preview](onnx-overview.md).

Questa Guida introduttiva si basa su **Scikit-learn** e usa il [set di dati di Boston Housing](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_boston.html).

## <a name="before-you-begin"></a>Prima di iniziare

* Se non è stato distribuito un modulo perimetrale del database SQL di Azure, seguire i passaggi per [distribuire l'anteprima di Edge del database SQL usando il portale di Azure](deploy-portal.md).

* Installare [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download).

* Aprire Azure Data Studio e seguire questa procedura per installare i pacchetti necessari per questa Guida introduttiva:

    1. Aprire il [nuovo notebook](https://docs.microsoft.com/sql/azure-data-studio/sql-notebooks) connesso al kernel Python 3. 
    1. Fare clic su **Gestisci pacchetti** e in **Aggiungi nuovo**cercare **sklearn**e installare il pacchetto Scikit-learn. 
    1. Installare anche i pacchetti **onnxmltools**, **onnxruntime**, **skl2onnx**e **SQLAlchemy** .
    
* Per ogni parte di script riportata di seguito, immetterla in una cella del notebook Azure Data Studio ed eseguire la cella.

## <a name="train-a-pipeline"></a>Eseguire il training di una pipeline

Suddividere il set di dati per utilizzare le funzionalità per stimare il valore mediano di una casa.

```python
import numpy as np
import onnxmltools
import onnxruntime as rt
import pandas as pd
import skl2onnx
import sklearn
import sklearn.datasets

from sklearn.datasets import load_boston
boston = load_boston()
boston

df = pd.DataFrame(data=np.c_[boston['data'], boston['target']], columns=boston['feature_names'].tolist() + ['MEDV'])

# x contains all predictors (features)
x = df.drop(['MEDV'], axis = 1)

# y is what we are trying to predict - the median value
y = df.iloc[:,-1]


# Split the data frame into features and target
x_train = df.drop(['MEDV'], axis = 1)
y_train = df.iloc[:,-1]

print("\n*** Training dataset x\n")
print(x_train.head())

print("\n*** Training dataset y\n")
print(y_train.head())
```

**Output**:

```text
*** Training dataset x

        CRIM    ZN  INDUS  CHAS    NOX     RM   AGE     DIS  RAD    TAX  \
0  0.00632  18.0   2.31   0.0  0.538  6.575  65.2  4.0900  1.0  296.0
1  0.02731   0.0   7.07   0.0  0.469  6.421  78.9  4.9671  2.0  242.0
2  0.02729   0.0   7.07   0.0  0.469  7.185  61.1  4.9671  2.0  242.0
3  0.03237   0.0   2.18   0.0  0.458  6.998  45.8  6.0622  3.0  222.0
4  0.06905   0.0   2.18   0.0  0.458  7.147  54.2  6.0622  3.0  222.0

    PTRATIO       B  LSTAT  
0     15.3  396.90   4.98  
1     17.8  396.90   9.14  
2     17.8  392.83   4.03  
3     18.7  394.63   2.94  
4     18.7  396.90   5.33  

*** Training dataset y

0    24.0
1    21.6
2    34.7
3    33.4
4    36.2
Name: MEDV, dtype: float64
```

Creare una pipeline per eseguire il training del modello LinearRegression. È anche possibile usare altri modelli di regressione.

```python
from sklearn.compose import ColumnTransformer
from sklearn.linear_model import LinearRegression
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import RobustScaler

continuous_transformer = Pipeline(steps=[('scaler', RobustScaler())])

# All columns are numeric - normalize them
preprocessor = ColumnTransformer(
    transformers=[
        ('continuous', continuous_transformer, [i for i in range(len(x_train.columns))])])

model = Pipeline(
    steps=[
        ('preprocessor', preprocessor),
        ('regressor', LinearRegression())])

# Train the model
model.fit(x_train, y_train)
```

Verificare l'accuratezza del modello, quindi calcolare il Punteggio R2 e l'errore quadratico medio.

```python
# Score the model
from sklearn.metrics import r2_score, mean_squared_error
y_pred = model.predict(x_train)
sklearn_r2_score = r2_score(y_train, y_pred)
sklearn_mse = mean_squared_error(y_train, y_pred)
print('*** Scikit-learn r2 score: {}'.format(sklearn_r2_score))
print('*** Scikit-learn MSE: {}'.format(sklearn_mse))
```

**Output**:

```text
*** Scikit-learn r2 score: 0.7406426641094094
*** Scikit-learn MSE: 21.894831181729206
```

## <a name="convert-the-model-to-onnx"></a>Converte il modello in ONNX

Convertire i tipi di dati in tipi di dati SQL supportati. Questa conversione sarà necessaria anche per altri dataframe.

```python
from skl2onnx.common.data_types import FloatTensorType, Int64TensorType, DoubleTensorType

def convert_dataframe_schema(df, drop=None, batch_axis=False):
    inputs = []
    nrows = None if batch_axis else 1
    for k, v in zip(df.columns, df.dtypes):
        if drop is not None and k in drop:
            continue
        if v == 'int64':
            t = Int64TensorType([nrows, 1])
        elif v == 'float32':
            t = FloatTensorType([nrows, 1])
        elif v == 'float64':
            t = DoubleTensorType([nrows, 1])
        else:
            raise Exception("Bad type")
        inputs.append((k, t))
    return inputs
```

Utilizzando `skl2onnx`, convertire il modello LinearRegression nel formato ONNX e salvarlo localmente.

```python
# Convert the scikit model to onnx format
onnx_model = skl2onnx.convert_sklearn(model, 'Boston Data', convert_dataframe_schema(x_train))
# Save the onnx model locally
onnx_model_path = 'boston1.model.onnx'
onnxmltools.utils.save_model(onnx_model, onnx_model_path)
```

## <a name="test-the-onnx-model"></a>Testare il modello ONNX

Dopo la conversione del modello nel formato ONNX, assegnare un punteggio al modello in modo da visualizzare una riduzione delle prestazioni.

> [!NOTE]
> Il runtime di ONNX USA float anziché Double, quindi sono possibili discrepanze ridotte.

```python
import onnxruntime as rt
sess = rt.InferenceSession(onnx_model_path)

y_pred = np.full(shape=(len(x_train)), fill_value=np.nan)

for i in range(len(x_train)):
    inputs = {}
    for j in range(len(x_train.columns)):
        inputs[x_train.columns[j]] = np.full(shape=(1,1), fill_value=x_train.iloc[i,j])

    sess_pred = sess.run(None, inputs)
    y_pred[i] = sess_pred[0][0][0]

onnx_r2_score = r2_score(y_train, y_pred)
onnx_mse = mean_squared_error(y_train, y_pred)

print()
print('*** Onnx r2 score: {}'.format(onnx_r2_score))
print('*** Onnx MSE: {}\n'.format(onnx_mse))
print('R2 Scores are equal' if sklearn_r2_score == onnx_r2_score else 'Difference in R2 scores: {}'.format(abs(sklearn_r2_score - onnx_r2_score)))
print('MSE are equal' if sklearn_mse == onnx_mse else 'Difference in MSE scores: {}'.format(abs(sklearn_mse - onnx_mse)))
print()
```

**Output**:

```text
*** Onnx r2 score: 0.7406426691136831
*** Onnx MSE: 21.894830759270633

R2 Scores are equal
MSE are equal
```

## <a name="insert-the-onnx-model"></a>Inserire il modello ONNX

Archiviare il modello in Edge del database SQL di Azure, in una tabella `models` in un `onnx`di database. Nella stringa di connessione specificare l' **indirizzo del server**, il **nome utente**e la **password**.

```python
import pyodbc

server = '' # SQL Server IP address
username = '' # SQL Server username
password = '' # SQL Server password

# Connect to the master DB to create the new onnx database
connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=master;UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(connection_string, autocommit=True)
cursor = conn.cursor()

database = 'onnx'
query = 'DROP DATABASE IF EXISTS ' + database
cursor.execute(query)
conn.commit()

# Create onnx database
query = 'CREATE DATABASE ' + database
cursor.execute(query)
conn.commit()

# Connect to onnx database
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string, autocommit=True)
cursor = conn.cursor()

table_name = 'models'

# Drop the table if it exists
query = f'drop table if exists {table_name}'
cursor.execute(query)
conn.commit()

# Create the model table
query = f'create table {table_name} ( ' \
    f'[id] [int] IDENTITY(1,1) NOT NULL, ' \
    f'[data] [varbinary](max) NULL, ' \
    f'[description] varchar(1000))'
cursor.execute(query)
conn.commit()

# Insert the ONNX model into the models table
query = f"insert into {table_name} ([description], [data]) values ('Onnx Model',?)"

model_bits = onnx_model.SerializeToString()

insert_params  = (pyodbc.Binary(model_bits))
cursor.execute(query, insert_params)
conn.commit()
```

## <a name="load-the-data"></a>Caricare i dati

Caricare i dati nel database SQL di Azure Edge.

Per prima cosa, creare due tabelle, **funzionalità** e **destinazione**, per archiviare subset del set di dati della sede di Boston.

* **Funzionalità** contiene tutti i dati utilizzati per stimare il valore mediano di destinazione. 
* **Target** contiene il valore mediano per ogni record nel set di dati. 

```python
import sqlalchemy
from sqlalchemy import create_engine
import urllib

db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string)
cursor = conn.cursor()

features_table_name = 'features'

# Drop the table if it exists
query = f'drop table if exists {features_table_name}'
cursor.execute(query)
conn.commit()

# Create the features table
query = \
    f'create table {features_table_name} ( ' \
    f'    [CRIM] float, ' \
    f'    [ZN] float, ' \
    f'    [INDUS] float, ' \
    f'    [CHAS] float, ' \
    f'    [NOX] float, ' \
    f'    [RM] float, ' \
    f'    [AGE] float, ' \
    f'    [DIS] float, ' \
    f'    [RAD] float, ' \
    f'    [TAX] float, ' \
    f'    [PTRATIO] float, ' \
    f'    [B] float, ' \
    f'    [LSTAT] float, ' \
    f'    [id] int)'

cursor.execute(query)
conn.commit()

target_table_name = 'target'

# Create the target table
query = \
    f'create table {target_table_name} ( ' \
    f'    [MEDV] float, ' \
    f'    [id] int)'

x_train['id'] = range(1, len(x_train)+1)
y_train['id'] = range(1, len(y_train)+1)

print(x_train.head())
print(y_train.head())
```

Usare infine `sqlalchemy` per inserire i dataframe di `x_train` e `y_train` Pandas nelle tabelle `features` e `target`rispettivamente. 

```python
db_connection_string = 'mssql+pyodbc://' + username + ':' + password + '@' + server + '/' + database + '?driver=ODBC+Driver+17+for+SQL+Server'
sql_engine = sqlalchemy.create_engine(db_connection_string)
x_train.to_sql(features_table_name, sql_engine, if_exists='append', index=False)
y_train.to_sql(target_table_name, sql_engine, if_exists='append', index=False)
```

A questo punto è possibile visualizzare i dati nel database.

## <a name="run-predict-using-the-onnx-model"></a>Eseguire l'istruzione PREDICT usando il modello ONNX

Con il modello nel database SQL di Azure Edge, eseguire stime native sui dati usando il modello ONNX caricato.

> [!NOTE]
> Modificare il kernel del notebook in SQL per eseguire la cella rimanente.

```sql
USE onnx

DECLARE @model VARBINARY(max) = (
        SELECT DATA
        FROM dbo.models
        WHERE id = 1
        );

WITH predict_input
AS (
    SELECT TOP (1000) [id]
        , CRIM
        , ZN
        , INDUS
        , CHAS
        , NOX
        , RM
        , AGE
        , DIS
        , RAD
        , TAX
        , PTRATIO
        , B
        , LSTAT
    FROM [onnx].[dbo].[features]
    )
SELECT predict_input.id
    , p.variable1 AS MEDV
FROM PREDICT(MODEL = @model, DATA = predict_input) WITH (variable1 FLOAT) AS p
```

## <a name="next-steps"></a>Fasi successive

* [Machine Learning e AI con ONNX nel database perimetrale SQL](onnx-overview.md)
