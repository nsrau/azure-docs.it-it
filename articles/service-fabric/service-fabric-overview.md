<properties
   pageTitle="Panoramica dell'infrastruttura di servizi | Microsoft Azure"
   description="Panoramica di Service Fabric, in cui le applicazioni sono costituite da numerosi microservizi per assicurare scalabilità e resilienza. Service Fabric è una piattaforma di sistemi distribuiti che consente di creare applicazioni scalabili, affidabili e facilmente gestibili per il cloud"
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
   ms.date="09/22/2016"
   ms.author="mfussell"/>  

# Panoramica di Service Fabric
Service Fabric è una piattaforma di sistemi distribuiti che semplifica la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili. Service Fabric fa fronte anche alle principali problematiche correlate allo sviluppo e alla gestione delle applicazioni cloud. Gli sviluppatori e gli amministratori non devono più occuparsi della risoluzione di complessi problemi di infrastruttura e possono concentrarsi sull'implementazione di carichi di lavoro cruciali e impegnativi, consapevoli di quanto siano anche scalabili, affidabili e gestibili. Service Fabric rappresenta la piattaforma middleware di prossima generazione per la creazione e la gestione di applicazioni cloud di classe enterprise di primo livello.

## Applicazioni costituite da microservizi
Service Fabric consente di creare e gestire applicazioni scalabili e affidabili costituite da microservizi eseguiti a densità molto elevata in un pool condiviso di computer, chiamato cluster. Offre un sofisticato runtime per la creazione di microservizi scalabili e distribuiti, con e senza stato. Fornisce inoltre una gamma completa di funzionalità di gestione per il provisioning, la distribuzione, il monitoraggio, l'aggiornamento, l'esecuzione di patch e l'eliminazione di applicazioni distribuite.

Perché l'approccio ai microservizi è importante? I due motivi principali sono:

1. I microservizi consentono di ridimensionare diverse parti dell'applicazione a seconda delle esigenze di quest'ultima.

2. I team di sviluppo sono in grado di offrire una maggiore flessibilità nell'implementazione di modifiche e quindi di fornire funzionalità ai clienti più rapidamente e con maggiore frequenza.

Service Fabric viene attualmente usato in numerosi servizi Microsoft, tra cui database SQL di Azure, Azure DocumentDB, Cortana, Power BI, Microsoft Intune, Hub eventi di Azure, Azure IoT, Skype for Business e molti servizi di base di Azure.

Service Fabric è stato sviluppato specificamente per la creazione di servizi "nati nel cloud" che inizialmente possono essere di piccole dimensioni, ma che secondo le necessità possono espandersi su vasta scala con centinaia o migliaia di computer.

Gli attuali servizi su scala Internet vengono compilati attraverso microservizi. costituiti ad esempio da gateway di protocollo, profili utente, carrelli acquisti, elaborazione dell'inventario, code e cache. Service Fabric è una piattaforma di microservizi che assegna a ognuno di essi un nome univoco, con o senza stato.

Service Fabric offre funzionalità complete di gestione del ciclo di vita e del runtime per le applicazioni costituite da questi microservizi. Ospita i microservizi all'interno di contenitori distribuiti e attivati nel cluster di Service Fabric. Il passaggio dalle VM ai contenitori consente un incremento di densità considerevole. Analogamente, un altro incremento di densità considerevole si ottiene passando dai contenitori ai microservizi. Un singolo cluster di database SQL di Azure, ad esempio, è costituito da centinaia di computer che eseguono decine di migliaia di contenitori in cui vengono ospitati complessivamente centinaia di migliaia di database. Ogni database è un microservizio di Service Fabric con stato. Lo stesso accade per gli altri servizi menzionati in precedenza, motivo per cui il termine "iperscalabilità" viene usato per descrivere le funzionalità di Service Fabric. Se, infatti, i contenitori assicurano una densità elevata, i microservizi garantiscono un'iperscalabilità.

Per altre informazioni sull'approccio basato su microservizi, leggere [Perché usare un approccio basato su microservizi per la compilazione delle applicazioni?](service-fabric-overview-microservices.md)

## Creare i cluster di Service Fabric in qualsiasi ambiente
È possibile creare cluster di Service Fabric in molti ambienti, tra cui Azure o in locale, in Windows Server o su Linux. Inoltre l'ambiente di sviluppo nell'SDK è identico all'ambiente di produzione senza emulatori coinvolti. In altre parole, se è in esecuzione nel cluster di sviluppo locale viene distribuito nello stesso cluster in altri ambienti.

Per altre informazioni sulla creazione di cluster in locale, vedere gli articoli relativi alla [creazione di un cluster in Windows Server o Linux](service-fabric-deploy-anywhere.md) o alla creazione di un cluster in Azure [tramite il portale di Azure](service-fabric-cluster-creation-via-portal.md).

![Piattaforma Service Fabric][Image1]  

## Microservizi di Service Fabric con e senza stato

Service Fabric consente di compilare applicazioni costituite da microservizi. I microservizi senza stato, come i gateway di protocollo, i proxy Web e così via, non mantengono uno stato variabile al di fuori di una richiesta e della relativa risposta fornita dal servizio. I ruoli di lavoro di Servizi cloud di Azure sono un esempio di servizio senza stato. I microservizi con stato, come gli account utente, i database, i dispositivi, i carrelli acquisti, le code e così via, mantengono invece uno stato variabile e autorevole anche all'esterno della richiesta e della relativa risposta. Le attuali applicazioni su scala Internet sono costituite da una combinazione di microservizi con e senza stato.

