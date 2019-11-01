---
title: Introduzione ad Azure IoT (Internet of Things)
description: Introduzione che illustra i concetti fondamentali di Azure IoT e dei servizi IoT, inclusi esempi utili relativi all'uso di IoT.
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/11/2018
ms.author: robinsh
ms.openlocfilehash: 2f46ebcabd98c7a8c3376157c72da9ec5ed424a4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935221"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Informazioni su Azure IoT (Internet of Things)

Azure IoT (Internet of Things, Internet delle cose) è una raccolta di servizi cloud gestiti da Microsoft per la connessione, il monitoraggio e il controllo di miliardi di asset IoT. Semplificando, una soluzione IoT è costituita da uno o più dispositivi IoT e uno o più servizi back-end in esecuzione sul cloud in grado di comunicare tra loro. 

Questo articolo illustra i concetti fondamentali di IoT, presenta i casi d'uso e descrive brevemente gli otto servizi separati disponibili. Le informazioni relative alle opzioni disponibili consentono di individuare gli aspetti da approfondire per contribuire alla progettazione di uno scenario specifico.

## <a name="introduction"></a>Introduzione

Una soluzione IoT è costituita principalmente da dispositivi, servizi back-end e comunicazioni reciproche. 

### <a name="iot-devices"></a>Dispositivi IoT

I dispositivi sono in genere costituiti da un circuito stampato con sensori collegati per la connessione a Internet. Molti dispositivi comunicano tramite un chip Wi-Fi. Ecco alcuni esempi di dispositivi IoT:

* Sensori di pressione in una pompa dell'olio remota
* Sensori di temperatura e umidità in un condizionatore
* Accelerometri in un ascensore
* Sensori di presenza in una stanza

Due dispositivi usati spesso per la creazione di prototipi sono MX Chip IoT Devkit di base di Microsoft e i dispositivi Raspberry PI. MX Chip Devkit include sensori per temperatura, pressione e umidità, oltre a un giroscopio, un accelerometro, un magnetometro e un chip Wi-Fi. Raspberry PI è un dispositivo IoT a cui è possibile collegare molti tipi diversi di sensori, in modo da selezionare esattamente gli elementi necessari per lo scenario specifico. 

