---
title: Introduzione al benchmark di sicurezza di Azure
description: Introduzione al benchmark di sicurezza
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 2d0cceca841a2aff2538dcf8763d245ca8cfd13d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564361"
---
# <a name="azure-security-benchmarks-introduction"></a>Introduzione ai benchmark di sicurezza di Azure

L'elaborazione locale può avere diversi anni o addirittura decenni di esperienza. Si sa come proteggere tali distribuzioni. Ma il cloud è diverso. Come è possibile stabilire se le distribuzioni cloud sono sicure? Quali sono le differenze tra le procedure di sicurezza per i sistemi locali e le procedure di sicurezza per le distribuzioni cloud?

È disponibile una vasta gamma di white paper, procedure consigliate, architetture di riferimento, linee guida Web, strumenti open source, soluzioni commerciali, feed di intelligence e altro ancora che possono essere usati per proteggere il cloud. Quale opzione è consigliabile utilizzare? Che cosa è possibile fare per ottenere un livello di sicurezza accettabile nel cloud? 

Uno dei modi migliori per proteggere le distribuzioni cloud consiste nell'concentrarsi sulle raccomandazioni di benchmark per la sicurezza del cloud. Le raccomandazioni di benchmark per la protezione di qualsiasi servizio iniziano con una conoscenza di base dei rischi Cybersecurity e della relativa modalità di gestione. È quindi possibile usare questa comprensione adottando raccomandazioni di sicurezza benchmark dal provider di servizi cloud per selezionare impostazioni di configurazione della sicurezza specifiche nell'ambiente. 

Il benchmark di sicurezza di Azure include una raccolta di consigli sulla sicurezza a elevato utilizzo che è possibile usare per proteggere la maggior parte dei servizi usati in Azure. È possibile considerare questi consigli come "generali" o "organizzativi" perché sono applicabili alla maggior parte dei servizi di Azure. Le raccomandazioni relative ai benchmark di sicurezza di Azure vengono quindi personalizzate per ogni servizio di Azure e queste istruzioni personalizzate sono contenute negli articoli relativi alle raccomandazioni sui servizi. 

La documentazione di benchmark di sicurezza di Azure specifica i controlli di sicurezza e le raccomandazioni dei servizi.

- **Controlli di sicurezza**: le raccomandazioni del benchmark di sicurezza di Azure sono classificate in base ai controlli di sicurezza. I controlli di sicurezza rappresentano requisiti di sicurezza indipendenti dal fornitore, ad esempio la sicurezza della rete e la protezione dei dati. Ogni controllo di sicurezza include un set di raccomandazioni e istruzioni per la sicurezza che consentono di abilitare tali raccomandazioni. 
- **Raccomandazioni del servizio**: se disponibili, le raccomandazioni di benchmark per i servizi di Azure includeranno raccomandazioni di benchmark di sicurezza di Azure personalizzate per il servizio, oltre ad altre raccomandazioni univoche per il servizio specifico. 

I termini "Control", "benchmark" e "baseline" vengono usati spesso nella documentazione di benchmark di sicurezza di Azure ed è importante comprendere come Azure usa tali termini. 

| Durata | Description | Esempio |
|--|--|--|
| Controllo | Un **controllo** è una descrizione di alto livello di una funzionalità o di un'attività che deve essere risolta e non è specifico di una tecnologia o di un'implementazione. | La protezione dei dati è uno dei controlli di sicurezza. Questo controllo contiene azioni specifiche che devono essere risolte per garantire la protezione dei dati. |
| Benchmark | Un **benchmark** contiene raccomandazioni sulla sicurezza per una tecnologia specifica, ad esempio Azure. Le raccomandazioni vengono classificate in base al controllo a cui appartengono. | Il benchmark di sicurezza di Azure include i consigli sulla sicurezza specifici della piattaforma Azure  |
| Baseline | Una **linea di base** è i requisiti di sicurezza per un'organizzazione. I requisiti di sicurezza sono basati sulle raccomandazioni di benchmark. Ogni organizzazione decide quali raccomandazioni di benchmark includere nella baseline. | La società Contoso crea la propria linea di base di sicurezza scegliendo di richiedere raccomandazioni specifiche nel benchmark di sicurezza di Azure. |

I commenti e suggerimenti sul benchmark di sicurezza di Azure sono benvenuti. Si consiglia di fornire commenti nell'area commenti e suggerimenti riportata di seguito. Se si preferisce condividere l'input in modo più privato con il team di benchmark di sicurezza di Azure, è possibile compilare il modulo in https://aka.ms/AzSecBenchmark 