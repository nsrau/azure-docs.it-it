---
title: Usare un'identità del servizio gestito assegnata dall'utente di macchina virtuale di Linux per accedere ad Azure Resource Manager
description: Questa esercitazione illustra come usare un'identità del servizio gestito assegnata dall'utente su una macchina virtuale Linux per accedere ad Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6d4f7378ccd24af4281793dbc93df40830a1b31a
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
ms.locfileid: "34300802"
---
# <a name="tutorial-use-a-user-assigned-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Esercitazione: Usare un'identità assegnata dall'utente su una macchina virtuale Linux per accedere ad Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Questa esercitazione illustra come creare un'identità, assegnarla a una macchina virtuale Linux e quindi usare tale identità per accedere all'API Azure Resource Manager. Le identità del servizio gestito vengono gestite automaticamente da Azure. Tali identità consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di incorporare le credenziali nel codice. 

Le identità del servizio gestito vengono gestite automaticamente da Azure. Tali identità consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di incorporare le credenziali nel codice.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un'identità assegnata dall'utente
> * Assegnare a una macchina virtuale Linux l'identità assegnata dall'utente 
> * Concedere all'identità assegnata dall'utente l'accesso a un gruppo di risorse in Azure Resource Manager 
> * Ottenere un token di accesso tramite l'identità dall'utente e usarlo per chiamare Azure Resource Manager 

## <a name="prerequisites"></a>prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere le [differenze tra identità assegnata dal sistema e identità assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire la proceduta delle attività richieste di creazione delle risorse e gestione dei ruoli in questa esercitazione, l'account deve disporre delle autorizzazioni "Proprietario" nell'ambito appropriato (sottoscrizione o gruppo di risorse). Se è necessaria assistenza con l'assegnazione, vedere [Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](/azure/role-based-access-control/role-assignments-portal).

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Linux in un nuovo gruppo di risorse

Per questa esercitazione, è innanzitutto necessario creare una nuova macchina virtuale Linux. È anche possibile scegliere di usare una macchina virtuale esistente.

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Calcolo** e quindi **Ubuntu Server 16.04 LTS**.
3. Immettere le informazioni relative alla macchina virtuale. In **Tipo di autenticazione** selezionare **Chiave pubblica SSH** o **Password**. Le credenziali create consentono di eseguire l'accesso alla macchina virtuale.

    ![Creare una macchina virtuale di Linux](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Dall'elenco a discesa **Sottoscrizione** selezionare una sottoscrizione per la macchina virtuale.
5. Per selezionare un nuovo **Gruppo di risorse** in cui creare la macchina virtuale, scegliere **Crea nuovo**. Al termine fare clic su **OK**.
6. Selezionare la dimensione della macchina virtuale. Per visualizzare altre dimensioni, selezionare **View all** (Visualizza tutto) o modificare il filtro Tipo di disco supportato. Nel pannello delle impostazioni mantenere le impostazioni predefinite e fare clic su **OK**.

## <a name="create-a-user-assigned-identity"></a>Creare un'identità assegnata dall'utente

1. Se si usa la console dell'interfaccia della riga di comando invece di una sessione di Azure Cloud Shell, eseguire l'accesso ad Azure. Usare un account associato alla sottoscrizione di Azure in cui si desidera creare una nuova identità assegnata dall'utente:

    ```azurecli
    az login
    ```

2. Creare un'identità assegnata dall'utente mediante [az identity create](/cli/azure/identity#az_identity_create). Il parametro `-g` specifica il gruppo di risorse in cui viene creata l'identità del servizio gestito, mentre il parametro `-n` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<MSI NAME>` con valori personalizzati:
    
    > [!IMPORTANT]
    > La creazione delle identità assegnate dall'utente supporta solo caratteri alfanumerici e trattino (numeri 0-9, lettere a-z e A-Z, -). Inoltre, il nome può avere al massimo 24 caratteri perché l'assegnazione alla macchina virtuale/set di scalabilità di macchine virtuali funzioni correttamente. Ricontrollare in seguito per aggiornamenti. Per altre informazioni, vedere [Domande frequenti e problemi noti](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    La risposta contiene i dettagli relativi all'identità assegnata dall'utente creata ed è simile all'esempio seguente. Prendere nota del valore `id` dell'identità assegnata dall'utente perché verrà usato nel passaggio successivo:

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

## <a name="assign-a-user-assigned-identity-to-your-linux-vm"></a>Assegnare alla macchina virtuale Linux un'identità assegnata dall'utente

Un'identità assegnata dall'utente può essere usata dai client in più risorse di Azure. Usare i comandi seguenti per assegnare a una singola macchina virtuale l'identità assegnata dall'utente. Usare la proprietà `Id` restituita nel passaggio precedente per il parametro `-IdentityID`.

Assegnare l'identità del servizio gestito assegnata dall'utente alla macchina virtuale Linux tramite [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VM NAME>` con valori personalizzati. Usare la proprietà `id` restituita nel passaggio precedente per il valore del parametro `--identities`.

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Concedere all'identità assegnata dall'utente l'accesso a un gruppo di risorse in Azure Resource Manager 

L'Identità del servizio gestita (MSI) fornisce le identità che il codice può usare per richiedere i token di accesso per l'autenticazione alle API di risorsa che supporta l'autenticazione di Azure Active Directory. In questa esercitazione il codice accede all'API di Azure Resource Manager.  

Prima che il codice possa accedere all'API, è necessario concedere all'identità l'accesso a una risorsa in Azure Resource Manager. In questo caso si tratta del gruppo di risorse in cui è contenuta la macchina virtuale. Aggiornare il valore per `<SUBSCRIPTION ID>` e `<RESOURCE GROUP>` in base all'ambiente in uso. Sostituire inoltre `<MSI PRINCIPALID>` con la proprietà `principalId` restituita dal comando `az identity create` in [Creare un'identità del servizio gestito assegnata dall'utente](#create-a-user-assigned-msi):

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

Per completare questi passaggi, è necessario disporre di un client SSH. Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about). 

1. Accedere al [portale](https://portal.azure.com) di Azure.
2. Nel portale passare a **Macchine virtuali**, selezionare la macchina virtuale Linux e in **Panoramica** fare clic su **Connetti**. Copiare la stringa di connessione alla macchina virtuale.
3. Connettersi alla macchina virtuale usando un client SSH di propria scelta. Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about). Per richiedere assistenza nella configurazione delle chiavi del client SSH, vedere [Come usare le chiavi SSH con Windows in Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) o [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
4. Nella finestra terminale usare CURL per fare una richiesta all'endpoint di Identità del servizio metadati di Azure per ottenere un token di accesso per Azure Resource Manager.  

   La richiesta CURL per acquisire un token di accesso viene visualizzata nell'esempio seguente. Sostituire `<CLIENT ID>` con la proprietà `clientId` restituita dal comando `az identity create` in [Creare un'identità assegnata dall'utente](#create-a-user-assigned-msi): 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>"   
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

5. Usare il token di accesso per accedere ad Azure Resource Manager e leggere le proprietà del gruppo di risorse a cui in precedenza è stato concesso l'accesso per l'identità assegnata dall'utente. Verificare di sostituire `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` con i valori specificati in precedenza e `<ACCESS TOKEN>` con il token restituito nel passaggio precedente.

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

- Per una panoramica dell'identità del servizio gestita, vedere [Informazioni sull'identità del servizio gestita per le risorse di Azure](overview.md).

