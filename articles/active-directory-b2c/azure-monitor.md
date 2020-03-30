---
title: Monitor Azure AD B2C with Azure Monitor
titleSuffix: Azure AD B2C
description: Informazioni su come registrare gli eventi B2C di Azure AD con Monitoraggio di Azure usando la gestione delle risorse delegate.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: acba378badb41324b2124b84833407da920a0e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190059"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Monitor Azure AD B2C with Azure Monitor

Usare Monitoraggio di Azure per instradare i log di accesso e di [controllo](view-audit-logs.md) di Azure Active Directory B2C (Azure AD B2C) a diverse soluzioni di monitoraggio. È possibile conservare i log per l'utilizzo a lungo termine o integrarsi con strumenti SIEM (Security Information and Event Management) di terze parti per ottenere informazioni dettagliate sull'ambiente.

È possibile instradare gli eventi del log a:You can route log events to:

* Un [account di archiviazione](../storage/blobs/storage-blobs-introduction.md)di Azure.
* Un [hub eventi](../event-hubs/event-hubs-about.md) di Azure (e l'integrazione con le istanze Splunk e Sumo Logic).
* [Un'area di lavoro](../azure-monitor/platform/resource-logs-collect-workspace.md) di Log Analytics (per analizzare i dati, creare dashboard e avvisare su eventi specifici).

![Monitoraggio di Azure](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo articolo, distribuire un modello di Azure Resource Manager usando il modulo Azure PowerShell.To complete the steps in this article, you deploy an Azure Resource Manager template by using the Azure PowerShell module.

* [Modulo di Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) versione 6.13.1 o successivaAzure PowerShell module version 6.13.1 or versione successiva

È anche possibile usare Azure Cloud Shell , che include la versione più recente del modulo Di Azure PowerShell.You can also use the [Azure Cloud Shell](https://shell.azure.com), which includes the latest version of the Azure PowerShell module.

## <a name="delegated-resource-management"></a>Gestione delle risorse delegate

Azure AD B2C sfrutta il monitoraggio di [Azure Active Directory.](../active-directory/reports-monitoring/overview-monitoring.md) Per abilitare *le impostazioni* di diagnostica in Azure Active Directory all'interno del tenant di Azure AD B2C, usare la gestione delle [risorse delegate.](../lighthouse/concepts/azure-delegated-resource-management.md)

Autorizzare un utente o un gruppo nella directory B2C di Azure AD (il **provider**di servizi ) per configurare l'istanza di Monitoraggio di Azure all'interno del tenant che contiene la sottoscrizione di Azure **(cliente**). Per creare l'autorizzazione, distribuire un modello di [Azure Resource Manager](../azure-resource-manager/index.yml) nel tenant di Azure AD contenente la sottoscrizione. Le sezioni seguenti illustrano il processo.

## <a name="create-or-choose-resource-group"></a>Creare o scegliere un gruppo di risorseCreate or choose resource group

Si tratta del gruppo di risorse contenente l'account di archiviazione di Azure di destinazione, l'hub eventi o l'area di lavoro di Log Analytics per ricevere dati da Monitoraggio di Azure.This is the resource group containing the destination Azure storage account, event hub, or Log Analytics workspace to receive data from Azure Monitor. Specificare il nome del gruppo di risorse quando si distribuisce il modello di Azure Resource Manager.You specify the resource group name when you deploy the Azure Resource Manager template.

[Creare un gruppo](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) di risorse o sceglierne uno esistente nel tenant di Azure Active Directory (Azure AD) che contiene la sottoscrizione di Azure, *non* la directory che contiene il tenant B2C di Azure AD.

Questo esempio usa un gruppo di risorse denominato *azure-ad-b2c-monitor* nell'area *Stati Uniti centrali.*

## <a name="delegate-resource-management"></a>Delegare la gestione delle risorse

Successivamente, raccogliere le seguenti informazioni:

**ID directory** della directory B2C di Azure AD (noto anche come ID tenant).

1. Accedere al [portale](https://portal.azure.com/) di Azure come utente con il ruolo *di amministratore utente* (o versione successiva).
1. Selezionare l'icona **Directory - Sottoscrizione** nella barra degli strumenti del portale e quindi selezionare la directory che contiene il tenant B2C di Azure AD.
1. Selezionare **Azure Active Directory**, selezionare **Proprietà**.
1. Registrare **l'ID directory**.

**ID oggetto** del gruppo o dell'utente B2C di Azure AD che si vuole concedere al *Collaboratore* l'autorizzazione per il gruppo di risorse creato in precedenza nella directory contenente la sottoscrizione.

Per semplificare la gestione, è consigliabile usare i *gruppi* di utenti di Azure AD per ogni ruolo, consentendo di aggiungere o rimuovere singoli utenti al gruppo anziché assegnare autorizzazioni direttamente a tale utente. In questa procedura dettagliata si aggiunge un utente.

1. Con **Azure Active Directory** ancora selezionato nel portale di Azure, selezionare **Utenti**e quindi selezionare un utente.
1. Registrare **l'ID oggetto**dell'utente.

### <a name="create-an-azure-resource-manager-template"></a>Creare un modello di Azure Resource Manager

Per eseguire l'onboarding del tenant di Azure AD **(cliente**), creare un modello di [Azure Resource Manager](../lighthouse/how-to/onboard-customer.md) per l'offerta con le informazioni seguenti. I `mspOfferName` `mspOfferDescription` valori e sono visibili quando si visualizzano i dettagli dell'offerta nella pagina Provider di servizi del portale di Azure.The and values are visible when you view offer details in the [Service providers page](../lighthouse/how-to/view-manage-service-providers.md) of the Azure portal.

| Campo   | Definizione |
|---------|------------|
| `mspOfferName`                     | Nome che descrive questa definizione. Ad esempio, *Servizi gestiti B2C*di Azure AD. Questo valore viene visualizzato al cliente come titolo dell'offerta. |
| `mspOfferDescription`              | Breve descrizione dell'offerta. Ad esempio, *Abilita Monitoraggio di Azure in Azure AD B2C.*|
| `rgName`                           | Nome del gruppo di risorse creato in precedenza nel tenant di Azure AD. Ad esempio, *azure-ad-b2c-monitor*. |
| `managedByTenantId`                | **ID directory** del tenant B2C di Azure AD (noto anche come ID tenant). |
| `authorizations.value.principalId` | **ID oggetto** del gruppo O utente E2C che avrà accesso alle risorse in questa sottoscrizione di Azure. Per questa procedura dettagliata, specificare l'ID oggetto dell'utente registrato in precedenza. |

Scaricare i file di modello e parametri di Azure Resource Manager:Download the Azure Resource Manager template and parameter files:

- [rgDelegatedResourceManagement.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Successivamente, aggiornare il file dei parametri con i valori registrati in precedenza. Il frammento JSON seguente mostra un esempio di un file di parametri del modello di Azure Resource Manager.The following JSON snippet shows an example of an Azure Resource Manager template parameters file. Per `authorizations.value.roleDefinitionId`, utilizzare il valore del [ruolo](../role-based-access-control/built-in-roles.md) `b24988ac-6180-42a0-ab88-20f7382dd24c`predefinito per il ruolo *Collaboratore*, .

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Azure AD B2C Managed Services"
        },
        "mspOfferDescription": {
            "value": "Enables Azure Monitor in Azure AD B2C"
        },
        "rgName": {
            "value": "azure-ad-b2c-monitor"
        },
        "managedByTenantId": {
            "value": "<Replace with DIRECTORY ID of Azure AD B2C tenant (tenant ID)>"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "<Replace with user's OBJECT ID>",
                    "principalIdDisplayName": "Azure AD B2C tenant administrator",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                }
            ]
        }
    }
}
```

### <a name="deploy-the-azure-resource-manager-templates"></a>Distribuire i modelli di Azure Resource Manager

Dopo aver aggiornato il file dei parametri, distribuire il modello di Azure Resource Manager nel tenant di Azure come distribuzione a livello di sottoscrizione. Trattandosi di una distribuzione a livello di sottoscrizione, non è possibile avviarla nel portale di Azure. È possibile eseguire la distribuzione usando il modulo di Azure PowerShell o l'interfaccia della riga di comando di Azure.You can deploy by using the Azure PowerShell module or the Azure CLI. Il metodo di Azure PowerShell è illustrato di seguito.

Accedere alla directory contenente la sottoscrizione utilizzando [Connect-AzAccount](/powershell/azure/authenticate-azureps). Utilizzare `-tenant` il flag per forzare l'autenticazione alla directory corretta.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Usare il cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) per elencare le sottoscrizioni a cui l'account corrente può accedere nel tenant di Azure AD. Registrare l'ID della sottoscrizione che si vuole proiettare nel tenant B2C di Azure AD.

```PowerShell
Get-AzSubscription
```

Passare quindi alla sottoscrizione che si vuole proiettare nel tenant B2C di Azure AD:Next, switch to the subscription you want to project into the Azure AD B2C tenant:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Infine, distribuire i file di modello e parametri di Azure Resource Manager scaricati e aggiornati in precedenza. Sostituire `Location`i `TemplateFile`valori `TemplateParameterFile` , e di conseguenza.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

La corretta distribuzione del modello produce un output simile al seguente (output troncato per brevità):

```Console
PS /usr/csuser/clouddrive> New-AzDeployment -Name "AzureADB2C" `
>>                  -Location "centralus" `
>>                  -TemplateFile "rgDelegatedResourceManagement.json" `
>>                  -TemplateParameterFile "rgDelegatedResourceManagement.parameters.json" `
>>                  -Verbose
WARNING: Breaking changes in the cmdlet 'New-AzDeployment' :
WARNING:  - The cmdlet 'New-AzSubscriptionDeployment' is replacing this cmdlet.


WARNING: NOTE : Go to https://aka.ms/azps-changewarnings for steps to suppress this breaking change warning, and other information on breaking changes in Azure PowerShell.
VERBOSE: 7:25:14 PM - Template is valid.
VERBOSE: 7:25:15 PM - Create template deployment 'AzureADB2C'
VERBOSE: 7:25:15 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:42 PM - Resource Microsoft.ManagedServices/registrationDefinitions '44444444-4444-4444-4444-444444444444' provisioning status is succeeded
VERBOSE: 7:25:48 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:53 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is running
VERBOSE: 7:25:53 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:59 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is running
VERBOSE: 7:26:17 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:23 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is succeeded
VERBOSE: 7:26:23 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:29 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is succeeded

DeploymentName          : AzureADB2C
Location                : centralus
ProvisioningState       : Succeeded
Timestamp               : 1/31/20 7:26:24 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                   Type                       Value
                          =====================  =========================  ==========
                          mspOfferName           String                     Azure AD B2C Managed Services
                          mspOfferDescription    String                     Enables Azure Monitor in Azure AD B2C
...
```

Dopo aver distribuito il modello, il completamento della proiezione delle risorse può richiedere alcuni minuti. Potrebbe essere necessario attendere alcuni minuti (in genere non più di cinque) prima di passare alla sezione successiva per selezionare la sottoscrizione.

## <a name="select-your-subscription"></a>Selezionare la propria sottoscrizione

Dopo aver distribuito il modello e aver atteso alcuni minuti per il completamento della proiezione delle risorse, associare la sottoscrizione alla directory B2C di Azure AD con i passaggi seguenti.

1. **Disconnettersi dal** portale di Azure se è stato eseguito l'accesso. Questo e il passaggio seguente vengono eseguiti per aggiornare le credenziali nella sessione del portale.
1. Accedere al [portale](https://portal.azure.com) di Azure con l'account amministrativo B2C di Azure AD.
1. Selezionare l'icona **Directory - Sottoscrizione** nella barra degli strumenti del portale.
1. Selezionare la directory che contiene la sottoscrizione.

    ![Cambia directory](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Verificare di aver selezionato la directory e la sottoscrizione corrette. In questo esempio vengono selezionate tutte le directory e le sottoscrizioni.

    ![Tutte le directory selezionate nel filtro Sottoscrizione & directory](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Configurare le impostazioni di diagnostica

Le impostazioni di diagnostica definiscono dove devono essere inviati i log e le metriche per una risorsa. Le destinazioni possibili sono:

- [Account di archiviazione di AzureAzure storage account](../azure-monitor/platform/resource-logs-collect-storage.md)
- [Soluzioni hub eventi.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)
- [Area di lavoro di Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md)

Se non è già stato fatto, creare un'istanza del tipo di destinazione scelto nel gruppo di risorse specificato nel [modello di Azure Resource Manager.](#create-an-azure-resource-manager-template)

### <a name="create-diagnostic-settings"></a>Creare le impostazioni di diagnosticaCreate diagnostic settings

È possibile [creare impostazioni di diagnostica](../active-directory/reports-monitoring/overview-monitoring.md) nel portale di Azure.You're ready to Create diagnostic settings in the Azure portal.

Per configurare le impostazioni di monitoraggio per i log attività B2C di Azure AD:To configure monitoring settings for Azure AD B2C activity logs:

1. Accedere al [portale](https://portal.azure.com/)di Azure .
1. Selezionare l'icona **Directory - Sottoscrizione** nella barra degli strumenti del portale e quindi selezionare la directory che contiene il tenant B2C di Azure AD.
1. Selezionare **Azure Active Directory**
1. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.
1. Se nella risorsa sono presenti impostazioni esistenti, verrà visualizzato un elenco di impostazioni già configurate. Selezionare **Aggiungi impostazione diagnostica** per aggiungere una nuova impostazione o **Modifica** impostazione per modificarne una esistente. Ogni impostazione non può avere più di uno dei tipi di destinazione..

    ![Riquadro Impostazioni di diagnostica nel portale di AzureDiagnostics settings pane in Azure portal](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Assegna un nome alla tua impostazione se non ne ha già uno.
1. Selezionare la casella per ogni destinazione per inviare i registri. Selezionare **Configura** per specificarne le impostazioni come descritto nella tabella seguente.

    | Impostazione | Descrizione |
    |:---|:---|
    | Archivia in un account di archiviazione | Nome dell'account di archiviazione. |
    | Streaming in un hub eventi | Spazio dei nomi in cui viene creato l'hub eventi (se questa è la prima volta che si esegue il log di streaming) o trasmesso in streaming (se sono già presenti risorse che eseguono il streaming di tale categoria di log a questo spazio dei nomi).
    | Invia a Log Analytics | Nome dell'area di lavoro. |

1. Selezionare **AuditLogs** e **SignInLogs**.
1. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'aggiunta e la configurazione di impostazioni di diagnostica in Monitoraggio di Azure, vedere [Esercitazione: Raccogliere e analizzare i log](../azure-monitor/insights/monitor-azure-resource.md)delle risorse da una risorsa di Azure.For more information about adding and configuring diagnostic settings in Azure Monitor, see Tutorial: Collect and analyze resource logs from an Azure resource.

Per informazioni sullo streaming dei log di Azure AD in un hub eventi, vedere [Esercitazione: Trasmettere i log di Azure Active Directory a un hub eventi](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)di Azure.For information about streaming Azure AD logs to an event hub, see Tutorial: Stream Azure Active Directory logs to an Azure event hub.
