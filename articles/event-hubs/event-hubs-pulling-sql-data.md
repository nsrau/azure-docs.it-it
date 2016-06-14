<properties
   pageTitle="Trasferimento di dati SQL in Hub eventi di Azure | Microsoft Azure"
   description="Panoramica dell'importazione di Hub eventi da un esempio SQL"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor=""/>

<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/31/2016"
   ms.author="spyros;sethm" />

# Estrazione dei dati da SQL a un hub eventi di Azure

Un'architettura tipica per un'applicazione per l'elaborazione dei dati in tempo reale prevede per prima cosa il trasferimento dei dati a un hub eventi di Azure. Può trattarsi di uno scenario IoT, ad esempio il monitoraggio del traffico in tratti diversi di un'autostrada, o uno scenario di gioco, in cui vengono monitorate le azioni di un folto gruppo di giocatori accaniti, o uno scenario aziendale, ad esempio il monitoraggio dell'occupazione degli edifici. In questi casi, i produttori di dati sono generalmente oggetti esterni che generano dati di serie temporali da raccogliere, analizzare, archiviare e in base ai quali agire. La creazione dell'infrastruttura per questi processi può aver richiesto uno sforzo notevole. Cosa fare se si vuole importare dati da un'origine come un database anziché da dati di streaming e usarli in combinazione con altri dati di streaming? Si consideri il caso in cui si vuole usare Analisi di flusso di Azure, Remote Data Explorer (RDX) o un altro strumento per analizzare dati che si modificano lentamente provenienti da Microsoft Dynamics AX o da un sistema di gestione di strutture personalizzato e prendere decisioni basate sui dati. Per risolvere questo problema, è stato scritto e reso disponibile tramite open source un piccolo esempio di cloud, modificabile e distribuibile, che estrae i dati da una tabella SQL e li trasferisce all'hub eventi di Azure per usarli come input per le applicazioni analitiche di downstream. Tenere presente che si tratta di uno scenario raro, l'opposto di quanto solitamente avviene in un hub eventi. Tuttavia, se ci si trova in una situazione in cui è necessario seguire questa procedura, il codice è disponibile nella raccolta esempi di Azure [qui](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-import-from-sql/).

Si noti che il codice in questo esempio è da considerarsi semplicemente come un esempio. L'applicazione **non** è stata progettata come applicazione di produzione e non sono stati eseguiti tentativi di renderla adatta all'uso in tale ambiente. Si tratta solo ed esclusivamente di un esempio destinato agli sviluppatori. Prima di stabilire un'architettura end-to-end, è consigliabile esaminare sicurezza, prestazioni, funzionalità e fattori di costo.

## Struttura dell'applicazione

L'applicazione viene scritta in C# e il file readme.md dell'esempio contiene tutte le informazioni necessarie per modificare, compilare e pubblicare l'applicazione. Le sezioni seguenti contengono una panoramica generale delle funzionalità dell'applicazione.

Si presuppone che si abbia accesso a una tabella di SQL Azure. Sarà inoltre necessario avere configurato un hub eventi di Azure e conoscere la stringa di connessione necessaria per accedervi.

Quando la soluzione SqlToEventHub si avvia, legge un file di configurazione (App.config) per ottenere una serie di elementi, come spiegato nel file readme.md. Si tratta di elementi facilmente comprensibili, ad esempio il nome della tabella di dati e così via, e non è necessario offrire spiegazioni in questa sede.

Dopo che l'applicazione ha letto il file di configurazione, avvia un ciclo che prevede la lettura della tabella SQL e il trasferimento dei record all'hub eventi, quindi rimane in attesa di un intervallo di inattività definito dall'utente prima di ricominciare. Tenere presente quanto segue:

1. L'applicazione presuppone che la tabella SQL venga aggiornata da un processo esterno e che si vogliano inviare tutti e solo gli aggiornamenti a un hub eventi.
2. La tabella SQL deve avere un campo contenente un numero univoco e crescente, ad esempio un numero di record. Può essere un campo semplice denominato "Id" o qualsiasi altro elemento che viene incrementato quando il processo di aggiornamento del database aggiunge record, ad esempio "Creation\_time" o "Sequence\_number". L'applicazione annota e archivia il valore del campo in ogni iterazione. In ogni passaggio successivo del ciclo, l'applicazione esegue in sostanza query nella tabella per tutti i record in cui il valore del campo supera il valore osservato durante l'ultima iterazione del ciclo. Quest'ultimo valore viene denominato "offset".
3. L'applicazione crea una tabella "TableOffsets" all'avvio, per archiviare gli offset. La tabella viene creata con la query "CreateOffsetTableQuery" definita nel file di configurazione. 
4. Esistono diverse query usate per lavorare con la tabella di offset, definite nel file di configurazione come "OffsetQuery", "UpdateOffsetQuery" e "InsertOffsetQuery". Queste query non devono essere modificate.
5. Infine, la query "DataQuery" definita nel file di configurazione è la query da eseguire per estrarre i record dalla tabella SQL. Per scopi di ottimizzazione, è attualmente limitata ai primi 1000 record in ogni passaggio del ciclo: se, ad esempio, sono stati aggiunti 25000 record al database dall'ultima query, l'esecuzione della query potrebbe richiedere diverso tempo. Limitando la query a 1000 record per volta, le query sono molto più veloci. La selezione dei primi 1000 record comporta il trasferimento in batch successivi di 1000 record all'hub eventi.    

## Passaggi successivi

Per distribuire la soluzione, clonare o scaricare l'applicazione SqlToEventHub, modificare il file App.config, compilarlo e infine pubblicarlo. Dopo la pubblicazione, l'applicazione viene eseguita nel portale di Azure classico in Servizi cloud ed è possibile monitorare gli eventi in arrivo nell'hub eventi. Si noti che la frequenza verrà determinata da due elementi: la frequenza degli aggiornamenti della tabella SQL e l'intervallo di sospensione specificato nel file di configurazione per l'applicazione.

<!---HONumber=AcomDC_0601_2016-->