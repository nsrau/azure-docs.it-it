---
title: Servizi di Azure che supportano Identità del servizio gestita
description: Elenco di servizi che supportano Identità del servizio gestita e l'autenticazione di Azure AD
services: active-directory
author: daveba
ms.author: daveba
ms.date: 03/28/2018
ms.topic: reference
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: ff56928748f536ffe1cfc30891585565e962d9fe
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930294"
---
# <a name="services-that-support-managed-service-identity"></a>Servizi che supportano Identità del servizio gestita 

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. Con un'identità gestita è possibile eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. È in corso l'integrazione dell'autenticazione di Azure AD e dell'Identità del servizio gestito in Azure. Controllare spesso gli aggiornamenti.

## <a name="azure-services-that-support-managed-service-identity"></a>Servizi di Azure che supportano Identità del servizio gestito

I servizi di Azure seguenti supportano Identità del servizio gestito.

| Service | Status | Data | Configurare | Acquisizione di un token |
| ------- | ------ | ---- | --------- | ----------- |
| Macchine virtuali di Azure | Preview | Settembre 2017 | [Portale di Azure](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md)<br>[Modelli di Gestione risorse di Azure](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Servizio app di Azure | Preview | Settembre 2017 | [Portale di Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Modello di Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Funzioni di Azure | Preview | Settembre 2017 | [Portale di Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Modello di Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | Preview | Novembre 2017 | [Portale di Azure](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

## <a name="azure-services-that-support-azure-ad-authentication"></a>Servizi di Azure che supportano l'autenticazione di Azure AD

I servizi seguenti supportano l'autenticazione di Azure AD e sono stati testati con i servizi client che usano l'Identità del servizio gestito.

| Service | ID risorsa | Status | Data | Assegnare l'accesso |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com | Disponibile | Settembre 2017 | [Portale di Azure](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Interfaccia della riga di comando di Azure](howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | Disponibile | Settembre 2017 | |
| Azure Data Lake | https://datalake.azure.net | Disponibile | Settembre 2017 | |
| SQL di Azure | https://database.windows.net | Disponibile | Ottobre 2017 | |
| Hub eventi di Azure | https://eventhubs.azure.net | Disponibile | Dicembre 2017 | |
| Bus di servizio di Azure | https://servicebus.azure.net | Disponibile | Dicembre 2017 | |