---
title: Concetti sulla sicurezza X.509 dell'hub IoT di Azure | Microsoft Docs
description: "Concetto - capire il valore dei certificati di autorità del certificato X.509 nella produzione e nell'autenticazione del dispositivo IoT."
services: iot-hub
documentationcenter: .net
author: eustacea
manager: arjmands
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 34d4be431b76d5ba8258d932cb21ed6f4818863e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Comprensione concettuale dei certificati x.509 nel settore IoT

In questo articolo si descrive il valore dell'uso dei certificati di autorità del certificato X.509 nella produzione e nell'autenticazione del dispositivo all'hub IoT.  Include le informazioni relative alla configurazione della catena di alimentazione ed evidenzia i vantaggi.

L'articolo illustra:

* Cosa sono i certificati della CA x.509 e come ottenerli
* Come registrare il certificato della CA X.509 all'hub IoT
* Come configurare una catena di alimentazione di produzione per un'autenticazione basata sull'autorità di certificazione X.509
* Come si collegano i dispositivi firmati con l'autorità di certificazione X.509 all'hub IoT

## <a name="overview"></a>Panoramica

L'autenticazione dell'autorità di certificazione X.509 è un approccio per l'autenticazione dei dispositivi nell'hub IoT usando un metodo che semplifica notevolmente la creazione dell'identità del dispositivo e la gestione del ciclo di vita nella catena di alimentazione.

Un attributo distintivo dell'autenticazione dell'autorità di certificazione X.509 è una relazione uno/molti che un certificato dell'autorità della CA possiede con i dispositivi di downstream.  Questa relazione consente la registrazione di un numero qualsiasi di dispositivi nell'hub IoT registrando un certificato dell'autorità di certificazione X.509 una volta, in caso contrario i certificati univoci del dispositivo devono essere pre-registrati per ogni dispositivo prima che un dispositivo possa connettersi. Questa relazione uno/molti semplifica anche le operazioni di gestione del ciclo di vita dei certificati del dispositivo.

Un altro attributo importante dell'autenticazione dell'autorità di certificazione X.509 è una semplificazione della logistica della catena di alimentazione.  L'autenticazione sicura dei dispositivi richiede che ogni dispositivo usi un segreto univoco come ad esempio chiave come base per essere considerato attendibile. Nell'autenticazione basata sui certificati il segreto è una chiave privata. Un tipico flusso di produzione del dispositivo prevede più passaggi e responsabili.  La gestione sicura delle chiavi private del dispositivo in più responsabili e il mantenimento dell'attendibilità è un'operazione complessa e costosa.  Usando le autorità di certificazione si risolve questo problema, poiché ogni responsabile firma in una catena di crittografia considerata attendibile invece che dimostrarsi attendibile con delle chiavi private del dispositivo.  Ogni responsabile a turno firma i dispositivi nella rispettiva fase del processo del flusso di produzione.  Il risultato complessivo è una catena di alimentazione ottimale con una responsabilità incorporata tramite l'uso della catena di crittografia di attendibilità.  Vale la pena notare che questo processo produce maggiore protezione quando i dispositivi proteggono le chiavi private univoche.  A tal fine, l'utente è invitato a usare i moduli di protezione hardware (HSM) in grado di generare internamente le chiavi private che non vedranno mai la luce del giorno.

In questo articolo si offre una vista end-to-end dell'uso dell'autenticazione dell'autorità di certificazione X.509, dalla configurazione della catena di alimentazione alla connessione del dispositivo, mentre si usa un esempio reale per consolidare la comprensione.

## <a name="introduction"></a>Introduzione

Il certificato della CA X.509 è un certificato digitale il cui proprietario può firmare altri certificati.  Questo certificato digitale è X.509 perché è conforme con uno standard di formattazione dei certificati previsto dallo standard RFC 5280 di IETF ed è un'autorità di certificazione (CA) perché il proprietario può firmare altri certificati.

L'uso dell'autorità di certificazione X.509 viene compreso meglio in relazione a un esempio concreto.  Prendere in considerazione la Società-X, un creatore di Smart-X-Widgets progettati per l'installazione professionale. La Società-X affida in outsource sia la produzione sia l'installazione.  Contratta il produttore Azienda-Y per produrre gli Smart-X-Widgets e il fornitore di servizi Tecnico Z per l'installazione. La Società-X vuole che Smart-X-Widget venga spedito direttamente dalla Azienda-Y al Tecnico-Z per l'installazione e che si connetta direttamente all'istanza della Società-X dell'hub IoT dopo l'installazione senza altri interventi da parte della Società-X. A tale scopo, la Società-X deve completare alcune operazioni di installazione singole per preparare lo Smart-X-Widget alla connessione automatica.  Con lo scenario end-to-end in mente, il resto di questo articolo è strutturato come segue:

