 <properties
   pageTitle="Azure e Linux | Microsoft Azure"
   description="Descrive i servizi di calcolo, archiviazione e rete di Azure con macchine virtuali Linux."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="rickstercdn"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/01/2016"
   ms.author="rclaus"/>

# Azure e Linux
Microsoft Azure è una raccolta in continua crescita di servizi cloud pubblici integrati, che includono analisi, macchine virtuali, database, dispositivi mobili, rete, archiviazione, servizi cloud e Web, ideali per l'hosting delle soluzioni. Microsoft Azure fornisce una piattaforma di elaborazione scalabile che consente di pagare solo ciò che si usa, quando necessario, senza dover investire in hardware locale. Azure è subito pronto quando occorre aumentare le prestazioni e le istanze delle soluzioni per ottenere la massima scalabilità necessaria per soddisfare le esigenze dei clienti.
 
## Macchine virtuali di Azure
Con Macchine virtuali di Azure è possibile distribuire in modo flessibile un'ampia gamma di soluzioni di elaborazione. Distribuire una macchina virtuale di Windows o Linux dalla raccolta immagini o crearne una personalizzata da uno qualsiasi del crescente elenco di partner. È possibile distribuire praticamente qualsiasi carico di lavoro e in qualsiasi lingua in quasi tutti i sistemi operativi. Se ancora non si trova ciò che si sta cercando, è anche possibile usare immagini personalizzate dall'ambiente locale.
 
## Introduzione a Linux in Microsoft Azure

Usare insieme le macchine virtuali, le risorse di archiviazione e la rete di Microsoft Azure per fornire una "infrastruttura distribuita come servizio" (IaaS) di livello Internet per le esigenze di elaborazione di Linux. Per iniziare a usare Linux in Azure, sono necessari:

1. Account di valutazione gratuita. [Richiederne uno](https://azure.microsoft.com/pricing/free-trial/).
2. Interfaccia della riga di comando di Azure per Linux, Mac e Windows. [Installarla](../xplat-cli-install.md).
3. VM Linux. [Crearla](virtual-machines-linux-quick-create-cli.md).
4. Altre informazioni su Linux e Azure, incluse istruzioni su come qualificarsi per il Contratto di servizio (SLA). **È consigliabile leggere questo documento**.

## Logistica: Aree, distribuzioni, disponibilità, dimensioni delle VM e quote
### Regioni
Le risorse di Microsoft Azure vengono distribuite tra più aree geografiche in tutto il mondo. Una "area" rappresenta più data center in una singola area geografica. Al 1° gennaio 2016 risultano incluse: 8 in America, 2 in Europa, 6 Asia Pacifico, 2 in Cina continentale e 3 in India. Per un elenco completo di tutte le aree di Azure, viene mantenuto un elenco delle aree esistenti e annunciate di recente **[qui](https://azure.microsoft.com/regions/)**.

### Distribuzioni
Microsoft Azure supporta l'esecuzione di numerose distribuzioni comuni di Linux fornite e gestite da diversi partner. In Azure Marketplace sono disponibili distribuzioni come CentOS, Debian, Red Hat Enterprise, Ubuntu, FreeBSD e altre ancora. Microsoft collabora attivamente con diverse community Linux per aggiungere altre versioni all'elenco delle distribuzioni supportate. **[Estrarre le distribuzioni correnti](virtual-machines-linux-endorsed-distros.md)** Se la distribuzione di Linux preferita non è attualmente presente nella raccolta, è possibile usare una VM "Bring your own Linux" seguendo le linee guida **[in questa pagina.](virtual-machines-linux-create-upload-generic.md)**

## Disponibilità e contratto di servizio di Microsoft Azure
Per qualificarsi per la garanzia del contratto di servizio del 99,95% per le VM, devono essere distribuite due o più macchine virtuali che eseguono il carico di lavoro nello stesso set di disponibilità. In questo modo le macchine virtuali vengono distribuite tra più domini di errore nei centri dati Microsoft e anche in host con finestre di manutenzione diverse. Per informazioni dettagliate sul contratto di servizio è possibile visualizzarlo **[online qui](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/)**.

## Quote e dimensioni delle VM
Quando si distribuisce una VM in Azure, si selezionano le dimensioni della VM in una delle serie di dimensioni adatte per il proprio carico di lavoro. Le dimensioni influiscono anche sulla potenza di elaborazione, sulla memoria e sulla capacità di archiviazione della macchina virtuale. I costi vengono addebitati in base alla quantità di tempo in cui la VM è in esecuzione e utilizza le risorse allocate. Per un elenco più completo, vedere l'articolo sulle [dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md).

Ecco alcune linee guida fondamentali per la selezione delle dimensioni di una VM da una delle serie disponibili (A, D, DS, G e GS).

* Le VM serie A sono VM di fascia bassa con prezzi vantaggiosi per carichi di lavoro leggeri e scenari di sviluppo e test. Sono ampiamente disponibili in tutte le aree e possono connettersi e usare tutte le risorse standard disponibili per le macchine virtuali.
* Le dimensioni della serie A (A8 - A11) sono speciali configurazioni a elevato utilizzo di calcolo adatte per applicazioni di cluster di calcolo ad alte prestazioni.
* Le macchine virtuali serie D sono progettate per eseguire le applicazioni che richiedono maggiore potenza di calcolo e prestazioni del disco temporaneo. Le macchine virtuali serie D forniscono processori più veloci, un rapporto tra memoria e memoria centrale superiore e un'unità SSD ( solid-state drive) per il disco temporaneo. 
* La serie Dv2, la versione più recente della serie D, vanta una CPU più potente. La CPU della serie Dv2 è circa il 35% più rapida rispetto alla CPU della serie D. È basata sul processore Intel Xeon® E5-2673 v3 a 2,4 GHz (Haswalle) e con tecnologia Intel Turbo Boost 2.0 può funzionare fino a 3,2 GHz. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.
* Le macchine virtuali serie G offrono la maggiore quantità di memoria e vengono eseguite su host con processori della famiglia Intel Xeon E5 V3.

Nota: le VM serie DS e GS hanno accesso all'archiviazione Premium, l'archiviazione a bassa latenza e alte prestazioni basata su unità SSD per carichi di lavoro con uso intensivo di I/O. L'archiviazione Premium è disponibile solo in determinate aree geografiche. Per altre informazioni, vedere **[Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage/storage-premium-storage.md)**.

Ogni sottoscrizione di Azure ha limiti di quota predefiniti che possono influire sulla distribuzione di un numero elevato di VM per un progetto. Il limite corrente per ogni sottoscrizione è di 20 macchine virtuali per area. Questi limiti di quota possono essere aumentati creando un ticket di supporto in cui si richiede appunto un aumento del limite. Per altre informazioni sui limiti di quota, vedere**[Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md)**.

## Passaggi successivi

Account di valutazione gratuita. **[Richiederne uno](https://azure.microsoft.com/pricing/free-trial/)**. Se è già disponibile, provarlo. **[Installare l'interfaccia della riga di comando di Azure.](../xplat-cli-install.md)**. Se questa operazione è già stata eseguita, [creare una VM Linux ora](virtual-machines-linux-quick-create-cli.md).

<!---HONumber=AcomDC_0420_2016-->