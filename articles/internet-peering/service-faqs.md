---
title: Servizio di peering-domande frequenti
titleSuffix: Azure
description: Servizio di peering-domande frequenti
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75775472"
---
# <a name="peering-service---faqs"></a>Servizio di peering-domande frequenti

Per domande generali, è possibile consultare le informazioni riportate di seguito.

**Un vettore può usare il peering diretto esistente con Microsoft per supportare il servizio di peering?**

Sì, un vettore può sfruttare i PNI esistenti per supportare il servizio di peering. Un servizio di peering PNI richiede la diversità per supportare la disponibilità elevata. Se il PNI esistente ha già una diversità, non è necessaria alcuna nuova infrastruttura. Se la PNI esistente richiede la diversità, può essere aumentata.

**Un vettore può usare il nuovo peering diretto con Microsoft per supportare il servizio di peering?**

Sì, è anche possibile. Microsoft collaborerà con il gestore per creare un nuovo peering diretto per supportare il servizio di peering.  

**Perché il peering diretto è un requisito per supportare il servizio di peering?**

Uno dei principali driver dietro il servizio di peering consiste nel fornire connettività a Microsoft Servizi online tramite una SP ben connessa. PNI sono sempre in un intervallo di Gbps e, di conseguenza, un blocco predefinito fondamentale per la connettività con velocità effettiva elevata tra Carrier e Microsoft.

**Quali sono i requisiti di diversità per un peering diretto per supportare il servizio di peering?**

Un PNI deve supportare la ridondanza locale e la ridondanza geografica. La ridondanza locale è definita come due diversi set di percorsi in un sito di peering specifico. La ridondanza geografica richiede che il vettore disponga di connettività aggiuntiva in un sito Microsoft Edge diverso in caso di errore del sito primario. Per la durata del breve errore, il gestore può instradare il traffico attraverso il sito di backup.

**Il vettore offre già Internet di livello aziendale e SLA, in che modo questa offerta è diversa?**

Alcuni vettori offrono un contratto di classe e un livello Enterprise Internet nella propria parte della rete. Nel servizio di peering, Microsoft offrirà il traffico offerto dal contratto di servizio in Microsoft parte della rete. Selezionando il servizio di peering il cliente otterrà un contratto di servizio end-to-end. Il contratto di servizi dal proprio sito a Microsoft Edge sulla rete ISP può essere coperto dal provider di servizi Internet. Il contratto di contratto in Microsoft Global Network da Microsoft Edge all'applicazione degli utenti finali è ora coperto da Microsoft.

**Se un provider di servizi già esegue il peering con Microsoft usando PNI, quali tipi di modifiche sono necessarie per supportare il servizio di peering?**

* Modifiche software per identificare un utente del servizio di peering e il relativo traffico. Potrebbe richiedere modifiche ai criteri di routing per scambiare il traffico di un utente presso il Microsoft Edge più vicino attraverso la connessione al servizio di peering.
* Verificare che la connettività includa ridondanza locale e ridondanza geografica.
