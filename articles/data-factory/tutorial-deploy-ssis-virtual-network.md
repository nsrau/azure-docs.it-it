---
title: Esercitazione per configurare un runtime di integrazione Azure-SSIS per l'aggiunta a una rete virtualeTutorial to configure an Azure-SSIS integration runtime to join a virtual network
description: Informazioni su come aggiungere un runtime di integrazione Azure-SSIS a una rete virtuale di Azure.Learn how to join an Azure-SSIS integration runtime to an Azure virtual network.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 7470af23059ccd07e48050e6dc34521e299a986f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418610"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Configurare un runtime di integrazione (IR) di Azure-SQL Server Integration Services (SSIS) per l'aggiunta a una rete virtualeConfigure an Azure-SQL Server Integration Services (SSIS) integration runtime (IR) to join a virtual network

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questa esercitazione illustra i passaggi di base per usare il portale di Azure per configurare un runtime di integrazione (IR) di Azure-SQL Server Integration Services (SSIS) per partecipare a una rete virtuale.

I passaggi includono:

- Configurare una rete virtuale.
- Aggiungere il flusso di accesso a ir Azure-SSIS a una rete virtuale dal portale di Azure Data Factory.Join the Azure-SSIS IR to a virtual network from Azure Data Factory portal.

## <a name="prerequisites"></a>Prerequisiti

- Runtime di **integrazione Azure-SSIS**. Se non si dispone di un runtime di integrazione Azure-SSIS, eseguire il provisioning di un runtime di [integrazione Azure-SSIS in Azure Data Factory](tutorial-deploy-ssis-packages-azure.md) prima di iniziare.

- **Autorizzazione utente**. L'utente che crea il codice a raggi DiS Azure-SSIS deve disporre [dell'assegnazione](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope) di ruolo almeno nella risorsa Azure Data Factory con una delle opzioni seguenti:The user who creates the Azure-SSIS IR must have the role assignment at least on Azure Data Factory resource with one of the options below:

    - Usare il ruolo predefinito Collaboratore di rete. Questo ruolo richiede l'autorizzazione _Microsoft.Network/\*_, la quale ha un ambito molto maggiore del necessario.
    - Creare un ruolo personalizzato che include solo l'autorizzazione _Microsoft.Network/virtualNetworks/\*/join/action_ necessaria. Se si vuole anche portare i propri indirizzi IP pubblici per il provider di servizi di audio Azure-SSIS durante l'aggiunta a una rete virtuale di Azure Resource Manager, includere anche _Microsoft.Network/publicIPAddresses/'/join/action_ nel ruolo.

- **Rete virtuale**.

    - Se non si dispone di una rete virtuale, creare una rete virtuale usando il portale di Azure.If you do not have a virtual [network, create a virtual network using the Azure portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal).

    - Assicurarsi che il gruppo di risorse della rete virtuale possa creare ed eliminare determinate risorse di rete di Azure.Make sure that the virtual network's resource group can create and delete certain Azure network resources.
    
        Il runtime di integrazione Azure-SSIS deve creare alcune risorse di rete nello stesso gruppo di risorse della rete virtuale. Queste risorse includono:
        - Un servizio di bilanciamento del carico di Azure con il nome Guid>-azurebatch-cloudserviceloadbalancerAn Azure load balancer, with the name * \<Guid>-azurebatch-cloudserviceloadbalancer*
        - Un gruppo di sicurezza di\<rete, con il nome : Guid>-azurebatch-cloudservicenetworksecuritygroup
        - Un indirizzo IP pubblico di Azure, con il nome -azurebatch-cloudservicepublicip
    
        Tali risorse verranno create all'avvio del componente di ricreazione Azure-SSIS. Verranno eliminati quando il componente di ricreazione Azure-SSIS si arresta. Per evitare di bloccare l'arresto del componente di riproduzione Azure-SSIS, non riutilizzare queste risorse di rete nelle altre risorse.

    - Assicurarsi di non disporre di alcun [blocco delle risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) nel gruppo di risorse/sottoscrizione a cui appartiene la rete virtuale. Se si configura un blocco di sola lettura/eliminazione, l'avvio e l'arresto del componente di configurazione Azure-SSIS avranno esito negativo o il problema si blocca.

    - Assicurarsi di non disporre di criteri di Azure che impediscano la creazione delle risorse seguenti nel gruppo di risorse/sottoscrizione a cui appartiene la rete virtuale:Make sure that you don't have an Azure policy that prevents the following resources from being created under the resource group/subscription to which the virtual network belongs:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- Gli scenari di configurazione di rete riportati di seguito non vengono illustrati in questa esercitazione:Below **network configuration** scenarios are not covered in this tutorial:
    - Se si portano i propri indirizzi IP pubblici per il componente di accesso Azure-SSIS.
    - Se si utilizza il proprio server DNS (Domain Name System).
    - Se si usa un gruppo di sicurezza di rete (NSG) nella subnet.
    - Se si usa Azure ExpressRoute o una route definita dall'utente.
    - Se si usa il componente di riutilizzo personalizzato di Azure-SSIS.If you use customized Azure-SSIS IR.
    
    Per ulteriori informazioni, controllare la configurazione della [rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration).

