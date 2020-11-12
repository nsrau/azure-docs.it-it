---
title: 'Gateway per dispositivi downstream: Azure IoT Edge | Microsoft Docs'
description: Azure IoT Edge può essere usato per creare un dispositivo gateway trasparente, opaco o proxy che invia i dati da più dispositivi downstream al cloud o li elabora in locale.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 83e8089073f7e7e7634ddf00f7276e12aaf645b0
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536439"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Come usare un dispositivo Azure IoT Edge come gateway

I dispositivi IoT Edge possono funzionare come gateway, fornendo una connessione tra altri dispositivi nella rete e nell'hub Internet.

Il modulo Hub IoT Edge funge da hub Internet, in modo da poter gestire le connessioni da qualsiasi dispositivo con un'identità con l'hub Internet, inclusi altri dispositivi IoT Edge. Questo tipo di modello di gateway è denominato *trasparente* perché i messaggi possono passare dai dispositivi downstream all'hub Internet come se non fosse presente un gateway tra di essi.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
A partire dalla versione 1,2 di IoT Edge, i gateway trasparenti possono gestire le connessioni downstream da altri dispositivi IoT Edge.
::: moniker-end

Per i dispositivi che non possono o non possono connettersi all'hub delle cose in modo autonomo, IoT Edge Gateway possono fornire tale connessione. Questo tipo di modello di gateway viene chiamato *traduzione* , perché il dispositivo IOT Edge deve eseguire l'elaborazione dei messaggi di dispositivo downstream in ingresso prima che possano essere inviati all'hub Internet. Questi scenari richiedono moduli aggiuntivi sul gateway IoT Edge per gestire i passaggi di elaborazione.

I modelli del gateway trasparente e di conversione non si escludono a vicenda. Un singolo dispositivo IoT Edge può funzionare sia come gateway trasparente che come gateway di conversione.

Tutti i modelli di gateway offrono i vantaggi seguenti:

* **Analisi alla periferia** : usare i servizi di intelligenza artificiale localmente per elaborare i dati provenienti dai dispositivi downstream senza inviare i dati di telemetria con la massima fedeltà al cloud. Trovare e gestire le informazioni dettagliate in locale e inviare solo un subset di dati all'hub IoT.
* **Isolamento dei dispositivi downstream** : il dispositivo gateway può proteggere tutti i dispositivi downstream dall'esposizione a Internet. Può risiedere tra una rete di tecnologia operativa (OT) che non dispone di connettività e una rete IT (Information Technology) che fornisce l'accesso al Web. In modo analogo, i dispositivi che non hanno la possibilità di connettersi all'hub delle cose possono connettersi a un dispositivo gateway.
* **Multiplexing delle connessioni** : tutti i dispositivi connessi all'hub IoT tramite un gateway IoT Edge usano la stessa connessione sottostante.
* **Smorzamento del traffico** : il dispositivo IoT Edge esegue automaticamente il backoff esponenziale se l'hub IoT limita il traffico, salvando al contempo i messaggi in locale. Questo vantaggio rende la soluzione resiliente ai picchi di traffico.
* **Supporto offline** : il dispositivo gateway archivia i messaggi e gli aggiornamenti dei dispositivi gemelli che non possono essere recapitati all'hub Internet.

## <a name="transparent-gateways"></a>Gateway trasparenti

Nel modello di gateway trasparente, i dispositivi che teoricamente potrebbero connettersi all'hub Internet possono connettersi a un dispositivo gateway. I dispositivi downstream hanno le proprie identità dell'hub e si connettono usando i protocolli MQTT o AMQP. Il gateway passa semplicemente le comunicazioni tra i dispositivi e l'hub IoT. Sia i dispositivi che gli utenti che interagiscono con loro tramite l'hub Internet non sanno che un gateway sta mediando le comunicazioni. Questa mancanza di consapevolezza significa che il gateway è considerato *trasparente*.

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

I dispositivi IoT Edge non possono essere a valle di un gateway di IoT Edge.

![Diagramma-modello gateway trasparente](./media/iot-edge-as-gateway/edge-as-gateway-transparent.png)

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

A partire dalla versione 1.2.0, i dispositivi IoT Edge possono connettersi tramite gateway trasparenti.

<!-- TODO add a downstream IoT Edge device to graphic -->

