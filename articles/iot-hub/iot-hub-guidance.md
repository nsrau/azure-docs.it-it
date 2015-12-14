<properties
 pageTitle="Guida alla soluzione hub IoT | Microsoft Azure"
 description="Argomenti relativi a gateway, provisioning dei dispositivi e autenticazione per lo sviluppo di soluzioni IoT usando l'hub IoT di Azure."
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
 ms.date="11/10/2015"
 ms.author="dobett"/>

# Progettare la soluzione

Questo articolo offre indicazioni su come progettare le funzionalità seguenti nella propria soluzione IoT:

- Provisioning di dispositivi
- Gateway sul campo
- Autenticazione del dispositivo

## Provisioning di dispositivi

Le soluzioni IoT archiviano dati sui singoli dispositivi, ad esempio:

- Chiavi di identità e di autenticazione dei dispositivi
- Tipo e versione dell'hardware dei dispositivi
- Stato dei dispositivi
- Versioni e funzionalità del software
- Cronologia dei comandi dei dispositivi

I dati sui dispositivi archiviati da una determinata soluzione IoT dipendono dai requisiti specifici di tale soluzione, ma come minimo una soluzione deve archiviare le chiavi di identità e di autenticazione dei dispositivi. L'hub IoT include un [registro delle identità][lnk-devguide-identityregistry] in grado di memorizzare i valori per ogni dispositivo, ad esempio ID, chiavi di autenticazione e codici di stato. Una soluzione può usare altri servizi di Azure, ad esempio tabelle, BLOB o DocumentDB per memorizzare eventuali dati aggiuntivi sui dispositivi.

Il *provisioning dei dispositivi* è il processo di aggiunta dei dati iniziali dei dispositivi agli archivi nella soluzione. Per attivare un nuovo dispositivo per la connessione all'hub è necessario aggiungere un nuovo ID dispositivo e le relative chiavi al [registro delle identità dell'hub IoT][lnk-devguide-identityregistry]. Come parte del processo di provisioning, potrebbe essere necessario inizializzare i dati specifici del dispositivo in altri archivi di soluzioni.

