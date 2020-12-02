---
title: "Esercitazione: Accesso di un'app Web all'archiviazione usando identità gestite | Azure"
description: Questa esercitazione illustra come accedere ad Archiviazione di Azure per un'app usando identità gestite.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: 250e95b33b985aedcc1b1537f57338d29e848451
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "96020212"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>Esercitazione: Accedere ad Archiviazione di Azure da un'app Web

Informazioni su come accedere ad Archiviazione di Azure per un'app Web (non un utente connesso) in esecuzione nel servizio app di Azure usando identità gestite.

:::image type="content" alt-text="Diagramma che mostra come accedere all'archiviazione." source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

Si vuole aggiungere l'accesso al piano dati di Azure, ovvero Archiviazione di Azure, Database SQL di Azure, Azure Key Vault o altri servizi, dall'app Web. È possibile usare una chiave condivisa, ma in questo caso sarebbe necessario preoccuparsi della sicurezza operativa degli utenti che possono creare, distribuire e gestire il segreto. La chiave potrebbe inoltre essere archiviata in GitHub e i pirati informatici potrebbero quindi individuarla facilmente. Un modo più sicuro per concedere all'app Web l'accesso ai dati consiste nell'usare le [identità gestite](/azure/active-directory/managed-identities-azure-resources/overview).

Un'identità gestita di Azure Active Directory (Azure AD) consente al servizio app di accedere alle risorse tramite il controllo degli accessi in base al ruolo, senza richiedere le credenziali dell'app. Dopo aver assegnato un'identità gestita all'app Web, Azure si occupa della creazione e della distribuzione di un certificato. Gli utenti non devono preoccuparsi di gestire i segreti o le credenziali dell'app.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare un'identità gestita assegnata dal sistema in un'app Web.
> * Creare un account di archiviazione e un contenitore di Archiviazione BLOB di Azure.
> * Accedere all'archiviazione da un'app Web usando identità gestite.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Un'applicazione Web in esecuzione nel Servizio app di Azure con il [modulo di autenticazione/autorizzazione del servizio app abilitato](scenario-secure-app-authentication-app-service.md).

## <a name="enable-managed-identity-on-an-app"></a>Abilitare l'identità gestita in un'app

Se si crea e si pubblica l'app Web tramite Visual Studio, l'identità gestita è già stata abilitata automaticamente nell'app. Nel servizio app selezionare **Identità** nel riquadro sinistro e quindi selezionare **Assegnata dal sistema**. Verificare che lo **Stato** sia impostato su **Sì**. In caso contrario, selezionare **Salva** e quindi **Sì** per abilitare l'identità gestita assegnata dal sistema. Una volta abilitata l'identità gestita, lo stato è impostato su **Sì** e l'ID oggetto è disponibile.

:::image type="content" alt-text="Screenshot che mostra opzione dell'identità gestita assegnata dal sistema." source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

Questo passaggio crea un nuovo ID oggetto, diverso dall'ID app creato nel riquadro **Autenticazione/Autorizzazione**. Copiare l'ID oggetto dell'identità gestita assegnata dal sistema. Sarà necessario più avanti.

## <a name="create-a-storage-account-and-blob-storage-container"></a>Creare un account di archiviazione e un contenitore di Archiviazione BLOB

A questo punto si è pronti per creare un account di archiviazione e un contenitore di Archiviazione BLOB.

Ogni account di archiviazione deve appartenere a un gruppo di risorse di Azure. Un gruppo di risorse è un contenitore logico per raggruppare i servizi di Azure. Quando si crea un account di archiviazione, è possibile creare un nuovo gruppo di risorse o usarne uno esistente. Questo articolo illustra come creare un nuovo gruppo di risorse.

Un account di archiviazione per utilizzo generico v2 consente l'accesso a tutti i servizi di Archiviazione di Azure: BLOB, file, code, tabelle e dischi. La procedura descritta qui crea un account di archiviazione per utilizzo generico v2, ma i passaggi per creare qualsiasi tipo di account di archiviazione sono simili.

I BLOB in Archiviazione di Azure sono organizzati in contenitori. Prima di poter caricare un BLOB più avanti in questa esercitazione, sarà necessario creare un contenitore.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per creare un account di archiviazione per utilizzo generico v2 nel portale di Azure, seguire questa procedura.

1. Nel menu del portale di Azure selezionare **Tutti i servizi**. Nell'elenco delle risorse immettere **Account di archiviazione**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Account di archiviazione**.