Perché avere microservizi con stato insieme a quelli senza stato? I due motivi principali sono:

1. La capacità di compilare servizi OLTP (elaborazione delle transazioni online) ad alta velocità, bassa latenza e tolleranti agli errori mantenendo il codice e i dati nello stesso computer. Alcuni esempi sono le vetrine interattive, le ricerche, i sistemi Internet delle cose (IoT), i sistemi di trading, l'elaborazione delle carte di credito, i sistemi di rilevamento di frodi e la gestione dei record personali.

2. La semplificazione della progettazione delle applicazioni. Per i microservizi con stato non è più necessario usare le code e le cache aggiuntive necessarie in passato per soddisfare i requisiti di disponibilità e latenza di un'applicazione interamente senza stato. L'elevata disponibilità e la bassa latenza dei servizi con stato implicano un uso ridotto di componenti mobili da gestire in un'applicazione nel suo insieme.

Per altre informazioni sui modelli di applicazione con Service Fabric, vedere [Scenari di applicazione di Service Fabric](service-fabric-application-scenarios.md) e [Panoramica dei modelli di programmazione di Service Fabric](service-fabric-choose-framework.md) per il servizio.

## Gestione del ciclo di vita delle applicazioni
Service Fabric offre un supporto di alto livello per l'intero ciclo di vita delle applicazioni cloud, dallo sviluppo alla distribuzione, fino alla gestione giornaliera, alla manutenzione e alla rimozione delle autorizzazioni.

Le funzionalità di gestione del ciclo di vita delle applicazioni di Service Fabric consentono agli amministratori delle applicazioni e agli operatori IT di usare semplici flussi di lavoro con interazione limitata per il provisioning, la distribuzione, l'applicazione di patch e il monitoraggio delle applicazioni. Questi flussi di lavoro predefiniti riducono in modo significativo il carico degli operatori IT per mantenere le applicazioni continuamente disponibili.

La maggior parte delle applicazioni è costituita da una combinazione di microservizi con e senza stato e da altri runtime o file eseguibili distribuiti insieme. Grazie all'uso di tipi sicuri nelle applicazioni e di microservizi combinati in pacchetti, Service Fabric consente la distribuzione di più istanze di applicazione. Ogni istanza viene gestita e aggiornata in modo indipendente. Aspetto ancora più importante, Service Fabric può distribuire *qualsiasi* eseguibile o runtime e renderlo affidabile. Ad esempio, Service Fabric distribuisce ASP.NET Core 1, Node.js, VM Java, script o qualsiasi elemento costitutivo dell'applicazione.

Per altre informazioni sulla gestione del ciclo di vita delle applicazioni, vedere [Ciclo di vita dell'applicazione di Service Fabric](service-fabric-application-lifecycle.md). Per la distribuzione di qualsiasi codice, vedere [Distribuire un eseguibile guest in Service Fabric](service-fabric-deploy-existing-app.md)

## Funzionalità principali
Usando Service Fabric è possibile:

- Sviluppare applicazioni altamente scalabili, con funzionalità di riparazione automatica.

- Sviluppare applicazioni costituite da microservizi usando il modello di programmazione di Service Fabric. O, semplicemente ospitare eseguibili guest e altri framework di applicazione di propria scelta, come ASP.NET Core 1 o Node. js.

- Sviluppare microservizi con o senza stato altamente affidabili.

- Semplificare la progettazione dell'applicazione usando microservizi con stato al posto di cache e code.

- Eseguire la distribuzione in Azure o in cloud locali con Windows Server o Linux senza dover apportare modifiche al codice. Scrivere una sola volta e quindi eseguire la distribuzione ovunque in qualsiasi cluster di Service Fabric.

- Eseguire attività di sviluppo con un approccio di tipo "data center nel proprio computer". L'ambiente di sviluppo locale usa infatti lo stesso codice eseguito nei data center di Azure.

- Distribuire le applicazioni nel giro di pochi secondi.

- Distribuire applicazioni a una densità maggiore rispetto alle macchine virtuali, distribuendo centinaia o migliaia di applicazioni per ogni computer.

- Distribuire versioni diverse della stessa applicazione side-by-side, ognuna aggiornabile in modo indipendente.

- Gestire il ciclo di vita delle applicazioni con stato senza tempi di inattività, inclusa l'esecuzione di aggiornamenti di rilievo e non di rilievo.

- Gestire le applicazioni tramite API .NET, PowerShell o interfacce REST.

- Aggiornare i microservizi e applicarvi patch all'interno delle applicazioni in modo indipendente.

- Monitorare e diagnosticare l'integrità delle applicazioni e impostare i criteri per eseguire riparazioni automatiche.

- Aumentare o ridurre facilmente le dimensioni del cluster di Service Fabric, sapendo che le dimensioni delle applicazioni variano in base alle risorse disponibili.

- Osservare il sistema di bilanciamento delle risorse con riparazione automatica che orchestra la ridistribuzione delle applicazioni all'interno del cluster. Service Fabric esegue il ripristino dagli errori e ottimizza la distribuzione del carico in base alle risorse disponibili.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

* Per altre informazioni:
    * [Perché usare un approccio ai microservizi per la compilazione di applicazioni](service-fabric-overview-microservices.md)
    * [Panoramica della terminologia](service-fabric-technical-overview.md)
* Configurazione dell'[ambiente di sviluppo](service-fabric-get-started.md) di Service Fabric
* [Panoramica dei modelli di programmazione di Service Fabric](service-fabric-choose-framework.md) per il servizio

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png

<!---HONumber=AcomDC_0928_2016-->