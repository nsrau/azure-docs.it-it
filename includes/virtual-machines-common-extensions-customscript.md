

Fin dalla sua introduzione, l'estensione script personalizzato è stata ampiamente usata per configurare i carichi di lavoro nelle macchine virtuali Windows e Linux. Con l'introduzione dei modelli di Gestione risorse di Azure, gli utenti possono ora creare un singolo modello che non solo esegue il provisioning della macchina virtuale ma ne configura anche i carichi di lavoro.

## <a name="about-azure-resource-manager-templates"></a>Informazioni sui modelli di Azure Resource Manager
I modelli di Azure Resource Manager consentono di specificare in modo dichiarativo l'infrastruttura IaaS di Azure in linguaggio JSON, definendo le dipendenze tra risorse. Per una panoramica dettagliata dei modelli di Gestione risorse di Azure, consultare gli articoli seguenti:

* [Panoramica del gruppo di risorse](../articles/azure-resource-manager/resource-group-overview.md)
* [Distribuzione di modelli con Azure PowerShell](../articles/virtual-machines/windows/ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="prerequisites"></a>Prerequisiti
1. Scaricare gli strumenti della riga di comando di Azure per il sistema operativo in uso da [qui](https://azure.microsoft.com/downloads/).
2. Se gli script vengono eseguiti in una macchina virtuale esistente, verificare che l'agente VM sia abilitato nella macchina virtuale. In caso contrario, vedere le indicazioni per [Linux](../articles/virtual-machines/linux/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) o [Windows](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) per installarne uno.
3. Caricare gli script da eseguire sulla macchina virtuale in Archiviazione di Azure. Gli script possono provenire da uno o più contenitori di archiviazione.
4. In alternativa è possibile caricare gli script in un account GitHub.
5. Lo script deve essere creato in modo tale che lo script di ingresso che viene avviato dall'estensione a turno avvii altri script.

## <a name="using-the-custom-script-extension"></a>Uso dell'estensione Script personalizzato
Per la distribuzione con i modelli si usa la stessa versione dell'estensione di script personalizzato disponibile per le API di Gestione dei servizi di Azure. L'estensione supporta gli stessi parametri e scenari, come il caricamento dei file nell'account di archiviazione di Azure o nel percorso GitHub. La differenza principale quando si utilizzano i modelli consiste nella versione esatta dell'estensione che deve essere specificata anziché specificare la versione nel formato majorversion.*.

