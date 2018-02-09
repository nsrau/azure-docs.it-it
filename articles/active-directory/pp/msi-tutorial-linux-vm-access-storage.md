---
title: "Usare un'identità del servizio gestito assegnata dall'utente su una macchina virtuale Linux per accedere ad Archiviazione di Azure"
description: "Questa esercitazione illustra come usare un'identità del servizio gestito assegnata dall'utente su una macchina virtuale Linux per accedere ad Archiviazione di Azure."
services: active-directory
documentationcenter: 
author: bryanLa
manager: mtillman
editor: arluca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4de290c2200aa3beffe277313d0b0b44a32d1fe5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>Usare un'identità del servizio gestito assegnata dall'utente su una macchina virtuale Linux per accedere ad Archiviazione di Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Questa esercitazione illustra come creare un'identità del servizio gestito assegnata dall'utente da una macchina virtuale Linux e quindi usarla per accedere ad Archiviazione di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Creare un'identità del servizio gestito assegnata dall'utente
> * Assegnare l'identità del servizio gestito assegnata dall'utente a una macchina virtuale Linux
> * Concedere all'identità del servizio gestito l'accesso a un'istanza di Archiviazione di Azure
> * Ottenere un token di accesso mediante l'identità del servizio gestito assegnata dall'utente e usarlo per accedere a un'istanza di Archiviazione di Azure

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Per eseguire gli esempi di script dell'interfaccia della riga di comando in questa esercitazione sono disponibili due opzioni:

