---
title: Creare un'area di lavoro di Azure Databricks nella guida introduttiva della rete virtualeCreate an Azure Databricks workspace in your own Virtual Network quickstart
description: Questo articolo descrive come distribuire Azure Databricks nella rete virtuale.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 63dd1b4d9396d340dd17a7afb92ff9c38a2b38b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132667"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>Guida introduttiva: Creare un'area di lavoro di Azure Databricks nella propria rete virtuale

La distribuzione predefinita di Azure Databricks crea una nuova rete virtuale gestita da Databricks.The default deployment of Azure Databricks creates a new virtual network that is managed by Databricks. Questa guida introduttiva illustra invece come creare un'area di lavoro di Azure Databricks nella propria rete virtuale. È inoltre possibile creare un cluster Apache Spark all'interno di tale area di lavoro. 

Per altre informazioni sui motivi per cui è possibile scegliere di creare un'area di lavoro di Azure Databricks nella propria rete virtuale, vedere [Distribuire Azure Databricks nella rete virtuale di Azure (Iniezione di vNet).](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

Se non si dispone di una sottoscrizione di Azure, creare un [account gratuito.](https://azure.microsoft.com/free/databricks/)

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale](https://portal.azure.com/)di Azure .

> [!Note]
> Questa esercitazione non può essere eseguita usando una **sottoscrizione di valutazione gratuita di Azure**.
> Se l'utente ha un account gratuito, andare al proprio profilo e modificare la sottoscrizione a **con pagamento in base al consumo**. Per altre informazioni, vedere [Account gratuito di Azure](https://azure.microsoft.com/free/). Quindi [rimuovere il limite di spesa](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit) e [richiedere un aumento della quota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) per le vCPU nell'area dell'utente. Quando si crea l'area di lavoro Azure Databricks, è possibile selezionare il piano tariffario **Versione di valutazione (Premium - Unità Databricks gratuite per 14 giorni)** per concedere l'accesso gratuito Premium per 14 giorni dell'area di lavoro alle Unità Databricks di Azure.

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Nel menu del portale di Azure selezionare **Crea una risorsa**. Selezionare **quindi Rete > rete virtuale**.

    ![Creare una rete virtuale nel portale di AzureCreate a virtual network on Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. In **Crea rete virtuale**applicare le impostazioni seguenti: 

    |Impostazione|Valore consigliato|Descrizione|
    |-------|---------------|-----------|
    |Subscription|\<Sottoscrizione in uso\>|Selezionare la sottoscrizione di Azure da usare.|
    |Resource group|databricks-quickstart|Selezionare **Crea nuovo** e immettere un nuovo nome per l'account.|
    |Nome|databricks-quickstart|Selezionare un nome per la rete virtuale.|
    |Region|\<Selezionare l'area più vicina agli utenti\>|Selezionare una posizione geografica in cui è possibile ospitare la rete virtuale. Usare la località più vicina agli utenti.|

    ![Nozioni di base per una rete virtuale nel portale di AzureBasics for a virtual network on Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Selezionare **Avanti: Indirizzi IP >** e applicare le impostazioni seguenti. Quindi selezionare **Revisione e creazione**.
    
    |Impostazione|Valore consigliato|Descrizione|
    |-------|---------------|-----------|
    |Spazio di indirizzi IPv4|10.2.0.0/16|Intervallo di indirizzi della rete virtuale in notazione CIDR. L'intervallo CIDR deve essere compreso tra /16 e /24|
    |Nome della subnet|default|Selezionare un nome per la subnet predefinita nella rete virtuale.|
    |Intervallo di indirizzi subnet|10.2.0.0/24|Intervallo di indirizzi della subnet nella notazione CIDR. Deve essere contenuto nello spazio degli indirizzi della rete virtuale. L'intervallo di indirizzi di una subnet in uso non può essere modificato.|

    ![Impostare le configurazioni IP per una rete virtuale nel portale di AzureSet IP configurations for a virtual network on Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-ip-config.png)

4. Nella scheda **Revisione e creazione** selezionare **Crea** per distribuire la rete virtuale. Al termine della distribuzione, passare alla rete virtuale e selezionare **Spazio indirizzi** in **Impostazioni**. Nella casella *Aggiungi ulteriore intervallo*di indirizzi, inserire `10.179.0.0/16` e selezionare **Salva**.

    ![Spazio di indirizzi della rete virtuale di AzureAzure virtual network address space](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Creare un'area di lavoro di Azure Databricks

1. Nel menu del portale di Azure selezionare **Crea una risorsa**. Selezionare **quindi Analytics > Databricks**.

    ![Creare un'area di lavoro di Azure Databricks nel portale di AzureCreate an Azure Databricks workspace on Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. In Servizio Azure Databricks applicare le impostazioni seguenti:Under **Azure Databricks Service**, apply the following settings:

    |Impostazione|Valore consigliato|Descrizione|
    |-------|---------------|-----------|
    |Nome dell'area di lavoro|databricks-quickstart|Selezionare un nome per l'area di lavoro di Azure Databricks.Select a name for your Azure Databricks workspace.|
    |Subscription|\<Sottoscrizione in uso\>|Selezionare la sottoscrizione di Azure da usare.|
    |Resource group|databricks-quickstart|Selezionare lo stesso gruppo di risorse usato per la rete virtuale.|
    |Location|\<Selezionare l'area più vicina agli utenti\>|Scegliere lo stesso percorso della rete virtuale.|
    |Piano tariffario|Scegliere tra Standard e Premium.|Per ulteriori informazioni sui piani tariffari, vedere la [pagina dei prezzi di Databricks](https://azure.microsoft.com/pricing/details/databricks/).|

    ![Creare un'area di lavoro di Azure DatabricksCreate an Azure Databricks workspace basics](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Dopo aver immesso le impostazioni nella pagina **Nozioni di base,** selezionare **Avanti: Networking >** e applicare le impostazioni seguenti:

    |Impostazione|Valore consigliato|Descrizione|
    |-------|---------------|-----------|
    |Distribuire l'area di lavoro di Azure Databricks nella rete virtuale (VNet)Deploy Azure Databricks workspace in your Virtual Network (VNet)|Sì|Questa impostazione consente di distribuire un'area di lavoro di Azure Databricks nella rete virtuale.|
    |Rete virtuale|databricks-quickstart|Selezionare la rete virtuale creata nella sezione precedente.|
    |Nome subnet pubblica|subnet pubblica|Utilizzare il nome della subnet pubblica predefinita.|
    |Intervallo CIDR subnet pubblica|10.179.64.0/18|Utilizzare un intervallo CIDR fino a /26 incluso.|
    |Nome subnet privata|subnet privata|Utilizzare il nome della subnet privata predefinita.|
    |Intervallo CIDR subnet privata|10.179.0.0/18|Utilizzare un intervallo CIDR fino a /26 incluso.|

    ![Aggiungere informazioni sulla rete virtuale all'area di lavoro di Azure Databricks nel portale di AzureAdd VNet information to Azure Databricks workspace on Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-vnet-config.png)

3. Al termine della distribuzione, passare alla risorsa Azure Databricks.Once the deployment is complete, navigate to the Azure Databricks resource. Si noti che il peering della rete virtuale è disabilitato. Si noti inoltre il gruppo di risorse e il gruppo di risorse gestite nella pagina di panoramica. 

    ![Panoramica di Azure Databricks nel portale di AzureAzure Databricks overview in Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    Il gruppo di risorse gestite non è modificabile e non viene utilizzato per creare macchine virtuali. È possibile creare macchine virtuali solo nel gruppo di risorse gestito.

    ![Gruppo di risorse gestite di Azure DatabricksAzure Databricks managed resource group](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

    Quando una distribuzione dell'area di lavoro non riesce, l'area di lavoro viene comunque creata in uno stato di errore. Eliminare l'area di lavoro non riuscita e creare una nuova area di lavoro che risolva gli errori di distribuzione. Quando si elimina l'area di lavoro non riuscita, vengono eliminati anche il gruppo di risorse gestite e tutte le risorse distribuite correttamente.

## <a name="create-a-cluster"></a>Creare un cluster

> [!NOTE]
> Per usare un account gratuito per creare il cluster Azure Databricks, prima di creare il cluster, passare al profilo personale e impostare la sottoscrizione per il **pagamento in base al consumo**. Per altre informazioni, vedere [Account gratuito di Azure](https://azure.microsoft.com/free/).

1. Tornare al servizio Azure Databricks e selezionare **Avvia area di lavoro** nella pagina **Panoramica.Return** to your Azure Databricks service and select Launch Workspace on the Overview page.

2. Selezionare **Clusters** > **- Create Cluster**. Creare quindi un nome di cluster, ad esempio *databricks-quickstart-cluster*, e accettare le restanti impostazioni predefinite. Selezionare **Crea cluster**.

    ![Creare un cluster di Azure DatabricksCreate Azure Databricks cluster](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Dopo aver eseguito il cluster, tornare al gruppo di risorse gestite nel portale di Azure.Once the cluster is running, return to the managed resource group in the Azure portal. Si notino le nuove macchine virtuali, i dischi, l'indirizzo IP e le interfacce di rete. Viene creata un'interfaccia di rete in ognuna delle subnet pubbliche e private con indirizzi IP.  

    ![Gruppo di risorse gestite azure Databricks dopo la creazione del clusterAzure Databricks managed resource group after cluster creation](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Tornare all'area di lavoro di Azure Databricks e selezionare il cluster creato. Passare quindi alla scheda **Esecutori** nella pagina **dell'interfaccia utente** di Spark.Then navigate to the Executors tab on the Spark UI page. Si noti che gli indirizzi per il driver e gli esecutori sono nell'intervallo di subnet privata. In questo esempio, il driver è 10.179.0.6 e gli esecutori sono 10.179.0.4 e 10.179.0.5. I tuoi indirizzi IP potrebbero essere diversi.

    ![Esecutori dell'interfaccia utente di Azure Databricks SparkAzure Databricks Spark UI executors](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver finito l'articolo è possibile terminare il cluster. A questo scopo, nel riquadro sinistro dell'area di lavoro di Azure Databricks fare clic su **Clusters** (Cluster). Per il cluster che si vuole terminare, posizionare il cursore sui puntini di sospensione sotto la colonna **Actions** (Azioni) e fare clic sull'icona **Terminate** (Termina). In questo modo il cluster viene arrestato.

Se il cluster non viene terminato manualmente, verrà arrestato automaticamente, a condizione che sia stata selezionata la casella di controllo **Termina dopo \_ \_ minuti di inattività** durante la creazione del cluster. In tal caso, il cluster viene automaticamente arrestato se è rimasto inattivo per il tempo specificato.

Se non si vuole riutilizzare il cluster, è possibile eliminare il gruppo di risorse creato nel portale di Azure.If you do not wish to reuse the cluster, you can delete the resource group you created in the Azure portal.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un cluster Spark in Azure Databricks distribuito in una rete virtuale. Passare all'articolo successivo per informazioni su come eseguire una query su un contenitore Docker Linux di SQL Server nella rete virtuale usando JDBC da un blocco appunti di Azure Databricks.Advance to the next article to learn how to query a SQL Server Linux Docker container in the virtual network using JDBC from an Azure Databricks notebook.  

> [!div class="nextstepaction"]
>[Eseguire query su un contenitore Docker Linux di SQL Server in una rete virtuale da un blocco appunti di Azure DatabricksQuery a SQL Server Linux Docker container in a virtual network from an Azure Databricks notebook](vnet-injection-sql-server.md)
