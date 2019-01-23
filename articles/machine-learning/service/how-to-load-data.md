---
title: 'Caricamento: Data Prep SDK per Python'
titleSuffix: Azure Machine Learning service
description: Informazioni sul caricamento dei dati con Azure Machine Learning Data Prep SDK. È possibile caricare diversi tipi di dati di input, specificare i parametri e i tipi di file di dati oppure usare la funzionalità di lettura smart SDK per rilevare automaticamente il tipo di file.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 2478a5dd3f5d685253ef9145bec0a68ff324c6c3
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263816"
---
# <a name="load-and-read-data-with-azure-machine-learning"></a>Caricare e leggere dati con Azure Machine Learning

Questo articolo illustra i diversi metodi di caricamento dei dati con [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk). L'SDK supporta più funzionalità di inserimento dati, tra cui:

* Caricamento da numerosi tipi di file con inferenza dei parametri di analisi (codifica, separatore, intestazioni)
* Conversione del tipo mediante l'inferenza durante il caricamento dei file
* Supporto delle connessioni per Microsoft SQL Server e Azure Data Lake Storage

## <a name="load-data-automatically"></a>Caricare automaticamente i dati

Per caricare automaticamente i dati senza specificare il tipo di file, usare la funzione `auto_read_file()`. Il tipo di file e gli argomenti richiesti per leggerlo vengono dedotti automaticamente.

```python
import azureml.dataprep as dprep

dataflow = dprep.auto_read_file(path='./data/any-file.txt')
```

Questa funzione è utile per rilevare automaticamente il tipo di file, la codifica e altri argomenti di analisi, tutto da un unico comodo punto di ingresso. La funzione, inoltre, esegue automaticamente i seguenti passaggi comunemente eseguiti quando si caricano dati delimitati:

* Derivare e impostare il delimitatore
* Ignorare le righe vuote nella parte superiore del file
* Derivare e impostare la riga di intestazione

In alternativa, se si conosce il tipo di file è possibile digitare anticipatamente e si vuole controllare in modo esplicito la modalità con cui viene analizzato, proseguire a leggere questo articolo per visualizzare le funzioni specializzate offerte da SDK.

## <a name="load-text-line-data"></a>Caricamento di dati di righe di testo

Per leggere dati di testo semplice in un flusso di dati, usare `read_lines()` senza specificare parametri facoltativi.

```python
dataflow = dprep.read_lines(path='./data/text_lines.txt')
dataflow.head(5)
```

||Grafico a linee|
|----|-----|
|0|Data \| \|  Temperatura minima \| \|  Temperatura massima|
|1|01-07-2015 \|\|  -4,1 \|\|  10,0|
|2|02-07-2015 \|\|  -0,8 \|\|  10,8|
|3|03-07-2015 \|\|  -7,0 \|\|  10,5|
|4|04-07-2015 \|\|  -5,5 \|\|  9,3|

Dopo l'inserimento dei dati, eseguire il codice seguente per convertire l'oggetto flusso di dati in un dataframe Pandas.

```python
pandas_df = dataflow.to_pandas_dataframe()
```

## <a name="load-csv-data"></a>Caricare dati CSV

Durante la lettura di file delimitati, il runtime sottostante può dedurre i parametri di analisi, ad esempio il separatore, la codifica, l'uso o meno di intestazioni e così via. Eseguire il codice seguente per tentare di leggere un file specificandone solo la posizione.

```python
# SAS expires June 16th, 2019
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0||stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Contea Hale|10171002158| |
|2|ALABAMA|1|101710|Contea Hale|10171002162| |
|3|ALABAMA|1|101710|Contea Hale|10171002156| |
|4|ALABAMA|1|101710|Contea Hale|10171000588|2|

Per escludere righe durante il caricamento, definire il parametro `skip_rows`. Questo parametro non caricherà le righe specificate nel file CSV (usando un indice in base uno).

```python
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Contea Hale|10171002158|29|
|1|ALABAMA|1|101710|Contea Hale|10171002162|40 |
|2|ALABAMA|1|101710|Contea Hale|10171002156| 43|
|3|ALABAMA|1|101710|Contea Hale|10171000588|2|
|4|ALABAMA|1|101710|Contea Hale|10171000589|23 |

Eseguire il codice seguente per visualizzare i tipi di dati delle colonne.

```python
dataflow.head(1).dtypes

stnam                     object
fipst                     object
leaid                     object
leanm10                   object
ncessch                   object
schnam10                  object
MAM_MTH00numvalid_1011    object
dtype: object
```

Per impostazione predefinita, Azure Machine Learning Data Prep SDK non modifica il tipo di dati. L'origine dati da cui si sta leggendo è un file di testo, pertanto l'SDK legge tutti i valori come stringhe. Per questo esempio le colonne numeriche devono essere analizzate come numeri. Impostare il parametro `inference_arguments` su `InferenceArguments.current_culture()` per dedurre e convertire automaticamente i tipi di colonna durante la lettura del file.

