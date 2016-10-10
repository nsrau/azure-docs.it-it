<properties 
    pageTitle="Panoramica degli esempi per il bus di servizio | Microsoft Azure"
    description="Suddivide gli esempi relativi al bus di servizio in categorie e fornisce una descrizione e un collegamento a ognuno di essi."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="05/06/2016"
    ms.author="sethm" />

# Esempi relativi al bus di servizio

Gli esempi relativi al bus di servizio illustrano come usare le funzionalità principali del [bus di servizio](https://azure.microsoft.com/services/service-bus/) (servizio cloud) e del [bus di servizio per Windows Server](https://msdn.microsoft.com/library/dn282144.aspx). Questo articolo suddivide gli esempi disponibili in categorie e fornisce una descrizione e un collegamento a ognuno di essi.

>[AZURE.NOTE] Gli esempi relativi al bus di servizio non vengono installati con l'SDK. Per ottenere gli esempi, visitare la pagina relativa agli [esempi di Azure SDK](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
>
>In aggiunta,[qui](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) è disponibile un set aggiornato di esempi di messaggi del bus di servizio (in questo articolo non viene descritta la scrittura degli stessi). [Qui](https://github.com/Azure-Samples/azure-servicebus-relay-samples) è possibile trovare esempi di inoltro.

## Messaggistica negoziata del bus di servizio

Gli esempi seguenti illustrano come scrivere applicazioni che usano il bus di servizio.

Tenere presente che per gli esempi relativi alla messaggistica negoziata è necessaria una stringa di connessione per l'accesso allo spazio dei nomi servizio del bus di servizio.

### Per ottenere una stringa di connessione per il bus di servizio di Azure

1. Accedere al [portale di Azure classico](http://manage.windowsazure.com).

1. Nella colonna sinistra fare clic su **Bus di servizio**.

1. Fare clic sullo spazio dei nomi del servizio nell'elenco.

1. Fare clic su **Connection Information**. Nella finestra di dialogo **Accedi a informazioni di connessione** copiare la stringa di connessione negli Appunti.

1. Incollare la stringa di connessione nel file App.config relativo all'esempio.

### Per ottenere una stringa di connessione per il bus di servizio di Windows Server

1. Eseguire il cmdlet di PowerShell seguente:

	```
	get-sbClientConfiguration
	```

2. Incollare la stringa di connessione nel file App.config relativo all'esempio.

### Per iniziare

Questi esempi illustrano la funzionalità di messaggistica e inoltro di base.

|Nome esempio|Descrizione|Versione minima SDK|Disponibilità|
|---|---|---|---|
|[Introduzione: Messaggistica con code](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3)|Illustra come usare il bus di servizio di Microsoft Azure per inviare e ricevere messaggi da una coda.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Getting Started: Messaging With Topics](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5) (Introduzione: messaggistica con argomenti)|Illustra come usare il bus di servizio di Microsoft Azure per inviare e ricevere messaggi da un argomento con più sottoscrizioni.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Introduzione all'Hub eventi](http://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097)|Illustra le funzionalità di base di Hub eventi, ad esempio la creazione di un hub eventi, l'invio di eventi a un hub eventi e il consumo di eventi tramite il processore di eventi.|2\.4|Bus di servizio di Microsoft Azure|

### Esplorazione delle funzionalità

Gli esempi seguenti illustrano varie funzionalità del bus di servizio.

|Nome esempio|Descrizione|Versione minima SDK|Disponibilità|
|---|---|---|---|
|[Provider di token HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5)|Illustra le varie modalità di autenticazione di un client HTTP/REST con il bus di servizio.|2\.1|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Client HTTP del bus di servizio](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a)|Illustra come inviare e ricevere messaggi dal bus di servizio tramite HTTP/REST.|2\.3|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Inoltro automatico del bus di servizio](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b)|Illustra come inoltrare automaticamente messaggi da una coda, una sottoscrizione o una coda di messaggi non recapitabili a un'altra coda o argomento. Illustra anche come inviare un messaggio a una coda o a un argomento tramite una coda di trasferimento.|2\.3|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Messaggistica negoziata: Esempio di sessione con canali WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451)|Illustra come usare il bus di servizio di Microsoft Azure tramite canali Windows Communication Foundation (WCF). L'esempio mostra l'uso di canali WCF per inviare e ricevere messaggi tramite una coda del bus di servizio. Illustra sia la comunicazione basata su sessione sia la comunicazione non basata su sessione attraverso il bus di servizio.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Messaggistica negoziata: Transazioni](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e)|Illustra come usare le funzionalità di messaggistica del bus di servizio di Microsoft Azure nell'ambito di una transazione per garantire che venga eseguito in modo atomico il commit di batch di operazioni di messaggistica.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Messaggistica negoziata: Operazioni di gestione tramite REST](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88)|Illustra come eseguire operazioni di gestione nel bus di servizio tramite REST.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[API REST del provider di risorse](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203)|Illustra come usare le nuove API REST RDFE del bus di servizio per gestire spazi dei nomi ed entità di messaggistica.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Messaggistica negoziata: Esempio di sessione con il servizio WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2)|Illustra come usare il bus di servizio di Microsoft Azure con il modello di servizio WCF. L'esempio mostra l'uso del modello di servizio WCF per la comunicazione basata su sessione tramite una coda del bus di servizio.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Messaggistica negoziata: Richiesta-risposta](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8)|Illustra come usare il bus di servizio di Microsoft Azure e la funzionalità di richiesta-risposta. L'esempio mostra client e server semplici che comunicano tramite una coda del bus di servizio.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Messaggistica negoziata: Coda di messaggi non recapitabili](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8)|Illustra come usare il bus di servizio di Microsoft Azure e la funzionalità di messaggistica "coda di messaggi non recapitabili". L'esempio mostra un mittente e un ricevitore semplici che comunicano tramite una coda del bus di servizio.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Messaggistica negoziata: Messaggi rinviati](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879)|Illustra come usare la funzionalità di rinvio dei messaggi del bus di servizio di Microsoft Azure. L'esempio mostra un mittente e un ricevitore semplici che comunicano tramite una coda del bus di servizio.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Messaggistica negoziata: Sessione di messaggistica](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4)|Illustra come usare il bus di servizio di Microsoft Azure e la funzionalità Sessione di messaggistica. L'esempio mostra mittenti e un ricevitori semplici che comunicano tramite una coda del bus di servizio.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Brokered Messaging: Request Response Topic](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e) (Messaggistica negoziata: argomento con modello richiesta-risposta)|Illustra come implementare il modello richiesta-risposta usando gli argomenti e le sottoscrizioni del bus di servizio di Microsoft Azure. L'esempio mostra client e server semplici che comunicano tramite un argomento del bus di servizio.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Messaggistica negoziata: Coda con modello richiesta-risposta](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf)|Illustra come usare il bus di servizio di Microsoft Azure e la funzionalità di richiesta-risposta. L'esempio mostra client e server semplici che comunicano tramite due code del bus di servizio.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Messaggistica negoziata: Rilevamento di duplicati](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25)|Illustra come usare la funzionalità di rilevamento dei messaggi duplicati del bus di servizio di Microsoft Azure tramite le code. Vengono create due code, una con il rilevamento di duplicati abilitato e l'altra senza rilevamento di duplicati.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Messaggistica negoziata: Messaggistica asincrona](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74)|Illustra come usare il bus di servizio di Microsoft Azure per inviare e ricevere messaggi in modo asincrono da una coda. La coda fornisce una comunicazione asincrona disaccoppiata tra un mittente e un numero qualsiasi di ricevitori (in questo esempio il ricevitore è uno solo).|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Messaggistica negoziata: Filtri avanzati](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)|Illustra come usare i filtri avanzati di pubblicazione/sottoscrizione del bus di servizio di Microsoft Azure. Vengono creati un argomento e 3 sottoscrizioni con definizioni di filtro diverse, vengono inviati messaggi all'argomento e vengono ricevuti tutti i messaggi dalle sottoscrizioni.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Messaggistica negoziata: Prelettura di messaggi](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d)|Illustra come usare la funzionalità di prelettura dei messaggi del bus di servizio di Microsoft Azure. Mostra come usare la funzionalità di prelettura dei messaggi alla ricezione.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|

