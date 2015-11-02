<properties
 pageTitle="Argomenti delle linee guida per l'utilizzo di IoT Hub Azure | Microsoft Azure"
 description="Una raccolta di argomenti con indicazioni aggiuntive per le soluzioni di sviluppo che usano l'hub IoT di Azure."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# Linee guida sull'hub IoT di Azure

## Provisioning di dispositivi

Le soluzioni IoT gestiscono le informazioni sui dispositivi in molti sistemi e archivi diversi. Il [registro di identità dell'hub IoT][IoT Hub Developer Guide - identity registry] è uno degli archivi che gestiscono le informazioni sul dispositivo specifiche dell'applicazione. Si definisce *provisioning* il processo di creazione delle informazioni sul dispositivo richieste in tutti questi sistemi.

Per il provisioning di dispositivi sono necessari numerosi requisiti e strategie. Per altre informazioni, vedere le [linee guida sulla gestione dei dispositivi dell'hub IoT]. Il [registro di identità dell'hub IoT][IoT Hub Developer Guide - identity registry] fornisce le API necessarie per l'integrazione dell'hub IoT nel processo di provisioning.

## Gateway sul campo

Un gateway sul campo è un dispositivo specializzato o un software di uso generico che agisce come componente che abilita la comunicazione e potenzialmente come sistema di controllo locale dei dispositivi e hub di elaborazione dei dati dei dispositivi. Un gateway sul campo può eseguire funzioni di controllo ed elaborazione locali verso i dispositivi. Può inoltre filtrare o aggregare la telemetria del dispositivo e quindi ridurre la quantità di dati trasferiti al back-end.

L'ambito di un gateway sul campo include il gateway stesso e tutti i dispositivi collegati. Come implica il nome, i gateway sul campo operano all'esterno della struttura di elaborazione dei dati e sono solitamente associati alla posizione. Un gateway sul campo è diverso da un semplice router di traffico, in quanto ha un ruolo attivo nella gestione dell'accesso e del flusso di informazioni. Per questo motivo è un'entità indirizzata all'applicazione e un terminale di sessione o connessione di rete, ad esempio i gateway in questo contesto possono essere utili per il provisioning dei dispositivi, la trasformazione dei dati, la conversione dei protocolli e l'elaborazione delle regole eventi. I dispositivi o i firewall NAT, al contrario, non sono idonei come gateway sul campo perché non sono terminali di sessione o di connessione espliciti, ma piuttosto inoltrano (o negano) le connessioni o le sessioni che li attraversano.

### Gateway sul campo opachi o trasparenti

Rispetto alle identità dei dispositivi, i gateway sul campo sono detti *trasparenti* se altri dispositivi nell'ambito hanno voci di identità del dispositivo nel registro di identità dell'hub IoT. Sono detti *opachi* se la sola identità nel registro di identità dell'hub IoT è l'identità del gateway sul campo.

È importante notare che l'uso di gateway *opachi* impedisce all'hub IoT di fornire funzionalità di [antispoofing delle identità del dispositivo][IoT Hub Developer Guide - Anti-spoofing]. Inoltre, poiché tutti i dispositivi nell'ambito del gateway sul campo sono rappresentati come un singolo dispositivo nell'hub IoT, sono soggetti a limitazioni e quote come dispositivo singolo.

### Altre considerazioni

Quando si implementa un gateway sul campo, è possibile usare [gli SDK per dispositivi Azure IoT][]. Alcuni SDK offrono funzionalità specifiche del gateway sul campo, ad esempio funzionalità per il multiplexing delle comunicazioni di più dispositivi sulla stessa connessione all'hub IoT. Come descritto nella [guida per gli sviluppatori dell'hub IoT - Scelta del protocollo di comunicazione][], è consigliabile evitare di usare HTTP/1 come protocollo di trasporto per i gateway sul campo.

## Uso di schemi/servizi di autenticazione dei dispositivi personalizzati

