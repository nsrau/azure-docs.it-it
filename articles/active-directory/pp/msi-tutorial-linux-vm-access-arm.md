---
title: Utilizzare un file MSI di VM Linux assegnati dall'utente per accedere a Gestione risorse di Azure
description: "Un'esercitazione che illustra il processo di utilizzo di un User-Assigned gestiti servizio identità (MSI) in una VM Linux, per accedere a Gestione risorse di Azure."
services: active-directory
documentationcenter: 
author: bryanLa
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a51d0bdf092893288f2e1cc31a4dcc4117b041c2
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-resource-manager"></a>Utilizzare un utente assegnato gestiti servizio identità (MSI) in una VM Linux, per accedere a Gestione risorse di Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

In questa esercitazione viene illustrato come creare un utente assegnato gestiti servizio identità (MSI), assegnarla a una macchina virtuale Linux (VM) e quindi utilizzare tale identità per accedere all'API di gestione risorse di Azure. 

Identità del servizio gestito vengono gestite automaticamente da Azure. Consentono l'autenticazione ai servizi che supportano l'autenticazione di Azure AD, senza la necessità di incorporare le credenziali nel codice.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un file MSI assegnati dall'utente
> * Assegnare il file MSI in una macchina virtuale Linux 
> * Concedere l'accesso MSI a un gruppo di risorse di gestione risorse di Azure 
> * Ottenere un token di accesso usando il file MSI e utilizzarlo per chiamare Gestione risorse di Azure 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Per eseguire gli esempi di script CLI in questa esercitazione, sono disponibili due opzioni:

- Utilizzare [Azure Cloud Shell](~/articles/cloud-shell/overview.md) dal portale di Azure o tramite il pulsante "Provalo", si trova nell'angolo superiore destro di ogni blocco di codice.
- [Installare la versione più recente di CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 o versione successiva) se si preferisce utilizzare una console locale di CLI.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Linux in un nuovo gruppo di risorse

Per questa esercitazione, è innanzitutto necessario creare una nuova VM Linux. È inoltre possibile scegliere di utilizzare una macchina virtuale esistente.

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Calcolo** e quindi **Ubuntu Server 16.04 LTS**.
3. Immettere le informazioni relative alla macchina virtuale. In **Tipo di autenticazione** selezionare **Chiave pubblica SSH** o **Password**. Le credenziali create consentono di eseguire l'accesso alla macchina virtuale.

    ![Creare una macchina virtuale di Linux](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

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

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Assegnare MSI assegnati dall'utente per le VM Linux

A differenza di un file MSI assegnato dal sistema, un file MSI assegnati dall'utente può essere utilizzato dai client su più risorse di Azure. Per questa esercitazione, vengono assegnati a una singola macchina virtuale. È inoltre possibile assegnare più di una macchina virtuale.

Assegnare il file MSI assegnati dall'utente per le VM Linux utilizzando [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). Assicurarsi di sostituire il `<RESOURCE GROUP>` e `<VM NAME>` i valori dei parametri con valori personalizzati. Utilizzare il `id` proprietà restituita nel passaggio precedente per il `--identities` valore del parametro:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> -–identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Concedere l'accesso MSI utente assegnato a un gruppo di risorse di gestione risorse di Azure 

MSI fornisce il codice con un token di accesso per l'autenticazione alla risorsa API che supportano l'autenticazione di Azure AD. In questa esercitazione, il codice accede l'API di gestione risorse di Azure. 

Il codice può accedere tramite l'API, è necessario concedere l'accesso di identità del file MSI a una risorsa di gestione risorse di Azure. In questo caso, il gruppo di risorse in cui è contenuta la macchina virtuale. Sostituire i valori dei parametri `<CLIENT ID>`, `<SUBSCRIPTION ID>` e `<RESOURCE GROUP>` con i valori desiderati. Sostituire `<CLIENT ID>` con il `clientId` restituito dalla proprietà di `az identity create` comando [creare un utente assegnato MSI](#create-a-user-assigned-msi): 

```azurecli-interactive
az role assignment create --assignee <CLIENT ID> --role ‘Reader’ --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

La risposta contiene i dettagli per l'assegnazione di ruolo creato, in modo analogo all'esempio seguente:

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

   La richiesta CURL per acquisire un token di accesso viene visualizzata nell'esempio seguente. Assicurarsi di sostituire `<CLIENT ID>` con il `clientId` restituito dalla proprietà di `az identity create` comando [creare un utente assegnato MSI](#create-a-user-assigned-msi): 
    
   ```bash
   curl -H Metadata:true "http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Il valore di `resource` parametro deve essere una corrispondenza esatta per le azioni previste da Azure AD. Quando si utilizza l'ID di risorsa di gestione delle risorse, è necessario includere la barra finale nell'URI. 
    
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

4. Ora utilizzare il token di accesso per accedere a Gestione risorse di Azure e leggere le proprietà del gruppo di risorse a cui si concesse in precedenza l'accesso MSI assegnati dall'utente. Assicurarsi di sostituire `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` con i valori specificati in precedenza, e `<ACCESS TOKEN>` con il token restituito nel passaggio precedente.

    > [!NOTE]
    > L'URL è tra maiuscole e minuscole, pertanto assicurarsi di utilizzare lo stesso esatto case utilizzato in precedenza quando è denominato gruppo di risorse e "G" maiuscola in `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    La risposta contiene le informazioni gruppo di risorse specifiche, simile all'esempio seguente: 

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