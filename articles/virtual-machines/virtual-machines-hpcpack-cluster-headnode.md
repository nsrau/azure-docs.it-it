<properties
 pageTitle="Creare un nodo head HPC Pack in una macchina virtuale di Azure | Microsoft Azure"
 description="Informazioni su come usare il portale di Azure e il modello di distribuzione classica per creare un nodo head Microsoft HPC Pack in una macchina virtuale di Azure."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Creare il nodo head di un cluster HPC Pack in una macchina virtuale di Azure con un'immagine del Marketplace

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In questo articolo viene illustrata la creazione di una risorsa con il modello di distribuzione classica.

Questo articolo illustra come usare l'[immagine di macchina virtuale Microsoft HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) in Azure Marketplace per creare il nodo head di un cluster Windows HPC in Azure nel modello di distribuzione (gestione dei servizi) classica. Il nodo head deve essere aggiunto a un dominio di Active Directory in una rete virtuale di Azure. È possibile usare questo nodo head per una distribuzione con modello di verifica di HPC Pack in Azure e aggiungere risorse di calcolo al cluster per eseguire carichi di lavoro HPC.


![Nodo head HPC Pack][headnode]

>[AZURE.NOTE]L'immagine di macchina virtuale HPC Pack è attualmente basata su Windows Server 2012 R2 Datacenter con HPC Pack 2012 R2 Update 2 preinstallato. È preinstallato anche Microsoft SQL Server 2014 Express.


