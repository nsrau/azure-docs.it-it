---
title: Usare un utente assegnato MSI in una VM Linux per accedere all'archiviazione di Azure
description: "Un'esercitazione che illustra il processo di utilizzo di un utente assegnato gestiti servizio identità (MSI) in una VM Linux per accedere all'archiviazione di Azure."
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
ms.openlocfilehash: 5c02eb32fd12e28d79dcc0340811f4ced48ed4be
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>Usare un utente assegnato gestiti servizio identità (MSI) in una VM Linux per accedere all'archiviazione di Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

In questa esercitazione viene illustrato come creare e usare un utente assegnato gestiti servizio identità (MSI) da una macchina virtuale Linux, quindi utilizzarlo per accedere all'archiviazione di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Creare un utente assegnato l'identità del servizio gestito (MSI)
> * Assegnare il file MSI utente assegnato a una macchina virtuale Linux
> * Concedere l'accesso a file MSI in un'istanza di archiviazione di Azure
> * Ottenere un token di accesso usando l'identità MSI assegnati dall'utente e usarlo per accedere all'archiviazione di Azure

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Per eseguire gli esempi di script CLI in questa esercitazione, sono disponibili due opzioni:

- Utilizzare [Azure Cloud Shell](~/articles/cloud-shell/overview.md) dal portale di Azure o tramite il pulsante "Provalo", si trova nell'angolo superiore destro di ogni blocco di codice.
- [Installare la versione più recente di CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 o versione successiva) se si preferisce utilizzare una console locale di CLI.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Linux in un nuovo gruppo di risorse

È innanzitutto necessario creare una nuova VM Linux. Se si preferisce, è anche possibile abilitare MSI in una macchina virtuale esistente.

