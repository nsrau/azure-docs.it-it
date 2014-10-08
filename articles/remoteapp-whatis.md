<properties title="What is RemoteApp?" pageTitle="What is RemoteApp?" description="Learn about RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo"></tags>

# Informazioni su Azure RemoteApp

Con RemoteApp i programmi a cui si accede in modalità remota tramite Azure risultano come se fossero in esecuzione nel computer locale dell'utente finale. Questi programmi vengono chiamati programmi RemoteApp. Anziché essere presentati all'utente nel desktop del server Host sessione Desktop remoto, il programma RemoteApp è integrato nel desktop del client. Il programma RemoteApp viene eseguito nella propria finestra ridimensionabile, può essere trascinato su più monitor e presenta una propria voce nella barra delle applicazioni. Se un utente esegue più di un programma RemoteApp nello stesso server Host sessione Desktop remoto, il programma RemoteApp condividerà la stessa sessione di Servizi Desktop remoto.

RemoteApp contribuisce a ridurre la complessità e le attività amministrative in molte situazioni, ad esempio:

-   Per le succursali, in cui il supporto IT locale e la larghezza di banda della rete potrebbero essere limitati.
-   Nelle situazioni in cui gli utenti hanno l'esigenza di accedere ai programmi in remoto.
-   Nella distribuzione di programmi line-of-business, in particolare di quelli personalizzati.
-   In ambienti di tipo "hot desk" in cui agli utenti non è assegnata sempre la stessa postazione.
-   Per le distribuzioni di più versioni di un programma, in particolare quando l'installazione di più versioni in locale genera conflitti.

Diversamente da Servizi Desktop remoto tradizionale, Azure RemoteApp viene eseguito nel portale di gestione di Azure. Gli utenti accedono ai programmi tramite il portale, mentre la gestione dei programmi e degli utenti viene eseguita nel portale di amministrazione.

Sono disponibili due tipi di distribuzione di RemoteApp:

-   Una distribuzione cloud è ospitata nel cloud di Azure insieme a tutti i dati dei programmi.
-   Una distribuzione ibrida è ospitata nel cloud di Azure ma consente agli utenti di accedere ai dati archiviati nella rete locale.

Indipendentemente dal tipo di distribuzione scelto, dopo avere creato il servizio è possibile pubblicare i programmi da condividere con gli utenti nel feed degli utenti finali. Si tratta dell'elenco dei programmi disponibili a cui gli utenti possono accedere tramite il portale di Azure. Tenere presente che nel feed sono mostrati tutti i programmi per tutti i servizi associati alla sottoscrizione in uso.

