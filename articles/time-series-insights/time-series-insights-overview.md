---
title: Panoramica di Azure Time Series Insights | Microsoft Docs
description: Introduzione ad Azure Time Series Insights, un nuovo servizio per le soluzioni IoT e di analisi dei dati delle serie temporali
keywords: 
services: tsi
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: 
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: omravi
ms.openlocfilehash: 1814459e47280af62450a4093140ab6ab9b765fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-time-series-insights"></a>Informazioni su Azure Time Series Insights

Azure Time Series Insights è un servizio cloud gestito con componenti di archiviazione, analisi e visualizzazione che rendono facile inserire, archiviare, esplorare e analizzare miliardi di eventi simultaneamente. Time Series Insights offre una visualizzazione globale dei dati e permette di convalidare rapidamente le soluzioni IoT ed evitare costosi tempi di inattività per i dispositivi, semplificando l'individuazione di tendenze nascoste e anomalie e l'esecuzione di analisi delle cause radice in tempo quasi reale. Time Series Insights inserisce i dati delle serie temporali da gestori eventi (ad esempio, hub IoT o Hub eventi), indicizza i dati e ritira i dati in base a criteri di conservazione configurabili. Gli utenti utilizzano i dati tramite un'esperienza utente intuitiva o le API REST di query.

![Panoramica di Time Series Insights](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>Scenari principali

* Monitorare e convalidare le soluzioni IoT in pochi minuti.
* Visualizzare e analizzare dati IoT su larga scala.
* Accelerare l'analisi della causa radice e del rilevamento anomalie.
* Creare una visualizzazione globale di più dispositivi, stabilimenti e dati.

## <a name="capabilities-and-benefits"></a>Funzionalità e vantaggi

* **Avvio facile**: Azure Time Series Insights non richiede di preparare i dati in anticipo ed è incredibilmente veloce. È possibile connettersi a miliardi di eventi nell'hub IoT di Azure o nell'hub eventi in pochi minuti. Dopo la connessione, è possibile visualizzare e interagire con i dati dei sensori in pochi secondi, per convalidare rapidamente le soluzioni IoT. Time Series Insights è semplice da usare. È possibile interagire con i dati senza scrivere neppure una riga di codice.  Non è necessario avere familiarità con un particolare linguaggio. Time Series Insights offre agli utenti avanzati una superficie di query granulare con testo libero e un'esplorazione più semplice per tutti.

* **Informazioni dettagliate in tempo quasi reale**: Time Series Insights può inserire centinaia di milioni di eventi di sensori al giorno, con una latenza di un minuto, per poter reagire rapidamente ai cambiamenti. Time Series Insights consente di ottenere informazioni dettagliate sui dati dei sensori permettendo di trovare rapidamente tendenze e anomalie e di eseguire analisi complesse della causa radice evitando i costi dovuti ai tempi di inattività. Abilitando la correlazione incrociata tra dati in tempo reale e cronologici, Time Series Insights consente di sbloccare le tendenze nascoste nei dati.

* **Compilare soluzioni personalizzate**: è possibile incorporare i dati di Azure Time Series Insights nelle applicazioni esistenti o creare nuove soluzioni personalizzate con le API REST di Time Series Insights. È possibile creare visualizzazioni personalizzate che è possibile condividere per consentire ad altri di usufruire di quanto si è appreso.

* **Scalabilità**: Time Series Insights è progettato per supportare IoT su larga scala. Nella versione di anteprima può inserire da 1 milione a 100 milioni di eventi al giorno, con un intervallo di conservazione di 31 giorni. È possibile visualizzare e analizzare i flussi dei dati attivi in tempo quasi reale, oltre a grandi quantità di dati cronologici. I tassi di inserimento e conservazione aumenteranno man mano proporzionalmente alle sempre maggiori esigenze aziendali.

## <a name="time-series-insights-glossary"></a>Glossario di Time Series Insights

* **Ambiente**: un ambiente è una risorsa di Azure con capacità di inserimento e archiviazione.  I clienti effettuano il provisioning degli ambienti tramite il portale di Azure con la capacità necessaria.
* **Origine evento**: un'origine evento deriva da un gestore eventi, ad esempio Hub eventi di Azure.  Time Series Insights si connette direttamente alle origini evento, inserendo il flusso dei dati senza scrivere codice. Time Series Insights supporta attualmente gli hub eventi di Azure e gli hub IoT di Azure.
* **Dati di riferimento**: Time Series Insights consente agli utenti di aggiungere i dati delle serie temporali ai dati di riferimento.  I dati di riferimento possono includere i metadati sui dispositivi o altri dati statici che vengono modificati raramente. Time Series Insights aggiunge i dati di riferimento ai flussi di dati, consentendo agli utenti di visualizzare e analizzare questi dati in tempo quasi reale.
