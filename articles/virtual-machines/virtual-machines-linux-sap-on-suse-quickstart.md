<properties
   pageTitle="Testare SAP NetWeaver nelle VM SUSE Linux di Microsoft Azure | Microsoft Azure"
   description="Test di SAP NetWeaver nelle VM SUSE Linux di Microsoft Azure"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="hermanndms"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="na"
   ms.date="05/30/2016"
   ms.author="hermannd"/>

# Esecuzione di SAP NetWeaver nelle VM SUSE Linux di Microsoft Azure


Questo articolo descrive vari aspetti da considerare quando si esegue SAP NetWeaver in macchine virtuali (VM) SUSE Linux di Microsoft Azure. A partire dal 19 maggio 2016 SAP NetWeaver è ufficialmente supportato nelle macchine virtuali SUSE Linux in Azure. Tutti i dettagli riguardanti le versioni di Linux, le versioni del kernel SAP e così via, sono reperibili nella nota 1928533 di SAP "SAP Applications on Azure: Supported Products and Azure VM types" (Applicazioni SAP in Azure: prodotti supportati e i tipi di VM di Azure). Altra documentazione su SAP nelle macchine virtuali Linux è disponibili qui: [Uso di SAP in macchine virtuali (VM) Linux](virtual-machines-linux-sap-get-started.md).


Le informazioni seguenti sono utili per evitare alcuni errori potenziali.

## Immagini SUSE in Azure per l'esecuzione di SAP

