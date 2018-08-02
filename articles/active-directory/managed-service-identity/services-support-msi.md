---
title: Servizi di Azure che supportano Identità del servizio gestita
description: Elenco di servizi che supportano Identità del servizio gestita e l'autenticazione di Azure AD
services: active-directory
author: daveba
ms.author: daveba
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: 74fb9e784122dadd1ad2f6f29a497398eacf7464
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412883"
---
# <a name="services-that-support-managed-service-identity"></a>Servizi che supportano Identità del servizio gestita 

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. Con un'identità gestita è possibile eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. È in corso l'integrazione dell'autenticazione di Azure AD e dell'identità del servizio gestita in Azure. Controllare spesso gli aggiornamenti.

## <a name="azure-services-that-support-managed-service-identity"></a>Servizi di Azure che supportano Identità del servizio gestito

I servizi di Azure seguenti supportano Identità del servizio gestito.

| Service | Stato assegnato dal sistema | Stato assegnato dall'utente| Configurare | Acquisizione di un token |
| ------- | ------ | ---- | --------- | ----------- |
| Macchine virtuali di Azure | Preview | Preview | [portale di Azure](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md)<br>[Modelli di Gestione risorse di Azure](qs-configure-template-windows-vm.md)<br>[REST](qs-configure-rest-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Set di scalabilità di macchine virtuali | Preview | Preview | [Portale di Azure](qs-configure-portal-windows-vmss.md)<br>[PowerShell](qs-configure-powershell-windows-vmss.md)<br>[Interfaccia della riga di comando di Azure](qs-configure-cli-windows-vmss.md)<br>[Modelli di Gestione risorse di Azure](qs-configure-template-windows-vmss.md)<br>[REST](qs-configure-rest-vmss.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell)
| Servizio app di Azure | Disponibile | Non disponibile | [portale di Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Interfaccia della riga di comando di Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Modello di Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Funzioni di Azure | Disponibile | Non disponibile | [portale di Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Interfaccia della riga di comando di Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Modello di Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Azure Data Factory V2 | Preview | Non disponibile | [portale di Azure](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Gestione API di Azure | Preview | Non disponibile | [Modello di Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity) | 


## <a name="azure-services-that-support-azure-ad-authentication"></a>Servizi di Azure che supportano l'autenticazione di Azure AD

I servizi seguenti supportano l'autenticazione di Azure AD e sono stati testati con i servizi client che usano l'Identità del servizio gestito.

| Service | ID risorsa | Status | Data | Assegnare l'accesso |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | Disponibile | Settembre 2017 | [portale di Azure](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Interfaccia della riga di comando di Azure](howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | Disponibile | Settembre 2017 | |
| Azure Data Lake | https://datalake.azure.net/ | Disponibile | Settembre 2017 | |
| SQL di Azure | https://database.windows.net/ | Disponibile | Ottobre 2017 | |
| Hub eventi di Azure | https://eventhubs.azure.net | Disponibile | Dicembre 2017 | |
| Bus di servizio di Azure | https://servicebus.azure.net | Disponibile | Dicembre 2017 | |
| Archiviazione di Azure | https://storage.azure.com/ | Preview | Maggio 2018 | |
