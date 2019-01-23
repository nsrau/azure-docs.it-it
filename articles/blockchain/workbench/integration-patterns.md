---
title: Modelli di integrazione dei contratti intelligenti in Azure Blockchain Workbench
description: Panoramica dei modelli di integrazione dei contratti intelligenti in Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 11e0e1436e3f640c30fec5e8d6fd9ca10adbd707
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330468"
---
# <a name="smart-contract-integration-patterns"></a>Modelli di integrazione dei contratti intelligenti

I contratti intelligenti spesso rappresentano un flusso di lavoro aziendale che deve essere integrato con dispositivi e sistemi esterni.

I requisiti di questi flussi di lavoro comprendono l'esigenza di avviare transazioni in un libro mastro distribuito che includono dati da un sistema, un servizio o un dispositivo esterno. I sistemi esterni devono inoltre rispondere agli eventi originati dai contratti intelligenti in un libro mastro distribuito.

L'integrazione della messaggistica e dell'API REST invia le transazioni dai sistemi esterni ai contratti intelligenti inclusi in un'applicazione Azure Blockchain Workbench. Invia anche le notifiche degli eventi ai sistemi esterni in base alle modifiche che hanno luogo all'interno di un'applicazione.

Per scenari di integrazione dei dati, Azure Blockchain Workbench include un set di viste di database che uniscono una combinazione di dati transazionali dalla blockchain e di metadati sulle applicazioni e i contratti intelligenti.

Inoltre, alcuni scenari, come quelli correlati alla supply chain o ai media, potrebbero richiedere anche l'integrazione di documenti. Anche se Azure Blockchain Workbench non fornisce chiamate API per la gestione diretta dei documenti, i documenti possono essere incorporati in un'applicazione blockchain. Questa sezione include anche tale modello.

In questa sezione sono inclusi i modelli identificati per l'implementazione di ognuno di questi tipi di integrazioni nelle proprie soluzioni end-to-end.

## <a name="rest-api-based-integration"></a>Integrazione basata sull'API REST

Le funzionalità nell'applicazione Web generata da Azure Blockchain Workbench vengono esposte tramite l'API REST. Le funzionalità includono il caricamento, la configurazione e l'amministrazione delle applicazioni in Azure Blockchain Workbench, l'invio delle transazioni a un libro mastro distribuito e l'esecuzione di query sui metadati dell'applicazione e sui dati del libro mastro.

L'API REST viene usata principalmente per i client interattivi, come le applicazioni Web, per dispositivi mobili e bot.

In questa sezione vengono esaminati i modelli focalizzati sugli aspetti dell'API REST che inviano le transazioni a un libro mastro distribuito e i modelli che eseguono query sui dati sulle transazioni dal database SQL *fuori dalla catena* di Azure Blockchain Workbench.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Invio di transazioni a un libro mastro distribuito da un sistema esterno

L'API REST di Azure Blockchain Workbench invia richieste autenticate per l'esecuzione di transazioni in un libro mastro distribuito.

![Invio di transazioni a un libro mastro distribuito](./media/integration-patterns/send-transactions-ledger.png)

L'esecuzione di transazioni segue la procedura illustrata in precedenza, dove:

-   L'applicazione esterna esegue l'autenticazione in Azure Active Directory, di cui è stato eseguito il provisioning nell'ambito della distribuzione di Azure Blockchain Workbench.
-   Gli utenti autorizzati ricevono un token di connessione che può essere inviato all'API con le richieste.
-   Le applicazioni esterne effettuano chiamate all'API REST tramite il token di connessione.
-   L'API REST crea un pacchetto della richiesta come un messaggio e lo invia al bus di servizio. Da qui, il messaggio viene recuperato, firmato e inviato al libro mastro distribuito appropriato.
-   L'API REST effettua una richiesta al database SQL di Azure Blockchain Workbench per la registrazione della richiesta e la determinazione dello stato di provisioning corrente.
-   Il database SQL restituisce lo stato di provisioning e la chiamata API restituisce l'ID all'applicazione esterna che lo ha richiesto.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Esecuzione di query sui metadati di Blockchain Workbench e le transazioni del libro mastro distribuito

L'API REST di Azure Blockchain Workbench invia richieste autenticate per l'esecuzione di query sui dettagli relativi all'esecuzione dei contratti intelligenti in un libro mastro distribuito.

![Query dei metadati](./media/integration-patterns/querying-metadata.png)