```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dataflow.head(1).dtypes

stnam                      object
fipst                     float64
leaid                     float64
leanm10                    object
ncessch                   float64
schnam10                   object
ALL_MTH00numvalid_1011    float64
dtype: object
```

Molte delle colonne sono state rilevate correttamente come numeriche e il relativo tipo è impostato su `float64`.

## <a name="use-excel-data"></a>Usare dati Excel

L'SDK include una funzione `read_excel()` per il caricamento dei file di Excel. Per impostazione predefinita, la funzione carica il primo foglio della cartella di lavoro. Per definire un foglio specifico da caricare, definire il parametro `sheet_name` con il valore di stringa del nome del foglio.

```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dataflow.head(5)
```

||Colonna1|Colonna2|Colonna3|Colonna4|Colonna5|Colonna6|Colonna7|Colonna8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|
|1|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|
|2|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|Nessuna|
|3|RANK|Title|Studio|In tutto il mondo|Nazionale / %|Colonna1|Oltremare / %|Colonna2|Anno^|
|4|1|Avatar|Fox|2788|760,5|0,273|2027,5|0,727|2009^|5|

L'output mostra che i dati nel secondo foglio contenevano tre righe vuote prima delle intestazioni. La funzione `read_excel()` contiene parametri facoltativi che indicano di ignorare le righe e usare le intestazioni. Eseguire il codice seguente per ignorare le prime tre righe e usare la quarta riga come riga di intestazioni.

```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_column_headers=True, skip_rows=3)
```

||RANK|Title|Studio|In tutto il mondo|Nazionale / %|Colonna1|Oltremare / %|Colonna2|Anno^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Avatar|Fox|2788|760,5|0,273|2027,5|0,727|2009^|
|1|2|Titanic|Par.|2186,8|658,7|0,301|1528,1|0,699|1997^|
|2|3|Gli Avengers di Marvel|BV|1518,6|623,4|0,41|895,2|0,59|2012|
|3|4|Harry Potter e i Doni della Morte parte 2|WB|1341,5|381|0,284|960,5|0,716|2011|
|4|5|Frozen|BV|1274,2|400,7|0,314|873,5|0,686|2013|

## <a name="load-fixed-width-data-files"></a>Caricare file di dati a larghezza fissa

Per caricare file di dati a larghezza fissa occorre specificare un elenco di offset di caratteri. Si presuppone sempre che la prima colonna inizi all'offset zero.

```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dataflow.head(5)
```

||010000|99999|BOGUS NORWAY|NO|NO_1|ENRS|Colonna7|Colonna8|Colonna9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|BOGUS NORWAY|NO|NO|ENSO||||
|1|010010|99999|JAN MAYEN|NO|JN|ENJA|+70933|-008667|+00090|
|2|010013|99999|ROST|NO|NO|||||
|3|010014|99999|SOERSTOKKEN|NO|NO|ENSO|+59783|+005350|+00500|
|4|010015|99999|BRINGELAND|NO|NO|ENBL|+61383|+005867|+03270|

Per evitare il rilevamento delle intestazioni e analizzare i dati corretti, passare `PromoteHeadersMode.NONE` al parametro `header`.

```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)
```

||Colonna1|Colonna2|Colonna3|Colonna4|Colonna5|Colonna6|Colonna7|Colonna8|Colonna9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|BOGUS NORWAY|NO|NO_1|ENRS|Colonna7|Colonna8|Colonna9|
|1|010003|99999|BOGUS NORWAY|NO|NO|ENSO||||
|2|010010|99999|JAN MAYEN|NO|JN|ENJA|+70933|-008667|+00090|
|3|010013|99999|ROST|NO|NO|||||
|4|010014|99999|SOERSTOKKEN|NO|NO|ENSO|+59783|+005350|+00500|
|5|010015|99999|BRINGELAND|NO|NO|ENBL|+61383|+005867|+03270|

## <a name="load-sql-data"></a>Caricare dati SQL

L'SDK può caricare anche i dati di un'origine SQL. Attualmente, è supportato solo Microsoft SQL Server. Per leggere i dati da un server SQL, creare un oggetto `MSSQLDataSource` che contiene i parametri di connessione. Il parametro della password di `MSSQLDataSource` accetta un oggetto `Secret`. È possibile compilare un oggetto segreto in due modi:

