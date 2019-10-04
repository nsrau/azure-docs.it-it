---
title: Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale | Microsoft Docs
description: Informazioni su come aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale di Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 92687b7cb8cdad8612f5a44833efcca351c45a43
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114749"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale
Quando si usa SQL Server Integration Services (SSIS) in Azure Data Factory, è necessario aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale di Azure negli scenari seguenti: 

- Si vuole connettersi a archivi dati locali da pacchetti SSIS eseguiti nel Azure-SSIS IR senza configurare o gestire un runtime di integrazione self-hosted come proxy. 

- Si vuole connettersi alle risorse dei servizi di Azure supportate con gli endpoint di servizio della rete virtuale dai pacchetti SSIS eseguiti nel Azure-SSIS IR.

- Si sta ospitando un database del catalogo SSIS (SSISDB) nel database SQL di Azure con gli endpoint del servizio di rete virtuale o l'istanza gestita in una rete virtuale. 

Data Factory consente di aggiungere i Azure-SSIS IR a una rete virtuale creata tramite il modello di distribuzione classica o il modello di distribuzione di Azure Resource Manager. 

> [!IMPORTANT]
> La rete virtuale classica verrà deprecata. utilizzare invece la rete virtuale Azure Resource Manager.  Se si usa già la rete virtuale classica, passare alla rete virtuale Azure Resource Manager il prima possibile.

## <a name="access-to-on-premises-data-stores"></a>Accesso agli archivi dati locali
Se i pacchetti SSIS accedono agli archivi dati locali, è possibile aggiungere il Azure-SSIS IR a una rete virtuale connessa alla rete locale. In alternativa, è possibile configurare o gestire un runtime di integrazione self-hosted come proxy per la Azure-SSIS IR. Per altre informazioni, vedere [configurare un runtime di integrazione self-hosted come proxy per un Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

Quando si aggiunge il Azure-SSIS IR a una rete virtuale, tenere presenti le considerazioni seguenti: 

- Se nessuna rete virtuale è connessa alla rete locale, creare prima di tutto una [rete virtuale Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) per l'aggiunta del Azure-SSIS IR. Configurare quindi una connessione [gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) da sito a sito o una connessione [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) da tale rete virtuale alla rete locale. 

- Se una rete virtuale Azure Resource Manager è già connessa alla rete locale nello stesso percorso del Azure-SSIS IR, è possibile aggiungere il runtime di integrazione alla rete virtuale. 

- Se una rete virtuale classica è già connessa alla rete locale in un percorso diverso rispetto alla Azure-SSIS IR, è possibile creare una [rete virtuale Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) per l'aggiunta di Azure-SSIS IR. Configurare quindi una connessione [da rete virtuale classica a rete virtuale di Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 
 
- Se una rete virtuale Azure Resource Manager è già connessa alla rete locale in un percorso diverso rispetto al Azure-SSIS IR, è possibile creare prima di tutto una [rete virtuale Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) per aggiungere il Azure-SSIS IR. Configurare quindi una connessione di rete virtuale da Azure Resource Manager a Azure Resource Manager. 

## <a name="access-to-azure-services"></a>Accesso ai servizi di Azure
Se i pacchetti SSIS accedono alle risorse del servizio di Azure supportate con gli [endpoint del servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) e si vuole proteggere tali risorse per Azure-SSIS IR, è possibile aggiungere i Azure-SSIS IR alla subnet della rete virtuale configurata con la rete virtuale endpoint di servizio. Nel frattempo, aggiungere una regola della rete virtuale alle risorse del servizio di Azure per consentire l'accesso dalla stessa subnet.

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hosting del catalogo SSIS nel database SQL
Se si ospita il catalogo SSIS nel database SQL di Azure con gli endpoint servizio di rete virtuale, assicurarsi di aggiungere il runtime di integrazione Azure-SSIS alla stessa rete virtuale e subnet.

Per aggiungere la Azure-SSIS IR alla stessa rete virtuale dell'istanza gestita, verificare che il Azure-SSIS IR si trovi in una subnet diversa da quella dell'istanza gestita. Per aggiungere la Azure-SSIS IR a una rete virtuale diversa da quella dell'istanza gestita, è consigliabile usare il peering di rete virtuale (che è limitato alla stessa area) o una connessione dalla rete virtuale alla rete virtuale. Per altre informazioni, vedere [connettere l'applicazione a istanza gestita di database SQL di Azure](../sql-database/sql-database-managed-instance-connect-app.md).

