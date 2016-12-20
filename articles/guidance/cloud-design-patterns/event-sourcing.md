---
title: Modello di origine eventi | Azure | Documentazione Microsoft
description: Usare un archivio di solo accodamento per registrare la serie completa di eventi che descrivono le azioni eseguite sui dati di un dominio.
categories:
- data-management
- performance-scalability
keywords: schema progettuale
author: dragon119
manager: bennage
ms.service: guidance
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.author: mwasson
ms.date: 11/14/2016
translationtype: Human Translation
ms.sourcegitcommit: c4963489ca53a90cab480116ec0a044501ccb5e9
ms.openlocfilehash: 30aa02047aba637bd4e55519e04bb6f1fda3fdf3

---
   
# <a name="event-sourcing"></a>Origine eventi

Anziché archiviare solo lo stato corrente dei dati in un dominio, usare un archivio di solo accodamento per registrare la serie completa di azioni eseguite sui dati.
L'archivio svolge la funzione di sistema di registrazione e consente di materializzare gli oggetti del dominio. Contribuisce quindi a semplificare le attività in domini complessi, evitando la necessità di sincronizzare il modello dati e il dominio aziendale e migliorando le prestazioni, la scalabilità e la velocità di risposta. Può inoltre garantire coerenza ai dati transazionali e mantenere dati di cronologia e audit trail completi che consentono di eseguire azioni di compensazione.

## <a name="context-and-problem"></a>Contesto e problema

La maggior parte delle applicazioni interagisce con i dati e, in genere, le applicazioni mantengono lo stato corrente dei dati lasciando agli utenti la facoltà di aggiornarli. Nel tradizionale modello CRUD (Create, Read, Update, and Delete), ad esempio, il processo standard prevede la lettura dei dati dall'archivio, l'applicazione di alcune modifiche e l'aggiornamento dello stato corrente dei dati con i nuovi valori, spesso tramite transazioni che bloccano i dati.

L'approccio CRUD presenta tuttavia alcune limitazioni:

- Eseguendo le operazioni di aggiornamento direttamente nell'archivio dati, i sistemi CRUD generano un sovraccarico di elaborazione che può rallentare le prestazioni e la velocità di risposta e limitare la scalabilità.

- In un dominio in collaborazione con molti utenti simultanei, i conflitti di aggiornamento sono più frequenti poiché le operazioni di aggiornamento vengono eseguite su un unico elemento di dati.

- A meno che non sia presente un meccanismo di controllo aggiuntivo che registra i dettagli di ogni operazione in un log separato, quindi, la cronologia viene persa.

>  Per informazioni più dettagliate sui limiti dell'approccio CRUD, vedere [CRUD, Only When You Can Afford It](https://msdn.microsoft.com/library/ms978509.aspx) (CRUD, solo quando è accessibile). 

## <a name="solution"></a>Soluzione

Il modello di origine eventi definisce un approccio alla gestione delle operazioni sui dati determinato da una sequenza di eventi, ognuno dei quali registrato in un archivio di solo accodamento. Il codice dell'applicazione invia una serie di eventi che descrivono in modo imperativo ogni azione eseguita sui dati nell'archivio eventi, in cui sono salvati in modo permanente. Ogni evento rappresenta un set di modifiche ai dati (ad esempio `AddedItemToOrder`).

Gli eventi vengono salvati in modo permanente in un archivio eventi che svolge la funzione di sistema di registrazione (origine dati autorevole) dello stato corrente dei dati. L'archivio eventi pubblica in genere questi eventi in modo che i consumer vengano informati e, se necessario, possano gestirli. I consumer, ad esempio, possono iniziare le attività che si applicano alle operazioni sugli eventi di altri sistemi oppure eseguire qualsiasi altra azione associata necessaria per completare l'operazione. Tenere presente che il codice dell'applicazione che genera gli eventi è separato dai sistemi che sottoscrivono gli eventi.

Gli usi tipici degli eventi pubblicati dall'archivio eventi prevedono la gestione di viste materializzate di entità come azioni nell'applicazione e per l'integrazione con sistemi esterni. Un sistema, ad esempio, può gestire una vista materializzata di tutti gli ordini clienti usata per popolare parti dell'interfaccia utente. Quando l'applicazione aggiunge nuovi ordini, aggiunge o rimuove elementi nell'ordine o aggiunge le informazioni di spedizione, gli eventi che descrivono queste modifiche possono essere gestiti e usati per aggiornare la [vista materializzata](materialized-view.md).

