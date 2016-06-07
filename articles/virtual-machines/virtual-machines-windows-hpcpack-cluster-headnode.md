<properties
 pageTitle="Creare un nodo head HPC Pack in una macchina virtuale di Azure | Microsoft Azure"
 description="Informazioni su come usare il portale di Azure e il modello di distribuzione di Gestione risorse per creare un nodo head Microsoft HPC Pack in una macchina virtuale di Azure."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="05/19/2016"
 ms.author="danlep"/>

# Creare il nodo head di un cluster HPC Pack in una macchina virtuale di Azure con un'immagine del Marketplace


Usare un'[immagine di macchina virtuale Microsoft HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) in Azure Marketplace per creare il nodo head di un cluster HPC usando il portale di Azure. L'immagine di macchina virtuale HPC Pack è basata su Windows Server 2012 R2 Datacenter con HPC Pack 2012 R2 Update 3 preinstallato. Usare questo nodo head per una distribuzione basata sul modello di verifica di HPC Pack in Azure. Sarà quindi possibile aggiungere nodi di calcolo al cluster per eseguire carichi di lavoro HPC.



>[AZURE.TIP]Per una distribuzione di un cluster HPC Pack completo in Azure, che includa il nodo head e nodi di calcolo, si consiglia di usare un metodo automatico, ad esempio lo [script di distribuzione IaaS di HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) oppure un modello di Resource Manager come il modello [cluster HPC Pack per carichi di lavoro di Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Per altri modelli, vedere [Opzioni per creare e gestire un cluster high performance computing (HPC) Windows in Azure con Microsoft HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).


## Considerazioni sulla pianificazione

Come illustrato nella figura riportata di seguito, il nodo head verrà distribuito in un dominio di Active Directory in una rete virtuale di Azure.

![Nodo head HPC Pack][headnode]

* **Dominio di Active Directory** - Il nodo head di HPC Pack deve essere aggiunto a un dominio di Active Directory in Azure prima dell'avvio dei servizi HPC nella VM. Come illustrato in questo articolo, per una distribuzione basata sul modello di verifica, è possibile alzare di livello la macchina virtuale creata per il nodo head a controller di dominio prima di avviare i servizi HPC. In alternativa è possibile distribuire un controller di dominio e una foresta separati in Azure a cui aggiungere la VM del nodo head.

* **Rete virtuale di Azure** - Come descritto in questo articolo, quando si distribuisce il nodo head di HPC Pack usando il modello di distribuzione Resource Manager nel portale di Azure, si specifica o crea una rete virtuale di Azure. La rete virtuale verrà usata in seguito per aggiungere VM dei nodi di calcolo del cluster al cluster HPC o nel caso in cui si aggiunga il nodo head a un dominio di Active Directory esistente.

    
## Passaggi per la creazione del nodo head

Di seguito sono indicati i principali passaggi per la creazione di una macchina virtuale di Azure per il nodo head HPC usando il modello di distribuzione di Gestione risorse nel portale di Azure.


1. Se si desidera creare una nuova foresta di Active Directory in Azure con VM di controller di dominio separate, è possibile usare un [modello di Resource Manager](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/). Per una semplice distribuzione con modello di verifica di HPC Pack, è possibile escluderlo. Al contrario, la VM del nodo head verrà configurata come controller di dominio come descritto più avanti in questo articolo.
    
2. Per creare il nodo head di HPC Pack nel [portale di Azure](https://portal.azure.com), selezionare l'immagine di HPC Pack 2012 R2 da Azure Marketplace. A tale scopo, fare clic su **Nuovo** e cercare **HPC Pack** nel Marketplace. Selezionare l'immagine **HPC Pack 2012 R2 on Windows Server 2012 R2**.

3. Nella pagina **HPC Pack 2012 R2 on Windows Server 2012 R2** (HPC Pack 2012 R2 su Windows Server 2012 R2) selezionare il modello di distribuzione **Resource Manager** e fare clic su **Crea**.

    ![Immagine di HPC Pack][marketplace]

4. Usare il portale per configurare le impostazioni e creare la macchina virtuale. Per informazioni dettagliate, seguire l'esercitazione [Creare la prima macchina virtuale Windows nel portale di Azure](virtual-machines-windows-hero-tutorial.md). Per una distribuzione con modello di verifica è in genere possibile accettare molte impostazioni predefinite o consigliate.

    **Considerazioni sulla rete virtuale**

   * Se si desidera aggiungere il nodo head a un dominio di Active Directory esistente in Azure, assicurarsi di specificare la rete virtuale per tale dominio quando si crea la VM del nodo head.
   
   * Se si desidera creare una nuova rete virtuale, in **Impostazioni** specificare un intervallo di indirizzi di rete privata, ad esempio 10.0.0.0/16, e un intervallo di indirizzi di subnet, ad esempio 10.0.0.0/24.
    
4. Dopo aver creato e avviato la VM, [connettersi alla VM](virtual-machines-windows-connect-logon.md) tramite Desktop remoto. 

5. Aggiungere la macchina virtuale a una foresta di domini esistente o creare una nuova foresta di domini nella macchina virtuale.

    * Se la VM è stata creata in una rete virtuale di Azure con una foresta di domini esistente, usare gli strumenti standard di Server Manager o Windows PowerShell per aggiungerla alla foresta di domini. Eseguire quindi il riavvio.

    * Se la VM è stata creata in una nuova rete virtuale senza una foresta di domini esistente, configurare la VM come controller di dominio. A tale scopo, usare gli strumenti standard di Server Manager o Windows PowerShell per installare e configurare il ruolo Servizi di dominio di Active Directory. Per informazioni dettagliate, vedere [Installare una nuova foresta di Active Directory di Windows Server 2012](https://technet.microsoft.com/library/jj574166.aspx).

5. Quando la macchina virtuale è in esecuzione e fa parte di una foresta Active Directory, avviare i servizi di HPC Pack nel nodo head. A tale scopo, effettuare l'operazione seguente:

    a. Connettersi alla macchina virtuale con un account di dominio membro del gruppo Administrators locale. Ad esempio, è possibile usare l'account di amministratore impostato al momento della creazione della VM del nodo head.

    b. Per usare una configurazione predefinita del nodo head, avviare Windows PowerShell come amministratore e digitare il comando seguente:

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Per l'avvio dei servizi HPC Pack potrebbero essere richiesti vari minuti.

    Per ulteriori opzioni per la configurazione del nodo head, digitare `get-help HPCHNPrepare.ps1`.


## Passaggi successivi

* È ora possibile interagire con il nodo head del cluster HPC Pack. Ad esempio, avviare Gestione cluster HPC e completare l'[elenco delle attività di distribuzione](https://technet.microsoft.com/library/jj884141.aspx).
* Aggiungere [nodi "burst" di Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md) in un servizio cloud per aumentare la capacità di calcolo del cluster su richiesta. 

* Provare a eseguire un carico di lavoro di test nel cluster. Per un esempio vedere la [guida introduttiva](https://technet.microsoft.com/library/jj884144) a HPC Pack.

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png

<!---HONumber=AcomDC_0525_2016-->