1. Fare clic sul pulsante **+/Crea nuovo servizio** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Calcolo** e quindi **Ubuntu Server 16.04 LTS**.
3. Immettere le informazioni relative alla macchina virtuale. In **Tipo di autenticazione** selezionare **Chiave pubblica SSH** o **Password**. Le credenziali create consentono di eseguire l'accesso alla macchina virtuale.

    ![Testo immagine alt](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Dall'elenco a discesa **Sottoscrizione** selezionare una sottoscrizione per la macchina virtuale.
5. Per selezionare un nuovo **Gruppo di risorse** in cui creare la macchina virtuale, scegliere **Crea nuovo**. Al termine fare clic su **OK**.
6. Selezionare la dimensione della macchina virtuale. Per visualizzare altre dimensioni, selezionare **View all** (Visualizza tutto) o modificare il filtro Tipo di disco supportato. Nel pannello delle impostazioni mantenere le impostazioni predefinite e fare clic su **OK**.

## <a name="create-a-user-assigned-msi"></a>Creare un file MSI assegnati dall'utente

1. Se si utilizza la console CLI (invece di una sessione della Shell di Cloud di Azure), eseguire l'accesso a Azure. Utilizzare un account che viene associato alla sottoscrizione di Azure in cui si desidera creare il nuovo file MSI:

    ```azurecli
    az login
    ```

2. Creare un file MSI assegnati dall'utente tramite [identità az creare](/cli/azure/identity#az_identity_create). Il `-g` parametro specifica il gruppo di risorse in cui viene creato il file MSI, e `-n` parametro specifica il nome. Assicurarsi di sostituire il `<RESOURCE GROUP>` e `<MSI NAME>` i valori dei parametri con valori personalizzati:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    La risposta contiene i dettagli per il file MSI assegnati dall'utente creato, in modo analogo all'esempio seguente. Si noti il `id` valore per il file MSI, perché verrà usato nel passaggio successivo:

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

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Assegnare MSI assegnati dall'utente per le VM Linux

A differenza di un file MSI assegnato dal sistema, un file MSI assegnati dall'utente può essere utilizzato dai client su più risorse di Azure. Per questa esercitazione, vengono assegnati a una singola macchina virtuale. È inoltre possibile assegnare più di una macchina virtuale.

Assegnare il file MSI assegnati dall'utente per le VM Linux utilizzando [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). Assicurarsi di sostituire il `<RESOURCE GROUP>` e `<VM NAME>` i valori dei parametri con valori personalizzati. Utilizzare il `id` proprietà restituita nel passaggio precedente per il `--identities` valore del parametro:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> -–identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione 

Se si dispone già di uno, creare un account di archiviazione. È anche possibile ignorare questo passaggio e utilizzare un account di archiviazione esistente, se si preferisce. 

1. Fare clic sul pulsante **+/Crea nuovo servizio** nell'angolo superiore sinistro del portale di Azure.
2. Fare clic su **archiviazione**, quindi **Account di archiviazione**, e viene visualizzato un nuovo pannello "Creare account di archiviazione".
3. Immettere un **nome** per l'account di archiviazione, si utilizza in un secondo momento.  
4. **Modello di distribuzione** e **Tipologia account** devono essere impostati su "Gestione di risorse" e "Utilizzo generico". 
5. Verificare che le impostazioni in **Sottoscrizione** e **Gruppo di risorse** corrispondano a quelle specificate al momento della creazione della macchina virtuale nel passaggio precedente.
6. Fare clic su **Crea**.

    ![Creare un nuovo account di archiviazione](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Creare un contenitore BLOB nell'account di archiviazione

Poiché i file richiedono l'archiviazione blob, è necessario creare un contenitore blob in cui archiviare il file. Quindi caricare e scaricare un file per il contenitore blob, nel nuovo account di archiviazione.

1. Tornare all'account di archiviazione appena creato.
2. Fare clic sul collegamento **Contenitori** a sinistra, in "Servizio BLOB".
3. Fare clic su **+ Contenitore** nella parte superiore della pagina e verrà visualizzato il pannello "Nuovo contenitore".
4. Assegnare un nome al contenitore, selezionare un livello di accesso, quindi fare clic su **OK**. Il nome specificato viene utilizzato anche in un secondo momento nell'esercitazione. 

    ![Creare un contenitore di archiviazione](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Caricare un file per il contenitore appena creato facendo clic sul nome del contenitore, quindi **caricare**, quindi selezionare un file, quindi fare clic su **caricare**.

    ![Caricare file di testo](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>Concedere all'utente assegnato MSI l'accesso a un contenitore di archiviazione di Azure

Utilizzando un file MSI, il codice può ottenere i token di accesso per l'autenticazione a risorse che supportano l'autenticazione di Azure AD. In questa esercitazione, utilizzare l'archiviazione di Azure.

Innanzitutto si concede l'accesso di identità MSI a un contenitore di archiviazione di Azure. In questo caso, utilizzare il contenitore creato in precedenza. Aggiornare i valori per `<MSI CLIENTID>`, `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, e `<CONTAINER NAME>` come appropriato per l'ambiente. Sostituire `<CLIENT ID>` con il `clientId` restituito dalla proprietà di `az identity create` comando [creare un utente assegnato MSI](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI CLIENTID> --role ‘Reader’ --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/<CONTAINER NAME>"
```

La risposta include i dettagli per l'assegnazione di ruolo creato:

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

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>Ottenere un token di accesso utilizzando l'identità del MSI assegnati dall'utente e utilizzarlo per chiamare l'archiviazione di Azure

Per il resto dell'esercitazione, è necessario per la macchina virtuale creata in precedenza.

Per completare questi passaggi, è necessario disporre di un client SSH. Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about). Per richiedere assistenza nella configurazione delle chiavi del client SSH, vedere [Come usare le chiavi SSH con Windows in Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) o [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Nel portale di Azure passare a **Macchine virtuali**, selezionare la macchina virtuale Linux e quindi nella parte superiore della pagina **Panoramica** fare clic su **Connetti**. Copiare la stringa di connessione alla macchina virtuale.
2. **Connettersi** alla macchina virtuale usando un client SSH di propria scelta. 
3. Nella finestra terminal utilizzando CURL, effettuare una richiesta all'endpoint locale MSI per ottenere un accesso token per l'archiviazione di Azure.

   La richiesta CURL per acquisire un token di accesso viene visualizzata nell'esempio seguente. Assicurarsi di sostituire `<CLIENT ID>` con il `clientId` restituito dalla proprietà di `az identity create` comando [creare un utente assegnato MSI](#create-a-user-assigned-msi):

   ```bash
   curl -H Metadata:true "http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fstorage.azure.com/&client_id=<CLIENT ID>"
   ```

   > [!NOTE]
   > Nella richiesta precedente, il valore di `resource` parametro deve essere una corrispondenza esatta per le azioni previste da Azure AD. Quando si utilizza l'ID di risorsa di archiviazione di Azure, è necessario includere la barra finale nell'URI.
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

4. Ora è possibile utilizzare il token di accesso per accedere all'archiviazione di Azure, ad esempio per leggere il contenuto del file di esempio che è stato caricato in precedenza al contenitore. Sostituire i valori di `<STORAGE ACCOUNT>`, `<CONTAINER NAME>`, e `<FILE NAME>` con i valori specificati in precedenza, e `<ACCESS TOKEN>` con il token restituito nel passaggio precedente.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>"
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





