---
title: Monitorare Azure AD B2C con monitoraggio di Azure
titleSuffix: Azure AD B2C
description: Informazioni su come registrare eventi di Azure AD B2C con monitoraggio di Azure usando la gestione delle risorse delegata.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190059"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Monitorare Azure AD B2C con monitoraggio di Azure

Usare monitoraggio di Azure per indirizzare l'accesso Azure Active Directory B2C (Azure AD B2C) e i log di [controllo](view-audit-logs.md) a diverse soluzioni di monitoraggio. È possibile conservare i log per l'uso a lungo termine o l'integrazione con strumenti di gestione di informazioni ed eventi di sicurezza di terze parti per ottenere informazioni approfondite sull'ambiente.

È possibile instradare gli eventi del log a:

* Un [account di archiviazione](../storage/blobs/storage-blobs-introduction.md)di Azure.
* Un [Hub eventi](../event-hubs/event-hubs-about.md) di Azure (e si integrano con le istanze della logica Splunk e Sumo).
* Un' [area di lavoro log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) (per analizzare i dati, creare dashboard e avvisi per eventi specifici).

![Monitoraggio di Azure](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Prerequisites

Per completare i passaggi descritti in questo articolo, è possibile distribuire un modello di Azure Resource Manager usando il modulo di Azure PowerShell.

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) versione del modulo 6.13.1 o superiore

