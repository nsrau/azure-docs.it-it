---
title: Panoramica delle VM Linux in Azure | Microsoft Docs
description: Descrive i servizi di calcolo, archiviazione e rete di Azure con macchine virtuali Linux.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: timlt
editor: 
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/29/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: cdb2bda0c3f7e73b115c2609c3f229c633093bdc
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="azure-and-linux"></a>Azure e Linux
Microsoft Azure è una raccolta in continua crescita di servizi cloud pubblici integrati, che includono analisi, macchine virtuali, database, dispositivi mobili, rete, archiviazione, servizi cloud e Web&mdash;, ideali per l'hosting delle soluzioni.  Microsoft Azure fornisce una piattaforma di elaborazione scalabile che consente di pagare solo ciò che si usa, quando necessario, senza dover investire in hardware locale.  Azure è subito pronto quando occorre aumentare le prestazioni e le istanze delle soluzioni per ottenere la massima scalabilità necessaria per soddisfare le esigenze dei clienti.

Se si ha familiarità con le varie caratteristiche di Amazon AWS, è possibile esaminare il [documento di mapping delle definizioni](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/)di confronto tra Visual Studio Azure e AWS.

## <a name="regions"></a>Regioni
Le risorse di Microsoft Azure vengono distribuite tra più aree geografiche in tutto il mondo.  Una "area" rappresenta più data center in una singola area geografica. A novembre 2017 Azure dispone di 36 aree con disponibilità generale in tutto il mondo e sono state annunciate altre 6 aree aggiuntive. L'elenco aggiornato delle aree esistenti e annunciate di recente è disponibile nella pagina seguente:

* [Aree di Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Disponibilità
È stato annunciato un contratto di servizio leader del settore pari al 99,9% per una macchina virtuale a istanza singola, purché distribuita con Archiviazione Premium per tutti i dischi.  Perché la distribuzione si qualifichi per il contratto di servizio standard del 99,95% per le macchine virtuali, è comunque necessario distribuire due o più macchine virtuali che eseguono il carico di lavoro in un set di disponibilità. Un set di disponibilità assicura che le macchine virtuali vengano distribuite tra più domini di errore nei centri dati Azure e anche in host con finestre di manutenzione diverse. La versione completa del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) descrive la disponibilità garantita di Azure nel suo complesso.

## <a name="managed-disks"></a>Managed Disks

Il servizio Managed Disks gestisce in background le operazioni di creazione e gestione dell'account di archiviazione di Azure ed elimina la necessità di preoccuparsi dei limiti di scalabilità dell'account di archiviazione. Specificare le dimensioni del disco e il livello di prestazioni, Standard o Premium, e Azure si occuperà della creazione e della gestione del disco. Durante l'aggiunta di dischi o il ridimensionamento della macchina virtuale non è necessario preoccuparsi dello spazio di archiviazione usato. Se si creano nuove macchine virtuali, [usare l'interfaccia della riga di comando di Azure 2.0](quick-create-cli.md) o il portale di Azure per creare macchine virtuali con dischi dati e del sistema operativo gestiti. Se si dispone di macchine virtuali con dischi non gestiti, è possibile [convertire le macchine virtuali per eseguire il backup con Managed Disks](convert-unmanaged-to-managed-disks.md).

È anche possibile gestire le immagini personalizzate in un unico account di archiviazione per ogni area di Azure e usarle per creare centinaia di macchine virtuali nella stessa sottoscrizione. Per altre informazioni sui dischi gestiti, vedere [Azure Managed Disks overview](../linux/managed-disks-overview.md) (Panoramica di Azure Managed Disks).

## <a name="azure-virtual-machines--instances"></a>Macchine virtuali di Azure e istanze
Microsoft Azure supporta l'esecuzione di numerose distribuzioni comuni di Linux fornite e gestite da diversi partner.  In Azure Marketplace sono disponibili distribuzioni come Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD e altre ancora. Microsoft collabora attivamente con diverse community Linux per aggiungere altre versioni all'elenco delle [distribuzioni Linux approvate per Azure](endorsed-distros.md).

