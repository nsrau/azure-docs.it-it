---
title: Aggiornare lo script di distribuzione del modello di Visual Studio per usare Az PowerShellUpdate Visual Studio's template deployment script to use Az PowerShell
description: Aggiornare lo script di distribuzione del modello di Visual Studio da AzureRM a Az PowerShellUpdate the Visual Studio template deployment script from AzureRM to Az PowerShell
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
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Aggiornare lo script di distribuzione dei modelli di Visual Studio per usare il modulo Az PowerShellUpdate Visual Studio template deployment script to use Az PowerShell module

Visual Studio 16.4 supporta l'utilizzo del modulo Az PowerShell nello script di distribuzione del modello. Tuttavia, Visual Studio non installa automaticamente il modulo. Per utilizzare il modulo Az, è necessario eseguire quattro passaggi:

1. [Disinstallare il modulo di AzureRMUninstall AzureRM module](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Modulo Installa Az](/powershell/azure/install-az-ps)
1. Eseguire l'aggiornamento a Visual Studio to 16.4
1. Aggiornare lo script di distribuzione nel progetto.

## <a name="update-visual-studio-to-164"></a>Eseguire l'aggiornamento a Visual Studio to 16.4

Aggiornare l'installazione di Visual Studio alla versione 16.4 o successiva. Durante l'aggiornamento, assicurarsi che il componente Azure PowerShell non sia selezionato. Dopo aver installato il modulo Az tramite la raccolta, non si vuole reinstallare il modulo AzureRM.

Se è già stato eseguito l'aggiornamento alla 16.4 ed è stato selezionato il componente di Azure PowerShell, è possibile disinstallarlo eseguendo il programma di installazione di Visual Studio.If you already upgraded to 16.4 and the Azure PowerShell component was checked, you can uninstall it by running the Visual Studio Installer. Non selezionare il componente di Azure PowerShell nel carico di lavoro di Azure o nella pagina dei singoli componenti.

## <a name="update-the-deployment-script-in-your-project"></a>Aggiornare lo script di distribuzione nel progetto

Sostituire tutte le occorrenze della stringa 'AzureRm' con 'Az' nello script di distribuzione. Per visualizzare le modifiche, vedere le revisioni in questo [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619). Per altre informazioni sull'aggiornamento di script al modulo Az, vedere Eseguire la migrazione di Azure PowerShell da AzureRM ad Az.For more information about upgrading scripts to the Az module, [see Migrate Azure PowerShell from AzureRM to Az](/powershell/azure/migrate-from-azurerm-to-az).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'uso del progetto di Visual Studio, vedere Creazione e distribuzione di progetti di gruppi di [risorse di Azure tramite Visual Studio](create-visual-studio-deployment-project.md).
