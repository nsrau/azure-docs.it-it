---
title: Creare e caricare un'immagine della macchina virtuale FreeBSD | Microsoft Docs
description: Informazioni su come creare e caricare un disco rigido virtuale (VHD) che contiene il sistema operativo FreeBSD per creare una macchina virtuale di Azure.
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: huishao
ms.openlocfilehash: 71ba93c64657725b48ad5915c6bb26dc32e5434d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Creare e caricare un disco rigido virtuale con FreeBSD in Azure
Questo articolo descrive come creare e caricare un disco rigido virtuale (VHD) che contiene il sistema operativo FreeBSD. Dopo il caricamento, è possibile usarlo come immagine personalizzata per creare una macchina virtuale in Azure.

> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per informazioni sul caricamento di un disco rigido virtuale usando il modello di Resource Manager, vedere [qui](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che l'utente disponga degli elementi seguenti:

* **Una sottoscrizione Azure**: se non è già disponibile un account, è possibile crearne uno in pochi minuti. Se si ha un abbonamento a MSDN, vedere [Credito Azure mensile per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Altrimenti, vedere [Crea subito il tuo account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/).  
* **Strumenti di Azure PowerShell**: è necessario che il modulo Microsoft Azure PowerShell sia installato e configurato per usare la sottoscrizione di Azure. Per scaricare il modulo, vedere la pagina dei [download di Azure](https://azure.microsoft.com/downloads/). Qui è disponibile un'esercitazione che descrive come installare e configurare il modulo. Per caricare il disco rigido virtuale usare il cmdlet nella pagina [Download di Azure](https://azure.microsoft.com/downloads/) .
* **Sistema operativo FreeBSD installato in un file VHD**: è necessario installare un sistema operativo FreeBSD supportato in un disco rigido virtuale. Sono disponibili vari strumenti per la creazione di file VHD. Ad esempio, per creare il file VHD e installare il sistema operativo, è possibile usare soluzioni di virtualizzazione come Hyper-V. Per istruzioni, vedere su come installare e usare Hyper-V, vedere [Installare Hyper-V e creare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx). È disponibile anche un' [esercitazione in MSDN sull'uso di FreeBSD con Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).
>
>

Questa attività include i quattro passaggi seguenti:

## <a name="step-1-prepare-the-image-for-upload"></a>Passaggio 1: preparare l'immagine da caricare
Dalla macchina virtuale in cui è stato installato il sistema operativo FreeBSD, completare le procedure seguenti:

1. Abilitare DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. Abilitare SSH.

    Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. SSH è abilitato per impostazione predefinita dopo l'installazione dal disco FreeBSD. 
3. Configurare la console seriale.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. Installare sudo.

    L'account radice è disabilitato in Azure. Ciò significa che per eseguire comandi con privilegi elevati da un account utente senza privilegi è necessario usare sudo.

        # pkg install sudo
   
5. Prerequisiti per l'agente di Azure.

        # pkg install python27  
        # pkg install Py27-setuptools  
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Installare l'agente di Azure.

    L'ultima versione dell'agente di Azure è sempre disponibile in [github](https://github.com/Azure/WALinuxAgent/releases). La versione 2.0.10+ supporta ufficialmente FreeBSD 10 e 10.1, mentre la versione 2.1.4+ (inclusa la 2.2.x) supporta ufficialmente FreeBSD 10.2 e versioni successive.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    Per la versione 2.0 verrà usata come esempio la versione 2.0.16:

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    Per la versione 2.1 verrà usata come esempio la versione 2.1.4:

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

   > [!IMPORTANT]
   > Dopo aver installato l'agente di Azure, è consigliabile verificare che sia in esecuzione:
   >
   >

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # ps auxw | grep waagent
        root   639   0.0  0.5 104620 17520 u0- I    05:17    0:00.20 python /usr/local/sbin/waagent -daemon (python2.7)
        # cat /var/log/waagent.log
7. Eseguire il deprovisioning del sistema.

    Eseguire il deprovisioning del sistema per pulire il sistema e renderlo idoneo per un nuovo provisioning. Il comando seguente elimina anche l'ultimo account utente di cui è stato effettuato il provisioning e i dati associati:

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Ora è possibile arrestare la macchina virtuale.

## <a name="step-2-prepare-the-connection-to-azure"></a>Passaggio 2: preparare la connessione ad Azure
Assicurarsi di usare l'interfaccia della riga di comando di Azure nel modello di distribuzione classica (`azure config mode asm`), quindi accedere al proprio account:

```azurecli
azure login
```


<a id="upload"></a>


## <a name="step-3-upload-the-vhd-file"></a>Passaggio 3: Caricare il file .vhd

È necessario un account di archiviazione in cui caricare il file VHD. È possibile usare un account di archiviazione esistente o [crearne uno nuovo](../../../storage/common/storage-create-storage-account.md).

È possibile caricare il file VHD in qualsiasi posizione all'interno dell'archivio BLOB. Di seguito sono riportati alcuni termini che verranno usati durante il caricamento del file:

* **BlobStorageURL** è l'URL dell'account di archiviazione creato nel passaggio 2.
* **YourImagesFolder** è il contenitore all'interno dell'archivio BLOB in cui si vogliono archiviare le immagini.
* **VHDName** è l'etichetta visualizzata nel portale di Azure per identificare il disco rigido virtuale.
* **PathToVHDFile** è il percorso completo e il nome del file VHD.

Nella finestra di Azure PowerShell usata nel passaggio precedente, digitare:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-4-create-a-vm-with-the-uploaded-vhd-file"></a>Passaggio 4: Creare una macchina virtuale con il file con estensione vhd caricato
Dopo avere caricato il file VHD, è possibile aggiungerlo come immagine all'elenco di immagini personalizzate associate alla propria sottoscrizione e creare una macchina virtuale con questa immagine personalizzata.

1. Nella finestra di Azure PowerShell usata nel passaggio precedente, digitare:

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > Usare Linux come tipo di sistema operativo. La versione corrente di Azure PowerShell accetta solo "Linux" o "Windows" come parametro.
   >
   >
2. Una volta completati i passaggi precedenti, quando si seleziona la scheda **Immagini** nel portale di Azure classico, la nuova immagine risulta inclusa nell'elenco.  

    ![Scegli un'immagine](./media/freebsd-create-upload-vhd/addfreebsdimage.png)
3. Creare una macchina virtuale dalla raccolta. La nuova immagine è ora disponibile in **Immagini personali**.
4. Selezionare la nuova immagine. Seguire quindi le istruzioni visualizzate per configurare nome host, password/chiave SSH e così via.

    ![Immagine personalizzata](./media/freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. Dopo avere completato il provisioning, la macchina virtuale FreeBSD in esecuzione sarà visibile in Azure.

    ![immagine di FreeBSD in Azure](./media/freebsd-create-upload-vhd/freebsdimageinazure.png)
