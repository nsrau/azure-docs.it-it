<properties pageTitle="Work with a .NET backend mobile service" metaKeywords="Web API, mobile service, Azure, controllers" description="Provides examples on how to define, register, and use server-side components in Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Work with server scripts in Mobile Services" authors="glenga" solutions="mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />


# Usare un servizio mobile back-end .NET

Questo articolo fornisce informazioni dettagliate ed esempi sull'uso di un progetto Visual Studio back-end .NET che definisce il servizio mobile in Servizi mobili di Azure. Questo argomento è suddiviso nelle sezioni seguenti:

+ [Introduzione][Introduzione]
+ [Operazioni su tabella][Operazioni su tabella]
	+ [Procedura: Eseguire la registrazione per le operazioni su tabelle][Procedura: Eseguire la registrazione per le operazioni su tabelle]
	+ [Procedura: Eseguire l'override della risposta predefinita][Procedura: Eseguire l'override della risposta predefinita]
	+ [Procedura: Eseguire l'override della riuscita di execute][Procedura: Eseguire l'override della riuscita di execute]
	+ [Procedura: Eseguire l'override della gestione degli errori predefinita][Procedura: Eseguire l'override della gestione degli errori predefinita]
	+ [Procedura: Aggiungere parametri personalizzati][Procedura: Aggiungere parametri personalizzati]
	+ [Procedura: Usare gli utenti di tabella][Procedura: Usare gli utenti di tabella]
+ [API personalizzata][API personalizzata]
	+ [Procedura: Definire un'API personalizzata][Procedura: Definire un'API personalizzata]
	+ [Procedura: Implementare metodi HTTP][Procedura: Implementare metodi HTTP]
	+ [Procedura: Inviare e ricevere dati come XML][Procedura: Inviare e ricevere dati come XML]
	+ [Procedura: Usare utenti e intestazioni in un'API personalizzata][Procedura: Usare utenti e intestazioni in un'API personalizzata]
	+ [Procedura: Definire più route in un'API personalizzata][Procedura: Definire più route in un'API personalizzata]
+ [Utilità di pianificazione processi][Utilità di pianificazione processi]
	+ [Procedura: Definire script di processi pianificati][Utilità di pianificazione processi]
+ [Controllo del codice sorgente, codice condiviso e funzioni di supporto][Controllo del codice sorgente, codice condiviso e funzioni di supporto]
	+ [Procedura: Caricare moduli Node.js][Procedura: Caricare moduli Node.js]
	+ [Procedura: Usare le funzioni di supporto][Procedura: Usare le funzioni di supporto]
	+ [Procedura: Condividere il codice usando il controllo del codice sorgente][Procedura: Condividere il codice usando il controllo del codice sorgente]
	+ [Procedura: Usare le impostazioni app][Procedura: Usare le impostazioni app]
+ [Utilizzo dello strumento da riga di comando][Utilizzo dello strumento da riga di comando]
+ [Utilizzo delle tabelle][Utilizzo delle tabelle]
	+ [Procedura: Accedere alle tabelle dagli script][Procedura: Accedere alle tabelle dagli script]
	+ [Procedura: Eseguire inserimenti bulk][Procedura: Eseguire inserimenti bulk]
	+ [Procedura: Mappare tipi JSON a tipi di database][Procedura: Mappare tipi JSON a tipi di database]
	+ [Utilizzo di Transact-SQL per accedere alle tabelle][Utilizzo di Transact-SQL per accedere alle tabelle]
+ [Debug e risoluzione dei problemi][Debug e risoluzione dei problemi]
	+ [Procedura: Scrivere l'output nei log del servizio mobile][Procedura: Scrivere l'output nei log del servizio mobile]

## <a name="intro"></a>Introduzione

In un servizio mobile back-end .NET è possibile definire la logica di business personalizzata come codice JavaScript memorizzato ed eseguito sul server. Questo codice di script del server è assegnato a una delle funzionalità server seguenti:

+ [Operazioni di inserimento, lettura, aggiornamento o eliminazione su una determinata tabella][Operazioni su tabella].
+ [Processi pianificati][Utilità di pianificazione processi].
+ [Metodi HTTP definiti in un'API personalizzata][API personalizzata].

La firma della funzione principale dello script del server dipende dal contesto di cui viene usato lo script. È inoltre possibile definire codice di script comune come moduli nodes.js condivisi tra gli script. Per altre informazioni, vedere [Controllo del codice sorgente, codice condiviso e funzioni di supporto][Controllo del codice sorgente, codice condiviso e funzioni di supporto].

Per le descrizioni delle singole funzioni e dei singoli oggetti di script del server, vedere [Riferimento per gli script server di Servizi mobili].



  [Introduzione]: #intro
  [Operazioni su tabella]: #table-scripts
  [Procedura: Eseguire la registrazione per le operazioni su tabelle]: #register-table-scripts
  [Procedura: Eseguire l'override della risposta predefinita]: #override-response
  [Procedura: Eseguire l'override della riuscita di execute]: #override-success
  [Procedura: Eseguire l'override della gestione degli errori predefinita]: #override-error
  [Procedura: Aggiungere parametri personalizzati]: #access-headers
  [Procedura: Usare gli utenti di tabella]: #work-with-users
  [API personalizzata]: #custom-api
  [Procedura: Definire un'API personalizzata]: #define-custom-api
  [Procedura: Implementare metodi HTTP]: #handle-methods
  [Procedura: Inviare e ricevere dati come XML]: #api-return-xml
  [Procedura: Usare utenti e intestazioni in un'API personalizzata]: #get-api-user
  [Procedura: Definire più route in un'API personalizzata]: #api-routes
  [Utilità di pianificazione processi]: #scheduler-scripts
  [Controllo del codice sorgente, codice condiviso e funzioni di supporto]: #shared-code
  [Procedura: Caricare moduli Node.js]: #modules-helper-functions
  [Procedura: Usare le funzioni di supporto]: #helper-functions
  [Procedura: Condividere il codice usando il controllo del codice sorgente]: #shared-code-source-control
  [Procedura: Usare le impostazioni app]: #app-settings
  [Utilizzo dello strumento da riga di comando]: #command-prompt
  [Utilizzo delle tabelle]: #working-with-tables
  [Procedura: Accedere alle tabelle dagli script]: #access-tables
  [Procedura: Eseguire inserimenti bulk]: #bulk-inserts
  [Procedura: Mappare tipi JSON a tipi di database]: #JSON-types
  [Utilizzo di Transact-SQL per accedere alle tabelle]: #TSQL
  [Debug e risoluzione dei problemi]: #debugging
  [Procedura: Scrivere l'output nei log del servizio mobile]: #write-to-logs
