---
title: Acquisire un'immagine di una macchina virtuale Linux | Microsoft Docs
description: Informazioni su come acquisire un'immagine di una macchina virtuale di Azure basata su Linux creata con il modello di distribuzione classica.
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
ms.date: 03/14/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: ecde5dd3211bfbb290e6910d7d55136d079c6cf3
ms.contentlocale: it-it
ms.lasthandoff: 06/26/2017


---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Come acquisire una macchina virtuale Linux classica come immagine
> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Informazioni su come [eseguire questa procedura con il modello di Resource Manager](../capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Questo articolo illustra come acquisire una macchina virtuale di Azure classica che esegue Linux come un'immagine per creare altre macchine virtuali. Tale immagine include il disco del sistema operativo e i dischi dati collegati alla macchina virtuale. Poiché la configurazione di rete non è inclusa, è necessario definirla quando si crea l'altra macchina virtuale dall'immagine.

Azure archivia l'immagine in **Immagini**, insieme a tutte le immagini caricate. Per altre informazioni sulle immagini, vedere [Informazioni sulle immagini di macchine virtuali in Azure][About Virtual Machine Images in Azure].

## <a name="before-you-begin"></a>Prima di iniziare
Questa procedura presuppone che sia stata creata una macchina virtuale di Azure tramite il modello di distribuzione classica e che sia stato configurato il sistema operativo, inclusi gli eventuali dischi dati connessi. Se è necessario creare una macchina virtuale, leggere l'articolo [Come creare una macchina virtuale Linux][How to Create a Linux Virtual Machine].

## <a name="capture-the-virtual-machine"></a>Acquisizione della macchina virtuale
1. [Connettersi alla macchina virtuale](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) usando un client SSH di propria scelta.
2. Nella finestra di SSH digitare il comando seguente. L'output di `waagent` può variare leggermente, in base alla versione dell'utilità:

    ```bash
    sudo waagent -deprovision+user
    ```

    Il comando precedente prova a pulire il sistema per renderlo idoneo per un nuovo provisioning. Questa operazione esegue le attività seguenti:

   * Rimuove le chiavi host SSH (se Provisioning.RegenerateSshHostKeyPair è 'y' nel file di configurazione)
   * Cancella la configurazione NameServer in /etc/resolv.conf
   * Rimuove la password `root` dell'utente da /etc/shadow (se Provisioning.DeleteRootPassword è 'y' nel file di configurazione)
   * Rimuove i lease client DHCP memorizzati nella cache
   * Ripristina il nome host su localhost.localdomain
   * Elimina anche l'ultimo account utente (ottenuto da /var/lib/waagent) di cui è stato effettuato il provisioning **e i dati associati**.

     > [!NOTE]
     > Il deprovisioning elimina file e dati per "generalizzare" l'immagine. Eseguire questo comando solo su una macchina virtuale da acquisire come nuovo modello di immagine. Ciò non garantisce che dall'immagine vengano cancellate tutte le informazioni sensibili o che l'immagine sia adatta per la ridistribuzione a terze parti.

3. Digitare **y** per continuare. È possibile aggiungere il parametro `-force` per evitare questo passaggio di conferma.
4. Digitare **Exit** per chiudere il client SSH.

   > [!NOTE]
   > I restanti passaggi presuppongono che l' [interfaccia della riga di comando di Azure](../../../cli-install-nodejs.md) sia stata già installata sul computer client. Tutti i passaggi seguenti possono essere eseguiti anche nel [Portale di Azure](http://portal.azure.com).

5. Dal computer client, aprire l'interfaccia della riga di comando di Azure ed eseguire l'accesso alla sottoscrizione di Azure. Per informazioni dettagliate, leggere [Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure](../../../xplat-cli-connect.md).

   > [!NOTE]
   > Accedere al Portale di Azure.

6. Assicurarsi che sia attiva la modalità Gestione dei servizi:

    ```azurecli
    azure config mode asm
    ```

7. Arrestare la macchina virtuale già sottoposta a deprovisioning. Nell'esempio seguente viene arrestata la macchina virtuale denominata `myVM`:

    ```azurecli
    azure vm shutdown myVM
    ```
   Se necessario, è possibile visualizzare un elenco di tutte le macchine virtuali create nella propria sottoscrizione tramite il comando `azure vm list`

   > [!NOTE]
   > Se si usa il Portale di Azure, selezionare la macchina virtuale e fare clic su **Arresta** per arrestarla.

8. Quando la macchina virtuale viene arrestata, acquisire l'immagine. Nell'esempio seguente viene acquisita la macchina virtuale denominata `myVM` e viene creata un'immagine generalizzata denominata `myNewVM`:

    ```azurecli
    azure vm capture -t myVM myNewVM
    ```

    Il sottocomando `-t` consente di eliminare la macchina virtuale originale.

    > [!NOTE]
    > Nel Portale di Azure è possibile acquisire un'immagine selezionando **Immagine** dal menu Hub. Per l'immagine è necessario specificare le informazioni seguenti: nome, gruppo di risorse, posizione, tipo di sistema operativo e percorso BLOB di archiviazione.

9. La nuova immagine è ora disponibile nell'elenco di immagini che possono essere usate per configurare nuove macchine virtuali. È possibile visualizzarla con il comando:

   ```azurecli
   azure vm image list
   ```

   Nel [portale di Azure](http://portal.azure.com) la nuova immagine viene visualizzata in **Immagini VM (classico)** che appartiene a **Servizi di calcolo**. Per accedere a **Immagini VM (classico)**, fare clic su _Altri servizi_ nella parte inferiore dell'elenco di servizi di Azure e quindi eseguire una ricerca in **Servizi di calcolo**.   

   ![Acquisizione dell'immagine eseguita correttamente](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Passaggi successivi
L'immagine può ora essere usata per creare macchine virtuali. È possibile usare il comando `azure vm create` dell'interfaccia della riga di comando di Azure e indicare il nome dell'immagine creata. Per altre informazioni, vedere [Uso dell'interfaccia della riga di comando di Azure con il modello di distribuzione classica](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

In alternativa, usare il [Portale di Azure](http://portal.azure.com) per creare una macchina virtuale personalizzata tramite il metodo **Image** e selezionando l'immagine creata. Per altre informazioni, vedere [Come creare una macchina virtuale personalizzata][How to Create a Custom Virtual Machine].

**Vedere anche:** [Manuale dell'utente dell'agente Linux di Azure](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[About Virtual Machine Images in Azure]:../../virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]:create-custom.md
[How to Attach a Data Disk to a Virtual Machine]:attach-disk.md
[How to Create a Linux Virtual Machine]:create-custom.md