L'articolo relativo alla [guida alla gestione dei dispositivi dell'hub IoT][lnk-device-management] descrive alcune strategie comuni per il provisioning dei dispositivi. Le [API del registro delle identità dell'hub IoT][lnk-devguide-identityregistry] consentono di integrare l'hub IoT nel processo di provisioning.

## Gateway sul campo

In una soluzione IoT è presente un *gateway sul campo* tra i dispositivi e l'hub IoT e in genere si trova vicino ai dispositivi. I dispositivi comunicano direttamente con il gateway sul campo usando un protocollo supportato dai dispositivi e il gateway campo comunica con l'hub IoT usando un protocollo supportato dall'hub IoT. Un gateway campo può essere un dispositivo o un software autonomo specializzato, eseguito su un componente hardware esistente.

Un gateway sul campo differisce da un semplice dispositivo di routing del traffico (ad esempio un dispositivo o un firewall NAT), in quanto in genere esegue un ruolo attivo nella gestione dell'accesso e del flusso di informazioni nella soluzione. Ad esempio, un gateway sul campo è in grado di:

- Gestire i dispositivi locali. Ad esempio, un gateway sul campo può eseguire l'elaborazione delle regole degli eventi e inviare comandi ai dispositivi in risposta a dati di telemetria specifici.
- Filtrare o aggregare i dati di telemetria prima che vengano inoltrati all'hub IoT. Questo può ridurre la quantità di dati inviati all'hub IoT e potenzialmente ridurre i costi nella soluzione.
- Semplificare il provisioning dei dispositivi.
- Trasformare i dati di telemetria per facilitare l'elaborazione nel back-end della soluzione.
- Eseguire la conversione del protocollo per consentire ai dispositivi di comunicare con l'hub IoT anche quando non usano i protocolli di trasporto supportati dall'hub IoT.

> [AZURE.NOTE]Quando si distribuisce un gateway sul campo locale ai dispositivi, in alcuni scenari si potrebbe distribuire un [gateway del protocollo][lnk-gateway] nel cloud.

### Tipi di gateway sul campo

Un gateway sul campo può essere *trasparente* o *opaco*:

| &nbsp; | Trasparente | Opaco |
|--------|-------------|--------|
| Identità archiviate nel registro delle identità dell'hub IoT | Tutti i dispositivi connessi | Solo l'identità del gateway sul campo |
| L'hub IoT può offrire funzionalità di [anti-spoofing per le identità dei dispositivi][lnk-devguide-antispoofing] | Sì | No |
| [Quote e limitazioni][lnk-throttles-quotas] | Applicare a ogni dispositivo | Applicare al gateway sul campo |

### Altre considerazioni

È possibile usare [gli SDK dei dispositivi IoT di Azure][lnk-device-sdks] per implementare un gateway sul campo. Alcuni SDK dei dispositivi offrono funzionalità specifiche che consentono di implementare un gateway sul campo, ad esempio la possibilità di eseguire il multiplex della comunicazione da più dispositivi nella stessa connessione all'hub IoT. Come spiegato nell'articolo relativo alla [scelta del protocollo di comunicazione della guida per gli sviluppatori dell'hub IoT][lnk-devguide-protocol], è consigliabile evitare di usare HTTP/1 come protocollo di trasporto per un gateway sul campo.

## Autenticazione personalizzata del dispositivo

È possibile usare il [registro delle identità dei dispositivi][lnk-devguide-identityregistry] dell'hub IoT di Azure per configurare il controllo dell'accesso e le credenziali di sicurezza per ogni dispositivo. Tuttavia, se una soluzione IoT dispone già di un investimento significativo in un registro personalizzato delle identità dei dispositivi e/o in uno schema di autenticazione, è possibile integrare l'infrastruttura esistente con l'hub IoT creando un *servizio token*. In questo modo, è possibile usare altre funzionalità IoT nella soluzione.

Un servizio token è un servizio cloud personalizzato, che usa *criteri di accesso condivisi* dell'hub IoT con autorizzazioni **DeviceConnect** per creare token *con ambito dispositivo* che consentono a un dispositivo di collegarsi all'hub IoT.

  ![][img-tokenservice]

Di seguito sono riportati i passaggi principali del modello del servizio token:

1. Creare i [criteri di accesso condivisi dell'hub IoT][lnk-devguide-security] con autorizzazioni **DeviceConnect** per l'hub IoT. È possibile creare questi criteri nel [portale di Azure][lnk-portal] o a livello di programmazione. Il servizio token usa questi criteri per firmare i token creati.
2. Quando un dispositivo deve accedere all'hub IoT, richiede un token firmato dal servizio token. Il dispositivo può eseguire l'autenticazione con il registro delle identità dei dispositivi personalizzato/lo schema di autenticazione per determinare l'identità del dispositivo usata dal servizio token per creare il token.
3. Il servizio token restituisce un token, creato in base alla sezione relativa alla [sicurezza della guida per gli sviluppatori dell'hub IoT][lnk-devguide-security], usando `/devices/{deviceId}` come `resourceURI`, con `deviceId` come dispositivo da autenticare. Il servizio token usa i criteri di accesso condivisi per costruire il token.
4. Il dispositivo usa il token direttamente con l'hub IoT.

> [AZURE.NOTE]È possibile usare la classe .NET [SharedAccessSignatureBuilder][lnk-dotnet-sas] o la classe Java [IotHubServiceSasToken][lnk-java-sas] per creare un token nel servizio token.

Il servizio token può impostare la scadenza del token, in base alle esigenze. Alla scadenza del token, l'hub IoT interrompe la connessione del dispositivo e il dispositivo deve richiedere un nuovo token dal servizio token. Un intervallo di scadenza breve aumenterà il carico sia per il dispositivo che per il servizio token.

Perché un dispositivo si connetta all'hub, è comunque necessario aggiungerlo al registro delle identità dei dispositivi dell'hub IoT anche se il dispositivo usa un token e non una chiave di dispositivo per la connessione. Di conseguenza, è possibile continuare a usare il controllo dell'accesso per ogni dispositivo abilitando o disabilitando le identità dei dispositivi nel [registro delle identità dell'hub IoT][lnk-devguide-identityregistry] quando il dispositivo esegue l'autenticazione con un token. Questo riduce il rischio che vengano usati token con intervalli di scadenza prolungati.

### Confronto con un gateway personalizzato

Il modello del servizio token è il metodo consigliato per implementare un registro delle identità personalizzato/uno schema di autenticazione per l'hub IoT, perché consente all'hub IoT di gestire la maggior parte del traffico della soluzione. Tuttavia, in alcuni casi lo schema di autenticazione personalizzato è talmente legato al protocollo (ad esempio [TLS-PSK][lnk-tls-psk]) che è necessario un servizio che elabora tutto il traffico (*gateway personalizzato*). Per altre informazioni, vedere l'articolo relativo al [gateway del protocollo][lnk-gateway].

## Passaggi successivi

Per altre informazioni sull'hub IoT di Azure, vedere questi collegamenti:

- [Introduzione all'hub IoT (esercitazione)][lnk-get-started]
- [Che cos'è l'hub IoT Azure?][lnk-what-is-hub]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-device-management]: iot-hub-device-management.md

[lnk-device-sdks]: iot-hub-sdks-summary.md
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-gateway]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-what-is-hub]: iot-hub-what-is-iot-hub.md
[lnk-portal]: https://portal.azure.com
[lnk-throttles-quotas]: ../azure-subscription-service-limits.md/#iot-hub-limits
[lnk-devguide-antispoofing]: iot-hub-devguide.md#antispoofing
[lnk-devguide-protocol]: iot-hub-devguide.md#amqpvshttp
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html

<!---HONumber=AcomDC_1203_2015-->