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
 ms.date="04/29/2016"
 ms.author="dobett"/>

# Progettare la soluzione

Questo articolo offre indicazioni su come progettare le funzionalità seguenti nella soluzione di Internet delle cose (IoT):

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

I dati del dispositivo archiviati da una soluzione IoT dipendono dai requisiti specifici di tale soluzione. Tuttavia, come minimo, una soluzione deve archiviare identità di dispositivo e chiavi di autenticazione. L'hub IoT di Azure include un [registro delle identità][lnk-devguide-identityregistry] in grado di memorizzare i valori per ogni dispositivo, ad esempio ID, chiavi di autenticazione e codici di stato. Una soluzione può usare altri servizi di Azure, ad esempio tabelle, BLOB o Azure DocumentDB per memorizzare eventuali dati aggiuntivi sui dispositivi.

Il *provisioning dei dispositivi* è il processo di aggiunta dei dati iniziali dei dispositivi agli archivi nella soluzione. Per attivare un nuovo dispositivo per la connessione all'hub è necessario aggiungere un nuovo ID dispositivo e le relative chiavi al [registro delle identità dell'hub IoT][lnk-devguide-identityregistry]. Come parte del processo di provisioning, può essere necessario inizializzare i dati specifici del dispositivo in altri archivi di soluzioni.

