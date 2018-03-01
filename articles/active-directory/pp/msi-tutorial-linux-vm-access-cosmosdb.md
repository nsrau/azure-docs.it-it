---
title: "Usare un'identità del servizio gestito assegnata dall'utente su una macchina virtuale Linux per accedere ad Azure Cosmos DB"
description: "Questa esercitazione illustra come usare un'identità del servizio gestito assegnata dall'utente su una macchina virtuale Linux per accedere ad Azure Cosmos DB."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2018
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2c0c3597999e80af86f079385653d94ddfcab245
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-cosmos-db"></a>Usare un'identità del servizio gestito assegnata dall'utente su una macchina virtuale Linux per accedere ad Azure Cosmos DB 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Questa esercitazione illustra come creare e usare un'identità del servizio gestito assegnata dall'utente da una macchina virtuale Linux e quindi usarla per accedere ad Azure Cosmos DB. Si apprenderà come:

> [!div class="checklist"]
> * Creare un'identità del servizio gestito assegnata dall'utente
> * Assegnare l'identità del servizio gestito assegnata dall'utente a una macchina virtuale Linux
> * Concedere all'identità del servizio gestito l'accesso a un'istanza di Azure Cosmos DB
> * Ottenere un token di accesso mediante l'identità del servizio gestito assegnata dall'utente e usarlo per accedere ad Azure Cosmos DB

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Per eseguire gli esempi di script dell'interfaccia della riga di comando in questa esercitazione sono disponibili due opzioni:

