<properties
 pageTitle="Panoramica dell’hub IoT di Azure | Microsoft Azure"
 description="Panoramica del servizio hub IoT di Azure, inclusi la connettività dei dispositivi, i modelli di comunicazione e il modello di comunicazione assistita con i servizi"
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
 ms.date="11/09/2015"
 ms.author="dobett"/>

# Che cos'è l'hub IoT di Azure?

Benvenuti nell’hub IoT di Azure. In questo articolo viene fornita una panoramica dell'hub IoT di Azure e viene descritto il motivo per cui è consigliabile utilizzare questo servizio quando si implementa una soluzione IoT.

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end della soluzione. L'hub IoT di Azure:

- Garantisce un servizio di messaggistica affidabile da dispositivo a cloud e da cloud a dispositivo su vasta scala.
- Rende possibili comunicazioni sicure mediante le credenziali di sicurezza per i singoli dispositivi e il controllo di accesso.
- Comprende librerie di dispositivi per i linguaggi e le piattaforme più diffusi.

![L'hub IoT come gateway cloud][img-architecture]

## Problematiche relative alla connettività dei dispositivi IoT

L’hub IoT e le librerie di dispositivi consentono di risolvere le problematiche relative alla connessione affidabile e sicura dei dispositivi al back-end della soluzione. Dispositivi IoT:

- Sono spesso sistemi incorporati senza operatore umano.
- Possono trovarsi in località remote, dove l'accesso fisico è molto costoso.
- Possono essere raggiungibili solo tramite il back-end della soluzione.
- Possono avere risorse di alimentazione e di elaborazione limitate.
- Possono disporre di una connettività di rete intermittente, lenta o costosa.
- Possono richiedere protocolli di applicazioni proprietari, personalizzati o specifici del settore.
- Possono essere create utilizzando un'ampia gamma di piattaforme hardware e software molto comuni.

Oltre ai requisiti precedenti, le soluzioni IoT devono offrire scalabilità, sicurezza e affidabilità. L’implementazione del set di requisiti di connettività con tecnologie tradizionali come i contenitori Web e i broker di messaggistica è un'attività lunga e complessa.

## Perché usare l'hub IoT di Azure

L'hub IoT di Azure soddisfa i requisiti di connettività dei dispositivi nei modi seguenti:

-   **Autenticazione e connettività sicura per ogni dispositivo**. È possibile effettuare il provisioning di ogni dispositivo con la propria chiave di sicurezza per consentire la connessione all'hub IoT. Il [registro delle identità dell'hub IoT][lnk-devguide-identityregistry] memorizza le identità e le chiavi dei dispositivi di una soluzione. Un back-end della soluzione può inserire singoli dispositivi nella whitelist e nella blacklist, abilitando il controllo completo dell'accesso ai dispositivi.

-   **Un set completo di librerie di dispositivi**. Gli SDK per dispositivi Azure IoT sono disponibili e supportati per svariati linguaggi e piattaforme: C per diverse distribuzioni Linux, Windows e RTOS. Gli SDK per dispositivi Azure IoT supportano inoltre linguaggi gestiti quali C#, Java e JavaScript.

-   **Protocolli IoT ed estensibilità**. Se la soluzione non può usare le librerie di dispositivi, l'hub IoT espone un protocollo pubblico che consente ai dispositivi di usare i protocolli HTTP 1.1 e AMQP 1.0 in modalità nativa. È anche possibile estendere l'hub IoT per fornire il supporto per MQTT versione 3.1.1 con il componente open source [gateway del protocollo Azure IoT][protocol-gateway]. È possibile eseguire il gateway del protocollo Azure IoT nel cloud o in locale ed estenderlo per supportare i protocolli personalizzati.

-   **Scalabilità**. L'hub IoT di Azure è adattabile a milioni di dispositivi simultaneamente connessi e a milioni di eventi al secondo.

Questi vantaggi sono generici per molti modelli di comunicazione. L’Hub IoT consente di implementare i modelli di comunicazione specifici seguenti:

-   **Inserimento da dispositivo a cloud basato su eventi.** L’hub IoT può ricevere in modo affidabile milioni di eventi al secondo dai dispositivi, elaborarli nel percorso ricorrente utilizzando un motore di elaborazione di eventi o memorizzarli per l'analisi dei percorsi a freddo. L'hub IoT conserva i dati degli eventi per un massimo di 7 giorni per garantire un'elaborazione affidabile e assorbire i picchi di carico.

