---
title: Esercitazione per configurare un runtime di integrazione SSIS di Azure per l'aggiunta a una rete virtuale
description: Informazioni su come aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale di Azure.
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
ms.openlocfilehash: 0b83049e154afc48334cc6deb576c700ed71d844
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84118151"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Configurare un runtime di integrazione di Azure-SQL Server Integration Services (SSIS) per l'aggiunta a una rete virtuale

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questa esercitazione illustra i passaggi di base per l'uso del portale di Azure per configurare un runtime di integrazione di Azure-SQL Server Integration Services (SSIS) per l'aggiunta a una rete virtuale.

I passaggi includono:

- Configurare una rete virtuale.
- Aggiungere la Azure-SSIS IR a una rete virtuale dal portale di Azure Data Factory.

## <a name="prerequisites"></a>Prerequisiti

- **Runtime di integrazione SSIS di Azure**. Se non si dispone di un runtime di integrazione SSIS di Azure, effettuare il [provisioning di un runtime di integrazione SSIS di Azure in Azure Data Factory prima di](tutorial-deploy-ssis-packages-azure.md) iniziare.

- **Autorizzazione utente**. Per l'utente che crea l'istanza di Azure-SSIS IR è necessaria un'[assegnazione di ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope) almeno nella risorsa Azure Data Factory, con una delle opzioni descritte di seguito.

    - Usare il ruolo predefinito Collaboratore Rete. Questo ruolo richiede l'autorizzazione _Microsoft.Network/\*_ , la quale ha un ambito molto maggiore del necessario.
    - Creare un ruolo personalizzato che include solo l'autorizzazione _Microsoft.Network/virtualNetworks/\*/join/action_ necessaria. Se si vuole anche usare indirizzi IP pubblici per Azure-SSIS IR durante l'aggiunta a una rete virtuale Azure Resource Manager, includere anche l'autorizzazione _Microsoft. Network/publicIPAddresses/*/join/Action_ per il ruolo.

- **Rete virtuale**.

    - Se non si dispone di una rete virtuale, [creare una rete virtuale usando il portale di Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal).

    - Verificare che il gruppo di risorse della rete virtuale possa creare ed eliminare determinate risorse di rete di Azure.
    
        Il runtime di integrazione Azure-SSIS deve creare alcune risorse di rete nello stesso gruppo di risorse della rete virtuale. Tali risorse includono:
        - Un servizio di bilanciamento del carico di Azure, con nome * \<Guid> -azurebatch-cloudserviceloadbalancer*
        - Un gruppo di sicurezza di rete, denominato * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - Un indirizzo IP pubblico di Azure denominato -azurebatch-cloudservicepublicip
    
        Queste risorse verranno create all'avvio di Azure-SSIS IR e verranno eliminate all'arresto di Azure-SSIS IR. Per evitare di impedire l'arresto di Azure-SSIS IR, non riusare queste risorse di rete in altre risorse.

    - Verificare che non sia presente alcun [blocco delle risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) per il gruppo di risorse o la sottoscrizione a cui appartiene la rete virtuale. Se si configura un blocco di sola lettura/eliminazione, l'avvio e l'arresto di Azure-SSIS IR avranno esito negativo o si bloccheranno.

    - Assicurarsi che non si disponga di un'assegnazione di criteri di Azure che impedisce la creazione delle seguenti risorse nel gruppo di risorse/sottoscrizione a cui appartiene la rete virtuale:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- Gli scenari di **configurazione di rete** seguenti non sono trattati in questa esercitazione:
    - Se si importano indirizzi IP pubblici personalizzati per la Azure-SSIS IR.
    - Se si usa il proprio server di Domain Name System (DNS).
    - Se si usa un gruppo di sicurezza di rete (NSG) nella subnet.
    - Se si usa Azure ExpressRoute o una route definita dall'utente (UDR).
    - Se si usano Azure-SSIS IR personalizzati.
    
    Per ulteriori informazioni, controllare la [configurazione della rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration).

## <a name="configure-a-virtual-network"></a>Configurare una rete virtuale

Usare il portale di Azure per configurare una rete virtuale prima di provare a unirla a un Azure-SSIS IR.

1. Avviare Microsoft Edge o Google Chrome. Attualmente, solo questi Web browser supportano l'interfaccia utente di Data Factory.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **altri servizi**. Filtrare e selezionare **Reti virtuali**.

1. Filtrare e selezionare la propria rete virtuale dall'elenco.

1. Nella pagina **Rete virtuale** selezionare **Proprietà**.

1. Fare clic sul pulsante di copia per l'**ID RISORSA** per copiare l'ID risorsa per la rete virtuale negli Appunti. Salvare l'ID dagli Appunti in OneNote o in un file.

1. Nel menu a sinistra selezionare **subnet**.

    - Assicurarsi che la subnet selezionata disponga di spazio di indirizzi disponibile sufficiente per l'utilizzo da parte del Azure-SSIS IR. Lasciare gli indirizzi IP disponibili almeno due volte il numero del nodo IR. Azure riserva alcuni indirizzi IP all'interno di ogni subnet. Non è possibile usare questi indirizzi. Il primo e l'ultimo indirizzo IP delle subnet sono riservati per la conformità al protocollo e vengono usati altri tre indirizzi per i servizi di Azure. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Non selezionare il GatewaySubnet per distribuire un Azure-SSIS IR. È dedicata ai gateway di rete virtuale.
    - Non usare una subnet occupata esclusivamente da altri servizi di Azure, ad esempio SQL database SQL Istanza gestita, servizio app e così via.

