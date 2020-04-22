---
title: Creare e caricare un disco rigido virtuale Oracle Linux
description: Informazioni su come creare e caricare un disco rigido virtuale (VHD) di Azure che contiene un sistema operativo Oracle Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/10/2019
ms.author: guybo
ms.openlocfilehash: fd6d17709cc3e5e9f6bb89ed7480fcd9ee80fd97
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759380"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Preparare una macchina virtuale Oracle Linux per Azure

In questo articolo si presuppone che l'utente abbia già installato un sistema operativo Oracle Linux in un disco rigido virtuale. Sono disponibili vari strumenti per creare file con estensione vhd, ad esempio una soluzione di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e Configurare una macchina virtuale.](https://technet.microsoft.com/library/hh846766.aspx)

## <a name="oracle-linux-installation-notes"></a>Note generali sull'installazione di Oracle Linux
* Vedere anche [Note generali sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes) per altri suggerimenti sulla preparazione di Linux per Azure.
* Hyper-V e Azure supportano Oracle Linux con il kernel aziendale indistruttibile (UEK) o il kernel compatibile Red Hat.
* UEK2 di Oracle non è supportato su Hyper-V e Azure perché non include i driver necessari.
* Il formato VHDX non è supportato in Azure, solo nei **VHD fissi**.  È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd.
* Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che spesso è la scelta predefinita per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le macchine virtuali clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra macchina virtuale per la risoluzione dei problemi. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) possono essere utilizzati su dischi dati, se lo si preferisce.
* I kernel Linux con versione precedente a 2.6.37 non supportano NUMA in Hyper-V con macchine virtuali di dimensioni maggiori. Questo problema influisce principalmente sulle distribuzioni meno recenti utilizzando il kernel upstream Red Hat 2.6.32 ed è stato risolto in Oracle Linux 6.6 e versioni successive
* Non configurare una partizione swap nel disco del sistema operativo. L'agente Linux può essere configurato in modo da creare un file swap sul disco temporaneo delle risorse.  Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.
* Le dimensioni virtuali di tutti i dischi rigidi virtuali su Azure devono essere allineate a 1 MB. Quando si converte un disco non formattato in un disco rigido virtuale, prima della conversione è necessario assicurarsi che le dimensioni del disco non formattato siano un multiplo di 1 MB. Per altre informazioni, vedere [Note sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes).
* Verificare che il repository `Addons` sia abilitato. Modificare il `/etc/yum.repos.d/public-yum-ol6.repo`file (Oracle `/etc/yum.repos.d/public-yum-ol7.repo`Linux 6) o (Oracle `enabled=0` `enabled=1` Linux 7) e modificare la riga in **in [ol6_addons]** o **[ol7_addons]** in questo file.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6.4 e versioni successive
Per l'esecuzione della macchina virtuale in Azure è necessario eseguire specifici passaggi di configurazione nel sistema operativo.

1. Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.
2. Fare clic su **Connetti** per aprire la finestra della macchina virtuale.
3. Disinstallare NetworkManager attivando il seguente comando:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Nota:** se il pacchetto non è già installato, questo comando avrà esito negativo e verrà visualizzato un messaggio di errore. Si tratta di un comportamento previsto.
4. Creare un file denominato **network** in the `/etc/sysconfig/` contenente il testo seguente:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Creare un file denominato **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` contenente il testo seguente:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. Modificare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti possono provocare problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il seguente comando:
   
        # chkconfig network on
8. Installare python-pyasn1 eseguendo il comando seguente:
   
        # sudo yum install python-pyasn1
9. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. Per fare questo aprire "/boot/grub/menu.lst" in un editor di testo e assicurarsi che il kernel includa i seguenti parametri:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   In questo modo si garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug.
   
   Inoltre, è consigliabile *rimuovere* i parametri seguenti:
   
        rhgb quiet crashkernel=auto
   
   L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale.
   
   L'opzione `crashkernel` può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, il che potrebbe causare problemi con le macchine virtuali di dimensioni inferiori.
10. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.  Questo è in genere il valore predefinito.
11. Installare l'agente Linux di Azure eseguendo il comando seguente: La versione più recente è la 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Si noti che, installando il pacchetto WALinuxAgent, i pacchetti NetworkManager e NetworkManager-gnome verranno rimossi, se l'operazione non è già stata eseguita come descritto nel passaggio 2.
12. Non creare l'area di swap sul disco del sistema operativo.
    
    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco di risorse locale è un disco *temporaneo* e potrebbe essere svuotato quando viene eseguito il deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7.0 e versioni successive
**Modifiche in Oracle Linux 7**

La preparazione di una macchina virtuale Oracle Linux 7 per Azure è molto simile a Oracle Linux 6, tuttavia vi sono alcune importanti differenze da notare:

* Azure supporta Oracle Linux con Unbreakable Enterprise Kernel (UEK) o Red Hat Compatible Kernel. Si consiglia Oracle Linux con UEK.
* Il pacchetto NetworkManager e l'agente Linux di Azure non sono più in conflitto. Questo pacchetto viene installato per impostazione predefinita ed è consigliabile non rimuoverlo.
* GRUB2 viene ora usato come bootloader predefinito, quindi la procedura per la modifica dei parametri kernel è cambiata (vedere di seguito).
* XFS è ora il file system predefinito. Se si vuole, è ancora possibile usare il file system ext4.

**Passaggi di configurazione**

1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.
2. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.
3. Creare un file denominato **network** in the `/etc/sysconfig/` contenente il testo seguente:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Creare un file denominato **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` contenente il testo seguente:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Modificare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti possono provocare problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il seguente comando:
   
        # sudo chkconfig network on
7. Installare il pacchetto python-pyasn1 eseguendo il comando seguente:
   
        # sudo yum install python-pyasn1
8. Eseguire il comando seguente per cancellare i metadati yum correnti e installare eventuali aggiornamenti:
   
        # sudo yum clean all
        # sudo yum -y update
9. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A tale scopo, aprire "/etc/default/grub" in un editor di testo e modificare il parametro `GRUB_CMDLINE_LINUX` , ad esempio:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Disattiva inoltre le convenzioni di denominazione per le schede di interfaccia di rete in Oracle Linux 7 con il kernel aziendale infrangibile. Inoltre, è consigliabile *rimuovere* i parametri seguenti:
   
       rhgb quiet crashkernel=auto
   
   L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale.
   
   L'opzione `crashkernel` può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, il che potrebbe causare problemi con le macchine virtuali di dimensioni inferiori.
10. Dopo aver terminato di modificare "/etc/default/grub" come sopra illustrato, eseguire il comando seguente per ricompilare la configurazione GRUB:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.  Questo è in genere il valore predefinito.
12. Installare l'agente Linux di Azure eseguendo il comando seguente:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Non creare l'area di swap sul disco del sistema operativo.
    
    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco di risorse locale è un disco *temporaneo* e potrebbe essere svuotato quando viene eseguito il deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

## <a name="next-steps"></a>Passaggi successivi
È ora possibile usare il file con estensione vhd Oracle Linux per creare nuove macchine virtuali in Azure. Se è la prima volta che si carica il file VHD in Azure, vedere [Creare una macchina virtuale Linux da un disco personalizzato](upload-vhd.md#option-1-upload-a-vhd).

