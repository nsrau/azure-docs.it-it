<properties
   pageTitle="Test di SAP NetWeaver nelle VM SUSE Linux di Microsoft Azure | Microsoft Azure"
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
   ms.date="11/26/2015"
   ms.author="hermannd"/>

# Test di SAP NetWeaver nelle VM SUSE Linux di Microsoft Azure


Di seguito è indicato un elenco di elementi da tenere in considerazione durante il test di SAP NetWeaver nelle VM SUSE Linux di Microsoft Azure. Al momento non sono disponibili dichiarazioni ufficiali di supporto da parte di SAP riguardo all'uso coordinato di SAP, Linux e Azure. Tuttavia, i clienti possono eseguire alcuni test, demo o creazione di prototipi, purché tali attività non siano dipendenti dal supporto ufficiale di SAP.

Le informazioni seguenti sono utili per evitare alcuni errori potenziali.



## Immagini SUSE in Microsoft Azure per il test di SAP

Per il test di SAP in Azure, usare solo SUSE Linux Enterprise Server (SLES) 11 SP4 e SLES 12. Una speciale immagine SUSE è disponibile in Azure Marketplace: "SLES 11 SP3 for SAP CAL", tuttavia non è da considerarsi per l'utilizzo generale. È riservata alla soluzione [SAP Cloud Appliance Library](https://cal.sap.com/). Non è stato possibile nascondere questa immagine al pubblico. Pertanto, non deve essere utilizzata.

È consigliabile usare Gestione risorse di Azure per tutti i nuovi test da condurre in Azure. Per cercare immagini e versioni di SUSE SLES mediante Azure PowerShell o l'interfaccia della riga di comando di Azure, usare i comandi seguenti. L'output può quindi essere usato, ad esempio, per definire l'immagine del sistema operativo in un modello JSON per la distribuzione di una nuova VM SUSE Linux. I comandi di PowerShell seguenti sono validi per Azure PowerShell versione 1.0.1 o successiva.

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