Le query vengono eseguite in base alla procedura illustrata in precedenza, dove:

1. L'applicazione esterna esegue l'autenticazione in Azure Active Directory, di cui è stato eseguito il provisioning nell'ambito della distribuzione di Azure Blockchain Workbench.
2. Gli utenti autorizzati ricevono un token di connessione che può essere inviato all'API con le richieste.
3. Le applicazioni esterne effettuano chiamate all'API REST tramite il token di connessione.
4. L'API REST esegue una query sui dati per la richiesta dal database di SQL e li restituisce al client.

## <a name="messaging-integration"></a>Integrazione della messaggistica

L'integrazione della messaggistica facilita l'interazione con i sistemi, i servizi e i dispositivi in cui non è possibile o consigliabile usare un accesso interattivo. L'integrazione della messaggistica si concentra su due tipi di messaggi: i messaggi che richiedono l'esecuzione di transazioni in un libro mastro distribuito e gli eventi esposti da tale libro mastro quando le transazioni hanno avuto luogo.

L'integrazione della messaggistica, che è incentrata sull'esecuzione e sul monitoraggio di transazioni correlate alla creazione di utenti, alla creazione di contratti e all'esecuzione di transazioni sui contratti, viene usata principalmente dai sistemi back-end *headless*.

In questa sezione vengono esaminati i modelli focalizzati sugli aspetti dell'API basata su messaggi che inviano le transazioni a un libro mastro distribuito e i modelli che rappresentano i messaggi di evento esposti dal libro mastro distribuito sottostante.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Recapito di eventi unidirezionale da un contratto intelligente a un consumer eventi 

In questo scenario, si verifica un evento in un contratto intelligente, ad esempio una modifica dello stato o l'esecuzione di un tipo specifico di transazione. Questo evento viene trasmesso tramite una griglia di eventi ai consumer downstream e tali consumer eseguono quindi le azioni appropriate.

In uno scenario di questo tipo, quando si verifica una transazione, un consumer riceve un avviso ed esegue un'azione, ad esempio registrando le informazioni in un database SQL o in Common Data Service. Questo scenario è lo stesso modello che segue Workbench per popolare il database SQL *fuori dalla catena*.

Un altro esempio è costituito da transazioni su contratti intelligenti che passano a uno stato specifico, ad esempio quando un contratto diventa *OutOfCompliance*. Quando si verifica questa modifica dello stato, potrebbe essere attivato l'invio di un avviso al cellulare di un amministratore.

![Recapito di eventi unidirezionale](./media/integration-patterns/one-way-event-delivery.png)

Questo scenario si basa sulla procedura illustrata in precedenza, dove:

-   Il contratto intelligente passa a un nuovo stato e invia un evento al libro mastro.
-   Il libro mastro riceve l'evento e lo recapita ad Azure Blockchain Workbench.
-   Azure Blockchain Workbench è sottoscritto agli eventi del libro mastro e riceve l'evento.
-   Azure Blockchain Workbench pubblica l'evento per i sottoscrittori nella griglia di eventi.
-   I sistemi esterni sono sottoscritti alla griglia di eventi, usano il messaggio ed eseguono le azioni appropriate.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Recapito di eventi unidirezionale di un messaggio da un sistema esterno a un contratto intelligente

È inoltre disponibile uno scenario in cui il flusso avviene nella direzione opposta. In questo caso, viene generato un evento da un sensore o un sistema esterno e i dati originati da tale evento devono essere inviati a un contratto intelligente.

Un esempio comune è il recapito di dati dai mercati finanziari, come i prezzi di prodotti, titoli o obbligazioni, a un contratto intelligente.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Recapito diretto da Azure Blockchain Workbench nel formato previsto

Alcune applicazioni sono progettate per l'integrazione con Azure Blockchain Workbench e generano e inviano direttamente i messaggi nei formati previsti.

![Recapito diretto](./media/integration-patterns/direct-delivery.png)

Questo recapito si basa sulla procedura illustrata in precedenza, dove:

-   Si verifica un evento in un sistema esterno che attiva la creazione di un messaggio per Azure Blockchain Workbench.
-   Il sistema esterno include codice per la creazione del messaggio in un formato noto e lo invia direttamente al bus di servizio.
-   Azure Blockchain Workbench è sottoscritto agli eventi del bus di servizio e recupera il messaggio.
-   Azure Blockchain Workbench avvia una chiamata al libro mastro, inviando i dati dal sistema esterno a un contratto specifico.
-   Al momento della ricezione del messaggio, il contratto passa a un nuovo stato.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Recapito di un messaggio in un formato sconosciuto ad Azure Blockchain Workbench