In tutti i casi, la rete virtuale può essere distribuita solo tramite il modello di distribuzione Azure Resource Manager.

Per altre informazioni, vedere le sezioni seguenti. 

## <a name="virtual-network-configuration"></a>Configurazione della rete virtuale

Configurare la rete virtuale in modo che soddisfi questi requisiti: 

-   Assicurarsi che `Microsoft.Batch` sia un provider registrato nella sottoscrizione della subnet della rete virtuale che ospita il Azure-SSIS IR. Se si usa una rete virtuale classica, aggiungere `MicrosoftAzureBatch` anche al ruolo Collaboratore macchina virtuale classica per la rete virtuale. 

-   Assicurarsi di avere le autorizzazioni necessarie. Per altre informazioni, vedere [configurare le autorizzazioni](#perms).

-   Selezionare la subnet appropriata per ospitare il runtime di integrazione Azure-SSIS. Per ulteriori informazioni, vedere [selezionare la subnet](#subnet). 

-   Se si usa il proprio server di Domain Name System (DNS) nella rete virtuale, vedere [configurare il server DNS](#dns_server). 

-   Se si usa un gruppo di sicurezza di rete (NSG) nella subnet, vedere la pagina relativa alla [configurazione di un NSG](#nsg). 

-   Se si usa Azure ExpressRoute o una route definita dall'utente (UDR), vedere [usare Azure ExpressRoute o un UdR](#route). 

-   Verificare che il gruppo di risorse della rete virtuale sia in grado di creare ed eliminare determinate risorse di rete di Azure. Per altre informazioni, vedere [configurare il gruppo di risorse](#resource-group). 

-   Se si Personalizza il Azure-SSIS IR come descritto in [installazione personalizzata per Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup), i nodi di Azure-SSIS IR otterranno indirizzi IP privati da un intervallo predefinito di 172.16.0.0 a 172.31.255.255. Assicurarsi quindi che gli intervalli di indirizzi IP privati delle reti virtuali o locali non entrino in conflitto con questo intervallo.

Questo diagramma mostra le connessioni necessarie per il Azure-SSIS IR:

![Runtime di integrazione Azure-SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>Configurare le autorizzazioni

L'utente che crea il Azure-SSIS IR deve disporre delle autorizzazioni seguenti:

- Se si sta aggiungendo il runtime di integrazione SSIS a una rete virtuale di Azure Resource Manager, sono disponibili due opzioni:

  - Usare il ruolo predefinito collaboratore rete. Questo ruolo richiede l'autorizzazione _Microsoft.Network/\*_ , la quale ha un ambito molto maggiore del necessario.

  - Creare un ruolo personalizzato che include solo l'autorizzazione _Microsoft.Network/virtualNetworks/\*/join/action_ necessaria. 

- Se si aggiunge il runtime di integrazione SSIS a una rete virtuale classica, è consigliabile usare il ruolo di collaboratore macchina virtuale classico incorporato. In caso contrario, è necessario definire un ruolo personalizzato che include l'autorizzazione per accedere alla rete virtuale.

### <a name="subnet"></a> Selezionare la subnet

Quando si sceglie una subnet: 

-   Non selezionare il GatewaySubnet per distribuire un Azure-SSIS IR. È dedicata ai gateway di rete virtuale. 

-   Assicurarsi che la subnet selezionata disponga di spazio di indirizzi disponibile sufficiente per l'utilizzo da parte del Azure-SSIS IR. Lasciare gli indirizzi IP disponibili almeno due volte il numero del nodo IR. Azure riserva alcuni indirizzi IP all'interno di ogni subnet. Non è possibile usare questi indirizzi. Il primo e l'ultimo indirizzo IP delle subnet sono riservati per la conformità al protocollo e vengono usati altri tre indirizzi per i servizi di Azure. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

-   Non usare una subnet occupata esclusivamente da altri servizi di Azure, ad esempio istanza gestita di database SQL, servizio app e così via. 

### <a name="dns_server"></a>Configurare il server DNS 
Se è necessario usare il proprio server DNS in una rete virtuale unita dall'Azure-SSIS IR, assicurarsi che sia in grado di risolvere i nomi host di Azure globali, ad esempio un BLOB di archiviazione `<your storage account>.blob.core.windows.net`di Azure denominato. 

Sono consigliati i passaggi seguenti: 

-   Configurare il DNS personalizzato per l'invio di richieste al servizio DNS di Azure. È possibile inviare i record DNS non risolti all'indirizzo IP dei resolver ricorsivi di Azure (168.63.129.16) nel server DNS. 

-   Configurare il DNS personalizzato come server DNS primario per la rete virtuale. Configurare DNS di Azure come server DNS secondario. Registrare l'indirizzo IP dei resolver ricorsivi di Azure (168.63.129.16) come server DNS secondario nel caso in cui il server DNS non sia disponibile. 

Per altre informazioni, vedere [risoluzione dei nomi che usa il proprio server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a>Configurare un NSG
Se è necessario implementare un NSG per la subnet usata dal Azure-SSIS IR, consentire il traffico in ingresso e in uscita attraverso le porte seguenti: 

| Direction | Protocollo di trasporto | Source | Intervallo di porte di origine | Destination | Destination port range | Commenti |
|---|---|---|---|---|---|---|
| In ingresso | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (se si aggiunge il runtime di integrazione a una rete virtuale Gestione risorse) <br/><br/>10100, 20100, 30100 (se si aggiunge il runtime di integrazione a una rete virtuale classica)| Il servizio Data Factory usa queste porte per comunicare con i nodi del Azure-SSIS IR nella rete virtuale. <br/><br/> Indipendentemente dalla creazione di un NSG a livello di subnet, Data Factory configura sempre un NSG al livello delle schede di interfaccia di rete collegate alle macchine virtuali che ospitano il Azure-SSIS IR. Il gruppo di sicurezza di rete a livello di scheda di interfaccia di rete consente solo il traffico in entrata dagli indirizzi IP di Data Factory nelle porte specificate. Anche se si aprono queste porte al traffico Internet a livello di subnet, il traffico da indirizzi IP che non sono Data Factory indirizzi IP viene bloccato a livello di NIC. |
| In uscita | TCP | VirtualNetwork | * | AzureCloud | 443 | I nodi del Azure-SSIS IR nella rete virtuale usano questa porta per accedere ai servizi di Azure, ad esempio archiviazione di Azure e hub eventi di Azure. |
| In uscita | TCP | VirtualNetwork | * | Internet | 80 | I nodi del Azure-SSIS IR nella rete virtuale usano questa porta per scaricare un elenco di revoche di certificati da Internet. |
| In uscita | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | I nodi del Azure-SSIS IR nella rete virtuale usano queste porte per accedere a un SSISDB ospitato dal server del database SQL. Se il criterio di connessione del server di database SQL è impostato su **proxy** anziché su **Reindirizzamento**, è necessaria solo la porta 1433. Questa regola di sicurezza in uscita non è applicabile a un SSISDB ospitato dall'istanza gestita nella rete virtuale. |
||||||||

### <a name="route"></a>Usare Azure ExpressRoute o un UDR
Quando si connette un circuito [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) all'infrastruttura di rete virtuale per estendere la rete locale ad Azure, una configurazione comune usa il tunneling forzato (annunciando una route BGP, 0.0.0.0/0, alla rete virtuale). Questo tunneling forza il traffico Internet in uscita dal flusso della rete virtuale a un'appliance di rete locale per l'ispezione e la registrazione. 
 
In alternativa, è possibile definire [UdR](../virtual-network/virtual-networks-udr-overview.md) per forzare il traffico Internet in uscita dalla subnet che ospita il Azure-SSIS IR a un'altra subnet che ospita un'appliance virtuale di rete come firewall o firewall di Azure per l'ispezione e la registrazione. 

In entrambi i casi, la route di traffico interrompe la connettività in ingresso richiesta da servizi Azure Data Factory dipendenti (in particolare Azure Batch servizi di gestione) al Azure-SSIS IR nella rete virtuale. Per evitare questo problema, definire uno o più UdR sulla subnet che contiene il Azure-SSIS IR. 

È possibile applicare una route 0.0.0.0/0 con il tipo di hop successivo come **Internet** nella subnet che ospita il Azure-SSIS IR in uno scenario di Azure ExpressRoute. In alternativa, è possibile modificare la Route 0.0.0.0/0 esistente dal tipo di hop successivo come appliance virtuale a Internet in uno scenario di **appliance virtuale** di **rete** .

![Aggiungere una route](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Se si è preoccupati di perdere la possibilità di controllare il traffico Internet in uscita da tale subnet, è possibile definire UdR specifici per instradare il traffico solo tra i servizi Azure Batch Management e i Azure-SSIS IR con un tipo di hop successivo **Internet**.

Se, ad esempio, il Azure-SSIS IR si trova `UK South`in, si otterrebbe un elenco di intervalli IP di tag `BatchNodeManagement.UKSouth` del servizio dal [collegamento di download dell'intervallo IP dei tag del servizio](https://www.microsoft.com/en-us/download/details.aspx?id=56519) o tramite l'API di individuazione dei tag di [servizio](https://aka.ms/discoveryapi). Applicare quindi le seguenti UdR di route di intervallo IP correlate con il tipo di hop successivo come **Internet**.

![Impostazioni Azure Batch UDR](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Questo approccio comporta un costo aggiuntivo per la manutenzione. Controllare regolarmente l'intervallo di indirizzi IP e aggiungere nuovi intervalli IP nella UDR per evitare di suddividere il Azure-SSIS IR. Si consiglia di controllare l'intervallo di indirizzi IP mensilmente perché quando il nuovo IP viene visualizzato nel tag del servizio, l'indirizzo IP verrà applicato un altro mese. 

### <a name="resource-group"></a>Configurare il gruppo di risorse
Il runtime di integrazione Azure-SSIS deve creare alcune risorse di rete nello stesso gruppo di risorse della rete virtuale. Queste risorse includono:
   -   Un servizio di bilanciamento del carico di Azure, con il nome  *\<GUID >-azurebatch-cloudserviceloadbalancer*.
   -   Un indirizzo IP pubblico di Azure, con il nome  *\<GUID >-azurebatch-cloudservicepublicip*.
   -   Un gruppo di sicurezza del lavoro di rete, con il nome  *\<GUID >-azurebatch-cloudservicenetworksecuritygroup*. 

Queste risorse verranno create all'avvio del runtime di integrazione. Verranno eliminati al termine del runtime di integrazione. Per evitare di bloccare l'arresto del runtime di integrazione, non riutilizzare queste risorse di rete nelle altre risorse. 

Assicurarsi di non avere un blocco di risorsa per il gruppo di risorse o la sottoscrizione a cui appartiene la rete virtuale. Se si configura un blocco di sola lettura o un blocco di eliminazione, l'avvio e l'arresto del runtime di integrazione potrebbero avere esito negativo o l'IR potrebbe smettere di rispondere. 

Assicurarsi di non avere un criterio di Azure che impedisce la creazione delle seguenti risorse nel gruppo di risorse o nella sottoscrizione a cui appartiene la rete virtuale: 
   -   Microsoft.Network/LoadBalancers 
   -   Microsoft.Network/NetworkSecurityGroups 
   -   Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a> Domande frequenti

- Come è possibile proteggere l'indirizzo IP pubblico esposto nel Azure-SSIS IR per la connessione in ingresso? È possibile rimuovere l'indirizzo IP pubblico?
 
    Al momento, viene creato automaticamente un indirizzo IP pubblico quando la Azure-SSIS IR viene aggiunta alla rete virtuale. Si dispone di un NSG a livello di scheda di interfaccia di rete per consentire l'accesso in ingresso solo ai servizi di gestione Azure Batch per la connessione al Azure-SSIS IR. È anche possibile specificare un NSG a livello di subnet per la protezione in ingresso.

    Se non si vuole che l'indirizzo IP pubblico venga esposto, considerare la possibilità di configurare il runtime di integrazione [self-hosted come proxy per la Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) anziché per la rete virtuale, se applicabile allo scenario.
 
- È possibile aggiungere l'indirizzo IP statico della Azure-SSIS IR all'elenco Consenti del firewall per l'origine dati?
 
    - Se l'origine dati è locale, dopo aver connesso la rete virtuale alla rete locale e aver aggiunto il Azure-SSIS IR alla subnet della rete virtuale, è possibile aggiungere l'intervallo di indirizzi IP della subnet all'elenco Consenti.
    - Se l'origine dati è un servizio di Azure supportato con un endpoint del servizio di rete virtuale, è possibile configurare un punto di servizio della rete virtuale nella rete virtuale e aggiungere il Azure-SSIS IR alla subnet della rete virtuale. È quindi possibile consentire l'accesso usando la regola della rete virtuale dei servizi di Azure anziché l'intervallo di indirizzi IP.
    - Se l'origine dati è un tipo diverso di origine dati cloud, è possibile usare UDR per instradare il traffico in uscita dal Azure-SSIS IR all'appliance virtuale di sistema o al firewall di Azure usando un indirizzo IP pubblico statico. È possibile aggiungere l'indirizzo IP pubblico dell'appliance virtuale di Azure o del firewall di Azure all'elenco Consenti.
    - Se le risposte precedenti non soddisfano le proprie esigenze, è consigliabile fornire l'accesso all'origine dati configurando un runtime di integrazione [self-hosted come proxy per la Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Quindi, è possibile aggiungere l'indirizzo IP del computer che ospita il runtime di integrazione self-hosted all'elenco Consenti anziché unire il Azure-SSIS IR nella rete virtuale.

## <a name="azure-portal-data-factory-ui"></a>Portale di Azure (interfaccia utente di Data Factory)
Questa sezione illustra come aggiungere un Azure-SSIS IR esistente a una rete virtuale (classica o Azure Resource Manager) usando l'interfaccia utente di portale di Azure e Data Factory. 

Prima di aggiungersi al Azure-SSIS IR alla rete virtuale, è necessario configurare correttamente la rete virtuale. Attenersi alla procedura illustrata nella sezione relativa al tipo di rete virtuale (classica o Azure Resource Manager). Seguire quindi i passaggi della terza sezione per aggiungere la Azure-SSIS IR alla rete virtuale. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Configurare una rete virtuale Azure Resource Manager

Usare il portale per configurare una rete virtuale Azure Resource Manager prima di provare a unirla a un Azure-SSIS IR.

1. Avviare Microsoft Edge o Google Chrome. Attualmente, solo questi Web browser supportano l'interfaccia utente di Data Factory. 

1. Accedere al [portale di Azure](https://portal.azure.com). 

1. Selezionare **Altri servizi**. Filtrare e selezionare **Reti virtuali**. 

1. Filtrare e selezionare la propria rete virtuale dall'elenco. 

1. Nella pagina **Rete virtuale** selezionare **Proprietà**. 

1. Fare clic sul pulsante di copia per l'**ID RISORSA** per copiare l'ID risorsa per la rete virtuale negli Appunti. Salvare l'ID dagli Appunti in OneNote o in un file. 

1. Nel menu a sinistra selezionare **subnet**. Verificare che il numero di indirizzi disponibili sia maggiore dei nodi del Azure-SSIS IR. 

1. Verificare che il provider di Azure Batch sia registrato nella sottoscrizione di Azure che ha la rete virtuale. In alternativa, registrare il provider di Azure Batch. Se si dispone già di un account Azure Batch nella sottoscrizione, la sottoscrizione viene registrata per l'Azure Batch. Se si crea il runtime di integrazione Azure-SSIS nel portale di Data Factory, il provider di Azure Batch viene registrato automaticamente. 

   a. Nel portale di Azure, nel menu a sinistra, selezionare **sottoscrizioni**. 

   b. Selezionare la propria sottoscrizione. 

   c. A sinistra selezionare provider di **risorse**e verificare che **Microsoft. batch** sia un provider registrato. 

   ![Conferma dello stato "Registrato"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se non viene visualizzato **Microsoft.Batch** nell'elenco, per registrarlo [creare un account Azure Batch vuoto](../batch/batch-account-create-portal.md) nella propria sottoscrizione. È possibile eliminarlo in un secondo momento. 

### <a name="configure-a-classic-virtual-network"></a>Configurare una rete virtuale classica
Usare il portale per configurare una rete virtuale classica prima di provare ad aggiungervi un Azure-SSIS IR. 

1. Avviare Microsoft Edge o Google Chrome. Attualmente, solo questi Web browser supportano l'interfaccia utente di Data Factory. 

1. Accedere al [portale di Azure](https://portal.azure.com). 

1. Selezionare **Altri servizi**. Filtrare e selezionare **Reti virtuali (classiche)** . 

1. Filtrare e selezionare la propria rete virtuale dall'elenco. 

1. Nella pagina **Rete virtuale (classico)** selezionare **Proprietà**. 

   ![ID risorsa della rete virtuale classica](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Fare clic sul pulsante di copia per l'**ID RISORSA** per copiare l'ID risorsa della rete classica negli Appunti. Salvare l'ID dagli Appunti in OneNote o in un file. 

1. Nel menu a sinistra selezionare **subnet**. Verificare che il numero di indirizzi disponibili sia maggiore dei nodi del Azure-SSIS IR. 

   ![Numero di indirizzi disponibili nella rete virtuale](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Aggiungere **MicrosoftAzureBatch** al ruolo **Collaboratore Macchina virtuale classica** per la rete virtuale. 

    a. Nel menu a sinistra selezionare **controllo di accesso (IAM)** e selezionare la scheda **assegnazioni di ruolo** . 

    ![Pulsanti "Controllo di accesso" e "Aggiungi"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Selezionare **Aggiungi assegnazione di ruolo**.

    c. Nella pagina **Aggiungi assegnazione ruolo** selezionare **collaboratore macchina virtuale classica**per **ruolo**. Nella casella **Seleziona** incollare **ddbf3205-c6bd-46AE-8127-60eb93363864**, quindi selezionare **Microsoft Azure batch** dall'elenco dei risultati della ricerca. 

    ![Risultati della ricerca nella pagina "Aggiungi assegnazione ruolo"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. Selezionare **Save (Salva** ) per salvare le impostazioni e chiudere la pagina. 

    ![Salvare le impostazioni di accesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Verificare che venga visualizzato **Microsoft Azure Batch** nell'elenco dei collaboratori. 

    ![Confermare l'accesso di Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Verificare che il provider di Azure Batch sia registrato nella sottoscrizione di Azure che ha la rete virtuale. In alternativa, registrare il provider di Azure Batch. Se si dispone già di un account Azure Batch nella sottoscrizione, la sottoscrizione viene registrata per l'Azure Batch. Se si crea il runtime di integrazione Azure-SSIS nel portale di Data Factory, il provider di Azure Batch viene registrato automaticamente. 

   a. Nel portale di Azure, nel menu a sinistra, selezionare **sottoscrizioni**. 

   b. Selezionare la propria sottoscrizione. 

   c. A sinistra selezionare provider di **risorse**e verificare che **Microsoft. batch** sia un provider registrato. 

   ![Conferma dello stato "Registrato"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se non viene visualizzato **Microsoft.Batch** nell'elenco, per registrarlo [creare un account Azure Batch vuoto](../batch/batch-account-create-portal.md) nella propria sottoscrizione. È possibile eliminarlo in un secondo momento. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Aggiungere il runtime di integrazione Azure-SSIS alla rete virtuale

Dopo aver configurato la rete virtuale Azure Resource Manager o la rete virtuale classica, è possibile aggiungere la Azure-SSIS IR alla rete virtuale:

1. Avviare Microsoft Edge o Google Chrome. Attualmente, solo questi Web browser supportano l'interfaccia utente di Data Factory. 

1. Nel [portale di Azure](https://portal.azure.com)scegliere **Data Factory**dal menu a sinistra. Se non viene visualizzato **Data Factory** nel menu, selezionare **altri servizi**e quindi nella sezione Intelligence e **analisi** selezionare **Data Factory**. 

   ![Elenco di data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selezionare il data factory con il Azure-SSIS IR nell'elenco. Verrà visualizzata la home page della data factory. Selezionare il riquadro **Creare e distribuire**. Verrà visualizzata l'interfaccia utente di Data Factory in una scheda separata. 

   ![Home page di Data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Nell'interfaccia utente di Data Factory passare alla scheda **Modifica**, selezionare **Connessioni** e passare alla scheda **Runtime di integrazione**. 

   ![Scheda "Runtime di integrazione"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Se il Azure-SSIS IR è in esecuzione, nell'elenco **runtime di integrazione** , nella colonna **azioni** , selezionare il pulsante **Interrompi** per il Azure-SSIS IR. Non è possibile modificare un runtime di integrazione fino a quando non lo si arresta. 

   ![Arrestare il runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Nell'elenco **runtime di integrazione** , nella colonna **azioni** , selezionare il pulsante **modifica** per il Azure-SSIS IR. 

   ![Modificare il runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Nel pannello **Integration Runtime installazione** , avanzare nelle pagine **Impostazioni generali** e **Impostazioni SQL** selezionando il pulsante **Avanti** . 

1. Nella pagina **Impostazioni avanzate** : 

   a. Selezionare la casella di controllo accanto a **Seleziona un VNet**. 

   b. Per **Sottoscrizione** selezionare la sottoscrizione di Azure. Nella sottoscrizione è possibile selezionare una rete virtuale esistente. 
  
   c. In **Nome della rete virtuale** selezionare la rete virtuale. 

   d. In **Nome subnet** selezionare la subnet nella rete virtuale. 

   e. Se si vuole anche configurare o gestire un runtime di integrazione self-hosted come proxy per la Azure-SSIS IR, selezionare la casella di controllo **configurazione self-hosted** . Per altre informazioni, vedere [configurare un runtime di integrazione self-hosted come proxy per un Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).

   f. Selezionare il pulsante **convalida VNet** . Se la convalida ha esito positivo, selezionare il pulsante **Avanti** . 

   ![Impostazioni avanzate per la configurazione del runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Nella pagina **Riepilogo** esaminare tutte le impostazioni per il Azure-SSIS IR. Quindi selezionare il pulsante **Aggiorna** .

1. Avviare il Azure-SSIS IR selezionando il pulsante **Avvia** nella colonna **azioni** per la Azure-SSIS IR. Sono necessari da 20 a 30 minuti per avviare la Azure-SSIS IR che si aggiunge a una rete virtuale. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>Configurare una rete virtuale
Prima di poter aggiungere il Azure-SSIS IR a una rete virtuale, è necessario configurare la rete virtuale. Per configurare automaticamente le autorizzazioni e le impostazioni della rete virtuale per la Azure-SSIS IR per l'aggiunta alla rete virtuale, aggiungere lo script seguente:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Creare un runtime di integrazione Azure-SSIS e aggiungerlo a una rete virtuale
È possibile creare un runtime di integrazione Azure-SSIS e contemporaneamente aggiungerlo a una rete virtuale. Per istruzioni e script completi, vedere [creare un Azure-SSIS IR](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Aggiungere un runtime di integrazione Azure-SSIS esistente a una rete virtuale
L'articolo [creare un Azure-SSIS IR](create-azure-ssis-integration-runtime.md) illustra come creare una Azure-SSIS IR e aggiungerla a una rete virtuale nello stesso script. Se si dispone già di un Azure-SSIS IR, attenersi alla procedura seguente per aggiungerlo alla rete virtuale: 
1. Arrestare il runtime di integrazione Azure-SSIS. 
1. Configurare il runtime di integrazione Azure-SSIS per aggiungerlo alla rete virtuale. 
1. Avviare il runtime di integrazione Azure-SSIS. 

### <a name="define-the-variables"></a>Definire le variabili
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Arrestare il runtime di integrazione Azure-SSIS
È necessario arrestare il Azure-SSIS IR prima che sia possibile aggiungerlo a una rete virtuale. Questo comando rilascia tutti i nodi e arresta la fatturazione:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configurare le impostazioni della rete virtuale per aggiungere il runtime di integrazione Azure-SSIS

Per configurare le impostazioni per la rete virtuale a cui verrà aggiunto Azure-SSIS, usare lo script seguente: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Configurare il runtime di integrazione Azure-SSIS
Per configurare la Azure-SSIS IR per l'aggiunta alla rete virtuale, eseguire `Set-AzDataFactoryV2IntegrationRuntime` il comando: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Avviare il runtime di integrazione Azure-SSIS
Per avviare la Azure-SSIS IR, eseguire il comando seguente: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

L'esecuzione di questo comando richiede dai 20 ai 30 minuti.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Azure-SSIS IR, vedere gli articoli seguenti: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Questo articolo fornisce informazioni concettuali generali su IRs, tra cui Azure-SSIS IR. 
- [Esercitazione: Distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). In questa esercitazione vengono fornite istruzioni dettagliate per la creazione del Azure-SSIS IR. Viene usato il database SQL di Azure per ospitare il catalogo SSIS. 
- [Creare un Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Questo articolo si espande nell'esercitazione. Fornisce istruzioni sull'uso del database SQL di Azure con gli endpoint del servizio di rete virtuale o l'istanza gestita in una rete virtuale per ospitare il catalogo SSIS. Viene illustrato come aggiungere il Azure-SSIS IR a una rete virtuale. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). Questo articolo illustra come ottenere informazioni sulle Azure-SSIS IR. Fornisce descrizioni dello stato per le informazioni restituite. 
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). Questo articolo illustra come arrestare, avviare o eliminare i Azure-SSIS IR. Viene anche illustrato come scalare orizzontalmente il runtime di integrazione Azure-SSIS tramite l'aggiunta di nodi.