Per l'esecuzione di SAP NetWeaver in Azure, usare solo SUSE Linux Enterprise Server SLES 12 (SPx). Vedere anche la nota 1928533 di SAP. Una speciale immagine SUSE è disponibile in Azure Marketplace: "SLES 11 SP3 for SAP CAL", tuttavia non è da considerarsi per l'utilizzo generale. Non usare questa immagine, perché è riservata alla soluzione [SAP Cloud Appliance Library](https://cal.sap.com/).

È consigliabile usare Azure Resource Manager per tutti i nuovi test e installazioni da condurre in Azure. Per cercare immagini e versioni di SUSE SLES mediante Azure PowerShell o l'interfaccia della riga di comando di Azure, usare i comandi seguenti. L'output può quindi essere usato, ad esempio, per definire l'immagine del sistema operativo in un modello JSON per la distribuzione di una nuova VM SUSE Linux. I comandi di PowerShell seguenti sono validi per Azure PowerShell versione 1.0.1 e successive.

* Cercare i server di pubblicazione esistenti tra cui SUSE:

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* Cercare offerte esistenti da parte di SUSE:

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* Cercare offerte SUSE SLES:

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* Cercare una versione specifica di una SKU SLES:

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## Installazione di WALinuxAgent in una VM SUSE

L'agente denominato WALinuxAgent fa parte delle immagini SLES in Azure Marketplace. Per informazioni sull'installazione manuale, ad esempio durante il caricamento di un disco rigido virtuale (VHD) del sistema operativo SLES da locale, vedere:

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [Azzurro](virtual-machines-linux-endorsed-distros.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## "Enhanced monitoring" di SAP

Il "Enhanced monitoring" di SAP è un prerequisito obbligatorio per l'esecuzione di SAP in Azure. Consultare i dettagli nella nota 2191498 di SAP "SAP on Linux with Azure: Enhanced Monitoring" (SAP su Linux con Azure: Enhanced Monitoring).

## Collegamento di dischi di dati di Azure a una VM Linux di Azure

Non montare mai dischi dati di Azure in una VM Linux di Azure tramite l'ID dispositivo. Usare invece l'identificatore univoco universale (UUID). Prestare attenzione quando si usano, ad esempio, strumenti grafici per il montaggio di dischi dati di Azure. Eseguire una doppia verifica delle voci in /etc/fstab.

Il problema dell'ID dispositivo è che potrebbe cambiare e quindi la VM di Azure potrebbe bloccarsi nel processo di avvio. Per attenuare il problema, è possibile aggiungere il parametro nofail in /etc/fstab. È tuttavia necessario prestare attenzione a nofail, perché è possibile che le applicazioni usino il punto di montaggio come in precedenza, con il rischio di scrivere nel file system radice, nel caso in cui un disco dati di Azure esterno non venga montato durante l'avvio.

L'unica eccezione relativa al montaggio tramite UUID riguarda il collegamento di un disco del sistema operativo per la risoluzione dei problemi, come descritto nella sezione seguente.

## Risoluzione dei problemi di una VM SUSE non più accessibile

In alcune situazioni è possibile che una VM SUSE in Azure si blocchi durante il processo di avvio, ad esempio in caso di errori correlati al montaggio dei dischi. Si può verificare il problema usando la funzionalità di diagnostica di avvio per le macchine virtuali di Azure v2 nel portale di Azure. Per altre informazioni, vedere l'articolo relativo alla [diagnostica di avvio](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Un modo per risolvere il problema consiste nel collegare il disco del sistema operativo dalla VM danneggiata a un'altra VM SUSE in Azure, quindi apportare le modifiche appropriate, ad esempio modificando /etc/fstab o rimuovendo le regole udev di rete, come descritto nella sezione successiva.

Occorre fare una considerazione importante. La distribuzione di più VM SUSE dalla stessa immagine di Azure Marketplace, ad esempio SLES 11 SP4, causa il montaggio del disco del sistema operativo sempre con lo stesso UUID. L'uso dell'UUID per collegare un disco del sistema operativo da una VM diversa, distribuita con la stessa immagine di Azure Marketplace, genera di conseguenza due UUID identici. Questo causa problemi e può significare che la VM di cui è necessario risolvere i problemi venga in effetti avviata dal disco del sistema operativo collegato e danneggiato, invece che da quello originale.

Per evitare questo problema, è possibile procedere in due modi:

* Usare un'immagine di Azure Marketplace diversa per la risoluzione dei problemi della VM, ad esempio SLES 11 SPx invece di SLES 12.
* Non collegare il disco del sistema operativo danneggiato da un'altra VM tramite l'UUID, ma usare un altro metodo.

## Caricamento di una VM SUSE da locale ad Azure

Per una descrizione della procedura da seguire per caricare una VM SUSE da locale in Azure, vedere l'articolo [Preparare una macchina virtuale SLES o openSUSE per Azure](virtual-machines-linux-create-upload-vhd-suse.md).

Se si vuole caricare una VM senza il passaggio di deprovisioning finale, ad esempio per mantenere un'installazione SAP esistente e il nome host, verificare gli elementi seguenti:

* Assicurarsi che il disco del sistema operativo sia installato tramite UUID e non con l'ID dispositivo. Passare all'UUID soltanto in /etc/fstab non è sufficiente per il disco del sistema operativo. Non dimenticare di adattare anche il caricatore di avvio tramite YaST o modificando /boot/grub/menu.lst.
* Se si usa il formato VHDX per il disco del sistema operativo SUSE e questo viene convertito in VHD per il caricamento in Azure, è molto probabile che il dispositivo di rete cambi da eth0 a eth1. Per evitare problemi durante l'avvio in Azure in un secondo momento, ripristinare eth0 come descritto nell'articolo relativo alla [correzione di eth0 in SLES 11 clonati in VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Oltre a quanto descritto nell'articolo, è consigliabile rimuovere:

   /lib/udev/rules.d/75-persistent-net-generator.rules

È anche possibile installare l'agente Linux di Azure (waagent) per prevenire potenziali problemi, purché non siano presenti più schede di interfaccia di rete.

## Distribuzione di una VM SUSE in Azure

Creare le nuove VM SUSE tramite file modello JSON nel nuovo modello di Azure Resource Manager. Dopo aver creato il file modello JSON, è possibile distribuire la VM usando il comando dell'interfaccia della riga di comando seguente come alternativa a PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Per altre informazioni sui file modello JSON, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md) e [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).

Per altri dettagli relativi all'interfaccia della riga di comando e ad Azure Resource Manager, vedere [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Azure Resource Manager](xplat-cli-azure-resource-manager.md).

## Licenza SAP e chiave hardware

Per la certificazione SAP-Azure ufficiale è stato introdotto un nuovo meccanismo per il calcolo della chiave hardware SAP usata per la licenza SAP. A tale scopo, era necessario adattare il kernel SAP. Le versioni precedenti del kernel SAP per Linux non comprendono tale modifica del codice. Di conseguenza, in determinate situazioni, ad esempio nel ridimensionamento di VM di Azure, la chiave hardware SAP cambia e la relativa licenza potrebbe non essere più valida. Questo problema è stato risolto nell'ultima versione dei kernel Linux di SAP. Per i dettagli vedere la nota 1928533 di SAP.

## Pacchetto sapconf SUSE

SUSE offre un pacchetto denominato "sapconf" che gestisce un set di impostazioni specifiche di SAP. Per altre informazioni sulle funzionalità del pacchetto e su come installarlo e usarlo, vedere l'articolo relativo all'[uso di sapconf per preparare SUSE Linux Enterprise Server per l'esecuzione di sistemi SAP](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) e l'articolo di [informazioni su sapconf o come preparare un SUSE Linux Enterprise Server per l'esecuzione di sistemi SAP](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

## Condivisione NFS in installazioni SAP distribuite

Se è disponibile un'installazione distribuita in cui si vuole, ad esempio, installare il database e i server applicazioni SAP in VM separate, è possibile condividere la directory /sapmnt tramite il file system di rete (NFS). In caso di problemi con la procedura di installazione dopo la creazione della condivisione NFS per /sapmnt, verificare di avere impostato "no\_root\_squash" per tale condivisione.

## Volumi logici

In passato, se era necessario un volume logico di grandi dimensioni su più dischi dati di Azure, ad esempio per il database SAP, era consigliabile usare mdadm dal momento che lvm non era completamente convalidato su Azure. Per informazioni su come configurare RAID Linux in Azure tramite mdadm, vedere [Configurare RAID software in Linux](virtual-machines-linux-configure-raid.md). Nel frattempo, dall'inizio di maggio 2016, anche lvm è completamente supportato in Azure e può essere usato come alternativa a mdadm. Per altre informazioni su lvm in Azure vedere [Configurare LVM in una VM Linux in Azure](virtual-machines-linux-configure-lvm.md).


## Repository SUSE di Azure

In caso di problemi di accesso al repository SUSE standard di Azure, è disponibile un semplice comando per reimpostarlo. Questo problema può verificarsi se si crea un'immagine privata del sistema operativo in un'area di Azure e quindi si copia l'immagine in un'altra area in cui si vogliono distribuire nuove VM basate su tale immagine. È sufficiente eseguire il comando seguente nella VM:

   ```
   service guestregister restart
   ```

## Gnome Desktop

Se si vuole usare l'ambiente desktop Gnome per l'installazione di un sistema dimostrativo SAP completo all'interno di una singola VM, inclusi GUI SAP, browser e console di gestione di SAP, usare questo suggerimento per l'installazione nelle immagini SLES di Azure:

   Per SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   Per SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## Supporto SAP per Oracle su Linux nel cloud

Non esiste alcuna limitazione di supporto da parte di Oracle su Linux in ambienti virtualizzati. Anche se non è un argomento specifico di Azure, è importante conoscerlo. SAP non supporta Oracle su SUSE o Red Hat in un cloud pubblico come Azure. Per discutere di questo argomento, contattare Oracle direttamente.

<!---HONumber=AcomDC_0601_2016-->