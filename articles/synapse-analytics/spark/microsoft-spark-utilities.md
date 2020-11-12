---
title: Introduzione alle utilità di Microsoft Spark
description: Esercitazione sull'uso di MSSparkutils nei notebook di Azure sinapsi Analytics.
author: ruxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: c03d8e744598386db3d6d03a71e4d1b735d9d71f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533277"
---
# <a name="introduction-of-microsoft-spark-utilities"></a>Introduzione alle utilità di Microsoft Spark

Microsoft Spark Utilities (MSSparkUtils) è un pacchetto incorporato che consente di eseguire facilmente le attività più comuni. È possibile usare MSSparkUtils per lavorare con i file System, per ottenere le variabili di ambiente e per lavorare con i segreti. MSSparkUtils sono disponibili nelle `PySpark (Python)` pipeline di, `Scala` e `.NET Spark (C#)` notebook e sinapsi.

## <a name="pre-requisites"></a>Prerequisiti

### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>Configurare l'accesso a Azure Data Lake Storage Gen2 

I notebook sinapsi usano il pass-through di Azure Active Directory (Azure AD) per accedere agli account di ADLS Gen2. È necessario essere un **collaboratore dell'archiviazione BLOB** per accedere alla ADLS Gen2 account (o alla cartella). 

Le pipeline sinapsi usano l'identità dell'area di lavoro (MSI) per accedere agli account di archiviazione. Per usare MSSparkUtils nelle attività della pipeline, l'identità dell'area di lavoro deve essere **collaboratore dell'archiviazione BLOB** per accedere alla ADLS Gen2 account (o alla cartella).

Per assicurarsi che il Azure AD e l'identità del servizio gestito dell'area di lavoro abbiano accesso all'account ADLS Gen2, attenersi alla procedura seguente:
1. Aprire il [portale di Azure](https://portal.azure.com/) e l'account di archiviazione a cui si vuole accedere. È possibile passare al contenitore specifico a cui si vuole accedere.
2. Selezionare il **controllo di accesso (IAM)** nel pannello di sinistra.
3. Assegnare l' **account Azure ad** e l' **identità dell'area di lavoro** (uguale al nome dell'area di lavoro) al ruolo di **collaboratore dati BLOB di archiviazione** nell'account di archiviazione, se non è già assegnato. 
4. Selezionare **Salva**.

È possibile accedere ai dati in ADLS Gen2 con sinapsi Spark tramite l'URL seguente:

<code>abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path></code>

### <a name="configure-access-to-azure-blob-storage"></a>Configurare l'accesso all'archiviazione BLOB di Azure 

Le sinapsi sfruttano la **firma di accesso condiviso** per accedere all'archivio BLOB di Azure. Per evitare di esporre le chiavi SAS nel codice, è consigliabile creare un nuovo servizio collegato nell'area di lavoro sinapsi per l'account di archiviazione BLOB di Azure a cui si vuole accedere.

Per aggiungere un nuovo servizio collegato per un account di archiviazione BLOB di Azure, seguire questa procedura:

