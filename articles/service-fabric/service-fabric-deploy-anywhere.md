<properties
   pageTitle="Distribuzione in qualsiasi ambiente con Azure Service Fabric in Windows Server e Linux | Microsoft Azure"
   description="I cluster di Service Fabric verranno eseguiti in Windows Server e Linux, in modo da poter distribuire e ospitare le applicazioni di Service Fabric in qualsiasi ambiente che esegue Windows Server o Linux."
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
   ms.date="02/10/2016"
   ms.author="chackdan"/>

# "Distribuzione in qualsiasi ambiente" su Windows Server o Linux con Service Fabric
Con l'aggiunta di "Distribuzione in qualsiasi ambiente", Azure Service Fabric consentirà la creazione di cluster di Service Fabric su qualsiasi macchina virtuale o computer che esegue Windows Server o Linux. In questo modo, sarà possibile distribuire ed eseguire applicazioni di Service Fabric in qualsiasi ambiente in cui è presente un set interconnesso di computer Windows Server o Linux, in locale o con qualsiasi provider cloud.

 Service Fabric offre un pacchetto di installazione per la creazione di cluster di Service Fabric. Il vantaggio principale della "Distribuzione in qualsiasi ambiente" è che non esiste alcun blocco da parte del fornitore quando si compila un'applicazione usando Service Fabric, dal momento che è possibile scegliere dove eseguire le applicazioni. Questa funzionalità aumenta anche la possibilità di raggiungere una più ampia base di clienti, poiché i requisiti dei clienti possono variare in base agli ambienti in cui si intende eseguire le applicazioni. I clienti del settore sanitario e finanziario, ad esempio, possono avere esigenze diverse rispetto a un produttore di automobili o a un'azienda di viaggi.

L'anteprima tecnica di questa funzionalità dovrebbe essere rilasciata nel primo trimestre del 2016.

## Sistemi operativi supportati
Sarà possibile creare cluster in macchine virtuali o in computer che eseguono questi sistemi operativi: * Windows Server 2012 * Windows Server 2012 R2 * Windows Server 2016 * Linux

## Linguaggi di programmazione supportati
Sarà possibile scrivere applicazioni di Service Fabric in questi linguaggi di programmazione: * C# * Java

## Creazione e configurazione di cluster
Service Fabric fornisce un pacchetto di installazione che è possibile scaricare. Dopo aver scaricato il pacchetto, sarà necessario apportare modifiche a un file di configurazione per specificare le impostazioni del cluster. Dopo aver modificato le impostazioni del cluster, sarà necessario eseguire uno script di installazione per creare il cluster che si estenderà ai computer specificati nelle impostazioni del cluster.

I dettagli esatti del processo di installazione saranno condivisi al momento del rilascio dell'anteprima di questa funzionalità nel primo trimestre del 2016.

## Confronto tra distribuzioni cloud e distribuzioni locali
Il processo di creazione di un cluster di Service Fabric locale sarà simile al processo adottato per la creazione di un cluster in qualsiasi ambiente cloud con un set di macchine virtuali. I passaggi iniziali per eseguire il provisioning delle macchine virtuali saranno controllati dall'ambiente cloud o locale in uso. Dopo aver ottenuto un set di macchine virtuali con connettività di rete abilitata, i passaggi successivi per configurare il pacchetto di Service Fabric, modificare le impostazioni del cluster ed eseguire gli script di creazione e gestione del cluster risulteranno simili. In questo modo, l'esperienza e le conoscenze relative all'utilizzo e alla gestione dei cluster di Service Fabric potranno essere usate anche quando si scelgono nuovi ambienti di hosting.

## Vantaggi della funzionalità Distribuzione in qualsiasi ambiente
* Poiché non esiste alcun blocco da parte del fornitore, è possibile scegliere dove eseguire le applicazioni.
* Dopo essere state scritte, le applicazioni di Service Fabric possono essere eseguite in più ambienti di hosting senza alcuna modifica o con cambiamenti minimi.
* Le conoscenze acquisite nella creazione di applicazioni dell'infrastruttura di servizi saranno utili anche per altre piattaforme di hosting.
* L'esperienza operativa derivata dall'esecuzione di cluster dell'infrastruttura di servizi sarà utile anche per altri ambienti di distribuzione.
* Ampia copertura di clienti non limitata da vincoli dell'ambiente di hosting.
* Un livello aggiuntivo di affidabilità e protezione contro interruzioni generalizzate per consentire lo spostamento dei servizi in un altro ambiente di distribuzione in caso di blackout di un centro dati o del provider di servizi cloud.

## Vantaggi dei cluster gestiti da Service Fabric in Azure rispetto ai cluster di Service Fabric creati e gestiti mediante "Distribuzione in qualsiasi ambiente"
L'esecuzione di cluster di Service Fabric in Azure offre alcuni vantaggi rispetto all'uso dell'opzione Distribuzione in qualsiasi ambiente. Se non sono presenti esigenze specifiche in merito all'ambiente di esecuzione dei cluster, quindi, è preferibile eseguirli in Azure. Per semplificare le operazioni e la gestione del cluster, in Azure viene offerta l'integrazione con altre funzionalità e servizi di Azure:

* **Portale di Azure:** il portale di Azure semplifica la creazione e la gestione dei cluster.
* **Gestione risorse di Azure:** l'uso di Gestione risorse di Azure consente di gestire facilmente tutte le risorse usate dal cluster come singola unità e semplifica il tracciamento dei costi e la fatturazione.
* Il cluster di Service Fabric per le **Risorse di Azure** è una risorsa ARM, quindi è possibile modellarlo in modo analogo ad altre risorse ARM in Azure.
* **Diagnostica:** in Azure viene offerta l'integrazione con Diagnostica di Azure e Operational Insights.
* **Scalabilità automatica:** per i cluster eseguiti in Azure vengono fornite funzionalità di scalabilità automatica integrate. In altri ambienti, se si usa la funzionalità Distribuzione in qualsiasi ambiente, sarà necessario creare funzionalità di scalabilità automatica personalizzate oppure scalare manualmente tramite le API esposte da Service Fabric per la scalabilità dei cluster.

<!---HONumber=AcomDC_0224_2016-->