Alcuni sistemi non possono essere modificati per recapitare i messaggi nei formati standard usati da Azure Blockchain Workbench. In questi casi, spesso è possibile usare i meccanismi e i formati dei messaggi esistenti di questi sistemi. In particolare, i tipi di messaggi nativi di questi sistemi possono essere trasformati tramite app per la logica, funzioni di Azure o altro codice personalizzato in modo che corrispondano a uno dei formati di messaggistica standard previsti.

![Formato del messaggio sconosciuto](./media/integration-patterns/unknown-message-format.png)

Si basa sulla procedura illustrata in precedenza, dove:

-   Si verifica un evento in un sistema esterno che attiva la creazione di un messaggio.
-   Tramite un'app per la logica o codice personalizzato, il messaggio viene ricevuto e trasformato in un messaggio formattato standard di Azure Blockchain Workbench.
-   L'app per la logica invia il messaggio trasformato direttamente al bus di servizio.
-   Azure Blockchain Workbench è sottoscritto agli eventi del bus di servizio e recupera il messaggio.
-   Azure Blockchain Workbench avvia una chiamata al libro mastro, inviando i dati dal sistema esterno a una funzione specifica nel contratto.
-   La funzione viene eseguita e in genere modifica lo stato. La modifica dello stato fa avanzare il flusso di lavoro aziendale che corrisponde al contratto intelligente, consentendo l'esecuzione di altre funzioni in base alle esigenze.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Transizione del controllo a un processo esterno e attesa del completamento

Esistono scenari in cui un contratto intelligente deve interrompere l'esecuzione interna e passare il controllo a un processo esterno. Al completamento di tale processo esterno, viene inviato un messaggio al contratto intelligente e l'esecuzione continua all'interno di quest'ultimo.

#### <a name="transition-to-the-external-process"></a>Transizione al processo esterno

Questo modello viene in genere implementato usando l'approccio seguente:

-   Il contratto intelligente passa a un determinato stato. In questo stato, non è possibile eseguire alcuna funzione (o può esserne eseguito solo un numero limitato) finché un sistema esterno non effettua l'azione desiderata.
-   La modifica dello stato viene esposta come un evento per un consumer downstream.
-   Il consumer downstream riceve l'evento e attiva l'esecuzione di codice esterno.