1. Aprire [Azure sinapsi Studio](https://web.azuresynapse.net/).
2. Selezionare **Gestisci** dal pannello sinistro e selezionare **servizi collegati** sotto le **connessioni esterne**.
3. Cercare nell' **Archivio BLOB di Azure** nel pannello **nuovo servizio collegato** a destra.
4. Selezionare **Continua**.
5. Selezionare l'account di archiviazione BLOB di Azure per accedere al nome del servizio collegato e configurarlo. Suggerire l'uso della **chiave dell'account** per il **metodo di autenticazione**.
6. Selezionare **Test connessione** per verificare che le impostazioni siano corrette.
7. Selezionare **Crea** prima e fare clic su **pubblica tutto** per salvare le modifiche. 

È possibile accedere ai dati nell'archivio BLOB di Azure con sinapsi Spark tramite l'URL seguente:

<code>wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path></code>

Di seguito è riportato un esempio di codice:


:::zone pivot = "programming-language-python"

```python
from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linkedServiceName = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linkedServiceName)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end
 
###  <a name="configure-access-to-azure-key-vault"></a>Configurare l'accesso a Azure Key Vault

È possibile aggiungere un Azure Key Vault come servizio collegato per gestire le credenziali in sinapsi. Per aggiungere un Azure Key Vault come servizio collegato sinapsi, seguire questa procedura:
1. Aprire [Azure sinapsi Studio](https://web.azuresynapse.net/).
2. Selezionare **Gestisci** dal pannello sinistro e selezionare **servizi collegati** sotto le **connessioni esterne**.
3. Cercare **Azure Key Vault** nel pannello **nuovo servizio collegato** a destra.
4. Selezionare l'account Azure Key Vault per accedere al nome del servizio collegato e configurarlo.
5. Selezionare **Test connessione** per verificare che le impostazioni siano corrette.
6. Selezionare **Crea** prima e fare clic su **pubblica tutto** per salvare la modifica. 

I notebook sinapsi usano il pass-through di Azure Active Directory (Azure AD) per accedere a Azure Key Vault. Le pipeline sinapsi usano l'identità dell'area di lavoro (MSI) per accedere Azure Key Vault. Per assicurarsi che il codice funzioni sia nel notebook che nella pipeline sinapsi, è consigliabile concedere l'autorizzazione di accesso Secret sia per l'account Azure AD sia per l'identità dell'area di lavoro.

Per concedere l'accesso segreto all'identità dell'area di lavoro, seguire questa procedura:
1. Aprire il [portale di Azure](https://portal.azure.com/) e il Azure Key Vault a cui si vuole accedere. 
2. Selezionare i **criteri di accesso** nel pannello di sinistra.
3. Selezionare **Aggiungi criteri di accesso** : 
    - Scegliere **chiave, segreto, & gestione certificati** come modello di configurazione.
    - Selezionare l' **account Azure ad** e **l'identità dell'area di lavoro** (uguale al nome dell'area di lavoro) nell'entità di selezione o assicurarsi che sia già assegnata. 
4. Selezionare **Seleziona** e **Aggiungi**.
5. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.  

## <a name="file-system-utilities"></a>Utilità di file system

`mssparkutils.fs` fornisce utilità per l'uso di diversi file System, tra cui Azure Data Lake Storage Gen2 (ADLS Gen2) e archiviazione BLOB di Azure. Assicurarsi di configurare l'accesso a [Azure Data Lake storage Gen2](#configure-access-to-azure-data-lake-storage-gen2) e all' [archiviazione BLOB di Azure](#configure-access-to-azure-blob-storage) in modo appropriato.

Per una panoramica dei metodi disponibili, eseguire i comandi seguenti:

:::zone pivot = "programming-language-python"

```python
from notebookutils import mssparkutils
mssparkutils.fs.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Notebook.MSSparkUtils;
FS.Help()
```

::: zone-end

Il risultato è il seguente:
```
mssparkutils.fs provides utilities for working with various FileSystems.

Below is overview about the available methods:

cp(from: String, to: String, recurse: Boolean = false): Boolean -> Copies a file or directory, possibly across FileSystems
mv(from: String, to: String, recurse: Boolean = false): Boolean -> Moves a file or directory, possibly across FileSystems
ls(dir: String): Array -> Lists the contents of a directory
mkdirs(dir: String): Boolean -> Creates the given directory if it does not exist, also creating any necessary parent directories
put(file: String, contents: String, overwrite: Boolean = false): Boolean -> Writes the given String out to a file, encoded in UTF-8
head(file: String, maxBytes: int = 1024 * 100): String -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
append(file: String, content: String, createFileIfNotExists: Boolean): Boolean -> Append the content to a file
rm(dir: String, recurse: Boolean = false): Boolean -> Removes a file or directory

Use mssparkutils.fs.help("methodName") for more info about a method.

```

### <a name="list-files"></a>Elencare file
Elenca il contenuto di una directory.


:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.ls('Your directory path')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.ls("Your directory path")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Ls("Your directory path")
```

::: zone-end


### <a name="view-file-properties"></a>Visualizzazione delle proprietà di file
Restituisce le proprietà del file, inclusi il nome del file, il percorso del file, le dimensioni del file e se si tratta di una directory e di un file.

:::zone pivot = "programming-language-python"

```python
files = mssparkutils.fs.ls('Your directory path')
for file in files:
    print(file.name, file.isDir, file.isFile, file.path, file.size)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val files = mssparkutils.fs.ls("/")
files.foreach{
    file => println(file.name,file.isDir,file.isFile,file.size)
}
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var Files = FS.Ls("/");
foreach(var File in Files) {
    Console.WriteLine(File.Name+" "+File.IsDir+" "+File.IsFile+" "+File.Size);
}
```

::: zone-end

### <a name="create-new-directory"></a>Crea nuova directory

Crea la directory specificata se non esiste ed eventuali directory padre necessarie.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mkdirs('new directory name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mkdirs("new directory name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mkdirs("new directory name")
```

::: zone-end

### <a name="copy-file"></a>Copia file

Copia un file o una directory. Supporta la copia tra file System.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.cp('source file or directory', 'destination file or directory', True)# Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

### <a name="preview-file-content"></a>Anteprima contenuto file

Restituisce fino ai primi ' maxBytes ' byte del file specificato sotto forma di stringa codificata in UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.head('file path', maxBytes to read)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.head("file path", maxBytes to read)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Head("file path", maxBytes to read)
```

::: zone-end

### <a name="move-file"></a>Sposta file

Sposta un file o una directory. Supporta lo spostamento in file System.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mv('source file or directory', 'destination directory', True) # Set the last parameter as True to firstly create the parent directory if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mv("source file or directory", "destination directory", true) // Set the last parameter as True to firstly create the parent directory if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mv("source file or directory", "destination directory", true)
```

::: zone-end

### <a name="write-file"></a>Scrivi file

Scrive la stringa specificata in un file, codificato in UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.put("file path", "content to write", True) # Set the last parameter as True to overwrite the file if it existed already
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

### <a name="append-content-to-a-file"></a>Accoda contenuto a un file

Accoda la stringa specificata a un file, codificato in UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append('file path','content to append',True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>Elimina file o directory

Rimuove un file o una directory.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.rm('file path', True) # Set the last parameter as True to remove all files and directories recursively 
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end


## <a name="credentials-utilities"></a>Utilità credenziali

È possibile usare le utilità per le credenziali di MSSparkUtils per ottenere i token di accesso dei servizi collegati e gestire i segreti in Azure Key Vault. 

Eseguire il comando seguente per ottenere una panoramica dei metodi disponibili:

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

Ottenere il risultato:

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>Ottenere un token

Restituisce Azure AD token per un determinato gruppo di destinatari, nome (facoltativo). La tabella seguente elenca tutti i tipi di destinatari disponibili: 

|Tipo di destinatari|Chiave destinatari|
|--|--|
|Tipo di risoluzione destinatari|Pubblico|
|Risorsa destinatari archiviazione|Archiviazione|
|Risorsa data warehouse audience|DW|
|Risorsa Data Lake audience|'AzureManagement'|
|Risorsa destinatari dell'insieme di credenziali|Archivio data Lake|
|Risorsa audience di Azure OSSDB|'AzureOSSDB'|
|Risorsa di Azure sinapsi|Sinapsi|
|Risorsa Azure Data Factory|ADF|

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getToken('audience Key')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getToken("audience Key")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="validate-token"></a>Convalida token

Restituisce true se il token non è scaduto.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.isValidToken('your token')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.isValidToken("your token")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="get-connection-string-or-credentials-for-linked-service"></a>Ottenere la stringa di connessione o le credenziali per il servizio collegato

Restituisce la stringa di connessione o le credenziali per il servizio collegato. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getConnectionStringOrCreds('linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getConnectionStringOrCreds("linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="get-secret-using-workspace-identity"></a>Ottenere il segreto usando l'identità dell'area di lavoro

Restituisce Azure Key Vault segreto per un determinato nome di Azure Key Vault, nome del segreto e nome del servizio collegato utilizzando l'identità dell'area di lavoro. Assicurarsi di configurare l'accesso a [Azure Key Vault](#configure-access-to-azure-key-vault) in modo appropriato.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="get-secret-using-user-credentials"></a>Ottenere il segreto usando le credenziali utente

Restituisce Azure Key Vault segreto per un nome di Azure Key Vault, il nome del segreto e il nome del servizio collegato specificati utilizzando le credenziali utente. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="put-secret-using-workspace-identity"></a>Inserire il segreto usando l'identità dell'area di lavoro

Inserisce Azure Key Vault segreto per un determinato nome di Azure Key Vault, nome del segreto e nome del servizio collegato utilizzando l'identità dell'area di lavoro. Assicurarsi di configurare l'accesso [Azure Key Vault](#configure-access-to-azure-key-vault) in modo appropriato.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="put-secret-using-user-credentials"></a>Inserire il segreto usando le credenziali utente

Inserisce Azure Key Vault segreto per un nome di Azure Key Vault, il nome del segreto e il nome del servizio collegato specificati utilizzando le credenziali utente. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


## <a name="environment-utilities"></a>Utilità per l'ambiente 

Eseguire i comandi seguenti per ottenere una panoramica dei metodi disponibili:

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

Ottenere il risultato:
```
getUserName(): returns user name
getUserId(): returns unique user id
getJobId(): returns job id
getWorkspaceName(): returns workspace name
getPoolName(): returns Spark pool name
getClusterId(): returns cluster id
```

### <a name="get-user-name"></a>Ottenere il nome utente

Restituisce il nome utente corrente.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="get-user-id"></a>Ottieni ID utente

Restituisce l'ID utente corrente.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="get-job-id"></a>Ottieni ID processo

Restituisce l'ID del processo.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getJobId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getJobId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="get-workspace-name"></a>Ottenere il nome dell'area di lavoro

Restituisce il nome dell'area di lavoro.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getWorkspaceName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getWorkspaceName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="get-pool-name"></a>Ottenere il nome del pool

Restituisce il nome del pool di Spark.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getPoolName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getPoolName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="get-cluster-id"></a>Ottieni ID cluster

Restituisce l'ID del cluster corrente.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getClusterId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getClusterId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

- [Estrai notebook di esempio sinapsi](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Avvio rapido: Creare un pool di Apache Spark (anteprima) in Azure Synapse Analytics con gli strumenti Web](../quickstart-apache-spark-notebook.md)
- [Che cos'è Apache Spark in Azure Synapse Analytics](apache-spark-overview.md)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)