In qualsiasi momento, inoltre, le applicazioni possono leggere la cronologia degli eventi e usarla per materializzare lo stato corrente di un'entità riproducendo e consumando tutti gli eventi correlati all'entità. Questa operazione può verificarsi su richiesta per materializzare un oggetto di dominio quando si gestisce una richiesta o tramite un'attività pianificata in modo che lo stato dell'entità possa essere archiviato come una vista materializzata per supportare il livello di presentazione.

La figura illustra una panoramica del modello, incluse alcune delle opzioni relative all'uso del flusso eventi, tra cui la creazione di una vista materializzata, l'integrazione di eventi con applicazioni e sistemi esterni e la riproduzione di eventi per poter creare proiezioni dello stato corrente di specifiche entità.

![Panoramica ed esempio del modello di origine eventi](images/event-sourcing-overview.png)


Il modello di origine eventi offre i vantaggi seguenti:

Gli eventi non sono modificabili e possono essere memorizzati con un'operazione di solo accodamento. L'interfaccia utente, il flusso di lavoro o il processo che ha avviato un evento può continuare e le attività che gestiscono gli eventi possono essere eseguite in background. Questo vantaggio, combinato con l'assenza di conflitti durante l'elaborazione delle transazioni, può migliorare notevolmente le prestazioni e la scalabilità delle applicazioni, soprattutto in termini di interfaccia utente o livello di presentazione.

Gli eventi sono oggetti semplici che descrivono un'azione che si è verificata, insieme a eventuali dati associati necessari per descrivere l'azione rappresentata dall'evento. Gli eventi non aggiornano direttamente un archivio dati. Vengono semplicemente registrati per essere gestiti al momento opportuno. Questi fattori possono semplificare l'implementazione e la gestione.

Gli eventi, in genere, sono facilmente comprensibili per gli esperti del settore, mentre la [mancata corrispondenza di impedenza object-relational](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) può rendere difficile la comprensione delle tabelle di database complesse. Le tabelle sono costrutti artificiali che rappresentano lo stato corrente del sistema, non gli eventi che si sono verificati. 

La funzione di origine eventi contribuisce a impedire conflitti generati da aggiornamenti simultanei in quanto evita la necessità di aggiornare direttamente gli oggetti nell'archivio dati. La progettazione del modello di dominio, tuttavia, non prevede ancora la protezione da richieste che possono determinare uno stato incoerente.

L'archiviazione di solo accodamento degli eventi offre un audit trail che consente di monitorare le azioni intraprese in un archivio dati, di rigenerare lo stato corrente come proiezioni o viste materializzate riproducendo gli eventi in qualsiasi momento e di semplificare le operazioni di test e debug del sistema. La necessità di usare eventi di compensazione per annullare le modifiche fornisce inoltre una cronologia delle modifiche annullate, possibilità che non sarebbe consentita se il modello archiviasse semplicemente lo stato corrente. L'elenco degli eventi può essere usato anche per analizzare le prestazioni delle applicazioni e individuare tendenze di comportamento degli utenti, nonché per ottenere altre informazioni aziendali utili.

L'archivio eventi genera eventi e le attività eseguono operazioni in risposta agli eventi. È questa separazione tra le attività e gli eventi a garantire flessibilità ed estensibilità. Le attività sono a conoscenza del tipo di evento e dei dati relativi all'evento, ma non dell'operazione che l'evento ha generato. Ogni evento, inoltre, può essere gestito da più attività. Questo aspetto semplifica l'integrazione con altri servizi e sistemi che restano in ascolto solo di nuovi eventi generati dall'archivio eventi. Gli eventi che danno origine a nuovi eventi, tuttavia, tendono a essere di livello molto basso e può essere necessario generare eventi di integrazione specifici.

>  Il modello di origine eventi viene spesso combinato con il modello CQRS per l'esecuzione di attività di gestione dati in risposta agli eventi e per la materializzazione delle viste a partire da eventi archiviati.

## <a name="issues-and-considerations"></a>Considerazioni e problemi

Prima di decidere come implementare questo modello, considerare quanto segue:

Il sistema acquisirà la coerenza finale solo durante la creazione di viste materializzate o la generazione di proiezioni di dati tramite la riproduzione di eventi. Si verifica un minimo ritardo tra il momento in cui un'applicazione aggiunge un evento all'archivio eventi in seguito alla gestione di una richiesta, il momento in cui l'evento viene pubblicato e quello in cui viene gestito dai consumer. Durante questo intervallo di tempo è possibile che siano giunti nell'archivio nuovi eventi che descrivono ulteriori modifiche alle entità.