Le [API del registro delle identità dell'hub IoT][lnk-devguide-identityregistry] consentono di integrare l'hub IoT nel processo di provisioning.

## Gateway sul campo

In una soluzione IoT è presente un *gateway sul campo* tra i dispositivi e l'hub IoT e in genere si trova vicino ai dispositivi. I dispositivi comunicano direttamente con il gateway sul campo tramite un protocollo supportato dai dispositivi. Il gateway sul campo comunica con l'hub IoT usando un protocollo supportato dall'hub IoT. Un gateway sul campo può essere un dispositivo hardware altamente specializzato o un computer a basso consumo su cui viene eseguito il software che esegue lo scenario end-to-end a cui è destinato il gateway.

Un gateway sul campo differisce da un semplice dispositivo di routing del traffico, ad esempio un dispositivo o un firewall NAT (Network Address Translation), in quanto in genere esegue un ruolo attivo nella gestione dell'accesso e del flusso di informazioni nella soluzione. Ad esempio, un gateway sul campo è in grado di:

- **Aggiungere il supporto per dispositivi nuovi e legacy**: esistono milioni di sensori e attuatori nuovi e legacy che non possono inviare dati direttamente al cloud. Questi dispositivi usano un protocollo non adatto a Internet, non implementano la crittografia o non possono archiviare i certificati di identità. L'uso di un gateway riduce il carico di lavoro e i costi di connessione di questi dispositivi.
- **Eseguire analisi perimetrali**: sono molte le operazioni che possono essere eseguite localmente per ridurre la quantità di dati scambiati con il cloud. Ad esempio il filtro dei dati, l'invio in batch e la compressione. Potrebbe anche essere opportuno eseguire alcuni calcoli, ad esempio la pulizia dei dati o l'assegnazione dei punteggi di un modello di machine learning con i dati in tempo reale in locale.
- **Ridurre al minimo la latenza**: quando si cerca di impedire che si verifichino arresti della linea di produzione o si sta tentando di ripristinare l'elettricità, i millisecondi sono importanti. L'analisi dei dati in prossimità del dispositivo di raccolta può fare la differenza tra lo scampato pericolo e un errore di sistema a catena.
- **Risparmiare larghezza di banda**: una piattaforma petrolifera offshore genera tra 1 e 2 TB di dati ogni giorno. Un Boeing 787 crea mezzo terabyte di dati ad ogni volo. Non è pratico trasportare enormi quantità di dati da migliaia o centinaia di migliaia di dispositivi periferici nel cloud. E non è nemmeno necessario, poiché molte analisi critiche non richiedono elaborazione e archiviazione a livello di cloud.
- **Funzionare in modo affidabile**: i dati IoT vengono usati sempre più spesso per prendere decisioni riguardanti la sicurezza dei cittadini e le infrastrutture critiche. L'integrità e la disponibilità dell'infrastruttura e dei dati non possono essere compromesse da connessioni cloud intermittenti. L'uso di funzionalità come ad esempio archiviazione e inoltro per raccogliere ed elaborare i dati localmente per poi inviarli al cloud al momento opportuno, consente di creare soluzioni affidabili.
- **Risolvere problemi di privacy e sicurezza**: i dispositivi IoT, e i dati che producono, devono essere protetti. I gateway possono offrire servizi come l'isolamento dei dispositivi dall'Internet aperta, servizi di crittografia e identità per i dispositivi che non li contemplano, protezione dei dati memorizzati nel buffer o archiviati localmente e rimozione dei dati sensibili prima dell'invio delle informazioni tramite Internet.

### Altre considerazioni

È anche possibile usare gli [Azure IoT SDK per gateway][lnk-gateway-sdk] per implementare un gateway sul campo. Questi SDK offrono funzionalità specifiche, ad esempio la possibilità di eseguire il multiplex della comunicazione da più dispositivi nella stessa connessione all'hub IoT.

## Autenticazione personalizzata del dispositivo

È possibile usare il [registro delle identità dei dispositivi][lnk-devguide-identityregistry] dell'hub IoT di Azure per configurare il controllo dell'accesso e le credenziali di sicurezza per ogni dispositivo. Tuttavia, se una soluzione IoT dispone già di un investimento significativo in un registro personalizzato delle identità dei dispositivi e/o in uno schema di autenticazione, è possibile integrare l'infrastruttura esistente con l'hub IoT creando un *servizio token*. In questo modo, è possibile usare altre funzionalità IoT nella soluzione.

Un servizio token è un servizio cloud personalizzato. Usa i *criteri di accesso condivisi* dell'hub IoT con autorizzazioni **DeviceConnect** per creare token *basati sul dispositivo*. Questi token abilitano la connessione di un dispositivo all'hub IoT.

  ![Passaggi del modello di servizio token][img-tokenservice]

Di seguito sono riportati i passaggi principali del modello del servizio token:

1. Creare i [criteri di accesso condivisi dell'hub IoT][lnk-devguide-security] con autorizzazioni **DeviceConnect** per l'hub IoT. È possibile creare questi criteri nel [portale di Azure][lnk-portal] o a livello di programmazione. Il servizio token usa questi criteri per firmare i token creati.
2. Quando un dispositivo deve accedere all'hub IoT, richiede un token firmato dal servizio token. Il dispositivo può eseguire l'autenticazione con il registro delle identità dei dispositivi personalizzato/lo schema di autenticazione per determinare l'identità del dispositivo usata dal servizio token per creare il token.
3. Il servizio token restituisce un token. Il token viene creato in base alla [sezione relativa alla sicurezza della guida per gli sviluppatori dell'hub IoT][lnk-devguide-security], usando `/devices/{deviceId}` come `resourceURI`, con `deviceId` come dispositivo da autenticare. Il servizio token usa i criteri di accesso condivisi per costruire il token.
4. Il dispositivo usa il token direttamente con l'hub IoT.

> [AZURE.NOTE] È possibile usare la classe .NET [SharedAccessSignatureBuilder][lnk-dotnet-sas] o la classe Java [IotHubServiceSasToken][lnk-java-sas] per creare un token nel servizio token.

Il servizio token può impostare la scadenza del token, in base alle esigenze. Quando il token scade, l'hub IoT interrompe la connessione del dispositivo. Quindi, il dispositivo deve richiedere un nuovo token dal servizio token. Un intervallo di scadenza breve aumenterà il carico sia per il dispositivo che per il servizio token.

Perché un dispositivo si connetta all'hub, è comunque necessario aggiungerlo al registro delle identità dei dispositivi dell'hub IoT anche se il dispositivo usa un token e non una chiave di dispositivo per la connessione. Di conseguenza, è possibile continuare a usare il controllo dell'accesso per ogni dispositivo abilitando o disabilitando le identità dei dispositivi nel [registro delle identità dell'hub IoT][lnk-devguide-identityregistry] quando il dispositivo esegue l'autenticazione con un token. Questo riduce il rischio che vengano usati token con intervalli di scadenza prolungati.

### Confronto con un gateway personalizzato

Il modello di servizio token è il metodo consigliato per implementare uno schema di autenticazione/registro di identità personalizzato con l'hub IoT. È consigliabile perché l'hub IoT continua a gestire la maggior parte del traffico della soluzione. Tuttavia, in alcuni casi lo schema di autenticazione personalizzato è talmente legato al protocollo che è necessario un servizio che elabora tutto il traffico (*gateway personalizzato*). Esempi di servizio sono le [chiavi precondivise e Transport Layer Security][lnk-tls-psk]. Per altre informazioni, vedere l'argomento relativo al [gateway del protocollo][lnk-gateway].

## Heartbeat dei dispositivi <a id="heartbeat"></a>

Il [registro delle identità dell'hub IoT][lnk-devguide-identityregistry] contiene un campo denominato **connectionState**. È consigliabile usare il campo **connectionState** solo durante lo sviluppo e il debug ed evitare che le soluzioni IoT eseguano query su quel campo in fase di esecuzione, ad esempio per verificare se un dispositivo è connesso al fine di stabilire se inviare un messaggio da cloud a dispositivo o un SMS. Se è necessario che la soluzione IoT stabilisca se un dispositivo è connesso, in fase di esecuzione o con un'accuratezza maggiore di quella offerta dalla proprietà **connectionState**, dovrà implementare il *modello di heartbeat*.

Nel modello di heartbeat il dispositivo invia messaggi da dispositivo a cloud almeno una volta ogni intervallo di tempo stabilito, ad esempio almeno una volta ogni ora. Ciò significa che anche se in un dispositivo non sono presenti dati da inviare, invia comunque un messaggio vuoto da dispositivo a cloud, in genere con una proprietà che lo identifica come un heartbeat. Sul lato del servizio, la soluzione gestisce una mappa con l'ultimo heartbeat ricevuto per ogni dispositivo, presupponendo che esista un problema con un dispositivo se non riceve un messaggio di tipo heartbeat entro l'intervallo previsto.

Un'implementazione più complessa può includere le informazioni acquisite dal [monitoraggio delle operazioni][lnk-devguide-opmon] per identificare i dispositivi che provano a connettersi o a comunicare ma non vi riescono. Quando si implementa il modello di heartbeat, assicurarsi di controllare [Quote e limitazione][] dell'hub IoT.

> [AZURE.NOTE] Se una soluzione IoT richiede lo stato di connessione del dispositivo esclusivamente per determinare se inviare i messaggi da cloud a dispositivo e i messaggi non vengono trasmessi a grandi gruppi di dispositivi, un modello molto più semplice da considerare è usare un intervallo di scadenza breve. Consente di ottenere lo stesso risultato del mantenimento di un registro dello stato di connessione del dispositivo con un modello heartbeat, pur essendo molto più efficiente. È inoltre possibile, richiedendo gli acknowledgement messaggi, ricevere una notifica tramite l'hub IoT indicante i dispositivi in grado di ricevere i messaggi e che non sono online o sono malfunzionanti. Vedere la [Guida per gli sviluppatori dell'hub IoT di Azure][lnk-devguide-messaging] per altre informazioni sui messaggi da cloud a dispositivo.

## Passaggi successivi

Per ulteriori informazioni sulla pianificazione della distribuzione dell'hub IoT, vedere:

- [Supporto di MQTT][lnk-mqtt]
- [Dispositivi supportati][lnk-devices]
- [Supportare protocolli aggiuntivi][lnk-protocols]
- [Eseguire il confronto con Hub eventi][lnk-compare]
- [Scalabilità, disponibilità elevata e ripristino di emergenza][lnk-scaling]

Per esplorare ulteriormente le funzionalità dell'hub IoT, vedere:

- [Guida per sviluppatori][lnk-devguide]
- [Informazioni sulla gestione dei dispositivi tramite l'interfaccia utente di esempio][lnk-dmui]
- [Simulazione di un dispositivo con Gateway SDK][lnk-gateway]
- [Utilizzo del portale di Azure per gestire l'hub IoT][lnk-portal-manage]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

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
[lnk-devguide-messaging]: iot-hub-devguide.md#messaging
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[Quote e limitazione]: iot-hub-devguide.md#throttling
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[lnk-mqtt]: iot-hub-mqtt-support.md
[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal-manage]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->