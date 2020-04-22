---
title: Abilitare il servizio peering di Azure in un peering diretto tramite PowerShellEnable Azure Peering Service on a Direct peering by using PowerShell
titleSuffix: Azure
description: Abilitare il servizio peering di Azure in un peering diretto tramite PowerShellEnable Azure Peering Service on a Direct peering by using PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c029b822d00ce545e3623a6212421a55b2d1971f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686984"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Abilitare il servizio peering di Azure in un peering diretto tramite PowerShellEnable Azure Peering Service on a Direct peering by using PowerShell

Questo articolo descrive come abilitare il servizio peering di Azure in un peering diretto usando i cmdlet di PowerShell e il modello di distribuzione di Azure Resource Manager.This article describes how to enable Azure [Peering Service](overview-peering-service.md) on a Direct peering by using PowerShell cmdlets and the Azure Resource Manager deployment model.

Se si preferisce, è possibile completare questa guida usando il [portale](howto-peering-service-portal.md)di Azure .

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare i [prerequisiti](prerequisites.md) prima di iniziare la configurazione.
* Scegliere un peering diretto nella sottoscrizione per cui si vuole abilitare il servizio di peering. Se non ne hai uno, converti un peering diretto legacy o crea un nuovo peering diretto:
    * Per convertire un peering diretto legacy, seguire le istruzioni in Convertire un peering diretto legacy in una risorsa di Azure tramite PowerShell.To convert a legacy Direct peering, follow the instructions in [Convert a legacy Direct peering to an Azure resource by using PowerShell](howto-legacy-direct-powershell.md).
    * Per creare un nuovo peering diretto, seguire le istruzioni in [Creare o modificare un peering diretto tramite PowerShell.](howto-direct-powershell.md)

### <a name="work-with-azure-powershell"></a>Usare Azure PowerShellWork with Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Abilitare il servizio di peering in un peering diretto

### <a name="view-direct-peering"></a><a name= get></a>Visualizzare il peering direttoView Direct peering
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Abilitare il peering diretto per il servizio peeringEnable the Direct peering for Peering Service

Dopo aver ottenuto il peering diretto nel passaggio precedente, abilitarlo per il servizio di peering.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificare una connessione di peering direttoModify a Direct peering connection

Se è necessario modificare le impostazioni di connessione, vedere la sezione "Modificare un peering diretto" in [Creare o modificare un peering diretto tramite PowerShell](howto-direct-powershell.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare il peering di Exchange tramite PowerShellCreate or modify Exchange peering by using PowerShell](howto-exchange-powershell.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure tramite PowerShellConvert a legacy Exchange peering to an Azure resource by using PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Risorse aggiuntive
È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```powershell
Get-Help Get-AzPeering -detailed
```

Per le domande frequenti, vedere le domande frequenti sul servizio di [peering](service-faqs.md).