1. Verificare che il provider di Azure Batch sia registrato nella sottoscrizione di Azure che ha la rete virtuale. In alternativa, registrare il provider di Azure Batch. Se si dispone già di un account Azure Batch nella sottoscrizione, la sottoscrizione viene registrata per l'Azure Batch. Se si crea il runtime di integrazione Azure-SSIS nel portale di Data Factory, il provider di Azure Batch viene registrato automaticamente.

   1. Nel portale di Azure, nel menu a sinistra, selezionare **sottoscrizioni**.

   1. Selezionare la propria sottoscrizione.

   1. A sinistra selezionare provider di **risorse**e verificare che **Microsoft.Batch** sia un provider registrato.

   ![Conferma dello stato "Registrato"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se non viene visualizzato **Microsoft.Batch** nell'elenco, per registrarlo [creare un account Azure Batch vuoto](../batch/batch-account-create-portal.md) nella propria sottoscrizione. È possibile eliminarlo in un secondo momento.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Aggiungere il runtime di integrazione Azure-SSIS alla rete virtuale

Dopo aver configurato la rete virtuale Azure Resource Manager o la rete virtuale classica, è possibile aggiungere la Azure-SSIS IR alla rete virtuale:

1. Avviare Microsoft Edge o Google Chrome. Attualmente, solo questi Web browser supportano l'interfaccia utente di Data Factory.

1. Nel [portale di Azure](https://portal.azure.com)scegliere **Data Factory**dal menu a sinistra. Se non viene visualizzato **Data Factory** nel menu, selezionare **altri servizi**e quindi nella sezione Intelligence e **analisi** selezionare **Data Factory**.

   ![Elenco di data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selezionare il data factory con il Azure-SSIS IR nell'elenco. Verrà visualizzata la home page della data factory. Selezionare il riquadro **autore & monitoraggio** . Verrà visualizzata l'interfaccia utente di Data Factory in una scheda separata.

   ![Home page di Data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Nell'interfaccia utente di Data Factory passare alla scheda **Modifica**, selezionare **Connessioni** e passare alla scheda **Runtime di integrazione**.

   ![Scheda "Runtime di integrazione"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Se il Azure-SSIS IR è in esecuzione, nell'elenco **runtime di integrazione** , nella colonna **azioni** , selezionare il pulsante **Interrompi** per il Azure-SSIS IR. Non è possibile modificare la Azure-SSIS IR finché non viene arrestata.

   ![Arrestare il runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Nell'elenco **runtime di integrazione** , nella colonna **azioni** , selezionare il pulsante **modifica** per il Azure-SSIS IR.

   ![Modificare il runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Nel pannello di installazione di Integration runtime passare alle sezioni **Impostazioni generali** e **Impostazioni SQL** selezionando il pulsante **Avanti** .

1. Nella sezione **Impostazioni avanzate** :
   1. Selezionare la casella di controllo **selezionare un VNet per il Azure-SSIS Integration Runtime da aggiungere, consentire ad ADF di creare determinate risorse di rete e, facoltativamente, di importare indirizzi IP pubblici statici** .

   1. In **Sottoscrizione** selezionare la sottoscrizione di Azure che dispone della rete virtuale.

   1. Per **Località** viene selezionata la stessa località del runtime di integrazione.

   1. Per **tipo**selezionare il tipo di rete virtuale: classico o Azure Resource Manager. Si consiglia di selezionare un Azure Resource Manager rete virtuale, perché le reti virtuali classiche saranno presto deprecate.

   1. Per **Nome della rete virtuale** selezionare il nome della rete virtuale. Deve essere uguale a quello usato per il database SQL con gli endpoint del servizio di rete virtuale o SQL Istanza gestita con endpoint privato per ospitare SSISDB. Oppure deve essere lo stesso connesso alla rete locale. In caso contrario, può trattarsi di qualsiasi rete virtuale per portare gli indirizzi IP pubblici statici per Azure-SSIS IR.

   1. Per **Nome subnet** selezionare il nome della subnet nella rete virtuale. Deve corrispondere a quello usato per SQL datbase con gli endpoint del servizio rete virtuale per ospitare SSISDB. Oppure deve essere una subnet diversa da quella usata per il Istanza gestita SQL con endpoint privato per ospitare SSISDB. In caso contrario, può essere una qualsiasi subnet per portare gli indirizzi IP pubblici statici per Azure-SSIS IR.

   1. Selezionare **convalida VNet**. Se la convalida ha esito positivo, selezionare **continua**.

   ![Impostazioni avanzate con una rete virtuale](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Nella sezione **Riepilogo** esaminare tutte le impostazioni per il Azure-SSIS IR. Quindi selezionare **Aggiorna**.

1. Avviare il Azure-SSIS IR selezionando il pulsante **Avvia** nella colonna **azioni** per la Azure-SSIS IR. Sono necessari da 20 a 30 minuti per avviare la Azure-SSIS IR che si aggiunge a una rete virtuale.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull' [aggiunta di Azure-SSIS IR a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md).
