---
title: Creare un nodo head HPC Pack in una macchina virtuale di Azure | Microsoft Docs
description: Informazioni su come usare il portale di Azure e il modello di distribuzione di Resource Manager per creare un nodo head Microsoft HPC Pack 2012 R2 in una macchina virtuale di Azure.
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,hpc-pack
ms.assetid: e6a13eaf-9124-47b4-8d75-2bc4672b8f21
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 3d8300bbb54bd88e6ff3844208ec5d5fa25c5e8d
ms.openlocfilehash: d935f45f87558dd7f9838ad3b370de0d9a7870a1


---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Creare il nodo head di un cluster HPC Pack in una macchina virtuale di Azure con un'immagine del Marketplace
Usare un'[immagine di macchina virtuale Microsoft HPC Pack 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) in Azure Marketplace e nel portale di Azure per creare il nodo head di un cluster HPC. L'immagine di macchina virtuale HPC Pack è basata su Windows Server 2012 R2 Datacenter con HPC Pack 2012 R2 Update 3 preinstallato. Usare questo nodo head per una distribuzione basata sul modello di verifica di HPC Pack in Azure. Sarà quindi possibile aggiungere nodi di calcolo al cluster per eseguire carichi di lavoro HPC.

> [!TIP]
> Per distribuire un cluster HPC Pack 2012 R2 completo in Azure che include il nodo head e nodi di calcolo, si consiglia di usare un metodo automatizzato. Le opzioni includono lo [script di distribuzione di HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) e i modelli di Resource Manager, ad esempio il [cluster HPC Pack per i carichi di lavoro di Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). I modelli di Resource Manager sono disponibili anche per i [cluster Microsoft HPC Pack 2016](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates). 
> 
> 

## <a name="planning-considerations"></a>Considerazioni sulla pianificazione
Come illustrato nella figura riportata di seguito, il nodo head HPC Pack viene distribuito in un dominio di Active Directory in una rete virtuale di Azure.

![Nodo head HPC Pack][headnode]

* **Dominio di Active Directory**: il nodo head di HPC Pack 2012 R2 deve essere aggiunto a un dominio di Active Directory in Azure prima dell'avvio dei servizi HPC nella VM. Come illustrato in questo articolo, per una distribuzione basata sul modello di verifica, è possibile alzare di livello la macchina virtuale creata per il nodo head a controller di dominio prima di avviare i servizi HPC. In alternativa è possibile distribuire un controller di dominio e una foresta separati in Azure a cui aggiungere la VM del nodo head.

* **Modello di distribuzione**: per la maggior parte delle nuove distribuzioni, Microsoft consiglia di usare il modello di distribuzione di Resource Manager. Questo articolo presuppone che si usi questo modello di distribuzione.

* **Rete virtuale di Azure**: quando si usa il modello di distribuzione di Resource Manager per distribuire il nodo head, specificare o creare una rete virtuale di Azure. Usare la rete virtuale se è necessario aggiungere il nodo head a un dominio di Active Directory esistente. Servirà anche in un secondo momento per aggiungere VM dei nodi di calcolo al cluster.

## <a name="steps-to-create-the-head-node"></a>Passaggi per la creazione del nodo head
Di seguito sono indicati i principali passaggi per usare il portale di Azure per creare una VM di Azure per il nodo head HPC Pack usando il modello di distribuzione di Resource Manager. 

1. Se si desidera creare una nuova foresta di Active Directory in Azure con VM di controller di dominio separate, è possibile usare un [modello di Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc). Per una semplice distribuzione basata sul modello di verifica, è possibile saltare questo passaggio e configurare la VM del nodo head come controller di dominio. Le opzioni sono descritte più avanti in questo articolo.
2. Alla pagina [HPC Pack 2012 R2 su Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) di Azure Marketplace fare clic su **Crea macchina virtuale**. 
3. Nel portale, alla pagina **HPC Pack 2012 R2 on Windows Server 2012 R2** selezionare il modello di distribuzione di **Resource Manager** e fare clic su **Crea**.
   
    ![Immagine di HPC Pack][marketplace]
4. Usare il portale per configurare le impostazioni e creare la macchina virtuale. Chi non conosce Azure può seguire l'esercitazione [Creare la prima macchina virtuale Windows nel portale di Azure](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Per una distribuzione con modello di verifica è in genere possibile accettare le impostazioni predefinite o consigliate.
   
   > [!NOTE]
   > Se si desidera aggiungere il nodo head a un dominio di Active Directory esistente in Azure, assicurarsi di specificare la rete virtuale per tale dominio quando si crea la VM.
   > 
   > 
5. Dopo aver creato e avviato la VM, [connettersi alla VM](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) tramite Desktop remoto. 
6. Aggiungere la macchina virtuale a una foresta di domini di Active Directory scegliendo una delle opzioni seguenti:
   
   * Se la VM è stata creata in una rete virtuale di Azure con una foresta di domini esistente, aggiungerla alla foresta usando gli strumenti standard di Server Manager o Windows PowerShell. Eseguire quindi il riavvio.
   * Se la VM è stata creata in una nuova rete virtuale senza una foresta di domini esistente, configurare la VM come controller di dominio. Usare i passaggi standard per installare e configurare il ruolo Servizi di dominio Active Directory nel nodo head. Per informazioni dettagliate, vedere [Installare una nuova foresta di Active Directory di Windows Server 2012](https://technet.microsoft.com/library/jj574166.aspx).
7. Quando la VM è in esecuzione e fa parte di una foresta Active Directory, avviare i servizi di HPC Pack come descritto di seguito:
   
    a. Connettersi alla VM del nodo head con un account di dominio membro del gruppo Amministratori locale. Ad esempio, è possibile usare l'account di amministratore impostato al momento della creazione della VM del nodo head.
   
    b. Per usare una configurazione predefinita del nodo head, avviare Windows PowerShell come amministratore e digitare il comando seguente:
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    Per l'avvio dei servizi HPC Pack potrebbero essere richiesti vari minuti.
   
    Per ulteriori opzioni per la configurazione del nodo head, digitare `get-help HPCHNPrepare.ps1`.

## <a name="next-steps"></a>Passaggi successivi
* È ora possibile interagire con il nodo head del cluster HPC Pack. Ad esempio, avviare Gestione cluster HPC e completare l' [elenco delle attività di distribuzione](https://technet.microsoft.com/library/jj884141.aspx).
* Se si desidera aumentare la capacità di calcolo del cluster su richiesta, aggiungere [nodi "burst" di Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) in un servizio cloud. 
* Provare a eseguire un carico di lavoro di test nel cluster. Per un esempio vedere la [guida introduttiva](https://technet.microsoft.com/library/jj884144)a HPC Pack.

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png



<!--HONumber=Jan17_HO1-->


