---
title: Panoramica di Gemelli digitali di Azure | Microsoft Docs
description: Altre informazioni su Gemelli digitali di Azure, una soluzione Azure IoT per l'intelligenza spaziale.
author: julieseto
ms.author: jseto
ms.date: 10/10/2018
ms.topic: overview
ms.service: digital-twins
services: digital-twins
manager: bertvanhoof
ms.custom: mvc
ms.openlocfilehash: 85b67683730c8352051b4d6b48f813576b11615b
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49322852"
---
# <a name="overview-of-azure-digital-twins"></a>Panoramica di Gemelli digitali di Azure

Gemelli digitali di Azure è un servizio Azure IoT che consente di creare modelli completi dell'ambiente fisico. Tra le numerose funzionalità è inclusa la possibilità di creare grafici di intelligenza spaziale per modellare le relazioni e le interazioni tra persone, spazi e dispositivi.

Gemelli digitali di Azure consente di eseguire query sui dati da uno spazio fisico piuttosto che da molti sensori diversi. Questo servizio consente di creare esperienze riutilizzabili, altamente scalabili, con riconoscimento dello spazio, che collegano flussi di dati del mondo digitale e fisico. Queste funzionalità contestuali speciali possono contribuire all'ottimizzazione delle app, indipendentemente dal fatto che l'obiettivo sia stimare le esigenze di manutenzione di uno stabilimento, analizzare i requisiti di energia in tempo reale per una rete elettrica o ottimizzare l'uso dello spazio disponibile per un ufficio.

Gemelli digitali di Azure si applica a tutti i tipi di ambienti, ad esempio, magazzini, uffici, scuole, ospedali, banche, stadi, stabilimenti, parcheggi, parchi, smart grid e città. Sono possibili scenari come registrare la temperatura giornaliera in diversi stati, monitorare percorsi di droni ad alto traffico, identificare veicoli autonomi, analizzare i livelli di occupazione per un edificio o individuare il registro di cassa più usato in un negozio. Qualsiasi sia lo scenario del mondo reale, è possibile eseguire il provisioning di un'istanza digitale corrispondente con Gemelli digitali di Azure.

Il video seguente illustra in modo più approfondito il servizio Gemelli digitali:

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>Funzionalità principali

Le funzionalità principali di Gemelli digitali di Azure includono:

### <a name="spatial-intelligence-graph"></a>Grafico di intelligenza spaziale

Il [*grafico di intelligenza spaziale*](./concepts-objectmodel-spatialgraph.md), o *grafico spaziale*, è una rappresentazione virtuale dell'ambiente fisico che consente di modellare le relazioni tra persone, luoghi e dispositivi.

Si consideri un'app Smart Utility che può coinvolgere diversi contatori di utilizzo dell'energia elettrica connessi in un quartiere. Per monitorare e prevedere con precisione l'utilizzo e la fatturazione dell'energia elettrica, l'azienda Smart Utility deve modellare ogni dispositivo e sensore con informazioni di contesto sulla posizione e sul cliente destinatario della fattura. Il grafico di intelligenza spaziale consente di modellare questi tipi di relazioni complesse.

### <a name="digital-twin-object-models"></a>Modelli a oggetti di gemelli digitali

I [modelli a oggetti di gemelli digitali](./concepts-objectmodel-spatialgraph.md) sono protocolli di dispositivo e schemi di dati predefiniti per l'allineamento delle esigenze specifiche del dominio di una soluzione per accelerare e semplificare lo sviluppo.

Ad esempio, un'applicazione che gestisce l'occupazione delle stanze potrebbe usare tipi di spazio predefiniti, come campus, edificio, piano, stanze e così via.

### <a name="multiple-and-nested-tenants"></a>Più tenant annidati

È possibile realizzare soluzioni che possono essere ridimensionate in modo sicuro ed essere riutilizzate per più tenant. È anche possibile creare più subtenant accessibili e utilizzabili in modo isolato e sicuro.

Ad esempio, un'app per l'utilizzo dello spazio può essere configurata in modo da isolare i dati di un tenant da quelli di altri tenant all'interno di un singolo edificio oppure per combinare i dati per un singolo tenant con più edifici.

### <a name="advanced-compute-capabilities"></a>Funzionalità di calcolo avanzate

