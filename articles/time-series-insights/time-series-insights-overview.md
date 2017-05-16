---
title: Panoramica di Azure Time Series Insights | Microsoft Docs
description: Introduzione ad Azure Time Series Insight, un nuovo servizio per le soluzioni IoT e di analisi dei dati delle serie temporali
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/20/2017
ms.author: omravi
ms.translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: cc35c00bec18ee1d9ed52b29b68ddacc1136dc70
ms.contentlocale: it-it
ms.lasthandoff: 04/26/2017

---

# <a name="overview-of-azure-time-series-insights"></a>Panoramica di Azure Time Series Insights

Azure Time Series Insights è un servizio cloud completamente gestito con componenti di archiviazione, analisi e visualizzazione che rendono incredibilmente facile inserire, archiviare, esplorare e analizzare miliardi di eventi simultaneamente. Time Series Insights offre una visualizzazione globale dei dati e permette di convalidare rapidamente le soluzioni IoT ed evitare costosi tempi di inattività per i dispositivi cruciali, semplificando l'individuazione di tendenze nascoste e anomalie e l'esecuzione di analisi delle cause radice in tempo quasi reale. Time Series Insights inserisce i dati delle serie temporali da gestori eventi (ad esempio, hub IoT o Hub eventi), indicizza i dati e ritira i dati in base a criteri di conservazione configurabili. Gli utenti utilizzano i dati per il proprio scenario aziendale tramite un'esperienza utente intuitiva o le API REST di query.

![Panoramica di Time Series Insights](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>Scenari principali

* Convalida e monitoraggio di soluzioni IoT in pochi minuti
* Visualizzazione intuitiva e analisi dei dati IoT su larga scala
* Accelerazione dell'analisi della causa radice e del rilevamento delle anomalie
* Creazione di una visualizzazione globale di più dispositivi, stabilimenti e dati

## <a name="key-capabilities-and-benefits"></a>Funzionalità e vantaggi principali

* **Avvio facile**: Azure Time Series Insights non richiede di preparare i dati in anticipo ed è incredibilmente veloce, quindi è possibile connettersi a miliardi di eventi nell'hub IoT di Azure o nell'hub eventi in pochi minuti. Dopo la connessione, è possibile visualizzare e interagire con i dati dei sensori in pochi secondi, per convalidare rapidamente la soluzione IoT. Time Series Insights è davvero intuitivo e semplice da usare. È possibile interagire con i dati senza dover scrivere neppure una riga di codice.  Inoltre non è necessario avere familiarità con un particolare linguaggio, perché Time Series Insights offre agli utenti avanzati una superficie di query granulare con testo libero, oltre a un'esplorazione più semplice per i principianti.

* **Informazioni dettagliate in tempo quasi reale in pochi secondi**: i componenti di archiviazione, analisi e visualizzazione di Time Series Insights, tutti in un'unica posizione, consentono di usare al meglio i dati delle serie temporali. Time Series Insights può inserire centinaia di milioni di eventi di sensori al giorno, con una latenza di un minuto, per poter reagire rapidamente ai cambiamenti. Time Series Insights offre informazioni dettagliate sui dati dei sensori consentendo di trovare rapidamente tendenze e anomalie e di eseguire facilmente analisi complesse della causa radice evitando i costi dovuti ai tempi di inattività. Abilitando la correlazione incrociata tra dati in tempo reale e cronologici, Time Series Insights consente agli utenti di sbloccare le tendenze nascoste nei dati.

* **Compilare soluzioni personalizzate**: è possibile incorporare i dati di Azure Time Series Insights nelle applicazioni esistenti o creare nuove soluzioni personalizzate con le API REST di Time Series Insights.  È anche possibile creare e condividere in modo molto semplice visualizzazioni personalizzate, per permettere agli altri utenti di usufruire di quanto si è appreso in prima persona.

* **Scalabilità**: Time Series Insights è progettato per supportare la scalabilità IoT. Nella versione di anteprima può inserire da 1 milione a 100 milioni di eventi al giorno, con un intervallo di conservazione di 31 giorni. Time Series Insights consente agli utenti di visualizzare e analizzare i flussi di dati attivi in tempo quasi reale, oltre a grandi quantità di dati cronologici. I tassi di inserimento e conservazione aumenteranno man mano proporzionalmente alle sempre maggiori esigenze aziendali.

## <a name="taxonomy-of-time-series-insights"></a>Tassonomia di Time Series Insights

* **Ambiente**: un ambiente è una risorsa di Azure con capacità di inserimento e archiviazione.  I clienti effettuano il provisioning degli ambienti tramite il portale di Azure con la capacità necessaria.
* **Origine evento**: un'origine evento deriva da un gestore eventi, ad esempio Hub eventi di Azure.  Time Series Insights si connette direttamente alle origini evento, inserendo il flusso di dati senza che gli utenti debbano scrivere neppure una riga di codice. Time Series Insights supporta attualmente gli hub eventi di Azure e gli hub IoT di Azure, ma in futuro verranno aggiunte altre origini evento.
* **Dati di riferimento**: Time Series Insights consente agli utenti di aggiungere i dati delle serie temporali ai dati di riferimento.  I dati di riferimento possono includere i metadati sui dispositivi o altri dati statici che vengono modificati raramente. Time Series Insights aggiunge i dati di riferimento ai flussi di dati, consentendo agli utenti di visualizzare e analizzare questi dati in tempo quasi reale.

