<properties
 pageTitle="Introduzione alla libreria di Gestione dei dispositivi | Microsoft Azure"
 description="Libreria client di Gestione dei dispositivi dell'hub IoT di Azure"
 services="iot-hub"
 documentationCenter=""
 authors="CarlosAlayo"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="carlosa"/>

# Introduzione alla libreria client di Gestione dei dispositivi dell'hub IoT di Azure

## Panoramica

La libreria client di Gestione dei dispositivi dell'hub IoT di Azure consente di gestire i dispositivi IoT con l'hub IoT di Azure. "Gestire" significa anche riavviare, ripristinare le impostazioni predefinite e aggiornare il firmware. Attualmente viene fornita una libreria C indipendente dalla piattaforma, ma presto verrà aggiunto il supporto per altri linguaggi. Come descritto in [Panoramica di Gestione dei dispositivi dell'hub IoT di Azure][lnk-dm-overview], in Gestione dei dispositivi dell'hub IoT i concetti chiave sono tre:

- Dispositivi gemelli
- Processi del dispositivo
- Query su dispositivi

Se non si ha familiarità con questi concetti, è consigliabile leggere la panoramica prima di continuare perché sono tutti strettamente correlati alla libreria client.

La libreria client di Gestione dei dispositivi ha due responsabilità principali nella gestione dei dispositivi:

- Sincronizzare le proprietà nel dispositivo fisico con il dispositivo gemello corrispondente nell'hub IoT
- Pianificare i processi del dispositivo inviati dall'hub IoT al dispositivo

Le proprietà del dispositivo fisico (ad esempio, il livello della batteria e il numero di serie) vengono periodicamente sincronizzate con il dispositivo gemello basato sul cloud in modo che l'hub IoT abbia sempre una panoramica aggiornata di ogni dispositivo fisico, purché il dispositivo sia connesso.

Per il servizio il modo principale per interagire con il dispositivo fisico è attraverso i processi del dispositivo e il dispositivo gemello. I tipi di processo seguenti vengono forniti nell'hub IoT. La libreria client di Gestione dei dispositivi si occupa di gran parte dell'orchestrazione dei processi del dispositivo, ma l'implementazione dei callback necessari nel dispositivo per supportare ogni tipo di processo dipende dallo sviluppatore.

1.	**Aggiornamento del firmware**: aggiorna il firmware (o l'immagine del sistema operativo) nel dispositivo fisico
2.	**Riavvio**: riavvia il dispositivo fisico
3.	**Ripristino delle impostazioni predefinite**: ripristina l'immagine di backup predefinita del firmware (o immagine del sistema operativo) del dispositivo fisico archiviata nel dispositivo
4.	**Aggiornamento della configurazione**: configura l'agente client dell'hub IoT in esecuzione nel dispositivo fisico
5.	**Lettura della proprietà del dispositivo**: ottiene il valore più recente di una proprietà del dispositivo fisico
6.	**Scrittura della proprietà del dispositivo**: modifica una proprietà del dispositivo fisico

Le sezioni seguenti illustrano l'architettura della libreria client e forniscono le linee guida per implementare i diversi oggetti dispositivo nel dispositivo.

## Principi di progettazione e concetti funzionali della libreria client di gestione dei dispositivi
La libreria client di Gestione dei dispositivi è stata progettata pensando alla portabilità e all'integrazione multipiattaforma. Questi obiettivi sono stati raggiunti prendendo le decisioni di progettazione seguenti:

1.	È basata sul protocollo standard LWM2M su COAP per applicare l'estendibilità a una serie di dispositivi diversi.
2.	È scrittura in ANSI C99 per facilitare la portabilità a un'ampia gamma di piattaforme.
3.	È protetta con l'autenticazione TCP/TLS e hub IoT di Azure (token di firma di accesso condiviso) in modo che possa essere usata in scenari di sicurezza elevata.
4.	È basata sul progetto OSS [Eclipse Wakaama][lnk-Wakaama] per sfruttare codice esistente e contribuire alla community.

### Concetti di LWM2M pertinenti
È stato scelto lo standard LWM2M per applicare l'estendibilità a una serie di dispositivi diversi. Per semplificare l'esperienza di sviluppo, è stata eseguita l'astrazione della maggior parte del protocollo. È tuttavia importante conoscere i principi di base della libreria, soprattutto il modello di dati e come i dati vengono trasmessi.

#### Oggetti e risorse: modello di dati in LWM2M
Il modello di dati LWM2M introduce il concetto di oggetti e risorse:

- Gli **oggetti** descrivono un set di entità funzionali coerenti nel sistema, ad esempio gli aggiornamenti dei dispositivi e del firmware.
- Le **risorse** descrivono attributi o azioni incluse in tali oggetti, ad esempio le informazioni sul livello di batteria e l'azione di riavvio.

Si noti che in questo modello ci sono due relazioni "1 a molti":

- **Dispositivi e oggetti**: ogni dispositivo può avere più oggetti. Un dispositivo Contoso, ad esempio, deve avere un "oggetto dispositivo" e un "oggetto server". Nella sezione successiva verranno fornite le descrizioni dettagliate degli oggetti.
- **Oggetti e risorse**: ogni oggetto può avere più risorse. Un oggetto, ad esempio, può contenere le risorse di aggiornamento del firmware del dispositivo Contoso come l'URI del pacchetto in cui viene archiviata la nuova immagine.

#### Modello di osservazione/notifica: come vengono trasmessi i dati in LWM2M
Oltre a questi concetti, è importante conoscere come i dati scorrono dal dispositivo al servizio. A questo scopo, LWM2M definisce il modello di "osservazione/notifica". Quando il dispositivo fisico si connette al servizio, l'hub IoT avvia le "osservazioni" delle proprietà del dispositivo selezionate. Il dispositivo fisico "notifica" quindi al servizio le modifiche alle proprietà del dispositivo.

Nella libreria client il modello di osservazione/notifica è stato implementato per inviare i dati di gestione dei dispositivi dal dispositivo all'hub IoT. Il modello viene controllato da due parametri:

- **Periodo minimo**: periodo di tempo in cui il dispositivo ritarda l'invio di un aggiornamento per una proprietà osservata. Questo valore è impostato su 5 minuti. Il dispositivo invia quindi un aggiornamento per una proprietà osservata al massimo ogni 5 minuti, anche se il valore cambia più di frequente. Se quindi la proprietà viene modificata ogni minuto, il servizio visualizzerà solo l'ultima modifica avvenuta al minuto 5.

- **Periodo massimo**: periodo di tempo in cui il dispositivo, indipendentemente dal fatto che la proprietà osservata venga modificata o meno, invia un aggiornamento per il valore della proprietà. Questo valore è impostato su 6 ore. Il dispositivo invia quindi un aggiornamento per il valore di una proprietà osservata almeno ogni 6 ore, anche se il valore non è stato modificato.

> [AZURE.NOTE]  La sola eccezione a questi parametri è che le proprietà usate per il processo di aggiornamento del firmware hanno un periodo minimo impostato su 30 secondi. Infatti queste proprietà vengono modificate molto spesso durante l'esecuzione del processo, quindi il periodo minimo è stato ridotto per velocizzare la procedura di aggiornamento.

## Funzioni e architettura della libreria client
Come si è visto nella panoramica, le funzioni principali gestite dalle librerie client sono due:

- Sincronizzare il dispositivo fisico con il dispositivo gemello corrispondente nell'hub IoT.
- Pianificare i processi del dispositivo inviati dall'hub IoT al dispositivo.

In questa sezione verranno illustrati entrambi in dettaglio.

### Sincronizzazione del dispositivo fisico e del dispositivo gemello
La libreria client usa il modello di osservazione/notifica per mantenere il dispositivo gemello aggiornato. Si ricordi che il dispositivo gemello è la rappresentazione lato servizio del dispositivo fisico. Per eseguire la sincronizzazione, viene eseguita questa procedura:

1. Il dispositivo esegue la registrazione con il servizio, in genere durante l'inizializzazione. Ad esempio: "I'm a device, I have device ID Contoso with access token Y".
2. Il servizio osserva le risorse negli oggetti. Ad esempio: "keep me updated on the battery level of my Contoso device".
3. Il dispositivo notifica al servizio il valore della risorsa. La frequenza delle notifiche si basa sul periodo minimo e su quello massimo. Ad esempio: "5:00pm battery level 99%, 5:05pm battery level 90%, etc".

### Pianificare i processi del dispositivo: come interagiscono i processi del dispositivo gemello e del dispositivo
Per operare su un dispositivo fisico, il servizio deve trovare il dispositivo gemello associato. A tale scopo, è possibile effettuare una query delle proprietà o cercare un ID specifico. Conoscendo l'ID gemello (e quindi la corrispondenza con il dispositivo fisico), il servizio può avviare un processo nel dispositivo fisico.

Il processo del dispositivo rappresenta un set di comandi diversi che rappresentano la procedura specifica che si vuole eseguire (ad esempio, i passaggi di aggiornamento del firmware). Un processo del dispositivo può essere costituito da più passaggi:

1.	Il dispositivo gemello ha proprietà che rappresentano lo stato di un processo del dispositivo.
2.	Per passare da una procedura all'altra nel processo del dispositivo, le proprietà nel dispositivo gemello devono avere un determinato valore, quindi il dispositivo fisico deve impostare la proprietà corretta in modo che possa essere sincronizzato con il dispositivo gemello e il processo possa proseguire.

Questa sequenza viene ripetuta se la procedura è costituita da più passaggi, ad esempio, durante un aggiornamento del firmware, il dispositivo gemello modificherà più volte le proprietà nei vari passaggi prima di considerare terminato il processo.

## Linee guida per implementare la gestione dei dispositivi nel client
Nelle sezioni precedenti sono state illustrate le funzioni delle librerie client di Gestione dei dispositivi, i principi di progettazione e la relazione con LWM2M. Ora si vedrà come tutto si combina in fase di esecuzione.

La libreria client essenzialmente gestisce la comunicazione tra il dispositivo e il servizio, quindi resta solo da implementare la logica specifica del dispositivo che prevede due parti:

1.	**Implementare i dettagli specifici del dispositivo**: questo richiede di scrivere la logica specifica del dispositivo per eseguire le azioni richieste dal servizio (ad esempio, il download del pacchetto del firmware) nei callback appropriati. Di seguito è elencato un esempio di questo callback per il pacchetto di aggiornamento del firmware. È possibile trovare i callback nei file con estensione c della cartella disponibile [qui][lnk-github1].

            object_firmwareupdate *obj = get_firmwareupdate_object(0);
            obj->firmwareupdate_packageuri_write_callback =     start_firmware_download;
            // platform specific code
            obj->firmwareupdate_update_execute_callback = start_firmware_update;
            //platform specific code


2.	**Informare la libreria client quando le proprietà vengono modificate**: a questo scopo, chiamare le funzioni impostate corrispondenti nel file con estensione h della cartella disponibile [qui][lnk-github2].

        IOTHUB_CLIENT_RESULT set_firmwareupdate_state(uint16_t instanceId, int value);

### Oggetti che è necessario implementare nella libreria client di Gestione dei dispositivi

È stato illustrato come implementare la logica specifica del dispositivo per eseguire i processi del dispositivo. Ora verrà spiegato quali oggetti possono essere usati.

Alcuni di questi oggetti sono obbligatori e quindi è necessario implementare la logica specifica del dispositivo perché possa fare parte di Gestione dei dispositivi dell'hub IoT. Altri sono facoltativi ed è possibile scegliere in base alle esigenze del servizio, ad esempio, è possibile scegliere di non eseguire gli aggiornamenti del firmware con l'hub IoT. Ecco una descrizione di ogni oggetto:

- **Oggetto dispositivo (obbligatorio)**: fornisce informazioni specifiche del dispositivo, ad esempio informazioni sul produttore, numero del modello, numero di serie, ora del dispositivo. Il servizio può leggere queste informazioni e in alcuni casi aggiornarle. Definisce anche due azioni che il servizio può eseguire in un dispositivo: riavvio e ripristino delle impostazioni predefinite.
- **Oggetto server (obbligatorio)**: contiene i parametri e le impostazioni di connessione usati per connettersi all'hub IoT, ad esempio la durata della registrazione e l'associazione di trasporto. Il servizio può solo leggere queste informazioni.
- **Oggetto di configurazione (facoltativo)**: contiene le informazioni di configurazione definite dall'utente di cui il dispositivo può effettuare una query o il push a un dispositivo dal servizio per facilitare la configurazione del dispositivo. Il servizio può leggere e aggiornare queste informazioni.
- **Oggetto di aggiornamento del firmware (facoltativo)**: fornisce un'azione di aggiornamento del firmware che il servizio può richiamare. Fornisce anche informazioni come la posizione del pacchetto del firmware e lo stato di un'operazione di aggiornamento del firmware in corso.

A ogni oggetto è associato un set di risorse (si ricordi l'associazione 1 a molti). L'elenco di oggetti LWM2M e di tutte le risorse associate supportate in Gestione dei dispositivi dell'hub IoT di Azure è incluso alla fine di questo articolo.

> [AZURE.NOTE] Nella versione corrente del sistema non sono supportate proprietà del dispositivo personalizzate, istanze di risorse multiple e istanze di oggetti multiple.

### Riassumendo

Sotto è disponibile un diagramma riepilogativo. Sul lato destro, in blu, è possibile visualizzare i diversi componenti della libreria client di Gestione dei dispositivi: oggetti, gestori e metodi di notifica. Il lato sinistro, in verde, rappresenta la logica necessaria per scrivere a livello di applicazione per dispositivo. La libreria client connette la logica dell'applicazione all'hub IoT, assicurando che la comunicazione e la pianificazione vengano gestite in modo appropriato.

La figura seguente illustra i componenti della libreria client di Gestione dei dispositivi.

![][img-library-overview]

## Passaggi successivi: esperienza pratica
Questo articolo ha illustrato le nozioni di base dell'uso della libreria client di Gestione dei dispositivi dell'hub IoT per C.

Per un'esperienza pratica, è possibile accedere alle risorse seguenti:

- Esempio di aggiornamento del firmware Intel Edison: esempio con le funzionalità di gestione dei dispositivi abilitate con un dispositivo Intel Edison. Vedere [iotdm\_edison\_sample][lnk-edison-sample].
- Esempio di dispositivi simulati: esempio di dispositivo indipendente dalla piattaforma in esecuzione in dispositivi Linux e Windows. Vedere [iotdm\_simple\_sample][lnk-simple-sample].
- Per altre informazioni sugli oggetti LWM2M, vedere la pagina relativa al [registro di oggetti e risorse OMA LWM2M][lnk-oma]

## Appendice: Oggetti e risorse LWM2M attualmente supportati

### Oggetto dispositivo

| Nome risorsa | Operazione remota consentita sulla risorsa | Tipo | Intervallo e unità | Descrizione |
|-----------------|--------------------------------------|---------|-----------------|-------------|
| Produttore | Lettura | String | | Nome del produttore |
| ModelNumber | Lettura | String | | Identificatore modello (stringa specificata dal produttore) |
| DeviceType | Lettura | String | | Tipo di dispositivo (stringa specificata dal produttore)<br/>Nota: esegue il mapping all'API lato server **SystemPropertyNames.DeviceDescription** |
| SerialNumber | Lettura | String | | Numero di serie del dispositivo |
| FirmwareVersion | Lettura | String | | Versione corrente del firmware del dispositivo |
| HardwareVersion | Lettura | String | | Versione corrente dell'hardware del dispositivo. |
| Reboot | Esegui | | | Riavvia il dispositivo. |
| FactoryReset | Esegui | | | Esegue il ripristino delle impostazioni predefinite del dispositivo in modo che il dispositivo abbia la stessa configurazione della distribuzione iniziale. |
| CurrentTime | Lettura<br/>Scrittura | Time | | Ora UNIX corrente del dispositivo. Il client deve essere responsabile dell'aumento di questo valore a ogni secondo.<br/>Il server di Gestione dei dispositivi può scrivere in questa risorsa per sincronizzare il client con l'ora del server. |
| UTCOffset | Lettura<br/>Scrittura | String | | Differenza dall'ora UTC in vigore. |
| Fuso orario | Lettura<br/>Scrittura | String | | Indica in quale fuso orario si trova il dispositivo. |
| MemoryFree | Lettura | Integer | KB | Memoria attualmente disponibile stimata dello spazio di archiviazione per memorizzare dati e software nel dispositivo |
| MemoryTotal | Lettura | Integer | KB | Quantità totale di spazio di archiviazione per memorizzare dati e software nel dispositivo |
| BatteryLevel | Lettura | Integer | 0-100% | Livello di batteria corrente espresso come percentuale (da 0 a 100) |
| BatteryStatus | Lettura | Integer | 0-6 | **0**: la batteria funziona normalmente e non è collegata alla rete elettrica.<br/>**1**: la batteria si sta ricaricando.<br/>**2**: la batteria è completamente carica ed è collegata alla rete elettrica.<br/>**3**: la batteria è danneggiata.<br/>**4**: la batteria è quasi scarica.<br/>**5**: la batteria non è presente.<br/> **6**: informazioni sulla batteria non disponibili. |

### Oggetto di aggiornamento del firmware

| Nome risorsa | Operazione | Tipo | Intervallo e unità | Descrizione |
|----------------|-----------|---------|-----------------|-------------|
| Pacchetto | Scrittura | Opaco | | Pacchetto del firmware in formato binario.<br/>Esegue il mapping all'API del servizio:<br/>**SystemPropertyNames.FirmwarePackage** |
| PackageURI | Scrittura | String | 0-255 byte | URI da cui il dispositivo può scaricare il pacchetto del firmware.<br/>Esegue il mapping all'API del servizio: **SystemPropertyNames.FirmwarePackageUri** |
| Aggiornamento | Esegui | | | Aggiorna il firmware usando il pacchetto del firmware archiviato in Package o usando il firmware scaricato dall'URI del pacchetto.<br/>Esegue il mapping all'API del servizio:<br/>**ScheduleFirmwareUpdateAsync** |
| Stato | Lettura | Integer | 1-3 | Stato del processo di aggiornamento del firmware:<br/>**1**: inattivo. Può verificarsi prima di scaricare il pacchetto del firmware o dopo averlo applicato.<br/>**2**: download del pacchetto del firmware in corso.<br/>**3**: pacchetto del firmware scaricato.<br/> Esegue il mapping all'API del servizio: **SystemPropertyNames.FirmwareUpdateState** |
| UpdateResult | Lettura | Integer | 0-6 | Risultato del download o dell'aggiornamento del firmware<br/>**0**: valore predefinito.<br/>**1**: aggiornamento del firmware riuscito.<br/>**2**: spazio di archiviazione non sufficiente per il nuovo pacchetto del firmware.<br/>**3**: memoria esaurita durante il download del pacchetto del firmware.<br/>**4**: connessione persa durante il download del pacchetto del firmware.<br/>**5**: errore del controllo di ridondanza ciclico per il nuovo pacchetto scaricato.<br/>**6**: tipo di pacchetto del firmware non supportato.<br/>**7**: URI non valido. Esegue il mapping all'API del servizio: **SystemPropertyNames.FirmwareUpdateResult** |
| PkgName | Lettura | String | 0-255 byte | Nome descrittivo del pacchetto del firmware a cui fa riferimento la risorsa **Package**<br/>Esegue il mapping all'API del servizio:<br/>**SystemPropertyNames.FirmwarePackageName** |
| PackageVersion | Lettura | String | 0-255 byte | Versione del pacchetto del firmware a cui fa riferimento la risorsa **Package**<br/>Esegue il mapping all'API del servizio:<br/>**SystemPropertyNames.FirmwarePackageVersion** |

### Oggetto server LWM2M

| Nome risorsa | Operazione | Tipo | Intervallo e unità | Descrizione |
|------------------------|------------|---------|-----------------|---------------|
| Default Minimum Period | Lettura Scrittura | Integer | Secondi | Periodo di tempo in cui il dispositivo ritarda l'invio di un aggiornamento per una proprietà osservata. Ad esempio, con un valore di **DefaultMinPeriod** di 5 minuti, il dispositivo invia un aggiornamento per una proprietà osservata al massimo ogni 5 minuti, anche se il valore cambia più di frequente. Esegue il mapping all'API del servizio: **SystemPropertyNames.DefaultMinPeriod** |
| Default Maximum Period | Lettura Scrittura | Integer | Secondi | Periodo di tempo (in secondi) in cui il dispositivo, indipendentemente dal fatto che la proprietà osservata venga modificata, invia un aggiornamento per il valore della proprietà. Ad esempio, con un valore di **DefaultMaxPeriod** di 6 ore, una proprietà osservata invia un aggiornamento per il valore di tale proprietà almeno ogni 6 ore, indipendentemente dalle modifiche delle risorse.<br/>Esegue il mapping all'API del servizio:<br/>**SystemPropertyNames.DefaultMaxPeriod** |
| Durata | Lettura Scrittura | Integer | Secondi | Durata della registrazione del dispositivo. Una nuova richiesta di registrazione o di aggiornamento deve essere ricevuta dal dispositivo entro questa durata. In caso contrario, la registrazione del dispositivo con il servizio viene annullata.<br/>Esegue il mapping all'API del servizio:<br/>**SystemPropertyNames.RegistrationLifetime** |

### Oggetto di configurazione

| Nome risorsa | Operazione | Tipo | Intervallo e unità | Descrizione |
|---------------|------------|--------|-----------------|-------------|
| Nome | Lettura Scrittura | String | | Identifica in modo univoco il nome della configurazione del dispositivo da leggere o aggiornare. |
| Valore | Lettura Scrittura | String | | Identifica in modo univoco il valore della configurazione da leggere o aggiornare. |
| Applica | Esegui | | | Applica la modifica alla configurazione nel dispositivo. |

[img-library-overview]: media/iot-hub-device-management-library/library.png
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-simple-sample]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_simple_sample
[lnk-edison-sample]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample
[Azure IoT Hub device SDK]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c
[Azure IoT Hub]: Link%20to%20DM%20Overview
[Lightweight M2M]: http://openmobilealliance.org/about-oma/work-program/m2m-enablers/
[CoAP]: https://tools.ietf.org/html/rfc7252
[Wakaama]: https://github.com/eclipse/wakaama
[OMA LWM2M Object and resource registry]: http://technical.openmobilealliance.org/Technical/technical-information/omna/lightweight-m2m-lwm2m-object-registry

[lnk-run-linux]: http://TODO
[lnk-Wakaama]: https://github.com/eclipse/wakaama
[lnk-github1]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/lwm2m_objects
[lnk-github2]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/lwm2m_objects
[lnk-oma]: http://technical.openmobilealliance.org/Technical/technical-information/omna/lightweight-m2m-lwm2m-object-registry

<!---HONumber=AcomDC_0518_2016-->