È anche possibile usare la [Azure cloud Shell](https://shell.azure.com), che include la versione più recente del modulo di Azure PowerShell.

## <a name="delegated-resource-management"></a>Gestione delle risorse delegata

Azure AD B2C sfrutta [Azure Active Directory il monitoraggio](../active-directory/reports-monitoring/overview-monitoring.md). Per abilitare *le impostazioni di diagnostica* in Azure Active Directory all'interno del tenant di Azure ad B2C, si usa la [gestione delle risorse delegata](../lighthouse/concepts/azure-delegated-resource-management.md).

Autorizzare un utente o un gruppo nella directory Azure AD B2C ( **provider di servizi**) per configurare l'istanza di monitoraggio di Azure all'interno del tenant che contiene la sottoscrizione di Azure (il **cliente**). Per creare l'autorizzazione, si distribuisce un modello di [Azure Resource Manager](../azure-resource-manager/index.yml) nel tenant di Azure ad che contiene la sottoscrizione. Le sezioni seguenti illustrano il processo.

## <a name="create-or-choose-resource-group"></a>Creare o scegliere un gruppo di risorse

Questo è il gruppo di risorse contenente l'account di archiviazione di Azure di destinazione, l'hub eventi o l'area di lavoro Log Analytics per ricevere i dati da monitoraggio di Azure. Quando si distribuisce il modello di Azure Resource Manager, è necessario specificare il nome del gruppo di risorse.

[Creare un gruppo di risorse](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) o sceglierne uno esistente nel tenant Azure Active Directory (Azure ad) che contiene la sottoscrizione di Azure e *non* la directory che contiene il tenant di Azure ad B2C.

Questo esempio usa un gruppo di risorse denominato *Azure-ad-B2C-monitor* nell'area *Stati Uniti centrali* .

## <a name="delegate-resource-management"></a>Delegare la gestione delle risorse

Successivamente, raccogliere le informazioni seguenti:

**ID directory** della directory Azure ad B2C (noto anche come ID tenant).

1. Accedere al [portale di Azure](https://portal.azure.com/) come utente con il ruolo di *amministratore utente* (o versione successiva).
1. Selezionare l'icona **directory + sottoscrizione** sulla barra degli strumenti del portale e quindi selezionare la directory che contiene il tenant Azure ad B2C.
1. Selezionare **Azure Active Directory**, quindi **Proprietà**.
1. Registrare l' **ID directory**.

**ID oggetto** del gruppo Azure ad B2C o dell'utente a cui si vuole concedere l'autorizzazione *collaboratore* per il gruppo di risorse creato in precedenza nella directory che contiene la sottoscrizione.

Per semplificare la gestione, è consigliabile utilizzare Azure AD *gruppi* di utenti per ogni ruolo, consentendo di aggiungere o rimuovere singoli utenti al gruppo anziché assegnare le autorizzazioni direttamente a tale utente. In questa procedura dettagliata viene aggiunto un utente.

1. Con **Azure Active Directory** ancora selezionato nella portale di Azure, selezionare **utenti**e quindi selezionare un utente.
1. Registrare l' **ID oggetto**dell'utente.

### <a name="create-an-azure-resource-manager-template"></a>Creare un modello di Azure Resource Manager

Per caricare il tenant di Azure AD (il **cliente**), creare un [modello Azure Resource Manager](../lighthouse/how-to/onboard-customer.md) per l'offerta con le informazioni seguenti. I valori `mspOfferName` e `mspOfferDescription` sono visibili quando si visualizzano i dettagli dell'offerta nella [pagina provider di servizi](../lighthouse/how-to/view-manage-service-providers.md) della portale di Azure.

| Campo   | Definizione |
|---------|------------|
| `mspOfferName`                     | Nome che descrive questa definizione. Ad esempio, *Azure ad B2C servizi gestiti*. Questo valore viene visualizzato al cliente come titolo dell'offerta. |
| `mspOfferDescription`              | Breve descrizione dell'offerta. Ad esempio, *Abilita monitoraggio di Azure in Azure ad B2C*.|
| `rgName`                           | Il nome del gruppo di risorse creato in precedenza nel tenant del Azure AD. Ad esempio, *Azure-ad-B2C-monitor*. |
| `managedByTenantId`                | **ID directory** del tenant di Azure ad B2C (noto anche come ID tenant). |
| `authorizations.value.principalId` | **ID oggetto** del gruppo B2C o dell'utente che avrà accesso alle risorse in questa sottoscrizione di Azure. Per questa procedura dettagliata, specificare l'ID oggetto dell'utente registrato in precedenza. |

Scaricare il modello di Azure Resource Manager e i file dei parametri:

- [rgDelegatedResourceManagement.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Aggiornare quindi il file dei parametri con i valori registrati in precedenza. Il frammento di codice JSON seguente mostra un esempio di un file di parametri del modello Azure Resource Manager. Per `authorizations.value.roleDefinitionId`, utilizzare il valore del [ruolo predefinito](../role-based-access-control/built-in-roles.md) per il *ruolo collaboratore*, `b24988ac-6180-42a0-ab88-20f7382dd24c`.

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

Dopo aver aggiornato il file dei parametri, distribuire il modello di Azure Resource Manager nel tenant di Azure come distribuzione a livello di sottoscrizione. Trattandosi di una distribuzione a livello di sottoscrizione, non è possibile avviarla nel portale di Azure. È possibile eseguire la distribuzione usando il modulo Azure PowerShell o l'interfaccia della riga di comando di Azure. Di seguito è illustrato il metodo Azure PowerShell.

Accedere alla directory che contiene la sottoscrizione usando [Connect-AzAccount](/powershell/azure/authenticate-azureps). Usare il flag `-tenant` per forzare l'autenticazione nella directory corretta.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Usare il cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) per elencare le sottoscrizioni a cui l'account corrente può accedere nel tenant del Azure ad. Registrare l'ID della sottoscrizione che si vuole proiettare nel tenant del Azure AD B2C.

```PowerShell
Get-AzSubscription
```

Passare quindi alla sottoscrizione che si vuole proiettare nel tenant di Azure AD B2C:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Infine, distribuire il modello di Azure Resource Manager e i file di parametri scaricati e aggiornati in precedenza. Sostituire i valori di `Location`, `TemplateFile`e `TemplateParameterFile` di conseguenza.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

La distribuzione corretta del modello genera un output simile al seguente (l'output è troncato per brevità):

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

Dopo aver distribuito il modello, possono essere necessari alcuni minuti per il completamento della proiezione di risorse. Potrebbe essere necessario attendere alcuni minuti (in genere non più di cinque) prima di procedere alla sezione successiva per selezionare la sottoscrizione.

## <a name="select-your-subscription"></a>Selezionare la propria sottoscrizione

Dopo aver distribuito il modello e aver atteso alcuni minuti per il completamento della proiezione di risorse, associare la sottoscrizione alla directory Azure AD B2C con i passaggi seguenti.

1. **Disconnettersi** dal portale di Azure se si è connessi. Questa operazione e il passaggio seguente consentono di aggiornare le credenziali nella sessione del portale.
1. Accedere al [portale di Azure](https://portal.azure.com) con l'account amministrativo Azure ad B2C.
1. Selezionare l'icona **directory + sottoscrizione** sulla barra degli strumenti del portale.
1. Selezionare la directory che contiene la sottoscrizione.

    ![Cambia directory](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Verificare di aver selezionato la directory e la sottoscrizione corrette. In questo esempio vengono selezionate tutte le directory e le sottoscrizioni.

    ![Tutte le directory selezionate nel filtro directory & sottoscrizione](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Configurare le impostazioni di diagnostica

Le impostazioni di diagnostica definiscono dove devono essere inviati i log e le metriche per una risorsa. Le destinazioni possibili sono:

- [Account di archiviazione di Azure](../azure-monitor/platform/resource-logs-collect-storage.md)
- Soluzioni di [Hub eventi](../azure-monitor/platform/resource-logs-stream-event-hubs.md) .
- [area di lavoro Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md)

Se non è già stato fatto, creare un'istanza del tipo di destinazione scelto nel gruppo di risorse specificato nel [modello di Azure Resource Manager](#create-an-azure-resource-manager-template).

### <a name="create-diagnostic-settings"></a>Crea impostazioni di diagnostica

È ora possibile [creare le impostazioni di diagnostica](../active-directory/reports-monitoring/overview-monitoring.md) nel portale di Azure.

Per configurare le impostazioni di monitoraggio per i log attività Azure AD B2C:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare l'icona **directory + sottoscrizione** sulla barra degli strumenti del portale e quindi selezionare la directory che contiene il tenant Azure ad B2C.
1. Selezionare **Azure Active Directory**
1. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.
1. Se nella risorsa sono presenti impostazioni esistenti, verrà visualizzato un elenco di impostazioni già configurate. Selezionare **Aggiungi impostazioni di diagnostica** per aggiungere una nuova impostazione o **modificare** un'impostazione per modificarne una esistente. Ogni impostazione non può contenere più di uno dei tipi di destinazione.

    ![Riquadro impostazioni di diagnostica in portale di Azure](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Assegnare un nome all'impostazione se non ne è già presente uno.
1. Selezionare la casella per ogni destinazione per inviare i log. Selezionare **Configura** per specificare le impostazioni come descritto nella tabella seguente.

    | Impostazione | Descrizione |
    |:---|:---|
    | Archivia in un account di archiviazione | Nome dell'account di archiviazione. |
    | Streaming in un hub eventi | Lo spazio dei nomi in cui viene creato l'hub eventi, se è la prima volta che si esegue lo streaming dei log, o se sono già presenti risorse che eseguono il flusso di tale categoria di log a questo spazio dei nomi.
    | Invia a Log Analytics | Nome dell'area di lavoro. |

1. Selezionare **AuditLogs** e **SignInLogs**.
1. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'aggiunta e la configurazione delle impostazioni di diagnostica in monitoraggio di Azure, vedere [esercitazione: raccogliere e analizzare i log delle risorse da una risorsa di Azure](../azure-monitor/insights/monitor-azure-resource.md).

Per informazioni sul flusso di log Azure AD a un hub eventi, vedere [esercitazione: trasmettere i log di Azure Active Directory a un hub eventi di Azure](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