- Usare [Azure Cloud Shell](~/articles/cloud-shell/overview.md) tramite il portale di Azure o il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice.
- [Installare la versione più recente dell'interfaccia della riga di comando 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Linux in un nuovo gruppo di risorse

Creare prima di tutto una nuova macchina virtuale Linux. In alternativa, è anche possibile abilitare l'identità del servizio gestito in una macchina virtuale esistente.

1. Fare clic sul pulsante **+/Crea nuovo servizio** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Calcolo** e quindi **Ubuntu Server 16.04 LTS**.
3. Immettere le informazioni relative alla macchina virtuale. In **Tipo di autenticazione** selezionare **Chiave pubblica SSH** o **Password**. Le credenziali create consentono di eseguire l'accesso alla macchina virtuale.

    ![Testo immagine alt](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Dall'elenco a discesa **Sottoscrizione** selezionare una sottoscrizione per la macchina virtuale.
5. Per selezionare un nuovo **Gruppo di risorse** in cui creare la macchina virtuale, scegliere **Crea nuovo**. Al termine fare clic su **OK**.
6. Selezionare la dimensione della macchina virtuale. Per visualizzare altre dimensioni, selezionare **View all** (Visualizza tutto) o modificare il filtro Tipo di disco supportato. Nel pannello delle impostazioni mantenere le impostazioni predefinite e fare clic su **OK**.

## <a name="create-a-user-assigned-msi"></a>Creare un'identità del servizio gestito assegnata dall'utente

1. Se si usa la console dell'interfaccia della riga di comando invece di una sessione di Azure Cloud Shell, eseguire l'accesso ad Azure. Usare un account associato alla sottoscrizione di Azure in cui si desidera creare una nuova identità del servizio gestito:

    ```azurecli
    az login
    ```

2. Creare un'identità del servizio gestito assegnata dall'utente tramite [az identity create](/cli/azure/identity#az_identity_create). Il parametro `-g` specifica il gruppo di risorse in cui viene creata l'identità del servizio gestito, mentre il parametro `-n` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<MSI NAME>` con valori personalizzati:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    La risposta contiene i dettagli relativi all'identità del servizio gestito assegnata dall'utente creata ed è simile all'esempio seguente. Prendere nota del valore della proprietà `id` dell'identità del servizio gestito perché verrà usato nel passaggio successivo:

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "9012",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Assegnare l'identità del servizio gestito assegnata dall'utente alla macchina virtuale Linux in uso

A differenza di un'identità del servizio gestito assegnata dal sistema, un'identità del servizio gestito assegnata dall'utente può essere usata dai client in più risorse di Azure. Ai fini della presente esercitazione assegnare tale entità a una singola macchina virtuale. È anche possibile assegnarla a più di una macchina virtuale.

Assegnare l'identità del servizio gestito assegnata dall'utente alla macchina virtuale Linux tramite [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VM NAME>` con valori personalizzati. Usare la proprietà `id` restituita nel passaggio precedente per il valore del parametro `--identities`:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione 

Se non ne è già disponibile uno, creare un account di archiviazione. È anche possibile ignorare questo passaggio e usare un account di archiviazione esistente, se si preferisce. 

1. Fare clic sul pulsante **+/Crea nuovo servizio** nell'angolo superiore sinistro del portale di Azure.
2. Fare clic su **Archiviazione** e quindi su **Account di archiviazione**. Viene visualizzato un nuovo pannello "Crea account di archiviazione".
3. Immettere un **nome** per l'account di archiviazione, che verrà usato in un secondo momento.  
4. **Modello di distribuzione** e **Tipologia account** devono essere impostati su "Gestione di risorse" e "Utilizzo generico". 
5. Verificare che le impostazioni in **Sottoscrizione** e **Gruppo di risorse** corrispondano a quelle specificate al momento della creazione della macchina virtuale nel passaggio precedente.
6. Fare clic su **Crea**.

    ![Creare un nuovo account di archiviazione](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Creare un contenitore BLOB nell'account di archiviazione

Poiché i file richiedono l'archiviazione BLOB, è necessario creare un contenitore BLOB in cui archiviare il file. Nel contenitore BLOB caricare e scaricare quindi un file con il nuovo account di archiviazione.

1. Tornare all'account di archiviazione appena creato.
2. Fare clic sul collegamento **Contenitori** a sinistra, in "Servizio BLOB".
3. Fare clic su **+ Contenitore** nella parte superiore della pagina e verrà visualizzato il pannello "Nuovo contenitore".
4. Assegnare un nome al contenitore, selezionare un livello di accesso, quindi fare clic su **OK**. Il nome specificato verrà usato più avanti nell'esercitazione. 

    ![Creare un contenitore di archiviazione](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Per caricare un file nel nuovo contenitore creato, fare clic sul nome del contenitore, scegliere **Carica**, selezionare un file e quindi fare clic su **Carica**.

    ![Caricare un file di testo](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>Concedere all'identità del servizio gestito assegnata dall'utente l'accesso a un contenitore di Archiviazione di Azure

Usando un'identità del servizio gestito, il codice può ottenere i token di accesso per autenticarsi alle risorse che supportano l'autenticazione di Azure AD. In questa esercitazione si userà Archiviazione di Azure.

Concedere all'identità del servizio gestito l'accesso a un contenitore di Archiviazione di Azure. In questo caso usare il contenitore creato in precedenza. Aggiornare i valori per `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>` e `<CONTAINER NAME>` in base all'ambiente in uso. Sostituire inoltre `<MSI PRINCIPALID>` con la proprietà `principalId` restituita dal comando `az identity create` in [Creare un'identità del servizio gestito assegnata dall'utente](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/containers/<CONTAINER NAME>"
```

La risposta include i dettagli relativi all'assegnazione di ruolo creata:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.storage/storageAccounts/<STORAGE ACCOUNT NAME>/blogServices/default/<CONTAINER NAME>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>Ottenere un token di accesso mediante l'identità del servizio gestito assegnata dall'utente e usarlo per eseguire una chiamata ad Archiviazione di Azure

Il resto dell'esercitazione prevede che le operazioni vengano svolte dalla macchina virtuale creata in precedenza.

Per completare questi passaggi, è necessario disporre di un client SSH. Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about). Per richiedere assistenza nella configurazione delle chiavi del client SSH, vedere [Come usare le chiavi SSH con Windows in Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) o [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Nel portale di Azure passare a **Macchine virtuali**, selezionare la macchina virtuale Linux e quindi nella parte superiore della pagina **Panoramica** fare clic su **Connetti**. Copiare la stringa di connessione alla macchina virtuale.
2. **Connettersi** alla macchina virtuale usando un client SSH di propria scelta. 
3. Nella finestra terminale usare CURL per eseguire una richiesta all'endpoint locale dell'identità del servizio gestito per ottenere un token di accesso per Archiviazione di Azure.

   La richiesta CURL per acquisire un token di accesso viene visualizzata nell'esempio seguente. Sostituire `<CLIENT ID>` con la proprietà `clientId` restituita dal comando `az identity create` in [Creare un'identità del servizio gestito assegnata dall'utente](#create-a-user-assigned-msi):

   ```bash
   curl -H Metadata:true "http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fstorage.azure.com/&client_id=<CLIENT ID>"
   ```

   > [!NOTE]
   > Nella richiesta precedente il valore del parametro `resource` deve corrispondere esattamente a quello previsto da Azure AD. Quando si usa l'ID risorsa di Archiviazione di Azure, è necessario includere la barra finale nell'URI.
   > Nella risposta seguente l'elemento access_token è stato abbreviato per comodità.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
   ```

4. A questo punto usare il token di accesso per accedere ad Archiviazione di Azure, ad esempio per leggere il contenuto del file di esempio caricato in precedenza nel contenitore. Sostituire i valori di `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` e `<FILE NAME>` con i valori specificati in precedenza, e `<ACCESS TOKEN>` con il token restituito nel passaggio precedente.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME>?api-version=2017-11-09 -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   La risposta contiene il contenuto del file:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dell'Identità di servizio gestito, vedere [Panoramica dell'Identità di servizio gestito](msi-overview.md).
- Per informazioni su come eseguire questa esercitazione usando le credenziali della firma di accesso condiviso allo spazio di archiviazione, vedere [Use a Linux VM Managed Service Identity to access Azure Storage via a SAS credential](msi-tutorial-linux-vm-access-storage-sas.md) (Usare un'identità del servizio gestito della macchina virtuale Linux per accedere ad Archiviazione di Azure tramite le credenziali della firma di accesso condiviso).
- Per altre informazioni sulla funzionalità della firma di accesso condiviso dell'account di Archiviazione di Azure, vedere:
  - [Uso delle firme di accesso condiviso](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Creazione di una firma di accesso condiviso del servizio](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.





