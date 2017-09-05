Per usare l'interfaccia della riga di comando di Azure con i comandi e i modelli di Resource Manager per distribuire risorse e carichi di lavoro di Azure tramite gruppi di risorse, è necessario un account Azure. Se non si dispone di un account, è possibile ottenere un [Azure qui versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

Se non è già stata installata l'interfaccia della riga di comando di Azure e non è stata stabilita la connessione alla sottoscrizione, vedere [Installare l'interfaccia della riga di comando di Azure](../articles/cli-install-nodejs.md), impostare la modalità su `arm` con `azure config mode arm`, quindi connettersi ad Azure con il comando `azure login`.

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- Interfaccia della riga di comando di Azure 1.0: interfaccia della riga di comando per i modelli di distribuzione classica e Resource Manager (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](../articles/virtual-machines/linux/cli-manage.md): interfaccia della riga di comando di prossima generazione per il modello di distribuzione di Gestione risorsa

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Comandi di base di Azure Resource Manager nell'interfaccia della riga di comando di Azure
Questo articolo illustra i comandi di base da usare con l'interfaccia della riga di comando di Azure per gestire le risorse, principalmente macchine virtuali, e interagire con esse nella sottoscrizione di Azure.  Per una guida più dettagliata con parametri e opzioni della riga di comando specifici, è possibile usare la Guida in linea dei comandi e le relative opzioni digitando `azure <command> <subcommand> --help` o `azure help <command> <subcommand>`.

> [!NOTE]
> Questi esempi non includono operazioni basate su modelli che sono in genere consigliate per distribuzioni di macchine virtuali in Gestione risorse. Per informazioni, vedere come [usare l'interfaccia della riga di comando di Azure con Azure Resource Manager](../articles/xplat-cli-azure-resource-manager.md) e [Distribuire e gestire le macchine virtuali usando modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../articles/virtual-machines/linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

| Attività | Gestione risorse |
| --- | --- | --- |
| Creare la macchina virtuale di base |`azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>Ottenere `image-urn` dal comando `azure vm image list`. Vedere [questo articolo](../articles/virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per esempi. |
| Creare una macchina virtuale Linux |`azure  vm create [options] <resource-group> <name> <location> -y "Linux"` |
| Creare un'app Windows |`azure  vm create [options] <resource-group> <name> <location> -y "Windows"` |
| Elenco delle macchine virtuali |`azure  vm list [options]` |
| Visualizzare informazioni su una macchina virtuale |`azure  vm show [options] <resource_group> <name>` |
| Avviare una macchina virtuale |`azure vm start [options] <resource_group> <name>` |
| Arrestare una macchina virtuale |`azure vm stop [options] <resource_group> <name>` |
| Deallocare una macchina virtuale |`azure vm deallocate [options] <resource-group> <name>` |
| Riavviare una macchina virtuale |`azure vm restart [options] <resource_group> <name>` |
| Eliminare una macchina virtuale |`azure vm delete [options] <resource_group> <name>` |
| Acquisire una macchina virtuale |`azure vm capture [options] <resource_group> <name>` |
| Creare una macchina virtuale da un'immagine dell'utente |`azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>` |
| Creare una macchina virtuale da un disco specializzato |`azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>` |
| Aggiungere un disco dati a una macchina virtuale |`azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]` |
| Rimuovere un disco dati da una macchina virtuale |`azure  vm disk detach [options] <resource-group> <vm-name> <lun>` |
| Aggiungere un'estensione generica a una macchina virtuale |`azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Aggiungere l'estensione accesso alle macchine Virtuali a una macchina virtuale |`azure vm reset-access [options] <resource-group> <name>` |
| Aggiungere Docker estensione a una macchina virtuale |`azure  vm docker create [options] <resource-group> <name> <location> <os-type>` |
| Rimuovere un'estensione di macchina virtuale |`azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Ottenere l'utilizzo delle risorse della macchina virtuale |`azure vm list-usage [options] <location>` |
| Ottenere tutte le dimensioni delle macchine virtuali disponibili |`azure vm sizes [options]` |

## <a name="next-steps"></a>Passaggi successivi
* Per altri esempi relativi a comandi dell'interfaccia della riga di comando corrispondenti a funzioni più avanzate di gestione delle macchine virtuali, vedere [Comandi dell'interfaccia della riga di comando di Azure in modalità Azure Resource Manager](../articles/virtual-machines/azure-cli-arm-commands.md).
