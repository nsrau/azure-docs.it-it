<properties 
   pageTitle="Utilizzo dei connettori" 
   description="Utilizzo dei connettori" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="prkumar" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="prkumar"/>


#Utilizzo dei connettori#

I connettori sono app per le API che consentono di connettersi ai servizi in esecuzione nel cloud oppure in locale per ottenere o inserire dati o eseguire azioni. È possibile usarli in tutti i Servizi app di Azure, dalle app Web, alle app mobili e per la logica.

I Servizi app di Azure forniscono diversi connettori predefiniti che aiutano a connettersi ai servizi che interessano all'utente, concentrando gli sforzi sull'effettiva logica di business dell'applicazione. Inoltre, la piattaforma è facilmente estendibile tramite la scrittura di app per le API personalizzate per connettersi alle proprie applicazioni legacy.

Alcune delle funzionalità principali dei connettori BizTalk sono elencate di seguito:

1. Gestire la protezione OAuth per la connessione a servizi SaaS popolari (Facebook, Twitter, Office 365, SalesForce)
2. Connettività agli asset locali tramite un Inoltro del bus di servizio.
3. Connettività per i protocolli comuni per ricevere e inviare dati.

##Connettori e app per la logica##
Le app per la logica sono costituite da trigger e da azioni. Alcuni connettori possono essere usati come trigger per identificare l'eventuale disponibilità di nuovi dati e attivare un'app per la logica con tali dati come input. È anche possibile usare i connettori come un'azione nell'ambito di un'app per la logica per la ricerca di dati, la scrittura di dati o l'esecuzione di altre azioni supportate dal connettore.

###Connettori come trigger###
Diversi connettori forniscono i trigger per le app per la logica. Tali trigger sono di due tipi:

1. Trigger di polling: questi trigger eseguiranno il polling del servizio che interessa a una frequenza specificata per verificare la disponibilità di nuovi dati. Quando sono disponibili nuovi dati, verrà eseguita una nuova istanza dell'app per la logica con i dati come input. Il trigger può eseguire altre attività, come la pulizia dei dati letti e passati all'app per la logica, per prevenirne l'utilizzo ripetuto. Esempi di tali connettori sono File, SQL e Archiviazione di Azure.
2. Trigger di push: tali trigger restano in attesa di dati su un endpoint oppure che si verifichi un evento che attivi una nuova istanza dell'app per la logica. Esempi di tali connettori sono i listener HTTP e Twitter.

###Connettori come azioni###
È anche possibile usare i connettori come azioni come parte dell'app per la logica. Ciò può rivelarsi utile per cercare nell'app per la logica i dati da usare nell'esecuzione; ad esempio, può essere necessario cercare in un database SQL alcune informazioni aggiuntive riguardo un cliente quando si elabora un ordine inoltrato dal cliente in un'app per la logica. In alternativa, può essere necessario scrivere, aggiornare o eliminare i dati in una destinazione, per cui è possibile usare le azioni fornite dai connettori.

##Come usare i connettori##
Gli articoli seguenti offrono esempi di come usare alcuni connettori predefiniti.

* [Utilizzo del connettore HTTP]
* [Utilizzo del connettore FTP]
* [Utilizzo del connettore Box]
* [Utilizzo del connettore BLOB Archiviazione di Azure]
* [Utilizzo del connettore Facebook]
* [Utilizzo del connettore Twitter]
* [Utilizzo del connettore SalesForce]
* [Utilizzo del connettore Office 365]
* [Utilizzo del connettore Sharepoint]
* [Utilizzo del connettore SAP]

Per altre informazioni, consultare i riferimenti ai connettori e alle API [http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference).


<!-- Links -->

[Utilizzo del connettore Box]: app-service-logic-connector-box.md
[Utilizzo del connettore Facebook]: app-service-logic-connector-facebook.md
[Utilizzo del connettore SalesForce]: app-service-logic-connector-salesforce.md
[Utilizzo del connettore Twitter]: app-service-logic-connector-twitter.md
[Utilizzo del connettore SAP]: app-service-logic-connector-sap.md
[Utilizzo del connettore FTP]: app-service-logic-connector-ftp.md
[Utilizzo del connettore HTTP]: app-service-logic-connector-http.md
[Utilizzo del connettore BLOB Archiviazione di Azure]: app-service-logic-connector-azurestorageblob.md
[Utilizzo del connettore Office 365]: app-service-logic-connector-office365.md
[Utilizzo del connettore SharePoint]: app-service-logic-connector-sharepoint.md


<!--HONumber=49-->