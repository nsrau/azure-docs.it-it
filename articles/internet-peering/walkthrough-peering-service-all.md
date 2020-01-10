---
title: Procedura dettagliata per il partner di servizi peering
titleSuffix: Azure
description: Procedura dettagliata per il partner di servizi peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 28aa9f921de64fcc838935cf65b64a63280ddf16
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774627"
---
# <a name="peering-service-partner-walkthrough"></a>Procedura dettagliata per il partner di servizi peering

Questa sezione illustra i passaggi che un provider deve seguire per abilitare un peering diretto per il servizio di peering.

## <a name="create-direct-peering-connection-for-peering-service"></a>Creare una connessione di peering diretto per il servizio di peering
I provider di servizi possono espandere la loro copertura geografica creando un nuovo peering diretto che supporta il servizio di peering. A tale scopo,
1. Diventa un partner del servizio di peering, se non è già presente
1. Seguire le istruzioni per [creare o modificare un peering diretto usando il portale](howto-direct-portal.md). Verificare che soddisfi i requisiti di disponibilità elevata.
1. Quindi, seguire la procedura per [abilitare il servizio di peering in un peering diretto usando il portale](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-conection-for-peering-service"></a>Usare la connessione con peering diretto legacy per il servizio di peering
Se si vuole usare il peering diretto legacy per supportare il servizio di peering,
1. Diventare un partner del servizio di peering, se non è già stato fatto.
1. Seguire le istruzioni per [convertire un peering diretto legacy in una risorsa di Azure usando il portale](howto-legacy-direct-portal.md). Se necessario, ordinare altri circuiti per soddisfare i requisiti di disponibilità elevata.
1. Quindi, seguire la procedura per [abilitare il servizio di peering in un peering diretto usando il portale](howto-peering-service-portal.md).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [criteri di peering](https://peering.azurewebsites.net/peering).
* Per informazioni sui passaggi per configurare il peering diretto con Microsoft, seguire la [procedura dettagliata relativa al peering diretto](walkthrough-direct-all.md).
* Per informazioni sui passaggi per configurare il peering di Exchange con Microsoft, seguire la [procedura dettagliata del peering di Exchange](walkthrough-exchange-all.md).