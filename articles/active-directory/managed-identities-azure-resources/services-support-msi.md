---
title: Servizi di Azure che supportano le identità gestite per le risorse di Azure
description: Elenco di servizi che supportano le identità gestite per le risorse di Azure e l'autenticazione di Azure AD
services: active-directory
author: daveba
ms.author: daveba
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: ca7ce29adb0b83215b64065ef83ff476025b8e81
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199715"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Servizi che supportano le identità gestite per le risorse di Azure

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. Con un'identità gestita è possibile eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. È in corso l'integrazione dell'autenticazione di Azure AD e delle identità gestite per le risorse di Azure in Azure. Controllare spesso gli aggiornamenti.

> [!NOTE]
> Identità gestite per le risorse di Azure è il nuovo nome per il servizio precedentemente noto come identità del servizio gestita.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Servizi di Azure che supportano le identità gestite per le risorse di Azure

I servizi di Azure seguenti supportano le identità gestite per le risorse di Azure:

### <a name="azure-virtual-machines"></a>Macchine virtuali di Azure

|Tipo di identità gestita |  Disponibile a livello generale<br>Aree globali di Azure | Azure Government|Azure Germania|21Vianet per Azure Cina|
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile | Preview | Preview | Preview | Preview |
| Assegnata dall'utente | Preview | Preview | Preview | Preview | Preview

Vedere l'elenco seguente per configurare l'identità gestita per le macchine virtuali di Azure (nelle aree in cui è disponibile):

- [Portale di Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md)
- [Modelli di Gestione risorse di Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Set di scalabilità delle macchine virtuali di Azure

|Tipo di identità gestita |  Disponibile a livello generale<br>Aree globali di Azure | Azure Government|Azure Germania|21Vianet per Azure Cina|
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile | Preview | Preview | Preview |
| Assegnata dall'utente | Preview | Preview | Preview | Preview

Vedere l'elenco seguente per configurare l'identità gestita per il set di scalabilità di macchine virtuali di Azure (nelle aree in cui è disponibile):

- [Portale di Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md)
- [Modelli di Gestione risorse di Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Servizio app di Azure

|Tipo di identità gestita |  Disponibile a livello generale<br>Aree globali di Azure | Azure Government|Azure Germania|21Vianet per Azure Cina|
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile | Disponibile | Disponibile | Disponibile |
| Assegnata dall'utente | Preview | Non disponibile | Non disponibile | Non disponibile

Vedere l'elenco seguente per configurare l'identità gestita per il Servizio app di Azure (nelle aree in cui è disponibile):

- [Portale di Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interfaccia della riga di comando di Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modello di Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-functions"></a>Funzioni di Azure

Tipo di identità gestita |  Disponibile a livello generale<br>Aree globali di Azure | Azure Government|Azure Germania|21Vianet per Azure Cina|
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile | Disponibile | Disponibile | Disponibile |
| Assegnata dall'utente | Preview | Non disponibile | Non disponibile | Non disponibile

Vedere l'elenco seguente per configurare l'identità gestita per Funzioni di Azure (nelle aree in cui è disponibile):

- [Portale di Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interfaccia della riga di comando di Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modello di Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>App per la logica di Azure

Tipo di identità gestita |  Disponibile a livello generale<br>Aree globali di Azure | Azure Government|Azure Germania|21Vianet per Azure Cina|
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile | Disponibile | Disponibile | Disponibile |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile

Vedere l'elenco seguente per configurare l'identità gestita per le App per la logica di Azure (nelle aree in cui è disponibile):

- [Portale di Azure](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Modello di Azure Resource Manager](/azure/app-service/overview-managed-identity#deployment-template)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Tipo di identità gestita |  Disponibile a livello generale<br>Aree globali di Azure | Azure Government|Azure Germania|21Vianet per Azure Cina|
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile

Vedere l'elenco seguente per configurare l'identità gestita per Azure Data Factory V2 (nelle aree in cui è disponibile):

- [Portale di Azure](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk)

### <a name="azure-api-management"></a>Gestione API di Azure

Tipo di identità gestita |  Disponibile a livello generale<br>Aree globali di Azure | Azure Government|Azure Germania|21Vianet per Azure Cina|
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile | Disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile

Vedere l'elenco seguente per configurare l'identità gestita per Gestione API di Azure (nelle aree in cui è disponibile):

- [Modello di Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Istanze di contenitore di Azure

Tipo di identità gestita |  Disponibile a livello generale<br>Aree globali di Azure | Azure Government|Azure Germania|21Vianet per Azure Cina|
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Linux: Preview<br>Windows: Non disponibile | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Linux: Preview<br>Windows: Non disponibile | Non disponibile | Non disponibile | Non disponibile

Vedere l'elenco seguente per configurare l'identità gestita per Istanze di Azure Container (nelle aree in cui è disponibile):

- [Interfaccia della riga di comando di Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modello di Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


## <a name="azure-services-that-support-azure-ad-authentication"></a>Servizi di Azure che supportano l'autenticazione di Azure AD

I servizi seguenti supportano l'autenticazione di Azure AD e sono stati testati con i servizi client che usano le identità gestite per le risorse di Azure.

| Service | ID risorsa | Status | Assegnare l'accesso |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | `https://management.azure.com/` | Disponibile | [Portale di Azure](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Interfaccia della riga di comando di Azure](howto-assign-access-CLI.md) <br>[Modello di Azure Resource Manager](../../role-based-access-control/role-assignments-template.md) |
| Azure Key Vault | `https://vault.azure.net` | Disponibile |  
| Azure Data Lake | `https://datalake.azure.net/` | Disponibile |
| SQL di Azure | `https://database.windows.net/` | Disponibile |
| Hub eventi di Azure | `https://eventhubs.azure.net` | Preview |
| Bus di servizio di Azure | `https://servicebus.azure.net` | Preview |
| Archiviazione di Azure | `https://storage.azure.com/` | Preview |