---
title: Integrare la soluzione di monitoraggio esterna con lo Stack di Azure | Documenti Microsoft
description: Informazioni su come integrare Azure Stack con una soluzione di monitoraggio esterna nel Data Center.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: mabrigg
ms.openlocfilehash: 76499ac959b77e83494bc4f9593c20a99da5c147
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrare la soluzione di monitoraggio esterna con lo Stack di Azure

*Si applica a: Azure Stack integrate di sistemi*

Per il monitoraggio esterno dell'infrastruttura di Azure Stack, è necessario monitorare il software di Stack di Azure, i computer fisici e i commutatori di rete fisica. Ognuna di queste aree offre un metodo per recuperare le informazioni di stato e di avviso.

- Il software di Stack Azure offre un'API basata su REST per recuperare l'integrità e avvisi. (Con l'utilizzo di definita dal software di tecnologie quali spazi di archiviazione diretta, gli avvisi e lo stato di archiviazione sono parte del software di monitoraggio).
- Computer fisico può rendere disponibile tramite i baseboard management controller (BMC) l'integrità e informazioni sugli avvisi.
- I dispositivi di rete fisica possono rendere disponibile tramite il protocollo SNMP l'integrità e informazioni sugli avvisi.

Ogni soluzione Azure Stack viene fornito con un host del ciclo di vita di hardware. L'host esegue software di monitoraggio per i server fisici e i dispositivi di rete del fornitore di hardware Original Equipment Manufacturer (OEM). Se si desidera, è possibile ignorare queste soluzioni di monitoraggio e integrare direttamente con le soluzioni esistenti di monitoraggio nel Data Center.

> [!IMPORTANT]
> La soluzione di monitoraggio esterna che è utilizzare deve essere senza agente. È possibile installare agenti di terze parti all'interno dei componenti dello Stack di Azure.

Il diagramma seguente mostra il flusso del traffico tra un sistema integrato dello Stack di Azure, l'host del ciclo di vita di hardware, una soluzione di monitoraggio esterna e un sistema di raccolta di emissione/dati esterni.

![Diagramma che illustra il traffico tra Azure Stack, il monitoraggio e il ticket soluzione.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

In questo articolo viene illustrato come integrare Azure Stack con soluzioni di monitoraggio esterne, ad esempio System Center Operations Manager e Nagios. Include inoltre come utilizzare gli avvisi a livello di programmazione tramite PowerShell o tramite chiamate all'API REST.

## <a name="integrate-with-operations-manager"></a>Integrazione con Operations Manager

È possibile utilizzare Operations Manager per il monitoraggio esterno dello Stack di Azure. System Center Management Pack per Microsoft Azure Stack consente di monitorare più distribuzioni di Azure Stack con una singola istanza di Operations Manager. Il management pack utilizza il provider di risorse di integrità e il provider di risorse aggiornamento API REST per comunicare con lo Stack di Azure. Se si prevede di ignorare OEM software che è in esecuzione nell'host hardware del ciclo di vita di monitoraggio, è possibile installare i management pack fornitore per monitorare i server fisici. È anche possibile utilizzare Individuazione dei dispositivi di rete Operations Manager per monitorare i commutatori di rete.

Il management pack per lo Stack di Azure offre le funzionalità seguenti:

- È possibile gestire più distribuzioni di Azure Stack.
- È supportata per Azure Active Directory (Azure AD) e Active Directory Federation Services (ADFS).
- È possibile recuperare e chiudere gli avvisi.
- È presente dello stato e un dashboard di capacità.
- Include il rilevamento di modalità di manutenzione automatica per l'utilizzo di patch e aggiornamenti (P & U) in corso.
- Include attività di aggiornamento imposto per la distribuzione e area.
- È possibile aggiungere informazioni personalizzate a un'area.
- Supporta la notifica e creazione di report.

È possibile scaricare System Center Management Pack per Microsoft Azure Stack e la Guida dell'utente associato [qui](https://www.microsoft.com/en-us/download/details.aspx?id=55184), o direttamente da Operations Manager.

Per una soluzione di creazione di ticket, è possibile integrare Operations Manager con System Center Service Manager. Il connettore prodotto integrato consente la comunicazione bidirezionale che consente di chiudere un avviso nello Stack di Azure e Operations Manager dopo avere risolto una richiesta di servizio in Service Manager.

Il diagramma seguente mostra l'integrazione di Azure Stack con una distribuzione esistente di System Center. È possibile automatizzare Service Manager ulteriormente con System Center Orchestrator o Service Management Automation (SMA) per eseguire operazioni nello Stack di Azure.

![Diagramma che mostra l'integrazione con Operations Manager, Service Manager e SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrazione con Nagios

È stato sviluppato un Nagios plug-in di monitoraggio e le relative soluzioni Cloudbase il partner, disponibile con la licenza permissiva software gratuito: MIT (Massachusetts Institute of Technology).

Il plug-in viene scritto in Python e sfrutta il provider di risorse integrità API REST. Offre funzionalità di base per recuperare e chiudere gli avvisi nello Stack di Azure. Ad esempio il management pack di System Center, consente di aggiungere più distribuzioni di Stack di Azure e per inviare notifiche.

Il plug-in funziona con Nagios Enterprise e Nagios Core. È possibile scaricarlo [qui](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Il sito di download include inoltre i dettagli di installazione e configurazione.

### <a name="plugin-parameters"></a>Parametri di plug-in

Configurare il file di plug-in "Azurestack_plugin.py" con i parametri seguenti:

| Parametro | Descrizione | Esempio |
|---------|---------|---------|
| *arm_endpoint* | Endpoint di Azure Resource Manager (amministratore) |https://adminmanagement.Local.azurestack.External |
| *api_endpoint* | Endpoint di Azure Resource Manager (amministratore)  | https://adminmanagement.Local.azurestack.External |
| *Tenant_id* | ID sottoscrizione Admin | Recuperare tramite il portale dell'amministratore o PowerShell |
| *User_name* | Nome utente di sottoscrizione (operatore) | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Password di sottoscrizione (operatore) | password |
| *Client_id* | Client | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 * |
| *area* |  Nome dell'area Azure Stack | local |
|  |  |

* Il GUID di PowerShell fornita è universale. È possibile utilizzarlo per ogni distribuzione.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Utilizzare PowerShell per avvisi e monitoraggio dell'integrità

Se non si utilizza Operations Manager, Nagios o una soluzione basata su Nagios, è possibile utilizzare PowerShell per abilitare una vasta gamma di soluzioni per l'integrazione con Azure Stack di monitoraggio.
 
1. Per usare PowerShell, assicurarsi di aver [PowerShell installato e configurato](azure-stack-powershell-configure-quickstart.md) per un ambiente di operatore dello Stack di Azure. Installare PowerShell in un computer locale che può raggiungere l'endpoint di gestione risorse (amministratore) (https://adminmanagement. [ area]. [FQDN_esterno]).

2. Eseguire i comandi seguenti per connettersi all'ambiente dello Stack di Azure come un operatore di Stack di Azure:

   ```PowerShell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```
3. Passare alla directory in cui è installato il [strumenti di Azure Stack](https://github.com/Azure/AzureStack-Tools) come parte dell'installazione di PowerShell, ad esempio, c:\azurestack-tools-master. Quindi, passare alla directory infrastruttura ed eseguire il comando seguente per importare il modulo di infrastruttura:

   ```PowerShell
   Import-Module .\AzureStack.Infra.psm1
    ```
4. Utilizzare i comandi come negli esempi seguenti per gestire gli avvisi:
   ```PowerShell
   #Retrieve all alerts
   Get-AzsAlert -location [Region]

   #Filter for active alerts
   $Active=Get-AzsAlert -location [Region] | Where {$_.State -eq "active"}
   $Active

   #Close alert
   Close-AzsAlert -location [Region] -AlertID "ID"

   #Retrieve resource provider health
   Get-AzsResourceProviderHealths -location [Region]

   #Retrieve infrastructure role instance health
   Get-AzsInfrastructureRoleHealths -location [Region]
   ```

## <a name="use-the-rest-api-to-monitor-health-and-alerts"></a>Utilizzare l'API REST per avvisi e monitoraggio dell'integrità

È possibile utilizzare le chiamate API REST per ottenere avvisi, chiudere gli avvisi e ottenere l'integrità dei provider di risorse.

### <a name="get-alert"></a>Ottenere l'avviso

**Richiesta**

La richiesta ottiene tutti gli avvisi attivi e chiusi per la sottoscrizione del provider predefinito. Non vi è alcun corpo della richiesta.


|Metodo  |URI della richiesta  |
|---------|---------|
|GET     |   https://{armendpoint}/Subscriptions/{subId}/resourceGroups/System. {RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts?api-version=2016-05-01 "      |
|     |         |

**Argomenti**

|Argomento  |Descrizione  |
|---------|---------|
|armendpoint     |  L'endpoint di gestione risorse di Azure dell'ambiente dello Stack di Azure, il formato di https://adminmanagement. {RegionName}. {FQDN esterno}. Ad esempio, se è il nome FQDN esterno *azurestack.external* e nome dell'area *locale*, quindi l'endpoint di gestione risorse è https://adminmanagement.local.azurestack.external.       |
|subid     |   ID sottoscrizione dell'utente che effettua la chiamata. È possibile utilizzare questa API per eseguire query solo con un utente che dispone dell'autorizzazione per la sottoscrizione del provider predefinito.      |
|RegionName     |    Il nome dell'area della distribuzione di Azure Stack.     |
|api-version     |  Versione del protocollo utilizzato per effettuare questa richiesta. È necessario utilizzare 2016-05-01.      |
|     |         |

**Risposta**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/Alerts?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Active",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},

…
```

**Dettagli della risposta**


|  Argomento  |Descrizione  |
|---------|---------|
|*id*     |      ID univoco dell'avviso.   |
|*nome*     |     Nome interno dell'avviso.   |
|*type*     |     Definizione di risorsa.    |
|*Località*     |       Nome dell'area.     |
|*tag*     |   Tag delle risorse.     |
|*closedtimestamp*    |  Ora UTC in cui è stato chiuso l'avviso.    |
|*createdtimestamp*     |     Ora UTC quando è stato creato l'avviso.   |
|*description*     |    Descrizione dell'avviso.     |
|*faultid*     | Componente interessato.        |
|*alertid*     |  ID univoco dell'avviso.       |
|*faulttypeid*     |  Tipo univoco del componente guasto.       |
|*lastupdatedtimestamp*     |   Ora UTC dell'ultimo aggiornamento informazioni sugli avvisi.    |
|*HealthState*     | Stato di integrità complessivo.        |
|*nome*     |   Nome dell'avviso specifico.      |
|*fabricname*     |    Nome registrato dell'infrastruttura del componente non corretto.   |
|*description*     |  Descrizione del componente dell'infrastruttura registrati.   |
|*tipo di servizio*     |   Tipo di servizio registrati dell'infrastruttura.   |
|*monitoraggio e aggiornamento*     |   Procedura di correzione consigliata.    |
|*type*     |   Tipo di avviso.    |
|*resourceRegistrationid*    |     ID della risorsa interessata registrata.    |
|*resourceProviderRegistrationID*   |    ID del provider di risorse registrati del componente interessato.  |
|*serviceregistrationid*     |    ID del servizio registrato.   |
|*livello di gravità*     |     Gravità dell'avviso.  |
|*state*     |    Stato dell'avviso.   |
|*titolo*     |    Titolo dell'avviso.   |
|*impactedresourceid*     |     ID della risorsa interessata.    |
|*ImpactedresourceDisplayName*     |     Nome della risorsa interessata.  |
|*closedByUserAlias*     |   Utente che ha chiuso l'avviso.      |

### <a name="close-alert"></a>Chiudi avviso

**Richiesta**

La richiesta chiude un avviso dall'ID univoco.

|Metodo    |URI della richiesta  |
|---------|---------|
|PUT     |   https://{armendpoint}/Subscriptions/{subId}/resourceGroups/System. {RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts/alertid?api-version=2016-05-01 "    |

**Argomenti**


|Argomento  |Descrizione  |
|---------|---------|
|*armendpoint*     |   Endpoint di gestione delle risorse dell'ambiente dello Stack di Azure, il formato di https://adminmanagement. {RegionName}. {FQDN esterno}. Ad esempio, se è il nome FQDN esterno *azurestack.external* e nome dell'area *locale*, quindi l'endpoint di gestione risorse è https://adminmanagement.local.azurestack.external.      |
|*subid*     |    ID sottoscrizione dell'utente che effettua la chiamata. È possibile utilizzare questa API per eseguire query solo con un utente che dispone dell'autorizzazione per la sottoscrizione del provider predefinito.     |
|*RegionName*     |   Il nome dell'area della distribuzione di Azure Stack.      |
|*versione dell'API*     |    Versione del protocollo utilizzato per effettuare questa richiesta. È necessario utilizzare 2016-05-01.     |
|*alertid*     |    ID univoco dell'avviso.     |

**Corpo**

```json

{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"2017-08-10T20:18:58.1584868Z",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```
**Risposta**

```http
PUT https://adminmanagement.local.azurestack.external//subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```

**Dettagli della risposta**


|  Argomento  |Descrizione  |
|---------|---------|
|*id*     |      ID univoco dell'avviso.   |
|*nome*     |     Nome interno dell'avviso.   |
|*type*     |     Definizione di risorsa.    |
|*Località*     |       Nome dell'area.     |
|*tag*     |   Tag delle risorse.     |
|*closedtimestamp*    |  Ora UTC in cui è stato chiuso l'avviso.    |
|*createdtimestamp*     |     Ora UTC quando è stato creato l'avviso.   |
|*Descrizione*     |    Descrizione dell'avviso.     |
|*faultid*     | Componente interessato.        |
|*alertid*     |  ID univoco dell'avviso.       |
|*faulttypeid*     |  Tipo univoco del componente guasto.       |
|*lastupdatedtimestamp*     |   Ora UTC dell'ultimo aggiornamento informazioni sugli avvisi.    |
|*HealthState*     | Stato di integrità complessivo.        |
|*nome*     |   Nome dell'avviso specifico.      |
|*fabricname*     |    Nome registrato dell'infrastruttura del componente non corretto.   |
|*description*     |  Descrizione del componente dell'infrastruttura registrati.   |
|*tipo di servizio*     |   Tipo di servizio registrati dell'infrastruttura.   |
|*monitoraggio e aggiornamento*     |   Procedura di correzione consigliata.    |
|*type*     |   Tipo di avviso.    |
|*resourceRegistrationid*    |     ID della risorsa interessata registrata.    |
|*resourceProviderRegistrationID*   |    ID del provider di risorse registrati del componente interessato.  |
|*serviceregistrationid*     |    ID del servizio registrato.   |
|*livello di gravità*     |     Gravità dell'avviso.  |
|*state*     |    Stato dell'avviso.   |
|*titolo*     |    Titolo dell'avviso.   |
|*impactedresourceid*     |     ID della risorsa interessata.    |
|*ImpactedresourceDisplayName*     |     Nome della risorsa interessata.  |
|*closedByUserAlias*     |   Utente che ha chiuso l'avviso.      |

### <a name="get-resource-provider-health"></a>Ottenere l'integrità di provider di risorse

**Richiesta**

La richiesta ottiene lo stato di integrità per tutti i provider di risorse registrato.


|Metodo  |URI della richiesta  |
|---------|---------|
|GET    |   https://{armendpoint}/Subscriptions/{subId}/resourceGroups/System. {RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths?api-version=2016-05-01 "   |


**Argomenti**


|Argomenti  |Descrizione  |
|---------|---------|
|*armendpoint*     |    L'endpoint di gestione delle risorse dell'ambiente dello Stack di Azure, il formato di https://adminmanagement. {RegionName}. {FQDN esterno}. Ad esempio, se il nome FQDN esterno è azurestack.external e nome dell'area è locale, l'endpoint di gestione risorse è https://adminmanagement.local.azurestack.external.     |
|*subid*     |     ID sottoscrizione dell'utente che effettua la chiamata. È possibile utilizzare questa API per eseguire query solo con un utente che dispone dell'autorizzazione per la sottoscrizione del provider predefinito.    |
|*RegionName*     |     Il nome dell'area della distribuzione di Azure Stack.    |
|*versione dell'API*     |   Versione del protocollo utilizzato per effettuare questa richiesta. È necessario utilizzare 2016-05-01.      |


**Risposta**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths?api-version=2016-05-01
```

```json
{
"value":[
{
"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"name":"03ccf38f-f6b1-4540-9dc8ec7b6389ecca",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths",
"location":"local",
"tags":{},
"properties":{
"registrationId":"03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"displayName":"Key Vault",
"namespace":"Microsoft.KeyVault.Admin",
"routePrefix":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local",
"serviceLocation":"local",
"infraURI":"/subscriptions/4aa97de3-6b83-4582-86e1-65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local/infraRoles/Key Vault",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},
"healthState":"Healthy"
}
}

…
```
**Dettagli della risposta**


|Argomento  |Descrizione  |
|---------|---------|
|*Id*     |   ID univoco dell'avviso.      |
|*nome*     |  Nome interno dell'avviso.       |
|*type*     |  Definizione di risorsa.       |
|*Località*     |  Nome dell'area.       |
|*tag*     |     Tag delle risorse.    |
|*registrationId*     |   Registrazione univoco per il provider di risorse.      |
|*displayName*     |Nome visualizzato del provider di risorse.        |
|*spazio dei nomi*     |   Implementa l'API dello spazio dei nomi del provider di risorse.       |
|*routePrefix*     |    URI per interagire con il provider di risorse.     |
|*serviceLocation*     |   Area con cui è registrato questo provider di risorse.      |
|*infraURI*     |   URI del provider di risorse elencato come un ruolo di infrastruttura.      |
|*alertSummary*     |   Riepilogo di avviso critico e di avviso associata al provider di risorse.      |
|*healthState*     |    Stato di integrità del provider di risorse.     |


### <a name="get-resource-health"></a>Ottenere l'integrità delle risorse

La richiesta ottiene lo stato di integrità per un provider di risorse registrato specifico.

**Richiesta**

|Metodo  |URI della richiesta  |
|---------|---------|
|GET     |     https://{armendpoint}/Subscriptions/{subId}/resourceGroups/System. {RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths/{RegistrationID}/resourceHealths?api-version=2016-05-01 "    |

**Argomenti**

|Argomenti  |Descrizione  |
|---------|---------|
|*armendpoint*     |    L'endpoint di gestione delle risorse dell'ambiente dello Stack di Azure, il formato di https://adminmanagement. {RegionName}. {FQDN esterno}. Ad esempio, se il nome FQDN esterno è azurestack.external e nome dell'area è locale, l'endpoint di gestione risorse è https://adminmanagement.local.azurestack.external.     |
|*subid*     |ID sottoscrizione dell'utente che effettua la chiamata. È possibile utilizzare questa API per eseguire query solo con un utente che dispone dell'autorizzazione per la sottoscrizione del provider predefinito.         |
|*RegionName*     |  Il nome dell'area della distribuzione di Azure Stack.       |
|*versione dell'API*     |  Versione del protocollo utilizzato per effettuare questa richiesta. È necessario utilizzare 2016-05-01.       |
|*RegistrationID* |ID di registrazione per un provider di risorse specifico. |

**Risposta**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca /resourceHealths?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":
[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/472aaaa6-3f63-43fa-a489-4fd9094e235f/resourceHealths/028c3916-ab86-4e7f-b5c2-0468e607915c",
"name":"028c3916-ab86-4e7f-b5c2-0468e607915c",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths/resourceHealths",
"location":"local",
"tags":{},
"properties":
{"registrationId":"028c3916-ab86-4e7f-b5c2 0468e607915c","namespace":"Microsoft.Fabric.Admin","routePrefix":"/subscriptions/4aa97de3-6b83-4582-86e1 65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local",
"resourceType":"infraRoles",
"resourceName":"Privileged endpoint",
"usageMetrics":[],
"resourceLocation":"local",
"resourceURI":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/Privileged endpoint",
"rpRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},"healthState":"Unknown"
}
}
…
```

**Dettagli della risposta**

|Argomento  |Descrizione  |
|---------|---------|
|*Id*     |   ID univoco dell'avviso.      |
|*nome*     |  Nome interno dell'avviso.       |
|*type*     |  Definizione di risorsa.       |
|*Località*     |  Nome dell'area.       |
|*tag*     |     Tag delle risorse.    |
|*registrationId*     |   Registrazione univoco per il provider di risorse.      |
|*resourceType*     |Tipo di risorsa.        |
|*resourceName*     |   Nome di risorsa.   |
|*usageMetrics*     |    Metrica di utilizzo per la risorsa.     |
|*resourceLocation*     |   Nome dell'area in cui è distribuito.      |
|*resourceURI*     |   URI della risorsa.   |
|*alertSummary*     |   Riepilogo delle critici e avvisi, lo stato di integrità.     |

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul monitoraggio dello stato incorporate, vedere [monitorare l'integrità e avvisi in Azure Stack](azure-stack-monitor-health.md).


