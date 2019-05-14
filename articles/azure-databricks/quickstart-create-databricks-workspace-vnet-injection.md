---
title: Creare un'area di lavoro Azure Databricks in una rete virtuale
description: Questo articolo descrive come distribuire Azure Databricks per la rete virtuale.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 295b64b10f9f78ca6224d60fb84c6d1310aaa42e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770607"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>Avvio rapido: Creare un'area di lavoro Azure Databricks in una rete virtuale

Questa Guida introduttiva illustra come creare un'area di lavoro Azure Databricks in una rete virtuale. Inoltre, si creerà un cluster Apache Spark all'interno di tale area di lavoro.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Nel portale di Azure, selezionare **crea una risorsa** > **Networking** > **rete virtuale**.

2. Sotto **crea rete virtuale**, applicare le impostazioni seguenti: 

    |Impostazione|Valore consigliato|DESCRIZIONE|
    |-------|---------------|-----------|
    |NOME|databricks-quickstart|Selezionare un nome per la rete virtuale.|
    |Spazio degli indirizzi|10.1.0.0/16|Intervallo di indirizzi della rete virtuale nella notazione CIDR.|
    |Sottoscrizione|\<Sottoscrizione in uso\>|Selezionare la sottoscrizione di Azure da usare.|
    |Gruppo di risorse|databricks-quickstart|Selezionare **Crea nuovo** e immettere un nuovo nome gruppo di risorse per l'account.|
    |Località|\<Selezionare l'area più vicina agli utenti\>|Selezionare una località geografica in cui è possibile ospitare la rete virtuale. Usare la località più vicina agli utenti.|
    |Nome della subnet|default|Selezionare un nome per la subnet predefinita nella rete virtuale.|
    |Intervallo di indirizzi subnet|10.1.0.0/24|Intervallo di indirizzi della subnet nella notazione CIDR. Deve essere contenuto nello spazio indirizzi della rete virtuale. Non è possibile modificare l'intervallo di indirizzi di una subnet è in uso.|

    ![Creare una rete virtuale nel portale di Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Una volta completata la distribuzione, passare alla rete virtuale e selezionare **spazio indirizzi** sotto **impostazioni**. Nella casella di testo *Aggiungi intervallo di indirizzi*, inserire `10.179.0.0/16` e selezionare **salvare**.

    ![Spazio degli indirizzi di rete virtuale di Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Creare un'area di lavoro di Azure Databricks

1. Nel portale di Azure, selezionare **crea una risorsa** > **Analitica** > **Databricks**.

2. Sotto **Azure Databricks Service**, applicare le impostazioni seguenti:

    |Impostazione|Valore consigliato|DESCRIZIONE|
    |-------|---------------|-----------|
    |Nome dell'area di lavoro|databricks-quickstart|Selezionare un nome per l'area di lavoro Azure Databricks.|
    |Sottoscrizione|\<Sottoscrizione in uso\>|Selezionare la sottoscrizione di Azure da usare.|
    |Gruppo di risorse|databricks-quickstart|Selezionare lo stesso gruppo di risorse usato per la rete virtuale.|
    |Località|\<Selezionare l'area più vicina agli utenti\>|Scegliere la stessa località della rete virtuale.|
    |Piano tariffario|Scegliere tra Standard o Premium.|Per altre informazioni sui piani tariffari, vedere la [pagina dei prezzi di Databricks](https://azure.microsoft.com/pricing/details/databricks/).|
    |Distribuire l'area di lavoro di Azure Databricks in una rete virtuale|Sì|Questa impostazione consente di distribuire un'area di lavoro Azure Databricks in una rete virtuale.|
    |Rete virtuale|databricks-quickstart|Selezionare la rete virtuale creata nella sezione precedente.|
    |Nome Subnet pubblica|public-subnet|Usare il nome di subnet public di predefinita.|
    |Intervallo CIDR della Subnet Public|10.179.64.0/18|Intervallo CIDR per la subnet deve essere compreso tra /18 e /26.|
    |Nome della Subnet privata|private-subnet|Usare il nome della subnet privata predefinita.|
    |Intervallo CIDR della Subnet privata|10.179.0.0/18|Intervallo CIDR per la subnet deve essere compreso tra /18 e /26.|

    ![Creare un'area di lavoro Azure Databricks nel portale di Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Una volta completata la distribuzione, passare alla risorsa di Azure Databricks. Si noti che è disabilitato il peering di rete virtuale. Si noti anche che il gruppo di risorse e il gruppo di risorse gestite nella pagina di panoramica. 

    ![Panoramica di Azure Databricks nel portale di Azure](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    Il gruppo di risorse gestite contiene il percorso fisico dell'account di archiviazione (DBFS), ruolo di lavoro-sg (gruppo di sicurezza di rete,) i ruoli di lavoro-rete virtuale (rete virtuale). È anche il percorso in cui verranno creati le macchine virtuali, dischi, indirizzo IP e l'interfaccia di rete. Questo gruppo di risorse è limitato per impostazione predefinita. Tuttavia quando si avvia un cluster nella rete virtuale, un'interfaccia di rete viene creata tra le reti virtuali i ruoli di lavoro nel gruppo di risorse gestite e la rete virtuale "hub".

    ![Gruppo di risorse gestite di Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Creare un cluster

> [!NOTE]
> Per usare un account gratuito per creare il cluster Azure Databricks, prima di creare il cluster, passare al profilo personale e impostare la sottoscrizione per il **pagamento in base al consumo**. Per altre informazioni, vedere [Account gratuito di Azure](https://azure.microsoft.com/free/).

1. Tornare al servizio Azure Databricks e selezionare **avvia area di lavoro** nel **Panoramica** pagina.

2. Selezionare **cluster** > **+ crea Cluster**. Creare quindi un nome del cluster, ad esempio *databricks-Guida introduttiva-cluster*e accettare le impostazioni predefinite rimanenti. Selezionare **Crea cluster**.

    ![Creare cluster Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Quando il cluster è in esecuzione, tornare al gruppo di risorse gestite nel portale di Azure. Si noti che le nuove macchine virtuali, dischi, indirizzo IP e le interfacce di rete. Un'interfaccia di rete viene creata in ogni subnet con indirizzi IP pubbliche e private.  

    ![Gruppo di risorse gestite di Azure Databricks dopo la creazione del cluster](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Tornare all'area di lavoro Azure Databricks e selezionare il cluster creato. Quindi passare al **executor** scheda le **interfaccia utente di Spark** pagina. Si noti che gli indirizzi per il driver ed executor sono compresi nell'intervallo di subnet privata. In questo esempio, il driver è 10.179.0.6 e gli executor sono 10.179.0.4 e 10.179.0.5. Gli indirizzi IP potrebbero essere diversi.

    ![Executor dell'interfaccia utente di Databricks Spark di Azure](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver finito l'articolo è possibile terminare il cluster. A questo scopo, nel riquadro sinistro dell'area di lavoro di Azure Databricks fare clic su **Clusters** (Cluster). Per il cluster che si vuole terminare, posizionare il cursore sui puntini di sospensione sotto la colonna **Actions** (Azioni) e fare clic sull'icona **Terminate** (Termina). Questo arresta il cluster.

Se non viene terminato manualmente, il cluster si arresterà automaticamente se è stata selezionata la casella di controllo **Terminate after \_\_ minutes of inactivity** (Termina dopo \_\_ minuti di attività) durante la creazione del cluster. In tal caso, il cluster viene automaticamente arrestato se è rimasto inattivo per il tempo specificato.

Se non si desidera riutilizzare il cluster, è possibile eliminare il gruppo di risorse creato nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è creato un cluster Spark in Azure Databricks che è stato distribuito a una rete virtuale. Passare all'articolo successivo per informazioni su come eseguire una query di un contenitore Linux Docker di SQL Server nella rete virtuale usando JDBC da un notebook di Azure Databricks.  

> [!div class="nextstepaction"]
>[Eseguire una query un contenitore Linux Docker di SQL Server in una rete virtuale da un notebook di Azure Databricks](vnet-injection-sql-server.md)