## Inoltro del bus di servizio

Esempi che illustrano l'inoltro del bus di servizio.

### Per iniziare

|Nome esempio|Descrizione|Versione minima SDK|Disponibilità|
|---|---|---|---|
|[Messaggistica inoltrata: Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3)|Illustra come eseguire un client e un servizio del bus di servizio di Microsoft Azure in Azure. Nell'esempio il bus di servizio viene configurato a livello di codice. Nei file di configurazione sono archiviate solo le informazioni relative alla sicurezza e all'ambiente.|1\.8|Bus di servizio di Microsoft Azure|
|[Autenticazione della messaggistica inoltrata: Segreto condiviso](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02)|Illustra come usare un nome dell'autorità di certificazione e un segreto dell'autorità di certificazione per eseguire l'autenticazione con il bus di servizio.|1\.8|Bus di servizio di Microsoft Azure|
|[Autenticazione della messaggistica inoltrata: WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831)|Illustra come esporre un servizio HTTP che non richiede l'autenticazione utente del client.|1\.8|Bus di servizio di Microsoft Azure|
|[Binding della messaggistica inoltrata: WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0)|Illustra come usare il binding **WebHttpRelayBinding** per restituire dati binari con il modello di programmazione Web.|1\.8|Bus di servizio di Microsoft Azure|
|[Binding della messaggistica inoltrata: Connessione inoltrata NetTcp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692)|Illustra come usare il binding **NetTcpRelayBinding**.|1\.8|Bus di servizio di Microsoft Azure|

