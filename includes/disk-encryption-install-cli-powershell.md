---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 05794a046fdcb15a91145a75717a6a454d15a8da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72511487"
---
Crittografia disco di Azure può essere abilitata e gestita tramite [l'interfaccia della riga di comando di Azure](/cli/azure) e Azure [PowerShell.](/powershell/azure/new-azureps-module-az) A tale scopo, è necessario installare gli strumenti in locale e connettersi alla sottoscrizione di Azure.To do so you must install the tools locally and connect to your Azure subscription.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'[interfaccia della riga di comando di Azure 2.0](/cli/azure) è uno strumento da riga di comando per la gestione delle risorse di Azure. L'interfaccia della riga di comando è progettata per eseguire query sui dati in modo flessibile, supportare operazioni a esecuzione prolungata come processi non bloccanti e semplificare la creazione di script. È possibile installarlo in locale seguendo la procedura descritta in [Installare l'interfaccia della riga di comando](/cli/azure/install-azure-cli?view=azure-cli-latest)di Azure.

Per [accedere all'account Azure con l'interfaccia della riga](/cli/azure/authenticate-azure-cli)di comando di Azure, usare il comando az [login.](/cli/azure/reference-index?view=azure-cli-latest#az-login)

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
Il modulo az di Azure PowerShell fornisce un set di cmdlet che usa il modello di Azure Resource Manager per la gestione delle risorse di Azure.The [Azure PowerShell az module](/powershell/azure/new-azureps-module-az) provides a set of cmdlets that uses the Azure Resource [Manager](/azure/azure-resource-manager/resource-group-overview) model for managing your Azure resources. È possibile usarlo nel browser con [Azure Cloud Shell](/azure/cloud-shell/overview)oppure installarlo nel computer locale seguendo le istruzioni in Installare il modulo di Azure [PowerShell.](/powershell/azure/install-az-ps) 

Se è già installato in locale, assicurarsi di usare la versione più recente di Azure PowerShell SDK per configurare Crittografia dischi di Azure. Scaricare la versione più recente di [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

Per [accedere all'account Azure con Azure PowerShell,](/powershell/azure/authenticate-azureps?view=azps-2.5.0)usare il cmdlet [Connect-AzAccount.To](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) Sign in to your Azure account with Azure PowerShell , use the Connect-AzAccount cmdlet.

```powershell
Connect-AzAccount
```

Se si dispone di più sottoscrizioni e si desidera specificarne una, utilizzare il cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) per elencarle, seguito dal cmdlet [Set-AzContext:](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

L'esecuzione del cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verificherà che sia stata selezionata la sottoscrizione corretta.

Per verificare che i cmdlet di Crittografia disco di Azure siano installati, usare il cmdlet [Get-command:](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6)
     
```powershell
Get-command *diskencryption*
```
Per altre informazioni, vedere [Introduzione ad Azure PowerShell.For more](/powershell/azure/get-started-azureps)information, see Getting started with Azure PowerShell. 