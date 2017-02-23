---
title: Panoramica delle VM Linux in Azure | Documentazione Microsoft
description: Descrive i servizi di calcolo, archiviazione e rete di Azure con macchine virtuali Linux.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2016
ms.author: squillace
translationtype: Human Translation
ms.sourcegitcommit: 4d2bd4bbcaf889ee25cc4567772384b167166c10
ms.openlocfilehash: 736f30768da968f8e1d39ff94fe9de66cc219321


---
# <a name="azure-and-linux"></a>Azure e Linux
Microsoft Azure è una raccolta in continua crescita di servizi cloud pubblici integrati, che includono analisi, macchine virtuali, database, dispositivi mobili, rete, archiviazione, servizi cloud e Web&mdash;, ideali per l'hosting delle soluzioni.  Microsoft Azure fornisce una piattaforma di elaborazione scalabile che consente di pagare solo ciò che si usa, quando necessario, senza dover investire in hardware locale.  Azure è subito pronto quando occorre aumentare le prestazioni e le istanze delle soluzioni per ottenere la massima scalabilità necessaria per soddisfare le esigenze dei clienti.

Se si ha familiarità con le varie caratteristiche di Amazon AWS, è possibile esaminare il [documento di mapping delle definizioni](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/)di confronto tra Visual Studio Azure e AWS.

## <a name="regions"></a>Regioni
Le risorse di Microsoft Azure vengono distribuite tra più aree geografiche in tutto il mondo.  Una "area" rappresenta più data center in una singola area geografica.  Al 1° gennaio 2016 risultano incluse: 8 in America, 2 in Europa, 6 Asia Pacifico, 2 in Cina continentale e 3 in India.  Per un elenco completo di tutte le aree di Azure, è disponibile un elenco delle aree esistenti e annunciate di recente.

* [Aree di Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Disponibilità
Per qualificarsi per la garanzia del contratto di servizio del 99,95% per le VM, devono essere distribuite due o più macchine virtuali che eseguono il carico di lavoro nello stesso set di disponibilità. In questo modo le macchine virtuali vengono distribuite tra più domini di errore nei centri dati Microsoft e anche in host con finestre di manutenzione diverse. La versione completa del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) descrive la disponibilità garantita di Azure nel suo complesso. 

## <a name="managed-disks"></a>Managed Disks

