---
title: Livelli Premium e Basic di Griglia di eventi di Azure
description: Questo articolo descrive la differenza tra i livelli Premium e Basic di Griglia di eventi di Azure e indica quando usare ognuno
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79300335"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Livelli Premium e Basic di Griglia di eventi di Azure
Griglia di eventi di Azure è disponibile in due livelli, **Premium** e **Basic**. Il livello Basic prevede il pagamento in base al consumo. Offre tutti gli strumenti di pubblicazione/sottoscrizione di base necessari per usare Griglia di eventi per i modelli di programmazione basati su eventi. Il livello Premium offre un ulteriore passo in avanti con funzionalità di sicurezza destinate all'azienda. Il livello Premium è disponibile nelle prime fasi di **anteprima**, con molte funzionalità ancora in fase di sviluppo.

## <a name="overview"></a>Panoramica
Tutti gli argomenti e i domini personalizzati di Griglia di eventi appartengono al livello Basic o a quello Premium. A partire dalla versione `2020-04-01-preview` dell'API, è possibile scegliere il livello desiderato durante la creazione di un argomento o di un dominio. Il valore predefinito è il livello Basic. Gli argomenti e i domini creati con le versioni meno recenti dell'API appartengono per impostazione predefinita al livello Basic. Per cambiare il piano tariffario per gli argomenti e i domini, vedere [Come aggiornare il livello per argomenti e domini](update-tier.md).

## <a name="capabilities-and-features"></a>Funzionalità e caratteristiche

Nella tabella seguente vengono descritte le differenze tra i livelli:

|       &nbsp;                                           | Basic           | Premium        |
| ------------------------------------------------------ | --------------- | -------------- |
| Regole del firewall IP in ingresso                          | Anteprima  | Anteprima |
| Tag di servizio in uscita                                | Anteprima  | Anteprima |
| Integrazione della rete virtuale con endpoint privati in ingresso          | Non disponibile   | Anteprima |

## <a name="availability"></a>Disponibilità
Durante l'anteprima iniziale, gli argomenti e i domini del livello Premium con integrazione di endpoint privati sono disponibili nelle aree seguenti:

- Stati Uniti orientali
- Stati Uniti occidentali 2
- Stati Uniti centro-meridionali

## <a name="pricing-and-quotas"></a>Prezzi e quote
Per informazioni dettagliate sui prezzi e sull'uso del livello Basic, vedere [Prezzi di Griglia di eventi](https://azure.microsoft.com/pricing/details/event-grid/). I prezzi del livello Premium non sono stati ancora annunciati e finché non verranno resi disponibili il servizio sarà gratuito.

Finché non verranno annunciati i prezzi del livello Premium, le quote esistenti per il numero di argomenti e domini e per la velocità effettiva si applicano sia alle risorse del livello Premium che a quelle del livello Basic.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- Per eseguire l'aggiornamento dal livello Basic al livello Premium, vedere l'articolo [Aggiornamento del piano tariffario](update-tier.md). 
- È possibile configurare il firewall IP per la risorsa Griglia di eventi per limitare l'accesso tramite Internet pubblico consentendolo solo a un set di indirizzi IP o intervalli di indirizzi IP selezionati. Per le istruzioni dettagliate, vedere [Configurare il firewall](configure-firewall.md).
- È possibile configurare endpoint privati per limitare l'accesso consentendolo solo a reti virtuali selezionate. Per le istruzioni dettagliate, vedere [Configurare gli endpoint privati](configure-private-endpoints.md).