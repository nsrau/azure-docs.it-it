---
title: Creare un ruolo di Azure Resource Manager personalizzato e assegnarlo a un'entità servizio-Azure
description: Questo articolo fornisce indicazioni su come creare un ruolo di Azure Resource Manager personalizzato e assegnarlo a un'entità servizio per analisi video in tempo reale su IoT Edge usando l'interfaccia della riga di comando di Azure.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: a780ecbbf2530b15984c596281c4aa7e4f5dd520
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526579"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>Creare un ruolo di Azure Resource Manager personalizzato e assegnarlo a un'entità servizio

Per il corretto funzionamento di analisi video in tempo reale su IoT Edge istanza del modulo è necessario un account di servizi multimediali di Azure attivo. La relazione tra l'analisi video in tempo reale sul modulo IoT Edge e l'account del servizio multimediale di Azure viene stabilita tramite un set di proprietà del modulo gemello. Una di queste proprietà del dispositivo gemello è un' [entità servizio](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) che consente all'istanza del modulo di comunicare con e attivare le operazioni necessarie nell'account di servizi multimediali. Per ridurre al minimo i potenziali abusi e/o l'esposizione accidentale dei dati dal dispositivo perimetrale, questa entità servizio deve avere il minor numero di privilegi.

Questo articolo illustra i passaggi per la creazione di un ruolo di Azure Resource Manager personalizzato con Azure Cloud Shell, che viene quindi usato per creare un'entità servizio.

## <a name="prerequisites"></a>Prerequisiti  

I prerequisiti per questo articolo sono i seguenti:

* Sottoscrizione di Azure con sottoscrizione proprietario.
* Un Azure Active Directory con privilegi per creare un'app e assegnare un'entità servizio a un ruolo.

Il modo più semplice per verificare se l'account dispone delle autorizzazioni appropriate è tramite il portale. Vedere [Controllare le autorizzazioni necessarie](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="overview"></a>Panoramica  

Verranno esaminati i passaggi per la creazione di un ruolo personalizzato e il collegamento a un'entità servizio nell'ordine seguente:

1. Creare un account del servizio multimediale, se non ne è già presente uno.
1. Creare un'entità servizio.
1. Creare un ruolo di Azure Resource Manager personalizzato con privilegi limitati.
1. "Limitare" i privilegi dell'entità servizio usando il ruolo personalizzato creato.
1. Eseguire un semplice test per verificare se è possibile limitare correttamente l'entità servizio.
1. Acquisire i parametri che verranno utilizzati nei manifesti di distribuzione IoT Edge.

### <a name="create-a-media-services-account"></a>Creare un account di Servizi multimediali  

Se non si dispone di un account del servizio multimediale, attenersi alla procedura seguente per crearne uno.

