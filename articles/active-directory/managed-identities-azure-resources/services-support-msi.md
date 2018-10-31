---
title: Servizi di Azure che supportano le identità gestite per le risorse di Azure
description: Elenco di servizi che supportano le identità gestite per le risorse di Azure e l'autenticazione di Azure AD
services: active-directory
author: daveba
ms.author: daveba
ms.date: 10/23/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: 25da856d8bd72f4e74718cf32f62bbb0a3eb5b88
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984690"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Servizi che supportano le identità gestite per le risorse di Azure

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. Con un'identità gestita è possibile eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. È in corso l'integrazione dell'autenticazione di Azure AD e delle identità gestite per le risorse di Azure in Azure. Controllare spesso gli aggiornamenti.

> [!NOTE]
> Identità gestite per le risorse di Azure è il nuovo nome per il servizio precedentemente noto come identità del servizio gestita.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Servizi di Azure che supportano le identità gestite per le risorse di Azure

I servizi di Azure seguenti supportano le identità gestite per le risorse di Azure:

| Service | Stato assegnato dal sistema | Stato assegnato dall'utente| Configurare | Acquisizione di un token |
| ------- | ------ | ---- | --------- | ----------- |
| Macchine virtuali di Azure | Disponibile | Preview | [Portale di Azure](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md)<br>[Modelli di Gestione risorse di Azure](qs-configure-template-windows-vm.md)<br>[REST](qs-configure-rest-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Set di scalabilità di macchine virtuali | Disponibile | Preview | [Portale di Azure](qs-configure-portal-windows-vmss.md)<br>[PowerShell](qs-configure-powershell-windows-vmss.md)<br>[Interfaccia della riga di comando di Azure](qs-configure-cli-windows-vmss.md)<br>[Modelli di Gestione risorse di Azure](qs-configure-template-windows-vmss.md)<br>[REST](qs-configure-rest-vmss.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell)
| Servizio app di Azure | Windows: disponibile <br> Linux: presto disponibile | Non disponibile | [Portale di Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Interfaccia della riga di comando di Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Modello di Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Funzioni di Azure | Disponibile | Non disponibile | [Portale di Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Interfaccia della riga di comando di Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Modello di Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| App per la logica di Azure | Disponibile | Non disponibile | [Portale di Azure](/azure/logic-apps/create-managed-service-identity#azure-portal)<br>[Modello di Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#deployment-template) |  |
| Azure Data Factory V2 | Disponibile | Non disponibile | [Portale di Azure](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Gestione API di Azure | Disponibile | Non disponibile | [Modello di Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity) |
| Istanze di contenitore di Azure | Linux: anteprima<br>Windows: non disponibile | Linux: anteprima<br>Windows: non disponibile | [Interfaccia della riga di comando di Azure](~/articles/container-instances/container-instances-managed-identity.md)<br>[Modello di Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)<br>[YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file) |  |


## <a name="azure-services-that-support-azure-ad-authentication"></a>Servizi di Azure che supportano l'autenticazione di Azure AD

I servizi seguenti supportano l'autenticazione di Azure AD e sono stati testati con i servizi client che usano le identità gestite per le risorse di Azure.

| Service | ID risorsa | Status | Data | Assegnare l'accesso |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | `https://management.azure.com/` | Disponibile | Settembre 2017 | [Portale di Azure](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Interfaccia della riga di comando di Azure](howto-assign-access-CLI.md) |
| Azure Key Vault | `https://vault.azure.net` | Disponibile | Settembre 2017 | |
| Azure Data Lake | `https://datalake.azure.net/` | Disponibile | Settembre 2017 | |
| SQL di Azure | `https://database.windows.net/` | Disponibile | Ottobre 2017 | |
| Hub eventi di Azure | `https://eventhubs.azure.net` | Preview | Dicembre 2017 | |
| Bus di servizio di Azure | `https://servicebus.azure.net` | Preview | Dicembre 2017 | |
| Archiviazione di Azure | `https://storage.azure.com/` | Preview | Maggio 2018 | |