- Usare [Azure Cloud Shell](~/articles/cloud-shell/overview.md) tramite il portale di Azure o il pulsante **Prova**, che si trova nell'angolo in alto a destra di ogni blocco di codice.
- [Installare la versione più recente dell'interfaccia della riga di comando 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Linux in un nuovo gruppo di risorse

Per questa esercitazione si creerà una nuova macchina virtuale Linux, ma è anche possibile abilitare l'identità del servizio gestito in una macchina virtuale esistente.

1. Fare clic su **+ Crea una risorsa** visualizzato nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Calcolo** e quindi **Ubuntu Server 16.04 LTS VM**.
3. Immettere le informazioni relative alla macchina virtuale. In **Tipo di autenticazione** selezionare **Chiave pubblica SSH** o **Password**. Le credenziali create consentono di eseguire l'accesso alla macchina virtuale.

    ![Macchina virtuale Linux con identità del servizio gestito](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Dall'elenco a discesa **Sottoscrizione** selezionare una sottoscrizione per la macchina virtuale.
5. In **Gruppo di risorse** scegliere **Crea nuovo** e digitare il nome del gruppo di risorse per questa macchina virtuale. Al termine fare clic su **OK**.
6. Selezionare la dimensione della macchina virtuale. Per visualizzare altre dimensioni, selezionare **View all** (Visualizza tutto) o modificare il filtro Tipo di disco supportato. Nel pannello **Impostazioni** mantenere le impostazioni predefinite e fare clic su **OK**.

## <a name="create-a-user-assigned-msi"></a>Creare un'identità del servizio gestito assegnata dall'utente

1. Se si usa la console dell'interfaccia della riga di comando invece di una sessione di Azure Cloud Shell, eseguire l'accesso ad Azure. Usare un account associato alla sottoscrizione di Azure in cui si desidera creare una nuova identità del servizio gestito:

    ```azurecli
    az login
    ```

2. Creare un'identità del servizio gestito assegnata dall'utente tramite [az identity create](/cli/azure/identity#az_identity_create). Il parametro `-g` specifica il gruppo di risorse in cui viene creata l'identità del servizio gestito, mentre il parametro `-n` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<MSI NAME>` con valori personalizzati:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    La risposta contiene i dettagli per l'identità del servizio gestito assegnata dall'utente creata, simile all'esempio seguente (si notino i valori `clientId` e `id` per l'identità del servizio gestito, in quanto verranno usati nei passaggi successivi):

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
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

## <a name="create-a-cosmos-db-account"></a>Creare un account Cosmos DB 

Se non ne è già disponibile uno, creare un account Cosmos DB. È anche possibile ignorare questo passaggio e usare un account Cosmos DB esistente, se si preferisce. 

1. Fare clic sul pulsante **+/Crea nuovo servizio** nell'angolo superiore sinistro del portale di Azure.
2. Fare clic su **Database**, quindi su **Azure Cosmos DB**. Verrà visualizzato un nuovo pannello "Nuovo account".
3. Immettere un **ID** per l'account Cosmos DB, da usare in un secondo momento.  
4. **API** deve essere impostato su "SQL". L'approccio descritto in questa esercitazione può essere usato con gli altri tipi di API disponibili, ma i passaggi illustrati si riferiscono all'API SQL.
5. Verificare che le impostazioni in **Sottoscrizione** e **Gruppo di risorse** corrispondano a quelle specificate al momento della creazione della macchina virtuale nel passaggio precedente.  Selezionare un **Percorso** in cui è disponibile Cosmos DB.
6. Fare clic su **Crea**.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Creare una raccolta nell'account Cosmos DB

Successivamente, aggiungere una raccolta di dati nell'account Cosmos DB su cui è possibile eseguire query nei passaggi successivi.

1. Passare all'account Cosmos DB appena creato.
2. Nella scheda **Panoramica** fare clic sul pulsante **+/Aggiungi raccolta**. Verrà visualizzato il pannello "Aggiungi raccolta".
3. Assegnare alla raccolta un ID database e un ID raccolta, selezionare una capacità di archiviazione, immettere una chiave di partizione, specificare un valore di velocità effettiva, quindi fare clic su **OK**.  Per questa esercitazione, è sufficiente usare "Test" come ID database e ID raccolta, selezionare una capacità di archiviazione fissa e una velocità effettiva più bassa (400 UR/sec).

## <a name="grant-your-user-assigned-msi-access-to-the-cosmos-db-account-access-keys"></a>Concedere all'identità del servizio gestito assegnata dall'utente l'accesso alle chiavi di accesso all'account Cosmos DB

Cosmos DB non supporta l'autenticazione di Azure AD in modo nativo.  È tuttavia possibile usare un'identità del servizio gestito per recuperare una chiave di accesso Cosmos DB da Gestione risorse e usarla per accedere a Cosmos DB.  In questo passaggio viene concesso all'identità del servizio gestito assegnata dall'utente l'accesso alle chiavi per l'account Cosmos DB.

Concedere all'identità del servizio gestito l'accesso all'account Cosmos DB in Azure Resource Manager mediante l'interfaccia della riga di comando di Azure. Aggiornare i valori per `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<COSMOS DB ACCOUNT NAME>` per l'ambiente in uso. Sostituire `<MSI PRINCIPALID>` con la proprietà `principalId` restituita dal comando `az identity create` in [Creare un'identità del servizio gestito assegnata dall'utente](#create-a-user-assigned-msi).  Cosmos DB supporta due livelli di granularità quando si usano le chiavi di accesso: accesso all'account in lettura/scrittura e in sola lettura.  Assegnare il ruolo `DocumentDB Account Contributor` se si desidera ottenere le chiavi di lettura/scrittura per l'account o assegnare il ruolo `Cosmos DB Account Reader Role` se si desidera ottenere le chiavi di sola lettura per l'account:

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

La risposta include i dettagli relativi all'assegnazione di ruolo creata:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msi-and-use-it-to-call-azure-resource-manager"></a>Ottenere un token di accesso usando l'identità del servizio gestito assegnata dall'utente e usarlo per chiamare Azure Resource Manager

La parte rimanente dell'esercitazione prevede che le operazioni vengano svolte dalla macchina virtuale creata in precedenza.

Per completare questi passaggi, è necessario disporre di un client SSH. Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Per richiedere assistenza nella configurazione delle chiavi del client SSH, vedere [Come usare le chiavi SSH con Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) o [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Nel portale di Azure passare a **Macchine virtuali**, selezionare la macchina virtuale Linux e quindi nella parte superiore della pagina **Panoramica** fare clic su **Connetti**. Copiare la stringa di connessione alla macchina virtuale. 
2. Connettersi alla macchina virtuale tramite il client SSH.  
3. Successivamente viene richiesto di immettere la **Password** aggiunta durante la creazione della **macchina virtuale Linux**. A questo punto l'accesso è stato eseguito correttamente.  
4. Usare CURL per ottenere il token di accesso per Azure Resource Manager.  

    Di seguito sono illustrate la richiesta e la risposta CURL per il token di accesso.  Sostituire <CLIENT ID> con il valore clientId dell'identità del servizio gestito assegnata dall'utente:
    
    ```bash
    curl 'http://localhost:50342/oauth2/token?resource=https://management.azure.com/&client_id=<CLIENT ID>' -H "Metadata:true"
    ```
    
    > [!NOTE]
    > Nella richiesta precedente il valore del parametro "risorsa" deve corrispondere esattamente a quello previsto da Azure AD. Quando si usa l'ID risorsa di Azure Resource Manager, è necessario includere la barra finale nell'URI.
    > Nella risposta seguente l'elemento access_token è stato abbreviato per comodità.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Ottenere le chiavi di accesso da Azure Resource Manager per eseguire le chiamate a Cosmos DB  

Di seguito viene usato CURL per eseguire una chiamata a Gestione risorse usando il token di accesso recuperato nella sezione precedente, per recuperare la chiave di accesso dell'account Cosmos DB. Dopo avere ottenuto la chiave di accesso, è possibile eseguire una query su Cosmos DB. Sostituire i valori dei parametri `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<COSMOS DB ACCOUNT NAME>` con i valori desiderati. Sostituire il valore `<ACCESS TOKEN>` con il token di accesso recuperato in precedenza.  Se si desidera recuperare le chiavi di lettura/scrittura, usare il tipo di operazione della chiave `listKeys`.  Se si desidera recuperare le chiavi di sola lettura, usare il tipo di operazione della chiave `readonlykeys`:

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Per il testo nell'URL precedente viene fatta distinzione tra maiuscole e minuscole. Assicurarsi quindi di usare la stessa combinazione di maiuscole e minuscole usata per i gruppi di risorse. Inoltre, è importante sapere che si tratta di una richiesta POST, non di una richiesta GET, e verificare di passare un valore per acquisire un limite di lunghezza con -d che può essere NULL.  

La risposta CURL restituisce l'elenco di chiavi.  Ad esempio, se si ottengono le chiavi di sola lettura:  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

Ora che si è in possesso della chiave di accesso per l'account Cosmos DB, è possibile trasferirla a un SDK Cosmos DB ed eseguire chiamate per accedere all'account.  Per un esempio rapido, è possibile passare la chiave di accesso all'interfaccia della riga di comando di Azure.  È possibile ottenere il valore <COSMOS DB CONNECTION URL> dalla scheda **Panoramica** nel pannello dell'account Cosmos DB nel portale di Azure.  Sostituire <ACCESS KEY> con il valore ottenuto in precedenza:

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Questo comando dell'interfaccia della riga di comando restituisce i dettagli sulla raccolta:

```bash
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dell'identità del servizio gestito, vedere [Identità del servizio gestito per le risorse di Azure](msi-overview.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.