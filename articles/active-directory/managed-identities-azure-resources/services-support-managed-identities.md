---
title: Servizi di Azure che supportano le identità gestite - Azure AD
description: Elenco di servizi che supportano le identità gestite per le risorse di Azure e l'autenticazione di Azure AD
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 07/09/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: 207b4a926e77ff55faad388b6eeaeb221ec252a8
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006781"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Servizi che supportano le identità gestite per le risorse di Azure

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. Con un'identità gestita è possibile eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. È in corso l'integrazione dell'autenticazione di Azure AD e delle identità gestite per le risorse di Azure in Azure. Controllare spesso gli aggiornamenti.

> [!NOTE]
> Identità gestite per le risorse di Azure è il nuovo nome per il servizio precedentemente noto come identità del servizio gestita.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Servizi di Azure che supportano le identità gestite per le risorse di Azure

I servizi di Azure seguenti supportano le identità gestite per le risorse di Azure:


### <a name="azure-api-management"></a>Gestione API di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | Anteprima | Anteprima | Non disponibile | Anteprima |

Vedere l'elenco seguente per configurare l'identità gestita per Gestione API di Azure (nelle aree in cui è disponibile):

- [Modello di Azure Resource Manager](../../api-management/api-management-howto-use-managed-service-identity.md)


### <a name="azure-app-service"></a>Servizio app di Azure

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] |
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check]  | ![Disponibile][check]  | ![Disponibile][check] |

Vedere l'elenco seguente per configurare l'identità gestita per il Servizio app di Azure (nelle aree in cui è disponibile):

