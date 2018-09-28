---
title: Caricare dati con l'SDK di preparazione dati Azure Machine Learning - Python
description: Informazioni sul caricamento dei dati con l'SDK di preparazione dati Azure Machine Learning. È possibile caricare diversi tipi di dati di input, specificare i parametri e i tipi di file di dati oppure usare la funzionalità di lettura smart SDK per rilevare automaticamente il tipo di file.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 000870e3273799930f519ff32d6b072d8c2d1f10
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989686"
---
#<a name="load-data-with-the-azure-machine-learning-data-prep-sdk"></a>Caricare dati con l'SDK di preparazione dati Azure Machine Learning

L'[SDK di preparazione dati Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) consente di caricare diversi tipi di dati di input. È possibile specificare il tipo di file di dati e i relativi parametri oppure usare la funzionalità di lettura smart SDK per rilevare automaticamente il tipo di un file.

## <a name="read-lines"></a>Lettura delle linee
Uno dei modi più semplici per caricare i dati è la lettura degli stessi come linee di testo.

```
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

Dopo che i dati vengono inseriti, è possibile recuperare un dataframe di Pandas per il set di dati completo.

```
df = dataflow.to_pandas_dataframe()
df
```

||Grafico a linee|
|----|-----|
|0|Data\|\| Temperatura minima\|\| Temperatura massima|
|1|01-07-2015 \|\| 4,1\|\| 10,0|
|2|02-07-2015\|\| 0,8\|\| 10,8|
|3|03-07-2015\|\| 7,0\|\| 10,5|
|4|04-07-2015\|\| 5,5\|\| 9,3|

## <a name="read-csv"></a>Lettura di CSV
Durante la lettura di file delimitati, è possibile consentire che il runtime sottostante deduca i parametri di analisi (ad esempio, un separatore, la codifica, se si desidera usare le intestazioni e così via) piuttosto che fornirli. Per questo esempio, tentare di leggere un file specificando solo la sua posizione. 

```
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

Uno dei parametri che è possibile specificare è un numero di righe da ignorare nei file di cui si esegue la lettura. Usare il codice seguente per filtrare la riga duplicata.
```
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

Successivamente, è possibile esaminare i tipi di dati delle colonne.
```
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
Sfortunatamente, tutte le colonne sono state restituite sotto forma di stringhe. Ciò accade perché, per impostazione predefinita, l'SDK di preparazione dati Azure Machine Learning non modifica il tipo di dati. L'origine dati da cui si sta leggendo è un file di testo, pertanto l'SDK legge tutti i valori come stringhe. Per questo esempio, tuttavia, si desidera analizzare le colonne numeriche come numeri. A tale scopo, è possibile impostare il parametro inference_arguments su current_culture.

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
Molte delle colonne sono state rilevate correttamente come numeri e il relativo tipo è impostato su float64. A seguito dell'inserimento, è possibile recuperare un dataframe di Pandas per il set di dati completo.

```
df = dataflow.to_pandas_dataframe()
df
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Contea Hale|1,017100e+10|49,0|
|1|ALABAMA|Contea Hale|1,017100e+10|40,0|
|2|ALABAMA|Contea Hale|1,017100e+10|43,0|
|3|ALABAMA|Contea Hale|1,017100e+10|2.0|
|4|ALABAMA|Contea Hale|1,017100e+10|23,0|

## <a name="read-excel"></a>Lettura di Excel
L'SDK di preparazione dati Azure Machine Learning comprende una funzione `read_excel` per caricare i file di Excel.
```
dataflow = dprep.read_excel(path='./data/excel.xlsx')
dataflow.head(5)
```
||Colonna1|Colonna2|Colonna3|Colonna4|Colonna5|Colonna6|Colonna7|Colonna8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Hoba|Iron, IV b|60000000,0|Trovato|1920,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |-19,58333|17,91667|
|1|Capo York|Iron, IIIAB|58200000,0|Trovato|1818,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |76,13333|-64,93333|
|2|Campo del Cielo|Iron, IAB-MG|50000000,0|Trovato|1576,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |-27,46667|-60,58333|
|3|Canyon Diablo|Iron, IAB-MG|30000000,0|Trovato|1891,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |35,05000|-111,03333|
|4|Armanty|Iron, IIIE|28000000,0|Trovato|1898,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |47,00000|88,00000|

Il primo foglio del file Excel è stato caricato. È possibile ottenere lo stesso risultato specificando in modo esplicito il nome del foglio da caricare. Se, invece, si desidera caricare il secondo foglio, è possibile fornire il suo nome come argomento.
```
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

Come si può notare, la tabella nel secondo foglio aveva le intestazioni e tre righe vuote. È necessario perciò modificare gli argomenti della funzione.
```
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_header=True, skip_rows=3)
df = dataflow.to_pandas_dataframe()
df
```

||RANK|Title|Studio|In tutto il mondo|Nazionale / %|Colonna1|Oltremare / %|Colonna2|Anno^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Avatar|Fox|2788|760,5|0,273|2027,5|0,727|2009^|
|1|2|Titanic|Par.|2186,8|658,7|0,301|1528,1|0,699|1997^|
|2|3|Gli Avengers di Marvel|BV|1518,6|623,4|0,41|895,2|0,59|2012|
|3|4|Harry Potter e i Doni della Morte parte 2|WB|1341,5|381|0,284|960,5|0,716|2011|
|4|5|Frozen|BV|1274,2|400,7|0,314|873,5|0,686|2013|

