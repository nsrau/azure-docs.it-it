---
title: Acquisire un&quot;immagine di una macchina virtuale Linux | Microsoft Docs
description: Informazioni su come acquisire un&quot;immagine di una macchina virtuale di Azure basata su Linux creata con il modello di distribuzione classica.
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 17d7ffee-a58e-4290-9de1-64c3cf1ddc05
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 7453b7b5938a15564110cae31fa878ee3616620a


---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Come acquisire una macchina virtuale Linux classica come immagine
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Informazioni su come [eseguire questa procedura con il modello di Resource Manager](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Questo articolo illustra come acquisire una macchina virtuale Azure classica che esegue Linux come immagine per creare altre macchine virtuali. Questa immagine include il disco del sistema operativo e i dischi dati collegati alla macchina virtuale. Poiché la configurazione di rete non è inclusa, è necessario definirla quando si creano le altre macchine virtuali dall'immagine.

Azure archivia l'immagine in **Immagini**, insieme a tutte le immagini caricate. Per altre informazioni sulle immagini, vedere [Informazioni sulle immagini di macchine virtuali in Azure][Informazioni sulle immagini di macchine virtuali in Azure].

## <a name="before-you-begin"></a>Prima di iniziare
Questa procedura presuppone che sia stata creata una macchina virtuale di Azure tramite il modello di distribuzione classico e che sia stato configurato il sistema operativo, inclusi gli eventuali dischi dati connessi. Se è necessario creare una VM, leggere l'articolo [creare una macchina virtuale Linux][creare una macchina virtuale Linux].

## <a name="capture-the-virtual-machine"></a>Acquisizione della macchina virtuale
1. [Effettuare la connessione alla macchina virtuale](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) usando un client SSH a scelta.
2. Nella finestra di SSH digitare il comando seguente. L'output di `waagent` può variare leggermente, in base alla versione dell'utilità:
   
    `sudo waagent -deprovision+user`
   
    Questo comando prova a pulire il sistema per renderlo idoneo per un nuovo provisioning. Questa operazione esegue le attività seguenti:
   
   * Rimuove le chiavi host SSH (se Provisioning.RegenerateSshHostKeyPair è 'y' nel file di configurazione)
   * Cancella la configurazione NameServer in /etc/resolv.conf
   * Rimuove la password `root` dell'utente da /etc/shadow (se Provisioning.DeleteRootPassword è 'y' nel file di configurazione)
   * Rimuove i lease client DHCP memorizzati nella cache
   * Ripristina il nome host su localhost.localdomain
   * Elimina anche l'ultimo account utente (ottenuto da /var/lib/waagent) di cui è stato effettuato il provisioning **e i dati associati**.
     
     > [!NOTE]
     > Il deprovisioning elimina file e dati per "generalizzare" l'immagine. Eseguire questo comando solo in una macchina virtuale che si vuole acquisire come nuovo modello di immagine. Ciò non garantisce che dall'immagine vengano cancellate tutte le informazioni sensibili o che l'immagine sia adatta per la ridistribuzione a terze parti.
     > 
     > 
3. Digitare **y** per continuare. È possibile aggiungere il parametro `-force` per evitare questo passaggio di conferma.
4. Digitare **Exit** per chiudere il client SSH.
   
   > [!NOTE]
   > I restanti passaggi presuppongono che l' [interfaccia della riga di comando di Azure](../xplat-cli-install.md) sia stata già installata sul computer client. Tutti i passaggi riportati di seguito possono essere eseguiti anche nel [portale di Azure classico][portale di Azure classico].
   > 
   > 
5. Dal computer client, aprire l'interfaccia della riga di comando di Azure ed eseguire l'accesso alla sottoscrizione di Azure. Per informazioni dettagliate, leggere [Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure](../xplat-cli-connect.md).
6. Assicurarsi che sia attiva la modalità Gestione dei servizi:
   
    `azure config mode asm`
7. Arrestare la macchina virtuale già sottoposta al deprovisioning nei passaggi precedenti con:
   
    `azure vm shutdown <your-virtual-machine-name>`
   
   > [!NOTE]
   > È possibile individuare tutte le macchine virtuali create nella sottoscrizione utilizzando `azure vm list`
   > 
   > 
8. Quando la macchina virtuale viene arrestata, acquisire l'immagine con il comando:
   
    `azure vm capture -t <your-virtual-machine-name> <new-image-name>`
   
    Digitare il nome dell'immagine al posto di *new-image-name*. Questo comando consente di creare un'immagine generalizzata del sistema operativo. Il sottocomando `-t` consente di eliminare la macchina virtuale originale.
9. La nuova immagine è ora disponibile nell'elenco delle immagini che possono essere utilizzate per configurare nuove macchine virtuali. È possibile visualizzarla con il comando:
   
   `azure vm image list`
   
   Nel [portale di Azure classico][portale di Azure classico] verrà visualizzata all'interno dell'elenco **IMMAGINI**.
   
   ![Acquisizione dell'immagine eseguita correttamente](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Passaggi successivi
L'immagine è pronta per essere utilizzata per creare macchine virtuali. È possibile usare il comando `azure vm create` dell'interfaccia della riga di comando di Azure e indicare il nome dell'immagine creata. Per informazioni dettagliate sul comando, vedere [Uso dell'interfaccia della riga di comando di Azure con modello di distribuzione classica](../virtual-machines-command-line-tools.md) . In alternativa, usare il [portale di Azure classico][portale di Azure classico] per creare una macchina virtuale personalizzata usando il metodo **Da raccolta** e selezionando l'immagine creata. Per altri dettagli, vedere [Come creare una macchina virtuale personalizzata][Come creare una macchina virtuale personalizzata].

**Vedere anche:** [Manuale dell'utente dell'agente Linux di Azure](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[portale di Azure classico]: http://manage.windowsazure.com
[Informazioni sulle immagini di macchine virtuali in Azure]: virtual-machines-linux-classic-about-images.md
[Come creare una macchina virtuale personalizzata]: virtual-machines-linux-classic-create-custom.md
[Come collegare un disco dati a una macchina virtuale]: virtual-machines-windows-classic-attach-disk.md
[creare una macchina virtuale Linux]: virtual-machines-linux-classic-create-custom.md



<!--HONumber=Nov16_HO3-->


