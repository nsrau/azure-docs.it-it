<properties
   pageTitle="Creare cluster di Azure Service Fabric su Windows Server e Linux | Microsoft Azure"
   description="I cluster di Service Fabric vengono eseguiti in Windows Server e Linux, per poter distribuire e ospitare le applicazioni di Service Fabric in qualsiasi ambiente che esegue Windows Server o Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/05/2016"
   ms.author="chackdan"/>

# Creare cluster autonomi di Service Fabric in Windows Server o Linux
Azure Service Fabric permette di creare cluster Service Fabric su qualsiasi macchina virtuale o computer con Windows Server o Linux in esecuzione. In questo modo, sarà possibile distribuire ed eseguire applicazioni di Service Fabric in qualsiasi ambiente in cui è presente un set interconnesso di computer Windows Server o Linux, in locale o con qualsiasi provider cloud.

**Nota**: la creazione di un cluster in Azure deve essere eseguita mediante un modello di Azure Resource Manager o il portale di Azure. Per altre informazioni, vedere [Creare un cluster di Service Fabric cluster usando un modello di Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) o [Creare un cluster di Service Fabric dal portale di Azure](service-fabric-cluster-creation-via-portal.md).

Service Fabric fornisce un pacchetto di installazione per la creazione dei cluster autonomi di Service Fabric in locale. Il vantaggio principale di questo approccio è che non esiste alcun blocco da parte del fornitore quando si compila un'applicazione usando Service Fabric, dal momento che è possibile scegliere dove eseguire le applicazioni. Questo approccio aumenta anche la possibilità di raggiungere una più ampia base di clienti, perché i requisiti dei clienti possono variare in base agli ambienti in cui si intende eseguire le applicazioni. I clienti del settore sanitario e finanziario, ad esempio, possono avere esigenze diverse rispetto a un produttore di automobili o a un'azienda di viaggi.

## Sistemi operativi supportati
Sarà possibile creare cluster in macchine virtuali o in computer che eseguono questi sistemi operativi:

* Windows Server 2012 R2
* Windows Server 2016
* Linux

Per altre informazioni su Windows Server, vedere [Creazione di cluster di Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md)

## Creazione e configurazione di cluster
Service Fabric fornisce un pacchetto di installazione che è possibile scaricare. Dopo aver scaricato il pacchetto, sarà necessario apportare modifiche a un file di configurazione JSON per specificare le impostazioni del cluster. Dopo aver modificato le impostazioni del cluster, è necessario eseguire uno script di installazione per creare il cluster che si estenderà ai computer specificati nelle impostazioni del cluster. È anche possibile eseguire uno script per rimuovere un cluster da un set di computer.

## Confronto tra distribuzioni cloud e distribuzioni locali
Il processo di creazione di un cluster di Service Fabric locale è simile al processo adottato per la creazione di un cluster in qualsiasi ambiente cloud con un set di macchine virtuali. I passaggi iniziali per eseguire il provisioning delle macchine virtuali saranno controllati dal provider cloud o dall'ambiente locale in uso. Dopo aver ottenuto un set di macchine virtuali con connettività di rete abilitata, i passaggi successivi per configurare il pacchetto di Service Fabric, modificare le impostazioni del cluster ed eseguire gli script di creazione e gestione del cluster risulteranno identici. In questo modo le conoscenze e l'esperienza relative all'uso e alla gestione dei cluster dell'infrastruttura dei servizi potranno essere usate anche quando si scelgono nuovi ambienti di hosting.

## Vantaggi della creazione di cluster autonomi di Service Fabric
* Poiché non esiste alcun blocco da parte del fornitore, è possibile scegliere dove creare il cluster.
* Dopo essere state scritte, le applicazioni di Service Fabric possono essere eseguite in più ambienti di hosting senza alcuna modifica o con cambiamenti minimi.
* Le conoscenze acquisite nella creazione di applicazioni di Service Fabric saranno utili anche per altri ambienti di hosting.
* L'esperienza operativa derivata dall'esecuzione e dalla gestione di cluster di Service Fabric sarà utile anche per altri ambienti.
* Ampia copertura di clienti non limitata da vincoli dell'ambiente di hosting.
* Un livello aggiuntivo di affidabilità e protezione contro interruzioni generalizzate per consentire lo spostamento dei servizi in un altro ambiente di distribuzione in caso di blackout di un centro dati o del provider di servizi cloud.

## Vantaggi dei cluster di Service Fabric in Azure rispetto ai cluster autonomi di Service Fabric creati in locale
L'esecuzione di cluster di Service Fabric in Azure offre alcuni vantaggi rispetto all'opzione locale. Se non sono presenti esigenze specifiche in merito all'ambiente di esecuzione dei cluster, quindi, è preferibile eseguirli in Azure. Per semplificare e rendere più affidabili le operazioni e la gestione del cluster, in Azure viene offerta l'integrazione con altre funzionalità e servizi di Azure.

* **Portale di Azure:** il portale di Azure semplifica la creazione e la gestione dei cluster.

* **Gestione risorse di Azure:** l'uso di Gestione risorse di Azure consente di gestire facilmente tutte le risorse usate dal cluster come singola unità e semplifica il tracciamento dei costi e la fatturazione.
* **Cluster di Service Fabric come risorsa di Azure** Un cluster di Service Fabric è una risorsa di Azure Resource Manager, quindi è possibile modellarla in modo analogo alle altre risorse di Azure Resource Manager in Azure.
* **Integrazione con l'infrastruttura di Azure** Service Fabric si coordina con l'infrastruttura di Azure per il sistema operativo, la rete e altri aggiornamenti, in modo da migliorare la disponibilità e l'affidabilità delle applicazioni.  
* **Diagnostica:** in Azure viene offerta l'integrazione con Diagnostica di Azure e Operational Insights.
* **Ridimensionamento automatico:** per i cluster in Azure è disponibile la funzionalità predefinita di ridimensionamento automatico, grazie a set di ridimensionamento delle macchine virtuali. In locale e in altri ambienti cloud sarà necessario sviluppare una funzionalità di ridimensionamento automatico specifica oppure ridimensionare manualmente mediante le API esposte da Service Fabric per il ridimensionamento dei cluster.

## Passaggi successivi
Creare un cluster nelle VM o nei computer che eseguono Windows Server: [Creazione di cluster di Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md)

Creare un cluster nelle VM o nei computer che eseguono Linux: [Service Fabric su Linux](service-fabric-linux-overview.md)

<!---HONumber=AcomDC_0406_2016-->