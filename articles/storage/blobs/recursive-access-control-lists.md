---
title: Impostare gli ACL in modo ricorsivo per Azure Data Lake Storage Gen2 | Microsoft Docs
description: È possibile aggiungere, aggiornare e rimuovere gli elenchi di controllo di accesso in modo ricorsivo per gli elementi figlio esistenti di una directory padre.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: d61942155e46792f95091957925523381a5a9cda
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422621"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Impostare gli elenchi di controllo di accesso (ACL) in modo ricorsivo per Azure Data Lake Storage Gen2

L'ereditarietà ACL è già disponibile per i nuovi elementi figlio creati in una directory padre. È anche possibile aggiungere, aggiornare e rimuovere gli ACL in modo ricorsivo per gli elementi figlio esistenti di una directory padre senza dover apportare queste modifiche singolarmente per ogni elemento figlio.

[Librerie](#libraries)  |  di [Esempi](#code-samples)  |  di [Procedure](#best-practice-guidelines)  |  consigliate [Invia commenti e suggerimenti](#provide-feedback)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](create-data-lake-storage-account.md) istruzioni.

- Le autorizzazioni corrette per eseguire il processo ACL ricorsivo. L'autorizzazione corretta include uno degli elementi seguenti: 

  - [Entità di sicurezza](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) con provisioning Azure Active Directory (ad) a cui è stato assegnato il ruolo di [proprietario dei dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) nell'ambito del contenitore di destinazione, del gruppo di risorse padre o della sottoscrizione.   

  - Utente proprietario del contenitore o della directory di destinazione a cui si intende applicare il processo ACL ricorsivo. Sono inclusi tutti gli elementi figlio nel contenitore o nella directory di destinazione. 

- Informazioni sul modo in cui gli ACL vengono applicati alle directory e ai file. Vedere [controllo di accesso in Azure Data Lake storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Vedere la sezione **configurare il progetto** di questo articolo per visualizzare le linee guida per l'installazione di PowerShell, .NET SDK e Python SDK.

## <a name="set-up-your-project"></a>Configurare il progetto

Installare le librerie necessarie.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Verificare che la versione di PowerShell installata sia `5.1` o superiore usando il comando seguente.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Per aggiornare la versione di PowerShell, vedere [aggiornamento di Windows PowerShell esistente](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell)
    
2. Installare **AZ. storage** Module.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Per altre informazioni su come installare i moduli di PowerShell, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Aprire [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)o aprire un'applicazione console comando come Windows PowerShell, se è stata [installata](https://docs.microsoft.com/cli/azure/install-azure-cli) l'interfaccia della riga di comando di Azure in locale.

2. Verificare con il comando seguente che la versione dell'interfaccia della riga di comando di Azure installata corrisponda a `2.14.0` o successive.

   ```azurecli
    az --version
   ```
   Se la versione dell'interfaccia della riga di comando di Azure fosse inferiore a `2.14.0`, installare una versione successiva. Vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="net"></a>[.NET](#tab/dotnet)

1. Aprire una finestra di comando (ad esempio: Windows PowerShell).

2. Dalla directory del progetto, installare il pacchetto di anteprima di Azure. storage. files. datalake usando il `dotnet add package` comando.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.5.0-preview.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. Aggiungere queste istruzioni using all'inizio del file di codice.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="java"></a>[Java](#tab/java)

Per iniziare, aprire [Questa pagina](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) e trovare la versione più recente della libreria Java. Aprire quindi il file di *pom.xml* nell'editor di testo. Aggiungere un elemento dependency che faccia riferimento a tale versione.

Se si prevede di autenticare l'applicazione client usando Azure Active Directory (AD), aggiungere una dipendenza alla libreria client di Azure Secret. Vedere  [aggiunta del pacchetto di librerie client segrete al progetto](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Aggiungere quindi queste istruzioni Imports al file di codice.

```java
import com.azure.identity.ClientSecretCredential;
import com.azure.identity.ClientSecretCredentialBuilder;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.Response;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

### <a name="python"></a>[Python](#tab/python)

1. Scaricare la [libreria client di Azure Data Lake storage per Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0).

2. Installare la libreria scaricata usando [PIP](https://pypi.org/project/pip/).

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

3. Aggiungere queste istruzioni Import all'inizio del file di codice.

   ```python
   import os, uuid, sys
   from azure.storage.filedatalake import DataLakeServiceClient
   from azure.core._match_conditions import MatchConditions
   from azure.storage.filedatalake._models import ContentSettings
   ```

---

## <a name="connect-to-your-account"></a>Connettersi all'account

È possibile connettersi usando Azure Active Directory (AD) o usando una chiave dell'account. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aprire una finestra di comando di Windows PowerShell e accedere alla sottoscrizione di Azure con il `Connect-AzAccount` comando e seguire le istruzioni visualizzate.

```powershell
Connect-AzAccount
```

Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione in cui si vogliono creare e gestire le directory. In questo esempio, sostituire il `<subscription-id>` valore del segnaposto con l'ID della sottoscrizione.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Scegliere quindi come si vuole che i comandi ottengano l'autorizzazione per l'account di archiviazione. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opzione 1: ottenere l'autorizzazione utilizzando Azure Active Directory (AD)

Con questo approccio, il sistema garantisce che l'account utente disponga delle assegnazioni appropriate di controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e delle autorizzazioni ACL. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

Nella tabella seguente vengono illustrati tutti i ruoli supportati e la relativa funzionalità di impostazione ACL.

|Ruolo|Funzionalità impostazione ACL|
|--|--|
|[Proprietario dei dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Tutte le directory e i file nell'account.|
|[Collaboratore ai dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Solo le directory e i file di proprietà dell'entità di sicurezza.|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opzione 2: ottenere l'autorizzazione usando la chiave dell'account di archiviazione

Con questo approccio, il sistema non controlla le autorizzazioni RBAC o ACL di Azure.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Se si usa l'interfaccia della riga di comando di Azure in locale, eseguire il comando di accesso.

   ```azurecli
   az login
   ```

   Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.

   In caso contrario, aprire una pagina del browser all'indirizzo [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e immettere il codice di autorizzazione visualizzato nel terminale. Quindi, accedere con le credenziali dell'account nel browser.

   Per altre informazioni sui diversi metodi di autenticazione, vedere [Autorizzare l'accesso ai dati BLOB o ai dati della coda con l'interfaccia della riga di comando di Azure](../common/authorize-data-operations-cli.md).

2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva alla sottoscrizione dell'account di archiviazione che ospiterà il sito Web statico.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

> [!NOTE]
> L'esempio presentato in questo articolo illustra l'autorizzazione Azure Active Directory. Per altre informazioni sui metodi di autorizzazione, vedere [Autorizzare l'accesso ai dati BLOB o ai dati della coda con l'interfaccia della riga di comando di Azure](../common/authorize-data-operations-cli.md).

### <a name="net"></a>[.NET](#tab/dotnet)

Per usare i frammenti di codice in questo articolo, è necessario creare un'istanza di [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) che rappresenta l'account di archiviazione.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Connettersi tramite Azure Active Directory (AD)

È possibile usare la [libreria client di identità di Azure per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) per autenticare l'applicazione con Azure ad.

Al termine dell'installazione del pacchetto, aggiungere questa istruzione using all'inizio del file di codice.

```csharp
using Azure.Identity;
```

Ottenere un ID client, un segreto client e un ID tenant. Per eseguire questa operazione, vedere [acquisizione di un token da Azure ad per l'autorizzazione delle richieste da un'applicazione client](../common/storage-auth-aad-app.md). Come parte di questo processo, è necessario assegnare uno dei ruoli di controllo degli [accessi in base al ruolo di Azure (RBAC)](../../role-based-access-control/overview.md) per l'entità di sicurezza. 

|Ruolo|Funzionalità impostazione ACL|
|--|--|
|[Proprietario dei dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Tutte le directory e i file nell'account.|
|[Collaboratore ai dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Solo le directory e i file di proprietà dell'entità di sicurezza.|

Questo esempio crea un'istanza di [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) usando un ID client, un segreto client e un ID tenant.  

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

``` 

#### <a name="connect-by-using-an-account-key"></a>Connettersi tramite una chiave dell'account

Questo approccio è il modo più semplice per connettersi a un account. 

Questo esempio crea un'istanza di [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) usando una chiave dell'account.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

> [!NOTE]
> Per altri esempi, vedere la documentazione della [libreria client Azure Identity per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

### <a name="java"></a>[Java](#tab/java)

Per usare i frammenti di codice in questo articolo, è necessario creare un'istanza di **DataLakeServiceClient** che rappresenta l'account di archiviazione. 

#### <a name="connect-by-using-an-account-key"></a>Connettersi tramite una chiave dell'account

Questo è il modo più semplice per connettersi a un account. 

Questo esempio crea un'istanza di **DataLakeServiceClient** usando una chiave dell'account.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

#### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Connettersi tramite Azure Active Directory (Azure AD)

È possibile usare la [libreria client Azure Identity per Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) per autenticare l'applicazione con Azure ad.

Questo esempio crea un'istanza di **DataLakeServiceClient** usando un ID client, un segreto client e un ID tenant.  Per ottenere questi valori, vedere [acquisizione di un token da Azure ad per l'autorizzazione delle richieste da un'applicazione client](../common/storage-auth-aad-app.md).

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> Per altri esempi, vedere la documentazione della [libreria client Azure Identity per Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

### <a name="python"></a>[Python](#tab/python)

Per usare i frammenti di codice in questo articolo, è necessario creare un'istanza di **DataLakeServiceClient** che rappresenta l'account di archiviazione. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Connettersi tramite Azure Active Directory (AD)

È possibile usare la [libreria client Azure Identity per Python](https://pypi.org/project/azure-identity/) per autenticare l'applicazione con Azure ad.

Questo esempio crea un'istanza di **DataLakeServiceClient** usando un ID client, un segreto client e un ID tenant.  Per ottenere questi valori, vedere [acquisizione di un token da Azure ad per l'autorizzazione delle richieste da un'applicazione client](../common/storage-auth-aad-app.md). Come parte di questo processo, è necessario assegnare uno dei ruoli di controllo degli [accessi in base al ruolo di Azure (RBAC)](../../role-based-access-control/overview.md) per l'entità di sicurezza. 

|Ruolo|Funzionalità impostazione ACL|
|--|--|
|[Proprietario dei dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Tutte le directory e i file nell'account.|
|[Collaboratore ai dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Solo le directory e i file di proprietà dell'entità di sicurezza.|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Per altri esempi, vedere la documentazione della [libreria client Azure Identity per Python](https://pypi.org/project/azure-identity/) .

### <a name="connect-by-using-an-account-key"></a>Connettersi tramite una chiave dell'account

Questo è il modo più semplice per connettersi a un account. 

Questo esempio crea un'istanza di **DataLakeServiceClient** usando una chiave dell'account.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Sostituire il valore segnaposto `storage_account_name` con il nome del proprio account di archiviazione.

- Sostituire il `storage_account_key` valore del segnaposto con la chiave di accesso dell'account di archiviazione.

---

## <a name="set-an-acl-recursively"></a>Impostare un ACL in modo ricorsivo

Quando si *imposta* un ACL, si **sostituisce** l'intero ACL, incluse tutte le voci. Se si desidera modificare il livello di autorizzazione di un'entità di sicurezza o aggiungere una nuova entità di sicurezza all'ACL senza influire sulle altre voci esistenti, è necessario *aggiornare* invece l'ACL. Per aggiornare un ACL anziché sostituirlo, vedere la sezione [aggiornare un ACL in modo ricorsivo](#update-an-acl-recursively) di questo articolo.   

Questa sezione contiene esempi su come impostare un ACL 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Impostare un ACL in modo ricorsivo utilizzando il cmdlet **set-AzDataLakeGen2AclRecursive** .

In questo esempio viene impostato l'ACL di una directory denominata `my-parent-directory` . Queste voci forniscono le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e offre a tutti gli altri utenti l'accesso. L'ultima voce ACL in questo esempio fornisce a un utente specifico l'ID oggetto "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" autorizzazioni di lettura ed esecuzione.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Se si vuole impostare una voce ACL **predefinita** , usare il parametro **-DefaultScope** quando si esegue il comando **set-AzDataLakeGen2ItemAclObject** . Ad esempio: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Per vedere un esempio che imposta gli ACL in modo ricorsivo nei batch specificando una dimensione del batch, vedere l'articolo di riferimento [set-AzDataLakeGen2AclRecursive](https://docs.microsoft.com/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Impostare un ACL in modo ricorsivo usando il comando [AZ storage FS Access set-ricorsivo](https://docs.microsoft.com/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) .

In questo esempio viene impostato l'ACL di una directory denominata `my-parent-directory` . Queste voci forniscono le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e offre a tutti gli altri utenti l'accesso. L'ultima voce ACL in questo esempio fornisce a un utente specifico l'ID oggetto "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" autorizzazioni di lettura ed esecuzione.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se si desidera impostare una voce ACL **predefinita** , aggiungere il prefisso `default:` a ogni voce. Ad esempio, `default:user::rwx` o `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`. 

### <a name="net"></a>[.NET](#tab/dotnet)

Impostare un ACL in modo ricorsivo chiamando il metodo **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** . Passare questo metodo a un [elenco](/dotnet/api/system.collections.generic.list-1) di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Ogni [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definisce una voce ACL. 

Se si vuole impostare una voce ACL **predefinita** , è possibile impostare la proprietà [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) su **true**. 

In questo esempio viene impostato l'ACL di una directory denominata `my-parent-directory` . Questo metodo accetta un parametro booleano denominato `isDefaultScope` che specifica se impostare l'ACL predefinito. Tale parametro viene utilizzato nel costruttore di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Le voci dell'ACL forniscono le autorizzazioni di lettura, scrittura ed esecuzione da parte dell'utente proprietario, assegna al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e offre a tutti gli utenti nessun accesso. L'ultima voce ACL in questo esempio fornisce a un utente specifico l'ID oggetto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "autorizzazioni di lettura ed esecuzione.

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlList = 
        new List<PathAccessControlItem>() 
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None, isDefaultScope),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

Per un esempio che imposta gli ACL in modo ricorsivo nei batch specificando una dimensione del batch, vedere l' [esempio](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

### <a name="java"></a>[Java](#tab/java)

Impostare un ACL in modo ricorsivo chiamando il metodo **DataLakeDirectoryClient. setAccessControlRecursive** . Passare questo metodo a un [elenco](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) di oggetti [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Ogni [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definisce una voce ACL. 

Se si vuole impostare una voce ACL **predefinita** , è possibile chiamare il metodo **setDefaultScope** di [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passare il valore **true**. 

In questo esempio viene impostato l'ACL di una directory denominata `my-parent-directory` . Questo metodo accetta un parametro booleano denominato `isDefaultScope` che specifica se impostare l'ACL predefinito. Tale parametro viene utilizzato in ogni chiamata al metodo **setDefaultScope** di [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). Le voci dell'ACL forniscono le autorizzazioni di lettura, scrittura ed esecuzione da parte dell'utente proprietario, assegna al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e offre a tutti gli utenti nessun accesso. L'ultima voce ACL in questo esempio fornisce a un utente specifico l'ID oggetto "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" autorizzazioni di lettura ed esecuzione.

```java
static public void SetACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){
    
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathAccessControlEntry> pathAccessControlEntries = 
        new ArrayList<PathAccessControlEntry>();

    // Create owner entry.
    PathAccessControlEntry ownerEntry = new PathAccessControlEntry();

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    ownerEntry.setDefaultScope(isDefaultScope);
    ownerEntry.setAccessControlType(AccessControlType.USER);
    ownerEntry.setPermissions(ownerPermission);

    pathAccessControlEntries.add(ownerEntry);

    // Create group entry.
    PathAccessControlEntry groupEntry = new PathAccessControlEntry();

    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(false);

    groupEntry.setDefaultScope(isDefaultScope);
    groupEntry.setAccessControlType(AccessControlType.GROUP);
    groupEntry.setPermissions(groupPermission);

    pathAccessControlEntries.add(groupEntry);

    // Create other entry.
    PathAccessControlEntry otherEntry = new PathAccessControlEntry();

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setExecutePermission(false).setReadPermission(false).setWritePermission(false);

    otherEntry.setDefaultScope(isDefaultScope);
    otherEntry.setAccessControlType(AccessControlType.OTHER);
    otherEntry.setPermissions(otherPermission);

    pathAccessControlEntries.add(otherEntry);

    // Create named user entry.
    PathAccessControlEntry userEntry = new PathAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(false);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx");
    userEntry.setPermissions(userPermission);    
    
    pathAccessControlEntries.add(userEntry);
    
    directoryClient.setAccessControlRecursive(pathAccessControlEntries);        

}
```

### <a name="python"></a>[Python](#tab/python)

Impostare un ACL in modo ricorsivo chiamando il metodo **DataLakeDirectoryClient.set_access_control_recursive** .

Se si desidera impostare una voce ACL **predefinita** , aggiungere la stringa `default:` all'inizio di ogni stringa di voce ACL. 

In questo esempio viene impostato l'ACL di una directory denominata `my-parent-directory` . 

Questo metodo accetta un parametro booleano denominato `is_default_scope` che specifica se impostare l'ACL predefinito. Se il parametro è `True` , l'elenco di voci ACL è preceduto dalla stringa `default:` . 

Le voci dell'ACL forniscono le autorizzazioni di lettura, scrittura ed esecuzione da parte dell'utente proprietario, assegna al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e offre a tutti gli utenti nessun accesso. L'ultima voce ACL in questo esempio fornisce a un utente specifico l'ID oggetto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "autorizzazioni di lettura ed esecuzione. Queste voci forniscono le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e offre a tutti gli altri utenti l'accesso. L'ultima voce ACL in questo esempio fornisce a un utente specifico l'ID oggetto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "autorizzazioni di lettura ed esecuzione.

```python
def set_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'   

        if is_default_scope:
           acl = 'default:user::rwx,default:group::rwx,default:other::rwx,default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

Per un esempio che elabora gli ACL in modo ricorsivo in batch specificando una dimensione del batch, vedere l' [esempio](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Python.

---

## <a name="update-an-acl-recursively"></a>Aggiornare un ACL in modo ricorsivo

Quando si *Aggiorna* un ACL, si modifica l'ACL anziché sostituire l'ACL. Ad esempio, è possibile aggiungere una nuova entità di sicurezza all'ACL senza influire sulle altre entità di sicurezza elencate nell'ACL.  Per sostituire l'ACL anziché aggiornarlo, vedere la sezione [impostare un ACL in modo ricorsivo](#set-an-acl-recursively) di questo articolo. 

Per aggiornare un ACL, creare un nuovo oggetto ACL con la voce ACL che si vuole aggiornare e quindi usare tale oggetto in Update ACL Operation. Non ottenere l'ACL esistente, fornire solo le voci ACL da aggiornare.

Questa sezione contiene esempi su come aggiornare un ACL.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aggiornare un ACL in modo ricorsivo usando il cmdlet  **Update-AzDataLakeGen2AclRecursive** . 

Questo esempio aggiorna una voce ACL con autorizzazione di scrittura. 

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Se si desidera aggiornare una voce ACL **predefinita** , utilizzare il parametro **-DefaultScope** quando si esegue il comando **set-AzDataLakeGen2ItemAclObject** . Ad esempio: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`.

Per un esempio in cui gli ACL vengono aggiornati in modo ricorsivo in batch specificando una dimensione del batch, vedere l'articolo di riferimento [Update-AzDataLakeGen2AclRecursive](https://docs.microsoft.com/powershell/module/az.storage/update-azdatalakegen2aclrecursive) .

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Aggiornare un ACL in modo ricorsivo usando il comando  [AZ storage FS Access Update-ricorsivo](https://docs.microsoft.com/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) . 

Questo esempio aggiorna una voce ACL con autorizzazione di scrittura. 

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se si desidera aggiornare una voce ACL **predefinita** , aggiungere il prefisso `default:` a ogni voce. Ad esempio, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`

### <a name="net"></a>[.NET](#tab/dotnet)

Aggiornare un ACL in modo ricorsivo chiamando il metodo **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** .  Passare questo metodo a un [elenco](/dotnet/api/system.collections.generic.list-1) di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Ogni [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definisce una voce ACL. 

Se si desidera aggiornare una voce ACL **predefinita** , è possibile impostare la proprietà [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) su **true**. 

Questo esempio aggiorna una voce ACL con autorizzazione di scrittura. Questo metodo accetta un parametro booleano denominato `isDefaultScope` che specifica se aggiornare l'ACL predefinito. Tale parametro viene utilizzato nel costruttore di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
        GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlListUpdate = 
        new List<PathAccessControlItem>()
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read |
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

Per un esempio in cui gli ACL vengono aggiornati in modo ricorsivo in batch specificando una dimensione del batch, vedere l' [esempio](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

### <a name="java"></a>[Java](#tab/java)

Aggiornare un ACL in modo ricorsivo chiamando il metodo **DataLakeDirectoryClient. updateAccessControlRecursive** .  Passare questo metodo a un [elenco](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) di oggetti [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Ogni [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definisce una voce ACL. 

Se si desidera aggiornare una voce ACL **predefinita** , è possibile utilizzare il metodo **setDefaultScope** di [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passare il valore **true**. 

Questo esempio aggiorna una voce ACL con autorizzazione di scrittura. Questo metodo accetta un parametro booleano denominato `isDefaultScope` che specifica se aggiornare l'ACL predefinito. Tale parametro viene utilizzato nella chiamata al metodo **setDefaultScope** di [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). 

```java
static public void UpdateACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){

    DataLakeDirectoryClient directoryClient =
    fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathAccessControlEntry> pathAccessControlEntries = 
        new ArrayList<PathAccessControlEntry>();

    // Create named user entry.
    PathAccessControlEntry userEntry = new PathAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx");
    userEntry.setPermissions(userPermission);    
    
    pathAccessControlEntries.add(userEntry);
    
    directoryClient.updateAccessControlRecursive(pathAccessControlEntries);          
}
```

### <a name="python"></a>[Python](#tab/python)

Aggiornare un ACL in modo ricorsivo chiamando il metodo **DataLakeDirectoryClient.update_access_control_recursive** . Se si desidera aggiornare una voce ACL **predefinita** , aggiungere la stringa `default:` all'inizio di ogni stringa di voce ACL. 

Questo esempio aggiorna una voce ACL con autorizzazione di scrittura.

In questo esempio viene impostato l'ACL di una directory denominata `my-parent-directory` . Questo metodo accetta un parametro booleano denominato `is_default_scope` che specifica se aggiornare l'ACL predefinito. Se il parametro è `True` , la voce ACL aggiornata è preceduta dalla stringa `default:` .  

```python
def update_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")

        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'   

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

Per un esempio che elabora gli ACL in modo ricorsivo in batch specificando una dimensione del batch, vedere l' [esempio](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Python.

---

## <a name="remove-acl-entries-recursively"></a>Rimuovi le voci ACL in modo ricorsivo

È possibile rimuovere una o più voci ACL in modo ricorsivo. Per rimuovere una voce ACL, creare un nuovo oggetto ACL per la voce ACL da rimuovere, quindi utilizzare l'oggetto in Rimuovi operazione ACL. Non ottenere l'ACL esistente, fornire solo le voci ACL da rimuovere. 

Questa sezione contiene esempi su come rimuovere un ACL. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rimuovere le voci ACL usando il cmdlet **Remove-AzDataLakeGen2AclRecursive** . 

In questo esempio viene rimossa una voce ACL dalla directory radice del contenitore.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Se si vuole rimuovere una voce ACL **predefinita** , usare il parametro **-DefaultScope** quando si esegue il comando **set-AzDataLakeGen2ItemAclObject** . Ad esempio: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Per un esempio in cui gli ACL vengono rimossi in modo ricorsivo in batch specificando una dimensione del batch, vedere l'articolo di riferimento [Remove-AzDataLakeGen2AclRecursive](https://docs.microsoft.com/powershell/module/az.storage/remove-azdatalakegen2aclrecursive) .

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Rimuovere le voci ACL usando il comando [AZ storage FS Access Remove-ricorsivo](https://docs.microsoft.com/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) . 

In questo esempio viene rimossa una voce ACL dalla directory radice del contenitore.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se si desidera rimuovere una voce ACL **predefinita** , aggiungere il prefisso `default:` a ogni voce. Ad esempio, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`

### <a name="net"></a>[.NET](#tab/dotnet)

Rimuovere le voci ACL chiamando il metodo **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** . Passare questo metodo a un [elenco](/dotnet/api/system.collections.generic.list-1) di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Ogni [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definisce una voce ACL. 

Se si vuole rimuovere una voce ACL **predefinita** , è possibile impostare la proprietà [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) su **true**. 

In questo esempio viene rimossa una voce ACL dall'ACL della directory denominata `my-parent-directory` . Questo metodo accetta un parametro booleano denominato `isDefaultScope` che specifica se rimuovere la voce dall'ACL predefinito. Tale parametro viene utilizzato nel costruttore di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient, isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

Per un esempio in cui gli ACL vengono rimossi in modo ricorsivo in batch specificando una dimensione del batch, vedere l' [esempio](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

### <a name="java"></a>[Java](#tab/java)

Rimuovere le voci ACL chiamando il metodo **DataLakeDirectoryClient. removeAccessControlRecursive** . Passare questo metodo a un [elenco](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) di oggetti [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Ogni [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definisce una voce ACL. 

Se si vuole rimuovere una voce ACL **predefinita** , è possibile il metodo **setDefaultScope** di [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passare il valore **true**.  

In questo esempio viene rimossa una voce ACL dall'ACL della directory denominata `my-parent-directory` . Questo metodo accetta un parametro booleano denominato `isDefaultScope` che specifica se rimuovere la voce dall'ACL predefinito. Tale parametro viene utilizzato nella chiamata al metodo **setDefaultScope** di [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html).


```java
static public void RemoveACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){

    DataLakeDirectoryClient directoryClient =
    fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathRemoveAccessControlEntry> pathRemoveAccessControlEntries = 
        new ArrayList<PathRemoveAccessControlEntry>();

    // Create named user entry.
    PathRemoveAccessControlEntry userEntry = new PathRemoveAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"); 
    
    pathRemoveAccessControlEntries.add(userEntry);
    
    directoryClient.removeAccessControlRecursive(pathRemoveAccessControlEntries);      

}
```

### <a name="python"></a>[Python](#tab/python)

Rimuovere le voci ACL chiamando il metodo **DataLakeDirectoryClient.remove_access_control_recursive** . Se si desidera rimuovere una voce ACL **predefinita** , aggiungere la stringa `default:` all'inizio della stringa di immissione dell'ACL. 

In questo esempio viene rimossa una voce ACL dall'ACL della directory denominata `my-parent-directory` . Questo metodo accetta un parametro booleano denominato `is_default_scope` che specifica se rimuovere la voce dall'ACL predefinito. Se il parametro è `True` , la voce ACL aggiornata è preceduta dalla stringa `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Per un esempio che elabora gli ACL in modo ricorsivo in batch specificando una dimensione del batch, vedere l' [esempio](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Python.

---

## <a name="recover-from-failures"></a>Ripristino da errori

Potrebbero verificarsi errori di runtime o di autorizzazione. Per gli errori di runtime, riavviare il processo dall'inizio. Possono verificarsi errori di autorizzazione se l'entità di sicurezza non dispone di autorizzazioni sufficienti per modificare l'ACL di una directory o di un file che si trova nella gerarchia di directory da modificare. Risolvere il problema di autorizzazione, quindi scegliere di riprendere il processo dal punto in cui si è verificato l'errore usando un token di continuazione oppure riavviare il processo dall'inizio. Non è necessario usare il token di continuazione se si preferisce riavviare dall'inizio. È possibile riapplicare le voci ACL senza alcun impatto negativo.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

In questo esempio vengono restituiti i risultati alla variabile, quindi le voci non riuscite di pipe in una tabella formattata.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

In base all'output della tabella, è possibile correggere eventuali errori di autorizzazione e quindi riprendere l'esecuzione usando il token di continuazione.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

Per vedere un esempio che imposta gli ACL in modo ricorsivo nei batch specificando una dimensione del batch, vedere l'articolo di riferimento [set-AzDataLakeGen2AclRecursive](https://docs.microsoft.com/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

In caso di errore, è possibile restituire un token di continuazione impostando il `--continue-on-failure` parametro su `false` . Dopo aver indirizzato gli errori, è possibile riprendere il processo dal punto in cui si è verificato l'errore eseguendo di nuovo il comando e quindi impostando il `--continuation` parametro sul token di continuazione. 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

## <a name="net"></a>[.NET](#tab/dotnet)

In questo esempio viene restituito un token di continuazione in caso di errore. L'applicazione può chiamare nuovamente questo metodo di esempio dopo che l'errore è stato risolto e passare il token di continuazione. Se questo metodo di esempio viene chiamato per la prima volta, l'applicazione può passare un valore `null` per il parametro del token di continuazione. 

```cs
public async Task<string> ResumeAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient,
    List<PathAccessControlItem> accessControlList, 
    string continuationToken)
{
    try
    {
        var accessControlChangeResult =
            await directoryClient.SetAccessControlRecursiveAsync(
                accessControlList, continuationToken: continuationToken, null);

        if (accessControlChangeResult.Value.Counters.FailedChangesCount > 0)
        {
            continuationToken =
                accessControlChangeResult.Value.ContinuationToken;
        }

        return continuationToken;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
        return continuationToken;
    }

}
```

Per un esempio che imposta gli ACL in modo ricorsivo nei batch specificando una dimensione del batch, vedere l' [esempio](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

### <a name="java"></a>[Java](#tab/java)

In questo esempio viene restituito un token di continuazione in caso di errore. L'applicazione può chiamare nuovamente questo metodo di esempio dopo che l'errore è stato risolto e passare il token di continuazione. Se questo metodo di esempio viene chiamato per la prima volta, l'applicazione può passare un valore `null` per il parametro del token di continuazione. 

```java
static public String ResumeSetACLRecursively(DataLakeFileSystemClient fileSystemClient,
DataLakeDirectoryClient directoryClient,
List<PathAccessControlEntry> accessControlList, 
String continuationToken){

    try{
        PathSetAccessControlRecursiveOptions options = new PathSetAccessControlRecursiveOptions(accessControlList);
        
        options.setContinuationToken(continuationToken);
    
        Response<AccessControlChangeResult> accessControlChangeResult =  
            directoryClient.setAccessControlRecursiveWithResponse(options, null, null);

        if (accessControlChangeResult.getValue().getCounters().getFailedChangesCount() > 0)
        {
            continuationToken =
                accessControlChangeResult.getValue().getContinuationToken();
        }
    
        return continuationToken;

    }
    catch(Exception ex){
    
        System.out.println(ex.toString());
        return continuationToken;
    }
}
```

### <a name="python"></a>[Python](#tab/python)

In questo esempio viene restituito un token di continuazione in caso di errore. L'applicazione può chiamare nuovamente questo metodo di esempio dopo che l'errore è stato risolto e passare il token di continuazione. Se questo metodo di esempio viene chiamato per la prima volta, l'applicazione può passare un valore ``None`` per il parametro del token di continuazione. 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

Per un esempio che elabora gli ACL in modo ricorsivo in batch specificando una dimensione del batch, vedere l' [esempio](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Python.

---

Se si desidera che il processo venga completato senza interruzioni da errori di autorizzazione, è possibile specificarlo.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

In questo esempio viene usato il `ContinueOnFailure` parametro in modo che l'esecuzione continui anche se l'operazione rileva un errore di autorizzazione. 

```powershell

$TotalDirectoriesSuccess = 0
$TotalFilesSuccess = 0
$totalFailure = 0
$FailedEntries = New-Object System.Collections.Generic.List[System.Object]

$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

Per vedere un esempio che imposta gli ACL in modo ricorsivo nei batch specificando una dimensione del batch, vedere l'articolo di riferimento [set-AzDataLakeGen2AclRecursive](https://docs.microsoft.com/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per assicurarsi che il processo venga completato senza interruzioni, impostare il `--continue-on-failure` parametro su `true` . 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

### <a name="net"></a>[.NET](#tab/dotnet)

Per assicurarsi che il processo venga completato senza interruzioni, passare un oggetto **AccessControlChangedOptions** e impostare la proprietà **ContinueOnFailure** dell'oggetto su ``true`` .

In questo esempio le voci ACL vengono impostate in modo ricorsivo. Se il codice rileva un errore di autorizzazione, registra l'errore e continua l'esecuzione. In questo esempio viene stampato il numero di errori nella console. 

```cs
public async Task ContinueOnFailureAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient, 
    List<PathAccessControlItem> accessControlList)
{
    var accessControlChangeResult = 
        await directoryClient.SetAccessControlRecursiveAsync(
            accessControlList, null, new AccessControlChangeOptions() 
            { ContinueOnFailure = true });

    var counters = accessControlChangeResult.Value.Counters;

    Console.WriteLine("Number of directories changed: " +
        counters.ChangedDirectoriesCount.ToString());

    Console.WriteLine("Number of files changed: " +
        counters.ChangedFilesCount.ToString());

    Console.WriteLine("Number of failures: " +
        counters.FailedChangesCount.ToString());
}
```

Per un esempio che imposta gli ACL in modo ricorsivo nei batch specificando una dimensione del batch, vedere l' [esempio](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

### <a name="java"></a>[Java](#tab/java)

Per assicurarsi che il processo venga completato senza interruzioni, chiamare il metodo **setContinueOnFailure** di un oggetto [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passare il valore **true**.

In questo esempio le voci ACL vengono impostate in modo ricorsivo. Se il codice rileva un errore di autorizzazione, registra l'errore e continua l'esecuzione. In questo esempio viene stampato il numero di errori nella console. 

```java
static public void ContinueOnFailure(DataLakeFileSystemClient fileSystemClient,
DataLakeDirectoryClient directoryClient,
List<PathAccessControlEntry> accessControlList){
    
    PathSetAccessControlRecursiveOptions options = 
        new PathSetAccessControlRecursiveOptions(accessControlList);
        
    options.setContinueOnFailure(true);
    
    Response<AccessControlChangeResult> accessControlChangeResult =  
        directoryClient.setAccessControlRecursiveWithResponse(options, null, null);

    AccessControlChangeCounters counters = accessControlChangeResult.getValue().getCounters();

    System.out.println("Number of directories changes: " + 
        counters.getChangedDirectoriesCount());

    System.out.println("Number of files changed: " + 
        counters.getChangedDirectoriesCount());

    System.out.println("Number of failures: " + 
        counters.getChangedDirectoriesCount());
}
```

### <a name="python"></a>[Python](#tab/python)

Per assicurarsi che il processo venga completato senza interruzioni, non passare un token di continuazione al metodo **DataLakeDirectoryClient.set_access_control_recursive** .

In questo esempio le voci ACL vengono impostate in modo ricorsivo. Se il codice rileva un errore di autorizzazione, registra l'errore e continua l'esecuzione. In questo esempio viene stampato il numero di errori nella console. 

```python
def continue_on_failure():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl)

        print("Summary: {} directories and {} files were updated successfully, {} failures were counted."
          .format(acl_change_result.counters.directories_successful, acl_change_result.counters.files_successful,
                  acl_change_result.counters.failure_count))
        
    except Exception as e:
     print(e)
```

Per un esempio che elabora gli ACL in modo ricorsivo in batch specificando una dimensione del batch, vedere l' [esempio](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Python.

---

## <a name="resources"></a>Risorse

Questa sezione contiene collegamenti a librerie ed esempi di codice.

#### <a name="libraries"></a>Librerie

- [PowerShell](https://www.powershellgallery.com/packages/Az.Storage/3.0.0)
- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/storage/fs/access)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Java](/java/api/overview/azure/storage-file-datalake-readme)
- [Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)

#### <a name="code-samples"></a>Esempi di codice

- PowerShell: [Readme](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)  |  [esempio](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D) Leggimi

- INTERFACCIA della riga di comando di Azure: [esempio](https://github.com/Azure/azure-cli/blob/2a55a5350696a3a93a13f364f2104ec8bc82cdd3/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)

- Esempio net: [Leggimi](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%252FSc2aDVW3De4A%252Fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)  |  [Sample](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [Readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)  |  [esempio](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py) Leggimi

## <a name="best-practice-guidelines"></a>Linee guida sulle procedure consigliate

In questa sezione vengono illustrate alcune procedure consigliate per l'impostazione di ACL in modo ricorsivo. 

#### <a name="handling-runtime-errors"></a>Gestione degli errori di runtime

È possibile che si verifichi un errore di runtime per diversi motivi, ad esempio un'interruzione o un problema di connettività client. Se si verifica un errore di runtime, riavviare il processo ACL ricorsivo. Gli ACL possono essere riapplicati agli elementi senza causare un impatto negativo. 

#### <a name="handling-permission-errors-403"></a>Gestione degli errori di autorizzazione (403)

Se si verifica un'eccezione di controllo di accesso durante l'esecuzione di un processo ACL ricorsivo, è possibile che l' [entità di sicurezza](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) di Active Directory non disponga di autorizzazioni sufficienti per applicare un ACL a uno o più elementi figlio nella gerarchia di directory. Quando si verifica un errore di autorizzazione, il processo viene arrestato e viene fornito un token di continuazione. Correggere il problema di autorizzazione e quindi usare il token di continuazione per elaborare il set di dati rimanente. Non sarà necessario elaborare nuovamente le directory e i file che sono già stati elaborati correttamente. È anche possibile scegliere di riavviare il processo ACL ricorsivo. Gli ACL possono essere riapplicati agli elementi senza causare un impatto negativo. 

#### <a name="credentials"></a>Credenziali 

Si consiglia di effettuare il provisioning di un Azure AD entità di sicurezza a cui è stato assegnato il ruolo di [proprietario dei dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) nell'ambito del contenitore o dell'account di archiviazione di destinazione. 

#### <a name="performance"></a>Prestazioni 

Per ridurre la latenza, è consigliabile eseguire il processo ACL ricorsivo in una macchina virtuale (VM) di Azure che si trova nella stessa area dell'account di archiviazione. 

#### <a name="acl-limits"></a>Limiti ACL

Il numero massimo di ACL che è possibile applicare a una directory o a un file è 32 ACL di accesso e 32 ACL predefiniti. Per altre informazioni, vedere [Access control in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) (Controllo di accesso in Azure Data Lake Storage Gen2).

<a id="provide-feedback"></a>

### <a name="provide-feedback-or-report-issues"></a>Inviare commenti e suggerimenti o segnalare problemi

È possibile fornire commenti e suggerimenti o segnalare un problema all'indirizzo  [recursiveACLfeedback@microsoft.com](mailto:recursiveACLfeedback@microsoft.com) .

## <a name="see-also"></a>Vedere anche

- [Controllo di accesso in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
- [Problemi noti](data-lake-storage-known-issues.md)


