---
title: Porte e protocolli di comunicazione dell'hub IoT di Azure | Microsoft Docs
description: 'Guida per gli sviluppatori: descrive i protocolli di comunicazione supportati per le comunicazioni da dispositivo a cloud e da cloud a dispositivo e i numeri di porta che devono essere aperti.'
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 3e7f31371a0582a6f4941efbfa0087119278d2d1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729123"
---
# <a name="reference---choose-a-communication-protocol"></a>Informazioni di riferimento: scegliere un protocollo di comunicazione

L'hub IoT consente ai dispositivi di usare i protocolli seguenti per le comunicazioni lato dispositivo:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT su WebSocket
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP su WebSockets
* HTTPS

Per informazioni sulle modalità di supporto dei protocolli alle funzionalità specifiche dell'hub IoT, vedere [Indicazioni sulle comunicazioni da dispositivo a cloud](iot-hub-devguide-d2c-guidance.md) e [Indicazioni sulle comunicazioni da cloud a dispositivo](iot-hub-devguide-c2d-guidance.md).

Nella tabella seguente vengono fornite le indicazioni generali per la scelta del protocollo:

| Protocollo | Quando scegliere questo protocollo |
| --- | --- |
| MQTT <br> MQTT su WebSocket |Viene usato su tutti i dispositivi che non richiedono la connessione di più dispositivi, ognuno con le sue credenziali per dispositivo, sulla stessa connessione TLS. |
| AMQP <br> AMQP su WebSocket |Usare in gateway cloud e sul campo per sfruttare il vantaggio della connessione multiplexing tra dispositivi. |
| HTTPS |Usare per i dispositivi che non supportano altri protocolli. |

Nella scelta del protocollo per le comunicazioni sul lato dispositivo occorre prendere in considerazione quanto segue:

* **Modello da cloud a dispositivo**. HTTPS non offre un modo efficiente per implementare il push del server. Di conseguenza, quando si usa HTTPS i dispositivi eseguono il polling dell'hub IoT per i messaggi da cloud a dispositivo. Questo approccio è inefficiente sia per il dispositivo che per l'hub IoT. In base alle attuali linee guida di HTTPS, ogni dispositivo dovrebbe eseguire il polling almeno ogni 25 minuti. MQTT e AMQP supportano il push del server quando si ricevono messaggi da cloud a dispositivo, consentendo il push immediato dei messaggi dall'hub IoT al dispositivo. Se la latenza di recapito rappresenta un problema, è consigliabile usare il protocollo MQTT o AMQP. Per i dispositivi che si connettono raramente, è possibile usare anche il protocollo HTTPS.

* **Gateway sul campo**. MQTT e HTTPS supportano solo una singola identità del dispositivo (ID dispositivo più credenziali) per ogni connessione TLS. Per questo motivo, questi protocolli non sono supportati per gli scenari gateway di campo che richiedono il multiplexing dei messaggi utilizzando più identità di dispositivo in un pool singolo o un pool di connessioni upstream all'hub IoT.For this reason, these protocols are not supported for [Field gateway scenarios](iot-hub-devguide-endpoints.md#field-gateways) that require multiplexing messages using multiple device identities across a single or a pool of upstream connections to IoT Hub. Tali gateway possono usare un protocollo che supporta più identità di dispositivo per ogni connessione, ad esempio AMQP, per il traffico upstream.

* **Dispositivi con risorse ridotte**. Le raccolte di MQTT e HTTPS hanno un'impronta inferiore rispetto alle raccolte di AMQP. Se quindi il dispositivo ha risorse limitate, ad esempio meno di 1 MB di RAM, questi protocolli possono costituire l'unica implementazione disponibile.

* **Attraversamento rete**. Il protocollo standard AMQP usa la porta 5671, mentre il protocollo MQTT è in ascolto sulla porta 8883. L'uso di queste porte può provocare problemi nelle reti chiuse ai protocolli non HTTPS. In un simile scenario, usare MQTT su WebSockets, AMQP su WebSockets o HTTPS.

* **Dimensioni del payload**. MQTT e AMQP sono protocolli binari, quindi hanno payload più compatti rispetto a HTTPS.

> [!WARNING]
> Quando si usa HTTPS, ogni dispositivo deve eseguire il polling dei messaggi da cloud a dispositivo non più di una volta ogni 25 minuti. In fase di sviluppo, ogni dispositivo può eseguire il polling più frequentemente, se lo si desidera.

## <a name="port-numbers"></a>Numeri di porta

I dispositivi possono comunicare con l'hub IoT in Azure tramite una serie di protocolli. In genere, la scelta del protocollo è determinata dai requisiti specifici della soluzione. Nella tabella seguente sono elencate le porte in uscita che devono essere aperte affinché un dispositivo possa utilizzare un protocollo specifico:

| Protocollo | Porta |
| --- | --- |
| MQTT |8883 |
| MQTT su WebSocket |443 |
| AMQP |5671 |
| AMQP su WebSockets |443 |
| HTTPS |443 |

Dopo aver creato un hub IoT in un'area di Azure, l'hub IoT manterrà lo stesso indirizzo IP per la durata di tale hub IoT. Per mantenere tuttavia la qualità del servizio, se Microsoft sposta l'hub IoT su un'unità di scala diversa, verrà assegnato ad esso un nuovo indirizzo IP.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'implementazione del protocollo MQTT da parte dell'hub IoT, vedere [Comunicare con l'hub IoT tramite il protocollo MQTT](iot-hub-mqtt-support.md).
