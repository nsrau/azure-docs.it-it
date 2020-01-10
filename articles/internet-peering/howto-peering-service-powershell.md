---
title: Abilitare il servizio di peering in un peering diretto usando PowerShell
titleSuffix: Azure
description: Abilitare il servizio di peering in un peering diretto usando PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774185"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Abilitare il servizio di peering in un peering diretto usando PowerShell

Questo articolo descrive come abilitare il [servizio di peering](overview-peering-service.md) in un peering diretto usando i cmdlet di PowerShell e il modello di distribuzione gestione risorse.

Se si preferisce, è possibile completare questa guida usando il [portale](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Verificare i [prerequisiti](prerequisites.md) prima di iniziare la configurazione.
* Scegliere un peering diretto nella sottoscrizione in cui si vuole abilitare il servizio peering. Se non si dispone di uno, convertire un peering diretto legacy o creare un nuovo peering diretto.
    * Per convertire un peering diretto legacy, seguire le istruzioni riportate in [convertire un peering diretto legacy in una risorsa di Azure usando PowerShell](howto-legacy-direct-powershell.md).
    * Per creare un nuovo peering diretto, seguire le istruzioni riportate in [creare o modificare un peering diretto usando PowerShell](howto-direct-powershell.md).

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Abilitare il servizio di peering in un peering diretto

### <a name= get></a>Visualizza peering diretto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name= get></a>Abilitare il peering diretto per il servizio di peering

Dopo aver ricevuto il peering diretto nel passaggio precedente, abilitarlo per il servizio di peering.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificare una connessione peering diretto

Se è necessario modificare le impostazioni di connessione, vedere la sezione **modificare un peering diretto** in [creare o modificare un peering diretto tramite PowerShell](howto-direct-powershell.md)

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare il peering di Exchange con PowerShell](howto-exchange-powershell.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure usando PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Risorse aggiuntive
È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```powershell
Get-Help Get-AzPeering -detailed
```

Per domande frequenti, vedere domande [frequenti sul servizio peering](service-faqs.md).