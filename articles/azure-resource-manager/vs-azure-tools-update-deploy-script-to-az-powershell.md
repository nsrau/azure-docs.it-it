---
title: Aggiornare lo script di distribuzione dei modelli di Visual Studio per usare Az PowerShell
description: Aggiornare lo script di distribuzione dei modelli di Visual Studio da AzureRM ad Az PowerShell
author: cweining
ms.service: azure-resource-manager
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: cweining
ms.openlocfilehash: 483b20f0003994f88ec35cc9f31a77cf571804f3
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695685"
---
# <a name="updating-the-visual-studio-template-deployment-script-to-use-az-powershell"></a>Aggiornamento dello script di distribuzione dei modelli di Visual Studio per usare Az PowerShell

Visual Studio 16.4 supporta l'uso di Az PowerShell nello script di distribuzione dei modelli. Visual Studio non installa Az PowerShell né aggiorna automaticamente lo script di distribuzione nel progetto del gruppo di risorse. Per usare la versione più recente di Az PowerShell, è necessario eseguire queste quattro operazioni:
1. Disinstallare AzureRM PowerShell
1. Installare Az PowerShell
1. Eseguire l'aggiornamento a Visual Studio 16.4
1. Aggiornare lo script di distribuzione nel progetto.

## <a name="uninstall-azurerm-powershell"></a>Disinstallare AzureRM PowerShell
Per disinstallare AzureRM PowerShell, seguire queste [istruzioni](https://docs.microsoft.com/powershell/azure/uninstall-az-ps?view=azps-2.7.0#uninstall-the-azurerm-module).

## <a name="install-az-powershell"></a>Installare Az PowerShell
Per installare AzureRM PowerShell, seguire queste [istruzioni](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.7.0).

## <a name="update-visual-studio-to-164"></a>Eseguire l'aggiornamento a Visual Studio to 16.4
Eseguire l'aggiornamento a Visual Studio 16.4 quando è disponibile. Durante l'aggiornamento, assicurarsi che il componente Azure PowerShell non sia selezionato. Poiché è stato installato Az PowerShell tramite la raccolta, non si vuole installare la versione di AzureRM di PowerShell che viene installata con Visual Studio.

Se è già stato eseguito l'aggiornamento alla versione 16.4 e il componente Azure PowerShell è stato selezionato, è possibile disinstallarlo tramite l'esecuzione del Programma di installazione di Visual Studio deselezionando il componente Azure PowerShell nel carico di lavoro di Azure o nella pagina dei singoli componenti.

## <a name="update-the-deployment-script-in-your-project"></a>Aggiornare lo script di distribuzione nel progetto
Sostituire tutte le occorrenze della stringa 'AzureRm' con 'Az' nello script di distribuzione. Per visualizzare le modifiche, vedere le revisioni in questo [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619). Per altre informazioni sull'aggiornamento degli script ad Az PowerShell, vedere questa [documentazione](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-2.5.0).


