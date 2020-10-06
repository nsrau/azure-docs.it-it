---
title: Creazione di soluzioni per la vendita al dettaglio con Azure IoT Central | Microsoft Docs
description: Informazioni su come creare logistica connessa, un centro di distribuzione digitale, l'analisi dei punti vendita, il monitoraggio delle condizioni, i pagamenti, la gestione intelligente degli inventari e soluzioni retail con i modelli di applicazione di Azure IoT Central.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 517344e0bae0a48dbee04da33d28c8777d7c142d
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91440257"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Creazione di soluzioni per la vendita al dettaglio con Azure IoT Central

Azure IoT Central è una piattaforma di app IoT che riduce il carico di lavoro e i costi associati allo sviluppo, alla gestione e alla manutenzione di soluzioni IoT di livello aziendale. La scelta di compilare con Azure IoT Central consente di concentrare tempo, denaro ed energie per trasformare il business con dati IoT, anziché semplicemente gestire e aggiornare un'infrastruttura IoT complessa e in continua evoluzione.

Questo articolo descrive diversi modelli di applicazione di IoT Central specifici degli ambienti retail. Gli sviluppatori di soluzioni possono usare questi modelli per creare soluzioni IoT destinate a ottimizzare le supply chain, migliorare le esperienze dei clienti nei punti vendita e monitorare con maggiore efficienza gli inventari.

:::image type="content" source="media/overview-iot-central-retail/retail-app-templates.png" alt-text="Panoramica di Azure IoT per la vendita al dettaglio":::

Le sezioni seguenti descrivono le funzionalità di questi modelli di applicazione:

## <a name="connected-logistics"></a>Logistica connessa

Secondo le previsioni, la spesa globale in logistica è destinata a raggiungere 10,6 trilioni di dollari nel 2020. La maggior parte di questa spesa è da attribuire al trasporto di merci, per cui gli spedizionieri sono sottoposti a un'intensa pressione competitiva, oltre che a vincoli rigorosi.

È possibile usare i sensori IoT per raccogliere e monitorare dati sulle condizioni ambientali, come temperatura, umidità, pendenze, urti, illuminazione e la posizione di una spedizione. I dati di telemetria raccolti da sensori e dispositivi IoT possono essere combinati con altre origini dati, ad esempio informazioni su meteo e traffico, nei sistemi di business intelligence basati sul cloud.

I vantaggi della soluzione di logistica connessa sono:

* Monitoraggio delle spedizioni con dati in tempo reale su tracciabilità e rintracciabilità. 
* Integrità delle spedizioni con monitoraggio in tempo reale delle condizioni ambientali.
* Protezione da furti, perdite o danni delle spedizioni.
* Geofencing, ottimizzazione dei percorsi, gestione della flotta e analisi dei veicoli.
* Previsione degli orari di partenza e arrivo delle spedizioni.

Gli screenshot seguenti illustrano il dashboard predefinito del modello di applicazione. Il dashboard è completamente personalizzabile in base a specifici requisiti delle soluzioni:

:::image type="content" source="media/overview-iot-central-retail/connected-logistics-dashboard1.png" alt-text="Panoramica di Azure IoT per la vendita al dettaglio":::

:::image type="content" source="media/overview-iot-central-retail/connected-logistics-dashboard2.png" alt-text="Panoramica di Azure IoT per la vendita al dettaglio":::

Per altre informazioni, vedere l'esercitazione [Distribuire ed esaminare un modello di applicazione di logistica connessa](./tutorial-iot-central-connected-logistics.md).

## <a name="digital-distribution-center"></a>Centro di distribuzione digitale

Man mano che produttori e rivenditori stabiliscono presenze in tutto il mondo, le loro supply chain si diramano e diventano sempre più complesse. I consumatori ormai si aspettano che siano disponibili ampie scelte di prodotti e che le merci arrivino entro uno o due giorni dall'acquisto. I centri di distribuzione devono adattarsi a queste tendenze e superare le inefficienze esistenti.

Attualmente, la dipendenza dal lavoro manuale implica che il ritiro e l'imballaggio rappresentino il 55-65% dei costi del centro di distribuzione. Le procedure manuali di ritiro e imballaggio sono anche solitamente più lente rispetto ai sistemi automatizzati e la rapida fluttuazione delle esigenze di personale rende ancora più difficile soddisfare i volumi di spedizione. Questa fluttuazione stagionale comporta un aumento del ricambio del personale e una maggiore probabilità che si verifichino errori costosi.