1. Passare al [cloud Shell](https://shell.azure.com/).
1. Selezionare "bash" come ambiente nell'elenco a discesa nella parte sinistra della finestra della shell

    ![Schermo capturs Mostra bash selezionato dalla finestra della shell.](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. Impostare la sottoscrizione di Azure come account predefinito usando il modello di comando seguente:
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. Creare un [gruppo di risorse](/cli/azure/group?view=azure-cli-latest#az-group-create) e un [account di archiviazione](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create).
1. A questo punto, creare un account di servizi multimediali di Azure usando il modello di comando seguente in Cloud Shell:

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>Creare un'entità servizio  

Verrà ora creata una nuova entità servizio e il collegamento verrà collegato all'account del servizio multimediale.

Senza parametri di autenticazione, l'autenticazione basata su password viene usata con una password casuale per l'entità servizio. In Cloud Shell usare il modello di comando seguente:

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

Questo comando genera una risposta simile alla seguente:

```
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "<yourServicePrincipalPassword>",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": " <yourAMSAccountName >",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "Central US",
  "ResourceGroup": " <yourResouceGroup >",
  "SubscriptionId": "<yourSubscriptionId>"
}

```
1. L'output per un'entità servizio con autenticazione della password include la chiave della password che in questo caso è il parametro "AadSecret". 

    Assicurarsi di copiare questo valore perché non può essere recuperato. Se si dimentica la password, [reimpostare le credenziali dell'entità servizio](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#reset-credentials).
1. La chiave appId e tenant viene visualizzata rispettivamente nell'output come "AadClientId" e "AadTenantId". Vengono usati nell'autenticazione basata su entità servizio. Registrare i valori, che possono essere tuttavia recuperati in qualsiasi momento con [az ad sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list).

### <a name="create-a-custom-role-definition"></a>Creare una definizione di ruolo personalizzata  

Per creare un ruolo personalizzato, attenersi alla procedura seguente:

1. Creare un file JSON di definizione del ruolo nel sistema locale e salvare il testo seguente nel file. 
    1. Sostituire <> Idsottoscrizione con l'ID sottoscrizione di Azure
    1. Le uniche azioni consentite per questo ruolo sono:
        * listContainerSas: consente di elencare gli URL del contenitore di archiviazione dei moduli con firme di accesso condiviso (SAS) per caricare e scaricare il contenuto degli asset.
        * Scrivere asset: consente al modulo di creare o aggiornare qualsiasi asset
        * listEdgePolicies: elenca i criteri applicati al dispositivo perimetrale  
        
        ```
        {
          "Name": "LVAEdge User",
          "IsCustom": true,
          "Description": "Can create assets, view list of containers and view Edge policies",
          "Actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action"
          ],
          "NotActions": [],
          "DataActions": [],
          "NotDataActions": [],
          "AssignableScopes": [
            "/subscriptions/<yourSubscriptionId>"
          ]
        }
        ```  
          
1. Una volta creato, eseguire il seguente modello di comando per creare la nuova definizione di ruolo nella sottoscrizione:
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    Al completamento dell'esecuzione del comando, viene visualizzato l'output seguente:
    
    ```
    {
      "assignableScopes": [
      "/subscriptions/<yourSubscriptionId>"
      ],
      "description": "Can create assets, view list of containers and view Edge policies",
      "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<unique name>",
      "name": "<unique name>",
      "permissions": [
        {
          "actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action",
          ],
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": " LVAEdge User ",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

### <a name="create-role-assignment"></a>Crea assegnazione ruolo  

Per aggiungere un'assegnazione di ruolo, sarà necessario il valore objectId dell'entità servizio a cui si vuole assegnare il ruolo personalizzato appena creato.

Usare il comando seguente in Cloud Shell per ottenere l'objectId:

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>` può essere recuperato dall'output del passaggio [Crea entità servizio](#create-service-principal) .

Con il comando precedente viene stampato il valore objectId dell'entità servizio. 

```
“objectId” : “<yourObjectId>”,
```

Usare [AZ Role Assignment create Command](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) template per collegare il ruolo personalizzato all'entità servizio:

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

Parametri

|Parametri|Descrizione| 
|---|---|
|--Role |Nome o ID del ruolo personalizzato. In questo caso: "utente LVAEdge".|
|--Assignee-Object-ID|ID oggetto dell'entità servizio che si utilizzerà.|

Il risultato sarà simile al seguente:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleAssignments/<yourCustomRoleId>",
  "name": "<yourCustomRoleId>",
  "principalId": "<yourServicePrincipalId>",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<yourRoleDefinitionId>",
  "scope": "/subscriptions/<yourSubscriptionId>",
  "type": "Microsoft.Authorization/roleAssignments"
} 
```

### <a name="confirm-that-role-assignment-happened"></a>Verificare che sia stata eseguita l'assegnazione di ruolo

Per verificare che l'entità servizio sia ora collegata al ruolo personalizzato appena creato, eseguire il comando seguente:

```
az role assignment list  --assignee < objectId>
```

Il risultato dovrebbe essere simile al seguente:

```
[
  {
    "canDelegate": null,
    "id": "/subscriptions/xxx/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "principalId": "<yourServicePrincipalID>",
    "principalName": "<yourServicePrincipalName>",
    "principalType": "ServicePrincipal",
    "roleDefinitionId": "/subscriptions/xxx/providers/Microsoft.Authorization/roleDefinitions/zzz",
    "roleDefinitionName": "LVAEdge User",
    "scope": "/subscriptions/<yourSubscription ID>",
    "type": "Microsoft.Authorization/roleAssignments
  }
]  
```
 
Cercare "roleDefinitionName" e verificare che il relativo valore sia impostato su "LVAEdge User". 

Questo conferma che è stato collegato il ruolo utente personalizzato con l'entità servizio usata per l'applicazione.

### <a name="test-the-service-principal-rbac"></a>Testare l'entità servizio RBAC  

1. Accedere con l'entità servizio. Per questo motivo, sono necessarie tre informazioni per la Azure Active Directory per concedere il token di accesso appropriato che è possibile ottenere dall'output del passaggio [Crea entità servizio](#create-service-principal) :
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. A questo punto, è possibile provare ad accedere usando il modello di comando seguente:
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  A questo punto, è possibile verificare se l'account di accesso è limitato all'entità servizio con il ruolo "utente LVAEdge" provando a creare un gruppo di risorse per assicurarsi che abbia esito negativo. Eseguire il comando seguente in Cloud Shell:

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    Questo comando dovrebbe avere esito negativo e avrà un aspetto simile al seguente:
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>Passaggi successivi  

Prendere nota dei valori seguenti di questo articolo. Questi valori saranno necessari per configurare le proprietà dei dispositivi gemelli di analisi video in tempo reale sul modulo IoT Edge, vedere lo [schema JSON del modulo gemello](module-twin-configuration-schema.md).

| Variabile da questo articolo|Nome di proprietà del dispositivo gemello per analisi video in tempo reale su IoT Edge|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|