## <a name="read-fixed-width-files"></a>Leggere i file a larghezza fissa
Per i file a larghezza fissa, è possibile specificare un elenco di offset. Si considera sempre che la prima colonna inizi all'offset 0.

```
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


Se non sono presenti intestazioni nei file, è opportuno considerare la prima riga come dati. Passando `PromoteHeadersMode.NONE` all'argomento parola chiave dell'intestazione, è possibile evitare il rilevamento dell'intestazione e ottenere i dati corretti.

```
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)

df = dataflow.to_pandas_dataframe()
df
```
||Colonna1|Colonna2|Colonna3|Colonna4|Colonna5|Colonna6|Colonna7|Colonna8|Colonna9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|BOGUS NORWAY|NO|NO_1|ENRS|Colonna7|Colonna8|Colonna9|
|1|010003|99999|BOGUS NORWAY|NO|NO|ENSO||||
|2|010010|99999|JAN MAYEN|NO|JN|ENJA|+70933|-008667|+00090|
|3|010013|99999|ROST|NO|NO|||||
|4|010014|99999|SOERSTOKKEN|NO|NO|ENSO|+59783|+005350|+00500|
|5|010015|99999|BRINGELAND|NO|NO|ENBL|+61383|+005867|+03270|

## <a name="read-sql"></a>Lettura di SQL
L'SDK di preparazione dati Azure Machine Learning può anche caricare i dati dai server di SQL. Attualmente, è supportato solo Microsoft SQL Server.
Per leggere i dati da un server SQL, creare un oggetto origine dati che contiene le informazioni di connessione.

```
secret = dprep.register_secret("[SECRET-USERNAME]", "[SECRET-PASSWORD]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=[DATABASE-PASSWORD])
```
Come si può vedere, il parametro della password di `MSSQLDataSource` accetta un oggetto segreto. È possibile ottenere un oggetto segreto in due modi:
-   Registrare il segreto e il relativo valore con il motore di esecuzione. 
-   Creare il segreto con solo un ID (utile se il valore del segreto è già registrato nell'ambiente di esecuzione).

Dopo aver creato un oggetto origine dati, è possibile leggere i dati.
```
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

```
df = dataflow.to_pandas_dataframe()
df.dtypes
```
```
ProductID                                     int64
Name                                         object
ProductNumber                                object
Color                                        object
StandardCost                                float64
ListPrice                                   float64
Size                                         object
Weight                                      float64
ProductCategoryID                             int64
ProductModelID                                int64
SellStartDate             datetime64[ns, UTC+00:00]
SellEndDate                                  object
DiscontinuedDate                             object
ThumbNailPhoto                               object
ThumbnailPhotoFileName                       object
rowguid                                      object
ModifiedDate              datetime64[ns, UTC+00:00]
dtype: object
```

## <a name="read-from-azure-data-lake-storage"></a>Leggere da Azure Data Lake Storage
Esistono due modi in cui l'SDK può acquisire il token OAuth necessario per accedere ad Azure Data Lake Storage:
-   Recuperare il token di accesso da una sessione di accesso recente dell'account di accesso dell'utente all'interfaccia della riga di comando di Azure
-   Usare un'entità servizio (SP) e un certificato come segreto

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>Usare un token di accesso da una sessione recente dell'interfaccia della riga di comando di Azure
Nel computer locale, eseguire il comando seguente:

> [!NOTE] 
> Se l'account utente è membro di più di un tenant di Azure, è necessario specificare il tenant nel formato di nome host AAD URL.


```
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
```
### <a name="create-a-service-principal-with-the-azure-cli"></a>Creare un'entità servizio con l'interfaccia della riga di comando di Azure
È possibile usare l'interfaccia della riga di comando di Azure per creare un'entità servizio e il certificato corrispondente. Questa entità servizio specifica è configurata come Lettore, con il relativo ambito ridotto al solo l'account di Azure Data Lake Storage 'dpreptestfiles'
```
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```
Questo comando genera l'`appId` e il percorso che porta al file di certificato (in genere nella cartella home). Il file con estensione .crt contiene sia il certificato pubblico sia la chiave privata nel formato PEM.

Estrarre l'identificazione personale:
```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

##### <a name="configure-an-azure-data-lake-storage-account-for-the-service-principal"></a>Configurare un account di Azure Data Lake Storage per l'entità servizio
Per configurare l'ACL per il file system di Azure Data Lake Storage, usare il valore objectId dell'utente oppure, per questo esempio, l'entità servizio:
```
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```
##### <a name="configure-read-and-execute-access-for-the-azure-data-lake-storage-file-system"></a>Configurare la lettura ed eseguire l'accesso per il file system di Azure Data Lake Storage.
Poiché il modello di ACL HDFS sottostante non supporta l'ereditarietà, è necessario configurare singolarmente l'ACL per file e cartelle.
```
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
#### <a name="acquire-an-oauth-access-token"></a>Acquisire un token di accesso OAuth
Usare il pacchetto `adal` (tramite: `pip install adal`) per creare un contesto di autenticazione sul tenant MSFT e acquisire un token di accesso OAuth. Per Azure Data Lake Storage, la risorsa nella richiesta del token deve essere per 'https://datalake.azure.net', che è diversa dalla maggior parte delle altre risorse di Azure.

```
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
|4|1010691|10th Steet Community Farmers Market|http://agrimissouri.com/mo-grown/grodetail.php... |10th Street e Poplar|Lamar|Barton|

