---
title: Come usare le identità gestite per le risorse di Azure in una macchina virtuale di Azure con Azure SDK
description: Esempi di codice per l'uso di Azure SDK con una macchina virtuale di Azure che ha gestito le identità per le risorse di Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35498d51f9f6cd75058100189c223c0dd0f83a0c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188686"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Come usare le identità gestite per le risorse di Azure in una macchina virtuale di Azure con Azure SDK 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Questo articolo fornisce un elenco di esempi di SDK, che illustrano l'uso del supporto di Azure SDK per le identità gestite per le risorse di Azure.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Tutto il codice/script di esempio in questo articolo presuppone che il client sia in esecuzione su una macchina virtuale con le identità gestite per risorse di Azure abilitate. Usare la funzionalità di connessione alla macchina virtuale nel portale di Azure per connettersi in remoto alla macchina virtuale. Per informazioni dettagliate sull'abilitazione delle identità gestite per risorse di Azure in una macchina virtuale, vedere [Configurare le identità gestite per le risorse di Azure in una macchina virtuale tramite il portale di Azure](qs-configure-portal-windows-vm.md) o una delle varianti dell'articolo riguardanti PowerShell, l'interfaccia della riga di comando, un modello o un Azure SDK. 

## <a name="sdk-code-samples"></a>Esempi di codice SDK

| SDK             | Esempio di codice |
| --------------- | ----------- |
| .NET            | [Deploy an Azure Resource Manager template from a Windows VM using managed identities for Azure resources](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) (Distribuire un modello di Azure Resource Manager da una macchina virtuale Windows usando identità gestite per le risorse di Azure) |
| .NET Core       | [Call Azure services from a Linux VM using managed identities for Azure resources](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) (Chiamare servizi Azure da una macchina virtuale Linux usando identità gestite per le risorse di Azure) |
| Node.js         | [Manage resources using managed identities for Azure resources](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) (Gestione delle risorse usando le identità gestite per le risorse di Azure) |
| Python          | [Use managed identities for Azure resources to authenticate simply from inside a VM](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) (Uso delle identità gestite per le risorse di Azure per l'autenticazione semplice da una macchina virtuale) |
| Ruby            | [Manage resources from a VM with managed identities for Azure resources enabled](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) (Gestione delle risorse da una macchina virtuale con identità gestite per le risorse di Azure abilitate) |

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure SDK](https://azure.microsoft.com/downloads/) per l'elenco completo delle risorse di Azure SDK, inclusi i download di librerie, la documentazione e altro ancora.
- Per abilitare le identità gestite per le risorse di Azure in una macchina virtuale di Azure, vedere [Configurare le identità gestite per le risorse di Azure in una macchina virtuale tramite il portale di Azure](qs-configure-portal-windows-vm.md).








