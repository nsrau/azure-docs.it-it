---
title: Impostare gli ACL in modo ricorsivo per Azure Data Lake Storage Gen2 | Microsoft Docs
description: È possibile aggiungere, aggiornare e rimuovere gli elenchi di controllo di accesso in modo ricorsivo per gli elementi figlio esistenti di una directory padre.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 10/06/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: f9f0983bdb5e8763d13eeab8ea21bef7fb9ef47f
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803331"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Impostare gli elenchi di controllo di accesso (ACL) in modo ricorsivo per Azure Data Lake Storage Gen2

L'ereditarietà ACL è già disponibile per i nuovi elementi figlio creati in una directory padre. È anche possibile aggiungere, aggiornare e rimuovere gli ACL in modo ricorsivo per gli elementi figlio esistenti di una directory padre senza dover apportare queste modifiche singolarmente per ogni elemento figlio.

> [!NOTE]
> La possibilità di impostare elenchi di accesso in modo ricorsivo è in anteprima pubblica ed è disponibile in tutte le aree.  

[Librerie](#libraries)  |  di [Esempi](#code-samples)  |  di [Procedure](#best-practice-guidelines)  |  consigliate [Invia commenti e suggerimenti](#provide-feedback)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni.

- Le autorizzazioni corrette per eseguire il processo ACL ricorsivo. L'autorizzazione corretta include uno degli elementi seguenti: 

  - [Entità di sicurezza](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) con provisioning Azure Active Directory (ad) a cui è stato assegnato il ruolo di [proprietario dei dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) nell'ambito del contenitore di destinazione, del gruppo di risorse padre o della sottoscrizione.   

  - Utente proprietario del contenitore o della directory di destinazione a cui si intende applicare il processo ACL ricorsivo. Sono inclusi tutti gli elementi figlio nel contenitore o nella directory di destinazione. 

- Informazioni sul modo in cui gli ACL vengono applicati alle directory e ai file. Vedere [controllo di accesso in Azure Data Lake storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Vedere la sezione **configurare il progetto** di questo articolo per visualizzare le linee guida per l'installazione di PowerShell, .NET SDK e Python SDK.

## <a name="set-up-your-project"></a>Impostare il progetto

Installare le librerie necessarie.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Verificare che sia installato .NET Framework. Vedere [scaricare .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
2. Verificare che la versione di PowerShell installata sia `5.1` o superiore usando il comando seguente.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Per aggiornare la versione di PowerShell, vedere [aggiornamento di Windows PowerShell esistente](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell)
    
3. Installare la versione più recente del modulo PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

4. Chiudere e riaprire la console di PowerShell.

5. Installare il modulo **AZ. storage** Preview.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Per altre informazioni su come installare i moduli di PowerShell, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

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

### <a name="python"></a>[Python](#tab/python)

1. Scaricare la [libreria client di Azure Data Lake storage per Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0).

2. Installare la libreria scaricata usando [PIP](https://pypi.org/project/pip/).

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

Aggiungere queste istruzioni Import all'inizio del file di codice.

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

Impostare un ACL in modo ricorsivo utilizzando il `Set-AzDataLakeGen2AclRecursive` cmdlet.

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

### <a name="net"></a>[.NET](#tab/dotnet)

Impostare un ACL in modo ricorsivo chiamando il metodo **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** . Passare questo metodo a un [elenco](/dotnet/api/system.collections.generic.list-1) di [PathAccessControlItems](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Ogni [PathAccessControlItems](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definisce una voce ACL.

In questo esempio viene impostato l'ACL di una directory denominata `my-parent-directory` . Queste voci forniscono le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e offre a tutti gli altri utenti l'accesso. L'ultima voce ACL in questo esempio fornisce a un utente specifico l'ID oggetto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "autorizzazioni di lettura ed esecuzione.

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient)
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
            RolePermissions.Execute),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

### <a name="python"></a>[Python](#tab/python)

Impostare un ACL in modo ricorsivo chiamando il metodo **DataLakeDirectoryClient.set_access_control_recursive** .

In questo esempio viene impostato l'ACL di una directory denominata `my-parent-directory` . Queste voci forniscono le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e offre a tutti gli altri utenti l'accesso. L'ultima voce ACL in questo esempio fornisce a un utente specifico l'ID oggetto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "autorizzazioni di lettura ed esecuzione.

```python
def set_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

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

### <a name="net"></a>[.NET](#tab/dotnet)

Aggiornare un ACL in modo ricorsivo chiamando il metodo **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** . 

Questo esempio aggiorna una voce ACL con autorizzazione di scrittura. 

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient)
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
            RolePermissions.Execute, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

### <a name="python"></a>[Python](#tab/python)

Aggiornare un ACL in modo ricorsivo chiamando il metodo **DataLakeDirectoryClient.update_access_control_recursive** . 

Questo esempio aggiorna una voce ACL con autorizzazione di scrittura. 

```python
def update_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

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

### <a name="net"></a>[.NET](#tab/dotnet)

Rimuovere le voci ACL chiamando il metodo **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** . 

In questo esempio viene rimossa una voce ACL dall'ACL della directory denominata `my-parent-directory` . 

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

### <a name="python"></a>[Python](#tab/python)

Rimuovere le voci ACL chiamando il metodo **DataLakeDirectoryClient.remove_access_control_recursive** . 

In questo esempio viene rimossa una voce ACL dall'ACL della directory denominata `my-parent-directory` . 

```python
def remove_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:4a9028cf-f779-4032-b09d-970ebe3db258'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

---

## <a name="recover-from-failures"></a>Ripristino da errori

Potrebbero verificarsi errori di runtime o di autorizzazione. Per gli errori di runtime, riavviare il processo dall'inizio. Possono verificarsi errori di autorizzazione se l'entità di sicurezza non dispone di autorizzazioni sufficienti per modificare l'ACL di una directory o di un file che si trova nella gerarchia di directory da modificare. Risolvere il problema di autorizzazione, quindi scegliere di riprendere il processo dal punto in cui si è verificato l'errore usando un token di continuazione oppure riavviare il processo dall'inizio. Non è necessario usare il token di continuazione se si preferisce riavviare dall'inizio. È possibile riapplicare le voci ACL senza alcun impatto negativo.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Restituisce i risultati alla variabile. Inviare le voci non riuscite a una tabella formattata.

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

## <a name="net"></a>[.NET](#tab/dotnet)

In questo esempio viene restituito un token di continuazione in caso di errore. L'applicazione può chiamare nuovamente questo metodo di esempio dopo che l'errore è stato risolto e passare il token di continuazione. Se questo metodo di esempio viene chiamato per la prima volta, l'applicazione può passare un valore ``null`` per il parametro del token di continuazione. 

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

---

## <a name="resources"></a>Risorse

Questa sezione contiene collegamenti a librerie ed esempi di codice.

#### <a name="libraries"></a>Librerie

- [PowerShell](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.powershellgallery.com%2Fpackages%2FAz.Storage%2F2.5.2-preview&data=02%7C01%7Cnormesta%40microsoft.com%7Ccdabce06132c42132b4008d849a2dfb1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637340311173215017&sdata=FWynO9UKTt7ESMCFgkWaL7J%2F%2BjODaRo5BD6G6yCx9os%3D&reserved=0)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)

#### <a name="code-samples"></a>Esempi di codice

- PowerShell: [Readme](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)  |  [esempio](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D) Leggimi

- Esempio net: [Leggimi](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%252FSc2aDVW3De4A%252Fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)  |  [Sample](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [Readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)  |  [esempio](https://recursiveaclpr.blob.core.windows.net/privatedrop/datalake_samples_access_control_async.py?sv=2019-02-02&st=2020-08-24T07%3A48%3A10Z&se=2021-08-25T07%3A48%3A00Z&sr=b&sp=r&sig=%2F1c540%2BpXYyNcuTmWPWHg2m9SyClXLIMw7ChLZGsyD0%3D) Leggimi

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