L'agente denominato WALinuxAgent fa parte delle immagini SLES in Azure Marketplace. Di seguito sono indicati indirizzi in cui è possibile trovare informazioni sull'installazione manuale (ad esempio, durante il caricamento di un VHD del sistema operativo SLES da locale):

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [Azzurro](virtual-machines-linux-endorsed-distributions.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## Collegamento di dischi di dati di Azure a una VM Linux di Azure

Non eseguire mai un montaggio di dischi dati di Azure in una VM Linux di Azure tramite ID dispositivo. Usare invece l'UUID. Prestare attenzione quando si usano, ad esempio, gli strumenti grafici per il montaggio dei dischi dati di Azure. Eseguire una doppia verifica delle voci in /etc/fstab.

Il problema dell'ID dispositivo è che potrebbe cambiare e quindi la VM di Azure potrebbe bloccarsi nel processo di avvio. È possibile aggiungere il parametro nofail in /etc/fstab per attenuare il problema. Tuttavia, è necessario prestare attenzione al fatto che nofail potrebbe far sì che le applicazioni usino il punto di montaggio come in precedenza, con il rischio di scrittura nel file system root, nel caso in cui non venga montato un disco dati di Azure esterno durante l'avvio.

## Caricamento di una VM SUSE da locale ad Azure

[Questo articolo](virtual-machines-linux-create-upload-vhd-suse.md) descrive i passaggi per il caricamento di una VM SUSE da locale ad Azure.

Se si vuole caricare una VM senza il passaggio di deprovisioning finale, ad esempio per mantenere un'installazione SAP esistente e il nome host, controllare gli elementi seguenti:

* Verificare che il disco del sistema operativo sia installato tramite UUID e non tramite ID dispositivo. Passare all'UUID soltanto in /etc/fstab non è sufficiente per il disco del sistema operativo. Non dimenticare di adattare anche il caricatore di avvio tramite YaST o modificando /boot/grub/menu.lst
* Se è stato usato il formato VHDX per il disco del sistema operativo SUSE e questo viene convertito in VHD per il caricamento in Azure, è molto probabile che il dispositivo di rete sia cambiato da eth0 a eth1. Per evitare problemi durante l'avvio in Azure in un secondo momento, ripristinare eth0 come descritto in [questo articolo](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Oltre a quanto descritto nell'articolo, è consigliabile rimuovere:

   /lib/udev/rules.d/75-persistent-net-generator.rules

Anche l'installazione dell'agente Linux di Azure (waagent) aiuta a prevenire potenziali problemi, purché non siano presenti più schede di interfaccia di rete.

## Distribuzione di una VM SUSE in Azure

Le nuove VM SUSE vengono create tramite file di modello JSON nel nuovo modello di Gestione risorse di Azure. Dopo aver creato il file di modello JSON, distribuire la VM usando il comando dell'interfaccia della riga di comando seguente come alternativa a PowerShell:

   ``` crea gruppo di distribuzione azure "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ``` Altre informazioni sui file di modello JSON sono disponibili [in questo articolo](resource-group-authoring-templates.md) e [in questa pagina Web](https://azure.microsoft.com/documentation/templates/).

Altre informazioni sull'interfaccia della riga di comando e su Gestione risorse di Azure sono disponibili in [questo articolo](xplat-cli-azure-resource-manager.md).

## Licenza SAP e chiave hardware

Per la certificazione SAP-Windows-Azure ufficiale è stato introdotto un nuovo meccanismo per il calcolo della chiave hardware SAP usata per la licenza SAP. A tale scopo, era necessario adattare il kernel SAP. Le attuali versioni del kernel SAP per Linux non comprendono tale modifica del codice. Di conseguenza, in determinate situazioni (ad esempio, nel ridimensionamento di VM di Azure) la chiave hardware SAP potrebbe cambiare e la relativa licenza potrebbe non essere più valida.

## Pacchetto sapconf SUSE

SUSE offre un pacchetto denominato "sapconf" che si occupa di un set di impostazioni specifiche di SAP. Altre informazioni su questo pacchetto, sulle sue funzionalità e su come installarlo e usarlo sono reperibili in [questo post del blog SUSE](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) e [questo post del blog SAP](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

## Condivisione NFS in installazioni SAP distribuite

Nel caso di un'installazione distribuita in cui si vuole installare, ad esempio, il database e i server delle applicazioni SAP in VM separate, la directory /sapmnt può essere condivisa tramite NFS. In caso di problemi con la procedura di installazione dopo la creazione della condivisione NFS per /sapmnt, verificare che sia stato impostato "no\_root\_squash" per tale condivisione. Questa soluzione è stata applicata in un test case interno.


## Volumi logici

La gestione di volumi logici non è completamente convalidata in Azure. Se è necessario un volume logico di grandi dimensioni in più dischi dati di Azure (ad esempio, per il database SAP), è consigliabile usare mdadm. [Questo articolo](virtual-machines-linux-configure-raid.md) descrive la procedura di configurazione del RAID Linux in Azure tramite mdadm.


## Repository SUSE di Azure

In caso di problemi di accesso al repository SUSE standard di Azure, è disponibile un semplice comando per reimpostarlo. Questo problema può verificarsi quando si crea un'immagine del sistema operativo privata in un'area di Azure e quindi si copia l'immagine in un'altra area in cui si vuole distribuire nuove VM basate su tale immagine. È sufficiente eseguire il comando seguente nella VM:

   ```
   service guestregister restart
   ```

## Gnome Desktop

Se si vuole usare l'ambiente desktop Gnome per l'installazione di un sistema demo di SAP completo all'interno di una singola VM contenente GUI SAP, browser, console di gestione di SAP e così via, ecco un piccolo suggerimento per l'installazione nelle immagini SLES di Azure:

   SLES 11

   ```
   zypper in -t pattern gnome
   ```

   SLES 12

   ```
   zypper in -t pattern gnome-basic
   ```

## Supporto SAP-Oracle su Linux nel cloud

Non esiste alcuna limitazione di supporto da parte di Oracle su Linux in ambienti virtualizzati. Si tratta di un argomento generale, non specifico di Azure. Tuttavia, è importante comprenderlo. SAP non supporterà Oracle su SUSE o RedHat in un cloud pubblico come Azure. I clienti devono contattare Oracle direttamente per discutere di questo argomento.

<!---HONumber=AcomDC_0114_2016-->