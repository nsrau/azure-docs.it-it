---
title: Introduzione ad Azure IoT (Internet delle cose)
description: Panoramica di Azure IoT e delle tecnologie e dei servizi correlati.
author: BryanLa
manager: timlt
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 05/18/2018
ms.author: bryanla
ms.openlocfilehash: ed96181606e2db4102aa609973ade9ecbfde6c90
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187275"
---
# <a name="introduction-to-azure-and-the-internet-of-things"></a>Introduzione ad Azure e Internet delle cose

Azure IoT è costituito da tre aree di tecnologie e soluzioni: soluzioni, servizi della piattaforma ed Edge, tutti progettati per semplificare lo sviluppo end-to-end dell'applicazione IoT. Questo articolo inizia con la descrizione delle caratteristiche comuni di una soluzione IoT nel cloud, seguita da una panoramica del modo in cui Azure IoT gestisce le complessità nei progetti IoT e dei motivi che rendono consigliabile adottare Azure IoT.

## <a name="iot-solution-architecture"></a>Architettura della soluzione IoT

Le soluzioni IoT richiedono una comunicazione bidirezionale sicura tra dispositivi, possibilmente nell'ordine dei milioni, e un back-end della soluzione. Una soluzione può ad esempio usare l'analisi predittiva e automatizzata per trovare informazioni dettagliate dal flusso di eventi da dispositivo a cloud. 

Il diagramma seguente mostra gli elementi chiave di una tipica architettura della soluzione IoT. Il diagramma è indipendente dai dettagli dell'implementazione specifica, ad esempio i servizi di Azure usati e i sistemi operativi dei dispositivi. In questa architettura, i dispositivi IoT raccolgono i dati che inviano a un gateway nel cloud. Il gateway del cloud rende i dati disponibili per l'elaborazione da parte di altri servizi back-end. Questi servizi back-end possono inviare i dati a:

* Altre applicazioni line-of-business.
* Operatori umani tramite un dashboard o un altro dispositivo di presentazione.

![Architettura della soluzione IoT][img-solution-architecture]

> [!NOTE]
> Per un'analisi approfondita dell'architettura IoT, vedere [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Architettura di riferimento di Microsoft Azure IoT).

### <a name="device-connectivity"></a>Connettività dei dispositivi

In un'architettura della soluzione IoT i dispositivi in genere inviano i dati di telemetria al cloud per l'archiviazione e l'elaborazione. In uno scenario di manutenzione predittiva, ad esempio, il back-end della soluzione potrebbe usare il flusso di dati dei sensori per determinare quando una determinata pompa richiede manutenzione. I dispositivi possono anche ricevere e rispondere a messaggi da cloud a dispositivo leggendo i messaggi da un endpoint cloud. Nello stesso esempio il back-end della soluzione potrebbe inviare messaggi ad altre pompe della stazione di pompaggio per avviare il reinstradamento dei flussi appena prima che inizi l'intervento di manutenzione, in modo che il tecnico della manutenzione possa iniziare subito il lavoro al suo arrivo.

La connessione sicura e affidabile dei dispositivi è spesso la sfida principale nelle soluzioni IoT. I dispositivi IoT infatti presentano caratteristiche diverse da quelle di altri client, ad esempio browser e app per dispositivi mobili. I dispositivi IoT in particolare:

* Sono spesso sistemi incorporati senza operatore umano (diversamente da un telefono).
* Possono essere distribuiti in località remote, dove l'accesso fisico è costoso.
* Possono essere raggiungibili solo tramite il back-end della soluzione. Non esiste un altro modo per interagire con il dispositivo.
* Possono avere risorse di alimentazione e di elaborazione limitate.
* Possono disporre di una connettività di rete intermittente, lenta o costosa.
* Possono richiedere protocolli di applicazioni proprietari, personalizzati o specifici del settore.
* Possono essere creati utilizzando un'ampia gamma di piattaforme hardware e software comuni.

Oltre ai vincoli precedenti, le soluzioni IoT devono essere anche scalabili, sicure e affidabili.