Se la distribuzione di Linux preferita non è attualmente presente nella raccolta, è possibile usare una VM "Bring your own Linux" [creando e caricando un VHD Linux in Azure](create-upload-generic.md).

Con Macchine virtuali di Azure è possibile distribuire in modo flessibile un'ampia gamma di soluzioni di elaborazione. È possibile distribuire praticamente qualsiasi carico di lavoro e in qualsiasi lingua in quasi tutti i sistemi operativi come Windows, Linux oppure crearne uno personalizzato da uno dei nostri partner in costante aumento. Se ancora non si trova ciò che si sta cercando,  è anche possibile usare immagini personalizzate dall'ambiente locale.

## <a name="vm-sizes"></a>Dimensioni delle VM
Le [dimensioni](sizes.md) della VM usata sono determinate dal carico di lavoro che si desidera eseguire. Le dimensioni scelte determinano quindi fattori quali potenza di elaborazione, memoria e capacità di archiviazione. Azure offre una vasta gamma di dimensioni per supportare molti tipi di uso.

Azure addebita un [costo orario](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) in base alla dimensione della VM e al sistema operativo. Per le ore parziali, Azure addebita il costo solo dei minuti usati. I costi di archiviazione vengono determinati e addebitati separatamente.

## <a name="automation"></a>Automazione
Per ottenere le impostazioni cultura DevOps corrette, l'intera infrastruttura deve essere contenuta nel codice.  Quando l'intera infrastruttura è presente nel codice, può essere facilmente ricreata (server Phoenix).  Azure funziona con gli strumenti di automazione principali, come Ansible, Chef, SaltStack e Puppet.  Azure dispone di strumenti di automazione propri:

* [Modelli di Azure](create-ssh-secured-vm-from-template.md)
* [Azure VMAccess](using-vmaccess-extension.md)

