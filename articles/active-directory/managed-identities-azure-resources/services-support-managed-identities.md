---
title: Azure Services that support managed identities - Azure AD
description: Elenco di servizi che supportano le identità gestite per le risorse di Azure e l'autenticazione di Azure AD
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 09/24/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ead9b53f530a309d6bdb3bd384c29650bf5c8e6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224297"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Servizi che supportano le identità gestite per le risorse di Azure

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. Con un'identità gestita è possibile eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. È in corso l'integrazione dell'autenticazione di Azure AD e delle identità gestite per le risorse di Azure in Azure. Controllare spesso gli aggiornamenti.

> [!NOTE]
> Identità gestite per le risorse di Azure è il nuovo nome assegnato al servizio precedentemente noto come Identità del servizio gestita.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Servizi di Azure che supportano le identità gestite per le risorse di Azure

I servizi di Azure seguenti supportano le identità gestite per le risorse di Azure:

### <a name="azure-virtual-machines"></a>Macchine virtuali di Azure

| Tipo di identità gestita | Disponibile a livello generale<br>Aree a livello globale di Azure | Azure per enti pubblici | Azure Germania | 21Vianet per Azure Cina |
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile | Preview | Preview | Preview | 
| Assegnata dall'utente | Disponibile | Preview | Preview | Preview |