A seconda del protocollo di comunicazione e della disponibilità di rete, un dispositivo può comunicare con il cloud direttamente o tramite un gateway intermedio. Le architetture IoT presentano spesso una combinazione di questi due modelli di comunicazione.

### <a name="data-processing-and-analytics"></a>e analisi dei dati

Nelle moderne soluzioni IoT l'elaborazione dei dati può essere eseguita nel cloud o sul lato dispositivo. L'elaborazione lato dispositivo viene detta *elaborazione perimetrale*. La scelta di dove elaborare i dati dipende da alcuni fattori, ad esempio:

* Vincoli di rete. Se la larghezza di banda tra i dispositivi e il cloud è limitata, si è incentivati a preferire l'elaborazione perimetrale.
* Tempo di risposta. Se è necessario intervenire su un dispositivo in tempo quasi reale, può essere meglio elaborare la risposta nel dispositivo stesso, ad esempio un braccio robotico che deve essere arrestato in caso di emergenza.
* Ambiente normativo. Alcuni dati non possono essere inviati al cloud.

In generale l'elaborazione dei dati sia nel dispositivo perimetrale che nel cloud è una combinazione delle funzionalità seguenti:

* Ricezione dei dati di telemetria su larga scala dai dispositivi e scelta di come elaborare e archiviare tali dati.
* Analisi dei dati di telemetria per fornire informazioni dettagliate, sia in tempo reale che dopo un evento.
* Invio di comandi dal cloud o da un dispositivo gateway a un dispositivo specifico.

Un back-end cloud IoT deve anche offrire:

* Funzionalità di registrazione del dispositivo che consentono di:
    * Effettuare il provisioning dei dispositivi.
    * Controllare a quali dispositivi è consentito connettersi all'infrastruttura.
* Gestione dei dispositivi per controllarne lo stato e monitorarne le attività.

In uno scenario di manutenzione predittiva, ad esempio, il back-end cloud archivia i dati di telemetria cronologici. La soluzione usa questi dati per identificare un potenziale comportamento anomalo in pompe specifiche prima che causino un vero problema. Usando l'analisi dei dati, può stabilire che la soluzione preventiva è l'invio di un comando al dispositivo per eseguire un'azione correttiva. Questo processo genera un ciclo di feedback automatizzato tra il dispositivo e il cloud che accresce notevolmente l'efficienza della soluzione.

### <a name="presentation-and-business-connectivity"></a>Connettività aziendale e di presentazione

Il livello di connettività aziendale e di presentazione consente agli utenti finali di interagire con la soluzione IoT e i dispositivi. Consente anche agli utenti di visualizzare e analizzare i dati raccolti dai dispositivi. Queste visualizzazioni possono assumere la forma di dashboard o report di BI che possono mostrare sia i dati cronologici sia i dati quasi in tempo reale. Ad esempio, un operatore può controllare lo stato di determinate stazioni di pompaggio e vedere gli avvisi generati dal sistema. Questo livello consente anche l'integrazione della soluzione back-end IoT con le applicazioni line-of-business esistenti che possono legarsi ai processi o ai flussi di lavoro aziendali. Una soluzione di manutenzione predittiva, ad esempio, può essere integrata con un sistema di pianificazione per prenotare un tecnico per visitare una stazione di pompaggio quando identifica una pompa che richiede manutenzione.

## <a name="why-azure-iot"></a>Perché scegliere Azure IoT?

Azure IoT semplifica la complessità dei progetti IoT e gestisce aspetti complicati come sicurezza, incompatibilità delle infrastrutture e scalabilità della soluzione IoT. Ecco come:

**Agile** <br>
Accelera l'adozione della tecnologia IoT
* Scalabile: iniziare in piccolo e poi crescere fino alle dimensioni desiderate, ovunque. Milioni di dispositivi, terabyte di dati nella maggior parte delle aree in tutto il mondo.

* Aperto: usare l'infrastruttura esistente oppure modernizzarla per il futuro tramite la connessione a qualsiasi dispositivo, software o servizio.

* Ibrido: costruire in base alle esigenze distribuendo la soluzione nella rete perimetrale, nel cloud o in qualsiasi posizione intermedia.