* Registrare il segreto e il relativo valore con il motore di esecuzione.
* Creare il segreto con solo un `id` (se il valore del segreto è già registrato nell'ambiente di esecuzione) usando `dprep.create_secret("[SECRET-ID]")`.

```python
secret = dprep.register_secret(value="[SECRET-PASSWORD]", id="[SECRET-ID]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=secret)
```

Dopo aver creato un oggetto origine dati, è possibile leggere i dati dall'output della query.

```python
dataflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dataflow.head(5)
```

||ProductID|NOME|ProductNumber|Colore|StandardCost|ListPrice|Dimensione|Peso|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|rowguid|ModifiedDate|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|0|680|HL Road Frame - Nero, 58|FR-R92B-58|Nero|1059,3100|1431,50|58|1016,04|18|6|01-06-2002 00:00:00+00:00|Nessuna|Nessuna|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461F-9069-55309d90ea7e|11-03-2008 |0:01:36.827000 + 00:00|
|1|706|HL Road Frame - Rosso, 58|FR-R92R-58|Rosso|1059,3100|1431,50|58|1016,04|18|6|01-06-2002 00:00:00+00:00|Nessuna|Nessuna|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|11-03-2008 |10:01:36.827000 + 00:00|
|2|707|Sport-100 Casco, Rosso|HL-U509-R|Rosso|13,0863|34,99|Nessuna|Nessuna|35|33|01-07-2005 00:00:00+00:00|Nessuna|Nessuna|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|11-03-2008 |10:01:36.827000 + 00:00|
|3|708|Sport-100 Casco, Nero|HL U509|Nero|13,0863|34,99|Nessuna|Nessuna|35|33|01-07-2005 00:00:00+00:00|Nessuna|Nessuna|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|a25a44fb-c2de-4268-958f-110b8d7621e2|11-03-2008 |10:01:36.827000 + 00:00|
|4|709|Calzini da mountain bike, M|SO-B909-M|Bianco|3,3963|9,50|M|Nessuna|27|18|01-07-2005 00:00:00+00:00|30-06-2006 00:00:00+00:00|Nessuna|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|18f95f47-1540-4e02-8F1F-cc1bcb6828d0|11-03-2008 |10:01:36.827000 + 00:00|

## <a name="use-azure-data-lake-storage"></a>Usare Azure Data Lake Storage

Esistono due modi in cui l'SDK può acquisire il token OAuth necessario per accedere ad Azure Data Lake Storage:

* Recuperare il token di accesso da una sessione recente dell'account di accesso dell'utente all'interfaccia della riga di comando di Azure
* Usare un'entità servizio (SP) e un certificato come segreto

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>Usare un token di accesso da una sessione recente dell'interfaccia della riga di comando di Azure

Nel computer locale eseguire il comando seguente.

```azurecli
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
```

> [!NOTE]
> Se l'account utente è membro di più di un tenant di Azure, è necessario specificare il tenant nel formato di nome host AAD URL.

### <a name="create-a-service-principal-with-the-azure-cli"></a>Creare un'entità servizio con l'interfaccia della riga di comando di Azure

Usare l'interfaccia della riga di comando di Azure per creare un'entità servizio e il certificato corrispondente. Questa entità servizio specifica è configurata con il ruolo `reader`, con il relativo ambito ridotto al solo account di Azure Data Lake Storage "dpreptestfiles".

```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```

Questo comando genera l'`appId` e il percorso che porta al file di certificato (in genere nella cartella home). Il file con estensione .crt contiene sia il certificato pubblico sia la chiave privata nel formato PEM.

```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Per configurare l'ACL per il file system di Azure Data Lake Storage, usare il valore objectId dell'utente. In questo esempio viene invece usata l'entità servizio.

```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Per configurare gli accessi `Read` e `Execute` per il file system di Azure Data Lake Storage, configurare singolarmente l'ACL per file e cartelle. Questo perché il modello di ACL HDFS sottostante non supporta l'ereditarietà.

```azurecli
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```

```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```

### <a name="acquire-an-oauth-access-token"></a>Acquisire un token di accesso OAuth

Usare il pacchetto `adal` (`pip install adal`) per creare un contesto di autenticazione sul tenant MSFT e acquisire un token di accesso OAuth. Per Azure Data Lake Storage, la risorsa nella richiesta del token deve essere per 'https://datalake.azure.net', che è diversa dalla maggior parte delle altre risorse di Azure.

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dataflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dataflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Website|street|city|Contea|
|----|------|-----|----|----|----|----|
|0|1012063|Caledonia Farmers Market Association - Danville|https://sites.google.com/site/caledoniafarmers... ||Danville|Caledonia|
|1|1011871|Stearns Homestead Farmers' Market|http://Stearnshomestead.com |6975 Ridge Road|Parma|Cuyahoga|
|2|1011878|100 Mile Market|http://www.pfcmarkets.com |507 Harrison St|Kalamazoo|Kalamazoo|
|3|1009364|106 S. Main Street Farmers Market|http://thetownofsixmile.wordpress.com/ |106 S. Main Street|Six Mile|||
|4|1010691|10th Street Community Farmers Market|https://agrimissouri.com/... |10th Street e Poplar|Lamar|Barton|
