---
title: Panoramica dell&quot;hub IoT di Azure | Documentazione Microsoft
description: "Panoramica del servizio hub IoT di Azure: informazioni sull&quot;hub IoT, connettività dei dispositivi, Internet delle cose, modelli di comunicazione, gateway e modello di comunicazione assistita con i servizi"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/23/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: e223d0613cd48994315451da87e6b7066585bdb6
ms.openlocfilehash: 42774f83dc4c53dc8f090acb922b5aae6e8b4bed


---
# <a name="what-is-azure-iot-hub"></a>Che cos'è l'hub IoT di Azure?
Benvenuti nell’hub IoT di Azure. Questo articolo offre una panoramica dell'hub IoT di Azure e descrive il motivo per cui è consigliabile usare questo servizio quando si implementa una soluzione Internet delle cose (IoT). L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end della soluzione. L'hub IoT di Azure:

* Offre più opzioni di comunicazione dispositivo a cloud e cloud a dispositivo, inclusi la messaggistica unidirezionale, il trasferimento di file e i metodi di risposta alle richieste.
* Offre un'archiviazione in cui eseguire query per metadati e informazioni di stato sincronizzato.
* Abilita comunicazioni sicure e controllo di accesso tramite chiavi di sicurezza per dispositivo o certificati X.509.
* Fornisce un monitoraggio esteso per gli eventi relativi alla connettività dei dispositivi e alla gestione delle identità dei dispositivi.
* Comprende librerie di dispositivi per i linguaggi e le piattaforme più diffusi.

L'articolo [Confronto tra hub IoT e hub eventi][lnk-compare] descrive le differenze più importanti tra questi due servizi e mette in evidenza i vantaggi dell'uso dell'hub IoT nelle soluzioni IoT.

Vedere [Sicurezza dell'Internet of Things sin dall'inizio][lnk-security-ground-up] per altre informazioni su come Azure e hub IoT proteggono la soluzione IoT.

![Hub IoT di Azure come gateway cloud in una soluzione Internet delle cose][img-architecture]

> [!NOTE]
> Per un'analisi approfondita dell'architettura IoT, vedere [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Architettura di riferimento di Microsoft Azure IoT).
> 
> 

## <a name="iot-device-connectivity-challenges"></a>Problematiche relative alla connettività dei dispositivi IoT
L’hub IoT e le librerie di dispositivi consentono di risolvere le problematiche relative alla connessione affidabile e sicura dei dispositivi al back-end della soluzione. Dispositivi IoT:

* Sono spesso sistemi incorporati senza operatore umano.
* Possono trovarsi in località remote, dove l'accesso fisico è molto costoso.
* Possono essere raggiungibili solo tramite il back-end della soluzione.
* Possono avere risorse di alimentazione e di elaborazione limitate.
* Possono disporre di una connettività di rete intermittente, lenta o costosa.
* Possono richiedere protocolli di applicazioni proprietari, personalizzati o specifici del settore.
* Possono essere create utilizzando un'ampia gamma di piattaforme hardware e software molto comuni.

Oltre ai requisiti precedenti, le soluzioni IoT devono offrire scalabilità, sicurezza e affidabilità. L'implementazione del set di requisiti di connettività con tecnologie tradizionali, come i contenitori Web e i broker di messaggistica, è un'attività lunga e complessa.

## <a name="why-use-azure-iot-hub"></a>Perché usare l'hub IoT di Azure
Oltre a un'ampia gamma di opzioni di comunicazione [dispositivo a cloud][lnk-d2c-guidance] e [cloud a dispositivo][lnk-c2d-guidance], inclusi messaggistica, trasferimenti di file e metodi di risposta alle richieste, l'hub IoT di Azure affronta le situazioni di connettività dei dispositivi nei modi seguenti:

* **Dispositivi gemelli**. Con l'uso dei [dispositivi gemelli ][lnk-twins], è possibile archiviare, sincronizzare ed eseguire query sui metadati del dispositivo e sulle informazioni di stato. I dispositivi gemelli sono documenti JSON nei quali vengono archiviate informazioni sullo stato dei dispositivi (metadati, configurazioni e condizioni). L'hub IoT rende permanente un dispositivo gemello per ogni dispositivo che viene connesso all'hub IoT. 
* **Autenticazione e connettività sicura per ogni dispositivo**. È possibile effettuare il provisioning di ogni dispositivo con la [chiave di sicurezza][lnk-devguide-security] per consentire la connessione all'hub IoT. Il [registro di identità dell'hub IoT][lnk-devguide-identityregistry] archivia le identità e le chiavi dei dispositivi in una soluzione. Un back-end della soluzione può aggiungere singoli dispositivi per negare o consentire il controllo completo dell'accesso ai dispositivi.
* **Monitoraggio delle operazioni di connettività dei dispositivi**. È possibile ricevere i log dettagliati sulle operazioni di gestione delle identità dei dispositivi e sugli eventi di connettività dei dispositivi. Questa funzionalità di monitoraggio permette alla soluzione IoT di identificare i problemi di connettività, ad esempio i dispositivi che provano a connettersi con credenziali non corrette, inviare messaggi troppo spesso o rifiutare tutti i messaggi da cloud a dispositivo.
* **Un set completo di librerie di dispositivi**. Gli [Azure IoT SDK per dispositivi][lnk-device-sdks] sono disponibili e supportati per vari linguaggi e piattaforme: C per diverse distribuzioni Linux, Windows e sistemi operativi in tempo reale. Gli SDK per dispositivi Azure IoT supportano inoltre linguaggi gestiti, ad esempio C#, Java e JavaScript.
* **Protocolli IoT ed estensibilità**. Se la soluzione non può usare le librerie dei dispositivi, l'hub IoT espone un protocollo pubblico che consente ai dispositivi di usare i protocolli MQTT v3.1.1, HTTP 1.1 o AMQP 1.0 in modalità nativa. È anche possibile estendere l'hub IoT per fornire supporto per protocolli personalizzati tramite le operazioni seguenti:
  
  * Creazione di un gateway sul campo con [Azure IoT SDK per gateway][lnk-gateway-sdk] che converte il protocollo personalizzato in uno dei tre protocolli riconosciuti dall'hub IoT. 
  * Personalizzazione del [gateway del protocollo IoT di Azure][protocol-gateway], un componente open source che viene eseguito nel cloud.