Le soluzioni basate sulle fotocamere con IoT abilitato possono offrire vantaggi di trasformazione, grazie a un ciclo di feedback digitale. I dati provenienti da tutto il centro di distribuzione generano informazioni dettagliate di utilità pratica e di qualità superiore.

I vantaggi di un centro di distribuzione digitale sono:

* Monitoraggio con fotocamere delle merci man mano che arrivano e passano attraverso il sistema trasportatore.
* Identificazione automatica di merci difettose.
* Tracciabilità efficiente degli ordini.
* Riduzione dei costi, aumento della produttività e ottimizzazione dell'utilizzo.

Gli screenshot seguenti illustrano il dashboard predefinito del modello di applicazione. Il dashboard è completamente personalizzabile in base a specifici requisiti delle soluzioni: 

:::image type="content" source="media/overview-iot-central-retail/digital-distribution-center-dashboard.png" alt-text="Panoramica di Azure IoT per la vendita al dettaglio":::

Per altre informazioni, vedere l'esercitazione [Distribuire ed esaminare un modello di applicazione del centro distribuzione digitale](./tutorial-iot-central-digital-distribution-center.md).

## <a name="in-store-analytics---condition-monitoring"></a>Analisi punti vendita - Monitoraggio delle condizioni

Per i rivenditori, le condizioni ambientali all'interno dei rispettivi punti vendita rappresentano un importante fattore di differenziazione rispetto alla concorrenza. È infatti fondamentale mantenere condizioni piacevoli nei negozi a vantaggio dei clienti.  

Gli sviluppatori di soluzioni possono usare il modello di applicazione Analisi punti vendita - Monitoraggio delle condizioni di IoT Central per creare una soluzione end-to-end. Il modello di applicazione consente di connettersi digitalmente e di monitorare un ambiente di punto vendita usando un'ampia varietà di dispositivi sensori. Questi dispositivi sensori generano dati di telemetria che è possibile convertire in informazioni aziendali per consentire al rivenditore di ridurre i costi operativi e offrire un'esperienza soddisfacente ai clienti.

Usare il modello di applicazione per:

* Connettere diversi tipi di sensori IoT a un'istanza dell'applicazione IoT Central.
* Monitorare e gestire l'integrità della rete di sensori e i dispositivi gateway nell'ambiente.
* Creare regole personalizzate in base alle condizioni ambientali all'interno di un negozio per attivare avvisi per i responsabili.
* Trasformare le condizioni ambientali all'interno del negozio in informazioni dettagliate che possono essere sfruttate per migliorare l'esperienza dei clienti.
* Esportare le informazioni aggregate nelle applicazioni aziendali nuove o esistenti per fornire dati utili e tempestivi al personale del negozio.

Il modello di applicazione include un set di modelli di dispositivi e usa un set di dispositivi simulati per popolare il dashboard. 

Gli screenshot seguenti illustrano il dashboard predefinito del modello di applicazione. Il dashboard è completamente personalizzabile in base a specifici requisiti delle soluzioni: 

:::image type="content" source="media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png" alt-text="Panoramica di Azure IoT per la vendita al dettaglio":::

Per altre informazioni, vedere l'esercitazione [Creare un'applicazione di analisi dei punti vendita in Azure IoT Central](./tutorial-in-store-analytics-create-app.md).

## <a name="in-store-analytics---checkout"></a>Analisi punti vendita - Pagamento

Per i rivenditori, l'esperienza di pagamento all'interno dei rispettivi negozi rappresenta un importante fattore di differenziazione rispetto alla concorrenza. È infatti fondamentale offrire un'esperienza fluida nei negozi per incoraggiare i clienti a tornare.  

Gli sviluppatori di soluzioni possono usare il modello di applicazione Analisi punti vendita - Pagamento per creare una soluzione che offra informazioni dettagliate al personale del negozio addetto alle casse. Ad esempio, i sensori possono fornire informazioni sulla lunghezza delle code e i tempi medi di attesa medi per ogni fila di casse.

Usare il modello di applicazione per:

