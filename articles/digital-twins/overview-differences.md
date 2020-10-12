---
title: Differenze rispetto alla versione precedente
titleSuffix: Azure Digital Twins
description: Informazioni sulle modifiche apportate alla nuova versione di Gemelli digitali di Azure
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 6d1786766fc61ed0edfdc53295a50ba212818fc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89181426"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-previous-version-2018"></a>Che cosa è la nuova versione di Gemelli digitali di Azure? In cosa si differenzia dalla versione precedente (2018)?

La prima versione di anteprima pubblica di Gemelli digitali di Azure è stata rilasciata a ottobre 2018. Sebbene i concetti di base della versione precedente siano stati trasferiti al nuovo servizio ora in anteprima pubblica, molte interfacce e dettagli di implementazione sono stati modificati per rendere il servizio più flessibile e accessibile. Queste modifiche sono state motivate dal feedback dei clienti.

> [!IMPORTANT]
> Alla luce dell'ampliamento delle funzionalità del nuovo servizio, la versione precedente del servizio Gemelli digitali di Azure verrà ritirata entro la fine dell'anno 2020.

Se è stata usata la prima versione di Gemelli digitali di Azure durante l'anteprima pubblica precedente, usare le informazioni e le procedure consigliate in questo articolo per informazioni su come usare il nuovo servizio e sfruttarne le funzionalità.

## <a name="differences-by-topic"></a>Differenze per argomento

La tabella seguente fornisce una visualizzazione affiancata dei concetti che sono cambiati tra la versione precedente del servizio e il nuovo servizio (attuale).

| Argomento | Versione precedente | Nuova versione |
| --- | --- | --- | --- |
| **Modellazione**<br>*Maggiore flessibilità* | La versione precedente era progettata per gli spazi intelligenti, quindi era dotata di un vocabolario integrato per gli edifici. | La nuova versione di Gemelli digitali di Azure è indipendente dal settore. È possibile definire un vocabolario personalizzato e modelli personalizzati per la soluzione, per rappresentare più tipi di ambienti in modo più flessibile.<br><br>Per altre informazioni, vedere [*Concetti: Modelli personalizzati*](concepts-models.md). |
| **Topologia**<br>*Maggiore flessibilità*| La versione precedente supportava una struttura di dati ad albero su misura per gli spazi intelligenti. I gemelli digitali erano collegati con relazioni gerarchiche. | Con la nuova versione i gemelli digitali possono essere collegati in topologie del grafo arbitrarie, organizzate come si preferisce. Ciò offre una maggiore flessibilità per esprimere le complesse relazioni del mondo reale.<br><br>Per altre informazioni, vedere [*Concetti: Gemelli digitali e grafo dei gemelli*](concepts-twins-graph.md). |
| **Calcolo**<br>*Più avanzato, maggiore flessibilità* | Nella versione precedente la logica per l'elaborazione degli eventi e la telemetria era definita in funzioni definite dall'utente JavaScript. Il debug con le funzioni definite dall'utente era limitato. | La nuova versione ha un modello di calcolo aperto: è possibile fornire la logica personalizzata collegando risorse di calcolo esterne come [Funzioni di Azure](../azure-functions/functions-overview.md). In questo modo è possibile usare il linguaggio di programmazione desiderato, accedere a librerie di codice personalizzate senza restrizioni e sfruttare le risorse di sviluppo e di debug che potrebbero essere presenti nel servizio esterno.<br><br>Per altre informazioni, vedere [*Procedura: Configurare una funzione di Azure per l'elaborazione dei dati*](how-to-create-azure-function.md). |
| **Gestione dei dispositivi con l'hub IoT**<br>*Maggiore accessibilità* | Nella versione precedente i dispositivi venivano gestiti con un'istanza dell'[hub IoT](../iot-hub/about-iot-hub.md) che era interna al servizio Gemelli digitali di Azure. Questo hub integrato non era completamente accessibile agli sviluppatori. | Nella nuova versione è possibile usare un hub IoT personale collegando un'istanza dell'hub IoT creata in modo indipendente, insieme a tutti i dispositivi già gestiti. In questo modo si ha accesso completo alle funzionalità dell'hub IoT e il controllo della gestione dei dispositivi.<br><br>Per altre informazioni, vedere [*Procedura: Inserire dati di telemetria dall'hub IoT*](how-to-ingest-iot-hub-data.md). |
| **Sicurezza**<br>*Più standard* | La versione precedente aveva ruoli predefiniti che era possibile usare per gestire l'accesso all'istanza. | La nuova versione si integra con lo stesso servizio back-end di [controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md) usato da altri servizi di Azure. Questo può rendere più semplice l'autenticazione tra gli altri servizi di Azure nella soluzione come l'hub IoT, Funzioni di Azure, Griglia di eventi e altro ancora.<br>Con il controllo degli accessi in base al ruolo, è ancora possibile usare ruoli predefiniti oppure è possibile creare e configurare ruoli personalizzati.<br><br>Per altre informazioni, vedere [*Concetti: Sicurezza per le soluzioni di Gemelli digitali di Azure*](concepts-security.md). |
| **Scalabilità**<br>*Maggiore* | La versione precedente presentava limitazioni di scalabilità per dispositivi, messaggi, grafi e unità di scala. Per ogni sottoscrizione era supportata una sola istanza di Gemelli digitali di Azure.  | La nuova versione si basa su una nuova architettura con una migliore scalabilità e una maggiore potenza di calcolo. Supporta anche 10 istanze per area, per sottoscrizione.<br><br>Vedere [*Riferimento: Limiti del servizio durante l'anteprima pubblica*](reference-service-limits.md) per informazioni dettagliate sui limiti nell'anteprima pubblica. |

## <a name="service-limits-in-public-preview"></a>Limiti del servizio durante l'anteprima pubblica

Per un elenco dei limiti di Gemelli digitali di Azure durante l'anteprima pubblica, vedere [*Riferimento: Limiti del servizio durante l'anteprima pubblica*](reference-service-limits.md).

## <a name="next-steps"></a>Passaggi successivi

Successivamente, approfondire l'uso di Gemelli digitali di Azure con la prima esercitazione:

[*Esercitazione: Scrivere il codice di un'app client*](tutorial-code.md)