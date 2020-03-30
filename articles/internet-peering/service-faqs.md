---
title: Servizio peering - Domande frequenti
titleSuffix: Azure
description: Servizio peering - Domande frequenti
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775472"
---
# <a name="peering-service---faqs"></a>Servizio peering - Domande frequenti

È possibile rivedere le informazioni riportate di seguito per domande generali.

**Un gestore può usare il peering diretto esistente con Microsoft per supportare il servizio peering?**

Sì, un vettore può sfruttare il suo PNI esistente per supportare peering Service. Un PNI del servizio di peering richiede diversità per supportare l'HA. Se l'attuale PNI ha già una diversità, non sono necessarie nuove infrastrutture. Se l'attuale PNI ha bisogno di diversità, allora può essere aumentata.

**Un operatore può usare il nuovo peering diretto con Microsoft per supportare il servizio peering?**

Sì, anche questo è possibile. Microsoft collaborerà con Carrier per creare un nuovo peering diretto per supportare il servizio di peering.  

**Perché Direct esegue il peering con un requisito per supportare il servizio di peering?**

Uno dei driver principali dietro Peering Service è quello di fornire la connettività ai servizi online Microsoft attraverso un SP ben collegato. I PNI sono sempre nell'intervallo Gbps e quindi un elemento fondamentale per una connettività ad alta velocità effettiva tra carrier e Microsoft.

**Quali sono i requisiti di diversità in un peering diretto per supportare il servizio di peering?**

Un PNI deve supportare la ridondanza locale e la ridondanza geografica. La ridondanza locale è definita come due set diversi di percorsi in un sito peering specifico. La ridondanza geografica richiede che Carrier disponga di connettività aggiuntiva in un sito perimetrale Microsoft diverso in caso di errore del sito primario. Per la breve durata di errore, il vettore può instradare il traffico attraverso il sito di backup.

**Il vettore offre già SLA e Enterprise grade Internet, come è diversa questa offerta?**

Alcuni vettori offrono Servizi di integrazione e Internet di livello enterprise da parte della rete. In Servizio peering, Microsoft offrirà il traffico di offerta SLA su parte Microsoft della rete. Selezionando Il cliente del servizio di peering otterrà un contratto di servizio end-to-end. Il servizio di sla dal proprio sito al bordo Microsoft sulla rete ISP può essere coperto dall'ISP. Il contratto di servizio in Microsoft Global Network dall'applicazione Microsoft edge to end users è ora coperto da Microsoft.

**Se un provider di servizi è già peering con Microsoft utilizzando PNI, che tipo di modifiche sono necessarie per supportare peering service?**

* Modifiche software per identificare un utente del servizio di peering e il relativo traffico. Può richiedere modifiche ai criteri di routing per scambiare il traffico di un utente al perimetro Microsoft più vicino tramite la connessione del servizio di peering.
* Assicurarsi che la connettività disponga di ridondanza locale e ridondanza geografica.
