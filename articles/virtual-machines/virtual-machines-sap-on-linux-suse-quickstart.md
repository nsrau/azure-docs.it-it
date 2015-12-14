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


Di seguito è indicato un elenco di elementi da considerare durante il test di SAP NetWeaver nelle VM SUSE Linux di Microsoft Azure. Al momento non sono presenti dichiarazioni ufficiali di supporto da parte di SAP circa l'utilizzo coordinato di SAP, Linux e Azure. Tuttavia i clienti possono eseguire alcuni test, demo o la creazione di prototipi, purché non tali attività siano dipendenti dal supporto ufficiale di SAP.

L'elenco seguente può aiutare a evitare alcuni potenziali ostacoli e semplificare le operazioni:



## Immagini SUSE in Microsoft Azure per il test di SAP 

Utilizzare solo SLES 11SP4 e SLES 12 per il test di SAP su Azure. Un'immagine SUSE speciale è reperibile nella raccolta immagini di Azure: "SLES 11 SP3 for SAP CAL", tuttavia non è da considerarsi per uso generale. Tale immagine è riservata per la soluzione SAP Cloud Appliance Library denominata SAP "CAL" ( <https://cal.sap.com/> ). Non era possibile nascondere questa immagine al pubblico. Pertanto, non deve essere utilizzata.

Tutti i nuovi test in Azure devono essere condotti con Gestione risorse di Azure. Per cercare immagini e versioni di SUSE SLES mediante Azure Powershell o l'interfaccia della riga di comando, utilizzare i comandi seguenti. L'output può quindi essere utilizzato, ad esempio, per definire l'immagine del sistema operativo in un modello json per la distribuzione di una nuova VM SUSE Linux:

* cercare i server di pubblicazione esistenti tra cui SUSE:

   ```
   PS  : Get-AzureVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* cercare offerte esistenti da parte di SUSE:
      
   ```
   PS  : Get-AzureVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
      
* cercare offerte SUSE SLES:
      
   ```
   PS  : Get-AzureVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```
      
* cercare una versione specifica di una sku SLES:
      
   ```
   PS  : Get-AzureVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```
     
## Installazione di WALinuxAgent in una VM SUSE 
 
L'agente fa parte delle immagini SLES nella raccolta di Azure. Di seguito sono indicati indirizzi in cui è possibile reperire informazioni sull'installazione manuale (ad esempio, durante il caricamento di un VHD del sistema operativo SLES da locale):

<http://software.opensuse.org/package/WALinuxAgent>

<https://azure.microsoft.com/documentation/articles/virtual-machines-linux-endorsed-distributions/>

<https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/>

## Collegamento di dischi di dati di Azure a una VM Linux di Azure

Non eseguire MAI montaggio di dischi di dati di Azure in una VM Linux di Azure tramite ID dispositivo. Utilizzare invece UUID. Prestare attenzione quando si utilizzano, ad esempio, gli strumenti grafici per il montaggio dei dischi di dati di Azure. Eseguire una doppia verifica delle voci in /etc/fstab. Il problema dell'ID dispositivo è che potrebbe cambiare, pertanto la VM di Azure potrebbe bloccarsi nel processo di avvio. Si potrebbe aggiungere il parametro nofail in /etc/fstab per attenuare il problema. Tuttavia, è necessario prestare attenzione al fatto che nofail potrebbe far sì che le applicazioni utilizzino il punto di montaggio come in precedenza, con il rischio di scrittura nel file system root, nel caso in cui non venga montato un disco di dati di Azure esterno durante l'avvio.

## Caricamento di una VM SUSE da locale ad Azure

Nel blog seguente vengono descritti i passaggi: <https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-suse/>

Per caricare una VM senza il passaggio di deprovisioning finale, ad esempio per mantenere un'installazione SAP esistente e il nome host, è necessario controllare gli elementi seguenti:

* verificare che il disco del sistema operativo sia installato tramite UUID e non tramite l'ID dispositivo. Limitarsi a cambiare l'UUID in /etc/fstab NON è sufficiente per il disco del sistema operativo. È opportuno non dimenticare di adattare anche il caricatore di avvio, ad esempio tramite yast o modificando /boot/grub/menu.lst
* se è stato utilizzato il formato vhdx per il disco del sistema operativo SUSE e questo viene convertito in VHD per il caricamento in Azure, è molto probabile che il dispositivo di rete sia cambiato da eth0 a eth1. Per evitare problemi durante l'avvio in Azure in un secondo momento, è necessario ripristinare eth0 come descritto di seguito: <https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/>

Oltre a quanto descritto nell'articolo si consiglia di rimuovere anche

   /lib/udev/rules.d/75-persistent-net-generator.rules

Inoltre, l'installazione di waagent dovrebbe evitare qualsiasi potenziale problema finché non sono presenti più schede di interfaccia di rete.

## Licenza SAP e chiave hardware

Per la certificazione SAP-Windows-Azure ufficiale è stato introdotto un nuovo meccanismo per il calcolo della chiave hardware SAP utilizzata per la licenza SAP. A tale scopo, era necessario adattare il kernel SAP. Le attuali versioni del kernel SAP per Linux NON comprendono tale modifica del codice. Pertanto, può verificarsi che, in determinate situazioni (ad esempio, nel ridimensionamento di VM di Azure), la chiave hardware SAP cambi e la relativa licenza non sia più valida

## Condivisione NFS in installazioni SAP distribuite

Nel caso di un'installazione distribuita in cui si intenda installare, ad esempio, il database e i server delle applicazioni SAP in VM separate, la directory /sapmnt tramite NFS può essere condivisa tramite NFS. In caso di problemi con la procedura di installazione dopo la creazione della condivisione NFS per /sapmnt, verificare che sia stato impostato "no\_root\_squash" per tale condivisione. Questa soluzione è stata individuata in un test case interno


## Volumi logici

LVM non è completamente convalidata in Azure. Se è necessario un volume logico di grandi dimensioni su più dischi di dati di Azure (ad esempio, per il database SAP), è necessario utilizzare mdadm. Ecco un interessante blog che descrive la procedura di configurazione del RAID Linux in Azure tramite mdadm:

<https://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-raid/>


## Repository Azure SUSE

In caso di problemi di accesso al repository Azure SUSE standard, è disponibile un semplice comando per reimpostarlo. Questo problema può verificarsi quando si crea un'immagine del sistema operativo privata in un'area di Azure e quindi si copia l'immagine in un'altra area in cui distribuire nuove VM basate su di essa. È sufficiente eseguire il comando seguente nella VM:

   ```
   service guestregister restart
   ```

## Gnome Desktop

Per utilizzare Gnome Desktop per l'installazione di un sistema demo di SAP completo all'interno di una singola VM contenente GUI SAP, browser, console di gestione di SAP e così via, ecco un piccolo suggerimento per l'installazione sulle immagini SLES di Azure:

   SLES 11

   ```
   zypper in -t pattern gnome
   ```
      
   SLES 12
   
   ```
   zypper in -t pattern gnome-basic
   ```

## Supporto SAP-Oracle su Linux nel cloud
 
In realtà si tratta di un argomento generale, non specifico di Azure. Tuttavia, è molto importante da comprendere. Non esiste alcuna limitazione di supporto da parte di Oracle su Linux in ambienti virtualizzati. Di conseguenza, SAP non supporterà Oracle su SUSE o RedHat in un cloud pubblico come Azure. I clienti devono contattare Oracle direttamente per discutere di questo argomento.

<!---HONumber=AcomDC_1203_2015-->