---
title: Creare un contenitore del servizio cloud con PowerShell | Documentazione Microsoft
description: Questo articolo illustra come creare un contenitore del servizio cloud con PowerShell. Il contenitore ospita il ruolo Web e il ruolo di lavoro.
services: cloud-services
documentationcenter: .net
author: cawaMS
manager: timlt
editor: 
ms.assetid: c8f32469-610e-4f37-a3aa-4fac5c714e13
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: 2023fa7b318f9f76ce1e1ea0a46110297be9a001
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Usare un prompt dei comandi di Azure PowerShell per creare un contenitore del servizio cloud vuoto
Questo articolo illustra come creare velocemente un contenitore di Servizi cloud usando i cmdlet di Azure PowerShell. Eseguire i passaggi seguenti:

1. Installare i cmdlet di Microsoft Azure PowerShell dalla pagina dei [download di Azure PowerShell](http://aka.ms/webpi-azps) .
2. Aprire il prompt dei comandi di PowerShell.
3. Usare [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) per accedere.

   > [!NOTE]
   > Per altre istruzioni sull'installazione del cmdlet di Azure PowerShell e sulla connessione alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Usare il cmdlet **New-AzureService** per creare un contenitore del servizio cloud vuoto.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```
5. Per richiamare il cmdlet, seguire questo esempio:

   ```
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Per altre informazioni sulla creazione del servizio cloud di Azure, eseguire:

```
Get-help New-AzureService
```

### <a name="next-steps"></a>Passaggi successivi
* Per gestire la distribuzione del servizio cloud, vedere i comandi [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) e [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx). Per altre informazioni, è anche possibile vedere [Come configurare i servizi cloud](cloud-services-how-to-configure.md) .
* Per pubblicare il progetto servizio cloud in Azure, vedere l'esempio di codice **PublishCloudService.ps1** riportato nell'articolo [Recapito continuo per Servizi cloud in Azure](cloud-services-dotnet-continuous-delivery.md).