* Connettere diversi tipi di sensori IoT a un'istanza dell'applicazione IoT Central.
* Monitorare e gestire l'integrità della rete di sensori e i dispositivi gateway nell'ambiente.
* Creare regole personalizzate in base alle condizioni di pagamento all'interno di un negozio per attivare per il personale.
* Trasformare le condizioni di pagamento all'interno del negozio in informazioni dettagliate che possono essere sfruttate per migliorare l'esperienza dei clienti.
* Esportare le informazioni aggregate nelle applicazioni aziendali nuove o esistenti per fornire dati utili e tempestivi al personale del negozio.

Il modello di applicazione include un set di modelli di dispositivi e usa un set di dispositivi simulati per popolare il dashboard con i dati del traffico alle casse. 

Gli screenshot seguenti illustrano il dashboard predefinito del modello di applicazione. Il dashboard è completamente personalizzabile in base a specifici requisiti delle soluzioni: 

:::image type="content" source="media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png" alt-text="Panoramica di Azure IoT per la vendita al dettaglio":::

Per altre informazioni, vedere l'esercitazione [Creare un'applicazione di analisi dei punti vendita in Azure IoT Central](./tutorial-in-store-analytics-create-app.md).

## <a name="smart-inventory-management"></a>Gestione inventario intelligente

L'inventario è l'insieme di scorte di merci in possesso di un rivenditore. La gestione dell'inventario è cruciale per garantire che il prodotto giusto si trovi nel posto giusto al momento giusto. I rivenditori devono bilanciare i costi di immagazzinaggio di un numero eccessivo di scorte rispetto ai costi derivanti dalla carenza di merci sufficienti per soddisfare la domanda.

I dati IoT generati da tag RFID (Radio Frequency Identification), beacon e fotocamere offrono l'opportunità di migliorare i processi di gestione dell'inventario. I dati di telemetria raccolti da sensori e dispositivi IoT possono essere combinati con altre origini dati, ad esempio informazioni su meteo e traffico, nei sistemi di business intelligence basati sul cloud.

I vantaggi della gestione intelligente dell'inventario sono:

* Riduzione del rischio di esaurimento delle scorte e garanzia del livello di servizio desiderato per i clienti. 
* Analisi approfondita e informazioni dettagliate sull'accuratezza dell'inventario quasi in tempo reale.
* Strumenti per determinare la quantità corretta di scorte da mantenere per soddisfare gli ordini dei clienti.

Questo modello di applicazione è incentrato sulla connettività dei dispositivi, oltre che sulla configurazione e la gestione di dispositivi di lettura RFID e Bluetooth a basso consumo energetico.

Gli screenshot seguenti illustrano il dashboard predefinito del modello di applicazione. Il dashboard è completamente personalizzabile in base a specifici requisiti delle soluzioni:

:::image type="content" source="media/overview-iot-central-retail/smart-inventory-management-dashboard.png" alt-text="Panoramica di Azure IoT per la vendita al dettaglio":::

Per altre informazioni, vedere [Distribuire ed esaminare un modello di applicazione Gestione inventario intelligente](./tutorial-iot-central-smart-inventory-management.md).

## <a name="micro-fulfillment-center"></a>Centro di micro-adempimenti

Nel panorama sempre più competitivo della vendita al dettaglio, i rivenditori sono sottoposti a continue pressioni per chiudere il divario tra domanda e offerta. Una nuova tendenza emersa per soddisfare le crescenti richieste dei consumatori consiste nell'ospitare l'inventario vicino ai clienti finali e ai negozi che visitano.

Il modello di applicazione Centro di micro-adempimenti di IoT Central consente agli sviluppatori di soluzioni di monitorare e gestire tutti gli aspetti dei loro centri di adempimenti completamente automatizzati. Il modello include un set di sensori simulati di monitoraggio delle condizioni e operatori robotici per accelerare il processo di sviluppo di soluzioni. Questi dispositivi sensori acquisiscono segnali significativi che possono essere convertiti in informazioni aziendali dettagliate per ridurre i costi operativi e migliorare le esperienze dei clienti.

Il modello di applicazione consente di: 

- Connettere facilmente tipi diversi di sensori IoT, ad esempio robot o sensori di monitoraggio delle condizioni, a un'istanza dell'applicazione IoT Central.
- Monitorare e gestire l'integrità della rete di sensori e i dispositivi gateway nell'ambiente.
- Creare regole personalizzate in base alle condizioni ambientali all'interno di un centro di adempimenti per attivare avvisi appropriati.
- Trasformare le condizioni ambientali all'interno del centro di adempimenti in informazioni dettagliate che possono essere sfruttate dal team del magazzino.
- Esportare le informazioni aggregate nelle applicazioni aziendali nuove o esistenti a vantaggio del personale addetto.

