---
title: Introduzione a Linux in Azure | Microsoft Docs
description: Informazioni sull&quot;uso delle macchine virtuali Linux in Azure.
services: virtual-machines-linux
documentationcenter: python
author: szarkos
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: b13bf305-87bf-4df3-815e-e8f6337aa6ea
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: eaac5300292e328bcff9ddf5447bea0e53075179
ms.lasthandoff: 04/03/2017


---
# <a name="introduction-to-linux-on-azure"></a>Introduzione a Linux in Azure
In questo argomento viene fornita una panoramica relativa ad alcuni aspetti dell'uso di macchine virtuali Linux nel cloud di Azure. Se si usa un'immagine presente nella raccolta, la distribuzione di una macchina virtuale Linux è un processo estremamente semplice.

## <a name="authentication-usernames-passwords-and-ssh-keys"></a>Autenticazione: nomi utente, password e chiavi SSH
Quando si crea una macchina virtuale Linux usando il portale di Azure classico viene richiesto di inserire un nome utente, una password o una chiave pubblica SSH. La scelta del nome utente per la distribuzione di una macchina virtuale Linux in Azure è soggetta a un vincolo: i nomi degli account di sistema (UID <100) già presenti nella macchina virtuale, ad esempio l'account 'root', non sono consentiti.

* Vedere [Creare una macchina virtuale che esegue Linux](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Vedere [Come usare SSH con Linux in Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="obtaining-superuser-privileges-using-sudo"></a>Ottenere privilegi utente avanzato tramite `sudo`
L'account utente specificato durante la distribuzione di istanze di macchine virtuali in Azure è un account con privilegi. Tale account viene configurato dall'agente Linux di Azure con la capacità di elevare i privilegi al ruolo di utente ROOT (account utente con privilegi avanzati) tramite l'utilità `sudo` . Dopo aver eseguito l'accesso usando questo account utente, sarà possibile eseguire comandi come utente ROOT usando la sintassi del comando.

    # sudo <COMMAND>

Facoltativamente, è possibile ottenere una shell di root usando **sudo -s**.

* Vedere [Uso di privilegi root sulle macchine virtuali Linux in Azure](use-root-privileges.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="firewall-configuration"></a>Configurazione del firewall
In Azure è disponibile un filtro dei pacchetti in ingresso che limita la connettività alle porte specificate nel portale di Azure classico. Per impostazione predefinita, l'unica porta consentita è SSH. È possibile aprire l'accesso a porte aggiuntive nella macchina virtuale Linux mediante la configurazione di endpoint nel portale di Azure classico:

* Vedere [Come configurare gli endpoint in una macchina virtuale](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

Le immagini Linux disponibili nella Raccolta di Azure non abilitano il firewall *iptables* per impostazione predefinita. Se necessario, è possibile configurare il firewall per implementare funzionalità di filtro.

## <a name="hostname-changes"></a>Modifica del nome host
Quando si distribuisce un'istanza di un'immagine Linux è necessario specificare un nome host per la macchina virtuale. Quando la macchina virtuale è in esecuzione, il nome host viene pubblicato nei server DNS della piattaforma, in modo che più macchine virtuali connesse tra loro possano eseguire ricerche di indirizzi IP usando i nomi host.

Se, dopo la distribuzione di una macchina virtuale, si vuole modificarne il nome host, usare il comando

    # sudo hostname <newname>

L'agente Linux di Azure include funzionalità per il rilevamento automatico delle modifiche del nome host, la configurazione della macchina virtuale per il salvataggio permanente della modifica e la pubblicazione della modifica nei server DNS della piattaforma.

* [Guida dell'utente dell'agente Linux di Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="cloud-init"></a>cloud-init
Le immagini **Ubuntu** e **CoreOS** usano cloud-init in Azure, che offre capacità aggiuntive per il bootstrap di una macchina virtuale.

* [Come inserire dati personalizzati](../windows/classic/inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Dati personalizzati e cloud-init in Microsoft Azure](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
* [Creare partizioni di scambio di Azure con cloud-init](https://wiki.ubuntu.com/AzureSwapPartitions)
* [Come usare CoreOS in Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)

## <a name="virtual-machine-image-capture"></a>Acquisizione di immagini di macchine virtuali
Azure offre la possibilità di acquisire lo stato di una macchina virtuale esistente in un'immagine che potrà essere usata in un secondo momento per distribuire altre istanze di macchine virtuali. L'agente Linux di Azure può essere usato per ripristinare lo stato precedente di alcune personalizzazioni effettuate durante il processo di provisioning. Per acquisire una macchina virtuale come immagine, eseguire la procedura seguente:

1. Eseguire **waagent -deprovision** per annullare la personalizzazione del provisioning. Facoltativamente, eseguire **waagent -deprovision+user** per eliminare l'account utente specificato durante il provisioning e tutti i relativi dati.
2. Arrestare/Spegnere la macchina virtuale.
3. Fare clic su *Acquisisci* nel portale di Azure classico oppure usare Powershell o gli strumenti dell’interfaccia della riga di comando per acquisire la macchina virtuale come immagine.
   
   * Vedere: [Come acquisire una macchina virtuale Linux da usare come modello](classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="attaching-disks"></a>Collegamento di dischi
Ogni macchina virtuale ha un *disco risorse* temporaneo locale collegato. Poiché è possibile che i dati su un disco risorse non vengano mantenuti tra un riavvio e l'altro, questo tipo di disco viene spesso usato da applicazioni e processi in esecuzione nella macchina virtuale per l'archiviazione **temporanea** di dati. È anche usato per archiviare file di paging o di scambio per il sistema operativo.

In Linux il disco risorse è in genere gestito dall'agente Linux di Azure e viene montato automaticamente in **/mnt/resource** (o **/mnt** nelle immagini Ubuntu).

> [!NOTE]
> Si noti che il disco risorse è un disco **temporaneo** e potrebbe essere eliminato e riformattato al riavvio della macchina virtuale.
> 
> 

In Linux il kernel potrebbe assegnare al disco dati il nome `/dev/sdc`. In questo caso gli utenti dovranno suddividere in partizioni, formattare e montare tale risorsa. Questa procedura è illustrata in dettaglio nell'esercitazione [Come collegare un disco dati a una macchina virtuale](../windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

* **Vedere anche:** [Configurare RAID software in Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) & [Configurare LVM su una macchina virtuale Linux in Azure](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