1. Nella finestra **Account di archiviazione** visualizzata selezionare **Aggiungi**.

1. Selezionare la sottoscrizione in cui creare l'account di archiviazione.

1. Nel menu a discesa del campo **Gruppo di risorse** selezionare il gruppo di risorse che contiene l'app Web.

1. Immettere quindi un nome per l'account di archiviazione. Il nome scelto deve essere univoco in Azure. Deve avere inoltre una lunghezza compresa tra 3 e 24 caratteri e può includere solo numeri e lettere minuscole.

1. Selezionare la località per l'account di archiviazione o usare la località predefinita.

1. Mantenere i valori predefiniti per questi campi:

    |Campo|valore|
    |--|--|
    |Modello di distribuzione|Gestione risorse|
    |Prestazioni|Standard|
    |Tipologia account|Archiviazione v2 (utilizzo generico V2)|
    |Replica|Archiviazione con ridondanza geografica e accesso in lettura|
    |Livello di accesso|Accesso frequente|

1. Selezionare **Rivedi e crea** per esaminare le impostazioni dell'account di archiviazione e creare l'account.

1. Selezionare **Crea**.

Per creare un contenitore di Archiviazione BLOB in Archiviazione di Azure, seguire questa procedura.

1. Passare al nuovo account di archiviazione nel portale di Azure.

1. Nel menu sinistro per l'account di archiviazione scorrere fino alla sezione **Servizio BLOB** e quindi selezionare **Contenitori**.

1. Selezionare il pulsante **+ Contenitore**.

1. Immettere un nome per il nuovo contenitore. Il nome del contenitore deve essere composto da lettere minuscole, deve iniziare con una lettera o un numero e può contenere solo lettere, numeri e trattino (-).

1. Impostare il livello di accesso pubblico del contenitore. Il livello predefinito è **Private (no anonymous access)** (Privato - nessun accesso anonimo).

1. Fare clic su **OK** per creare il contenitore.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per creare un account di archiviazione per utilizzo generico v2 e un contenitore di Archiviazione BLOB, eseguire lo script seguente. Specificare il nome del gruppo di risorse che contiene l'app Web. Immettere un nome per l'account di archiviazione. Il nome scelto deve essere univoco in Azure. Deve avere inoltre una lunghezza compresa tra 3 e 24 caratteri e può includere solo numeri e lettere minuscole.

Specificare la località dell'account di archiviazione. Per visualizzare un elenco delle località valide per la sottoscrizione, eseguire ```Get-AzLocation | select Location```. Il nome del contenitore deve essere composto da lettere minuscole, deve iniziare con una lettera o un numero e può contenere solo lettere, numeri e trattino (-).

Ricordare di sostituire i valori segnaposto tra parentesi acute con i valori personalizzati.

```powershell
Connect-AzAccount

$resourceGroup = "securewebappresourcegroup"
$location = "<location>"
$storageName="securewebappstorage"
$containerName = "securewebappblobcontainer"

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per creare un account di archiviazione per utilizzo generico v2 e un contenitore di Archiviazione BLOB, eseguire lo script seguente. Specificare il nome del gruppo di risorse che contiene l'app Web. Immettere un nome per l'account di archiviazione. Il nome scelto deve essere univoco in Azure. Deve avere inoltre una lunghezza compresa tra 3 e 24 caratteri e può includere solo numeri e lettere minuscole. 

Specificare la località dell'account di archiviazione. Il nome del contenitore deve essere composto da lettere minuscole, deve iniziare con una lettera o un numero e può contenere solo lettere, numeri e trattino (-).

Nell'esempio seguente viene usato l'account Azure AD per autorizzare l'operazione di creazione del contenitore. Prima di creare il contenitore, assegnare il ruolo Collaboratore ai dati dei BLOB di archiviazione a se stessi. Anche se si è il proprietario dell'account, sono necessarie autorizzazioni esplicite per eseguire operazioni sui dati nell'account di archiviazione.

Ricordare di sostituire i valori segnaposto tra parentesi acute con i valori personalizzati.

```azurecli-interactive
az login

az storage account create \
    --name securewebappstorage \
    --resource-group securewebappresourcegroup \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope $storageId

az storage container create \
    --account-name securewebappstorage \
    --name securewebappblobcontainer \
    --auth-mode login