Gli screenshot seguenti illustrano il dashboard predefinito del modello di applicazione. Il dashboard è completamente personalizzabile in base a specifici requisiti delle soluzioni:

:::image type="content" source="media/overview-iot-central-retail/MFC-Dashboard.png" alt-text="Panoramica di Azure IoT per la vendita al dettaglio":::

Per altre informazioni, vedere l'esercitazione [Distribuire ed esaminare un modello di applicazione del centro di micro-adempimenti](./tutorial-micro-fulfillment-center.md).

## <a name="video-analytics---object-and-motion-detection"></a>Analisi video - rilevamento movimento e oggetti

Il modello di applicazione *Analisi video - rilevamento movimento e oggetti di IoT Central* consente di provare rapidamente a distribuire, gestire e monitorare una soluzione che usa videocamere perimetrali intelligenti per rilevare oggetti e movimento.

L'applicazione di analisi video usa un modulo [Analisi video live](#live-video-analytics) in esecuzione in IoT Edge. Il modulo Analisi video live offre una piattaforma che consente di creare applicazioni video intelligenti che si estendono dai dispositivi perimetrali al cloud. È possibile usare la piattaforma per migliorare le soluzioni IoT, ad esempio l'applicazione di analisi video, grazie al rilevamento di movimento e oggetti.

Il modello di applicazione include quattro dashboard dell'applicazione:

* **Getting Started** (Introduzione) offre collegamenti a risorse utili per imparare a usare il modello di applicazione.
* **Demo Dashboard** (Dashboard demo) fornisce un'illustrazione dei tipi di informazioni che è possibile visualizzare dalle fotocamere connesse.
* **(Sample) Real Camera Management** (Gestione fotocamere reali) usa fotocamere simulate per illustrare come è possibile gestire le fotocamere dall'applicazione.
* **(Sample) Real Camera Monitor** (Monitoraggio fotocamere reali) usa fotocamere simulate per illustrare come è possibile monitorare le fotocamere dall'applicazione.

:::image type="content" source="media/overview-iot-central-retail/live-video-analytics.png" alt-text="Panoramica di Azure IoT per la vendita al dettaglio":::

Per ottenere altre informazioni sull'architettura della soluzione, vedere [Architettura dell'applicazione Analisi video](architecture-video-analytics.md).

Per informazioni su come distribuire la soluzione, vedere l'esercitazione [Creare un'applicazione di analisi video in Azure IoT Central](tutorial-video-analytics-deploy.md).

### <a name="live-video-analytics"></a>Analisi di video live

[Analisi video live](https://github.com/Azure/live-video-analytics) offre una piattaforma per creare applicazioni video intelligenti che spaziano dai dispositivi perimetrali al cloud. La piattaforma offre la possibilità di acquisire, registrare e analizzare video live e di pubblicare i risultati, ovvero video o analisi video, nei servizi di Azure. I servizi di Azure possono essere in esecuzione sul cloud o nei dispositivi perimetrali. È possibile usare la piattaforma per migliorare le soluzioni IoT grazie all'analisi video.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a creare una soluzione per la vendita al dettaglio:

* Iniziare con l'esercitazione [Creare un'applicazione di analisi dei punti vendita in Azure IoT Central](./tutorial-in-store-analytics-create-app.md), che illustra come creare una soluzione con uno dei modelli di applicazione per l'analisi dei punti vendita.
* [Distribuire ed esaminare un modello di applicazione di logistica connessa](./tutorial-iot-central-connected-logistics.md).
* [Distribuire ed esaminare un modello di applicazione del centro distribuzione digitale](./tutorial-iot-central-digital-distribution-center.md).
* [Distribuire ed esaminare un modello di applicazione Gestione inventario intelligente](./tutorial-iot-central-smart-inventory-management.md).
* [Distribuire ed esaminare un modello di applicazione del centro di micro-adempimenti](./tutorial-micro-fulfillment-center.md).
* [Distribuire ed esaminare il modello di applicazione di analisi video](./tutorial-video-analytics-deploy.md).
* Per altre informazioni su IoT Central, vedere [Panoramica di IoT Central](../preview/overview-iot-central.md).