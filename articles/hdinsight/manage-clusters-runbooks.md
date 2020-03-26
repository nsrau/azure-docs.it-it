---
title: 'Esercitazione: Usare i runbook di Automazione di Azure per creare cluster - Azure HDInsight'
description: Informazioni su come creare ed eliminare i cluster Azure HDInsight con script in esecuzione nel cloud usando i runbook di Automazione di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 05c0aaf6cc33442fa4f36eb38eb0d6d593fc6c1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75553121"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Esercitazione: Creare cluster Azure HDInsight con Automazione di Azure

Automazione di Azure consente di creare script da eseguire nel cloud e di gestire le risorse di Azure su richiesta o in base a una pianificazione. Questo articolo descrive come creare runbook di PowerShell per creare ed eliminare i cluster Azure HDInsight.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Installare i moduli necessari per interagire con HDInsight.
> * Creare e archiviare le credenziali necessarie durante la creazione del cluster.
> * Creare un nuovo runbook di Automazione di Azure per creare un cluster HDInsight.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Un [account di Automazione di Azure](../automation/automation-quickstart-create-account.md) esistente.
* Un [account di archiviazione di Azure](../storage/common/storage-account-create.md) esistente, che verrà usato come risorsa di archiviazione del cluster.

## <a name="install-hdinsight-modules"></a>Installare i moduli di HDInsight

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare gli account di Automazione di Azure.
1. Selezionare **Raccolta moduli** in **Risorse condivise**.
1. Digitare **AzureRM.Profile** nella casella e premere INVIO per eseguire la ricerca. Selezionare il risultato della ricerca restituito.
1. Nella schermata **AzureRM.profile** selezionare **Importa**. Selezionare la casella per aggiornare i moduli di Azure e quindi selezionare **OK**.

    ![importare il modulo AzureRM.profile](./media/manage-clusters-runbooks/import-azurermprofile-module.png)

1. Tornare alla raccolta moduli selezionando **Raccolta moduli** in **Risorse condivise**.
1. Digitare **HDInsight**. Selezionare **AzureRM.HDInsight**.

    ![esplorare i moduli di HDInsight](./media/manage-clusters-runbooks/browse-modules-hdinsight.png)

1. Nel pannello **AzureRM.HDInsight** selezionare **Importa** e fare clic su **OK**.

    ![importare il modulo AzureRM.HDInsight](./media/manage-clusters-runbooks/import-azurermhdinsight-module.png)

## <a name="create-credentials"></a>Creare le credenziali

1. In **Risorse condivise** selezionare **Credenziali**.
1. Selezionare **Aggiungi credenziali**.
1. Immettere le informazioni necessarie nel pannello **Nuove credenziali**. Queste credenziali consentono di archiviare la password del cluster, per consentire l'accesso ad Ambari.

    | Proprietà | valore |
    | --- | --- |
    | Nome | `cluster-password` |
    | Nome utente | `admin` |
    | Password | `SECURE_PASSWORD` |
    | Conferma password | `SECURE_PASSWORD` |

1. Selezionare **Create** (Crea).
1. Ripetere lo stesso processo per una nuova credenziale `ssh-password` con il nome utente `sshuser` e una password di propria scelta. Selezionare **Create** (Crea). Questa credenziale consente di archiviare la password SSH per il cluster.

    ![creare le credenziali](./media/manage-clusters-runbooks/create-credentials.png)

## <a name="create-a-runbook-to-create-a-cluster"></a>Creare un runbook per creare un cluster

1. In **Automazione processi** selezionare **Runbook**.
1. Selezionare **Crea un runbook**.
1. Nel pannello **Crea un runbook** specificare un nome per il runbook, ad esempio `hdinsight-cluster-create`. Selezionare **Powershell** dall'elenco a discesa **Tipo di runbook**.
1. Selezionare **Create** (Crea).

    ![creare il runbook](./media/manage-clusters-runbooks/create-runbook.png)

1. Immettere il codice seguente nella schermata **Modifica runbook di PowerShell** e selezionare **Pubblica**:

    ![pubblica il runbook](./media/manage-clusters-runbooks/publish-runbook.png)

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>Creare un runbook per eliminare un cluster

1. In **Automazione processi** selezionare **Runbook**.
1. Selezionare **Crea un runbook**.
1. Nel pannello **Crea un runbook** specificare un nome per il runbook, ad esempio `hdinsight-cluster-delete`. Selezionare **Powershell** dall'elenco a discesa **Tipo di runbook**.
1. Selezionare **Create** (Crea).
1. Immettere il codice seguente nella schermata **Modifica runbook di PowerShell** e selezionare **Pubblica**:

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>Eseguire i runbook

### <a name="create-a-cluster"></a>Creare un cluster

1. Per visualizzare l'elenco dei runbook per l'account di Automazione, selezionare**Runbook** in **Automazione processi**.
1. Selezionare `hdinsight-cluster-create` o il nome usato durante la creazione del runbook di creazione del cluster.
1. Selezionare **Avvia** per eseguire immediatamente il runbook. È anche possibile pianificare i runbook per l'esecuzione periodica. Vedere [Pianificazione di un runbook in Automazione di Azure](../automation/shared-resources/schedules.md)
1. Immettere i parametri richiesti per lo script e selezionare **OK**. Verrà creato un nuovo cluster HDInsight con il nome specificato nel parametro **CLUSTERNAME**.

    ![eseguire il runbook di creazione cluster](./media/manage-clusters-runbooks/execute-create-runbook.png)

### <a name="delete-a-cluster"></a>Eliminazione di un cluster

Per eliminare il cluster, selezionare il runbook `hdinsight-cluster-delete` creato. Selezionare **Avvia**, immettere il parametro **CLUSTERNAME** e fare clic su **OK**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare l'account di Automazione di Azure creato per evitare addebiti imprevisti. A tale scopo, passare al portale di Azure, selezionare il gruppo di risorse in cui è stato creato l'account di Automazione di Azure, selezionare l'account di Automazione e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire cluster Apache Hadoop in HDInsight usando Azure PowerShell](hdinsight-administer-use-powershell.md)