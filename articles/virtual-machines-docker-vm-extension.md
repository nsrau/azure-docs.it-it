<properties 
	pageTitle="Estensione della macchina virtuale Docker per Linux in Azure" 
	description="Descrive Docker e i contenitori, le estensioni di Macchine virtuali di Azure e le risorse aggiuntive per creare contenitori Docker da xplat-cli e dal portale." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services" 
	ms.date="02/11/2015" 
	ms.author="rasquill"/>

# Estensione della macchina virtuale Docker per Linux in Azure
[Docker](https://www.docker.com/) è uno dei più popolari approcci alla virtualizzazione che usa [contenitori Linux](http://en.wikipedia.org/wiki/LXC) invece di macchine virtuali allo scopo di isolare i dati ed eseguire i calcoli su risorse condivise. È possibile usare l'estensione della macchina virtuale Docker per l'[Agente Linux di Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/) per creare una VM Docker che ospiti un numero qualsiasi di contenitori per le applicazioni su Azure.

Questo argomento descrive:

+ [Contenitori Docker e Linux]
+ [Come usare l'estensione della VM Docker con Azure]
+ [Estensioni della macchina virtuale per Linux e Windows] 

Per creare subito le macchine virtuali abilitate per Docker, vedere:

+ [Come usare l'estensione della VM Docker dall'interfaccia multipiattaforma di Azure (xplat-cli)]
+ [Come usare l'estensione della VM Docker con il portale Azure]
+ [Come iniziare a usare rapidamente Docker in Azure Marketplace]

## <a id='Docker and Linux Containers'>Contenitori Docker e Linux</a>
[Docker](https://www.docker.com/) è uno dei più popolari approcci alla virtualizzazione che usa [contenitori Linux](http://en.wikipedia.org/wiki/LXC) invece di macchine virtuali allo scopo di isolare i dati ed eseguire i calcoli su risorse condivise. Inoltre, fornisce altri servizi che consentono di creare o assemblare rapidamente applicazioni e distribuirle tra altri contenitori Docker.

I contenitori Docker e Linux non sono [Hypervisor](http://en.wikipedia.org/wiki/Hypervisor) come Windows Hyper-V e [KVM](http://www.linux-kvm.org/page/Main_Page) su Linux (vi sono molti altri esempi). Gli hypervisor virtualizzano il sistema operativo sottostante per consentire l'esecuzione di sistemi operativi completi all'interno di un hypervisor come se fossero un'applicazione. 

Docker e altri approcci di tipo *container* hanno radicalmente ridotto sia i tempi di avvio che quelli di elaborazione, oltre ai costi di archiviazione, richiesti dall'uso del processo e dalle funzionalità di isolamento del file system del kernel Linux, in modo da esporre solo le funzionalità del kernel a un contenitore altrimenti isolato.

La seguente tabella descrive a un livello molto elevato le differenze in termini di funzionalità che esistono tra gli hypervisor e i contenitori Linux. Alcune funzionalità possono essere più o meno utili a seconda delle esigenze collegate alla propria applicazione.

|   Funzionalità      | Hypervisor | Contenitori  |
| :------------- |-------------| ----------- |
| Isolamento del processo | Più o meno completi | Se si ottiene la radice, l'host del contenitore potrebbe risultare compromesso |
| Memoria su disco richiesta | Sistema operativo completo più app | Solo requisiti app |
| Tempo richiesto per l'avvio | Notevolmente più lungo: avvio del sistema operativo più caricamento delle app | Notevolmente più breve: è necessario avviare solo le app perché il kernel è già in esecuzione  |
| Automazione del contenitore | Varia notevolmente a seconda del sistema operativo e delle app | [Raccolta di immagini docker](https://registry.hub.docker.com/); altri 

Per assistere a una discussione generale sui contenitori e i relativi vantaggi, guardare questa [sessione con lavagna condivisa relativa a Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

Per altre informazioni su Docker e su come funziona, vedere le [informazioni su Docker](https://www.docker.com/whatisdocker/)

#### Procedure consigliate per la sicurezza dei contenitori Docker e Linux

Poiché i contenitori condividono l'accesso al kernel del computer host, se il malware riesce a penetrare potrebbe accedere non solo al computer host, ma anche agli altri contenitori. Per proteggere il sistema del contenitore più efficacemente rispetto alla configurazione predefinita, [Docker consiglia](https://docs.docker.com/articles/security/) di usare criteri di gruppo aggiuntivi o la [sicurezza basata sui ruoli](http://en.wikipedia.org/wiki/Role-based_access_control), ad esempio [SELinux](http://selinuxproject.org/page/Main_Page) o [AppArmor](http://wiki.apparmor.net/index.php/Main_Page), oltre a ridurre il più possibile le capacità kernel concesse ai contenitori. Su Internet ci sono molti altri documenti in cui si descrivono gli approcci alla sicurezza quando si usano contenitori come Docker.

## <a id='How to use the Docker VM Extension with Azure'>Come usare l'estensione della VM Docker con Azure</a>

L'estensione della VM Docker è un componente installato nell'istanza della macchina virtuale creata che a sua volta installa il motore Docker e gestisce la comunicazione remota con la macchina virtuale. Esistono due metodi per installare l'estensione della VM: È possibile creare la VM dal portale di gestione oppure dall'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli). 

È possibile usare il portale per aggiungere l'estensione della VM Docker a qualsiasi macchina virtuale Linux (attualmente l'unica immagine che la supporta è Ubuntu 14.04 LTS, nelle versioni rilasciate dopo luglio). Usando la riga di comando xplat-cli, tuttavia, è possibile installare l'estensione della VM Docker e creare e caricare i certificati di comunicazione Docker simultaneamente durante la creazione dell'istanza della macchina virtuale.

Per creare subito le macchine virtuali abilitate per Docker, vedere:

+ [Come usare l'estensione della VM Docker dall'interfaccia multipiattaforma di Azure (xplat-cli)]
+ [Come usare l'estensione della VM Docker con il portale Azure]

## <a id='Virtual Machine Extensions for Linux and Windows'>Estensioni della macchina virtuale per Linux e Windows</a>
L'estensione della VM Docker per Azure è solo una delle varie estensioni VM che offrono un comportamento speciale e molte altre sono attualmente in corso di sviluppo. Ad esempio, diverse funzionalità dell'[estensione Agente VM Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/) consentono di modificare e gestire la macchina virtuale, incluse le funzionalità di sicurezza, kernel, rete e così via. L'estensione VMAccess, ad esempio, consente di reimpostare la password amministratore o la chiave SSH.

Per un elenco completo, vedere [Estensioni della VM di Azure](http://msdn.microsoft.com/library/azure/dn606311.aspx).

<!--Anchors-->
[Come usare l'estensione della VM Docker dall'interfaccia multipiattaforma di Azure (xplat-cli)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Come usare l'estensione della VM Docker con il portale Azure]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/
[Come iniziare a usare rapidamente Docker in Azure Marketplace]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-ubuntu-quickstart/
[Contenitori Docker e Linux]: #Docker-and-Linux-Containers
[Come usare l'estensione della VM Docker con Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Estensioni della macchina virtuale per Linux e Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows

<!--HONumber=47-->
