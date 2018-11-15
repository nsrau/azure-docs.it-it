---
title: Usare un'identità gestita assegnata dall'utente per una macchina virtuale Linux per accedere ad Azure Resource Manager
description: Esercitazione che illustra come usare un'identità gestita assegnata dall'utente in una macchina virtuale Linux per accedere ad Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 638946f0ffa8b6540f55fb7a22ac17bd262269bf
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621684"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Esercitazione: Usare un'identità gestita assegnata dall'utente in una macchina virtuale Linux per accedere ad Azure Resource Manager

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Questa esercitazione illustra come creare un'identità gestita assegnata dall'utente, assegnarla a una macchina virtuale Linux e quindi usare tale identità per accedere all'API di Azure Resource Manager. Le identità gestite per le risorse di Azure vengono gestite automaticamente da Azure. Tali identità consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di incorporare le credenziali nel codice. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'identità gestita assegnata dall'utente
> * Assegnare l'identità gestita assegnata dall'utente a una macchina virtuale Linux 
> * Concedere l'accesso a un gruppo di risorse di Azure Resource Manager all'identità gestita assegnata dall'utente 
> * Ottenere un token di accesso tramite l'identità gestita assegnata dall'utente e usarlo per chiamare Azure Resource Manager 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Accedere al portale di Azure](https://portal.azure.com)

- [Creare una macchina virtuale Linux](/azure/virtual-machines/linux/quick-create-portal)

- Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

1. Se si usa la console dell'interfaccia della riga di comando invece di una sessione di Azure Cloud Shell, eseguire l'accesso ad Azure. Usare un account associato alla sottoscrizione di Azure in cui si vuole creare la nuova identità gestita assegnata dall'utente:

    ```azurecli
    az login
    ```

2. Creare un'identità gestita assegnata dall'utente tramite [az identity create](/cli/azure/identity#az-identity-create). Il parametro `-g` specifica il gruppo di risorse in cui viene creata l'identità gestita assegnata dall'utente, mentre il parametro `-n` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<UAMI NAME>` con valori personalizzati:
    
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <UAMI NAME>
```

La risposta contiene i dettagli relativi all'identità gestita assegnata dall'utente creata ed è simile all'esempio seguente. Prendere nota del valore `id` dell'identità gestita assegnata dall'utente perché verrà usato nel passaggio successivo:

```json
{
"clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
"clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
"id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>",
"location": "westcentralus",
"name": "<UAMI NAME>",
"principalId": "9012",
"resourceGroup": "<RESOURCE GROUP>",
"tags": {},
"tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
"type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

## <a name="assign-a-user-assigned-managed-identity-to-your-linux-vm"></a>Assegnare un'identità gestita assegnata dall'utente alla macchina virtuale Linux

Un'identità gestita assegnata dall'utente può essere usata dai client in più risorse di Azure. Usare i comandi seguenti per assegnare l'identità gestita assegnata dall'utente a una singola macchina virtuale. Usare la proprietà `Id` restituita nel passaggio precedente per il parametro `-IdentityID`.

Assegnare l'identità gestita assegnata dall'utente alla macchina virtuale Linux usando [az vm assign-identity](/cli/azure/vm#az-vm-assign-identity). Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<VM NAME>` con valori personalizzati. Usare la proprietà `id` restituita nel passaggio precedente per il valore del parametro `--identities`.

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>"
```

## <a name="grant-your-user-assigned-managed-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Concedere l'accesso a un gruppo di risorse di Azure Resource Manager all'identità gestita assegnata dall'utente 

Le identità gestite per le risorse di Azure forniscono le identità che il codice può usare per richiedere i token di accesso per l'autenticazione alle API delle risorse che supportano l'autenticazione di Azure AD. In questa esercitazione il codice accede all'API di Azure Resource Manager.  

Prima che il codice possa accedere all'API, è necessario concedere all'identità l'accesso a una risorsa in Azure Resource Manager. In questo caso si tratta del gruppo di risorse in cui è contenuta la macchina virtuale. Aggiornare il valore per `<SUBSCRIPTION ID>` e `<RESOURCE GROUP>` in base all'ambiente in uso. Sostituire inoltre `<UAMI PRINCIPALID>` con la proprietà `principalId` restituita dal comando `az identity create` in [Creare un'identità gestita assegnata dall'utente](#create-a-user-assigned-managed-identity):

```azurecli-interactive
az role assignment create --assignee <UAMI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
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

   La richiesta CURL per acquisire un token di accesso viene visualizzata nell'esempio seguente. Assicurarsi di sostituire `<CLIENT ID>` con la proprietà `clientId` restituita dal comando `az identity create` in [Creare un'identità gestita assegnata dall'utente](#create-a-user-assigned-managed-identity): 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<UAMI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Il valore del parametro `resource` deve corrispondere esattamente a quello previsto da Azure AD. Quando si usa l'ID risorsa di Gestione risorse, è necessario includere la barra finale nell'URI. 
    
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

5. Usare il token di accesso per accedere ad Azure Resource Manager e leggere le proprietà del gruppo di risorse a cui in precedenza si è concesso l'accesso per l'identità gestita assegnata dall'utente. Verificare di sostituire `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` con i valori specificati in precedenza e `<ACCESS TOKEN>` con il token restituito nel passaggio precedente.

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

In questa esercitazione si è appreso come creare un'identità gestita assegnata dall'utente e associarla a una macchina virtuale Linux per accedere all'API di Azure Resource Manager.  Per altre informazioni su Azure Resource Manager, vedere:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)