## <a name="configure-a-virtual-network"></a>Configurare una rete virtuale

Usare il portale di Azure per configurare una rete virtuale prima di provare ad aggiungere un componente di riutilizzo Azure-SSIS.

1. Avviare Microsoft Edge o Google Chrome. Attualmente, solo questi Web browser supportano l'interfaccia utente di Data Factory.Currently, only these web browsers support the Data Factory UI.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Altri servizi**. Filtrare e selezionare **Reti virtuali**.

1. Filtrare e selezionare la propria rete virtuale dall'elenco.

1. Nella pagina **Rete virtuale** selezionare **Proprietà**.

1. Fare clic sul pulsante di copia per l'**ID RISORSA** per copiare l'ID risorsa per la rete virtuale negli Appunti. Salvare l'ID dagli Appunti in OneNote o in un file.

1. Nel menu a sinistra selezionare **Subnet.**

    - Verificare che la subnet selezionata disponga di spazio di indirizzi disponibile sufficiente per il componente di autorizzazione indirizzi Azure-SSIS da usare. Lasciare gli indirizzi IP disponibili per almeno due volte il numero del nodo IR. Azure riserva alcuni indirizzi IP all'interno di ogni subnet. Questi indirizzi non possono essere utilizzati. Il primo e l'ultimo indirizzo IP delle subnet sono riservati per la conformità del protocollo e altri tre indirizzi vengono usati per i servizi di Azure.The first and last IP addresses of the subnets are reserved for protocol conformance, and three more addresses are used for Azure services. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Non selezionare GatewaySubnet per distribuire un componente di accesso Azure-SSIS. È dedicato ai gateway di rete virtuale.
    - Non usare una subnet occupata esclusivamente da altri servizi di Azure, ad esempio l'istanza gestita del database SQL, il servizio app e così via.

