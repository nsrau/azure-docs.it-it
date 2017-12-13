# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Usare strumenti di automazione dell'infrastruttura con macchine virtuali in Azure
Per creare e gestire macchine virtuali (VM) in modo coerente e scalabile, è in genere consigliabile una certa parte di automazione. Sono disponibili numerosi strumenti e soluzioni che consentono di automatizzare l'intero ciclo di vita di distribuzione e gestione dell'infrastruttura di Azure. Questo articolo presenta alcuni degli strumenti di automazione dell'infrastruttura che è possibile usare in Azure. Questi strumenti sono in genere associati a uno degli approcci seguenti:

- Automatizzare la configurazione delle macchine virtuali
    - Gli strumenti includono [Ansible](#ansible), [Chef](#chef) e [Puppet](#puppet).
    - Gli strumenti specifici per la personalizzazione delle macchine virtuali includono [cloud-init](#cloud-init) per le macchine virtuali Linux, [PowerShell DSC (Desired State Configuration)](#powershell-dsc)e l'[estensione di script personalizzata di Azure](#azure-custom-script-extension) per tutte le macchine virtuali di Azure.
 
- Automatizzare la gestione dell'infrastruttura
    - Tra gli strumenti sono inclusi [Packer](#packer), per automatizzare le compilazioni di immagini di macchina virtuale personalizzate, e [Terraform](#terraform), per automatizzare il processo di compilazione dell'infrastruttura.
    - [Automazione di Azure](#azure-automation) può eseguire azioni nell'infrastruttura di Azure e in quella locale.

- Automatizzare la distribuzione di applicazioni
    - Gli esempi includono [Visual Studio Team Services](#visual-studio-team-services) e [Jenkins](#jenkins).


## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) è un motore di automazione per la gestione della configurazione, la creazione di macchine virtuali e la distribuzione di applicazioni. Ansible usa un modello senza agente, in genere con chiavi SSH, per autenticare e gestire i computer di destinazione. Le attività di configurazione vengono definite nei runbook, con alcuni moduli Ansible disponibili per eseguire attività specifiche. Per altre informazioni, vedere [How Ansible works](https://www.ansible.com/how-ansible-works) (Funzionamento di Ansible).

È possibile passare agli argomenti seguenti:

- [Installare e configurare Ansible su Linux per l'uso in Azure](../articles/virtual-machines/linux/ansible-install-configure.md).
- [Creare una macchina virtuale di base](../articles/virtual-machines/linux/ansible-create-vm.md).
- [Creare un ambiente di macchine virtuali completo che include risorse di supporto](../articles/virtual-machines/linux/ansible-create-complete-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) è una piattaforma di automazione che permette di definire le modalità di configurazione, distribuzione e gestione dell'infrastruttura. I componenti aggiuntivi includono Chef Habitat per l'automazione del ciclo di vita dell'applicazione anziché dell'infrastruttura e Chef InSpec, che permette di automatizzare la conformità ai requisiti di sicurezza e dei criteri. I client Chef vengono installati nei computer di destinazione, con uno o più server Chef centrali che archiviano e gestiscono le configurazioni. Per altre informazioni, vedere [An Overview of Chef](https://docs.chef.io/chef_overview.html) (Panoramica di Chef).

È possibile passare agli argomenti seguenti:

- [Deploy Chef Automate from the Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview) (Distribuire Chef Automate da Azure Marketplace).
- [Installare Chef in Windows e creare macchine virtuali di Azure](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) è una piattaforma di automazione di livello aziendale che gestisce il processo di distribuzione delle applicazioni. Gli agenti vengono installati nei computer di destinazione per consentire a Puppet Master di eseguire i manifesti che definiscono la configurazione desiderata dell'infrastruttura e delle macchine virtuali di Azure. Puppet può integrarsi con altre soluzioni come Jenkins e GitHub per offrire un flusso di lavoro DevOps migliorato. Per altre informazioni, vedere [How Puppet works](https://puppet.com/product/how-puppet-works) (Funzionamento di Puppet).

È possibile passare agli argomenti seguenti:

- [Deploy Puppet from the Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2016-1?tab=Overview) (Distribuire Puppet da Azure Marketplace).


## <a name="cloud-init"></a>cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) è un approccio diffuso per personalizzare una macchina virtuale Linux al primo avvio. Cloud-init consente di installare pacchetti e scrivere file o configurare utenti e impostazioni di sicurezza. Poiché cloud-init viene chiamato durante il processo di avvio iniziale, non è necessario applicare passaggi aggiuntivi o agenti alla configurazione.  Per altre informazioni su come formattare correttamente i file `#cloud-config`, vedere il [sito della documentazione di cloud-init](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  I file `#cloud-config` sono file di testo codificati in formato base64.

Cloud-init funziona anche fra distribuzioni. Ad esempio, non si usa **apt-get install** o **yum install** per installare un pacchetto. In alternativa, è possibile definire un elenco di pacchetti da installare. Cloud-init userà automaticamente lo strumento di gestione del pacchetto nativo per la distribuzione selezionata.

 Microsoft sta collaborando attivamente con i partner di distribuzione Linux approvati per offrire immagini abilitate per cloud-init in Azure Marketplace. Queste immagini permetteranno il funzionamento uniforme di distribuzioni e configurazioni di cloud-init con macchine virtuali e set di scalabilità di macchine virtuali. La tabella seguente contiene le attuali immagini abilitate per cloud-init disponibili nella piattaforma Azure:

| Autore | Offerta | SKU | Versione | Pronta per cloud-init
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |16.04-LTS |più recenti |sì | 
|Canonical |UbuntuServer |14.04.5-LTS |più recenti |sì |
|CoreOS |CoreOS |Stabile |più recenti |sì |
|OpenLogic |CentOS |7-CI |più recenti |preview |
|RedHat |RHEL |7-RAW-CI |più recenti |preview |

Altre informazioni dettagliate su cloud-init in Azure:

- [Supporto di cloud-init per macchine virtuali Linux in Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Provare un'esercitazione sulla configurazione automatica della macchina virtuale tramite cloud init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell DSC (Desired State Configuration)](https://msdn.microsoft.com/en-us/powershell/dsc/overview) è una piattaforma di gestione che permette di definire la configurazione dei computer di destinazione. È possibile usare DSC anche in Linux tramite il [server Open Management Infrastructure (OMI)](https://collaboration.opengroup.org/omi/).

Le configurazioni DSC definiscono che cosa installare nel computer e come configurare l'host. Un motore di Gestione configurazione locale viene eseguito in ogni nodo di destinazione che elabora le azioni necessarie in base alle configurazioni di cui è stato eseguito il push. Un server di pull è un servizio Web che viene eseguito in un host centrale per archiviare le configurazioni DSC e le risorse associate. Il server di pull comunica con il motore di Gestione configurazione locale in ogni host di destinazione per fornire le configurazioni necessarie e i report sulla conformità.

È possibile passare agli argomenti seguenti:

- [Creare una configurazione DSC di base](https://msdn.microsoft.com/powershell/dsc/quickstart).
- [Configurazione di un server di pull DSC](https://msdn.microsoft.com/powershell/dsc/pullserver).
- [Usare DSC per Linux](https://msdn.microsoft.com/powershell/dsc/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Estensione di script personalizzata di Azure
L'estensione di script personalizzata per [Linux](../articles/virtual-machines/linux/extensions-customscript.md) o [Windows](../articles/virtual-machines/windows/extensions-customscript.md) scarica ed esegue script nelle macchine virtuali di Azure. È possibile usare l'estensione quando si crea una macchina virtuale o ogni volta che la macchina virtuale è in uso. 

Gli script possono essere scaricati da una risorsa di archiviazione di Azure o da qualsiasi posizione pubblica, ad esempio un repository GitHub. Con l'estensione di script personalizzata è possibile scrivere script in qualsiasi linguaggio in esecuzione nella macchina virtuale di origine. Questi script possono essere usati per installare applicazioni o configurare la macchina virtuale in base alle esigenze. Per proteggere le credenziali, le informazioni riservate come le password possono essere archiviate in una configurazione protetta. Queste credenziali vengono decrittografate solo all'interno della macchina virtuale.

È possibile passare agli argomenti seguenti:

- [Creare una VM Linux con l'interfaccia della riga di comando di Azure e usare l'estensione di script personalizzata](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Creare una VM Windows con Azure PowerShell e usare l'estensione di script personalizzata](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packer
[Packer](https://www.packer.io) automatizza il processo di compilazione quando si crea un'immagine di macchina virtuale personalizzata in Azure. È possibile usare Packer per definire il sistema operativo ed eseguire script di post-configurazione per personalizzare la macchina virtuale per esigenze specifiche. Una volta configurata, la macchina virtuale viene acquisita come immagine del disco gestito. Packer automatizza il processo per creare risorse di rete, archiviazione e macchina virtuale di origine, eseguire script di configurazione e quindi creare l'immagine di macchina virtuale.

È possibile passare agli argomenti seguenti:

- [Come usare Packer per creare immagini di macchine virtuali di Linux in Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Come usare Packer per creare immagini di macchine virtuali di Windows in Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) è uno strumento di automazione che permette di definire e creare un'intera infrastruttura di Azure con un unico linguaggio in formato basato su modelli, ovvero HCL (HashiCorp Configuration Language). Con Terraform è possibile definire i modelli per automatizzare il processo di creazione di risorse di rete, archiviazione e macchina virtuale per una soluzione di applicazione specifica. È possibile usare i modelli Terraform esistenti per altre piattaforme con Azure per garantire la coerenza e semplificare la distribuzione dell'infrastruttura senza che sia necessaria la conversione in modelli di Azure Resource Manager.

È possibile passare agli argomenti seguenti:

- [Installare e configurare Terraform con Azure](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Creare un'infrastruttura di Azure con Terraform](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Automazione di Azure
[Automazione di Azure](https://azure.microsoft.com/services/automation/) usa runbook per elaborare un set di attività nelle macchine virtuali come destinazione. Automazione di Azure viene usato per gestire le macchine virtuali esistenti anziché per creare un'infrastruttura. È possibile eseguire Automazione di Azure su macchine virtuali sia Linux sia Windows, nonché su macchine virtuali o fisiche locali con un ruolo di lavoro ibrido per runbook. I runbook possono essere archiviati in un repository di controllo del codice sorgente come GitHub. Questi runbook possono quindi essere eseguiti manualmente o in base a una pianificazione definita.

Automazione di Azure fornisce anche un servizio DSC (Desired State Configuration) che permette di creare le definizioni per la configurazione di un set specifico di macchine virtuali. DSC assicura quindi che la configurazione necessaria venga applicata e che la macchina virtuale resti coerente. Azure Automation DSC viene eseguito su computer Windows e Linux.

È possibile passare agli argomenti seguenti:

- [Creare un runbook di PowerShell](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Usare un ruolo di lavoro ibrido per runbook per gestire risorse locali](../articles/automation/automation-hybrid-runbook-worker.md).
- [Usare Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="visual-studio-team-services"></a>Visual Studio Team Services
[Team Services](https://www.visualstudio.com/team-services/) è una famiglia di strumenti che permettono di condividere e monitorare il codice, usare compilazioni automatiche e creare una pipeline di sviluppo e integrazione continua completa. Team Services si integra con Visual Studio e altri editor per semplificare l'utilizzo. Team Services può anche creare e configurare macchine virtuali di Azure e quindi distribuire codice in queste macchine virtuali.

È possibile passare agli argomenti seguenti:

- [Creare una pipeline di integrazione continua con Team Services](../articles/virtual-machines/windows/tutorial-vsts-iis-cicd.md).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) è un server di integrazione continua che permette di distribuire e testare le applicazioni e creare pipeline automatiche per il recapito del codice. Sono disponibili centinaia di plug-in per l'estensione della piattaforma Jenkins principale ed è anche possibile l'integrazione con molti altri prodotti e soluzioni tramite webhook. È possibile installare manualmente Jenkins in una macchina virtuale di Azure, eseguire Jenkins da un contenitore Docker oppure usare un'immagine di Azure Marketplace predefinita.

È possibile passare agli argomenti seguenti:

- [Come creare un'infrastruttura di sviluppo in una macchina virtuale Linux in Azure con Jenkins, GitHub e Docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Passaggi successivi
Sono disponibili molte opzioni diverse per usare gli strumenti di automazione dell'infrastruttura in Azure. È possibile usare la soluzione più adatta alle esigenze e all'ambiente con la massima libertà. Per iniziare a usare e provare alcuni degli strumenti integrati in Azure, vedere gli argomenti sull'automazione della personalizzazione di una macchina virtuale [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) o [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md).