Vedere l'elenco seguente per configurare l'identità gestita per le macchine virtuali di Azure (nelle aree in cui è disponibile):

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md)
- [Modelli di Gestione risorse di Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Set di scalabilità delle macchine virtuali di Azure

|Tipo di identità gestita | Disponibile a livello generale<br>Aree a livello globale di Azure | Azure per enti pubblici | Azure Germania | 21Vianet per Azure Cina |
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile | Preview | Preview | Preview |
| Assegnata dall'utente | Disponibile | Preview | Preview | Preview |

Vedere l'elenco seguente per configurare l'identità gestita per il set di scalabilità di macchine virtuali di Azure (nelle aree in cui è disponibile):

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md)
- [Modelli di Gestione risorse di Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Servizio app di Azure

| Tipo di identità gestita | Disponibile a livello generale<br>Aree a livello globale di Azure | Azure per enti pubblici | Azure Germania | 21Vianet per Azure Cina |
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile | Disponibile | Disponibile | Disponibile |
| Assegnata dall'utente | Disponibile | Non disponibile | Non disponibile | Non disponibile |

Vedere l'elenco seguente per configurare l'identità gestita per il Servizio app di Azure (nelle aree in cui è disponibile):

- [Azure portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [interfaccia della riga di comando di Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modello di Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprint

|Tipo di identità gestita | Disponibile a livello generale<br>Aree a livello globale di Azure | Azure per enti pubblici | Azure Germania | 21Vianet per Azure Cina |
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile | Disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Disponibile | Disponibile | Non disponibile | Non disponibile |

Refer to the following list to use a managed identity with [Azure Blueprints](../../governance/blueprints/overview.md):

- [Azure portal - blueprint assignment](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - blueprint assignment](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Funzioni di Azure

Tipo di identità gestita |Disponibile a livello generale<br>Aree a livello globale di Azure | Azure per enti pubblici | Azure Germania | 21Vianet per Azure Cina |
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile | Disponibile | Disponibile | Disponibile |
| Assegnata dall'utente | Disponibile | Non disponibile | Non disponibile | Non disponibile |

Vedere l'elenco seguente per configurare l'identità gestita per Funzioni di Azure (nelle aree in cui è disponibile):

- [Azure portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [interfaccia della riga di comando di Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modello di Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>App per la logica di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree a livello globale di Azure | Azure per enti pubblici | Azure Germania | 21Vianet per Azure Cina |
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Preview | Preview | Non disponibile | Preview |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Vedere l'elenco seguente per configurare l'identità gestita per le App per la logica di Azure (nelle aree in cui è disponibile):

- [Azure portal](/azure/logic-apps/create-managed-service-identity#azure-portal-system-logic-app)
- [Modello di Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Tipo di identità gestita | Disponibile a livello generale<br>Aree a livello globale di Azure | Azure per enti pubblici | Azure Germania | 21Vianet per Azure Cina |
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Vedere l'elenco seguente per configurare l'identità gestita per Azure Data Factory V2 (nelle aree in cui è disponibile):

- [Azure portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Gestione API di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree a livello globale di Azure | Azure per enti pubblici | Azure Germania | 21Vianet per Azure Cina |
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile | Disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Vedere l'elenco seguente per configurare l'identità gestita per Gestione API di Azure (nelle aree in cui è disponibile):

- [Modello di Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Istanze di Azure Container

Tipo di identità gestita | Disponibile a livello generale<br>Aree a livello globale di Azure | Azure per enti pubblici | Azure Germania | 21Vianet per Azure Cina |
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Linux: anteprima<br>Windows: non disponibile | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Linux: anteprima<br>Windows: non disponibile | Non disponibile | Non disponibile | Non disponibile |

Vedere l'elenco seguente per configurare l'identità gestita per Istanze di Azure Container (nelle aree in cui è disponibile):

- [interfaccia della riga di comando di Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modello di Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Attività di Registro Azure Container

Tipo di identità gestita | Disponibile a livello generale<br>Aree a livello globale di Azure | Azure per enti pubblici | Azure Germania | 21Vianet per Azure Cina |
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Preview | Non disponibile | Non disponibile | Non disponibile |

Refer to the following list to configure managed identity for Azure Container Registry Tasks (in regions where available):

- [interfaccia della riga di comando di Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Servizi di Azure che supportano l'autenticazione di Azure AD

I servizi seguenti supportano l'autenticazione di Azure AD e sono stati testati con i servizi client che usano le identità gestite per le risorse di Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Refer to the following list to configure access to Azure Resource Manager:

- [Assign access via Azure portal](howto-assign-access-portal.md)
- [Assign access via Powershell](howto-assign-access-powershell.md)
- [Assign access via Azure CLI](howto-assign-access-CLI.md)
- [Assign access via Azure Resource Manager template](../../role-based-access-control/role-assignments-template.md)

| Cloud | ID risorsa | Status |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| Disponibile |
| Azure per enti pubblici | `https://management.usgovcloudapi.net/` | Disponibile |
| Azure Germania | `https://management.microsoftazure.de/` | Disponibile |
| 21Vianet per Azure Cina | `https://management.chinacloudapi.cn` | Disponibile |

### <a name="azure-key-vault"></a>Azure Key Vault

| Cloud | ID risorsa | Status |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| Disponibile |
| Azure per enti pubblici | `https://vault.usgovcloudapi.net` | Disponibile |
| Azure Germania |  `https://vault.microsoftazure.de` | Disponibile |
| 21Vianet per Azure Cina | `https://vault.azure.cn` | Disponibile |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Cloud | ID risorsa | Status |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | Disponibile |
| Azure per enti pubblici |  | Non disponibile |
| Azure Germania |   | Non disponibile |
| 21Vianet per Azure Cina |  | Non disponibile |

### <a name="azure-sql"></a>Azure SQL 

| Cloud | ID risorsa | Status |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | Disponibile |
| Azure per enti pubblici | `https://database.usgovcloudapi.net/` | Disponibile |
| Azure Germania | `https://database.cloudapi.de/` | Disponibile |
| 21Vianet per Azure Cina | `https://database.chinacloudapi.cn/` | Disponibile |

### <a name="azure-event-hubs"></a>Hub eventi di Azure

| Cloud | ID risorsa | Status |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | Disponibile |
| Azure per enti pubblici |  | Non disponibile |
| Azure Germania |   | Non disponibile |
| 21Vianet per Azure Cina |  | Non disponibile |

### <a name="azure-service-bus"></a>Service Bus di Azure

| Cloud | ID risorsa | Status |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | Disponibile |
| Azure per enti pubblici |  | Disponibile |
| Azure Germania |   | Non disponibile |
| 21Vianet per Azure Cina |  | Non disponibile |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blobs and queues

| Cloud | ID risorsa | Status |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Disponibile |
| Azure per enti pubblici | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | Disponibile |
| Azure Germania | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | Disponibile |
| 21Vianet per Azure Cina | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | Disponibile |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | ID risorsa | Status |
|--------|------------|--------|
| Azure Global | `https://*.asazure.windows.net` | Disponibile |
| Azure per enti pubblici | `https://*.asazure.usgovcloudapi.net` | Disponibile |
| Azure Germania | `https://*.asazure.cloudapi.de` | Disponibile |
| 21Vianet per Azure Cina | `https://*.asazure.chinacloudapi.cn` | Disponibile |