* Acquisire il certificato della CA X.509

* Registrare il certificato della CA X.509 a hub IoT

* Firmare i dispositivi in una catena di certificati

* Connessione del dispositivo

## <a name="acquire-the-x509-ca-certificate"></a>Acquisire il certificato della CA X.509

La Società-X ha la possibilità di acquistare un certificato di autorità di certificazione X.509 da un'autorità di certificazione di radice pubblica o crearne uno tramite un processo autofirmato.  Una soluzione potrebbe essere ottimale rispetto all'altro in base allo scenario dell'applicazione.  Indipendentemente dall'opzione, il processo comporta due passaggi fondamentali, la generazione di una coppia di chiavi pubblica/privata e la firma della chiave pubblica in un certificato.

![img-csr-flow](./media/csr-flow.png)

Dettagli su come l'esecuzione di questi passaggi cambi con provider di servizio diversi.

### <a name="purchasing-an-x509-ca-certificate"></a>Acquistare un certificato della CA X.509

Il vantaggio dell'acquisto di un certificato della CA risiede nell'avere un atto della CA radice ben noto che funge da terza parte attendibile a garanzia della legittimità dei dispositivi IoT quando si connettono i dispositivi. La Società-X sceglie questa opzione se si prevede che lo Smart-X-Widget interagisca con i prodotti o i servizi di terze parti dopo la connessione iniziale all'hub IoT.

Per acquistare un certificato di autorità di certificazione X.509, la Società-X sceglie un provider di servizi dei certificati radice. Una ricerca su internet per la frase 'Autorità di certificazione radice' genererà lead validi.  La CA radice guiderà la Società-X su come creare la coppia di chiavi pubblica/privata e su come generare una Richiesta di firma del certificato (CSR) per i servizi.  Una richiesta di firma del certificato è il processo formale di richiesta di un certificato da parte di un'autorità di certificazione.  Il risultato dell'acquisto è un certificato da usare come certificato di autorità.  Data la grande diffusione di certificati X.509, è probabile che il certificato sia stato formattato correttamente in relazione allo standard IETF RFC 5280.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Creazione di un certificato della CA autofirmato X.509

Il processo per creare un certificato della CA autofirmato X.509 è simile all'acquisto, ad eccezione del fatto che interessa un firmatario di terze parti come autorità di certificazione radice. In questo esempio, la Società-X firmerà il certificato dell'autorità invece che un'autorità di certificazione radice.  La Società-X può scegliere questa opzione per verificare fino a che punto è in grado di acquistare un certificato di autorità. La Società-X può anche usare un certificato autofirmato della CA X.509 nella produzione, se lo Smart-X-Widget non sarà connesso a nessun servizio di terze parti di fuori dell'hub IoT.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Registrare il certificato X.509 all'hub IoT

La Società-X deve registrare l'autorità di certificazione X.509 sull'hub IoT in cui verrà usata per autenticare gli Smart-X-Widgets al momento della connessione.  Si tratta di un processo unico che offre la possibilità di autenticare e gestire un numero qualsiasi di dispositivi Smart-X-Widget.  Questo processo è unico a causa di una relazione uno/molti tra l'autorità di certificazione e i dispositivi e costituisce anche uno dei principali vantaggi dell'uso del metodo di autenticazione dell'autorità di certificazione X.509.  L'alternativa consiste nel caricare le identificazioni personali del certificato singolo per ogni dispositivo Smart-X-Widget aggiungendo così dei costi operativi.

La registrazione del certificato della CA X.509 è un processo in due fasi, il caricamento del certificato e la proof-of-possession del certificato.