### Esplorazione delle funzionalità

Esempi che illustrano varie funzionalità dell'inoltro del bus di servizio.

|Nome esempio|Descrizione|Versione minima SDK|Disponibilità|
|---|---|---|---|
|[Autenticazione della messaggistica inoltrata: WebToken semplice](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392)|Illustra come usare una credenziale token Web semplice per eseguire l'autenticazione con il bus di servizio. Questo esempio è simile all'esempio per Echo, fatta eccezione per alcune modifiche. In particolare, questo esempio aggiunge un comportamento alle applicazioni ServiceHost (servizio) e ChannelFactory (client).|1\.8|Bus di servizio di Microsoft Azure|
|[Messaggistica inoltrata: Bilanciamento del carico](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8)|Illustra come usare il bus di servizio di Microsoft Azure per instradare messaggi a più ricevitori. Vengono descritte più istanze di un servizio semplice che comunicano con un client usando il binding **NetTcpRelayBinding**.|1\.8|Bus di servizio di Microsoft Azure|
|[Binding della messaggistica inoltrata: Net Event](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977)|Illustra l'uso del binding **NetEventRelayBinding** nel bus di servizio di Microsoft Azure.|1\.8|Bus di servizio di Microsoft Azure|
|[Binding della messaggistica inoltrata: Sessione WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb)|Illustra l'uso del binding **WS2007HttpRelayBinding** con le sessioni affidabili abilitate. Mostra anche come specificare credenziali del bus di servizio nel file di configurazione anziché a livello di codice.|1\.8|Bus di servizio di Microsoft Azure|
|[Binding della messaggistica inoltrata: WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5)|Illustra come usare il binding **WS2007HttpRelayBinding** con la sicurezza dei messaggi, per proteggere i messaggi end-to-end richiedendo al tempo stesso ai client di eseguire l'autenticazione con il bus di servizio.|1\.8|Bus di servizio di Microsoft Azure|
|[Messaggistica inoltrata: Scambio di metadati](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e)|Illustra come esporre un endpoint di metadati che usa il binding di inoltro. **MetadataExchange** è supportato nei binding di inoltro seguenti: **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding** e **WS2007HttpRelayBinding**.|1\.8|Bus di servizio di Microsoft Azure|
|[Binding della messaggistica inoltrata: Connessione diretta NetTcp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161)|Illustra come configurare il binding **NetTcpRelayBinding** per supportare la modalità di connessione **ibrida**, che stabilisce prima una connessione inoltrata e, se possibile, passa automaticamente a una connessione diretta tra un client e un servizio.|1\.8|Bus di servizio di Microsoft Azure|
|[Binding della messaggistica inoltrata: NetTcp MsgSec UserName](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392)|Illustra come usare il binding **NetTcpRelayBinding** con la sicurezza dei messaggi.|1\.8|Bus di servizio di Microsoft Azure|
|[Binding della messaggistica inoltrata: Net Oneway](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a)|Illustra come esporre e consumare un endpoint di servizio usando il binding **NetOnewayRelayBinding**.|1\.8|Bus di servizio di Microsoft Azure|
|[Binding della messaggistica inoltrata: WS2007Http semplice](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a)|Illustra l'uso del binding **WS2007HttpRelayBinding**. Descrive un servizio semplice che non usa opzioni di sicurezza e non richiede l'autenticazione da parte dei client.|1\.8|Bus di servizio di Microsoft Azure|

## Strumenti di riferimento del bus di servizio

Gli esempi seguenti illustrano altre funzionalità del servizio.

|Nome esempio|Descrizione|Versione minima SDK|Disponibilità|
|---|---|---|---|
|[Service Bus Explorer](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)|Service Bus Explorer consente agli utenti di connettersi a uno spazio dei nomi servizio del bus di servizio e di gestire le entità di messaggistica in modo semplificato. Lo strumento offre caratteristiche avanzate, tra cui funzionalità di importazione/esportazione, nonché la possibilità di testare le entità di messaggistica e i servizi di inoltro.|1\.8|Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server|
|[Autorizzazione: SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93)|Questo esempio illustra come creare e gestire identità del servizio in Controllo di accesso di Microsoft Azure Active Directory (anche noto come Servizio di controllo di accesso o ACS) per l'uso con il bus di servizio.|N/D|Bus di servizio di Microsoft Azure|

## Passaggi successivi

Per altre panoramiche sul bus di servizio, vedere gli argomenti seguenti.

- [Panoramica della messaggistica del bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md)
- [Architettura del bus di servizio](service-bus-architecture.md)
- [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)

<!---HONumber=AcomDC_0928_2016-->