---
title: Abilitare il servizio di peering in un peering diretto tramite il portale
titleSuffix: Azure
description: Abilitare il servizio di peering in un peering diretto tramite il portale
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d6b67c42ef8a5ba5ae98894775d1f56cee39ba8b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774978"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Abilitare il servizio di peering in un peering diretto tramite il portale

Questo articolo descrive come abilitare il [servizio di peering](overview-peering-service.md) in un peering diretto usando il portale.

Se si preferisce, è possibile completare questa guida usando [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Verificare i [prerequisiti](prerequisites.md) prima di iniziare la configurazione.
* Scegliere un peering diretto nella sottoscrizione in cui si vuole abilitare il servizio peering. Se non si dispone di uno, convertire un peering diretto legacy o creare un nuovo peering diretto.
    * Per convertire un peering diretto legacy, seguire le istruzioni riportate in [convertire un peering diretto legacy in una risorsa di Azure usando il portale](howto-legacy-direct-portal.md).
    * Per creare un nuovo peering diretto, seguire le istruzioni riportate in [creare o modificare un peering diretto usando il portale](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Abilitare il servizio di peering in un peering diretto

### <a name= get></a>Visualizza peering diretto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name= get></a>Abilitare il peering diretto per il servizio di peering

Dopo aver aperto il peering diretto nel passaggio precedente, abilitarlo per il servizio di peering.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificare una connessione peering diretto

Se è necessario modificare le impostazioni di connessione, vedere la sezione **modificare un peering diretto** in [creare o modificare un peering diretto tramite il portale](howto-direct-portal.md)

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare il peering di Exchange tramite il portale](howto-exchange-portal.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure tramite il portale](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per domande frequenti, vedere domande [frequenti sul servizio peering](service-faqs.md).