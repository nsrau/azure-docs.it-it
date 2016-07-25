<properties
 pageTitle="Informazioni sull’hub IoT Azure per professionisti IT | Microsoft Azure"
 description="Informazioni per aiutare i professionisti dell’IT a lavorare con l’hub IoT Azure, ad esempio con i requisiti delle porte e il background della sicurezza."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="dobett"/>

# Configurazione e gestione dell'accesso all'hub IoT

In questo articolo vengono fornite informazioni che aiutano i professionisti dell’IT a configurare un ambiente dove i dispositivi IoT comunicano con l’hub IoT attraverso un'infrastruttura di rete.

## Connettività di rete

I dispositivi possono comunicare con l'hub IoT in Azure tramite una varietà di protocolli. In genere, la scelta del protocollo è determinata dai requisiti specifici della soluzione. Nella tabella seguente sono elencate le porte in uscita che devono essere aperte affinché un dispositivo possa utilizzare un protocollo specifico:

| Protocollo | Porte |
| -------- | ------- |
| HTTPS | 443 |
| AMQP | 5671 |
| AMQP su WebSockets | 443 |
| MQTT | 8883 |
| LWM2M (gestione dei dispositivi) | 5684 |

Dopo aver creato un hub IoT in un'area di Azure, l'hub manterrà lo stesso indirizzo IP per la durata di tale hub. Tuttavia, per mantenere la qualità del servizio, se Microsoft sposta l'hub IoT su un'unità di scala diversa, verrà assegnato ad esso un nuovo indirizzo IP.

## Hub IoT e sicurezza

Solo i dispositivi registrati con un hub IoT possono comunicare con tale hub IoT. Un dispositivo registrato deve disporre dell’autorizzazione *DeviceConnect*. Un dispositivo si identifica includendo un token che incapsula l'ID univoco del dispositivo in ogni richiesta effettuata e l'hub controlla la validità del token e che il dispositivo non sia bloccato (autorizzazione *DeviceConnect* revocata).

L'accesso ad altri endpoint di gestione in un hub IoT viene controllato anche tramite un set di autorizzazioni: *iothubowner*, *servizio*, *registryRead* e *registryReadWrite*. Qualsiasi applicazione di gestione di client che si connette a un hub IoT deve includere un token con le autorizzazioni appropriate.

## Passaggi successivi

Questo articolo contiene informazioni specifiche per i professionisti dell’IT e gli sviluppatori che eseguono la configurazione dei propri ambienti di sviluppo e test. La [sezione sulla protezione nella Guida per sviluppatori di hub IoT Azure][lnk-devguide] fornisce informazioni aggiuntive sui token e sul sistema di autorizzazioni nell'hub IoT.

Per esplorare ulteriormente le funzionalità dell'hub IoT, vedere:

- [Progettare una soluzione][lnk-design]
- [Guida per sviluppatori][lnk-devguide]
- [Informazioni sulla gestione dei dispositivi tramite l'interfaccia utente di esempio][lnk-dmui]
- [Simulazione di un dispositivo con Gateway SDK][lnk-gateway]

[lnk-iothub]: iot-hub-what-is-iot-hub.md
[lnk-devguide]: iot-hub-devguide.md#security
[lnk-manage-portal]: iot-hub-manage-through-portal.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

<!---HONumber=AcomDC_0713_2016-->