```

---

## <a name="grant-access-to-the-storage-account"></a>Concedere l'accesso all'account di archiviazione

Prima di poter creare, leggere o eliminare i BLOB, è necessario concedere all'app Web l'accesso all'account di archiviazione. In un passaggio precedente è stata configurata l'app Web in esecuzione nel servizio app con un'identità gestita. Usando il Controllo degli accessi in base al ruolo di Azure è possibile concedere all'identità gestita l'accesso a un'altra risorsa, come per qualsiasi entità di sicurezza. Il ruolo Collaboratore ai dati dei BLOB di archiviazione fornisce all'app Web, rappresentata dall'identità gestita assegnata dal sistema, l'accesso in lettura, scrittura ed eliminazione ai dati e al contenitore BLOB.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione per concedere l'accesso all'app Web. Selezionare **Controllo di accesso (IAM)** e quindi **Assegnazioni di ruolo**. Verrà visualizzato un elenco di utenti che hanno accesso all'account di archiviazione. A questo punto si aggiungerà un'assegnazione di ruolo a un robot, il servizio app deve accedere all'account di archiviazione. Selezionare **Aggiungi** > **Aggiungi assegnazione di ruolo**.

In **Ruolo** selezionare **Collaboratore ai dati dei BLOB di archiviazione** per concedere all'app Web l'accesso in lettura ai BLOB di archiviazione. In **Assegna accesso a** selezionare **Servizio app**. In **Sottoscrizione** selezionare la propria sottoscrizione. Selezionare quindi il servizio app a cui fornire l'accesso. Selezionare **Salva**.

:::image type="content" alt-text="Screenshot che mostra la schermata Aggiungi assegnazione di ruolo." source="./media/scenario-secure-app-access-storage/add-role-assignment.png":::

L'app Web può ora accedere all'account di archiviazione.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Eseguire lo script seguente per assegnare all'app Web, rappresentata da un'identità gestita assegnata dal sistema, il ruolo Collaboratore ai dati dei BLOB di archiviazione nell'account di archiviazione.

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Eseguire lo script seguente per assegnare all'app Web, rappresentata da un'identità gestita assegnata dal sistema, il ruolo Collaboratore ai dati dei BLOB di archiviazione nell'account di archiviazione.

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>Accedere ad Archiviazione BLOB (.NET)

La classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) viene usata per ottenere le credenziali del token affinché il codice autorizzi le richieste al servizio di archiviazione di Azure. Creare un'istanza della classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential), che usa l'identità gestita per recuperare i token e collegarli al client del servizio. L'esempio di codice seguente ottiene le credenziali del token autenticato e le usa per creare un oggetto client del servizio, che carica un nuovo BLOB.

### <a name="install-client-library-packages"></a>Installare i pacchetti della libreria client

Installare il [pacchetto NuGet di Archiviazione BLOB](https://www.nuget.org/packages/Azure.Storage.Blobs/) per usare Archiviazione BLOB e il [pacchetto NuGet della libreria client Azure Identity per .NET](https://www.nuget.org/packages/Azure.Identity/) per eseguire l'autenticazione con le credenziali di Azure AD. Installare le librerie client usando l'interfaccia della riga di comando di .NET Core o la console di Gestione pacchetti in Visual Studio.

# <a name="command-line"></a>[Riga di comando](#tab/command-line)

Aprire una riga di comando e passare alla directory che contiene il file di progetto.

Eseguire i comandi di installazione.

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[Gestione pacchetti](#tab/package-manager)

Aprire il progetto o la soluzione in Visual Studio, quindi aprire la console selezionando **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.

Eseguire i comandi di installazione.
```powershell
Install-Package Azure.Storage.Blobs

Install-Package Azure.Identity
```

---

### <a name="example"></a>Esempio

```csharp
using System;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;
using System.IO;
using Azure.Identity;

// Some code omitted for brevity.

static public async Task UploadBlob(string accountName, string containerName, string blobName, string blobContents)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (Exception e)
    {
        throw e;
    }
}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se l'esercitazione è stata completata e l'app Web o le risorse associate non sono più necessarie, [pulire le risorse create](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare un'identità gestita assegnata dal sistema.
> * Creare un account di archiviazione e un contenitore di Archiviazione BLOB.
> * Accedere all'archiviazione da un'app Web usando identità gestite.

> [!div class="nextstepaction"]
> [Il servizio app accede a Microsoft Graph per conto dell'utente](scenario-secure-app-access-microsoft-graph-as-user.md)
