---
title: Porte e protocolli di comunicazione dell'hub IoT di Azure | Microsoft Docs
description: 'Guida per gli sviluppatori: descrive i protocolli di comunicazione supportati per le comunicazioni da dispositivo a cloud e da cloud a dispositivo e i numeri di porta che devono essere aperti.'
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: dobett
ms.openlocfilehash: 37602bf78f7a43fb8255ddc0aad21f24095cb43c
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="reference---choose-a-communication-protocol"></a>Informazioni di riferimento: scegliere un protocollo di comunicazione

L'hub IoT consente ai dispositivi di usare i protocolli seguenti per le comunicazioni lato dispositivo:

* [MQTT][lnk-mqtt]
* MQTT su WebSocket
* [AMQP][lnk-amqp]
* AMQP su WebSockets
* HTTPS

Per informazioni sulle modalità di supporto dei protocolli alle funzionalità specifiche dell'hub IoT, vedere [Indicazioni sulle comunicazioni da dispositivo a cloud][lnk-d2c-guidance] e [Indicazioni sulle comunicazioni da cloud a dispositivo][lnk-c2d-guidance].

Nella tabella seguente vengono fornite le indicazioni generali per la scelta del protocollo:

| Protocol | Quando scegliere questo protocollo |
| --- | --- |
| MQTT <br> MQTT su WebSocket |Viene usato su tutti i dispositivi che non richiedono la connessione di più dispositivi, ognuno con le sue credenziali per dispositivo, sulla stessa connessione TLS. |
| AMQP <br> AMQP su WebSocket |Usare in gateway cloud e sul campo per sfruttare il vantaggio della connessione multiplexing tra dispositivi. |
| HTTPS |Usare per i dispositivi che non supportano altri protocolli. |

Nella scelta del protocollo per le comunicazioni sul lato dispositivo occorre prendere in considerazione quanto segue:

* **Modello da cloud a dispositivo**. HTTPS non offre un modo efficiente per implementare il push del server. Di conseguenza, quando si usa HTTPS i dispositivi eseguono il polling dell'hub IoT per i messaggi da cloud a dispositivo. Questo approccio è inefficiente sia per il dispositivo che per l'hub IoT. In base alle attuali linee guida di HTTPS, ogni dispositivo dovrebbe eseguire il polling almeno ogni 25 minuti. MQTT e AMQP supportano il push del server quando si ricevono messaggi da cloud a dispositivo, consentendo il push immediato dei messaggi dall'hub IoT al dispositivo. Se la latenza di recapito rappresenta un problema, è consigliabile usare il protocollo MQTT o AMQP. Per i dispositivi che si connettono raramente, è possibile usare anche il protocollo HTTPS.
* **Gateway sul campo**. Quando si usano i protocolli MQTT e HTTPS, non è possibile connettere più dispositivi, ognuno con le sue credenziali per dispositivo, con la stessa connessione TLS. Questi protocolli non sono ottimali per [scenari di gateway sul campo][lnk-azure-gateway-guidance] che richiedono una connessione TLS tra il gateway sul campo e l'hub IoT per ogni dispositivo connesso.
* **Dispositivi con risorse ridotte**. Le raccolte di MQTT e HTTPS hanno un'impronta inferiore rispetto alle raccolte di AMQP. Di conseguenza, se il dispositivo ha risorse limitate, ad esempio meno di 1 MB di RAM, questi protocolli potrebbero costituire l'unica implementazione disponibile.
* **Attraversamento rete**. Il protocollo standard AMQP usa la porta 5671, mentre il protocollo MQTT è in ascolto sulla porta 8883. L'uso di queste porte può provocare problemi nelle reti chiuse ai protocolli non HTTPS. In un simile scenario, usare MQTT su WebSockets, AMQP su WebSockets o HTTPS.
* **Dimensioni del payload**. MQTT e AMQP sono protocolli binari, quindi hanno payload più compatti rispetto a HTTPS.

> [!WARNING]
> Quando si usa HTTPS, ogni dispositivo deve eseguire il polling dei messaggi da cloud a dispositivo ogni 25 minuti o più. In fase di sviluppo è comunque accettabile eseguire il polling con una frequenza maggiore di 25 minuti.

## <a name="port-numbers"></a>Numeri di porta

I dispositivi possono comunicare con l'hub IoT in Azure tramite una serie di protocolli. In genere, la scelta del protocollo è determinata dai requisiti specifici della soluzione. Nella tabella seguente sono elencate le porte in uscita che devono essere aperte affinché un dispositivo possa utilizzare un protocollo specifico:

| Protocol | Porta |
| --- | --- |
| MQTT |8883 |
| MQTT su WebSocket |443 |
| AMQP |5671 |
| AMQP su WebSockets |443 |
| HTTPS |443 |

Dopo aver creato un hub IoT in un'area di Azure, l'hub IoT manterrà lo stesso indirizzo IP per la durata di tale hub IoT. Per mantenere tuttavia la qualità del servizio, se Microsoft sposta l'hub IoT su un'unità di scala diversa, verrà assegnato ad esso un nuovo indirizzo IP.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'implementazione del protocollo MQTT da parte dell'hub IoT, vedere [Comunicare con l'hub IoT tramite il protocollo MQTT][lnk-mqtt-support].

[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-mqtt-support]: iot-hub-mqtt-support.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
