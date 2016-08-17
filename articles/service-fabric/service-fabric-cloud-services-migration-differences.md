<properties
   pageTitle="Differenze tra Servizi cloud e Service Fabric | Microsoft Azure"
   description="Panoramica concettuale per la migrazione di applicazioni da Servizi cloud a Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# Informazioni sulle differenze tra Servizi cloud e Service Fabric che è opportuno conoscere prima di procedere alla migrazione di applicazioni.
Microsoft Azure Service Fabric è la piattaforma di applicazioni cloud di nuova generazione per applicazioni distribuite con livelli di scalabilità e affidabilità elevati. Introduce molte nuove funzionalità per la creazione di pacchetti, la distribuzione, l'aggiornamento e la gestione di applicazioni cloud distribuite.

Questa è una guida introduttiva alla migrazione di applicazioni da Servizi cloud a Service Fabric. La guida è incentrata soprattutto sulle differenze a livello di architettura e progettazione tra Servizi cloud e Service Fabric.
 
## Applicazioni e infrastruttura

Una differenza fondamentale tra Servizi cloud e Service Fabric è la relazione tra macchine virtuali, carichi di lavoro e applicazioni. Per carico di lavoro in questo caso si intente il codice scritto dall'utente per eseguire un'attività specifica o fornire un servizio.
 
 - **Servizi cloud viene usato per la distribuzione di applicazioni come macchine virtuali.** Il codice scritto dall'utente è strettamente accoppiato a un'istanza di macchina virtuale, ad esempio un ruolo di lavoro o Web. Per distribuire un carico di lavoro in Servizi Cloud, distribuire una o più istanze di VM che eseguono il carico di lavoro. Non essendoci una separazione tra applicazioni e VM, non esiste una definizione formale di applicazione. Un'applicazione può essere considerata come un set di istanze del ruolo di lavoro o Web all'interno di una distribuzione di Servizi cloud o come un'intera distribuzione di Servizi cloud. In questo esempio un'applicazione viene illustrata come un set di istanze del ruolo.
 
![Applicazioni e topologia di Servizi cloud][1]

 - **Service Fabric viene usato per la distribuzione di applicazioni in macchine virtuali esistenti o nei computer che eseguono Service Fabric in Windows o Linux.** I servizi scritti dall'utente sono completamente disaccoppiati dall'infrastruttura sottostante, che è astratta dalla piattaforma di applicazioni di Service Fabric, consentendo la distribuzione di un'applicazione in più ambienti. Un carico di lavoro in Service Fabric è detto "servizio" e uno o più servizi sono raggruppati in un'applicazione definita formalmente che viene eseguita sulla piattaforma di applicazioni di Service Fabric. Più applicazioni possono essere distribuite in un singolo cluster di Service Fabric.
 
![Applicazioni e topologia di Service Fabric][2]
 
Lo stesso Service Fabric è un livello della piattaforma di applicazioni eseguito in Windows o Linux, mentre Servizi cloud è un sistema per la distribuzione di macchine virtuali gestite di Azure con carichi di lavoro collegati. Il modello di applicazione di Service Fabric offre numerosi vantaggi:

 - Tempi di distribuzione rapidi. La creazione di istanze di macchina virtuale può richiedere tempi lunghi. In Service Fabric le macchine virtuali sono distribuite solo una volta per formare un cluster che ospita la piattaforma di applicazioni di Service Fabric. Da quel momento, i pacchetti di applicazioni possono essere distribuiti nel cluster molto rapidamente.
 - Hosting ad alta densità. In Servizi cloud una macchina virtuale con ruolo di lavoro ospita un carico di lavoro. In Service Fabric le applicazioni sono separate dalle macchine virtuali che le eseguono, quindi è possibile distribuire un numero elevato di applicazioni in un numero limitato di macchine virtuali, con la possibilità di ridurre il costo complessivo per le distribuzioni estese.
 - La piattaforma di Service Fabric può essere eseguita ovunque sia disponibile un computer con Windows Server o Linux, sia in Azure che in locale. La piattaforma offre un livello di astrazione dall'infrastruttura sottostante, consentendo l'esecuzione dell'applicazione in ambienti diversi.
 - Gestione delle applicazioni distribuite. Service Fabric è una piattaforma che ospita non solo le applicazioni distribuite, ma consente anche di gestire il relativo ciclo di vita indipendentemente dalla macchina virtuale che le ospita o dal ciclo di vita del computer.

## Architettura dell'applicazione

L'architettura di un'applicazione di Servizi cloud include in genere numerose dipendenze dei servizi esterni, ad esempio Bus di servizio, tabelle e archivio BLOB di Azure, SQL, Redis e altri, per gestire lo stato e i dati di un'applicazione e le comunicazioni tra ruoli di lavoro e Web in una distribuzione di Servizi cloud. Un esempio di applicazione di Servizi cloud completa può avere un aspetto simile al seguente:

