---
title: includere il file
description: includere file
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 05794a046fdcb15a91145a75717a6a454d15a8da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "72511487"
---
La funzionalità Crittografia dischi di Azure può essere abilitata e gestita tramite l'[interfaccia della riga di comando di Azure](/cli/azure) e [Azure PowerShell](/powershell/azure/new-azureps-module-az). A tale scopo, è necessario installare gli strumenti localmente e connettersi alla sottoscrizione di Azure.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'[interfaccia della riga di comando di Azure 2.0](/cli/azure) è uno strumento da riga di comando per la gestione delle risorse di Azure. L'interfaccia della riga di comando è progettata per eseguire query sui dati in modo flessibile, supportare operazioni a esecuzione prolungata come processi non bloccanti e semplificare la creazione di script. È possibile installarla in locale seguendo i passaggi descritti in [Installare l'interfaccia della riga di comando di Azure.](/cli/azure/install-azure-cli?view=azure-cli-latest).

Per [accedere all'account di Azure con l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli), usare il comando [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login).

```azurecli
az login
```

Per selezionare un tenant per l'accesso, usare:
    
```azurecli
az login --tenant <tenant>
```

In caso di più sottoscrizioni, per specificarne una in particolare è necessario ottenere l'elenco di sottoscrizioni tramite [az account list](/cli/azure/account#az-account-list) (elenco account di accesso di AZ) e specificarla da [az account set](/cli/azure/account#az-account-set) (imposta account di accesso di AZ).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Per altre informazioni, vedere [Azure Service Fabric e interfaccia della riga di comando di Azure 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
Il [modulo az di Azure PowerShell](/powershell/azure/new-azureps-module-az) offre un set di cmdlet che usano il modello [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) per la gestione delle risorse di Azure. È possibile usarlo nel browser con [Azure Cloud Shell](/azure/cloud-shell/overview) oppure installarlo nel computer locale seguendo le istruzioni disponibili in [Installare il modulo Azure PowerShell](/powershell/azure/install-az-ps). 

Se è già installato in locale, assicurarsi di usare la versione più recente di Azure PowerShell SDK per configurare Crittografia dischi di Azure. Scaricare la versione più recente di [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

Per [accedere all'account di Azure con Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0), usare il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0).

```powershell
Connect-AzAccount
```

Se si hanno più sottoscrizioni e si vuole specificarne una, usare il cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) per elencarle, seguito dal cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0):

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

L'esecuzione del cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verifica la selezione della sottoscrizione corretta.

Per controllare che i cmdlet di Crittografia dischi di Azure siano installati, usare il cmdlet [Get-command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6):
     
```powershell
Get-command *diskencryption*
```
Per altre informazioni, vedere [Introduzione ad Azure PowerShell](/powershell/azure/get-started-azureps). 