<properties
pageTitle="Preparare una macchina virtuale Oracle Linux per Azure | Microsoft Azure"
description="Configurazione dettagliata di una macchina virtuale di Oracle che eseguono Linux in Microsoft Azure."
services="virtual-machines-linux"
authors="bbenz"
manager="timlt"
documentationCenter="virtual-machines"
tags="azure-service-management,azure-resource-manager"
/>

<tags
ms.service="virtual-machines-linux"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-linux"
ms.workload="infrastructure-services"
ms.date="06/22/2015"
ms.author="bbenz" />

# Preparare una macchina virtuale Oracle Linux per Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


-   [Preparare una macchina virtuale Oracle Linux 6.4+ per Azure](virtual-machines-linux-oracle-create-upload-vhd.md)

-   [Preparare una macchina virtuale Oracle Linux 7.0+ per Azure](virtual-machines-linux-oracle-create-upload-vhd.md)

## Prerequisiti
In questo articolo si presuppone che l'utente abbia già installato un sistema operativo Oracle Linux in un disco rigido virtuale. Sono disponibili vari strumenti per creare file con estensione vhd, ad esempio una soluzione di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

**Note generali sull'installazione di Oracle Linux**

- Il kernel compatibile con Red Hat di Oracle e i relativi UEK3 (Unbreakable Enterprise Kernel) sono supportati sia su Hyper-V sia su Azure. Per ottenere i migliori risultati, assicurarsi di eseguire l'aggiornamento al kernel più recente durante la preparazione del VHD Oracle Linux.

- UEK2 di Oracle non è supportato su Hyper-V e Azure perché non include i driver necessari.

- Il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd.

- Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, spesso predefinite per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le macchine virtuali clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra macchina virtuale per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare LVM o [RAID](virtual-machines-linux-configure-raid.md).

- NUMA non è supportato per macchine virtuali di dimensioni maggiori a causa di un bug presente nelle versioni del kernel di Linux inferiori a 2.6.37. Questo problema incide principalmente sulle distribuzioni che usano il kernel upstream Red Hat 2.6.32. L'installazione manuale dell'agente Linux di Azure (waagent) disabiliterà automaticamente NUMA nella configurazione GRUB per il kernel Linux. Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.

- Non configurare una partizione swap nel disco del sistema operativo. L'agente Linux può essere configurato in modo da creare un file swap sul disco temporaneo delle risorse. Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.

- Tutti i dischi rigidi virtuali devono avere dimensioni multiple di 1 MB.

- Verificare che il repository `Addons` sia abilitato. Scegliere di modificare il file `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) o `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux) e la riga `enabled=0` in `enabled=1` in **[ol6\_addons]** o **[ol7\_addons]** in questo file.


## Oracle Linux 6.4+
Per l'esecuzione della macchina virtuale in Azure è necessario eseguire specifici passaggi di configurazione nel sistema operativo.

1. Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.

2. Fare clic su **Connect** per aprire la finestra della macchina virtuale.

3. Disinstallare NetworkManager attivando il seguente comando:

		# sudo rpm -e --nodeps NetworkManager

	>[AZURE.NOTE] se il pacchetto non è già installato, questo comando avrà esito negativo e verrà visualizzato un messaggio di errore. Si tratta di un comportamento previsto.

4. Creare nella directory /etc/sysconfig/ un file denominato **network** contenente il testo seguente:

	`NETWORKING=yes` `HOSTNAME=localhost.localdomain`

5.  Creare nella directory /etc/sysconfig/network-scripts/ un file denominato **ifcfg-eth0** contenente il testo seguente:

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
			TYPE=Ethernet
			USERCTL=no
			PEERDNS=yes
		IPV6INIT=no

6.  Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Queste regole provocano problemi quando si clona una macchina virtuale in Azure o Hyper-V:

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

7.  Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:

		# chkconfig network on

8.  Installare python-pyasn1 eseguendo il comando seguente:

		# sudo yum install python-pyasn1

