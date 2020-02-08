---
title: Creare un'area di lavoro Azure Databricks nella Guida introduttiva alla rete virtuale
description: Questo articolo descrive come distribuire Azure Databricks alla rete virtuale.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 046b2da7cdb966192e485ff9f5510eb63c9e0008
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086557"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>Guida introduttiva: creare un'area di lavoro Azure Databricks nella propria rete virtuale

La distribuzione predefinita di Azure Databricks crea una nuova rete virtuale gestita da databricks. Questa Guida introduttiva illustra come creare un'area di lavoro Azure Databricks nella propria rete virtuale. Si crea anche un cluster Apache Spark all'interno di tale area di lavoro. 

Per altre informazioni sui motivi per cui è possibile scegliere di creare un'area di lavoro Azure Databricks nella propria rete virtuale, vedere [distribuire Azure Databricks nella rete virtuale di Azure (VNet Injection)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/databricks/).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

> [!Note]
> Questa esercitazione non può essere eseguita usando una **sottoscrizione di valutazione gratuita di Azure**.
> Se l'utente ha un account gratuito, andare al proprio profilo e modificare la sottoscrizione a **con pagamento in base al consumo**. Per altre informazioni, vedere [Account gratuito di Azure](https://azure.microsoft.com/free/). Quindi [rimuovere il limite di spesa](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit) e [richiedere un aumento della quota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) per le vCPU nell'area dell'utente. Quando si crea l'area di lavoro Azure Databricks, è possibile selezionare il piano tariffario **Versione di valutazione (Premium - Unità Databricks gratuite per 14 giorni)** per concedere l'accesso gratuito Premium per 14 giorni dell'area di lavoro alle Unità Databricks di Azure.

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Nel menu del portale di Azure selezionare **Crea una risorsa**. Selezionare quindi rete **> rete virtuale**.

    ![Creare una rete virtuale in portale di Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. In **Crea rete virtuale**applicare le impostazioni seguenti: 

    |Impostazione|Valore suggerito|Descrizione|
    |-------|---------------|-----------|
    |Sottoscrizione|\<Sottoscrizione in uso\>|Selezionare la sottoscrizione di Azure da usare.|
    |Gruppo di risorse|databricks-avvio rapido|Selezionare **Crea nuovo** e immettere un nuovo nome per il gruppo di risorse per l'account.|
    |Name|databricks-avvio rapido|Selezionare un nome per la rete virtuale.|
    |Region|\<Selezionare l'area più vicina agli utenti\>|Selezionare una posizione geografica in cui è possibile ospitare la rete virtuale. Usare la località più vicina agli utenti.|

    ![Nozioni di base per una rete virtuale su portale di Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Selezionare **Avanti: indirizzi IP >** e applicare le impostazioni seguenti. Quindi selezionare **Verifica + crea**.
    
    |Impostazione|Valore suggerito|Descrizione|
    |-------|---------------|-----------|
    |Spazio indirizzi IPv4|10.2.0.0/16|Intervallo di indirizzi della rete virtuale nella notazione CIDR. L'intervallo CIDR deve essere compreso tra/16 e/24|
    |Nome della subnet|predefiniti|Selezionare un nome per la subnet predefinita nella rete virtuale.|
    |Intervallo di indirizzi subnet|10.2.0.0/24|Intervallo di indirizzi della subnet nella notazione CIDR. Deve essere contenuto nello spazio degli indirizzi della rete virtuale. Non è possibile modificare l'intervallo di indirizzi di una subnet in uso.|

    ![Impostare le configurazioni IP per una rete virtuale in portale di Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-ip-config.png)

4. Nella scheda **Verifica e crea** selezionare **Crea** per distribuire la rete virtuale. Al termine della distribuzione, passare alla rete virtuale e selezionare **spazio indirizzi** in **Impostazioni**. Nella casella *aggiungere un intervallo di indirizzi aggiuntivo*, inserire `10.179.0.0/16` e selezionare **Salva**.

    ![Spazio di indirizzi della rete virtuale di Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Creare un'area di lavoro di Azure Databricks

1. Nel menu del portale di Azure selezionare **Crea una risorsa**. Quindi selezionare **Analytics > databricks**.

    ![Creare un'area di lavoro Azure Databricks in portale di Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. In **Azure Databricks servizio**applicare le impostazioni seguenti:

    |Impostazione|Valore suggerito|Descrizione|
    |-------|---------------|-----------|
    |Nome dell'area di lavoro|databricks-avvio rapido|Selezionare un nome per l'area di lavoro Azure Databricks.|
    |Sottoscrizione|\<Sottoscrizione in uso\>|Selezionare la sottoscrizione di Azure da usare.|
    |Gruppo di risorse|databricks-avvio rapido|Selezionare lo stesso gruppo di risorse usato per la rete virtuale.|
    |Location|\<Selezionare l'area più vicina agli utenti\>|Scegliere lo stesso percorso della rete virtuale.|
    |Piano tariffario|Scegliere tra standard o Premium.|Per altre informazioni sui piani tariffari, vedere la [pagina dei prezzi di databricks](https://azure.microsoft.com/pricing/details/databricks/).|

    ![Creare una Azure Databricks di base dell'area di lavoro](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Al termine dell'immissione delle impostazioni nella pagina **nozioni di base** , fare clic su **Next: Networking >** e applicare le impostazioni seguenti:

    |Impostazione|Valore suggerito|Descrizione|
    |-------|---------------|-----------|
    |Distribuire Azure Databricks area di lavoro nella rete virtuale (VNet)|Sì|Questa impostazione consente di distribuire un'area di lavoro Azure Databricks nella rete virtuale.|
    |Rete virtuale|databricks-avvio rapido|Selezionare la rete virtuale creata nella sezione precedente.|
    |Nome della subnet pubblica|subnet pubblica|Usare il nome predefinito della subnet pubblica.|
    |Intervallo CIDR della subnet pubblica|10.179.64.0/18|Utilizzare un intervallo CIDR fino a e includendo/26.|
    |Nome subnet privata|subnet privata|Usare il nome della subnet privata predefinita.|
    |Intervallo CIDR della subnet privata|10.179.0.0/18|Utilizzare un intervallo CIDR fino a e includendo/26.|

    ![Aggiungere le informazioni VNet all'area di lavoro Azure Databricks in portale di Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-vnet-config.png)

3. Al termine della distribuzione, passare alla risorsa Azure Databricks. Si noti che il peering di rete virtuale è disabilitato. Si noti anche il gruppo di risorse e il gruppo di risorse gestite nella pagina panoramica. 

    ![Panoramica di Azure Databricks in portale di Azure](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    Il gruppo di risorse gestite non è modificabile e non viene usato per creare macchine virtuali. È possibile creare solo macchine virtuali nel gruppo di risorse gestito.

    ![Azure Databricks gruppo di risorse gestite](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Creare un cluster

> [!NOTE]
> Per usare un account gratuito per creare il cluster Azure Databricks, prima di creare il cluster, passare al profilo personale e impostare la sottoscrizione per il **pagamento in base al consumo**. Per altre informazioni, vedere [Account gratuito di Azure](https://azure.microsoft.com/free/).

1. Tornare al servizio Azure Databricks e selezionare **Avvia area di lavoro** nella pagina **Panoramica** .

2. Selezionare **cluster** >  **+ Crea cluster**. Creare quindi un nome di cluster, ad esempio *databricks-QuickStart-cluster*, e accettare le impostazioni predefinite rimanenti. Selezionare **Crea cluster**.

    ![Crea cluster Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Quando il cluster è in esecuzione, tornare al gruppo di risorse gestite nel portale di Azure. Si notino le nuove macchine virtuali, i dischi, l'indirizzo IP e le interfacce di rete. Viene creata un'interfaccia di rete in ogni subnet pubblica e privata con indirizzi IP.  

    ![Azure Databricks gruppo di risorse gestito dopo la creazione del cluster](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Tornare all'area di lavoro Azure Databricks e selezionare il cluster creato. Passare quindi alla scheda **eXecutors (esecutori** ) nella pagina dell' **interfaccia utente di Spark** . Si noti che gli indirizzi del driver e degli esecutori si trovano nell'intervallo della subnet privata. In questo esempio, il driver è 10.179.0.6 ed eXecutors sono 10.179.0.4 e 10.179.0.5. Gli indirizzi IP potrebbero essere diversi.

    ![Azure Databricks Executor dell'interfaccia utente Spark](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver finito l'articolo è possibile terminare il cluster. A questo scopo, nel riquadro sinistro dell'area di lavoro di Azure Databricks fare clic su **Clusters** (Cluster). Per il cluster che si vuole terminare, posizionare il cursore sui puntini di sospensione sotto la colonna **Actions** (Azioni) e fare clic sull'icona **Terminate** (Termina). Questo arresta il cluster.

Se non viene terminato manualmente, il cluster si arresterà automaticamente se è stata selezionata la casella di controllo **Terminate after \_\_ minutes of inactivity** (Termina dopo \_\_ minuti di attività) durante la creazione del cluster. In tal caso, il cluster viene automaticamente arrestato se è rimasto inattivo per il tempo specificato.

Se non si vuole riusare il cluster, è possibile eliminare il gruppo di risorse creato nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un cluster Spark in Azure Databricks distribuito in una rete virtuale. Passare all'articolo successivo per informazioni su come eseguire una query su un contenitore Docker di SQL Server Linux nella rete virtuale usando JDBC da un notebook di Azure Databricks.  

> [!div class="nextstepaction"]
>[Eseguire query su un contenitore Docker SQL Server Linux in una rete virtuale da un notebook di Azure Databricks](vnet-injection-sql-server.md)
