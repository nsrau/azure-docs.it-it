---
title: Panoramica di Azure IoT Edge | Microsoft Docs
description: Descrive i concetti principali dell'architettura di Azure IoT Edge, come gateway, moduli e broker.
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2017
ms.author: dobett
ms.openlocfilehash: 11f6375c319c8945b0278003ee08215715f7ac42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-edge-architectural-concepts"></a>Concetti dell'architettura di Azure IoT Edge

Prima di esaminare il codice di esempio o di creare un gateway sul campo usando IoT Edge, è necessario conoscere i concetti principali che supportano l'architettura di IoT Edge.

## <a name="iot-edge-modules"></a>Moduli di IoT Edge

Per compilare un gateway con Azure IoT Edge, è necessario creare e assemblare i *moduli di IoT Edge*. I moduli usano *messaggi* per scambiarsi dati tra loro. Un modulo riceve un messaggio, esegue un'azione su di esso, lo trasforma facoltativamente in un nuovo messaggio e quindi lo pubblica per l'elaborazione da parte di altri moduli. Alcuni moduli potrebbero produrre solo nuovi messaggi e non elaborare mai i messaggi in arrivo. Una catena di moduli crea una pipeline di elaborazione dei dati in cui ogni modulo esegue una trasformazione sui dati in un punto nella pipeline.

![Catena di moduli nel gateway compilati con Azure IoT Edge][1]

IoT Edge contiene i componenti seguenti:

* Moduli già scritti che eseguono funzioni di gateway comuni.
* Le interfacce che uno sviluppatore può usare per scrivere i moduli personalizzati.
* L'infrastruttura necessaria per distribuire ed eseguire un set di moduli.

L'SDK offre un livello di astrazione che consente di compilare gateway da eseguire in vari sistemi operativi e piattaforme.

![Livello di astrazione di Azure IoT Edge][2]

## <a name="messages"></a>Messaggi

Nonostante sia più semplice concettualizzare il funzionamento di un gateway come una trasmissione di messaggi tra moduli, ciò non lo riflette con precisione. I moduli di IoT Edge usano un broker per comunicare tra loro. I moduli pubblicano i messaggi nel broker tramite modelli di messaggistica, ad esempio bus o pubblicazione/sottoscrizione, e consentono al broker di instradare il messaggio ai moduli a esso connessi.

Un modulo usa la funzione **Broker_Publish** per pubblicare un messaggio nel broker. Il broker recapita i messaggi a un modulo richiamando una funzione di callback. Un messaggio è costituito da un set di proprietà chiave/valore e contenuto passato come blocco di memoria.

![Ruolo del broker in Azure IoT Edge][3]

## <a name="message-routing-and-filtering"></a>Routing e filtro dei messaggi

Per indirizzare i messaggi ai moduli di IoT Edge corretti, è possibile procedere in due modi:

* È possibile passare un set di collegamenti al broker in modo che conosca l'origine e il sink di ogni modulo.
* Un modulo può filtrare le proprietà del messaggio.

Un modulo deve agire unicamente sui messaggi a esso destinati. I collegamenti e il filtro dei messaggi creano una pipeline dei messaggi.

## <a name="next-steps"></a>Passaggi successivi

Per vedere l'applicazione di questi concetti in un esempio eseguibile, vedere [Esplorare l'architettura di Azure IoT Edge in Linux][lnk-hello-world].

<!-- Images -->
[1]: media/iot-hub-iot-edge-overview/modules.png
[2]: media/iot-hub-iot-edge-overview/modules_2.png
[3]: media/iot-hub-iot-edge-overview/messages_1.png

<!-- Links -->
[lnk-hello-world]: ./iot-hub-linux-iot-edge-get-started.md
