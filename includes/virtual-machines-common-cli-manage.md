* Se l'interfaccia della riga di comando di Azure non è ancora stata installata e connessa alla sottoscrizione, vedere [Installare l'interfaccia della riga di comando di Azure](../articles/xplat-cli-install.md) e [Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure](../articles/xplat-cli-connect.md). Se si vogliono usare i comandi della modalità Resource Manager, assicurarsi di connettersi con il comando `azure login`.

* Per iniziare con la modalità di Gestione risorse nell’interfaccia della riga di comando Azure, potrebbe essere necessario cambiare la modalità di comando. Per impostazione predefinita, l'interfaccia della riga di comando viene avviata in modalità Gestione dei servizi. Per passare alla modalità Gestione risorse, eseguire `azure config mode arm`. Per tornare alla modalità Gestione dei servizi, eseguire `azure config mode asm`.

* Per la Guida in linea di comando e le opzioni, digitare `azure <command> <subcommand> --help` o `azure help <command> <subcommand>`.

## Attività della macchina virtuale
La tabella seguente confronta attività di VM comuni che è possibile eseguire con i comandi dell'interfaccia della riga di comando di Azure in modalità Gestione dei servizi e in modalità Resource Manager. Con molti comandi di Gestione risorse è necessario passare il nome di un gruppo di risorse esistente.

> [AZURE.NOTE] Questi esempi non includono operazioni basate su modelli che sono in genere consigliate per distribuzioni di macchine virtuali in Gestione risorse. Per informazioni, vedere [Usare l’interfaccia della riga di comando Azure con la Gestione risorse di Azure](../articles/xplat-cli-azure-resource-manager.md) e [Distribuire e gestire le macchine virtuali usando modelli di Gestione risorse di Azure e l’interfaccia della riga di comando di Azure](../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md).

Attività | Modalità Gestione dei servizi | Modalità di Gestione risorse
-------------- | ----------- | -------------------------
Creare la macchina virtuale di base | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Ottenere `image-urn` dal comando `azure vm image list`. Vedere [questo articolo](../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md) per informazioni dettagliate).
Creare una macchina virtuale Linux | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Creare un'app Windows | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
Elenco delle macchine virtuali | `azure  vm list [options]` | `azure  vm list [options]`
Visualizzare informazioni su una macchina virtuale | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
Avviare una macchina virtuale | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
Arrestare una macchina virtuale | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
Deallocare una macchina virtuale | Non disponibile | `azure vm deallocate [options] <resource-group> <name>`
Riavviare una macchina virtuale | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
Eliminare una macchina virtuale | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
Acquisire una macchina virtuale | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
Creare una macchina virtuale da un'immagine dell'utente | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
Creare una macchina virtuale da un disco specializzato | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azure  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
Aggiungere un disco dati a una macchina virtuale | `azure  vm disk attach [options] <vm-name> <disk-image-name>` -O- <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
Rimuovere un disco dati da una macchina virtuale | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
Aggiungere un'estensione generica a una macchina virtuale | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
Aggiungere l'estensione accesso alle macchine Virtuali a una macchina virtuale | Non disponibile | `azure vm reset-access [options] <resource-group> <name>`
Aggiungere Docker estensione a una macchina virtuale | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
Aggiungere l'estensione Chef a una macchina virtuale | `azure  vm extension get-chef [options] <vm-name>` | Non disponibile
Disabilitare un'estensione di macchina virtuale | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | Non disponibile
Rimuovere un'estensione di macchina virtuale | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
Elencare le estensioni di macchina virtuale | `azure vm extension list [options]` | Non disponibile
Visualizzare un'immagine di macchina virtuale | `azure vm image show [options]` | Non disponibile
Ottenere l'utilizzo delle risorse della macchina virtuale | Non disponibile | `azure vm list-usage [options] <location>`
Ottenere tutte le dimensioni delle macchine virtuali disponibili | Non disponibile | `azure vm sizes [options]`


## Passaggi successivi

* Per altri esempi dell'interfaccia della riga di comando, vedere [Comandi dell'interfaccia della riga di comando di Azure in modalità Gestione dei servizi di Azure](../articles/virtual-machines-command-line-tools.md) e [Comandi dell'interfaccia della riga di comando di Azure in modalità Azure Resource Manager](../articles/virtual-machines/azure-cli-arm-commands.md).

<!---HONumber=AcomDC_0420_2016-->