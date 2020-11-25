---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018163"
---
Installare le versioni più recenti dei moduli di PowerShell per Gestione dei servizi di Azure e il modulo ExpressRoute. Non è possibile usare l'ambiente Azure CloudShell per eseguire moduli di Gestione dei servizi.

1. Per installare il modulo di Gestione dei servizi di Azure, seguire le istruzioni riportate nell'articolo [Installazione del modulo di Gestione dei servizi](/powershell/azure/servicemanagement/install-azure-ps). Se è già installato il modulo Az o di Resource Manager, assicurarsi di usare "-AllowClobber".
2. Importare i moduli installati. Quando si usa l'esempio seguente, modificare il percorso in base alla posizione e alla versione dei moduli di PowerShell installati.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Per accedere all'account Azure, aprire la console di PowerShell con diritti elevati e connettersi all'account. Usare l'esempio seguente per connettersi tramite il modulo di Gestione dei servizi:

   ```powershell
   Add-AzureAccount
   ```