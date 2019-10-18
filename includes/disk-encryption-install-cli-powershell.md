---
title: file di inclusione
description: file di inclusione
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 05794a046fdcb15a91145a75717a6a454d15a8da
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72511487"
---
Crittografia dischi di Azure può essere abilitata e gestita tramite l'interfaccia della riga di comando di [Azure](/cli/azure) e [Azure PowerShell](/powershell/azure/new-azureps-module-az). A tale scopo, è necessario installare gli strumenti localmente e connettersi alla sottoscrizione di Azure.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'[interfaccia della riga di comando di Azure 2.0](/cli/azure) è uno strumento da riga di comando per la gestione delle risorse di Azure. L'interfaccia della riga di comando è progettata per eseguire query sui dati in modo flessibile, supportare operazioni a esecuzione prolungata come processi non bloccanti e semplificare la creazione di script. È possibile installarlo in locale seguendo i passaggi descritti in [installare l'interfaccia della](/cli/azure/install-azure-cli?view=azure-cli-latest)riga di comando di Azure.

Per [accedere al proprio account Azure con l'interfaccia della](/cli/azure/authenticate-azure-cli)riga di comando di Azure, usare il comando [AZ login](/cli/azure/reference-index?view=azure-cli-latest#az-login) .

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
Il [Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az) fornisce un set di cmdlet che usa il modello di [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) per la gestione delle risorse di Azure. È possibile usarlo nel browser con [Azure cloud Shell](/azure/cloud-shell/overview)oppure è possibile installarlo nel computer locale seguendo le istruzioni riportate in [Install the Azure PowerShell Module](/powershell/azure/install-az-ps). 

Se è già installato in locale, assicurarsi di usare la versione più recente di Azure PowerShell SDK per configurare Crittografia dischi di Azure. Scaricare la versione più recente di [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

Per [accedere al proprio account Azure con Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0), usare il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```powershell
Connect-AzAccount
```

Se si dispone di più sottoscrizioni e si vuole specificarne una, usare il cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) per elencarle, seguito dal cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Eseguendo il cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) , viene verificato che sia stata selezionata la sottoscrizione corretta.

Per verificare che i cmdlet di crittografia dischi di Azure siano installati, usare il cmdlet [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) :
     
```powershell
Get-command *diskencryption*
```
Per ulteriori informazioni, vedere [Introduzione a Azure PowerShell](/powershell/azure/get-started-azureps). 