-   **Messaggistica affidabile da cloud a dispositivo (o *comandi*).** Il back-end della soluzione può utilizzare hub IoT per inviare messaggi affidabili (con garanzia di recapito At-Least-Once) ai singoli dispositivi. Ogni messaggio ha la propria impostazione di durata e il back-end può richiedere conferme di recapito e di scadenza per assicurare la visibilità completa del ciclo di vita di un messaggio da cloud a dispositivo. Ciò consente di implementare la logica di business che include le operazioni da eseguire sui dispositivi.

E’ possibile implementare altri modelli comuni, ad esempio il caricamento e il download di file, sfruttando le funzionalità IoT specifiche dell'hub IoT, come la gestione coerente dell'identità dei dispositivi, il monitoraggio della connettività e la scalabilità.

L'articolo [Confronto di Hub IoT e Hub eventi][lnk-compare] descrive le differenze chiave tra questi due servizi e mette in evidenza i vantaggi dell'impiego dell'hub IoT in qualsiasi soluzione.

## Gateway

In una soluzione IoT un gateway è generalmente un [gateway del protocollo][lnk-gateway] distribuito nel cloud o un [gateway campo][lnk-field-gateway] distribuito nei dispositivi locali. Un gateway del protocollo effettua la conversione tra protocolli, ad esempio da MQTT ad AMQP. Un gateway campo fornisce servizi di gestione locale per i dispositivi. Può trattarsi di un dispositivo specializzato o di software in esecuzione su hardware esistente. Entrambi i tipi di gateway fungono da intermediari tra i dispositivi e l'hub IoT.

Un gateway campo differisce da un semplice dispositivo di routing del traffico (ad esempio un dispositivo o un firewall NAT), in quanto in genere esegue un ruolo attivo nella gestione dell'accesso e del flusso di informazioni nella soluzione.

Una soluzione può includere sia gateway di protocollo che gateway campo.

## Come funziona l’Hub IoT?

L'hub IoT di Azure implementa il modello di [comunicazione assistita con i servizi][lnk-service-assisted-pattern] per eseguire la mediazione delle interazioni tra i dispositivi e il back-end della soluzione. L'obiettivo della comunicazione assistita con i servizi è di stabilire percorsi di comunicazione bidirezionali attendibili tra un sistema di controllo (ad esempio, l'hub IoT) e dispositivi per scopi specifici distribuiti in uno spazio fisico non attendibile. Il modello stabilisce i principi seguenti:

- La sicurezza ha la precedenza su tutte le altre funzionalità.
- I dispositivi non accettano informazioni di rete non richieste. Un dispositivo stabilisce tutte le connessioni e le route in modalità solo in uscita. Per ricevere un comando dal back-end, il dispositivo deve avviare periodicamente una connessione per cercare eventuali comandi in sospeso da elaborare.
- I dispositivi in genere stabiliscono una connessione o una route solo con i servizi noti con peering, ad esempio un hub IoT.
- Il percorso di comunicazione tra dispositivo e servizio o tra dispositivo e gateway viene protetto a livello di protocollo dell'applicazione.
- L'autorizzazione e l'autenticazione a livello di sistema devono basarsi sulle identità di ogni dispositivo e le credenziali e le autorizzazioni di accesso devono essere revocabili quasi immediatamente.
- La comunicazione bidirezionale per i dispositivi connessi in modo intermittente a causa di problemi di alimentazione o di connettività può essere facilitata trattenendo i comandi e le notifiche per i dispositivi finché un dispositivo non si connette per riceverli. L'hub IoT conserva le code specifiche dei dispositivi per i comandi inviati.
- I dati di payload delle applicazioni possono essere protetti separatamente per il transito sicuro dai gateway a un determinato servizio.

Il modello di comunicazione assistita con i servizi è stato usato con ottimi risultati nel settore dei dispositivi mobili a scalabilità elevatissima per implementare i servizi di notifica push, ad esempio il [servizio di notifica di Windows][lnk-wns], [Google Cloud Messaging][lnk-google-messaging] e [Apple Push Notification Service][lnk-apple-push].

## Passaggi successivi

Per altre informazioni sull'hub IoT di Azure, vedere questi collegamenti:

* [Iniziare a usare l'hub IoT][lnk-get-started]
* [Connettere il dispositivo][lnk-connect-device]
* [Elaborare i messaggi da dispositivo a cloud][lnk-d2c]

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-d2c]: iot-hub-csharp-csharp-process-d2c.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Comunicazione assistita con i servizi, post di blog di Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-guidance.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9

<!---HONumber=Nov15_HO4-->