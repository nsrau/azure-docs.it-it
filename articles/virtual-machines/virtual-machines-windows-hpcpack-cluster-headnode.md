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
 ms.date="02/04/2016"
 ms.author="danlep"/>

# Creare il nodo head di un cluster HPC Pack in una macchina virtuale di Azure con un'immagine del Marketplace

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.


Questo articolo descrive come usare [l'immagine di macchina virtuale Microsoft HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) in Azure Marketplace per creare il nodo head di un cluster HPC usando il portale di Azure. L'immagine di macchina virtuale HPC Pack è basata su Windows Server 2012 R2 Datacenter con HPC Pack 2012 R2 Update 3 preinstallato. Usare questo nodo head per una distribuzione basata sul modello di verifica di HPC Pack in Azure. Sarà quindi possibile aggiungere risorse di calcolo al cluster per eseguire carichi di lavoro HPC.


![Nodo head HPC Pack][headnode]

>[AZURE.TIP]Per una distribuzione di produzione di un cluster HPC Pack completo in Azure, si consiglia di usare un metodo di distribuzione automatica, ad esempio lo [script di distribuzione IaaS di HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md), oppure un modello di Resource Manager come il modello [cluster HPC Pack per carichi di lavoro di Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Per altri modelli, vedere [Opzioni per creare e gestire un cluster HPC in Azure](virtual-machines-windows-hpcpack-cluster-options.md).


## Considerazioni sulla pianificazione

* **Dominio di Active Directory** - Il nodo head di HPC Pack deve essere aggiunto a un dominio di Active Directory in Azure prima dell'avvio dei servizi HPC nella macchina virtuale. Come illustrato in questo articolo, per una distribuzione basata sul modello di verifica, è possibile alzare di livello la macchina virtuale creata per il nodo head a controller di dominio prima di avviare i servizi HPC. In alternativa è possibile distribuire un controller di dominio e una foresta separati in Azure a cui aggiungere la macchina virtuale.

* **Rete virtuale di Azure** - Come descritto in questo articolo, quando si distribuisce il nodo head HPC Pack usando il modello di distribuzione di Gestione risorse nel portale di Azure, si specifica o crea una rete virtuale di Azure (VNet). La rete virtuale verrà usata in seguito per aggiungere macchine virtuali del nodo di calcolo del cluster al cluster HPC o nel caso in cui si aggiunga il nodo head a un dominio di Active Directory esistente.

    
## Passaggi per la creazione del nodo head

Di seguito sono indicati i principali passaggi per la creazione di una macchina virtuale di Azure per il nodo head HPC usando il modello di distribuzione di Gestione risorse nel portale di Azure.


1. Se si desidera creare una nuova foresta di Active Directory in Azure con macchine virtuali di controller di dominio separate, è possibile usare un [modello di Gestione risorse](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/). Per una semplice distribuzione basata sul modello di verifica, è possibile saltare questo passaggio e configurare la macchina virtuale del nodo head come controller di dominio come descritto più avanti.
    
2. Nel [portale di Azure](https://portal.azure.com) selezionare l'immagine di HPC Pack 2012 R2 da Azure Marketplace. A tale scopo, fare clic su **Nuovo** e cercare il Marketplace per **HPC Pack**. Selezionare **HPC Pack 2012 R2 on Windows Server 2012 R2**.

3. Nella pagina **HPC Pack 2012 R2 on Windows Server 2012 R2** selezionare il modello di distribuzione di **Gestione risorse** e fare clic su **Crea**.

    ![Immagine di HPC Pack][marketplace]

4. Usare il portale per configurare le impostazioni e creare la macchina virtuale. Per informazioni dettagliate, seguire l'esercitazione [Creare una macchina virtuale di Windows nel portale di Azure](virtual-machines-windows-hero-tutorial.md). Per una distribuzione iniziale è in genere possibile accettare molte impostazioni predefinite o consigliate.

    **Considerazioni su VNet**

   * Se si crea una nuova VNet in **Impostazioni**, specificare un intervallo di indirizzi di rete privata, ad esempio 10.0.0.0/16, e un intervallo di indirizzi di subnet, ad esempio 10.0.0.0/24.
    
4. Dopo aver creato e avviato la macchina virtuale, [connettersi alla macchina virtuale](virtual-machines-windows-connect-logon.md). 

5. Aggiungere la macchina virtuale a una foresta di domini esistente o creare una nuova foresta di domini nella macchina virtuale.

    **Considerazioni sui domini di Active Directory**

    * Se la macchina virtuale è stata creata in una rete virtuale di Azure con una foresta di domini esistente, usare gli strumenti standard di Server Manager o Windows PowerShell per aggiungerla alla foresta di domini. Eseguire quindi il riavvio.

    * Se la macchina virtuale è stata creata in una rete virtuale di Azure senza una foresta di domini esistente, configurare la macchina virtuale come controller di dominio. A tale scopo, usare gli strumenti standard di Server Manager o Windows PowerShell per installare e configurare il ruolo Servizi di dominio di Active Directory. Per informazioni dettagliate, vedere [Installare una nuova foresta di Active Directory di Windows Server 2012](https://technet.microsoft.com/library/jj574166.aspx).

5. Quando la macchina virtuale è in esecuzione e fa parte di una foresta Active Directory, avviare i servizi di HPC Pack nel nodo head. A tale scopo, effettuare l'operazione seguente:

    a. Connettersi alla macchina virtuale con un account di dominio membro del gruppo Administrators locale. Ad esempio, è possibile usare l'account di amministratore impostato al momento della creazione della macchina virtuale del nodo head.

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

<!---HONumber=AcomDC_0323_2016-->