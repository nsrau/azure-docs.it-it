---
title: Componenti & prerequisiti
description: Informazioni dettagliate su tutti gli elementi necessari per iniziare a usare Azure Defender per i prerequisiti del servizio Internet.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.custom: references_regions
ms.openlocfilehash: 19263f8db58c8d20288d3ae74c24efd85667bc33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937835"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Prerequisiti di Azure Defender per l'it

Questo articolo fornisce una spiegazione dei diversi componenti di Defender per il servizio Internet delle cose, cosa è necessario iniziare e spiega i concetti di base per comprendere il servizio.

## <a name="minimum-requirements"></a>Requisiti minimi

- Livello standard dell'hub Internet
  - Privilegi a livello di **proprietario** del ruolo di Azure
- [Area di lavoro Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Centro sicurezza di Azure (scelta consigliata)
  - L'uso del Centro sicurezza di Azure è un'indicazione e non un requisito. Senza il Centro sicurezza di Azure, non sarà possibile visualizzare le altre risorse di Azure all'interno dell'hub Internet.

## <a name="working-with-defender-for-iot-service"></a>Uso di Defender per il servizio Internet

Il servizio Defender per informazioni dettagliate e la creazione di report sono disponibili tramite l'hub Azure e il Centro sicurezza di Azure. Per abilitare Defender per le cose nell'hub Azure, è necessario un account con privilegi a livello di **proprietario** . Dopo aver abilitato ASC per le cose nell'hub Internet delle cose, il Defender per l'uso di informazioni su Internet viene visualizzato come funzionalità di **sicurezza** nell'hub Azure  **e nel centro sicurezza di Azure** .

## <a name="supported-service-regions"></a>Aree del servizio supportate

Defender for Internet è attualmente supportato per gli hub Internet nelle aree di Azure seguenti:

- Stati Uniti centrali
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centro-occidentali
- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Stati Uniti centro-meridionali
- Stati Uniti centro-settentrionali
- Canada centrale
- Canada orientale
- Europa settentrionale
- Brasile meridionale
- Francia centrale
- Regno Unito occidentale
- Regno Unito meridionale
- Europa occidentale
- Europa settentrionale
- Giappone occidentale
- Giappone orientale
- Australia sud-orientale
- Australia orientale
- Asia orientale
- Asia sud-orientale
- Corea centrale
- Corea meridionale
- India centrale
- India meridionale

Defender for Internet viene indirizzato tutto il traffico da tutte le aree europee all'data center regionale dell'Europa occidentale e a tutte le aree rimanenti per la data center regionale degli Stati Uniti centrali.

## <a name="wheres-my-iot-hub"></a>Dov'è il mio hub Internet?

Controllare la posizione dell'hub Internet per verificare la disponibilità del servizio prima di iniziare.

1. Aprire l'hub IoT.
1. Fare clic su **Panoramica**.
1. Verificare che il percorso elencato corrisponda a una delle [aree di servizio supportate](#supported-service-regions).

## <a name="supported-platforms-for-agents"></a>Piattaforme supportate per gli agenti

Defender per gli agenti it supporta un elenco in continua crescita di dispositivi e piattaforme. Vedere l' [elenco piattaforma supportata](how-to-deploy-agent.md) per verificare la libreria di dispositivi esistente o pianificata.

## <a name="next-steps"></a>Passaggi successivi

- Leggi la [Panoramica](overview.md) sulla sicurezza di Azure.
- Informazioni su come [abilitare il servizio](quickstart-onboard-iot-hub.md)
- Leggi le [domande frequenti su Defender](resources-frequently-asked-questions.md)
- Scopri come [comprendere Defender per gli avvisi relativi](concept-security-alerts.md) alle cose