* Velocità: distribuire più velocemente, ridurre il time-to-market e ottenere un vantaggio sulla concorrenza con il leader degli acceleratori di soluzioni e dell'innovazione nell'IoT.

**Completo** <br>
Dare forza propulsiva all'azienda

* Completo: Microsoft è l'unico provider di soluzioni IoT con una piattaforma completa che si estende dal dispositivo al cloud fino ai Big Data e all'analisi avanzata e con servizi gestiti.

* Partner per il successo: sfruttare la potenza del più vasto ecosistema di partner del mondo e portare in vita line-of-business e tecnologia, nel settore e in tutto il mondo.

* Basato sui dati: l'IoT riguarda i dati e le migliori soluzioni IoT consentono di unire tutti gli strumenti necessari per archiviare, interpretare, trasformare, analizzare e presentare i dati all'utente giusto, nel posto giusto e al momento giusto.

* Incentrato sui dispositivi: Microsoft IoT consente di connettere qualsiasi cosa, dai dispositivi legacy a un vasto ecosistema di hardware certificato e offre la possibilità di creare i propri dispositivi in tutti i sistemi perimetrali, mobili e incorporati.

**Proteggere** <br>
Gestire l'aspetto più complesso dell'IoT: la sicurezza

* Ottimizzare: con Microsoft IoT è possibile unire la propria visione alla tecnologia, alle procedure consigliate e alle funzionalità necessarie per gestire l'aspetto più complesso dell'IoT: la sicurezza.

* Intervenire: proteggere i dati dell'IoT e gestire i rischi con la gestione delle identità e degli accessi, la protezione da minacce e la gestione della sicurezza.

* Tranquillità: garantire la sicurezza delle informazioni riservate tra dispositivi, software, applicazioni e servizi cloud, nonché negli ambienti locali.

* Conformità: Microsoft è leader di settore nella definizione di requisiti di sicurezza che soddisfano un'ampia gamma di standard internazionali e dei singoli settori per dispositivi IoT, dati e servizi.

## <a name="next-steps"></a>Passaggi successivi

Esplorare le aree di tecnologie e le soluzioni seguenti oppure vedere il sommario a sinistra per l'elenco dei servizi di Azure IoT.

<ul class="panelContent cardsF">  
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Soluzioni</h3>
                        <a href="/azure/iot-suite">Acceleratori di soluzioni IoT</a><br/>
                        <a href="/azure/iot-central">IoT Central</a>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Servizi della piattaforma</h3>
                        <a href="/azure/iot-hub">Hub IoT</a><br/>
                        <a href="/azure/iot-dps">Servizio Device Provisioning in hub IoT</a><br/>
                        <a href="/azure/azure-maps">Mappe</a><br/>
                        <a href="/azure/time-series-insights">Time Series Insights</a>
                    </div>
                </div>
            </div>
        </div>
    </li>  
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Microsoft Edge</h3>
                        <a href="/azure/iot-edge">IoT Edge</a><br/>
                        <a href="/azure/iot-edge/how-iot-edge-works">Informazioni su IoT Edge</a>
                    </div>
                </div>
            </div>
        </div>
    </li>      
</ul>

[img-paas-saas-technologies-solutions]: media/index/paas-saas-technologies-solutions.png
[img-solution-architecture]: ./media/iot-introduction/iot-reference-architecture.png
[img-dashboard]: ./media/iot-introduction/iot-suite.png

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-iot-central-land]: https://docs.microsoft.com/microsoft-iot-central/
[lnk-iot-dps-land]: /azure/iot-dps/index.yml
[lnk-iot-edge-land]: /azure/iot-edge/index.yml
[lnk-iot-hub-land]: /azure/iot-hub/index.md
[lnk-iot-maps-land]: /azure/maps/index.yml
[lnk-iot-sa-land]: ../iot-accelerators/index.yml
[lnk-iot-tsi-land]: /azure/time-series-insights/index.yml

[lnk-iot-hub]: ../iot-hub/about-iot-hub.md
[lnk-iot-sa]: ../iot-accelerators/about-iot-accelerators.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[lnk-protocol-gateway]:  ../iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: https://aka.ms/iotrefarchitecture