::: moniker-end

### <a name="parent-and-child-relationships"></a>Relazioni padre e figlio

Si dichiarano le relazioni Transparent Gateway nell'hub degli elementi Internet impostando il gateway IoT Edge come *padre* di un dispositivo *figlio* downstream che si connette a esso.

La relazione padre/figlio viene stabilita in tre punti della configurazione del gateway:

#### <a name="cloud-identities"></a>Identità cloud

Tutti i dispositivi in uno scenario con gateway trasparente necessitano di identità cloud, in modo che possano eseguire l'autenticazione nell'hub Internet. Quando si crea o si aggiorna un'identità del dispositivo, è possibile impostare i dispositivi padre o figlio del dispositivo. Questa configurazione autorizza il dispositivo gateway padre a gestire l'autenticazione per i dispositivi figlio.

I dispositivi figlio possono avere un solo padre. Ogni elemento padre può avere fino a 100 elementi figlio.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
I dispositivi IoT Edge possono essere elementi padre e figlio nelle relazioni Transparent Gateway. È possibile creare una gerarchia di più dispositivi IoT Edge Reporting. Il nodo principale di una gerarchia del gateway può avere fino a cinque generazioni di elementi figlio. Ad esempio, un dispositivo IoT Edge può avere cinque livelli di dispositivi IoT Edge collegati come elementi figlio al di sotto di esso. Il dispositivo IoT Edge nella quinta generazione, tuttavia, non può avere elementi figlio, IoT Edge o altro.
::: moniker-end

#### <a name="gateway-discovery"></a>Individuazione gateway

Un dispositivo figlio deve essere in grado di trovare il dispositivo padre nella rete locale. Configurare i dispositivi gateway con un nome **host** , ovvero un nome di dominio completo (FQDN) o un indirizzo IP, che i dispositivi figlio utilizzeranno per individuarlo.

Nei dispositivi di tutto il downstream, usare il parametro **gatewayHostname** nella stringa di connessione per puntare al dispositivo padre.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Nei dispositivi IoT Edge downstream usare il parametro **parent_hostname** nel file config. YAML per puntare al dispositivo padre.
::: moniker-end

#### <a name="secure-connection"></a>Connessione protetta

I dispositivi padre e figlio devono anche autenticare le connessioni tra loro. Ogni dispositivo richiede una copia di un certificato CA radice condiviso che i dispositivi figlio usano per verificare che si connettano al gateway appropriato.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Quando più gateway IoT Edge si connettono tra loro in una gerarchia di gateway, tutti i dispositivi nella gerarchia devono usare una singola catena di certificati.
::: moniker-end

### <a name="device-capabilities-behind-transparent-gateways"></a>Funzionalità del dispositivo dietro gateway trasparenti

Tutte le primitive dell'hub Internet che funzionano con la pipeline di messaggistica di IoT Edge supportano anche scenari di Gateway transparent. Ogni gateway IoT Edge dispone di funzionalità di archiviazione e di avanzamento per i messaggi in arrivo.

Usare la tabella seguente per vedere il modo in cui sono supportate le funzionalità dell'hub per i dispositivi diversi rispetto ai dispositivi dietro i gateway.

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

