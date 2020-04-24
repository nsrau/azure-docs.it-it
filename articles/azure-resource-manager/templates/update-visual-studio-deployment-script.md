---
title: Aggiornare lo script di distribuzione dei modelli di Visual Studio per usare AZ PowerShell
description: Aggiornare lo script di distribuzione dei modelli di Visual Studio da AzureRM a AZ PowerShell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963870"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Aggiornare lo script di distribuzione del modello di Visual Studio per usare AZ PowerShell module

Visual Studio 16,4 supporta l'uso del modulo AZ PowerShell nello script di distribuzione del modello. Tuttavia, Visual Studio non installa automaticamente il modulo. Per usare il modulo AZ, è necessario eseguire quattro passaggi:

1. [Disinstalla modulo AzureRM](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Installare AZ Module](/powershell/azure/install-az-ps)
1. Eseguire l'aggiornamento a Visual Studio to 16.4
1. Aggiornare lo script di distribuzione nel progetto.

## <a name="update-visual-studio-to-164"></a>Eseguire l'aggiornamento a Visual Studio to 16.4

Aggiornare l'installazione di Visual Studio alla versione 16,4 o successiva. Durante l'aggiornamento, verificare che il componente Azure PowerShell non sia selezionato. Poiché è stato installato il modulo AZ tramite la raccolta, non si vuole reinstallare il modulo AzureRM.

Se è già stato eseguito l'aggiornamento a 16,4 e il componente Azure PowerShell è stato selezionato, è possibile disinstallarlo eseguendo il Programma di installazione di Visual Studio. Non selezionare il componente Azure PowerShell nel carico di lavoro di Azure o nella pagina dei singoli componenti.

## <a name="update-the-deployment-script-in-your-project"></a>Aggiornare lo script di distribuzione nel progetto

Sostituire tutte le occorrenze della stringa 'AzureRm' con 'Az' nello script di distribuzione. Per visualizzare le modifiche, vedere le revisioni in questo [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619). Per ulteriori informazioni sull'aggiornamento degli script al modulo AZ, vedere [eseguire la migrazione di Azure PowerShell da AzureRM a AZ](/powershell/azure/migrate-from-azurerm-to-az).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'uso del progetto di Visual Studio, vedere [creazione e distribuzione di progetti di gruppi di risorse di Azure tramite Visual Studio](create-visual-studio-deployment-project.md).
