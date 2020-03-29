---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74926231"
---
Installare le versioni più recenti dei moduli di PowerShell per Gestione dei servizi di Azure e il modulo ExpressRoute. Non è possibile usare l'ambiente CloudShell di Azure per eseguire i moduli SM.

1. Usare le istruzioni nell'articolo Installazione del modulo Gestione servizi per installare il modulo di gestione dei servizi di Azure.Use the instructions in the [Installing the Service Management module](/powershell/azure/servicemanagement/install-azure-ps) article to install the Azure Service Management Module. Se il modulo Az o RM è già installato, assicurarsi di utilizzare '-AllowClobber'.
2. Importare i moduli installati. Quando si usa l'esempio seguente, modificare il percorso in modo che rifletta il percorso e la versione dei moduli di PowerShell installati.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Per accedere all'account Azure, aprire la console di PowerShell con diritti elevati e connettersi all'account. Utilizzare l'esempio seguente per connettersi utilizzando il modulo Gestione assistenza:

   ```powershell
   Add-AzureAccount
   ```