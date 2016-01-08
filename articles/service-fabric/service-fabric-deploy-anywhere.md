<properties
   pageTitle="Distribuzione in qualsiasi ambiente con l'infrastruttura di servizi (Windows Server e Linux) | Microsoft Azure"
   description="I cluster dell'infrastruttura di servizi verranno eseguiti in Windows Server e Linux, consentendo di distribuire e ospitare le applicazioni dell'infrastruttura di servizi in qualsiasi ambiente che esegue Windows Server o Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunalds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/19/2015"
   ms.author="kunalds"/>

# "Distribuzione in qualsiasi ambiente" su Windows Server o Linux con Service Fabric
Con l'infrastruttura di servizi sarà possibile creare cluster dell'infrastruttura di servizi su qualsiasi macchina virtuale o computer che esegue Windows Server o Linux. In questo modo le applicazioni dell'infrastruttura di servizi possono essere distribuite ed eseguite in qualsiasi ambiente, in locale o in qualsiasi provider di servizi cloud in cui si dispone di un set di computer Windows Server o Linux interconnessi. L'infrastruttura di servizi fornirà un pacchetto di installazione per la creazione di cluster dell'infrastruttura di servizi. Il vantaggio principale della "Distribuzione in qualsiasi ambiente" è che non esiste alcun blocco da parte del fornitore quando si compila un'applicazione usando Service Fabric, dal momento che è possibile scegliere dove eseguire le applicazioni. La funzionalità aumenta anche la possibilità di raggiungere una più ampia base di clienti in quanto diversi clienti possono avere requisiti variabili per gli ambienti in cui desiderano eseguire le applicazioni, ad esempio i clienti del settore sanitario e finanziario possono avere esigenze diverse rispetto a un produttore di automobili o a un'azienda di viaggi. L'anteprima tecnica di questa funzionalità dovrebbe essere rilasciata nel primo trimestre del 2016.

## Sistemi operativi supportati
Sarà possibile creare cluster in macchine virtuali o in computer che eseguono questi sistemi operativi: * Windows Server 2012 R2 * Windows Server 2016 * Linux

## Linguaggi di programmazione supportati
Sarà possibile scrivere applicazioni dell'infrastruttura di servizi in questi linguaggi di programmazione: * C# * Java

## Creazione e configurazione di cluster
Service Fabric fornirà un pacchetto di installazione che sarà possibile scaricare dall'Area download Microsoft. Dopo aver scaricato il pacchetto, sarà necessario apportare modifiche a un file di configurazione per specificare le impostazioni del cluster. Dopo aver modificato le impostazioni del cluster, sarà necessario eseguire uno script di installazione per creare il cluster che si estenderà tra i computer specificati nelle impostazioni del cluster. I dettagli esatti del processo di installazione saranno condivisi al momento del rilascio dell'anteprima di questa funzionalità nel primo trimestre del 2016.

## Confronto tra distribuzioni cloud e distribuzioni locali
Il processo di creazione di un cluster locale dell'infrastruttura di servizi sarà simile al processo di creazione di un cluster in qualsiasi servizio cloud in cui si dispone di un set di macchine virtuali. I passaggi iniziali per eseguire il provisioning delle macchine virtuali saranno controllati dall'ambiente cloud o locale in uso. Dopo aver ottenuto un set di macchine virtuali con connettività di rete abilitata, i passaggi successivi per configurare il pacchetto dell'infrastruttura di servizi, modificare le impostazioni del cluster ed eseguire lo script di creazione e gestione del cluster risulteranno simili. In questo modo le conoscenze e l'esperienza relative all'uso e alla gestione dei cluster dell'infrastruttura dei servizi potranno essere usate anche quando si scelgono nuovi ambienti di hosting.

## Vantaggi della funzionalità Distribuzione in qualsiasi ambiente
* Nessun blocco da parte del fornitore.
* Dopo la scrittura, le applicazioni dell'infrastruttura di servizi possono essere eseguite in più ambienti di hosting senza alcuna modifica o con cambiamenti minimi.
* Le conoscenze acquisite nella creazione di applicazioni dell'infrastruttura di servizi saranno utili anche per altre piattaforme di hosting.
* L'esperienza operativa derivata dall'esecuzione di cluster dell'infrastruttura di servizi sarà utile anche per altri ambienti di distribuzione.
* Ampia copertura di clienti non limitata da vincoli dell'ambiente di hosting.
* Livello aggiuntivo di affidabilità e protezione contro interruzioni generalizzate tramite lo spostamento dei servizi in un altro ambiente di distribuzione in caso di blackout di un centro dati o del provider di servizi cloud.

## Differenze tra Distribuzione in qualsiasi ambiente e i cluster dell'infrastruttura di servizi ospitati in Azure
L'esecuzione di cluster dell'infrastruttura di servizi tramite il servizio ospitato di Azure offre alcuni vantaggi rispetto all'opzione Distribuzione in qualsiasi ambiente, pertanto se non sono presenti esigenze specifiche relative all'ambiente di esecuzione dei cluster è preferibile eseguirli come cluster ospitati in Azure. In Azure forniamo l'integrazione con altre funzionalità e servizi di Azure che semplificano le operazioni e la gestione del cluster:

* Portale di Azure: il portale di Azure semplifica la creazione e la gestione dei cluster.
* Gestione risorse di Azure: l’utilizzo di ARM consente una gestione semplificata di tutte le risorse utilizzate dal cluster come unità e semplifica il tracciamento dei costi e la fatturazione. 
* Diagnostica: in Azure offriamo l’integrazione con WAD (diagnostica di Microsoft Azure) e informazioni operative per la diagnostica.
* Scalabilità automatica: per i cluster ospitati in Azure forniremo funzionalità di scalabilità automatica integrate. In altri ambienti se si usa la funzionalità Distribuzione in qualsiasi ambiente sarà necessario creare funzionalità di scalabilità automatica personalizzate o scalare manualmente tramite le API esposte dall'infrastruttura di servizi per la scalabilità dei cluster.

<!---HONumber=AcomDC_1203_2015-->