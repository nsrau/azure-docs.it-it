<properties 
   pageTitle="Panoramica dell'infrastruttura di servizi | Microsoft Azure" 
   description="Una panoramica di infrastruttura di servizi, in cui le applicazioni sono costituite da molti microservizi per fornire scalabilità e resilienza. Service Fabric è una piattaforma di sistemi distribuiti che consente di creare applicazioni scalabili, affidabili e facilmente gestibili per il cloud" 
   services="service-fabric" 
   documentationCenter=".net" 
   authors="msfussell" 
   manager="timlt" 
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA" 
   ms.date="11/18/2015"
   ms.author="mfussell"/>

# Panoramica di Service Fabric
Service Fabric è una piattaforma di sistemi distribuiti che consente di creare applicazioni scalabili, affidabili e facilmente gestibili per il cloud. Service Fabric fa fronte alle principali problematiche correlate allo sviluppo e alla gestione delle applicazioni cloud. Grazie a Service Fabric, gli sviluppatori e gli amministratori non devono più occuparsi di complessi problemi di infrastruttura e possono concentrarsi sull'implementazione di carichi di lavoro cruciali e articolati, avendo la consapevolezza che siano scalabili affidabili e gestibili. Service Fabric rappresenta la piattaforma middleware di prossima generazione per la creazione e la gestione di tali servizi cloud di livello 1 per l'azienda.

## Applicazioni costituite da microservizi
Service Fabric consente di creare e gestire applicazioni scalabili e affidabili costituite da microservizi eseguiti a densità molto elevata in un pool condiviso di computer, comunemente chiamato cluster di Service Fabric. Sono disponibili un sofisticato runtime per creare microservizi con e senza stato scalabili e distribuiti, nonché funzionalità complete di gestione delle applicazioni per effettuare il provisioning, distribuire, monitorare, aggiornare ed eliminare le applicazioni distribuite oltre che per applicarvi patch.

Service Fabric attualmente controlla numerosi servizi Microsoft, tra cui database SQL di Azure, Azure DocumentDB, Cortana, Power BI, Microsoft Intune, Hub eventi di Azure, molti servizi di base di Azure e Skype for Business per citarne solo alcuni.

Service Fabric è stato sviluppato specificamente per la creazione di servizi "nati nel cloud" che inizialmente possono essere di piccole dimensioni, ma che secondo le necessità possono espandersi su vasta scala con centinaia o migliaia di computer, generando cluster di Service Fabric estesi su più set di disponibilità all'interno di un'area o in più aree diverse.

I servizi scalabili Internet di oggi vengono compilati usando microservizi, di cui i gateway di protocollo, i profili utente, i carrelli acquisti, l'elaborazione dell'inventario, le code e le cache sono solo alcuni esempi. Service Fabric è una piattaforma di microservizi che assegna a ognuno un nome univoco che può essere con o senza stato.

Service Fabric offre funzionalità complete di gestione del ciclo di vita e del runtime per le applicazioni costituite da questi microservizi. Ospita i microservizi all'interno di contenitori distribuiti e attivati nel cluster di Service Fabric. Così come è possibile far aumentare l'ordine di grandezza della densità passando dalle macchine virtuali ai contenitori, è altrettanto possibile ottenere un analogo ordine di grandezza della densità passando dai contenitori ai microservizi. Ad esempio, un singolo cluster del database SQL di Azure, basato su Service Fabric, è costituito da centinaia di computer che eseguono decine di migliaia di contenitori in cui viene ospitato un totale di centinaia di migliaia di database, ognuno dei quali è un microservizio di Service Fabric con stato. Lo stesso vale per Hub eventi e gli altri servizi citati in precedenza. Ecco perché, per descrivere le funzionalità di Service Fabric, è possibile usare il termine di "iperscalabilità". Se infatti i contenitori assicurano una densità elevata, i microservizi garantiscono un'iperscalabilità.

![Piattaforma Service Fabric][Image1]

## Microservizi di Service Fabric con e senza stato

I microservizi senza stato, come ad esempio i gateway di protocollo, i proxy Web e così via, non mantengono alcuno stato variabile al di fuori delle richieste e delle relative risposte fornite dal servizio. I ruoli di lavoro di Servizi cloud di Azure sono un esempio di servizio senza stato. I microservizi con stato, tra cui gli account utente, i database, i dispositivi, i carrelli acquisti, le code e così via, mantengono uno stato autorevole variabile oltre alla richiesta e alla relativa risposta. Le applicazioni scalabili Internet odierne sono costituite da una combinazione di microservizi con e senza stato.
 
Perché i microservizi con stato sono importanti? Perché non usare semplicemente servizi senza stato per tutti gli scopi? I motivi sono due:

1) La possibilità di creare servizi OLTP con un'elevata velocità effettiva, una bassa latenza e la tolleranza di errore, come ad esempio vetrine interattive, servizi di ricerca, sistemi IoT, sistemi commerciali, sistemi di elaborazione per carte di credito e di rilevamento delle frodi, servizi di gestione dei record personali e così via, tenendo il codice e i dati vicini nello stesso computer.