* **Scalabilità**. L'hub IoT di Azure è adattabile a milioni di dispositivi simultaneamente connessi e a milioni di eventi al secondo.

## <a name="gateways"></a>Gateway
In una soluzione IoT un gateway è generalmente un [gateway del protocollo][lnk-gateway] distribuito nel cloud o un [gateway sul campo][lnk-field-gateway] distribuito nei dispositivi locali. Un gateway del protocollo effettua la conversione tra protocolli, ad esempio da MQTT ad AMQP. Un gateway sul campo può eseguire analisi perimetrali, prendere decisioni rapide per ridurre la latenza, offrire servizi di gestione dei dispositivi, applicare vincoli sulla sicurezza e la privacy, nonché eseguire la conversione del protocollo. Entrambi i tipi di gateway fungono da intermediari tra i dispositivi e l'hub IoT.

Un gateway sul campo differisce da un semplice dispositivo di routing del traffico, ad esempio un firewall o un dispositivo NAT (Network Address Translation), in quanto in genere esegue un ruolo attivo nella gestione dell'accesso e del flusso di informazioni nella soluzione.

Una soluzione può includere sia gateway di protocollo che gateway campo.

## <a name="how-does-iot-hub-work"></a>Come funziona l’Hub IoT?
L'hub IoT di Azure implementa il modello di [comunicazione assistita con i servizi][lnk-service-assisted-pattern] per eseguire la mediazione delle interazioni tra i dispositivi e il back-end della soluzione. L'obiettivo della comunicazione assistita con i servizi è stabilire percorsi di comunicazione bidirezionali attendibili tra sistemi di controllo,ad esempio l'hub IoT, e dispositivi per scopi specifici distribuiti in uno spazio fisico non attendibile. Il modello stabilisce i principi seguenti:

* La sicurezza ha la precedenza su tutte le altre funzionalità.
* I dispositivi non accettano informazioni di rete non richieste. Un dispositivo stabilisce tutte le connessioni e le route in modalità solo in uscita. Per ricevere un comando dal back-end di soluzione, il dispositivo deve avviare periodicamente una connessione per cercare eventuali comandi in sospeso da elaborare.
* I dispositivi possono stabilire una connessione o una route solo con i servizi noti con peering, ad esempio un hub IoT.
* Il percorso di comunicazione tra dispositivo e servizio o tra dispositivo e gateway viene protetto a livello di protocollo dell'applicazione.
* L'autenticazione e l'autorizzazione a livello di sistema sono basate sulle identità per ogni dispositivo. Rendono le credenziali di accesso e le autorizzazioni revocabili quasi immediatamente.
* La comunicazione bidirezionale per i dispositivi connessi in modo intermittente a causa di problemi di alimentazione o di connettività può essere facilitata trattenendo i comandi e le notifiche per i dispositivi finché un dispositivo non si connette per riceverli. L'hub IoT conserva le code specifiche dei dispositivi per i comandi inviati.
* I dati di payload delle applicazioni possono essere protetti separatamente per il transito sicuro dai gateway a un determinato servizio.

Il modello di comunicazione assistita con i servizi è stato usato nel settore dei dispositivi mobili a scalabilità elevatissima per implementare i servizi di notifica push, ad esempio [Servizi notifica Push Windows][lnk-wns], [Google Cloud Messaging][lnk-google-messaging] e [Apple Push Notification Service][lnk-apple-push].

L'hub IoT è supportato tramite il percorso di peering pubblico di ExpressRoute.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come l'hub IoT di Azure consente la gestione dei dispositivi basata su standard per gestire, configurare e aggiornare i dispositivi in modalità remota, vedere [Panoramica della gestione dei dispositivi con l'hub IoT][lnk-device-management].

Per implementare le applicazioni client su una vasta gamma di piattaforme hardware e sistemi operativi per dispositivi, è possibile usare Azure IoT SDK per dispositivi. Gli SDK per dispositivi includono librerie che facilitano l'invio di dati di telemetria a un hub IoT e la ricezione di messaggi da cloud a dispositivo. Quando si usano gli SDK dei dispositivi, è possibile scegliere tra diversi protocolli di rete per comunicare con l'hub IoT. Per altre informazioni, vedere [Informazioni sugli SDK per dispositivi][lnk-device-sdks].

Per iniziare a scrivere codice ed eseguire alcuni esempi, vedere l'esercitazione [Introduzione all'hub IoT][lnk-get-started].

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Comunicazione assistita con i servizi, post di blog di Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-security-ground-up]: iot-hub-security-ground-up.md


<!--HONumber=Dec16_HO1-->


