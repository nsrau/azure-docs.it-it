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
ms.openlocfilehash: 8f65eb008d5df6ea3f2f85778d538023e6ce98eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945326"
---
# <a name="azure-security-benchmark-introduction"></a>Introduzione al benchmark di sicurezza di AzureAzure security benchmark introduction

Si possono avere diversi anni o addirittura decenni di esperienza con l'elaborazione locale. Si sa come proteggere tali distribuzioni; ma il cloud è diverso. Come è possibile sapere se le distribuzioni cloud sono sicure? Quali sono le differenze tra le procedure di sicurezza per i sistemi locali e le distribuzioni cloud?

Esiste un'ampia raccolta di white paper, procedure consigliate, architetture di riferimento, linee guida Web, strumenti open source, soluzioni commerciali, feed di intelligence e altro ancora, che possono essere utilizzati per proteggere il cloud. Quale opzione è consigliabile utilizzare? Cosa puoi fare per ottenere un livello di sicurezza accettabile nel cloud? 

Uno dei modi migliori per proteggere le distribuzioni cloud è concentrarsi sulle raccomandazioni di benchmark di sicurezza cloud. Le raccomandazioni di benchmark, per la protezione di qualsiasi servizio, iniziano con una comprensione fondamentale del rischio di sicurezza informatica e di come gestirlo. È quindi possibile usare questa comprensione adottando consigli sulla sicurezza di benchmark dal provider di servizi cloud per selezionare impostazioni di configurazione della sicurezza specifiche nell'ambiente. 

The Azure Security Benchmark includes a collection of high-impact security recommendations you can use to help secure most of the services you use in Azure. È possibile considerare questi consigli come "generali" o "organizzativi" in quanto sono applicabili alla maggior parte dei servizi di Azure.You can think of these recommendations as "general" or "organizational" as they are applicable to most Azure services. I consigli di Azure Security Benchmark vengono quindi personalizzati per ogni servizio di Azure e queste indicazioni personalizzate sono contenute negli articoli sui consigli per il servizio. 

La documentazione di Azure Security Benchmark specifica i controlli di sicurezza e i consigli sui servizi.

- **Controlli di sicurezza:** le raccomandazioni di Azure Security Benchmark sono suddivise in categorie in base ai controlli di sicurezza. I controlli di sicurezza rappresentano requisiti di sicurezza specifici di alto livello indipendenti dai fornitori, ad esempio la sicurezza di rete e la protezione dei dati. Ogni controllo di sicurezza include una serie di consigli e istruzioni sulla sicurezza che consentono di implementare tali consigli. 
- **Consigli**sui servizi: se disponibili, i consigli di benchmark per i servizi di Azure includeranno consigli di Azure Security Benchmark personalizzati in modo specifico per tale servizio. 

I termini "Control", "Benchmark" e "Baseline" vengono usati spesso nella documentazione di Azure Security Benchmark ed è importante comprendere in che modo Azure usa tali termini. 

| Termine | Descrizione | Esempio |
|--|--|--|
| Controllo | Un **controllo** è una descrizione generale di una funzionalità o un'attività che deve essere affrontata e non è specifica di una tecnologia o di un'implementazione. | La protezione dei dati è uno dei controlli di sicurezza. Questo controllo contiene azioni specifiche che devono essere indirizzate per garantire la protezione dei dati. |
| Benchmark | Un benchmark contiene consigli sulla sicurezza per una tecnologia specifica, ad esempio Azure.A **benchmark** contains security recommendations for a specific technology, such as Azure. Le raccomandazioni sono suddivise in categorie in base al controllo a cui appartengono. | Il benchmark di sicurezza di Azure comprende le raccomandazioni sulla sicurezza specifiche per la piattaforma AzureThe Azure Security benchmark comprises the security recommendations specific to the Azure platform  |
| Di base | Una **linea di base** è i requisiti di sicurezza per un'organizzazione. I requisiti di sicurezza si basano su raccomandazioni di riferimento. Ogni organizzazione decide quali raccomandazioni di benchmark includere nella propria linea di base. | The Contoso company creates its security baseline by choosing to require specific recommendations in the Azure Security Benchmark. |

Accogliamo con favore il tuo feedback sul benchmark di sicurezza di Azure! Ti invitiamo a fornire commenti nell'area di feedback qui sotto. Se si preferisce condividere l'input in modo più privato con il team di Azure Security Benchmark, è possibile compilare il modulo all'indirizzohttps://aka.ms/AzSecBenchmark 