![img-pop-flow](./media/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Caricamento del certificato della CA X.509

Il processo di caricamento del certificato della CA X.509 è proprio quello di caricare il certificato della CA nell'hub IoT.  Hub IoT si aspetta il certificato in un file. La Società-X semplicemente carica il file del certificato. Il file del certificato NON DEVE in nessuna circostanza contenere chiavi private.  Le procedure consigliate dagli standard che regolano l'infrastruttura a chiave pubblica (PKI) impongono che la conoscenza delle informazioni provate della Società-X in questo caso risieda esclusivamente all'interno della Società-X.

### <a name="proof-of-possession-of-the-certificate"></a>Proof-of-Possession del certificato

Il certificato della CA X.509, come qualsiasi certificato digitale, contiene informazioni pubbliche che sono soggette a intercettazione.  Di conseguenza, un utente malintenzionato può intercettare un certificato e provare a caricarlo come proprio.  In questo esempio, l'hub IoT desidera assicurarsi che il certificato della CA che la Società-X sta caricando appartenga davvero alla Società-X. Lo fa richiedendo alla Società-X di dimostrare di essere realmente in possesso del certificato tramite un [flusso di proof-of-possession (PoP)](https://tools.ietf.org/html/rfc5280#section-3.1). Il flusso di proof-of-possession comporta che la generazione di un numero casuale da parte dell'hub IoT sia firmata dalla Società-X con la propria chiave privata.  Se la Società-X ha seguito le procedure consigliate dell'infrastruttura a chiave pubblica e ha protetto la propria chiave privata, allora è l'unica nella posizione di poter rispondere correttamente alla richiesta di proof-of-possession.  L'hub IoT prosegue per registrare il certificato della CA di X.509 al momento di una risposta con esito positivo della richiesta di proof-of-possession.

Una risposta corretta alla richiesta di proof-of-possession da parte dell'hub IoT completa la registrazione dell'autorità di certificazione X.509.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Firmare i dispositivi in una catena di certificati

IoT richiede che ogni dispositivo disponga di un'identità univoca.  Queste identità sono assumono la forma di certificati per gli schemi di autenticazione basati sui certificati.  In questo esempio ciò significa che ogni Smart-X-Widget deve possedere un certificato univoco del dispositivo.  Come fa la Società-X a configurarlo nella catena di alimentazione?

Un modo per farlo è quello di pre-generare i certificati per gli Smart-X-widget e affidare la conoscenza delle chiavi private del dispositivo univoco corrispondenti ai partner della catena di alimentazione.  Per la Società-X, ciò significa conferire attendibilità all'Azienda-Y e al Tecnico-Z.  Benché questo sia un metodo valido, è dotato di richieste che devono essere soddisfatte per assicurare l'attendibilità come indicato di seguito:

1. La necessità di condividere delle chiavi private del dispositivo con i partner della catena di alimentazione, oltre a ignorare le procedure consigliate dell'infrastruttura PKI di non condividere mai le chiavi private, rende costosa la costruzione dell'attendibilità nella catena di alimentazione.  Ciò comporta che debbano essere installati sistemi di capitale come stanze protette per le chiavi private del dispositivo in casa e processi quali controlli di sicurezza periodici.  Entrambi comportano dei costi aggiuntivi per la catena di alimentazione.

2. Considerando in modo sicuro i dispositivi nella catena di alimentazione e gestendoli in un secondo momento nella distribuzione diventa un'attività di uno a uno per ogni coppia di chiave nel dispositivo dalla generazione del punto del certificato univoco del dispositivo (chiave privata) al ritiro del dispositivo. Ciò preclude la gestione del gruppo di dispositivi a meno che il concetto di gruppi sia in qualche modo integrato in modo esplicito nel processo. La gestione della contabilità e del ciclo di vita del dispositivo, pertanto, diventa un onere difficile di operazioni.  In questo esempio, la Società-X dovrebbe occuparsi del carico di lavoro.

L'autenticazione del certificato della CA X.509 offre soluzioni eleganti per affrontare le richieste elencate tramite l'uso di catene di certificati.  Una catena di certificati è il risultato di una CA che firma una CA intermedia che a sua volta firma un'altra CA intermedia e così via fino a che una CA intermedia finale non firma un dispositivo.  In questo esempio, la Società-X firma l'Azienda-Y, che a sua volta firma il Tecnico-Z che infine firma lo Smart-X-Widget.

![img-cert-chain-hierarchy](./media/cert-chain-hierarchy.png)

La precedente sequenza di certificati nella catena presenta la logica consegna dell'autorità.  Molte catene di alimentazione seguono questa consegna logica in base alla quale ogni autorità di certificazione intermedia viene firmata nella catena mentre riceve tutti i certificati della CA in upstream e l'ultima CA intermedia infine firma ogni dispositivo e inserisce tutti i certificati di autorità dalla catena al dispositivo. Questo è comune quando la società di produzione del contratto con una gerarchia di aziende contratta un'azienda specifica per eseguire la produzione.  Nel corso della gerarchia ci potrebbero essere più livelli (ad esempio, per tipo di prodotto/geografia linea di produzione/tipo), solo l'azienda alla fine ottiene di interagire con il dispositivo, ma la catena è gestita dalla parte superiore della gerarchia.

Delle catene alternative potrebbero avere diverse CA intermedie che interagiscono con il dispositivo, in tal caso l'autorità di certificazione che interagisce con il dispositivo inserisce il contenuto della catena di certificati in quel punto.  I modelli ibridi sono anche possibili laddove solo alcune delle autorità di certificazione abbiano un'interazione fisica con il dispositivo.

In questo esempio, sia l'Azienda-Y sia il Tecnico-Z interagiscono con lo Smart-X-Widget.  Anche se la Società-X è la proprietaria dello Smart-X-Widget, non interagisce effettivamente con esso nell'intera catena di alimentazione.  La catena di certificati di attendibilità per lo Smart-X-Widget comporta pertanto che la Società-X firmi per l'Azienda-Y che a sua volta firma per il Tecnico-Z che fornirà quindi la firma finale allo Smart-X-Widget. La produzione e l'installazione dello Smart-X-Widget comporta che l'Azienda-Y e il Tecnico-Z usino i loro rispettivi certificati CA intermedi per firmare ogni Smart-X-Widget. Il risultato finale di questo processo è lo Smart-X-widget con certificati univoci del dispositivo e una catena di certificati attendibili che va fino al certificato della CA della Società-X.

![img-cert-mfr-chain](./media/cert-mfr-chain.png)

Questo è una buona occasione per esaminare il valore del metodo della CA X.509.  Invece di pre-generare e passare i certificati per ogni Smart-X-Widget nella catena di alimentazione, la Società-X firma solo una volta per l'Azienda-Y.  Invece di tenere traccia di ogni dispositivo in tutto il ciclo di vita dei dispositivi, la Società-X potrebbe non monitorare né gestire i dispositivi tramite i gruppi che emergono naturalmente dal processo della catena di alimentazione, ad esempio, i dispositivi installati dal Tecnico-Z dopo luglio di un qualunque anno.

Ultima cosa, ma non meno importante, il metodo di autenticazione della CA garantisce una contabilità protetta nella catena di alimentazione del dispositivo. A causa del processo della catena di certificati, le azioni di ogni membro nella catena sono registrate e crittograficamente e verificabili.

Questo processo si basa su alcuni presupposti che devono essere resi visibili per motivi di completezza.  È necessaria la creazione indipendente della coppia di chiavi pubblica/privata univoche del dispositivo e che la chiave privata sia protetta all'interno del dispositivo.  Fortunatamente esistono dei chip in silicone protetti sotto forma di moduli di protezione hardware (HSM) in grado di generare chiavi internamente e proteggere le chiavi private.  La Società X deve solo aggiungere uno di tali chips alla distinta di base dei componenti del Smart-X-Widget.

## <a name="device-connection"></a>Connessione del dispositivo

Le sezioni precedenti hanno lavorato per la costruzione della connessione del dispositivo.  Registrando semplicemente un certificato della CA X.509 all'hub IoT una volta, come possono potenzialmente milioni di dispositivi connettersi e ottenere l'autenticazione dal primo momento?  Semplice. Tramite lo stesso caricamento del certificato e lo stesso flusso di proof-of-possession che si è verificato in precedenza con la registrazione del certificato della CA X.509.

I dispositivi prodotti per l'autenticazione della CA X.509 sono dotati di certificati univoci del dispositivo e di una catena di certificati dalle rispettive catena di alimentazione della produzione.  La connessione del dispositivo, anche per la prima volta, ha luogo in un processo a due fasi: il caricamento della catena di certificati e la proof-of-possession.

Durante il caricamento della catena di certificati, il dispositivo carica il proprio certificato univoco del dispositivo insieme alla catena di certificati installata all'interno di esso nell'hub IoT.  Usando il certificato della CA X.509 pre-registrato, l'hub IoT può convalidare crittograficamente un paio di elementi, che la catena di certificati caricata è internamente coerente e che la catena è stata originata dal proprietario valido del certificato della CA X.509.  È quanto accade con il processo di registrazione della CA di X.509, l'hub IoT dovrebbe avviare un processo di richiesta-risposta di proof-of-possession per verificare che la catena e pertanto il certificato del dispositivo appartenga effettivamente al dispositivo che lo sta caricando.  Agisce in questo modo generando una richiesta casuale che deve essere firmata dal dispositivo usando la chiave privata per la convalida da parte di hub IoT.   Una risposta corretta attiva hub IoT per accettare il dispositivo come autentico e concedergli la connessione.

In questo esempio, ciascun Smart-X-Widget potrebbe caricare il proprio certificato univoco del dispositivo insieme ai certificati della CA X.509 dell'Azienda-Y e del Tecnico-Z e quindi rispondere alla richiesta di proof-of-possession dall'hub IoT.

![img-device-pop-flow](./media/device-pop-flow.png)

Si noti che la base della relazione di attendibilità risiede nelle chiavi private di protezione che comprendono le chiavi private del dispositivo.  Pertanto è fondamentale sottolineare l'importanza dei chip di sicurezza in silicone sotto forma di moduli di protezione hardware (HSM) per la protezione delle chiavi private del dispositivo e la procedura consigliata complessiva di non condividere mai le chiavi private, come ad esempio affidare a un'altra azienda la propria chiave privata.

