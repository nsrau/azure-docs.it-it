---
title: Abilitare il servizio peering di Azure in un peering diretto usando PowerShell
titleSuffix: Azure
description: Abilitare il servizio peering di Azure in un peering diretto usando PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 98341fbbbcafb6aee938870c22050c6edec352ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079048"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Abilitare il servizio peering di Azure in un peering diretto usando PowerShell

Questo articolo descrive come abilitare il [servizio peering](overview-peering-service.md) di Azure in un peering diretto usando i cmdlet di PowerShell e il modello di distribuzione Azure Resource Manager.

Se si preferisce, è possibile completare questa guida usando il [portale](howto-peering-service-portal.md)di Azure.

## <a name="before-you-begin"></a>Prima di iniziare
* Verificare i [prerequisiti](prerequisites.md) prima di iniziare la configurazione.
* Scegliere un peering diretto nella sottoscrizione per cui si vuole abilitare il servizio di peering. Se non si dispone di uno, convertire un peering diretto legacy o creare un nuovo peering diretto:
    * Per convertire un peering diretto legacy, seguire le istruzioni riportate in [convertire un peering diretto legacy in una risorsa di Azure usando PowerShell](howto-legacy-direct-powershell.md).
    * Per creare un nuovo peering diretto, seguire le istruzioni riportate in [creare o modificare un peering diretto usando PowerShell](howto-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Usare Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Abilitare il servizio di peering in un peering diretto

### <a name="view-direct-peering"></a><a name= get></a>Visualizza peering diretto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Abilitare il peering diretto per il servizio di peering

Dopo aver ottenuto il peering diretto nel passaggio precedente, abilitarlo per il servizio di peering.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificare una connessione peering diretto

Se è necessario modificare le impostazioni di connessione, vedere la sezione "modificare un peering diretto" in [creare o modificare un peering diretto usando PowerShell](howto-direct-powershell.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare il peering di Exchange tramite PowerShell](howto-exchange-powershell.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure tramite PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Risorse aggiuntive
È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```powershell
Get-Help Get-AzPeering -detailed
```

Per domande frequenti, vedere domande [frequenti sul servizio di peering](service-faqs.md).
