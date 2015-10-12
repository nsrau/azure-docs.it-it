<properties
 pageTitle="Informazioni sull’hub IoT Azure per professionisti IT | Microsoft Azure"
 description="Informazioni per aiutare i professionisti dell’IT a lavorare con l’hub IoT Azure, ad esempio con i requisiti delle porte e il background della sicurezza."
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/04/2015"
 ms.author="dobett"/>

# Configurazione e gestione dell'accesso all'hub IoT

In questo articolo vengono fornite informazioni che aiutano i professionisti dell’IT a configurare un ambiente dove i dispositivi IoT comunicano con l’hub IoT attraverso un'infrastruttura di rete.

## Connettività di rete

I dispositivi comunicano con l’hub IoT in Azure tramite protocolli HTTPS o AMQP. In genere, la scelta del protocollo è determinata dai requisiti specifici della soluzione. Nella tabella seguente sono elencate le porte in uscita che devono essere aperte affinché un dispositivo possa utilizzare un protocollo specifico:

| Protocollo | Porte |
| -------- | ------- |
| HTTPS | 443 |
| AMQP | 5671 |

Dopo aver creato un hub IoT in un'area di Azure, l'hub manterrà lo stesso indirizzo IP per la durata di tale hub. Tuttavia, in uno scenario di ripristino di emergenza, se Microsoft sposta l'hub IoT su un'unità di scala diversa, verrà assegnato ad esso un nuovo indirizzo IP.

## Hub IoT e sicurezza

Solo i dispositivi registrati con un hub IoT possono comunicare con tale hub IoT. Un dispositivo registrato deve disporre dell’autorizzazione *DeviceConnect*. Un dispositivo si identifica includendo un token che incapsula l'ID univoco del dispositivo in ogni richiesta effettuata e l'hub controlla la validità del token e che il dispositivo non sia bloccato (autorizzazione *DeviceConnect* revocata).

L'accesso ad altri endpoint di gestione in un hub IoT viene controllato anche tramite un set di autorizzazioni: *iothubowner*, *servizio*, *registryRead* e *registryReadWrite*. Qualsiasi applicazione di gestione di client che si connette a un hub IoT deve includere un token con le autorizzazioni appropriate.

## Passaggi successivi

Questo articolo contiene informazioni specifiche per i professionisti dell’IT e gli sviluppatori che eseguono la configurazione dei propri ambienti di sviluppo e test. Seguire i collegamenti seguenti per ulteriori informazioni sul servizio hub IoT Azure:

- [Che cos'è l’hub IoT Azure?][lnk-iothub]
- La sezione ["Protezione" nella Guida per sviluppatori di hub IoT Azure][lnk-devguide] fornisce informazioni aggiuntive sui token e sul sistema di autorizzazioni nell'hub IoT.
- [Gestire l’hub IoT tramite il portale Azure][lnk-manage-portal] descrive come utilizzare il portale di Azure per gestire l'hub IoT.

[lnk-iothub]: iot-hub-what-is-iot-hub.md
[lnk-devguide]: iot-hub-devguide.md#security
[lnk-manage-portal]: iot-hub-manage-through-portal.md

<!---HONumber=Oct15_HO1-->