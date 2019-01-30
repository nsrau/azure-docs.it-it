---
title: Controllare l'utilizzo delle risorse di Azure rispetto ai limiti | Microsoft Docs
description: Informazioni su come controllare l'utilizzo delle risorse di Azure rispetto ai limiti della sottoscrizione.
services: networking
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: networking
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.author: jdial
ms.openlocfilehash: 0b15861f663c98d3b873f95a0ea6c485ada91fb6
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421607"
---
# <a name="check-resource-usage-against-limits"></a>Controllare l'utilizzo delle risorse rispetto ai limiti

In questo articolo si apprenderà come visualizzare il numero per ogni tipo di risorsa di rete distribuito nell'ambito della sottoscrizione e quali sono i [limiti della sottoscrizione](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits). La possibilità di visualizzare l'utilizzo di risorse rispetto ai limiti è utile per tenere traccia dell'utilizzo corrente e pianificare quello futuro. Per tenere traccia dell'utilizzo, è possibile usare il [portale di Azure](#azure-portal), [PowerShell](#powershell) o l'[interfaccia della riga di comando di Azure](#azure-cli).

## <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nell'angolo in alto a sinistra del portale di Azure selezionare **Tutti i servizi**.
3. Nella casella *Filtro* immettere **Sottoscrizioni**. Selezionare la voce **Sottoscrizioni** quando viene visualizzata nei risultati della ricerca.
4. Selezionare il nome della sottoscrizione per cui visualizzare le informazioni sull'utilizzo.
5. In **Impostazioni** selezionare **Utilizzo + quote**.
6. È possibile selezionare le opzioni seguenti:
    - **Tipi di risorsa**: è possibile selezionare tutti i tipi di risorse o tipi specifici di risorse da visualizzare.
    - **Provider**: è possibile selezionare tutti i provider di risorse oppure **Calcolo**, **Rete** o **Archiviazione**.
    - **Località**: è possibile selezionare tutte le località di Azure oppure località specifiche.
    - È possibile scegliere di visualizzare tutte le risorse o solo i tipi di cui è distribuita almeno una risorsa.

    L'esempio nell'immagine seguente mostra tutte le risorse di rete con almeno una risorsa distribuita nell'area Stati Uniti orientali:

        ![View usage data](./media/check-usage-against-limits/view-usage.png)

    È possibile ordinare le colonne selezionando le relative intestazioni. I limiti indicati sono quelli definiti per la sottoscrizione. Se è necessario aumentare un limite predefinito, selezionare **Richiedi aumento** e quindi completare e inviare la richiesta di supporto. Tutte le risorse hanno un limite massimo elencato nell'articolo relativo ai [limiti](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) di Azure. Se il limite corrente è già quello massimo, non è possibile aumentarlo.

## <a name="powershell"></a>PowerShell

È possibile eseguire i comandi seguenti in [Azure Cloud Shell](https://shell.azure.com/powershell) oppure in PowerShell dal computer. Azure Cloud Shell è una shell interattiva gratuita. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Se si esegue PowerShell dal computer, è necessario il modulo *AzureRM* di PowerShell versione 6.0.1 o successiva. Per trovare la versione installata, eseguire `Get-Module -ListAvailable AzureRM` nel computer. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per accedere ad Azure.

Per visualizzare l'utilizzo rispetto ai limiti, eseguire [Get-AzureRmNetworkUsage](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.8.0). L'esempio seguente ottiene le informazioni sull'utilizzo delle risorse dove almeno una risorsa è distribuita nell'area Stati Uniti orientali:

```azurepowershell-interactive
Get-AzureRmNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

L'output restituito ha lo stesso formato dell'output di esempio seguente:

```powershell
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Se si usano i comandi dell'interfaccia della riga di comando di Azure per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/bash) o tramite l'interfaccia della riga di comando dal computer in uso. Questo articolo richiede l'interfaccia della riga di comando di Azure 2.0.32 o versioni successive. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando di Azure in locale, è anche necessario eseguire `az login` per accedere ad Azure.

Per visualizzare l'utilizzo rispetto ai limiti, eseguire [az network list-usages](/cli/azure/network?view=azure-cli-latest#az-network-list-usages). L'esempio seguente ottiene le informazioni sull'utilizzo delle risorse nell'area Stati Uniti orientali:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

L'output restituito ha lo stesso formato dell'output di esempio seguente:

```azurecli
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```
