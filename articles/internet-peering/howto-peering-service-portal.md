---
title: Abilitare il servizio peering di Azure in un peering diretto tramite il portale di AzureEnable Azure Peering Service on a Direct peering by using the Azure portal
titleSuffix: Azure
description: Abilitare il servizio peering di Azure in un peering diretto tramite il portale di AzureEnable Azure Peering Service on a Direct peering by using the Azure portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 625a17e5acff00f78c5a19725653eec629936f87
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687034"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Abilitare il servizio peering di Azure in un peering diretto tramite il portale di AzureEnable Azure Peering Service on a Direct peering by using the Azure portal

Questo articolo descrive come abilitare il servizio peering di Azure in un peering diretto usando il portale di Azure.This article describes how to enable Azure [Peering Service](overview-peering-service.md) on a Direct peering by using the Azure portal.

Se si preferisce, è possibile completare questa guida utilizzando [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare i [prerequisiti](prerequisites.md) prima di iniziare la configurazione.
* Scegliere un peering diretto nella sottoscrizione per cui si vuole abilitare il servizio di peering. Se non ne hai uno, converti un peering diretto legacy o crea un nuovo peering diretto:
    * Per convertire un peering diretto legacy, seguire le istruzioni in [Convertire un peering diretto legacy](howto-legacy-direct-portal.md)in una risorsa di Azure usando il portale .
    * Per creare un nuovo peering diretto, seguire le istruzioni in [Creare o modificare un peering diretto tramite il portale](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Abilitare il servizio di peering in un peering diretto

### <a name="view-direct-peering"></a><a name= get></a>Visualizzare il peering direttoView Direct peering
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Abilitare il peering diretto per il servizio peeringEnable the Direct peering for Peering Service

Dopo aver aperto un peering diretto nel passaggio precedente, abilitarlo per il servizio di peering.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificare una connessione di peering direttoModify a Direct peering connection

Per modificare le impostazioni di connessione, vedere la sezione "Modificare un peering diretto" in [Creare o modificare un peering diretto tramite il portale](howto-direct-portal.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare il peering di Exchange tramite il portale](howto-exchange-portal.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure tramite il portaleConvert a legacy Exchange peering to an Azure resource by using the portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per le domande frequenti, vedere le domande frequenti sul servizio di [peering](service-faqs.md).