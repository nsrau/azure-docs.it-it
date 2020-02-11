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
ms.openlocfilehash: 171f06ad238f862adbd7071ee10d81133d7a6855
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022138"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Creazione di soluzioni per la vendita al dettaglio con Azure IoT Central



Azure IoT Central è una piattaforma di app IoT che riduce il carico di lavoro e i costi associati allo sviluppo, alla gestione e alla manutenzione di soluzioni IoT di livello aziendale. La scelta di compilare con Azure IoT Central consente di concentrare tempo, denaro ed energie per trasformare il business con dati IoT, anziché semplicemente gestire e aggiornare un'infrastruttura IoT complessa e in continua evoluzione.

Questo articolo descrive diversi modelli di applicazione di IoT Central specifici degli ambienti retail. Gli sviluppatori di soluzioni possono usare questi modelli per creare soluzioni IoT destinate a ottimizzare le supply chain, migliorare le esperienze dei clienti nei punti vendita e monitorare con maggiore efficienza gli inventari.

> [!div class="mx-imgBorder"]
> ![Panoramica di Azure IoT per la vendita al dettaglio](./media/overview-iot-central-retail/retail-app-templates.png)

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

> [!div class="mx-imgBorder"]
> ![Dashboard logistico connesso](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![Dashboard logistico connesso](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

Per altre informazioni, vedere l'esercitazione [Distribuire ed esaminare un modello di applicazione di logistica connessa](./tutorial-iot-central-connected-logistics-pnp.md).

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

> [!div class="mx-imgBorder"]
> ![Dashboard del centro di distribuzione digitale](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

Per altre informazioni, vedere l'esercitazione [Distribuire ed esaminare un modello di applicazione del centro distribuzione digitale](./tutorial-iot-central-digital-distribution-center-pnp.md).

## <a name="in-store-analytics---condition-monitoring"></a>Analisi punti vendita - Monitoraggio delle condizioni

Per i rivenditori, le condizioni ambientali all'interno dei rispettivi punti vendita rappresentano un importante fattore di differenziazione rispetto alla concorrenza. È infatti fondamentale mantenere condizioni piacevoli nei negozi a vantaggio dei clienti.  

Gli sviluppatori di soluzioni possono usare il modello di applicazione Analisi punti vendita - Monitoraggio delle condizioni di IoT Central per creare una soluzione end-to-end. Il modello di applicazione consente di connettersi digitalmente e di monitorare un ambiente di punto vendita usando un'ampia varietà di dispositivi sensori. Questi dispositivi sensori generano dati di telemetria che è possibile convertire in informazioni aziendali per consentire al rivenditore di ridurre i costi operativi e offrire un'esperienza soddisfacente ai clienti.

Usare il modello di applicazione per:

* Connettere un'ampia varietà di sensori IoT a un'istanza dell'applicazione IoT Central.
* Monitorare e gestire l'integrità della rete di sensori e i dispositivi gateway nell'ambiente.
* Creare regole personalizzate in base alle condizioni ambientali all'interno di un negozio per attivare avvisi per i responsabili.
* Trasformare le condizioni ambientali all'interno del negozio in informazioni dettagliate che possono essere sfruttate per migliorare l'esperienza dei clienti.
* Esportare le informazioni aggregate nelle applicazioni aziendali nuove o esistenti per fornire dati utili e tempestivi al personale del negozio.

Il modello di applicazione include un set di modelli di dispositivi e usa un set di dispositivi simulati per popolare il dashboard. 

Gli screenshot seguenti illustrano il dashboard predefinito del modello di applicazione. Il dashboard è completamente personalizzabile in base a specifici requisiti delle soluzioni: 

> [!div class="mx-imgBorder"]
> ![Analisi punti vendita - Monitoraggio delle condizioni](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

Per altre informazioni, vedere l'esercitazione [Creare un'applicazione di analisi dei punti vendita in Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md).

## <a name="in-store-analytics---checkout"></a>Analisi punti vendita - Pagamento

Per i rivenditori, l'esperienza di pagamento all'interno dei rispettivi negozi rappresenta un importante fattore di differenziazione rispetto alla concorrenza. È infatti fondamentale offrire un'esperienza fluida nei negozi per incoraggiare i clienti a tornare.  

Gli sviluppatori di soluzioni possono usare il modello di applicazione Analisi punti vendita - Pagamento per creare una soluzione che offra informazioni dettagliate al personale del negozio addetto alle casse. Ad esempio, i sensori possono fornire informazioni sulla lunghezza delle code e i tempi medi di attesa medi per ogni fila di casse.

Usare il modello di applicazione per:

* Connettere un'ampia varietà di sensori IoT a un'istanza dell'applicazione IoT Central.
* Monitorare e gestire l'integrità della rete di sensori e i dispositivi gateway nell'ambiente.
* Creare regole personalizzate in base alle condizioni di pagamento all'interno di un negozio per attivare per il personale.
* Trasformare le condizioni di pagamento all'interno del negozio in informazioni dettagliate che possono essere sfruttate per migliorare l'esperienza dei clienti.
* Esportare le informazioni aggregate nelle applicazioni aziendali nuove o esistenti per fornire dati utili e tempestivi al personale del negozio.

Il modello di applicazione include un set di modelli di dispositivi e usa un set di dispositivi simulati per popolare il dashboard con i dati del traffico alle casse. 

Gli screenshot seguenti illustrano il dashboard predefinito del modello di applicazione. Il dashboard è completamente personalizzabile in base a specifici requisiti delle soluzioni: 

> [!div class="mx-imgBorder"]
> ![Analisi punti vendita - Pagamento](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

Per altre informazioni, vedere l'esercitazione [Creare un'applicazione di analisi dei punti vendita in Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md).

## <a name="smart-inventory-management"></a>Gestione inventario intelligente

L'inventario è l'insieme di scorte di merci in possesso di un rivenditore. La gestione dell'inventario è cruciale per garantire che il prodotto giusto si trovi nel posto giusto al momento giusto. I rivenditori devono bilanciare i costi di immagazzinaggio di un numero eccessivo di scorte rispetto ai costi derivanti dalla carenza di merci sufficienti per soddisfare la domanda.

I dati IoT generati da tag RFID (Radio Frequency Identification), beacon e fotocamere offrono l'opportunità di migliorare i processi di gestione dell'inventario. I dati di telemetria raccolti da sensori e dispositivi IoT possono essere combinati con altre origini dati, ad esempio informazioni su meteo e traffico, nei sistemi di business intelligence basati sul cloud.

I vantaggi della gestione intelligente dell'inventario sono:

* Riduzione del rischio di esaurimento delle scorte e garanzia del livello di servizio desiderato per i clienti. 
* Analisi approfondita e informazioni dettagliate sull'accuratezza dell'inventario quasi in tempo reale.
* Strumenti per determinare la quantità corretta di scorte da mantenere per soddisfare gli ordini dei clienti.

Questo modello di applicazione è incentrato sulla connettività dei dispositivi, oltre che sulla configurazione e la gestione di dispositivi di lettura RFID e Bluetooth a basso consumo energetico.

Gli screenshot seguenti illustrano il dashboard predefinito del modello di applicazione. Il dashboard è completamente personalizzabile in base a specifici requisiti delle soluzioni:

> [!div class="mx-imgBorder"]
> ![Dashboard di gestione dell'inventario intelligente](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Per altre informazioni, vedere [Distribuire ed esaminare un modello di applicazione Gestione inventario intelligente](./tutorial-iot-central-smart-inventory-management-pnp.md).

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

> [!div class="mx-imgBorder"]
> ![Centro di micro-adempimenti](./media/overview-iot-central-retail/MFC-Dashboard.png)

Per altre informazioni, vedere l'esercitazione [Distribuire ed esaminare un modello di applicazione del centro di micro-adempimenti](./tutorial-iot-central-smart-inventory-management-pnp.md).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a creare una soluzione per la vendita al dettaglio:

* Iniziare con l'esercitazione [Creare un'applicazione di analisi dei punti vendita in Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md), che illustra come creare una soluzione con uno dei modelli di applicazione per l'analisi dei punti vendita.
* [Distribuire ed esaminare un modello di applicazione di logistica connessa](./tutorial-iot-central-connected-logistics-pnp.md).
* [Distribuire ed esaminare un modello di applicazione del centro distribuzione digitale](./tutorial-iot-central-digital-distribution-center-pnp.md).
* [Distribuire ed esaminare un modello di applicazione Gestione inventario intelligente](./tutorial-iot-central-smart-inventory-management-pnp.md).
* [Distribuire ed esaminare un modello di applicazione del centro di micro-adempimenti](./tutorial-iot-central-smart-inventory-management-pnp.md).
* Per altre informazioni su IoT Central, vedere [Panoramica di IoT Central](../preview/overview-iot-central.md).