Il servizio Managed Disks gestisce in background le operazioni di creazione e gestione dell'account di archiviazione di Azure ed elimina la necessità di preoccuparsi dei limiti di scalabilità dell'account di archiviazione. È sufficiente specificare le dimensioni del disco e il livello di prestazioni, Standard o Premium, e Azure si occuperà della creazione e della gestione del disco. Anche se si aggiungono dischi o si ridimensiona la macchina virtuale, non è necessario preoccuparsi dello spazio di archiviazione usato. Se si creano nuove macchine virtuali, [usare l'interfaccia della riga di comando di Azure 2.0 (Anteprima)](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o il portale di Azure per creare macchine virtuali con dischi dati o del sistema operativo gestiti. Se si dispone di macchine virtuali con dischi non gestiti, è possibile [convertire le macchine virtuali per eseguire il backup con Managed Disks](virtual-machines-linux-convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
 
È anche possibile gestire le immagini personalizzate in un unico account di archiviazione per ogni area di Azure e usarle per creare centinaia di macchine virtuali nella stessa sottoscrizione. Per altre informazioni sul servizio Managed Disks, vedere [Azure Managed Disks Overview](../storage/storage-managed-disks-overview.md) (Panoramica di Azure Managed Disks).
 
## <a name="azure-virtual-machines--instances"></a>Macchine virtuali di Azure e istanze
Microsoft Azure supporta l'esecuzione di numerose distribuzioni comuni di Linux fornite e gestite da diversi partner.  In Azure Marketplace sono disponibili distribuzioni come Red Hat Enterprise, CentOS, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD e altre ancora. Microsoft collabora attivamente con diverse community Linux per aggiungere altre versioni all'elenco delle [distribuzioni Linux approvate per Azure](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Se la distribuzione di Linux preferita non è attualmente presente nella raccolta, è possibile usare una VM "Bring your own Linux" [creando e caricando un VHD Linux in Azure](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Con Macchine virtuali di Azure è possibile distribuire in modo flessibile un'ampia gamma di soluzioni di elaborazione. È possibile distribuire praticamente qualsiasi carico di lavoro e in qualsiasi lingua in quasi tutti i sistemi operativi come Windows, Linux oppure crearne uno personalizzato da uno dei nostri partner in costante aumento. Se ancora non si trova ciò che si sta cercando,  è anche possibile usare immagini personalizzate dall'ambiente locale.

## <a name="vm-sizes"></a>Dimensioni delle VM
Quando si distribuisce una VM in Azure, si selezionano le dimensioni della VM in una delle serie di dimensioni adatte per il proprio carico di lavoro. Le dimensioni influiscono anche sulla potenza di elaborazione, sulla memoria e sulla capacità di archiviazione della macchina virtuale. I costi vengono addebitati in base alla quantità di tempo in cui la VM è in esecuzione e utilizza le risorse allocate. Elenco completo delle [dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ecco alcune linee guida fondamentali per la selezione delle dimensioni di una VM da una delle serie disponibili (A, D, DS, G e GS).

* Le VM serie A sono VM di fascia bassa con prezzi vantaggiosi per carichi di lavoro leggeri e scenari di sviluppo e test. Sono ampiamente disponibili in tutte le aree e possono connettersi e usare tutte le risorse standard disponibili per le macchine virtuali.
* Le dimensioni della serie A (A8 - A11) sono speciali configurazioni a elevato utilizzo di calcolo adatte per applicazioni di cluster di calcolo ad alte prestazioni.
* Le macchine virtuali serie D sono progettate per eseguire le applicazioni che richiedono maggiore potenza di calcolo e prestazioni del disco temporaneo. Le macchine virtuali serie D forniscono processori più veloci, un rapporto tra memoria e memoria centrale superiore e un'unità SSD ( solid-state drive) per il disco temporaneo.
* La serie Dv2, la versione più recente della serie D, vanta una CPU più potente. La CPU della serie Dv2 è circa il 35% più rapida rispetto alla CPU della serie D. È basata sul processore Intel Xeon® E5-2673 v3 a 2,4 GHz (Haskell) e con tecnologia Intel Turbo Boost 2.0 può funzionare fino a 3,2 GHz. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.
* Le macchine virtuali serie G offrono la maggiore quantità di memoria e vengono eseguite su host con processori della famiglia Intel Xeon E5 V3.

Nota: le VM serie DS e GS hanno accesso all'archiviazione Premium, l'archiviazione a bassa latenza e alte prestazioni basata su unità SSD per carichi di lavoro con uso intensivo di I/O. L'archiviazione Premium è disponibile solo in determinate aree geografiche. Per informazioni dettagliate, vedere:

* [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage/storage-premium-storage.md)

## <a name="automation"></a>Automazione
Per ottenere le impostazioni cultura DevOps corrette, l'intera infrastruttura deve essere contenuta nel codice.  Quando l'intera infrastruttura è presente nel codice, può essere facilmente ricreata (server Phoenix).  Azure funziona con gli strumenti di automazione principali, come Ansible, Chef, SaltStack e Puppet.  Azure dispone di strumenti di automazione propri:

* [Modelli di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure VMAccess](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Azure sta implementando il supporto per [cloud-init](http://cloud-init.io/) nella maggior parte delle implementazioni Linux che lo supportano.  Attualmente le VM Ubuntu di Canonical vengono distribuite con cloud-init abilitato per impostazione predefinita.  Fedora, CentOS e RedHats RHEL supportano cloud-init, tuttavia nelle immagini Azure gestite tramite RedHat non è installato cloud-init.  Per usare cloud-init in un sistema operativo della famiglia RedHat, è necessario creare un'immagine personalizzata con cloud-init installato.

* [Uso di cloud-init nelle macchine virtuali Linux di Azure](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quotas"></a>Quote
Ogni sottoscrizione di Azure ha limiti di quota predefiniti che possono influire sulla distribuzione di un numero elevato di VM per un progetto. Il limite corrente per ogni sottoscrizione è di 20 macchine virtuali per area.  I limiti di quota possono essere aumentati creando un ticket di supporto in cui si richiede appunto un aumento del limite.  Per altre informazioni sui limiti di quota:

* [Limiti del servizio di sottoscrizione di Azure](../azure-subscription-service-limits.md)

## <a name="partners"></a>Partner
Microsoft collabora con i partner per verificare che le immagini disponibili vengano aggiornate e ottimizzate per un runtime Azure.  Per altre informazioni sui partner, controllare le pagine relative a Marketplace.

Linux in Azure - [Distribuzioni supportate](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Redhat - [Azure Marketplace - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)

Canonical - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)

Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)

FreeBSD - [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)

CoreOS - [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)

RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)

Bitnami - [Bitnami Library for Azure](https://azure.bitnami.com/)

Mesosphere - [Azure Marketplace - Mesosphere DC/OS on Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)

Docker - [Azure Marketplace - Azure Container Service con Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)

Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)

## <a name="getting-setup-on-azure"></a>Configurazione in Azure
Per iniziare a usare Azure, è necessario un account Azure, aver installato l'interfaccia della riga di comando di Azure e disporre di una coppia di chiavi pubblica e privata SSH.

### <a name="sign-up-for-an-account"></a>Iscriversi per ottenere un account
Il primo passaggio quando si usa il cloud di Azure è effettuare l'iscrizione per un account Azure.  Per iniziare, accedere alla pagina relativa alla [creazione di un account Azure](https://azure.microsoft.com/pricing/free-trial/) .

### <a name="install-the-cli"></a>Installare l'interfaccia della riga di comando
Con il nuovo account di Azure, è possibile iniziare a usare immediatamente il portale di Azure, che è un pannello di amministrazione basato sul Web.  Per gestire il cloud di Azure tramite la riga di comando, installare `azure-cli`.  Installare l'[interfaccia della riga di comando di Azure 2.0 (Anteprima)](/cli/azure/install) sulla workstation Mac o Linux.

### <a name="create-an-ssh-key-pair"></a>Creare una coppia di chiavi SSH
A questo punto si dispone di un account Azure, del portale Web di Azure e dell'interfaccia della riga di comando di Azure.  Il passaggio successivo consiste nel creare una coppia di chiavi SSH per accedere tramite SSH in Linux senza specificare una password.  [Creare le chiavi SSH in Linux e Mac](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per abilitare gli accessi senza password e migliorare la sicurezza.


### <a name="create-a-vm-using-the-cli"></a>Creare una macchina virtuale usando l'interfaccia della riga di comando
La creazione di una VM Linux usando l'interfaccia della riga di comando è un modo rapido per distribuire una VM senza uscire dal terminale in cui si sta operando.  Tutte le informazioni specificate nel portale Web sono disponibili tramite un flag o un'opzione della riga di comando.  

* [Creare una VM Linux usando l'interfaccia della riga di comando](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="create-a-vm-in-the-portal"></a>Creare una VM nel portale
La creazione di una VM Linux nel portale Web di Azure consente di selezionare facilmente le diverse opzioni per eseguire una distribuzione.  Anziché usare i flag o le opzioni della riga di comando, è possibile visualizzare un pratico layout Web con diverse opzioni e impostazioni.  Tutti gli elementi disponibili tramite l'interfaccia della riga di comando sono presenti anche nel portale.

* [Creare una VM Linux usando il portale](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="login-using-ssh-without-a-password"></a>Accesso tramite SSH senza una password
La VM è ora in esecuzione in Azure ed è possibile eseguire l'accesso.  L'uso di password per l'accesso tramite SSH non è sicuro e richiede tempo.  L'uso delle chiavi SSH è il modo più sicuro e rapido per eseguire l'accesso.  Quando si crea una VM Linux tramite il portale o l'interfaccia della riga di comando, sono disponibili due opzioni di autenticazione.  Se si sceglie una password per SSH, Azure configura la VM in modo da consentire gli accessi tramite password.  Se si sceglie di usare una chiave pubblica SSH, Azure configura la VM per consentire solo gli accessi tramite le chiavi SSH e disabilita gli accessi tramite password. Per proteggere la VM Linux consentendo solo gli accessi tramite le chiavi SSH, usare l'opzione di chiave pubblica SSH durante la creazione della VM nel portale o nell'interfaccia della riga di comando.

* [Disabilitare le password SSH nella VM Linux configurando SSHD](virtual-machines-linux-mac-disable-ssh-password-usage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="related-azure-components"></a>Componenti di Azure correlati
## <a name="storage"></a>Archiviazione
* [Introduzione ad Archiviazione di Microsoft Azure](../storage/storage-introduction.md)
* [Aggiungere un disco a una VM Linux tramite l'interfaccia della riga di comando di Azure](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Come collegare un disco dati a una macchina virtuale Linux nel portale di Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="networking"></a>Rete
* [Panoramica di Rete virtuale.](../virtual-network/virtual-networks-overview.md)
* [Indirizzi IP in Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Apertura di porte per una VM Linux in Azure](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare un nome di dominio completo nel portale di Azure](virtual-machines-linux-portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="containers"></a>Contenitori
* [Macchine virtuali e contenitori in Azure](virtual-machines-linux-containers.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Introduzione al servizio contenitore di Azure](../container-service/container-service-intro.md)
* [Distribuire un cluster del servizio contenitore di Azure](../container-service/container-service-deployment.md)

## <a name="next-steps"></a>Passaggi successivi
Quella descritta è panoramica di Linux in Azure.  Il passaggio successivo consiste nella creazione di alcune macchine virtuali.

* [Creare una VM Linux in Azure usando il portale](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una VM Linux in Azure tramite l'interfaccia della riga di comando](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Feb17_HO2-->