| Funzionalità | Dispositivo IoT | Tutto dietro un gateway |
| ---------- | ---------- | -------------------- |
| [Messaggi da dispositivo a cloud (D2C)](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Sì-D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Sì-D2C figlio](./media/iot-edge-as-gateway/check-yes.png) |
| [Messaggi da cloud a dispositivo (C2D)](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Sì-C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Sì, C2D figlio.](./media/iot-edge-as-gateway/check-yes.png) |
| [Metodi diretti](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Sì-metodo diretto.](./media/iot-edge-as-gateway/check-yes.png) | ![Sì-metodo diretto dell'elemento figlio](./media/iot-edge-as-gateway/check-yes.png) |
| [Dispositivi gemelli](../iot-hub/iot-hub-devguide-device-twins.md) e [moduli gemelli](../iot-hub/iot-hub-devguide-module-twins.md) | ![Sì-tutti i gemelli](./media/iot-edge-as-gateway/check-yes.png) | ![Sì-gemelli degli elementi figlio](./media/iot-edge-as-gateway/check-yes.png) |
| [Caricamento file](../iot-hub/iot-hub-devguide-file-upload.md) | ![Sì-caricamento file](./media/iot-edge-as-gateway/check-yes.png) | ![Caricamento file figlio senza stato](./media/iot-edge-as-gateway/crossout-no.png) |

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

| Funzionalità | Dispositivo IoT | Tutto dietro un gateway | Dispositivo IoT Edge | IoT Edge dietro un gateway |
| ---------- | ---------- | --------------------------- | --------------- | -------------------------------- |
| [Messaggi da dispositivo a cloud (D2C)](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Sì-D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Sì-D2C figlio](./media/iot-edge-as-gateway/check-yes.png) | ![Sì, IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Sì-IoT Edge figlio D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [Messaggi da cloud a dispositivo (C2D)](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Sì-C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Sì, C2D figlio.](./media/iot-edge-as-gateway/check-yes.png) | ![C2D no IoT Edge](./media/iot-edge-as-gateway/crossout-no.png) | ![C2D figlio senza IoT Edge](./media/iot-edge-as-gateway/crossout-no.png) |
| [Metodi diretti](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Sì-metodo diretto.](./media/iot-edge-as-gateway/check-yes.png) | ![Sì-metodo diretto dell'elemento figlio](./media/iot-edge-as-gateway/check-yes.png) | ![Sì, IoT Edge metodo diretto](./media/iot-edge-as-gateway/check-yes.png) | ![Sì-figlio IoT Edge metodo diretto](./media/iot-edge-as-gateway/check-yes.png) |
| [Dispositivi gemelli](../iot-hub/iot-hub-devguide-device-twins.md) e [moduli gemelli](../iot-hub/iot-hub-devguide-module-twins.md) | ![Sì-tutti i gemelli](./media/iot-edge-as-gateway/check-yes.png) | ![Sì-gemelli degli elementi figlio](./media/iot-edge-as-gateway/check-yes.png) | ![Sì IoT Edge gemelli](./media/iot-edge-as-gateway/check-yes.png) | ![Sì-figlio IoT Edge gemelli](./media/iot-edge-as-gateway/check-yes.png) |
| [Caricamento file](../iot-hub/iot-hub-devguide-file-upload.md) | ![Sì-caricamento file](./media/iot-edge-as-gateway/check-yes.png) | ![Caricamento file figlio senza stato](./media/iot-edge-as-gateway/crossout-no.png) | ![Caricamento file senza IoT Edge](./media/iot-edge-as-gateway/crossout-no.png) | ![Caricamento file figlio senza IoT Edge](./media/iot-edge-as-gateway/crossout-no.png) |
| Pull dell'immagine del contenitore |   |   | ![Sì-IoT Edge pull del contenitore](./media/iot-edge-as-gateway/check-yes.png) | ![Sì-pull del contenitore IoT Edge figlio](./media/iot-edge-as-gateway/check-yes.png) |
| Caricamento BLOB |   |   | ![Sì-IoT Edge caricamento BLOB](./media/iot-edge-as-gateway/check-yes.png) | ![Sì-caricamento BLOB IoT Edge figlio](./media/iot-edge-as-gateway/check-yes.png) |

Le **Immagini del contenitore** possono essere scaricate, archiviate e recapitate dai dispositivi padre ai dispositivi figlio.

I **BLOB** , inclusi i bundle di supporto e i log, possono essere caricati dai dispositivi figlio ai dispositivi padre.

::: moniker-end

## <a name="translation-gateways"></a>Gateway di traduzione

Se i dispositivi downstream non possono connettersi all'hub Internet, il gateway IoT Edge deve fungere da traduttore. Questo modello è spesso necessario per i dispositivi che non supportano MQTT, AMQP o HTTP. Poiché questi dispositivi non possono connettersi all'hub Internet, non possono connettersi al modulo Hub IoT Edge senza alcuna pre-elaborazione.

I moduli personalizzati o di terze parti che sono spesso specifici dell'hardware o del protocollo downstream del dispositivo devono essere distribuiti nel gateway IoT Edge. Questi moduli di conversione accettano i messaggi in arrivo e li trasformano in un formato che può essere riconosciuto dall'hub Internet.

Esistono due modelli per i gateway di conversione, ovvero la *conversione di protocollo* e la *conversione di identità*.

![Diagramma-modelli di gateway di traduzione](./media/iot-edge-as-gateway/edge-as-gateway-translation.png)

### <a name="protocol-translation"></a>Conversione di protocollo

Nel modello del gateway di conversione del protocollo, solo il gateway IoT Edge ha un'identità con l'hub Internet. Il modulo Translation riceve i messaggi dai dispositivi downstream, li converte in un protocollo supportato e quindi il dispositivo IoT Edge invia i messaggi per conto dei dispositivi downstream. Tutte le informazioni sembrano provenire da un unico dispositivo, il gateway. I dispositivi downstream devono incorporare informazioni di identificazione aggiuntive nei messaggi se le applicazioni cloud vogliono analizzare i dati in base ai singoli dispositivi. Inoltre, le primitive dell'hub Internet, come i gemelli e i metodi diretti, sono supportate solo per il dispositivo gateway, non per i dispositivi downstream. I gateway in questo modello sono considerati *opachi* in contrapposizione ai gateway trasparenti, perché nascondono le identità dei dispositivi downstream.

La conversione del protocollo supporta i dispositivi con vincoli di risorse. Molti dispositivi esistenti generano dati che consentono di ottenere una visione più approfondita dei dati aziendali, ma non sono stati progettati tenendo presente la connettività cloud. I gateway opachi consentono di sbloccare i dati e usarli in una soluzione Internet delle cose.

### <a name="identity-translation"></a>Conversione di identità

Il modello di gateway per la conversione delle identità si basa sulla conversione dei protocolli, ma il gateway IoT Edge fornisce anche un'identità del dispositivo dell'hub Internet per conto dei dispositivi downstream. Il modulo Translation è responsabile della comprensione del protocollo usato dai dispositivi downstream, della fornitura dell'identità e della conversione dei messaggi in primitive dell'hub. I dispositivi downstream vengono visualizzati nell'hub IoT come dispositivi di prima classe con gemelli e metodi. Un utente può interagire con i dispositivi nell'hub IoT senza essere a conoscenza del dispositivo gateway intermedio.

La conversione di identità offre i vantaggi della conversione dei protocolli e consente inoltre la gestibilità completa dei dispositivi downstream dal cloud. Tutti i dispositivi della soluzione IoT appaiono nell'hub IoT indipendentemente dal protocollo usato.

### <a name="device-capabilities-behind-translation-gateways"></a>Funzionalità del dispositivo dietro i gateway di conversione

La tabella seguente illustra il modo in cui le funzionalità dell'hub Internet vengono estese ai dispositivi downstream in entrambi i modelli di gateway di conversione.

| Funzionalità | Conversione di protocollo | Conversione di identità |
| ---------- | -------------------- | -------------------- |
| Identità archiviate nel registro delle identità dell'hub IoT | Solo l'identità del dispositivo gateway | Identità di tutti i dispositivi connessi |
| Dispositivo gemello | Solo il gateway ha dispositivi e moduli gemelli | Ogni dispositivo connesso ha il proprio dispositivo gemello |
| Metodi diretti e messaggi da cloud a dispositivo | Il cloud può indirizzare solo il dispositivo gateway | Il cloud può indirizzare ogni singolo dispositivo connesso |
| [Quote e limitazioni dell'hub IoT](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Si applicano al dispositivo gateway | Si applicano a ogni dispositivo |

Quando si usa il modello di conversione del protocollo, tutti i dispositivi che si connettono tramite tale gateway condividono la stessa coda da cloud a dispositivo, che può contenere al massimo 50 messaggi. Usare questo modello solo quando pochi dispositivi si connettono tramite ogni gateway sul campo e il traffico da cloud a dispositivo è basso.

Il runtime di IoT Edge non include le funzionalità di conversione di protocollo o di identità. Questi modelli richiedono moduli personalizzati o di terze parti spesso specifici dell'hardware e del protocollo utilizzati. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) contiene diversi moduli di conversione del protocollo tra cui scegliere. Per un esempio che usa il modello di conversione delle identità, vedere [Azure IOT Edge Starter Kit LoRaWAN](https://github.com/Azure/iotedge-lorawan-starterkit).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui tre passaggi per configurare un gateway trasparente:

* [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md)
* [Autenticare un dispositivo downstream sull'hub IoT di Azure](how-to-authenticate-downstream-device.md)
* [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md)