> [!NOTE]
>  Per altre informazioni sulla coerenza finale, vedere [Nozioni di base sulla coerenza dei dati](https://msdn.microsoft.com/library/dn589800.aspx).

L'archivio eventi è un'origine permanente di informazioni e i dati dell'evento, quindi, non dovrebbero essere mai aggiornati. L'unico modo per aggiornare un'entità in modo da annullare una modifica è quello di aggiungere un evento di compensazione all'archivio eventi. Se deve essere modificato il formato, anziché i dati, degli eventi persistenti, ad esempio durante una migrazione, potrebbe essere difficile combinare eventi esistenti nell'archivio con la nuova versione. È possibile che sia necessario scorrere tutti gli eventi per modificarli in modo da uniformarli al nuovo formato oppure aggiungere nuovi eventi che usano il nuovo formato. Valutare l'opportunità di usare uno stamp di versione in ogni versione dello schema eventi per mantenere sia il nuovo che il vecchio formato.

È possibile che nell'archivio eventi vengano archiviati eventi anche da parte di applicazioni multithread e da più istanze delle applicazioni. La coerenza degli eventi nell'archivio eventi è di fondamentale importanza, così come l'ordine degli eventi che interessano un'entità specifica (l'ordine in cui vengono apportate le modifiche influisce sullo stato corrente dell'entità). L'aggiunta di un timestamp a ogni evento contribuisce a evitare problemi. Un'altra procedura comune è quella di annotare ogni evento generato da una richiesta con un identificatore incrementale. Se due azioni tentano contemporaneamente di aggiungere eventi per una stessa entità, l'archivio eventi può rifiutare un evento che corrisponde a un identificatore di entità e a un identificatore di evento esistenti.

Non esiste un approccio standard o un meccanismo esistente, ad esempio query SQL, per leggere gli eventi in modo da ottenere informazioni. Un flusso di eventi è l'unico tipo di dati che è possibile estrarre usando un identificatore di eventi come criterio. L'ID evento è generalmente associato a entità individuali. Lo stato corrente di un'entità può essere determinato solo riproducendo tutti gli eventi associati all'entità sullo stato originale dell'entità.

La lunghezza di ogni flusso di eventi influisce sulle modalità di gestione e di aggiornamento del sistema. Se i flussi sono di grandi dimensioni, valutare l'opportunità di creare snapshot a intervalli specifici, ad esempio dopo un determinato numero di eventi. È possibile ottenere lo stato corrente dell'entità mediante lo snapshot e la riproduzione di tutti gli eventi che si sono verificati a partire da quel punto nel tempo. Per altre informazioni sulla creazione di snapshot di dati, vedere la sezione [Snapshot del sito Web di Martin Fowler sull'architettura delle applicazioni aziendali](http://martinfowler.com/eaaDev/Snapshot.html) e l'articolo sulla [replica snapshot master-subordinato](https://msdn.microsoft.com/library/ff650012.aspx). 

Anche se l'origine eventi riduce al minimo il rischio di conflitti tra gli aggiornamenti ai dati, l'applicazione deve comunque essere in grado di gestire eventuali incoerenze derivanti dalla coerenza finale e dalla mancanza di transazioni. Un evento che indica una riduzione delle scorte, ad esempio, può giungere nell'archivio dati mentre viene emesso un ordine per l'elemento mancante, determinando quindi la necessità di riconciliare le due operazioni, ovvero informando il cliente o creando un ordine arretrato.

La pubblicazione degli eventi può essere di tipo "almeno una volta", in modo che i consumer degli eventi siano idempotenti. In questo modo, non è necessario riapplicare l'aggiornamento descritto in un evento se l'evento viene gestito più volte. Se, ad esempio, più istanze di un consumer mantengono un dato aggregato per la proprietà di un'entità, ad esempio il numero totale di ordini, quando si verifica un evento relativo a un nuovo ordine effettuato, è necessario incrementare il dato aggregato in una sola istanza. Sebbene non si tratti di una caratteristica chiave della funzione di origine eventi, costituisce comunque la decisione di implementazione consueta.

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Usare questo modello negli scenari seguenti:

- Quando si vuole acquisire l'intento, lo scopo o il motivo insito nei dati. Le modifiche a un'entità cliente, ad esempio, possono essere acquisite come una serie di tipi di eventi specifici, come _Trasferito_, _Account chiuso_ o _Deceduto_.
 
- Quando è essenziale ridurre al minimo o evitare completamente la presenza di conflitti tra gli aggiornamenti ai dati.

- Quando si vuole registrare gli eventi che si verificano ed essere in grado di riprodurli in modo da ripristinare lo stato di un sistema, annullare le modifiche o mantenere una cronologia e audit log. Se un'attività si articola in più passaggi, ad esempio, può essere necessario eseguire determinate azioni per annullare alcuni aggiornamenti e quindi riprodurre una serie di passaggi per ripristinare i dati a uno stato coerente.

- Quando l'uso di eventi costituisce un elemento naturale dell'operatività di un'applicazione e richiede un impegno aggiuntivo minimo in termini di sviluppo o implementazione.

- Quando si deve separare il processo di inserimento o aggiornamento dei dati dalle attività necessarie per applicare queste azioni. Questa necessità può derivare dalla volontà di migliorare le prestazioni dell'interfaccia utente o di distribuire gli eventi ad altri listener che eseguono determinate operazioni quando si verifica un evento. Uno scenario di questo tipo può essere costituito, ad esempio, dall'integrazione di un sistema di gestione delle paghe con un sito Web di invio di note spese, in modo che degli eventi generati dall'archivio eventi in risposta ad aggiornamenti ai dati eseguiti sul sito Web possano avvalersi sia il sito Web sia il sistema di gestione delle paghe.

- Se si vuole avere la possibilità di modificare il formato dei modelli materializzati e i dati dell'entità in caso di variazione dei requisiti oppure se, in combinazione con un modello CQRS, si deve adattare un modello di lettura o le viste che espongono i dati.

- Quando il modello di origine eventi viene usato con un modello CQRS e la coerenza finale è accettabile durante l'aggiornamento di un modello di lettura o l'impatto sulle prestazioni generato dalla riattivazione di entità e dati da un flusso di eventi è accettabile.

Questo modello può non essere utile nelle situazioni seguenti:

- Domini semplici o di piccole dimensioni, con poca o alcuna logica di business o sistemi non di dominio che per natura interagiscono bene con i tradizionali meccanismi di gestione dati CRUD.

- Sistemi in cui sono necessari coerenza e aggiornamenti in tempo reale alle visualizzazioni dei dati.

- Sistemi in cui non sono necessarie funzioni di audit trail, cronologia e annullamento e riproduzione delle azioni.

- Sistemi in cui la frequenza di conflitti tra gli aggiornamenti ai dati sottostanti è molto bassa. Ad esempio, sistemi che in prevalenza aggiungono dati anziché aggiornarli.

## <a name="example"></a>Esempio

Un sistema di gestione delle conferenze deve tenere traccia del numero di prenotazioni completate per una conferenza in modo da poter verificare la disponibilità di posti nel momento in cui un aspirante partecipante prova a effettuare una prenotazione. Il sistema può archiviare il numero totale di prenotazioni per una conferenza in almeno due modi:

- Il sistema può memorizzare le informazioni sul numero totale di prenotazioni come entità separata in un database in cui sono contenute le informazioni di prenotazione. Per ogni nuova prenotazione effettuata o annullata, il sistema può incrementare o diminuire il numero di conseguenza. Questo approccio è molto semplice in teoria, ma può generare problemi di scalabilità nel caso in cui un numero elevato di partecipanti tenti di effettuare una prenotazione in un breve intervallo di tempo, ad esempio nell'ultimo giorno prima della chiusura delle prenotazioni.

- Il sistema può memorizzare le informazioni su ogni nuova prenotazione aggiunta o annullata come eventi contenuti in un archivio eventi. Può quindi calcolare il numero di posti disponibili riproducendo questi eventi. Questo approccio può essere più scalabile grazie alla non modificabilità degli eventi. Il sistema deve solo riuscire a leggere dati dall'archivio eventi e, se necessario, aggiungere nuovi dati. Le informazioni sugli eventi rappresentati da ogni nuova prenotazione aggiunta o annullata non vengono mai modificate.

Il grafico seguente illustra come il sottosistema di prenotazione dei posti del sistema di gestione delle conferenze possa essere implementato usando il modello di origine eventi.

![Uso del modello di origine eventi per acquisire informazioni sulle prenotazioni in un sistema di gestione delle conferenze](images/event-sourcing-bounded-context.png)


Di seguito è illustrata la sequenza di azioni per la prenotazione di due posti:

1. L'interfaccia utente esegue un comando per prenotare i posti per due partecipanti. Il comando viene gestito da un gestore di comando separato. Una parte della logica che viene separata dall'interfaccia utente diventa responsabile della gestione delle richieste inviate sotto forma di comandi.

2. Viene creata un'aggregazione contenente informazioni su tutte le prenotazioni per la conferenza tramite l'esecuzione di query sugli eventi che descrivono le nuove prenotazioni e gli annullamenti. L'aggregazione prende il nome di `SeatAvailability` ed è contenuta in un modello di dominio che espone i metodi per l'esecuzione di query e la modifica dei dati inclusi nell'aggregazione.

    >  Alcune ottimizzazioni da valutare comprendono l'uso di snapshot (in modo che non sia necessario eseguire query e riprodurre l'elenco completo degli eventi per ottenere lo stato corrente dell'aggregazione) e la conservazione di una copia memorizzata nella cache dell'aggregazione in memoria.

3. Il gestore di comando richiama un metodo esposto dal modello di dominio per effettuare le prenotazioni.

4. L'aggregazione `SeatAvailability` registra un evento contenente il numero di posti prenotati. La volta successiva in cui l'aggregazione applica un evento verranno usate tutte le prenotazioni per calcolare il numero di posti rimanenti.

5. Il sistema aggiunge il nuovo evento all'elenco degli eventi presenti nell'archivio eventi.

Se un utente annulla una prenotazione, il sistema segue un processo simile, ma in questo caso il gestore di comando esegue un comando che genera un evento di annullamento della prenotazione e lo aggiunge all'archivio eventi.

Oltre a garantire una maggiore scalabilità, l'uso di un archivio eventi offre anche una cronologia completa o un audit trail delle prenotazioni aggiunte o annullate per una conferenza. Gli eventi nell'archivio di eventi costituiscono il record esatto. Non è necessario rendere l'aggregazione permanente in altri modi poiché il sistema può facilmente riprodurre gli eventi e ripristinare lo stato a un qualsiasi punto nel tempo.

>  Altre informazioni su questo esempio sono disponibili in [Introducing Event Sourcing](https://msdn.microsoft.com/library/jj591559.aspx) (Introduzione all'origine eventi).

## <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive

Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:

- [Modello di separazione di responsabilità per query e comandi (CQRS, Command and Query Responsibility Segregation)](command-and-query-responsibility-segregation-cqrs.md). L'archivio di scrittura che costituisce un'origine permanente di informazioni per l'implementazione di un modello CQRS è spesso basato su un'implementazione del modello di origine eventi. Questo modello descrive come isolare in un'applicazione le operazioni di lettura dei dati dalle operazioni di aggiornamento dei dati tramite l'uso di interfacce separate.

- [Modello di viste materializzate](materialized-view.md). L'archivio dati usato in un sistema basato sull'origine di eventi non è in genere adatto per l'esecuzione di query efficienti. In questo caso, l'approccio comune è quello di generare viste dati prepopolate a intervalli regolari o in caso di variazione dei dati. Questo modello illustra come viene eseguita questa operazione.

- [Modello di transazioni di compensazione](materialized-view.md). I dati esistenti in un archivio di origine eventi non vengono aggiornati, ma vengono aggiunte nuove voci che determinano la transizione dello stato delle entità su nuovi valori. Per annullare una modifica, vengono usati voci di compensazione poiché non è possibile annullare semplicemente la modifica precedente. In questo modello viene descritto come annullare le conseguenze prodotte da un'operazione precedente.

- [Nozioni di base sulla coerenza dei dati](https://msdn.microsoft.com/library/dn589800.aspx). Quando si usa il modello di origine eventi con un archivio di letture o viste materializzate diverse, i dati letti non saranno subito coerenti ma avranno una coerenza finale. Questo modello riepiloga i problemi da affrontare per mantenere la coerenza dei dati distribuiti.

- [Linee guida di partizionamento di dati](https://msdn.microsoft.com/library/dn589795.aspx). Quando si usa il modello di origine eventi, si ricorre spesso al partizionamento dei dati per migliorare la scalabilità, ridurre i conflitti e ottimizzare le prestazioni. Questo modello descrive come suddividere i dati in partizioni distinte e illustra i problemi che possono derivarne.

- Post di Greg Young sui [vantaggi offerti dal modello di origine eventi](http://codebetter.com/gregyoung/2010/02/20/why-use-event-sourcing/).



<!--HONumber=Nov16_HO3-->


