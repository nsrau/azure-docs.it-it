---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926231"
---
Installare le versioni più recenti dei moduli di PowerShell per Gestione dei servizi di Azure e il modulo ExpressRoute. Non è possibile usare l'ambiente Azure CloudShell per eseguire i moduli SM.

1. Usare le istruzioni riportate nell'articolo [installazione del modulo di gestione dei servizi](/powershell/azure/servicemanagement/install-azure-ps) per installare il modulo gestione dei servizi di Azure. Se è già installato il modulo AZ o RM, assicurarsi di usare "-AllowClobber".
2. Importare i moduli installati. Quando si usa l'esempio seguente, modificare il percorso in modo da riflettere il percorso e la versione dei moduli di PowerShell installati.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Per accedere al proprio account Azure, aprire la console di PowerShell con diritti elevati e connettersi al proprio account. Usare l'esempio seguente per la connessione tramite il modulo di gestione dei servizi:

   ```powershell
   Add-AzureAccount
   ```