2) La semplificazione della progettazione delle applicazioni, in quanto per i microservizi con stato non è più necessario usare le code e le cache aggiuntive necessarie in passato per soddisfare i requisiti di disponibilità e latenza di un'applicazione interamente senza stato. L'elevata disponibilità e la bassa latenza dei servizi con stato implicano un uso ridotto di componenti mobili da gestire in un'applicazione nel suo insieme.

Per altre informazioni sui modelli di applicazione e la progettazione con Service Fabric, vedere l'articolo relativo agli [scenari applicativi](service-fabric-application-scenarios.md).

## Gestione del ciclo di vita delle applicazioni
Service Fabric offre un supporto di prima categoria per il ciclo di vita completo delle applicazioni cloud, dallo sviluppo alla distribuzione, alla gestione giornaliera, alla manutenzione e infine alla rimozione delle autorizzazioni.

Le funzionalità di gestione del ciclo di vita delle applicazioni di Service Fabric consentono agli amministratori delle applicazioni e agli operatori IT di usare semplici flussi di lavoro con interazione limitata per il provisioning, la distribuzione, l'applicazione di patch e il monitoraggio delle applicazioni. Questi flussi di lavoro predefiniti riducono in modo significativo il carico degli operatori IT per mantenere le applicazioni continuamente disponibili.

La maggior parte delle applicazioni è costituita da una combinazione di microservizi con e senza stato e da altri runtime o file EXE distribuiti insieme. Grazie all'uso di tipi sicuri per le applicazioni e i microservizi contenuti nei pacchetti, Service Fabric consente la distribuzione di più istanze di applicazione, ognuna delle quali può essere gestita e aggiornata in modo indipendente. Un aspetto importante è dato dal fatto che Service Fabric è in grado di distribuire *qualsiasi* eseguibile o runtime e di renderlo affidabile. Ad esempio, può essere usato per distribuire codice ASP.NET 5, node.js, script o qualsiasi elemento costitutivo dell'applicazione.
  
Per altre informazioni sulla gestione del ciclo di vita delle applicazioni, vedere l'articolo relativo al [ciclo di vita dell'applicazione](service-fabric-application-lifecycle.md).

## Funzionalità principali
Usando Service Fabric è possibile:

- Sviluppare applicazioni altamente scalabili, con funzionalità di riparazione automatica.

- Eseguire le attività di sviluppo come si avesse un "data center nel proprio computer". L'ambiente di sviluppo locale infatti include lo stesso codice eseguito nei data center di Azure.
 
- Sviluppare applicazioni costituite da microservizi, eseguibili e altri framework di applicazioni a scelta, ad esempio ASP.NET, nodejs e così via.

- Sviluppare servizi e microservizi con o senza stato e renderli altamente affidabili.

- Semplificare la progettazione dell'applicazione usando servizi o microservizi con stato al posto di cache e code.
 
- Distribuire le applicazioni nel giro di pochi secondi.

- Eseguire la distribuzione in Azure o nei cloud locali con Windows Server senza dover apportare modifiche al codice. Scrivere una sola volta e quindi eseguire la distribuzione in qualsiasi cluster di Service Fabric.

- Distribuire applicazioni a una densità maggiore rispetto alle macchine virtuali, distribuendo centinaia o migliaia di applicazioni per ogni computer.

- Distribuire versioni diverse della stessa applicazione side-by-side, ognuna aggiornabile in modo indipendente.
 
- Gestire il ciclo di vita delle applicazioni con stato senza tempi di inattività, inclusi gli aggiornamenti di rilievo e non di rilievo.

- Gestire le applicazioni tramite API .NET, PowerShell o interfacce REST.
 
- Aggiornare i microservizi e applicarvi patch all'interno delle applicazioni in modo indipendente.

- Monitorare e diagnosticare l'integrità delle applicazioni e impostare i criteri per eseguire riparazioni automatiche.

- Scalare facilmente il cluster dell’infrastruttura di servizi verticalmente o orizzontalmente, sapendo che le applicazioni vengono scalate in base alle risorse disponibili.

- Osservare il bilanciamento delle risorse con riparazione automatica che orchestra la ridistribuzione delle applicazioni all'interno del cluster di Service Fabric per il ripristino da errori e per ottimizzare la distribuzione del carico in base alle risorse disponibili.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

* Per altre informazioni: 
	* [Perché un approccio di microservizi per la creazione di applicazioni?](service-fabric-overview-microservices.md).
	* [Panoramica tecnica](service-fabric-technical-overview.md)
* Configurare l'[ambiente di sviluppo](service-fabric-get-started.md) dell’infrastruttura di servizi.  
* Scelta di un [framework del modello di programmazione](service-fabric-choose-framework.md) per il servizio.


[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png

 

<!---HONumber=Nov15_HO4-->