---
title: Abilitare il servizio peering in un peering diretto tramite PowerShellEnable Peering Service on a Direct peering using PowerShell
titleSuffix: Azure
description: Abilitare il servizio peering in un peering diretto tramite PowerShellEnable Peering Service on a Direct peering using PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774185"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Abilitare il servizio peering in un peering diretto tramite PowerShellEnable Peering Service on a Direct peering using PowerShell

In questo articolo viene descritto come abilitare il servizio di peering in un peering diretto utilizzando i cmdlet di PowerShell e il modello di distribuzione di Resource Manager.This article describes how to enable [Peering Service](overview-peering-service.md) on a Direct peering by using PowerShell cmdlets and the Resource Manager deployment model.

Se si preferisce, è possibile completare questa guida utilizzando il [portale](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare [i prerequisiti](prerequisites.md) prima di iniziare la configurazione.
* Scegliere un peering diretto nella sottoscrizione per cui abilitare il servizio di peering. Se non ne hai uno, converti un peering Diretto legacy o crea un nuovo peering diretto.
    * Per convertire un peering diretto legacy, seguire le istruzioni in Convertire un peering diretto legacy in risorse di Azure tramite PowerShell.To convert a legacy Direct peering, follow the instructions in [Convert a legacy Direct peering to Azure resource using PowerShell.](howto-legacy-direct-powershell.md)
    * Per creare un nuovo peering diretto, seguire le istruzioni in [Creare o modificare un peering diretto tramite PowerShell.](howto-direct-powershell.md)

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Abilitare il servizio di peering in un peering diretto

### <a name="view-direct-peering"></a><a name= get></a>Visualizzare il peering direttoView Direct peering
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Abilitare il peering diretto per il servizio peeringEnable the Direct peering for Peering Service

Dopo aver ottenuto il peering diretto nel passaggio precedente, abilitarlo per il servizio peering.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificare una connessione di peering direttoModify a Direct peering connection

Se è necessario modificare le impostazioni di connessione, vedere la sezione Modificare un peering diretto in [Creare o modificare un peering diretto tramite PowerShellIf](howto-direct-powershell.md) you need to modify connection settings, refer to Modify a **Direct peering** section in Create or modify a Direct peering using PowerShell

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare il peering di Exchange tramite PowerShellCreate or modify Exchange peering using PowerShell](howto-exchange-powershell.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure usando PowerShellConvert a legacy Exchange peering to Azure resource using PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Risorse aggiuntive
È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```powershell
Get-Help Get-AzPeering -detailed
```

Per le domande frequenti, vedere Domande frequenti sul servizio di [peering](service-faqs.md).