L'hub IoT consente di configurare il controllo di accesso e le credenziali di sicurezza per ogni dispositivo usando il [registro di identità del dispositivo][IoT Hub Developer Guide - identity registry].

Se una soluzione IoT dispone già di un investimento significativo in termini di schema di autenticazione e/o registro di identità dei dispositivi personalizzati, può comunque sfruttare i vantaggi offerti dalle altre funzionalità dell'hub IoT creando un *servizio token* per l'hub IoT.

Il servizio token è un servizio cloud distribuito automaticamente che usa *criteri di accesso condiviso* dell'hub IoT con autorizzazioni **DeviceConnect** per creare token *con ambito dispositivo*.

  ![][img-tokenservice]

Ecco i passaggi principali del modello di servizio token.

1. Creare i [criteri di accesso condiviso dell'hub IoT][IoT Hub Developer Guide - Security] con autorizzazioni **DeviceConnect** per l'hub IoT. I criteri saranno usati dal servizio token per firmare i token.
2. Quando un dispositivo deve accedere all'hub IoT, richiede un token firmato al servizio token. Il dispositivo può usare lo schema di autenticazione o il registro delle identità dei dispositivi personalizzato.
3. Il servizio token restituisce un token, creato in base alla [guida per gli sviluppatori dell'hub IoT - Sicurezza][], usando `/devices/{deviceId}` come `resourceURI`, con `deviceId` come dispositivo da autenticare.
4. Il dispositivo usa il token direttamente con l'hub IoT.

Il servizio token può impostare la scadenza del token, in base alle esigenze. Alla scadenza, l'hub IoT interrompe la connessione e il dispositivo deve richiedere un nuovo token al servizio token. Chiaramente, un intervallo di scadenza breve aumenterà il carico sia nel dispositivo che nel servizio token.

È utile specificare che per consentire al dispositivo di connettersi, l'identità del dispositivo deve essere creata nell'hub IoT. Ciò significa che il controllo di accesso per ogni dispositivo, disabilitando le identità del dispositivo in base alla [guida per gli sviluppatori dell'hub IoT - Registro delle identità][], è ancora operativo, anche se il dispositivo si autentica con un token. Questo approccio contribuisce ad attenuare gli effetti dell'esistenza di token di lunga durata.

### Confronto con un gateway personalizzato

Il modello di servizio token è il metodo consigliato per implementare uno schema di autenticazione/registro di identità personalizzato con l'hub IoT, perché consente all'hub IoT di gestire la maggior parte del traffico delle soluzioni. In alcuni casi lo schema di autenticazione personalizzato è talmente intrecciato con il protocollo, ad esempio [TLS-PSK][], da richiedere un servizio che elabora tutto il traffico (*gateway personalizzato*). Per altre informazioni, vedere l'argomento relativo al [gateway del protocollo][].

## Passaggi successivi

Per altre informazioni sull'hub IoT di Azure, vedere questi collegamenti:

- [Introduzione all’hub IoT (esercitazione)][lnk-get-started]
- [Che cos'è l’hub IoT Azure?][]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[IoT Hub Developer Guide - identity registry]: iot-hub-devguide.md#identityregistry
[guida per gli sviluppatori dell'hub IoT - Registro delle identità]: iot-hub-devguide.md#identityregistry
[linee guida sulla gestione dei dispositivi dell'hub IoT]: iot-hub-device-management.md
[IoT Hub Developer Guide - Anti-spoofing]: iot-hub-devguide.md#antispoofing
[gli SDK per dispositivi Azure IoT]: iot-hub-sdks-summary.md
[guida per gli sviluppatori dell'hub IoT - Scelta del protocollo di comunicazione]: iot-hub-devguide.md#amqpvshttp
[IoT Hub Developer Guide - Security]: iot-hub-devguide.md#security
[guida per gli sviluppatori dell'hub IoT - Sicurezza]: iot-hub-devguide.md#security
[TLS-PSK]: https://tools.ietf.org/html/rfc4279
[gateway del protocollo]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Che cos'è l’hub IoT Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=Oct15_HO4-->