9.  Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire "/boot/grub/menu.lst" in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

	In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Verrà disabilitato NUMA, a causa di un bug nel kernel compatibile con Red Hat di Oracle.

	È consigliabile inoltre *rimuovere* i parametri seguenti:

		rhgb quiet crashkernel=auto

	L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale.

	L'opzione `crashkernel` può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, il che può causare problemi con le macchine virtuali di dimensioni inferiori.

10.  Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito.

11.  Installare l'agente Linux di Azure eseguendo il comando seguente:

		# sudo yum install WALinuxAgent

	Si noti che, installando il pacchetto WALinuxAgent, i pacchetti NetworkManager e NetworkManager-gnome verranno rimossi, se l'operazione non è già stata eseguita come descritto nel passaggio 2.

12.  Non creare l'area di swap sul disco del sistema operativo.

	L'agente Linux di Azure può configurare automaticamente l'area di swap usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco *temporaneo* che può essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure, come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

		ResourceDisk.Format=y

		ResourceDisk.Filesystem=ext4

		ResourceDisk.MountPoint=/mnt/resource

		ResourceDisk.EnableSwap=y

		ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.

13.  Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

14.  Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

## Oracle Linux 7.0+
**Modifiche in Oracle Linux 7**

La preparazione di una macchina virtuale Oracle Linux 7 per Azure è molto simile a Oracle Linux 6, tuttavia vi sono alcune importanti differenze da tenere in considerazione:

-   Sia il kernel compatibile con Red Hat di Oracle sia UEK3 di Oracle sono supportati in Azure. È consigliabile usare il kernel UEK3.

-   Il pacchetto NetworkManager e l'agente Linux di Azure non sono più in conflitto. Questo pacchetto viene installato per impostazione predefinita ed è consigliabile non rimuoverlo.

-   GRUB2 viene ora usato come bootloader predefinito, quindi la procedura per la modifica dei parametri kernel è cambiata (vedere di seguito).

-   XFS è ora il file system predefinito. Se si vuole, è ancora possibile usare il file system ext4.

**Procedura di configurazione**

1.  Nella console di gestione di Hyper-V selezionare la macchina virtuale.

2.  Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.

3.  Creare nella directory /etc/sysconfig/ un file denominato **network** contenente il testo seguente:

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

4.  Nella directory /etc/sysconfig/network-scripts/ creare un file denominato **ifcfg-eth0** contenente il testo seguente:

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
			PEERDNS=yes
		IPV6INIT=no

5.  Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti provocano problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V.

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6.  Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:

		# sudo chkconfig network on

7.  Installare il pacchetto python-pyasn1 eseguendo il comando seguente:

		# sudo yum install python-pyasn1

8.  Eseguire il comando seguente per cancellare i metadati yum correnti e installare eventuali aggiornamenti:

		# sudo yum clean all
		# sudo yum -y update

9.  Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A tale scopo, aprire "/etc/default/grub" in un editor di testo e modificare il parametro GRUB\_CMDLINE\_LINUX, ad esempio:

		GRUB\_CMDLINE\_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

	In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. È consigliabile inoltre *rimuovere* i parametri seguenti:

		rhgb quiet crashkernel=auto

	L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale.

	L'opzione `crashkernel` può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, il che può causare problemi con le macchine virtuali di dimensioni inferiori.

10.  Dopo aver terminato di modificare "/etc/default/grub", eseguire il comando seguente per ricompilare la configurazione GRUB:

		# sudo grub2-mkconfig -o /boot/grub2/grub.cfg

11.  Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito.

12.  Installare l'agente Linux di Azure eseguendo il comando seguente:

		# sudo yum install WALinuxAgent

13.  Non creare l'area di swap sul disco del sistema operativo.

	L'agente Linux di Azure può configurare automaticamente l'area di swap usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco *temporaneo* che può essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure, come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048 ## NOTE: Set this to whatever you need it to be.

14.  Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

15.  Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

<!---HONumber=AcomDC_0824_2016-->