Per altre informazioni sui dispositivi IoT disponibili, vedere il [catalogo dei dispositivi certificati per IoT](https://catalog.azureiotsolutions.com/alldevices) più grande del settore.

Gli [SDK per i dispositivi IoT](../iot-hub/iot-hub-devguide-sdks.md) consentono di creare app in esecuzione nei dispositivi, in modo che eseguano le attività necessarie. Con gli SDK è possibile inviare dati di telemetria all'hub IoT, ricevere messaggi e aggiornamenti dall'hub IoT e così via.

### <a name="communication"></a>Comunicazione

Il dispositivo può comunicare con i servizi back-end in entrambe le direzioni. Ecco alcuni esempi dei modi in cui il dispositivo può comunicare con la soluzione back-end.

#### <a name="examples"></a>Esempi 

* Il dispositivo può inviare ogni 5 minuti a un hub IoT i dati relativi alla temperatura da un camion frigorifero in movimento. 

* Il servizio back-end può richiedere al dispositivo di inviare dati di telemetria con maggiore frequenza per contribuire alla diagnosi di un problema. 

* Il dispositivo può inviare avvisi in base ai valori letti dai rispettivi sensori. Se ad esempio si monitora un reattore chimico di tipo batch in uno stabilimento chimico, è consigliabile inviare un avviso quando la temperatura supera un determinato valore.

* Il dispositivo può inviare informazioni a un dashboard per la visualizzazione da parte di operatori umani. Una sala di controllo in una raffineria, ad esempio, può mostrare la temperatura e la pressione di ogni conduttura, oltre al volume dei liquidi che attraversano tale conduttura, consentendo agli operatori di monitorarla. 

Queste attività e altro ancora possono essere implementate con gli [SDK per i dispositivi IoT](../iot-hub/iot-hub-devguide-sdks.md).

#### <a name="connection-considerations"></a>Considerazioni sulla connessione

La connessione sicura e affidabile dei dispositivi è spesso la sfida principale nelle soluzioni IoT. I dispositivi IoT infatti presentano caratteristiche diverse da quelle di altri client, ad esempio browser e app per dispositivi mobili. I dispositivi IoT in particolare:

* Sono spesso sistemi incorporati senza operatore umano (diversamente da un telefono).

* Possono essere distribuiti in località remote, dove l'accesso fisico è costoso.

* Possono essere raggiungibili solo tramite il back-end della soluzione. Non esiste un altro modo per interagire con il dispositivo.

* Possono avere risorse di alimentazione e di elaborazione limitate.

* Possono disporre di una connettività di rete intermittente, lenta o costosa.

* Possono richiedere protocolli di applicazioni proprietari, personalizzati o specifici del settore.

### <a name="back-end-services"></a>Servizi back-end 

Ecco alcune funzioni offerte da un servizio back-end.

* Ricezione dei dati di telemetria su larga scala dai dispositivi e possibilità di scegliere come elaborare e archiviare tali dati.

* Analisi dei dati di telemetria per fornire informazioni dettagliate, in tempo reale o dopo un evento.

* Invio di comandi dal cloud a un dispositivo specifico. 

* Provisioning di dispositivi e possibilità di controllare i dispositivi autorizzati a connettersi all'infrastruttura.

* Controllo dello stato e monitoraggio delle attività dei dispositivi.

In uno scenario di manutenzione predittiva, ad esempio, il back-end cloud archivia i dati di telemetria cronologici. La soluzione usa questi dati per identificare un potenziale comportamento anomalo in pompe specifiche prima che causino un vero problema. Usando l'analisi dei dati, può stabilire che la soluzione preventiva è l'invio di un comando al dispositivo per eseguire un'azione correttiva. Questo processo genera un ciclo di feedback automatizzato tra il dispositivo e il cloud che accresce notevolmente l'efficienza della soluzione.

## <a name="an-iot-example"></a>Esempio di IoT

Ecco un esempio del modo in cui una società ha usato IoT per risparmiare milioni di dollari. 

Un ranch di grandi dimensioni include centinaia di migliaia di bovini. Tenere traccia di così tanti animali e del rispettivo stato è difficile e richiede numerosi spostamenti. Sono stati quindi collegati sensori a ogni animale per l'invio di informazioni quali le coordinate GPS e la temperatura a un servizio back-end e la scrittura di tali informazioni in un database.

Un servizio di analisi esamina i dati in ingresso e analizza i dati per ogni capo di bestiame per rispondere a domande analoghe alle seguenti:

* Il capo di bestiame ha la febbre? Da quanto tempo ha la febbre? Se è passato più di un giorno, è possibile ottenere le coordinate GPS, individuare l'animale e, se necessario, curarlo con antibiotici. 

* Il capo di bestiame è rimasto nello stesso posto per più di un giorno? Se la risposta è affermativa, è possibile ottenere le coordinate GPD e individuare l'animale. L'animale è caduto da un dirupo? Il capo di bestiame è ferito? Il capo di bestiame necessita di assistenza? 

L'implementazione di questa soluzione IoT ha consentito all'azienda di controllare e curare rapidamente i bovini e ha ridotto la quantità di tempo necessaria per tenere sotto controllo gli animali, consentendo quindi un risparmio significativo. Per altri esempi completi sull'uso di IoT da parte delle aziende, vedere [Microsoft Technical Case Studies for IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured) (Case study tecnici Microsoft per IoT). 

## <a name="iot-services"></a>Servizi IoT

Azure include alcuni servizi correlati a IoT e può essere difficile individuare il servizio da usare in una situazione specifica. Alcuni servizi, come IoT Central e gli acceleratori di soluzione IoT, forniscono modelli utili per la creazione di soluzioni personalizzate e per il completamento rapido delle operazioni iniziali. È anche possibile sviluppare completamente soluzioni personalizzate usando gli altri servizi disponibili, in base all'assistenza e al livello di controllo necessari. Ecco un elenco dei servizi disponibili e delle possibilità di utilizzo di tali servizi.

1. [**IoT Central**](../iot-central/core/overview-iot-central.md): soluzione SaaS che consente di connettere, monitorare e gestire i dispositivi IoT. Per iniziare, selezionare un modello per il tipo di dispositivo e quindi creare e testare un'applicazione IoT Central di base che verrà usata dagli operatori del dispositivo. L'applicazione IoT Central consentirà anche di monitorare i dispositivi e di effettuare il provisioning dei nuovi dispositivi. Questo servizio è destinato a soluzioni semplici che non richiedono una personalizzazione approfondita del servizio. 

2. [**Acceleratori di soluzione IoT**](/azure/iot-suite): questa è una raccolta di soluzioni PaaS che possono essere usate per accelerare lo sviluppo di una soluzione IoT. È possibile iniziare con una soluzione IoT disponibile e quindi personalizzarla completamente in base ai propri requisiti. Per personalizzare il back-end sono necessarie competenze Java o .NET e per personalizzare la visualizzazione sono necessarie competenze JavaScript. 

3. [**Hub IoT**](/azure/iot-hub/): questo servizio consente di stabilire una connessione dai dispositivi a un hub IoT e di monitorare e controllare miliardi di dispositivi IoT. Ciò risulta particolarmente utile se sono necessarie comunicazioni bidirezionali tra i dispositivi IoT e il back-end. Questo è il servizio sottostante per IoT Central e per gli acceleratori di soluzione IoT. 

4. [**Servizio Device Provisioning in hub IoT**](/azure/iot-dps/): servizio helper per l'hub IoT che può essere usato per il provisioning sicuro dei dispositivi nell'hub IoT. Con questo servizio è possibile effettuare con facilità il provisioning di milioni di dispositivi rapidamente, invece di effettuare il provisioning di un dispositivo alla volta. 

5. [**IoT Edge**](/azure/iot-edge/): questo servizio è basato sull'hub IoT. Può essere usato per analizzare i dati nei dispositivi IoT invece che sul cloud. Lo spostamento di parti del carico di lavoro nei dispositivi perimetrali consente di inviare al cloud un numero minore di messaggi. 

6. [**Gemelli digitali di Azure**](../digital-twins/index.yml): questo servizio consente di creare modelli completi dell'ambiente fisico. È possibile modellare fedelmente le relazioni e le interazioni tra persone, spazi e dispositivi. È ad esempio possibile prevedere le esigenze a livello di manutenzione per uno stabilimento, analizzare in tempo reale i requisiti a livello energetico per una rete elettrica oppure ottimizzare l'uso dello spazio disponibile per un ufficio.

7. [**Time Series Insights**](/azure/time-series-insights): questo servizio consente di archiviare, visualizzare ed eseguire query su quantità elevate di dati relativi alle serie temporali generati dai dispositivi IoT. È possibile usare questo servizio con l'hub IoT. 

8. [**Mappe di Azure**](/azure/azure-maps): questo servizio fornisce informazioni geografiche ad applicazioni Web e per dispositivi mobili. Sono disponibili un set completo di API REST e un controllo JavaScript basato su Web che possono essere usati per creare applicazioni flessibili compatibili con applicazioni desktop o per dispositivi mobili per dispositivi Apple e Windows.

## <a name="next-steps"></a>Passaggi successivi

Per casi aziendali effettivi e per informazioni sull'architettura usata, vedere [Microsoft Azure IoT Technical Case Studies](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured) (Case study tecnici di Microsoft Azure IoT).

Per alcuni progetti di esempio che possono essere usati con un DevKit di IoT, vedere [IoT DevKit Project Catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) (Catalogo di progetti per DevKit IoT). 

Per una spiegazione più completa dei diversi servizi e del modo in cui vengono usati, vedere [Servizi e tecnologie di Azure IoT](iot-services-and-technologies.md).

Per un'analisi approfondita dell'architettura IoT, vedere [Microsoft Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture) (Architettura di riferimento di Microsoft Azure IoT).