Funzionalità di calcolo avanzate dette [funzioni definite dall'utente](./concepts-user-defined-functions.md) consentono di definire ed eseguire funzioni personalizzate sui [dati dei dispositivi](./concepts-device-ingress.md) in ingresso per inviare segnali a endpoint predefiniti. Questa funzionalità migliora la personalizzazione e l'automazione delle attività dei dispositivi.

Ad esempio, un'applicazione intelligente per l'agricoltura può includere una funzione definita dall'utente per valutare le letture dei sensori dell'umidità del suolo insieme alle previsioni meteo e quindi inviare segnali sulle esigenze di irrigazione.

### <a name="built-in-access-control"></a>Controllo di accesso predefinito

Funzionalità avanzate di gestione dell'accesso e delle identità, come il [controllo degli accessi in base al ruolo](./security-role-based-access-control.md) e [Azure Active Directory](./security-authenticating-apis.md), consentono di controllare in modo sicuro l'accesso di singoli utenti e dispositivi.

Ad esempio, un'app per la gestione di strutture può essere configurata per consentire agli occupanti di una stanza di regolare la temperatura entro un intervallo specificato e ai responsabili della struttura di impostare la temperatura in qualsiasi stanza su qualsiasi valore.

### <a name="ecosystem"></a>Ecosistema

È possibile connettere un'istanza di Gemelli digitali di Azure a molti servizi di Azure potenti, tra cui Analisi di flusso di Azure, intelligenza artificiale e servizi di archiviazione, nonché a Mappe di Azure, Microsoft Mixed Reality, Dynamics 365 o Office 365.

Ad esempio, l'applicazione per un edificio di uffici intelligente può usare Gemelli digitali di Azure per rappresentare i team e i dispositivi distribuiti su molti piani. Man mano che i dispositivi trasmettono i dati in tempo reale nell'istanza di Gemelli digitali di cui è stato effettuato il provisioning, Analisi di flusso di Azure può elaborare tali dati per fornire informazioni dettagliate significative su cui è possibile intervenire. I dati possono quindi essere archiviati in Archiviazione di Azure e convertiti in un formato di file condivisibile per essere distribuiti in tutta l'organizzazione con Office 365.

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>Soluzioni che traggono vantaggio da Gemelli digitali di Azure

Gemelli digitali di Azure è utile per rappresentare il mondo fisico e le tante relazioni che lo caratterizzano, perché semplifica la modellazione, l'elaborazione dei dati, la gestione degli eventi e il rilevamento dei dispositivi IoT. L'elenco seguente include solo alcuni dei tanti scenari, in diversi settori, che traggono vantaggio dall'uso di questo servizio:

* Mostrare a una società che gestisce proprietà immobiliari i livelli di occupazione di uno spazio nel tempo per ottenere informazioni dettagliate sui modi migliori per configurare un edificio a uso uffici.
* Attivare ticket di ordine di lavoro per un'app per dispositivi mobili che gestisce il servizio di sicurezza, la pianificazione dei servizi di pulizia e qualsiasi altro servizio in uno spazio commerciale o in una sede di eventi sportivi.
* Mostrare agli occupanti di un edificio quali stanze sono occupate in tempo reale, consentendo quindi la prenotazione di spazi di lavoro adatti alle specifiche esigenze.
* Registrare la posizione delle dotazioni all'interno di uno spazio.
* Ottimizzare la ricarica di veicoli elettrici modellando le preferenze degli utenti e i vincoli della rete elettrica.

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>Gemelli digitali di Azure nel contesto di altri servizi IoT

Gemelli digitali di Azure usa l'hub IoT di Azure per connettere i dispositivi e i sensori IoT che consentono di mantenere tutto aggiornato con il mondo fisico. Il diagramma seguente mostra le relazioni tra Gemelli digitali di Azure e altri servizi Azure IoT:

![Gemelli digitali di Azure è un servizio basato sull'hub IoT di Azure](./media/overview/azure-digital-twins-in-iot-ecosystem.png)

Per una descrizione più dettagliata degli altri componenti dell'ecosistema IoT, vedere [Tecnologie e soluzioni di Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-services-and-technologies).

## <a name="next-steps"></a>Passaggi successivi

Continuare con una breve demo delle funzionalità di Gemelli digitali di Azure:

> [!div class="nextstepaction"]
> [Guida introduttiva: Trovare le stanze disponibili usando Gemelli digitali di Azure](./quickstart-view-occupancy-dotnet.md)

Analizzare in dettaglio un'applicazione di gestione di strutture con Gemelli digitali di Azure:

> [!div class="nextstepaction"]
> [Esercitazione: Distribuire Gemelli digitali di Azure e configurare un grafico spaziale](./tutorial-facilities-setup.md)

Informazioni su concetti di base relativi a Gemelli digitali di Azure:

> [!div class="nextstepaction"]
> [Informazioni sui modelli a oggetti di Gemelli digitali e sul grafico di intelligenza spaziale](./concepts-objectmodel-spatialgraph.md)