Azure sta implementando il supporto per [cloud-init](http://cloud-init.io/) nella maggior parte delle implementazioni Linux che lo supportano.  Attualmente le VM Ubuntu di Canonical vengono distribuite con cloud-init abilitato per impostazione predefinita.  RedHats RHEL, CentOS e Fedora supportano cloud-init, ma cloud-init attualmente non è installato nelle immagini di Azure gestite tramite RedHat.  Per usare cloud-init in un sistema operativo della famiglia RedHat, è necessario creare un'immagine personalizzata con cloud-init installato.

* [Uso di cloud-init nelle macchine virtuali Linux di Azure](using-cloud-init.md)

## <a name="quotas"></a>Quote
Ogni sottoscrizione di Azure ha limiti di quota predefiniti che possono influire sulla distribuzione di un numero elevato di VM per un progetto. Il limite corrente per ogni sottoscrizione è di 20 macchine virtuali per area.  È possibile aumentare i limiti di quota in modo semplice e rapido creando un ticket di supporto in cui si richiede appunto un aumento del limite.  Per altre informazioni sui limiti di quota:

* [Limiti del servizio di sottoscrizione di Azure](../../azure-subscription-service-limits.md)

## <a name="partners"></a>Partner
Microsoft collabora con i partner per verificare che le immagini disponibili vengano aggiornate e ottimizzate per un runtime Azure.  Per altre informazioni sui partner Azure, vedere i collegamenti seguenti:

* Linux in Azure - [Distribuzioni supportate](endorsed-distros.md)
* SUSE - [Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=%27SUSE%27)
* Redhat - [Azure Marketplace - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD - [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
* CoreOS - [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [Bitnami Library for Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Mesosphere DC/OS on Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker - [Azure Marketplace - Azure Container Service con Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)

## <a name="getting-started-with-linux-on-azure"></a>Introduzione a Linux in Azure
Per iniziare a usare Azure, è necessario un account Azure, aver installato l'interfaccia della riga di comando di Azure e disporre di una coppia di chiavi pubblica e privata SSH.

### <a name="sign-up-for-an-account"></a>Iscriversi per ottenere un account
Il primo passaggio quando si usa il cloud di Azure è effettuare l'iscrizione per un account Azure.  Per iniziare, accedere alla pagina relativa alla [creazione di un account Azure](https://azure.microsoft.com/pricing/free-trial/) .

### <a name="install-the-cli"></a>Installare l'interfaccia della riga di comando
Con il nuovo account di Azure, è possibile iniziare a usare immediatamente il portale di Azure, che è un pannello di amministrazione basato sul Web.  Per gestire il cloud di Azure tramite la riga di comando, installare `azure-cli`.  Installare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli) nella workstation Mac o Linux.

### <a name="create-an-ssh-key-pair"></a>Creare una coppia di chiavi SSH
A questo punto si dispone di un account Azure, del portale Web di Azure e dell'interfaccia della riga di comando di Azure.  Il passaggio successivo consiste nel creare una coppia di chiavi SSH per accedere tramite SSH in Linux senza specificare una password.  [Creare le chiavi SSH in Linux e Mac](mac-create-ssh-keys.md) per abilitare gli accessi senza password e migliorare la sicurezza.

### <a name="create-a-vm-using-the-cli"></a>Creare una macchina virtuale usando l'interfaccia della riga di comando
La creazione di una VM Linux usando l'interfaccia della riga di comando è un modo rapido per distribuire una VM senza uscire dal terminale in cui si sta operando.  Tutte le informazioni specificate nel portale Web sono disponibili tramite un flag o un'opzione della riga di comando.  

* [Creare una VM Linux usando l'interfaccia della riga di comando](quick-create-cli.md)

### <a name="create-a-vm-in-the-portal"></a>Creare una VM nel portale
La creazione di una VM Linux nel portale Web di Azure consente di selezionare facilmente le diverse opzioni per eseguire una distribuzione.  Anziché usare i flag o le opzioni della riga di comando, è possibile visualizzare un pratico layout Web con diverse opzioni e impostazioni.  Tutti gli elementi disponibili tramite l'interfaccia della riga di comando sono presenti anche nel portale.

* [Creare una VM Linux usando il portale](quick-create-portal.md)

### <a name="log-in-using-ssh-without-a-password"></a>Accedere tramite SSH senza una password
La VM è ora in esecuzione in Azure ed è possibile eseguire l'accesso.  L'uso di password per l'accesso tramite SSH non è sicuro e richiede tempo.  L'uso delle chiavi SSH è il modo più sicuro e rapido per eseguire l'accesso.  Quando si crea una VM Linux tramite il portale o l'interfaccia della riga di comando, sono disponibili due opzioni di autenticazione.  Se si sceglie una password per SSH, Azure configura la VM in modo da consentire gli accessi tramite password.  Se si sceglie di usare una chiave pubblica SSH, Azure configura la VM per consentire solo gli accessi tramite le chiavi SSH e disabilita gli accessi tramite password. Per proteggere la VM Linux consentendo solo gli accessi tramite le chiavi SSH, usare l'opzione di chiave pubblica SSH durante la creazione della VM nel portale o nell'interfaccia della riga di comando.

## <a name="related-azure-components"></a>Componenti di Azure correlati
## <a name="storage"></a>Archiviazione
* [Introduzione ad Archiviazione di Microsoft Azure](../../storage/common/storage-introduction.md)
* [Aggiungere un disco a una VM Linux tramite l'interfaccia della riga di comando di Azure](add-disk.md)
* [Come collegare un disco dati a una macchina virtuale Linux nel portale di Azure](attach-disk-portal.md)

## <a name="networking"></a>Rete
* [Panoramica di Rete virtuale.](../../virtual-network/virtual-networks-overview.md)
* [Indirizzi IP in Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Apertura di porte per una VM Linux in Azure](nsg-quickstart.md)
* [Creare un nome di dominio completo nel portale di Azure](portal-create-fqdn.md)

## <a name="containers"></a>Contenitori
* [Macchine virtuali e contenitori in Azure](containers.md)
* [Introduzione al servizio contenitore di Azure](../../container-service/container-service-intro.md)
* [Distribuire un cluster del servizio contenitore di Azure](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>Passaggi successivi
Quella descritta è panoramica di Linux in Azure.  Il passaggio successivo consiste nella creazione di alcune macchine virtuali.

* [Esplorare l'elenco crescente di script di esempio per l'esecuzione di attività comuni tramite l'interfaccia della riga di comando di Azure](cli-samples.md)