Per una distribuzione di produzione di un cluster HPC Pack in Azure, si consiglia di un metodo di distribuzione automatica, ad esempio lo [script di distribuzione IaaS di HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md) o un [modello di avvio rapido](https://azure.microsoft.com/documentation/templates/) di Gestione risorse di Azure.

## Considerazioni sulla pianificazione

* **Dominio di Active Directory** - Il nodo head di HPC Pack deve essere aggiunto a un dominio di Active Directory in Azure prima dell'avvio dei servizi HPC. Una delle opzioni supportate prevede la distribuzione di un controller di dominio e una foresta separati in Azure a cui è possibile aggiungere la macchina virtuale. Per una distribuzione con modello di verifica, è possibile alzare di livello la macchina virtuale creata per il nodo head a controller di dominio prima di avviare i servizi HPC.

* **Rete virtuale di Azure** - Se si prevede di aggiungere le macchine virtuali dei nodi di calcolo del cluster al cluster HPC o si crea un controller di dominio separato per il cluster, è necessario distribuire il nodo head in una rete virtuale di Azure. Senza una rete virtuale è comunque possibile aggiungere nodi "burst" di Azure al cluster.

## Passaggi per la creazione del nodo head

Di seguito sono indicati i principali passaggi per la creazione di una macchina virtuale di Azure per il nodo head HPC Pack. È possibile usare svariati strumenti di Azure per eseguire questi passaggi nel modello di distribuzione (gestione dei servizi) classica di Azure.


1. Se si prevede di creare una rete virtuale per la macchina virtuale del nodo head, vedere [Creare una rete virtuale (classica) usando il portale di anteprima di Azure](../virtual-networks/virtual-networks-create-vnet-classic-pportal.md).

    **Considerazioni**

    * È possibile accettare la configurazione predefinita dello spazio degli indirizzi e delle subnet della rete virtuale.

    * Se si prevede di usare dimensioni di istanze a elevato utilizzo di calcolo (A8 - A11) per il nodo head HPC Pack o in caso di aggiunta successiva di risorse di calcolo al cluster, scegliere un'area in cui le istanze sono disponibili. Quando si usano le istanze A8 o A9 per carichi di lavoro MPI, assicurarsi inoltre che lo spazio degli indirizzi della rete virtuale non si sovrapponga allo spazio degli indirizzi riservato dalla rete RDMA in Azure (172.16.0.0/12). Per altre informazioni, vedere [Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo](virtual-machines-a8-a9-a10-a11-specs.md).

2. Se è necessario creare una nuova foresta Active Directory in una macchina virtuale separata, vedere [Installazione di una nuova foresta Active Directory in una rete virtuale di Azure](../active-directory/active-directory-new-forest-virtual-machine.md).

    **Considerazioni**

    * Per molte distribuzioni di test è possibile creare un singolo controller di dominio in Azure. Per garantire disponibilità elevata per il dominio di Active Directory, è possibile distribuire un controller di dominio di backup aggiuntivo.

    * Per una distribuzione con modello di verifica semplice, è possibile saltare questo passaggio e in seguito alzare di livello la macchina virtuale del nodo head a controller di dominio.

3. Nel portale di gestione di Azure o nel portale di anteprima di Azure, creare una macchina virtuale classica selezionando l'immagine HPC Pack 2012 R2 da Azure Marketplace. (Vedere i passaggi per il portale di gestione [qui](virtual-machines-windows-tutorial-classic-portal.md).)

    **Considerazioni**

    * Selezionare almeno la dimensione di macchina virtuale A4.

    * Se si desidera distribuire il nodo head in una rete virtuale, assicurarsi di specificare la rete virtuale nella configurazione della macchina virtuale.

    * È consigliabile creare un nuovo servizio cloud per la macchina virtuale.

4. Dopo aver creato la macchina virtuale e quando la macchina virtuale è in esecuzione, aggiungere la macchina virtuale a una foresta di dominio esistente o creare una nuova foresta di domini nella macchina virtuale.

    **Considerazioni**

    * Se si crea la macchina virtuale in una rete virtuale di Azure con una foresta di domini esistente, connettersi alla macchina virtuale. Usare quindi gli strumenti standard Server Manager o Windows PowerShell per aggiungerla alla foresta. Eseguire quindi il riavvio.

    * Se la macchina virtuale non è stata creata in una rete virtuale di Azure o è stata creata in una rete virtuale senza una foresta di domini esistente, alzarla di livello a controller di dominio. A tale scopo, connettersi alla macchina virtuale e quindi usare gli strumenti standard Server Manager o Windows PowerShell. Per informazioni dettagliate, vedere [Installare una nuova foresta di Active Directory di Windows Server 2012](https://technet.microsoft.com/library/jj574166.aspx).

5. Quando la macchina virtuale è in esecuzione e fa parte di una foresta Active Directory, avviare i servizi di HPC Pack nel nodo head. A tale scopo, effettuare l'operazione seguente:

    a. Connettersi alla macchina virtuale con un account di dominio membro del gruppo Administrators locale. Ad esempio, è possibile usare l'account di amministratore impostato al momento della creazione della macchina virtuale del nodo head.

    b. Per usare una configurazione predefinita del nodo head, avviare Windows PowerShell come amministratore e digitare il comando seguente:

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Per l'avvio dei servizi HPC Pack potrebbero essere richiesti vari minuti.

    Per ulteriori opzioni per la configurazione del nodo head, digitare `get-help HPCHNPrepare.ps1`.


## Passaggi successivi

* È ora possibile interagire con il nodo head del cluster Windows HPC. Ad esempio, è possibile avviare Gestione cluster HPC o iniziare a usare i cmdlet di PowerShell per HPC.

* [Aggiungere macchine virtuali dei nodi di calcolo](virtual-machines-hpcpack-cluster-node-manage.md) al cluster o aggiungere [nodi burst di Azure](virtual-machines-hpcpack-cluster-node-burst.md) in un servizio cloud.

* Provare a eseguire un carico di lavoro di test nel cluster. Per un esempio vedere la [guida introduttiva](https://technet.microsoft.com/library/jj884144) a HPC Pack.

<!--Image references-->
[headnode]: ./media/virtual-machines-hpcpack-cluster-headnode/headnode.png

<!---HONumber=Oct15_HO1-->