![Architettura di Servizi cloud][9]

Le applicazioni di Service Fabric possono anche scegliere di usare gli stessi servizi esterni in un'applicazione completa. Usando questo esempio di architettura di Servizi Cloud, il percorso di migrazione più semplice da Servizi cloud a Service Fabric consiste nel sostituire solo la distribuzione di Servizi cloud con un'applicazione di Service Fabric, conservando la stessa architettura complessiva. I ruoli di lavoro e Web possono essere applicati ai servizi senza stato di Service Fabric con modifiche minime al codice.

![Architettura di Service Fabric dopo una migrazione semplice][10]

In questa fase il sistema continuerà a funzionare come prima. Sfruttando le funzionalità con stato di Service Fabric, è possibile internalizzare gli archivi stati esterni come servizi con stato, se applicabile. Questo approccio è più complesso di una semplice migrazione di ruoli di lavoro e Web a servizi senza stato di Service Fabric, perché richiede la scrittura di servizi personalizzati che forniscono funzionalità equivalenti all'applicazione come avveniva in precedenza con i servizi esterni. I vantaggi di questa operazione includono:

 - Rimozione delle dipendenze esterne
 - Unificazione dei modelli di distribuzione, gestione e aggiornamento
 
Un'architettura di esempio risultante dall'internalizzazione di questi servizi può avere un aspetto simile al seguente:

![Architettura di Service Fabric dopo una migrazione completa][11]

## Comunicazioni e flusso di lavoro

La maggior parte delle applicazioni di Servizi Cloud è costituita da più di un livello. Analogamente, un'applicazione di Service Fabric è costituita da più di un servizio, in genere molti servizi. Due modelli di comunicazione comuni sono la comunicazione diretta e quella tramite una risorsa di archiviazione esterna permanente.

### Comunicazione diretta

Con la comunicazione diretta i livelli possono comunicare direttamente tramite l'endpoint esposto da ogni livello. In ambienti senza stati, ad esempio Servizi cloud, questo significa selezionare un'istanza di un ruolo VM, in modo casuale o con bilanciamento del carico round robin, e connettersi direttamente all'endpoint.

![Comunicazione diretta di Servizi cloud][5]

 La comunicazione diretta è un modello di comunicazione comune in Service Fabric. La differenza principale tra Service Fabric e Servizi cloud è che in Servizi cloud ci si connette a una macchina virtuale, mentre in Service Fabric ci si connette a un servizio. Questa è una distinzione importante per due motivi:

 - I servizi di Service Fabric non sono associati alle macchine virtuali che li ospitano. I servizi possono spostarsi all'interno del cluster e, in effetti, è previsto che si spostino per vari motivi, ovvero per bilanciamento delle risorse, failover, aggiornamenti di applicazioni e infrastruttura e vincoli di bilanciamento di carico o selezione. Ciò significa che l'indirizzo di un'istanza del servizio può cambiare in qualsiasi momento.
 - Una macchina virtuale in Service Fabric può ospitare più servizi, ognuno con un endpoint univoco.

Service Fabric fornisce un meccanismo di individuazione del servizio, detto Naming Service, che può essere usato per risolvere gli indirizzi degli endpoint dei servizi.

![Comunicazione diretta di Service Fabric][6]

### Queues

Un meccanismo di comunicazione comune tra i livelli in ambienti senza stati come Servizi cloud consiste nell'usare una coda di archiviazione esterna per archiviare in modo permanente le attività di lavoro da un livello a un altro. Uno scenario comune è un livello web che invia processi a una coda di Azure o un bus di servizio in cui le istanze del ruolo di lavoro possono rimuovere dalla coda ed elaborare i processi.

![Comunicazione con coda di Servizi cloud][7]

Lo stesso modello di comunicazione può essere usato in Service Fabric. Può essere utile durante la migrazione di un'applicazione di Servizi cloud esistente a Service Fabric.

![Comunicazione diretta di Service Fabric][8]
 
## Passaggi successivi

Il percorso di migrazione più semplice da Servizi cloud a Service Fabric consiste nel sostituire solo la distribuzione di Servizi cloud con un'applicazione di Service Fabric, conservando approssimativamente la stessa l'architettura complessiva. L'articolo seguente fornisce una guida per convertire un ruolo di lavoro o Web in un servizio di Service Fabric senza stato.

 - [Migrazione semplice: convertire un ruolo di lavoro o Web a un servizio di Service Fabric senza stato](./service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png

<!---HONumber=AcomDC_0803_2016-->