1. Verificare che il provider di Azure Batch sia registrato nella sottoscrizione di Azure che ha la rete virtuale. In alternativa, registrare il provider batch di Azure.Or can register the Azure Batch provider. Se nella sottoscrizione è già presente un account Batch di Azure, la sottoscrizione è registrata per Azure Batch.If you already have an Azure Batch account in your subscription, your subscription is registered for Azure Batch. Se si crea il runtime di integrazione Azure-SSIS nel portale di Data Factory, il provider di Azure Batch viene registrato automaticamente.

   1. Nel portale di Azure scegliere **Sottoscrizioni**nel menu a sinistra.

   1. Selezionare la propria sottoscrizione.

   1. A sinistra, selezionare **Provider di risorse**e verificare che **Microsoft.Batch** sia un provider registrato.

   ![Conferma dello stato "Registrato"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se non viene visualizzato **Microsoft.Batch** nell'elenco, per registrarlo [creare un account Azure Batch vuoto](../batch/batch-account-create-portal.md) nella propria sottoscrizione. È possibile eliminarlo in un secondo momento.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Aggiungere il runtime di integrazione Azure-SSIS alla rete virtuale

Dopo aver configurato la rete virtuale di Azure Resource Manager o la rete virtuale classica, è possibile aggiungere il provider di disponibilità di Azure-SSIS alla rete virtuale:After you've configured your Azure Resource Manager virtual network or classic virtual network, you can join the Azure-SSIS IR to the virtual network:

1. Avviare Microsoft Edge o Google Chrome. Attualmente, solo questi Web browser supportano l'interfaccia utente di Data Factory.Currently, only these web browsers support the Data Factory UI.

1. Nel [portale di Azure](https://portal.azure.com), nel menu a sinistra, selezionare **Data factory**. Se non è visualizzato **Data factory** nel menu, selezionare **Altri servizi**, quindi nella sezione INTELLIGENCE e **ANALYTICS** selezionare **Data factory**.

   ![Elenco di data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selezionare la data factory con il raggio di ir Azure-SSIS nell'elenco. Verrà visualizzata la home page della data factory. Selezionare il riquadro **Monitor & autore.** Verrà visualizzata l'interfaccia utente di Data Factory in una scheda separata.

   ![Home page di Data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Nell'interfaccia utente di Data Factory passare alla scheda **Modifica**, selezionare **Connessioni** e passare alla scheda **Runtime di integrazione**.

   ![Scheda "Runtime di integrazione"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Se il runtime di integrazione Azure-SSIS è in esecuzione, nella colonna **Azioni** dell'elenco **Runtime** di integrazione selezionare il pulsante **Interrompi** per il runtime di integrazione Azure-SSIS. Non è possibile modificare il componente di ri-acqua Azure-SSIS finché non viene arrestato.

   ![Arrestare il runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Nella colonna **Azioni** dell'elenco **Runtime** di integrazione selezionare il pulsante **Modifica** per il runtime di integrazione Azure-SSIS.

   ![Modificare il runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Nel pannello di installazione del runtime di integrazione, scorrere le sezioni **Impostazioni generali** e **Impostazioni SQL** selezionando il pulsante **Avanti.**

1. Nella sezione **Impostazioni avanzate:**
   1. Selezionare la casella di controllo Selezionare una rete virtuale per il runtime di **integrazione Azure-SSIS, consentire ad ADF di creare determinate risorse di rete e, facoltativamente, portare i propri indirizzi IP pubblici statici.**

   1. In **Sottoscrizione** selezionare la sottoscrizione di Azure che dispone della rete virtuale.

   1. Per **Località** viene selezionata la stessa località del runtime di integrazione.

   1. Per Tipo selezionare il tipo di rete virtuale: classica o Azure Resource Manager.For **Type**, select the type of your virtual network: classic or Azure Resource Manager. È consigliabile selezionare una rete virtuale di Azure Resource Manager, perché le reti virtuali classiche saranno presto deprecate.

   1. Per **Nome della rete virtuale** selezionare il nome della rete virtuale. Deve essere lo stesso usato per il server di database SQL di Azure con endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato per ospitare SSISDB. Oppure dovrebbe essere lo stesso connesso alla rete locale. In caso contrario, può essere qualsiasi rete virtuale per portare i propri indirizzi IP pubblici statici per il codice IR Azure-SSIS.

   1. Per **Nome subnet** selezionare il nome della subnet nella rete virtuale. Deve essere lo stesso usato per il server di database SQL di Azure con endpoint del servizio di rete virtuale per ospitare SSISDB. In alternativa, deve essere una subnet diversa da quella usata per l'istanza gestita con endpoint privato per ospitare SSISDB. In caso contrario, può essere qualsiasi subnet per portare i propri indirizzi IP pubblici statici per il codice IR Azure-SSIS.

   1. Selezionare **Convalida rete virtuale**. Se la convalida ha esito positivo, selezionare **Continua**.

   ![Impostazioni avanzate con una rete virtuale](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Nella sezione **Riepilogo** esaminare tutte le impostazioni per il componente di ricambio Azure-SSIS. Quindi selezionare **Aggiorna**.

1. Avviare il componente di ri-azione Azure-SSIS selezionando il pulsante **Start** nella colonna **Azioni** per il componente di ri-acqua Azure-SSIS. L'avvio del componente di accesso Azure-SSIS che si unisce a una rete virtuale richiede da 20 a 30 minuti.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni [sull'aggiunta del componente di accesso di Azure-SSIS a una rete virtuale.](join-azure-ssis-integration-runtime-virtual-network.md)
