<properties
 pageTitle="Panoramica dell'hub IoT di Azure | Microsoft Azure"
 description="Panoramica del servizio hub IoT di Azure: informazioni sull'hub IoT, connettività dei dispositivi, Internet delle cose, modelli di comunicazione e modello di comunicazione assistita con i servizi"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="06/06/2016"
 ms.author="dobett"/>

# Che cos'è l'hub IoT di Azure?

Benvenuti nell’hub IoT di Azure. Questo articolo fornisce una panoramica dell'hub IoT di Azure e descrive il motivo per cui è consigliabile usare questo servizio quando si implementa una soluzione di Internet delle cose (IoT).

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end della soluzione. L'hub IoT di Azure:

- Garantisce un servizio di messaggistica affidabile da dispositivo a cloud e da cloud a dispositivo su vasta scala.
- Rende possibili comunicazioni sicure mediante le credenziali di sicurezza per i singoli dispositivi e il controllo di accesso.
- Fornisce un monitoraggio esteso per gli eventi relativi alla connettività dei dispositivi e alla gestione delle identità dei dispositivi.
- Comprende librerie di dispositivi per i linguaggi e le piattaforme più diffusi.

L'articolo [Confronto di Hub IoT e Hub eventi][lnk-compare] descrive le differenze chiave tra questi due servizi e mette in evidenza i vantaggi dell'impiego dell'hub IoT in qualsiasi soluzione.

![Hub IoT di Azure come gateway cloud in una soluzione Internet delle cose][img-architecture]  

> [AZURE.NOTE] Per un'analisi approfondita dell'architettura IoT, vedere l'[architettura di riferimento di Microsoft Azure IoT][lnk-refarch].

## Problematiche relative alla connettività dei dispositivi IoT

L’hub IoT e le librerie di dispositivi consentono di risolvere le problematiche relative alla connessione affidabile e sicura dei dispositivi al back-end della soluzione. Dispositivi IoT:

- Sono spesso sistemi incorporati senza operatore umano.
- Possono trovarsi in località remote, dove l'accesso fisico è molto costoso.
- Possono essere raggiungibili solo tramite il back-end della soluzione.
- Possono avere risorse di alimentazione e di elaborazione limitate.
- Possono disporre di una connettività di rete intermittente, lenta o costosa.
- Possono richiedere protocolli di applicazioni proprietari, personalizzati o specifici del settore.
- Possono essere create utilizzando un'ampia gamma di piattaforme hardware e software molto comuni.

Oltre ai requisiti precedenti, le soluzioni IoT devono offrire scalabilità, sicurezza e affidabilità. L'implementazione del set di requisiti di connettività con tecnologie tradizionali, come i contenitori Web e i broker di messaggistica, è un'attività lunga e complessa.

## Perché usare l'hub IoT di Azure

L'hub IoT di Azure soddisfa i requisiti di connettività dei dispositivi nei modi seguenti:

-   **Autenticazione e connettività sicura per ogni dispositivo**. È possibile effettuare il provisioning di ogni dispositivo con la propria [chiave di sicurezza][lnk-devguide-security] per consentire la connessione all'hub IoT. Il [registro delle identità dell'hub IoT][lnk-devguide-identityregistry] memorizza le identità e le chiavi dei dispositivi di una soluzione. Un back-end della soluzione può aggiungere singoli dispositivi per negare o consentire il controllo completo dell'accesso ai dispositivi.

-   **Monitoraggio delle operazioni di connettività dei dispositivi**. È possibile ricevere i log dettagliati sulle operazioni di gestione delle identità dei dispositivi e sugli eventi di connettività dei dispositivi. Questa funzionalità di monitoraggio permette alla soluzione IoT di identificare facilmente i problemi di connettività, ad esempio i dispositivi che provano a connettersi con credenziali non corrette, inviare messaggi troppo spesso o rifiutare tutti i messaggi da cloud a dispositivo.

-   **Un set completo di librerie di dispositivi**. Gli [SDK per dispositivi Azure IoT][lnk-device-sdks] sono disponibili e supportati per vari linguaggi e piattaforme: C per diverse distribuzioni Linux, Windows e sistemi operativi in tempo reale. Gli SDK per dispositivi Azure IoT supportano inoltre linguaggi gestiti, ad esempio C#, Java e JavaScript.

-   **Protocolli IoT ed estensibilità**. Se la soluzione non può usare le librerie dei dispositivi, l'hub IoT espone un protocollo pubblico che consente ai dispositivi di usare i protocolli MQTT v3.1.1, HTTP 1.1 o AMQP 1.0 in modalità nativa. È anche possibile estendere l'hub IoT per fornire supporto per protocolli personalizzati tramite le operazioni seguenti:

    - Creazione di un gateway sul campo con [Azure IoT Gateway SDK][lnk-gateway-sdk] che converte il protocollo personalizzato in uno dei tre protocolli riconosciuti dall'hub IoT.
    - Personalizzazione del [gateway del protocollo IoT di Azure][protocol-gateway], un componente open source che viene eseguito nel cloud.

-   **Scalabilità**. L'hub IoT di Azure è adattabile a milioni di dispositivi simultaneamente connessi e a milioni di eventi al secondo.

Questi vantaggi sono generici per molti modelli di comunicazione. L’Hub IoT consente di implementare i modelli di comunicazione specifici seguenti:

-   **Inserimento da dispositivo a cloud basato su eventi.** L'hub IoT può ricevere in modo affidabile milioni di eventi al secondo dai dispositivi ed elaborarli nel percorso attivo tramite un motore di processore di eventi oppure archiviarli nel percorso non attivo per l'analisi. L'hub IoT conserva i dati degli eventi per un massimo di sette giorni per garantire un'elaborazione affidabile e assorbire i picchi di carico.

-   **Messaggistica affidabile da cloud a dispositivo (o *comandi*).** Il back-end della soluzione può utilizzare hub IoT per inviare messaggi affidabili (con garanzia di recapito At-Least-Once) ai singoli dispositivi. Ogni messaggio ha la propria impostazione di durata e il back-end può richiedere conferme di recapito e di scadenza per assicurare la visibilità completa del ciclo di vita di un messaggio da cloud a dispositivo. A questo punto, è possibile implementare la logica di business che include le operazioni da eseguire sui dispositivi.

-   **Caricare i file e i dati del sensore memorizzati nella cache nel cloud.** I dispositivi possono caricare file in Archiviazione di Azure usando gli URI di firma di accesso condiviso gestiti dall'hub IoT. L'hub IoT può generare le notifiche all'arrivo dei file nel cloud per abilitare il back-end alla loro elaborazione.

## Gateway

In una soluzione IoT un gateway è generalmente un [gateway del protocollo][lnk-gateway] distribuito nel cloud o un [gateway sul campo][lnk-field-gateway] distribuito nei dispositivi locali. Un gateway del protocollo effettua la conversione tra protocolli, ad esempio da MQTT ad AMQP. Un gateway sul campo può eseguire analisi perimetrali, prendere decisioni rapide per ridurre la latenza, offrire servizi di gestione dei dispositivi, applicare vincoli sulla sicurezza e la privacy, nonché eseguire la conversione del protocollo. Entrambi i tipi di gateway fungono da intermediari tra i dispositivi e l'hub IoT.

Un gateway sul campo differisce da un semplice dispositivo di routing del traffico, ad esempio un firewall o un dispositivo NAT (Network Address Translation), in quanto in genere esegue un ruolo attivo nella gestione dell'accesso e del flusso di informazioni nella soluzione.

Una soluzione può includere sia gateway di protocollo che gateway campo.

## Come funziona l’Hub IoT?

L'hub IoT di Azure implementa il modello di [comunicazione assistita con i servizi][lnk-service-assisted-pattern] per eseguire la mediazione delle interazioni tra i dispositivi e il back-end della soluzione. L'obiettivo della comunicazione assistita con i servizi è stabilire percorsi di comunicazione bidirezionali attendibili tra sistemi di controllo,ad esempio l'hub IoT, e dispositivi per scopi specifici distribuiti in uno spazio fisico non attendibile. Il modello stabilisce i principi seguenti:

- La sicurezza ha la precedenza su tutte le altre funzionalità.
- I dispositivi non accettano informazioni di rete non richieste. Un dispositivo stabilisce tutte le connessioni e le route in modalità solo in uscita. Per ricevere un comando dal back-end, il dispositivo deve avviare periodicamente una connessione per cercare eventuali comandi in sospeso da elaborare.
- I dispositivi possono stabilire una connessione o una route solo con i servizi noti con peering, ad esempio un hub IoT.
- Il percorso di comunicazione tra dispositivo e servizio o tra dispositivo e gateway viene protetto a livello di protocollo dell'applicazione.
- L'autenticazione e l'autorizzazione a livello di sistema sono basate sulle identità per ogni dispositivo. Rendono le credenziali di accesso e le autorizzazioni revocabili quasi immediatamente.
- La comunicazione bidirezionale per i dispositivi connessi in modo intermittente a causa di problemi di alimentazione o di connettività può essere facilitata trattenendo i comandi e le notifiche per i dispositivi finché un dispositivo non si connette per riceverli. L'hub IoT conserva le code specifiche dei dispositivi per i comandi inviati.
- I dati di payload delle applicazioni possono essere protetti separatamente per il transito sicuro dai gateway a un determinato servizio.

Il modello di comunicazione assistita con i servizi è stato usato con ottimi risultati nel settore dei dispositivi mobili a scalabilità elevatissima per implementare i servizi di notifica push, ad esempio [Servizi notifica Push Windows][lnk-wns], [Google Cloud Messaging][lnk-google-messaging] e [Apple Push Notification Service][lnk-apple-push].

## Passaggi successivi

Per informazioni su come Azure IoT Hub consente la gestione dei dispositivi IoT basati su standard per gestire, configurare e aggiornare i dispositivi in modalità remota, vedere [Panoramica di gestione dei dispositivi dell'hub IoT di Azure][lnk-device-management].

Per implementare le applicazioni client su una vasta gamma di piattaforme hardware e sistemi operativi per dispositivi, è possibile usare gli SDK per dispositivi IoT. GLI SDK per dispositivi IoT includono librerie che facilitano l'invio di dati di telemetria a un hub IoT e la ricezione di comandi da cloud a dispositivo. Quando si usano gli SDK, è possibile scegliere tra diversi protocolli di rete per comunicare con l'hub IoT. Per ulteriori informazioni, vedere [Informazioni sugli SDK del dispositivo][lnk-device-sdks].

Per iniziare a scrivere codice ed eseguire alcuni esempi, vedere l'esercitazione [Introduzione all'hub IoT][lnk-get-started].

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
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md

<!---HONumber=AcomDC_0824_2016-->