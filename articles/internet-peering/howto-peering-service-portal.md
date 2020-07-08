---
title: Abilitare il servizio peering di Azure in un peering diretto usando il portale di Azure
titleSuffix: Azure
description: Abilitare il servizio peering di Azure in un peering diretto usando il portale di Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: a52e6038b622c004dc0d133394cd4f53600b2935
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84700043"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Abilitare il servizio peering di Azure in un peering diretto usando il portale di Azure

Questo articolo descrive come abilitare il [servizio peering](overview-peering-service.md) di Azure in un peering diretto usando il portale di Azure.

Se si preferisce, è possibile completare questa guida usando [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Verificare i [prerequisiti](prerequisites.md) prima di iniziare la configurazione.
* Scegliere un peering diretto nella sottoscrizione per cui si vuole abilitare il servizio di peering. Se non si dispone di uno, convertire un peering diretto legacy o creare un nuovo peering diretto:
    * Per convertire un peering diretto legacy, seguire le istruzioni riportate in [convertire un peering diretto legacy in una risorsa di Azure usando il portale](howto-legacy-direct-portal.md).
    * Per creare un nuovo peering diretto, seguire le istruzioni riportate in [creare o modificare un peering diretto usando il portale](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Abilitare il servizio di peering in un peering diretto

### <a name="view-direct-peering"></a><a name= get></a>Visualizza peering diretto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Abilitare il peering diretto per il servizio di peering

Dopo aver aperto un peering diretto nel passaggio precedente, abilitarlo per il servizio di peering.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificare una connessione peering diretto

Per modificare le impostazioni di connessione, vedere la sezione "modificare un peering diretto" in [creare o modificare un peering diretto usando il portale](howto-direct-portal.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering di Exchange con il portale](howto-exchange-portal.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure con il portale](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per domande frequenti, vedere domande [frequenti sul servizio di peering](service-faqs.md).