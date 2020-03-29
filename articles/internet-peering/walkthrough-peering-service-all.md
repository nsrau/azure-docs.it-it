---
title: Procedura dettagliata per il partner del servizio di peering
titleSuffix: Azure
description: Procedura dettagliata per il partner del servizio di peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d626d98613bd5d988b599d0980c885d7f73bb958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720249"
---
# <a name="peering-service-partner-walkthrough"></a>Procedura dettagliata per il partner del servizio di peering

In questa sezione vengono illustrati i passaggi che un provider deve seguire per abilitare un peering diretto per il peering.

## <a name="create-direct-peering-connection-for-peering-service"></a>Creare una connessione di peering diretto per il servizio peeringCreate Direct peering connection for Peering Service
I provider di servizi possono espandere la propria copertura geografica creando un nuovo peering diretto che supporta il servizio di peering. Per raggiungere questo obiettivo,
1. Diventa un partner del peering service se non è già
1. Seguire le istruzioni per [creare o modificare un peering diretto tramite il portale](howto-direct-portal.md). Assicurarsi che soddisfi i requisiti di disponibilità elevata.
1. Eseguire quindi la procedura per abilitare il [servizio peering in un peering diretto tramite il portale](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Usare la connessione peering diretto legacy per il servizio peeringUse legacy Direct peering connection for Peering Service
Se si dispone di peering diretto legacy che si desidera utilizzare per supportare il servizio di peering,
1. Diventa un partner del servizio di peering, se non lo è già.
1. Seguire le istruzioni per convertire un peering diretto legacy in una risorsa di [Azure tramite il portale.](howto-legacy-direct-portal.md) Se necessario, ordinare circuiti aggiuntivi per soddisfare i requisiti di disponibilità elevata.
1. Eseguire quindi la procedura per abilitare il [servizio peering in un peering diretto tramite il portale](howto-peering-service-portal.md).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [criteri di peering](https://peering.azurewebsites.net/peering).
* Per informazioni sui passaggi per configurare il peering diretto con Microsoft, segui la procedura dettagliata di [peering diretto.](walkthrough-direct-all.md)
* Per informazioni sui passaggi per configurare il peering di Exchange con Microsoft, seguire la procedura dettagliata di [peering di Exchange](walkthrough-exchange-all.md).