- [Azure portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Interfaccia della riga di comando di Azure](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Modello di Azure Resource Manager](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes con abilitazione di Azure Arc

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | Anteprima | Non disponibile | Non disponibile | Non disponibile | 
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Azure Arc Enabled Kubernetes supporta attualmente l' [identità assegnata dal sistema](../../azure-arc/kubernetes/connect-cluster.md#azure-arc-agents-for-kubernetes). Il certificato di identità del servizio gestito viene usato da tutti gli agenti Kubernetes abilitati per Azure Arc per la comunicazione con Azure.

### <a name="azure-blueprints"></a>Azure Blueprint

|Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | Non disponibile |
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | Non disponibile |

Fare riferimento all'elenco seguente per usare un'identità gestita con [Azure Blueprints](../../governance/blueprints/overview.md):

- [Portale di Azure - assegnazione del progetto](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [API REST - assegnazione del progetto](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Ricerca cognitiva di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

### <a name="azure-cognitive-services"></a>Servizi cognitivi di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |


### <a name="azure-container-instances"></a>Istanze di Azure Container

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | Linux: Anteprima<br>Windows: Non disponibile | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Linux: Anteprima<br>Windows: Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Vedere l'elenco seguente per configurare l'identità gestita per Istanze di Azure Container (nelle aree in cui è disponibile):

- [Interfaccia della riga di comando di Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modello di Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Attività di Registro Azure Container

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Anteprima | Non disponibile | Non disponibile | Non disponibile |

Fare riferimento all'elenco seguente per configurare l'identità gestita per Attività del Registro Azure Container, nelle aree in cui è disponibile:

- [Interfaccia della riga di comando di Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Esplora dati di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Vedere l'elenco seguente per configurare l'identità gestita per Azure Data Factory V2 (nelle aree in cui è disponibile):

- [Azure portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)



### <a name="azure-event-grid"></a>Griglia di eventi di Azure 

Tipo di identità gestita |Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | Anteprima | Anteprima | Non disponibile | Anteprima |
| Assegnata dall'utente | Non disponibile | Non disponibile  | Non disponibile  | Non disponibile |









### <a name="azure-functions"></a>Funzioni di Azure

Tipo di identità gestita |Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] |
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check]  | ![Disponibile][check]  | ![Disponibile][check]  |

Vedere l'elenco seguente per configurare l'identità gestita per Funzioni di Azure (nelle aree in cui è disponibile):

- [Azure portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Interfaccia della riga di comando di Azure](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Modello di Azure Resource Manager](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Hub IoT Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Vedere l'elenco seguente per configurare l'identità gestita per Azure Data Factory V2 (nelle aree in cui è disponibile):

- [Azure portal](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Importazione/Esportazione di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile nell'area in cui è disponibile il servizio importazione/esportazione di Azure | Anteprima | Disponibile | Disponibile |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

### <a name="azure-kubernetes-service-aks"></a>Servizio Azure Kubernetes

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] | 
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |


Per altre informazioni, vedere [Usare le identità gestite nel servizio Azure Kubernetes](../../aks/use-managed-identity.md).


### <a name="azure-logic-apps"></a>App per la logica di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |


Vedere l'elenco seguente per configurare l'identità gestita per le App per la logica di Azure (nelle aree in cui è disponibile):

- [Azure portal](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Modello di Azure Resource Manager](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)


### <a name="azure-policy"></a>Criteri di Azure

|Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Vedere l'elenco seguente per configurare l'identità gestita per i criteri di Azure (nelle aree in cui sono disponibili):

- [Azure portal](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Interfaccia della riga di comando di Azure](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create)
- [Modelli di Gestione risorse di Azure](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/resources/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

L' [identità gestita per le applicazioni Service Fabric](../../service-fabric/concepts-managed-identity.md) è disponibile in tutte le aree geografiche.

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | ![Disponibile][check] | Non disponibile | Non disponibile |Non disponibile |

Fare riferimento all'elenco seguente per configurare l'identità gestita per le applicazioni di Service Fabric di Azure in tutte le regioni:

- [Modello di Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | Non disponibile | Non disponibile | Non disponibile | 
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |


Per altre informazioni, vedere [come abilitare l'identità gestita assegnata dal sistema per l'applicazione Azure Spring cloud](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md).


### <a name="azure-virtual-machine-scale-sets"></a>Set di scalabilità delle macchine virtuali di Azure

|Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | Anteprima | Anteprima | Anteprima |
| Assegnata dall'utente | ![Disponibile][check] | Anteprima | Anteprima | Anteprima |

Vedere l'elenco seguente per configurare l'identità gestita per il set di scalabilità di macchine virtuali di Azure (nelle aree in cui è disponibile):

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md)
- [Modelli di Gestione risorse di Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Macchine virtuali di Azure

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Anteprima | Anteprima | 
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check] | Anteprima | Anteprima |

Vedere l'elenco seguente per configurare l'identità gestita per le macchine virtuali di Azure (nelle aree in cui è disponibile):

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md)
- [Modelli di Gestione risorse di Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [Azure SDK](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Generatore di immagini VM di Azure

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | Non disponibile | Non disponibile | Non disponibile | Non disponibile | 
| Assegnata dall'utente | [Disponibile in aree supportate](../../virtual-machines/windows/image-builder-overview.md#regions) | Non disponibile | Non disponibile | Non disponibile |

Per informazioni su come configurare l'identità gestita per generatore di immagini di VM di Azure (in aree in cui è disponibile), vedere [Panoramica di Image Builder](../../virtual-machines/windows/image-builder-overview.md#permissions).
### <a name="azure-signalr-service"></a>Servizio Azure SignalR

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | Anteprima | Anteprima | Non disponibile | Anteprima |
| Assegnata dall'utente | Anteprima | Anteprima | Non disponibile | Anteprima |

Vedere l'elenco seguente per configurare l'identità gestita per il servizio Azure SignalR (nelle aree in cui sono disponibili):

- [Modello di Azure Resource Manager](../../azure-signalr/howto-use-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Servizi di Azure che supportano l'autenticazione di Azure AD

I servizi seguenti supportano l'autenticazione di Azure AD e sono stati testati con i servizi client che usano le identità gestite per le risorse di Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Fare riferimento all'elenco seguente per configurare l'accesso ad Azure Resource Manager:

- [Assegnare l'accesso tramite il portale di Azure](howto-assign-access-portal.md)
- [Assegnare l'accesso tramite PowerShell](howto-assign-access-powershell.md)
- [Assegnare l'accesso tramite l'interfaccia della riga di comando di Azure](howto-assign-access-CLI.md)
- [Assegnare l'accesso tramite il modello di Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Disponibile][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Disponibile][check] |
| Azure Germania | `https://management.microsoftazure.de/` | ![Disponibile][check] |
| 21Vianet per Azure Cina | `https://management.chinacloudapi.cn` | ![Disponibile][check] |

### <a name="azure-key-vault"></a>Insieme di credenziali chiave di Azure

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Disponibile][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Disponibile][check] |
| Azure Germania |  `https://vault.microsoftazure.de` | ![Disponibile][check] |
| 21Vianet per Azure Cina | `https://vault.azure.cn` | ![Disponibile][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Disponibile][check] |
| Azure Government |  | Non disponibile |
| Azure Germania |   | Non disponibile |
| 21Vianet per Azure Cina |  | Non disponibile |

### <a name="azure-sql"></a>SQL di Azure

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Disponibile][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Disponibile][check] |
| Azure Germania | `https://database.cloudapi.de/` | ![Disponibile][check] |
| 21Vianet per Azure Cina | `https://database.chinacloudapi.cn/` | ![Disponibile][check] |

### <a name="azure-event-hubs"></a>Hub eventi di Azure

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Disponibile][check] |
| Azure Government |  | Non disponibile |
| Azure Germania |   | Non disponibile |
| 21Vianet per Azure Cina |  | Non disponibile |

### <a name="azure-service-bus"></a>Bus di servizio di Azure

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Disponibile][check] |
| Azure Government |  | ![Disponibile][check] |
| Azure Germania |   | Non disponibile |
| 21Vianet per Azure Cina |  | Non disponibile |









### <a name="azure-storage-blobs-and-queues"></a>BLOB e code di Archiviazione di Azure

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Disponibile][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Disponibile][check] |
| Azure Germania | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Disponibile][check] |
| 21Vianet per Azure Cina | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Disponibile][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Disponibile][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Disponibile][check] |
| Azure Germania | `https://*.asazure.cloudapi.de` | ![Disponibile][check] |
| 21Vianet per Azure Cina | `https://*.asazure.chinacloudapi.cn` | ![Disponibile][check] |

> [!Note]
> Microsoft Power BI [supporta anche le identità gestite](../../stream-analytics/powerbi-output-managed-identity.md).


[check]: media/services-support-managed-identities/check.png "Disponibile"
