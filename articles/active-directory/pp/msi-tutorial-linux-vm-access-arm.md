---
title: "Usare un'identità del servizio gestito assegnata dall'utente di macchina virtuale di Linux per accedere ad Azure Resource Manager"
description: "Questa esercitazione illustra come usare un'identità del servizio gestito assegnata dall'utente su una macchina virtuale Linux per accedere ad Azure Resource Manager."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 350b20dbff306221fbedd069ef378f11e2ec1415
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-resource-manager"></a>Usare un'identità del servizio gestito assegnata dall'utente su una macchina virtuale Linux per accedere ad Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Questa esercitazione illustra come creare un'identità del servizio gestito, assegnarla a una macchina virtuale Linux e quindi usare tale identità per accedere all'API Azure Resource Manager. 

Le identità del servizio gestito vengono gestite automaticamente da Azure. Tali identità consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di incorporare le credenziali nel codice.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un'identità del servizio gestito assegnata dall'utente
> * Assegnare l'identità del servizio gestito a una macchina virtuale Linux 
> * Concedere all'identità del servizio gestito l'accesso a un gruppo di risorse in Azure Resource Manager 
> * Ottenere un token di accesso usando l'identità del servizio gestito e usarlo per chiamare Azure Resource Manager 

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Per eseguire gli esempi di script dell'interfaccia della riga di comando in questa esercitazione sono disponibili due opzioni:

- Usare [Azure Cloud Shell](~/articles/cloud-shell/overview.md) tramite il portale di Azure o il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice.
- [Installare la versione più recente dell'interfaccia della riga di comando 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Linux in un nuovo gruppo di risorse

Per questa esercitazione, è innanzitutto necessario creare una nuova macchina virtuale Linux. È anche possibile scegliere di usare una macchina virtuale esistente.

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Calcolo** e quindi **Ubuntu Server 16.04 LTS**.
3. Immettere le informazioni relative alla macchina virtuale. In **Tipo di autenticazione** selezionare **Chiave pubblica SSH** o **Password**. Le credenziali create consentono di eseguire l'accesso alla macchina virtuale.

    ![Creare una macchina virtuale di Linux](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

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
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
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

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Concedere all'identità del servizio gestito a un gruppo di risorse in Azure Resource Manager 

L'identità del servizio gestito contiene un token di accesso per il codice in uso per l'autenticazione alle API della risorsa supportano l'autenticazione di Azure AD. In questa esercitazione il codice accede l'API di Azure Resource Manager. 

Prima che il codice possa accedere all'API, è necessario concedere all'identità del servizio gestito l'accesso a una risorsa in Azure Resource Manager. In questo caso si tratta del gruppo di risorse in cui è contenuta la macchina virtuale. Aggiornare i valori per `<SUBSCRIPTION ID>` e `<RESOURCE GROUP>` in base all'ambiente in uso. Sostituire inoltre `<MSI PRINCIPALID>` con la proprietà `principalId` restituita dal comando `az identity create` in [Creare un'identità del servizio gestito assegnata dall'utente](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

La risposta contiene i dettagli per l'assegnazione di ruolo creata, in modo analogo all'esempio seguente:

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per chiamare Azure Resource Manager 

Il resto dell'esercitazione prevede che le operazioni vengano svolte dalla macchina virtuale creata in precedenza.

Per completare questi passaggi, è necessario disporre di un client SSH. Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about). Per richiedere assistenza nella configurazione delle chiavi del client SSH, vedere [Come usare le chiavi SSH con Windows in Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) o [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Nel portale di Azure passare a **Macchine virtuali**, selezionare la macchina virtuale Linux e quindi nella parte superiore della pagina **Panoramica** fare clic su **Connetti**. Copiare la stringa di connessione alla macchina virtuale.
2. **Connettersi** alla macchina virtuale usando un client SSH di propria scelta.  
3. Nella finestra terminale usare CURL per fare una richiesta all'endpoint locale di Identità del servizio gestito per ottenere un token di accesso per Azure Resource Manager.  

   La richiesta CURL per acquisire un token di accesso viene visualizzata nell'esempio seguente. Sostituire `<CLIENT ID>` con la proprietà `clientId` restituita dal comando `az identity create` in [Creare un'identità del servizio gestito assegnata dall'utente](#create-a-user-assigned-msi): 
    
   ```bash
   curl -H Metadata:true "http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Il valore del parametro `resource` deve corrispondere esattamente a quello previsto da Azure AD. Quando si usa l'ID risorsa di Gestione risorse, è necessario includere la barra finale nell'URI. 
    
    La risposta include il token di accesso necessario per accedere ad Azure Resource Manager. 
    
    Esempio di risposta:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

4. Usare ora il token di accesso per accedere ad Azure Resource Manager e leggere le proprietà del gruppo di risorse a cui in precedenza è stato concesso l'accesso per l'identità del servizio gestito assegnata dall'utente. Verificare di sostituire `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` con i valori specificati in precedenza e `<ACCESS TOKEN>` con il token restituito nel passaggio precedente.

    > [!NOTE]
    > L'URL rispetta la distinzione tra maiuscole e minuscole, pertanto usare la stessa combinazione usata in precedenza quando al gruppo di risorse è stato assegnato il nome e il carattere "G" maiuscolo in `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    La risposta contiene le informazioni sul gruppo di risorse specifico, in modo analogo all'esempio seguente: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dell'Identità di servizio gestito, vedere [Panoramica dell'Identità di servizio gestito](msi-overview.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.