![Transizione del controllo al processo esterno](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Restituzione del controllo dal contratto intelligente

A seconda della possibilità di personalizzare il sistema esterno, potrebbe essere possibile o meno recapitare i messaggi in uno dei formati standard previsti da Azure Blockchain Workbench. La capacità dei sistemi esterni di generare uno di questi messaggi determina quale dei due percorsi di ritorno seguenti viene intrapreso.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Recapito diretto da Azure Blockchain Workbench nel formato previsto

![](./media/integration-patterns/direct-delivery.png)

In questo modello la comunicazione con il contratto e la successiva modifica dello stato hanno luogo secondo il processo precedente, in base al quale:

-   Al raggiungimento del completamento o di un'attività cardine specifica nell'esecuzione del codice esterno, viene inviato un evento al bus di servizio connesso ad Azure Blockchain Workbench.

-   Per i sistemi che non possono essere adattati direttamente per la scrittura di un messaggio conforme a quanto previsto dall'API, il messaggio viene trasformato.

-   Il contenuto del messaggio è incluso in un pacchetto e inviato a una funzione specifica nel contratto intelligente. Questo recapito viene eseguito per conto dell'utente associato al sistema esterno.

-   La funzione viene eseguita e in genere modifica lo stato. La modifica dello stato fa avanzare il flusso di lavoro aziendale che corrisponde al contratto intelligente, consentendo l'esecuzione di altre funzioni in base alle esigenze.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Recapito di un messaggio in un formato sconosciuto ad Azure Blockchain Workbench

![Formato del messaggio sconosciuto](./media/integration-patterns/unknown-message-format.png)

In questo modello in cui non è possibile inviare direttamente un messaggio in un formato standard, la comunicazione con il contratto e la successiva modifica dello stato hanno luogo secondo il processo precedente, in base al quale:

1.  Al raggiungimento del completamento o di un'attività cardine specifica nell'esecuzione del codice esterno, viene inviato un evento al bus di servizio connesso ad Azure Blockchain Workbench.
2.  Tramite un'app per la logica o codice personalizzato, il messaggio viene ricevuto e trasformato in un messaggio formattato standard di Azure Blockchain Workbench.
3.  L'app per la logica invia il messaggio trasformato direttamente al bus di servizio.
4.  Azure Blockchain Workbench è sottoscritto agli eventi del bus di servizio e recupera il messaggio.
5.  Azure Blockchain Workbench avvia una chiamata al libro mastro, inviando i dati dal sistema esterno a un contratto specifico.
6. Il contenuto del messaggio è incluso in un pacchetto e inviato a una funzione specifica nel contratto intelligente. Questo recapito viene eseguito per conto dell'utente associato al sistema esterno.
7.  La funzione viene eseguita e in genere modifica lo stato. La modifica dello stato fa avanzare il flusso di lavoro aziendale che corrisponde al contratto intelligente, consentendo l'esecuzione di altre funzioni in base alle esigenze.

## <a name="iot-integration"></a>Integrazione IoT

Uno scenario di integrazione comune consiste nell'inclusione in un contratto intelligente di dati di telemetria recuperati da sensori. In base ai dati inviati dai sensori, i contratti intelligenti possono eseguire azioni informate e modificare lo stato del contratto.

Ad esempio, l'aumento della temperatura oltre i 40 gradi in un veicolo usato per il trasporto di medicinali potrebbe influire sull'efficacia dei farmaci e causare un problema di sicurezza pubblica se il problema non venisse rilevato e il veicolo rimosso dalla supply chain. Se una persona guidasse la propria auto a una velocità superiore ai 160 km all'ora, le informazioni del sensore risultanti potrebbero attivare un annullamento dell'assicurazione da parte della relativa compagnia. Se l'auto fosse a noleggio, i dati del GPS potrebbero indicare quando il conducente esce dall'area geografica coperta dal contratto di noleggio e addebitare una penale.

La difficolta sta nel fatto che questi sensori possono inviare costantemente dati e non è appropriato inviare tutti questi dati a un contratto intelligente. Un approccio tipico consiste nel limitare il numero di messaggi inviati alla blockchain, recapitando allo stesso tempo tutti i messaggi a un archivio secondario. Ad esempio, possono essere recapitati solo i messaggi ricevuti a intervalli fissi, ad esempio una volta all'ora, e quando un valore contenuto non rientra nell'intervallo previsto per un contratto intelligente. Il controllo dei valori che non rientrano nelle tolleranze assicura che i dati rilevanti per la logica di business dei contratti vengano ricevuti ed eseguiti. Il controllo del valore in base all'intervallo conferma che il sensore sta ancora inviando dati. Tutti i dati vengono inviati a un archivio di reporting secondario per consentire la creazione di report, l'analisi e l'apprendimento automatico. Ad esempio, se acquisire ogni minuto le letture dei sensori di un sistema GPS potrebbe non essere necessario per un contratto intelligente, ciò potrebbe fornire dati interessanti da usare nei report o per la rappresentazione degli itinerari su una mappa.

Nella piattaforma Azure l'integrazione con i dispositivi in genere viene eseguita tramite l'hub IoT. L'hub IoT offre il routing dei messaggi in base al contenuto e consente il tipo di funzionalità descritte in precedenza.

![Messaggi IoT](./media/integration-patterns/iot.png)

Il processo illustra un modello:

-   Un dispositivo comunica direttamente o tramite un gateway sul campo con l'hub IoT.
-   L'hub IoT riceve i messaggi e li valuta, ad esempio rispetto alle route stabilite per il controllo del contenuto del messaggio. *Il sensore segnala una temperatura superiore a 10 gradi?*
-   L'hub IoT invia i messaggi che soddisfano i criteri a un bus di servizio definito per la route.
-   Un'app per la logica o altro codice è in ascolto del bus di servizio stabilito dall'hub IoT per la route.
-   L'app per la logica o il codice recupera e trasforma il messaggio in un formato noto.
-   Il messaggio trasformato, ora in formato standard, viene inviato al bus di servizio per Azure Blockchain Workbench.
-   Azure Blockchain Workbench è sottoscritto agli eventi del bus di servizio e recupera il messaggio.
-   Azure Blockchain Workbench avvia una chiamata al libro mastro, inviando i dati dal sistema esterno a un contratto specifico.
-   Al momento della ricezione del messaggio, il contratto valuta i dati e può modificare lo stato in base al risultato della valutazione. Ad esempio, per una temperatura elevata, potrebbe modificare lo stato in *Non conforme*.

## <a name="data-integration"></a>Integrazione dei dati

Oltre alle API REST e basata su messaggi, Azure Blockchain Workbench fornisce anche l'accesso a un database SQL popolato con i metadati sulle applicazioni e sui contratti, nonché i dati transazionali dal libri mastri distribuiti.

![Integrazione dei dati](./media/integration-patterns/data-integration.png)

L'integrazione dei dati è ben nota:

-   Azure Blockchain Workbench archivia i metadati sulle applicazioni, i flussi di lavoro, i contratti e le transazioni nell'ambito del normale funzionamento.
-   Sistemi o strumenti esterni forniscono una o più finestre di dialogo per semplificare la raccolta delle informazioni relative al database, ad esempio nome del server di database, nome del database, tipo di autenticazione, credenziali di accesso e viste del database da usare.
-   Vengono scritte query sulle viste del database SQL per facilitare l'uso downstream da parte di sistemi esterni, servizi, report, strumenti di sviluppo e strumenti di produttività aziendali.

## <a name="storage-integration"></a>Integrazione dell'archiviazione

Molti scenari possono richiedere la necessità di incorporare file attestabili. Per diversi motivi, non è appropriato inserire file in una blockchain. Al contrario, un approccio comune è quello di eseguire un hash crittografico (ad esempio SHA-256) su un file e condividere tale hash in un libro mastro distribuito. L'esecuzione dell'hash in un secondo momento deve restituire lo stesso risultato. Se il file viene modificato, anche solo con la modifica di un pixel in un'immagine, l'hash restituisce un valore diverso.

![Integrazione dell'archiviazione](./media/integration-patterns/storage-integration.png)

Il modello può essere implementato quando:

-   Un sistema esterno rende persistente un file in un meccanismo di archiviazione come Archiviazione di Azure.
-   Viene generato un hash con il file o il file e i metadati associati, ad esempio un identificatore del proprietario, l'URL del file e così via.
-   Il valore hash e gli eventuali metadati vengono inviati a una funzione di un contratto intelligente, ad esempio *FileAdded*
-   In seguito, sarà possibile eseguire nuovamente l'hash del file e dei metadati e confrontarlo con i valori archiviati nel libro mastro.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Prerequisiti per l'implementazione di modelli di integrazione tramite l'API REST e basata su messaggi

Per facilitare l'interazione di un sistema o un dispositivo esterno con il contratto intelligente tramite l'API REST o basata su messaggi, devono essere soddisfatte le condizioni seguenti:

1. In Azure Active Directory per il consorzio, viene creato un account che rappresenta il sistema o il dispositivo esterno.
2. Uno o più contratti intelligenti appropriati per l'applicazione Azure Blockchain Workbench hanno funzioni definite per accettare gli eventi dal sistema o dispositivo esterno.
3. Il file di configurazione dell'applicazione per il contratto intelligente contiene il ruolo a cui è assegnato il sistema o il dispositivo.
4. Il file di configurazione dell'applicazione per il contratto intelligente identifica gli stati in cui questa funzione viene chiamata dal ruolo definito.
5. Il file di configurazione dell'applicazione e i relativi contratti intelligenti vengono caricati in Azure Blockchain Workbench.

Dopo il caricamento dell'applicazione, l'account di Azure Active Directory per il sistema esterno viene assegnato al contratto e al ruolo associato.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Test dei flussi di integrazione dei sistemi esterni prima di scrivere il codice di integrazione 

L'integrazione con sistemi esterni è un requisito essenziale in numerosi scenari. È consigliabile essere in grado di convalidare la progettazione dei contratti intelligenti prima dello sviluppo del codice per l'integrazione con i sistemi esterni o in parallelo con questa attività.

L'uso di Azure Active Directory (Azure AD) può accelerare notevolmente la produttività degli sviluppatori e il time-to-value. In particolare, l'integrazione del codice con un sistema esterno può richiedere una considerevole quantità di tempo. Grazie ad Azure AD e alla generazione automatica dell'esperienza utente tramite Azure Blockchain Workbench, è possibile consentire agli sviluppatori di accedere a Blockchain Workbench come sistema esterno e di popolare i valori dal sistema esterno tramite l'esperienza utente. È possibile sviluppare e testare rapidamente le idee in un ambiente per il modello di verifica prima che venga scritto il codice di integrazione per i sistemi esterni.
