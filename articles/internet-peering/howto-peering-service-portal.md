---
title: Abilitare il servizio di peering in un peering diretto con il portale
titleSuffix: Azure
description: Abilitare il servizio di peering in un peering diretto con il portale
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 87a65826a338f4098ae24c33939ea7f9d4475e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129910"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Abilitare il servizio di peering in un peering diretto con il portale

In questo articolo viene descritto come abilitare il [servizio peering](overview-peering-service.md) in un peering diretto tramite il portale.

Se si preferisce, è possibile completare questa guida usando [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare [i prerequisiti](prerequisites.md) prima di iniziare la configurazione.
* Scegliere un peering diretto nella sottoscrizione per cui abilitare il servizio di peering. Se non ne hai uno, converti un peering Diretto legacy o crea un nuovo peering diretto.
    * Per convertire un peering diretto legacy, seguire le istruzioni in [Convertire un peering diretto legacy in risorse](howto-legacy-direct-portal.md)di Azure usando il portale.
    * Per creare un nuovo peering diretto, seguire le istruzioni in [Creare o modificare un peering diretto tramite il portale](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Abilitare il servizio di peering in un peering diretto

### <a name="view-direct-peering"></a><a name= get></a>Visualizzare il peering direttoView Direct peering
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Abilitare il peering diretto per il servizio peeringEnable the Direct peering for Peering Service

Dopo aver aperto il peering diretto nel passaggio precedente, abilitarlo per il servizio di peering.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificare una connessione di peering direttoModify a Direct peering connection

Se è necessario modificare le impostazioni di connessione, vedere la sezione **Modificare un peering diretto** in Creare o modificare un [peering diretto tramite il portale](howto-direct-portal.md)

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare il peering di Exchange tramite il portale](howto-exchange-portal.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure con il portale](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per le domande frequenti, vedere Domande frequenti sul servizio di [peering](service-faqs.md).