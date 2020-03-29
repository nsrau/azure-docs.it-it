---
title: Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale
description: Informazioni su come aggiungere un runtime di integrazione Azure-SSIS a una rete virtuale di Azure.Learn how to join an Azure-SSIS integration runtime to an Azure virtual network.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/01/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 7e8a1793a329a863c9df97ae5ddcbee6cef10e8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964338"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale

Quando si usa SQL Server Integration Services (SSIS) in Azure Data Factory, è necessario aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale di Azure negli scenari seguenti:When using SQL Server Integration Services (SSIS) in Azure Data Factory, you should join your Azure-SSIS integration runtime (IR) to an Azure virtual network in the following scenarios:

- Si vuole connettersi agli archivi dati locali dai pacchetti SSIS eseguiti nel runtime di integrazione Azure-SSIS senza configurare o gestire un runtime di integrazione self-hosted come proxy. 

- Si vuole ospitare il database del catalogo SSIS (SSISDB) in un database SQL di Azure con regole del firewall IP/endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato. 

- Si desidera connettersi alle risorse di Azure configurate con gli endpoint del servizio di rete virtuale dai pacchetti SSIS eseguiti nel runtime di accesso Azure-SSIS.

- Si desidera connettersi agli archivi dati/risorse configurati con le regole del firewall IP dai pacchetti SSIS eseguiti nel runtime di accesso Azure-SSIS.

Data Factory consente di aggiungere il raggio di gioco di Azure-SSIS a una rete virtuale creata tramite il modello di distribuzione classica o il modello di distribuzione di Azure Resource Manager.Data Factory lets you join your Azure-SSIS IR to a virtual network created through the classic deployment model or the Azure Resource Manager deployment model.

> [!IMPORTANT]
> La rete virtuale classica è deprecata, quindi usare la rete virtuale di Azure Resource Manager.The classic virtual network is being deprecated, so use the Azure Resource Manager virtual network instead.  Se si usa già la rete virtuale classica, passare alla rete virtuale di Azure Resource Manager appena possibile.

L'esercitazione sulla configurazione di un runtime di integrazione di Azure-SQL Server Integration Services (SSIS) per l'aggiunta a una rete virtuale illustra i passaggi minimi tramite il portale di Azure.The configuring an [Azure-SQL Server Integration Services (SSIS) integration runtime (IR)](tutorial-deploy-ssis-virtual-network.md) to join a virtual network tutorial shows the minimum steps via Azure portal. Questo articolo si espande sull'esercitazione e descrive tutte le attività facoltative:This article expands on the tutorial and describes all the optional tasks:

- Se si usa la rete virtuale (classica).
- Se si portano i propri indirizzi IP pubblici per il componente di accesso Azure-SSIS.
- Se si utilizza il proprio server DNS (Domain Name System).
- Se si usa un gruppo di sicurezza di rete (NSG) nella subnet.
- Se si usa Azure ExpressRoute o una route definita dall'utente.
- Se si usa il componente di riutilizzo personalizzato di Azure-SSIS.If you use customized Azure-SSIS IR.
- Se si usa il provisioning di Azure Powershell.If you use Azure Powershell provisioning.

## <a name="access-to-on-premises-data-stores"></a>Accesso agli archivi dati locali

Se i pacchetti SSIS accedono agli archivi dati locali, è possibile aggiungere il catalogo i/r di Azure-SSIS a una rete virtuale connessa alla rete locale. In alternativa, è possibile configurare e gestire un componente di accesso client come proxy per il componente di accesso Azure-SSIS. Per altre informazioni, vedere Configurare un prodotto a mano self-hosted come proxy per un componente di accesso [Azure-SSIS](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

Quando si unisce il raggio di ingresso Azure-SSIS a una rete virtuale, tenere presenti i punti importanti seguenti:When joining your Azure-SSIS IR to a virtual network, remember these important points: 

- Se nessuna rete virtuale è connessa alla rete locale, creare innanzitutto una rete virtuale di [Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) per la partecipazione al componente di accesso Azure-SSIS. Configurare quindi una [connessione gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) da sito a sito o una connessione [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) da tale rete virtuale alla rete locale. 

- Se una rete virtuale di Azure Resource Manager è già connessa alla rete locale nello stesso percorso del proprio provider di oggetti, è possibile aggiungere il componente di accesso alla rete virtuale. 

- Se una rete virtuale classica è già connessa alla rete locale in una posizione diversa dal provider di disponibilità di Azure-SSIS, è possibile creare una rete virtuale di [Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) per l'aggiunta al componente di accesso Azure-SSIS. Configurare quindi una connessione di rete virtuale da classica ad Azure Resource Manager.Then configure [a classic-to-Azure Resource Manager virtual network](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) connection. 
 
- Se una rete virtuale di Azure Resource Manager è già connessa alla rete locale in una posizione diversa dal livello di integrazione Azure-SSIS, è innanzitutto possibile creare una rete virtuale di [Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) per l'aggiunta del componente di integrazione Azure-SSIS. Configurare quindi una connessione di rete virtuale azure Resource Manager-Azure Resource Manager.Then configure an Azure Resource Manager-to-Azure Resource Manager virtual network connection. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hosting the SSIS catalog in SQL Database

Se si ospita il catalogo SSIS in un database SQL di Azure con endpoint del servizio di rete virtuale, assicurarsi di aggiungere il componente di accesso Azure-SSIS alla stessa rete virtuale e subnet.

Se si ospita il catalogo SSIS in un'istanza gestita con endpoint privato, assicurarsi di aggiungere il componente di accesso Azure-SSIS alla stessa rete virtuale, ma in una subnet diversa dall'istanza gestita. Per aggiungere il raggio di gioco di azione Azure-SSIS a una rete virtuale diversa rispetto all'istanza gestita, è consigliabile eseguire il peering della rete virtuale (limitato alla stessa area) o una connessione dalla rete virtuale alla rete virtuale. Per altre informazioni, vedere [Connettere l'applicazione all'istanza gestita del database SQL](../sql-database/sql-database-managed-instance-connect-app.md)di Azure.For more information, see Connect your application to Azure SQL Database managed instance .

## <a name="access-to-azure-services"></a>Accesso ai servizi di Azure

Se i pacchetti SSIS accedono alle risorse di Azure che supportano gli endpoint del servizio di [rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) e si vuole proteggere l'accesso a tali risorse dal provider di informazioni Azure-SSIS, è possibile aggiungere il provider di disponibilità di Azure-SSIS a una subnet di rete virtuale configurata per gli endpoint del servizio di rete virtuale e quindi aggiungere una regola di rete virtuale alle risorse di Azure pertinenti per consentire l'accesso dalla stessa subnet.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Accesso alle origini dati protette dalla regola del firewall IP

Se i pacchetti SSIS accedono agli archivi dati/risorse che consentono solo specifici indirizzi IP pubblici statici e si desidera proteggere l'accesso a tali risorse dal sistema di disponibilità di Azure-SSIS, è possibile importare i propri [indirizzi IP pubblici](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address) per il sistema di informazioni di messaggiip Azure-SSIS aggiungendolo a una rete virtuale e quindi aggiungere una regola del firewall IP alle risorse rilevanti per consentire l'accesso da tali indirizzi IP.

In tutti i casi, la rete virtuale può essere distribuita solo tramite il modello di distribuzione di Azure Resource Manager.In all cases, the virtual network can be deployed only through the Azure Resource Manager deployment model.

Per altre informazioni, vedere le sezioni seguenti. 

## <a name="virtual-network-configuration"></a>Configurazione della rete virtuale

Configurare la rete virtuale per soddisfare i requisiti seguenti:Set up your virtual network to meet these requirements: 

- Assicurarsi `Microsoft.Batch` che sia un provider registrato nella sottoscrizione della subnet di rete virtuale che ospita il provider di disponibilità di azure-SSIS. Se si usa una rete `MicrosoftAzureBatch` virtuale classica, partecipare anche al ruolo Collaboratore macchina virtuale classica per tale rete virtuale. 

- Assicurarsi di avere le autorizzazioni necessarie. Per ulteriori informazioni, consultate [Impostare le autorizzazioni.](#perms)

- Selezionare la subnet appropriata per ospitare il runtime di integrazione Azure-SSIS. Per ulteriori informazioni, vedere [Selezionare la subnet](#subnet). 

- Se si portano i propri indirizzi IP pubblici per il sistema di gestione azure-SSIS, vedere [Selezionare gli indirizzi IP pubblici statici](#publicIP)

- Se si utilizza il proprio server DNS (Domain Name System) nella rete virtuale, vedere [Configurare il server DNS](#dns_server). 

- Se si utilizza un gruppo di sicurezza di rete (NSG) nella subnet, vedere [Configurare un gruppo](#nsg)di sicurezza di rete . 

- Se si usa Azure ExpressRoute o una route definita dall'utente, vedere Usare Azure ExpressRoute o un udR.If you use Azure ExpressRoute or a user-defined route (UDR), see [Use Azure ExpressRoute or a UDR.](#route) 

- Assicurarsi che il gruppo di risorse della rete virtuale (o il gruppo di risorse degli indirizzi IP pubblici se si portano i propri indirizzi IP pubblici) possa creare ed eliminare determinate risorse di rete di Azure.Make sure the virtual network's resource group (or the public IP addresses' public ip addresses'if you bring your own public IP addresses) can create and delete certain Azure network resources. Per ulteriori informazioni, vedere [Impostare il gruppo di risorse](#resource-group). 

- Se si personalizza il controllo di accesso Azure-SSIS come descritto in Configurazione personalizzata per il codice AI [Azure-SSIS,](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)i nodi a livello di mano Azure-SSIS otterranno indirizzi IP privati da un intervallo predefinito compreso tra 172.16.0.0 e 172.31.255.255. Assicurarsi quindi che gli intervalli di indirizzi IP privati delle reti virtuali o locali non collidano con questo intervallo.

Questo diagramma mostra le connessioni necessarie per il raggio di ir Azure-SSIS:This diagram shows the required connections for your Azure-SSIS IR:

![Runtime di integrazione Azure-SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a>Impostare le autorizzazioni

L'utente che crea il codice a questo raggio Azure-SSIS deve disporre delle autorizzazioni seguenti:The user who creates the Azure-SSIS IR must have the following permissions:

- Se si sta aggiungendo il runtime di integrazione SSIS a una rete virtuale di Azure Resource Manager, sono disponibili due opzioni:

  - Usare il ruolo predefinito Collaboratore di rete. Questo ruolo richiede l'autorizzazione _Microsoft.Network/\*_, la quale ha un ambito molto maggiore del necessario.

  - Creare un ruolo personalizzato che include solo l'autorizzazione _Microsoft.Network/virtualNetworks/\*/join/action_ necessaria. Se si vuole anche portare i propri indirizzi IP pubblici per il provider di servizi di audio Azure-SSIS durante l'aggiunta a una rete virtuale di Azure Resource Manager, includere anche _Microsoft.Network/publicIPAddresses/'/join/action_ nel ruolo.

- Se si sta aggiungendo il runtime di integrazione SSIS a una rete virtuale classica, è consigliabile usare il ruolo predefinito Collaboratore Macchina virtuale classica. In caso contrario, è necessario definire un ruolo personalizzato che include l'autorizzazione per accedere alla rete virtuale.

### <a name="select-the-subnet"></a><a name="subnet"></a>Selezionare la subnet

Quando si sceglie una subnet: 

- Non selezionare GatewaySubnet per distribuire un componente di accesso Azure-SSIS. È dedicato ai gateway di rete virtuale. 

- Verificare che la subnet selezionata disponga di spazio di indirizzi disponibile sufficiente per il componente di autorizzazione indirizzi Azure-SSIS da usare. Lasciare gli indirizzi IP disponibili per almeno due volte il numero del nodo IR. Azure riserva alcuni indirizzi IP all'interno di ogni subnet. Questi indirizzi non possono essere utilizzati. Il primo e l'ultimo indirizzo IP delle subnet sono riservati per la conformità del protocollo e altri tre indirizzi vengono usati per i servizi di Azure.The first and last IP addresses of the subnets are reserved for protocol conformance, and three more addresses are used for Azure services. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Non usare una subnet occupata esclusivamente da altri servizi di Azure, ad esempio l'istanza gestita del database SQL, il servizio app e così via. 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>Selezionare gli indirizzi IP pubblici statici

Se si desidera portare i propri indirizzi IP pubblici statici per il sistema di disponibilità di Azure-SSIS durante l'aggiunta a una rete virtuale, assicurarsi che soddisfino i requisiti seguenti:If you want to bring your own static public IP addresses for Azure-SSIS IR while joining it to a virtual network, make sure they meet the following requirements:

- Devono essere forniti esattamente due inutilizzati che non sono già associati ad altre risorse di Azure.Exactly two unused ones that are not already associated with other Azure resources should. Quello aggiuntivo verrà usato quando si aggiorna periodicamente il rinisbio Azure-SSIS.

- Entrambi devono essere statici di tipo standard. Per ulteriori dettagli, fare riferimento a [SKU dell'indirizzo IP pubblico.](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku)

- Entrambi devono avere un nome DNS. Se non è stato fornito un nome DNS durante la creazione, è possibile farlo nel portale di Azure.If you have not provided a DNS name when creating them, you can do so on Azure portal.

![Runtime di integrazione Azure-SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- Loro e la rete virtuale devono essere sotto la stessa sottoscrizione e nella stessa area.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a>Configurare il server DNS 
Se è necessario usare il proprio server DNS in una rete virtuale aggiunta dal componente di accesso Azure-SSIS per risolvere il nome host `<your storage account>.blob.core.windows.net`privato, assicurarsi che sia in grado di risolvere anche i nomi host di Azure globali, ad esempio un BLOB di Archiviazione di Azure denominato. 

Di seguito è riportato un approccio consigliato:One recommended approach is below: 

-   Configurare il DNS personalizzato per inoltrare le richieste al DNS di Azure.Configure the custom DNS to forward requests to Azure DNS. È possibile inoltrare i record DNS non risolti all'indirizzo IP dei resolver ricorsivi di Azure (168.63.129.16) nel proprio server DNS. 

Per ulteriori informazioni, vedere [Risoluzione dei](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)nomi che utilizza il proprio server DNS . 

> [!NOTE]
> Utilizzare un nome di dominio completo (FQDN) per il nome `<your_private_server>.contoso.com` host `<your_private_server>`privato, ad esempio utilizzare anziché , poiché il componente di gestione delle informazioni Azure-SSIS non aggiungerà automaticamente il proprio suffisso DNS.

### <a name="set-up-an-nsg"></a><a name="nsg"></a>Configurare un gruppo di sicurezza di baseSet up an NSG
Se è necessario implementare un gruppo di sicurezza di rete per la subnet usata dal raggio di iraldi Azure-SSIS, consentire il traffico in ingresso e in uscita tramite le porte seguenti:If you need to implement an NSG for the subnet used by your Azure-SSIS IR, allow inbound and outbound traffic through the following ports: 

-   **Requisito in ingresso del sistema di informazioni di Azure-SSISInbound requirement of Azure-SSIS IR**

| Direction | Protocollo di trasporto | Source (Sorgente) | Intervallo di porte di origine | Destination | Intervallo di porte di destinazione | Commenti |
|---|---|---|---|---|---|---|
| In ingresso | TCP | BatchNodeManagement (Gestione batch) | * | VirtualNetwork | 29876, 29877 (se si aggiunge il file IR a una rete virtuale di Resource Manager) <br/><br/>10100, 20100, 30100 (se si aggiunge il runtime di integrazione a una rete virtuale classica)| Il servizio Data Factory usa queste porte per comunicare con i nodi del raggio di irrisorse di Azure-SSIS nella rete virtuale. <br/><br/> Indipendentemente dal fatto che si crei o meno un gruppo di sicurezza di rete a livello di subnet, Data Factory configura sempre un gruppo di sicurezza di rete a livello di schede di interfaccia di rete (NIC) collegate alle macchine virtuali che ospitano il ir Azure-SSIS. Il gruppo di sicurezza di rete a livello di scheda di interfaccia di rete consente solo il traffico in entrata dagli indirizzi IP di Data Factory nelle porte specificate. Anche se si aprono queste porte al traffico Internet a livello di subnet, il traffico proveniente da indirizzi IP non indirizzi IP di Data Factory viene bloccato a livello di scheda di interfaccia di rete. |
| In ingresso | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | (Facoltativo) Questa regola è necessaria solo quando il supporto tecnico Microsoft chiede al cliente di aprire per la risoluzione avanzata dei problemi e può essere chiusa subito dopo la risoluzione dei problemi. Il tag di servizio **CorpNetSaw** consente solo alle workstation di accesso sicuro sulla rete aziendale Microsoft di utilizzare il desktop remoto. And this service tag can't be selected from portal and is only available via Azure PowerShell or Azure CLI. <br/><br/> A livello di interfaccia di rete, la porta 3389 è aperta per impostazione predefinita e consente di controllare la porta 3389 a livello di subnet NSG, nel frattempo il flusso di accesso a livello di unità di sicurezza di Azure-SSIS non è consentito la porta 3389 in uscita per impostazione predefinita nella regola di Windows Firewall in ogni nodo a iR per la protezione. |
||||||||

-   **Requisito in uscita del sistema di informazioni sul sistema operativo Azure-SSISOutbound requirement of Azure-SSIS IR**

| Direction | Protocollo di trasporto | Source (Sorgente) | Intervallo di porte di origine | Destination | Intervallo di porte di destinazione | Commenti |
|---|---|---|---|---|---|---|
| In uscita | TCP | VirtualNetwork | * | AzureCloud | 443 | The nodes of your Azure-SSIS IR in the virtual network use this port to access Azure services, such as Azure Storage and Azure Event Hubs. |
| In uscita | TCP | VirtualNetwork | * | Internet | 80 | (Facoltativo) I nodi del calo di rinstallazione Azure-SSIS nella rete virtuale usano questa porta per scaricare un elenco di revoche di certificati da Internet.The nodes of your Azure-SSIS IR in the virtual network use this port to download a certificate revocation list from the internet. Se si blocca questo traffico, è possibile che si verifichi un downgrade delle prestazioni all'avvio del controllo di messaggistica esecuzione dei messaggi di richiesta e perdita di capacità di controllare l'utilizzo dei certificati per l'utilizzo dei certificati. Se si desidera limitare ulteriormente la destinazione a determinati FQDN, fare riferimento alla sezione **Usare Azure ExpressRoute o UDR**|
| In uscita | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | (Facoltativo) Questa regola è necessaria solo quando i nodi del componente di accesso Azure-SSIS nella rete virtuale accedono a un SSISDB ospitato dal server di database SQL. Se il criterio di connessione al server di database SQL è impostato su **Proxy** anziché su **Reindirizza**, è necessaria solo la porta 1433. <br/><br/> Questa regola di sicurezza in uscita non è applicabile a un SSISDB ospitato dall'istanza gestita nella rete virtuale o nel server di database di Azure configurato con l'endpoint privato. |
| In uscita | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | (Facoltativo) Questa regola è necessaria solo quando i nodi del componente di gestione e aggiornamento Azure-SSIS nella rete virtuale accedono a un sSISDB ospitato dall'istanza gestita nella rete virtuale o nel server di database di Azure configurato con endpoint privato. Se il criterio di connessione al server di database SQL è impostato su **Proxy** anziché su **Reindirizza**, è necessaria solo la porta 1433. |
| In uscita | TCP | VirtualNetwork | * | Archiviazione | 445 | (Facoltativo) Questa regola è necessaria solo quando si vuole eseguire il pacchetto SSIS archiviato in File di Azure.This rule is only required when you want to execute SSIS package stored in Azure Files. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a>Usare Azure ExpressRoute o UDRUse Azure ExpressRoute or UDR
Se si desidera esaminare il traffico in uscita dal componente di integrazione e mezzo di integrazione azure-SSIS, è possibile instradare il traffico avviato dal componente di integrazione e configurazione di Azure-SSIS all'appliance firewall locale tramite il tunneling forzato di [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) (adesempio, una route BGP, 0.0.0.0/0, alla rete virtuale) o a Network Virtual Appliance (NVA) come firewall o [Firewall di Azure](https://docs.microsoft.com/azure/firewall/) tramite [UDR](../virtual-network/virtual-networks-udr-overview.md). 

![Scenario di NVA per il sistema Di Gestione configurazione guidata dati per il sistema Di Gestione configurazione guidata dati per il sistema Di Azure-SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

È necessario fare sotto le cose per rendere l'intero scenario di lavoro
   -   Il traffico in ingresso tra i servizi di gestione di Azure Batch e il codice a raggi Digestione Azure-SSIS non può essere instradato tramite l'appliance firewall.
   -   L'appliance firewall deve consentire il traffico in uscita richiesto dal componente di gestione delle richieste di gestione delle richieste di accesso azure-SSIS.

Il traffico in ingresso tra i servizi di gestione di Azure Batch e il codice a- unico Azure-SSIS non può essere instradato all'appliance firewall altrimenti il traffico verrà interrotto a causa di un problema di routing asimmetrico. Le route devono essere definite per il traffico in ingresso in modo che il traffico possa rispondere nello stesso modo in cui è arrivato. È possibile definire UDR specifici per instradare il traffico tra i servizi di gestione di Azure Batch e il codice a vita Azure-SSIS con il tipo di hop successivo come **Internet**.

Ad esempio, se il catalogo iR Azure-SSIS si trova `UK South` in individua e si desidera controllare il `BatchNodeManagement.UKSouth` traffico in uscita tramite Firewall di Azure, è necessario ottenere innanzitutto un elenco IP di tag di servizio dal collegamento di [download dell'intervallo IP](https://www.microsoft.com/download/details.aspx?id=56519) dei tag del servizio o tramite l'API di individuazione dei tag di [servizio.](https://aka.ms/discoveryapi) Applicare quindi le seguenti UDR di route di intervallo IP correlate con il tipo di hop successivo come **Internet** insieme alla route 0.0.0.0/0 con il tipo di hop successivo come **appliance virtuale**.

![Impostazioni UDR di Azure BatchAzure Batch UDR settings](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Questo approccio comporta un costo aggiuntivo di manutenzione. Controllare regolarmente l'intervallo IP e aggiungere nuovi intervalli IP nell'UDR per evitare di interrompere il componente di irper di Azure-SSIS. Si consiglia di controllare l'intervallo IP mensilmente perché quando il nuovo IP viene visualizzato nel tag di servizio, l'IP avrà un altro mese entrare in vigore. 

Affinché l'appliance firewall consenta il traffico in uscita, è necessario consentire l'uscita verso le porte inferiori a i requisiti delle regole in uscita del gruppo di sicurezza di rete.
-   Porta 443 con destinazione come servizi cloud di Azure.Port 443 with destination as Azure Cloud services.

    Se si usa Firewall di Azure, è possibile specificare la regola di rete con il tag del servizio AzureCloud, altrimenti è possibile consentire la destinazione come tutte nell'appliance firewall.

-   Porta 80 con destinazione come siti di download CRL.

    È necessario consentire sotto i nomi di dominio completi utilizzati come siti di download CRL (elenco di revoche di certificati) dei certificati per lo scopo di gestione dei messaggi stica agli oggetti liristica Azure-SSIS:You shall allow below FQDNs which are used as CRL (Certificate Revocation List) download sites of certificates for Azure-SSIS IR management purpose:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Se si utilizzano certificati con CRL diverso, si consiglia di includerli. È possibile leggere questa opzione per ulteriori informazioni [sull'elenco di revoche](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx)di certificati .

    Se non si consente questo traffico, è possibile che si verifichi un downgrade delle prestazioni all'avvio del componente di accesso visivo di Azure-SSIS e si perda la capacità di controllare l'utilizzo dei certificati per l'utilizzo dei certificati, che non è consigliato dal punto di vista della sicurezza.

-   Porta 1433, 11000-11999 con destinazione come SQL di Azure (necessaria solo quando i nodi del sistema di disponibilità di Azure-SSIS nella rete virtuale accedono a un SSISDB ospitato dal server di database SQL).

    Se si usa Firewall di Azure, è possibile specificare la regola di rete con il tag del servizio SQL di Azure, altrimenti è possibile consentire la destinazione come URL SQL di Azure specifico nell'appliance firewall.

-   Porta 445 con destinazione come Archiviazione di Azure (necessaria solo quando si esegue il pacchetto SSIS archiviato in File di Azure).

    Se si usa Firewall di Azure, è possibile specificare la regola di rete con Il tag del servizio di archiviazione, altrimenti è possibile consentire la destinazione come URL di archiviazione file di Azure specifico nell'appliance firewall.

> [!NOTE]
> Per SQL di Azure e Archiviazione, se si configurano gli endpoint del servizio di rete virtuale nella subnet, il traffico tra il provider di disponibilità di bit Azure-SSIS e SQL di Azure nella stessa area: Archiviazione di Azure nella stessa area o area associata verrà instradato direttamente alla rete backbone di Microsoft AzureFor Azure-SSIS IR and Azure SQL in same region : Azure Storage in same region or paired region will be routed to Microsoft Azure backbone network directly al posto dell'appliance firewall.

Se non è necessaria la funzionalità di controllo del traffico in uscita del codice IR Azure-SSIS, è sufficiente applicare route per forzare tutto il traffico al tipo di hop successivo **Internet:**

-   In uno scenario Azure ExpressRoute è possibile applicare una route 0.0.0.0/0 con il tipo di hop successivo come **Internet** nella subnet che ospita il provider di informazioni Azure-SSIS. 
-   In uno scenario di appliance virtuale di rete, è possibile modificare la route 0.0.0.0/0 esistente applicata nella subnet che ospita il provider di servizi di audioutilizzo Azure-SSIS dal tipo di hop successivo come **appliance virtuale** a **Internet**.

![Aggiungere una route](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Specificare la route con il tipo di hop successivo **Internet** non significa che tutto il traffico passerà su Internet. Finché l'indirizzo di destinazione è per uno dei servizi di Azure, Azure instrada il traffico direttamente al servizio tramite la rete backbone di Azure, anziché instradare il traffico a Internet.As long as destination address is for one of's services, Azure routes the traffic directly to the service over Azure's backbone network, rather than routing the traffic to the Internet.

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a>Impostare il gruppo di risorse

Il runtime di integrazione Azure-SSIS deve creare alcune risorse di rete nello stesso gruppo di risorse della rete virtuale. Queste risorse includono:
- Un servizio di bilanciamento del carico di Azure, con il nome * \<Guid>-azurebatch-cloudserviceloadbalancer*.
- Un indirizzo IP pubblico di Azure, con il nome * \<Guid>-azurebatch-cloudservicepublicip*.
- Un gruppo di sicurezza di lavoro di rete, con il nome * \<Guid>-azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> È ora possibile portare i propri indirizzi IP pubblici statici per il componente di gestione dei raggi DiSIS Azure-SSIS.You can now bring your own static public IP addresses for Azure-SSIS IR. In questo scenario verranno creati solo il servizio di bilanciamento del carico di Azure e il gruppo di sicurezza di rete nello stesso gruppo di risorse degli indirizzi IP pubblici statici anziché nella rete virtuale.

Tali risorse verranno create all'avvio del componente di ricreazione Azure-SSIS. Verranno eliminati quando il componente di ricreazione Azure-SSIS si arresta. Se si portano i propri indirizzi IP pubblici statici per il ir Azure-SSIS, non verranno eliminati quando il componente di accesso Azure-SSIS si arresta. Per evitare di bloccare l'arresto del componente di riproduzione Azure-SSIS, non riutilizzare queste risorse di rete nelle altre risorse. 

Assicurarsi di non avere alcun blocco di risorsa per il gruppo di risorse/sottoscrizione a cui appartengono la rete virtuale o gli indirizzi IP pubblici statici. Se si configura un blocco di sola lettura/eliminazione, l'avvio e l'arresto del componente di configurazione Azure-SSIS avranno esito negativo o il problema si blocca.

Assicurarsi di non disporre di criteri di Azure che impediscono la creazione delle risorse seguenti nel gruppo di risorse/sottoscrizione a cui appartengono la rete virtuale o gli indirizzi IP pubblici statici:Make sure that you don't an Azure policy that prevents the following resources from being created under the resource group/subscription to which the virtual network/your static public IP addresses belong: 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a>Domande frequenti su <a name="faq"></a>

- Come è possibile proteggere l'indirizzo IP pubblico esposto nel componente di accesso Azure-SSIS per la connessione in ingresso? È possibile rimuovere l'indirizzo IP pubblico?
 
  Al momento, un indirizzo IP pubblico verrà creato automaticamente quando il sistema di disponibilità del sistema di disponibilità di un'azione tra Azure ESIS si unisce a una rete virtuale. Abbiamo un gruppo di sicurezza di rete a livello di scheda di interfaccia di rete per consentire solo ai servizi di gestione di Azure Batch di connettersi in ingresso al proprio iR Azure-SSIS. È inoltre possibile specificare un gruppo di sicurezza di rete a livello di subnet per la protezione in ingresso.

  Se non si vuole esporre alcun indirizzo IP pubblico, è consigliabile configurare un componente di accesso client indipendente come proxy per il componente di accesso [Azure-SSIS](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) anziché aggiungere il componente di accesso Azure-SSIS a una rete virtuale, se ciò si applica allo scenario.
 
- È possibile aggiungere l'indirizzo IP pubblico del mio componente di accesso Azure-SSIS all'elenco Consenti del firewall per le origini dati?

  È ora possibile portare i propri indirizzi IP pubblici statici per il componente di gestione dei raggi DiSIS Azure-SSIS.You can now bring your own static public IP addresses for Azure-SSIS IR. In questo caso, è possibile aggiungere gli indirizzi IP all'elenco Consenti del firewall per le origini dati. È anche possibile considerare altre opzioni seguenti per proteggere l'accesso ai dati dal livello di ir Azure-SSIS a seconda dello scenario:You can also consider other options below to secure data access from your Azure-SSIS IR depending on your scenario:

  - Se l'origine dati è in locale, dopo aver connesso una rete virtuale alla rete locale e aver aggiunto il componente di integrazione di Azure-SSIS alla subnet della rete virtuale, è possibile aggiungere l'intervallo di indirizzi IP privati della subnet all'elenco Consenti del firewall per l'origine dati. .
  - Se l'origine dati è un servizio di Azure che supporta gli endpoint del servizio di rete virtuale, è possibile configurare un endpoint del servizio di rete virtuale nella subnet della rete virtuale e aggiungere il provider di disponibilità di Azure-SSIS a tale subnet. È quindi possibile aggiungere una regola di rete virtuale con tale subnet al firewall per l'origine dati.
  - Se l'origine dati è un servizio cloud non Azure, è possibile usare un UDR per instradare il traffico in uscita dal provider di disponibilità di Azure-SSIS a un firewall NVA/Azure tramite un indirizzo IP pubblico statico. È quindi possibile aggiungere l'indirizzo IP pubblico statico del firewall NVA/Azure all'elenco Consenti del firewall per l'origine dati.
  - Se nessuna delle opzioni precedenti soddisfa le proprie esigenze, è consigliabile configurare un componente di accesso a motore indipendente come proxy per il componente di accesso [Azure-SSIS](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). È quindi possibile aggiungere l'indirizzo IP pubblico statico del computer che ospita il componente di gestione delle risorse di messaggi di ricreazione self-hosted all'elenco Consenti del firewall per l'origine dati.

- Perché è necessario fornire due indirizzi pubblici statici se si vuole portare il proprio per il componente di accesso Azure-SSIS?

  Il componente di ri-azione Azure-SSIS viene aggiornato automaticamente a intervalli regolari. I nuovi nodi vengono creati durante l'aggiornamento e quelli vecchi verranno eliminati. Tuttavia, per evitare tempi di inattività, i nodi precedenti non verranno eliminati fino a quando quelli nuovi non saranno pronti. Pertanto, il primo indirizzo IP pubblico statico utilizzato dai nodi precedenti non può essere rilasciato immediatamente ed è necessario il secondo indirizzo IP pubblico statico per creare i nuovi nodi.

- Ho portato i miei indirizzi IP pubblici statici per il sistema di raggi condizioni di accesso Azure-SSIS, ma perché non è ancora possibile accedere alle origini dati?

  - Verificare che i due indirizzi IP pubblici statici siano entrambi aggiunti all'elenco Consenti del firewall per le origini dati. Ogni volta che il runtime di accesso Azure-SSIS viene aggiornato, il relativo indirizzo IP pubblico statico passa da uno all'altro. Se si aggiunge solo uno di essi all'elenco Consenti, l'accesso ai dati per il componente di accesso Azure-SSIS verrà interrotto dopo l'aggiornamento.
  - Se l'origine dati è un servizio di Azure, verificare se è stato configurato con endpoint del servizio di rete virtuale. In questo caso, il traffico dal provider di disponibilità di accesso Azure-SSIS all'origine dati passerà all'uso degli indirizzi IP privati gestiti dai servizi di Azure e l'aggiunta di indirizzi IP pubblici statici all'elenco Consenti del firewall per l'origine dati non avrà effetto.

## <a name="azure-portal-data-factory-ui"></a>Portale di Azure (interfaccia utente di Data Factory)

Questa sezione illustra come aggiungere un provider di disponibilità di Azure-SSIS esistente a una rete virtuale (classica o Azure Resource Manager) usando il portale di Azure e l'interfaccia utente di Data Factory.This section shows you how to join an existing Azure-SSIS IR to a virtual network (classic or Azure Resource Manager) by using the Azure portal and Data Factory UI. 

Prima di unire il raggio di ingresso Azure-SSIS alla rete virtuale, è necessario configurare correttamente la rete virtuale. Seguire i passaggi nella sezione che si applica al tipo di rete virtuale (classico o Azure Resource Manager). Seguire quindi i passaggi nella terza sezione per aggiungere il componente di accesso Azure-SSIS alla rete virtuale. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Configurare una rete virtuale di Azure Resource ManagerConfigure an Azure Resource Manager virtual network

Usare il portale per configurare una rete virtuale di Azure Resource Manager prima di provare ad aggiungere un provider di servizi di accesso corrente Azure-SSIS.

1. Avviare Microsoft Edge o Google Chrome. Attualmente, solo questi Web browser supportano l'interfaccia utente di Data Factory.Currently, only these web browsers support the Data Factory UI. 

1. Accedere al [portale](https://portal.azure.com)di Azure . 

1. Selezionare **Altri servizi**. Filtrare e selezionare **Reti virtuali**. 

1. Filtrare e selezionare la propria rete virtuale dall'elenco. 

1. Nella pagina **Rete virtuale** selezionare **Proprietà**. 

1. Fare clic sul pulsante di copia per l'**ID RISORSA** per copiare l'ID risorsa per la rete virtuale negli Appunti. Salvare l'ID dagli Appunti in OneNote o in un file. 

1. Nel menu a sinistra selezionare **Subnet.** Verificare che il numero di indirizzi disponibili sia maggiore dei nodi nel componente di riferimento Azure-SSIS. 

1. Verificare che il provider di Azure Batch sia registrato nella sottoscrizione di Azure che ha la rete virtuale. In alternativa, registrare il provider batch di Azure.Or can register the Azure Batch provider. Se nella sottoscrizione è già presente un account Batch di Azure, la sottoscrizione è registrata per Azure Batch.If you already have an Azure Batch account in your subscription, your subscription is registered for Azure Batch. Se si crea il runtime di integrazione Azure-SSIS nel portale di Data Factory, il provider di Azure Batch viene registrato automaticamente. 

   1. Nel portale di Azure scegliere **Sottoscrizioni**nel menu a sinistra. 

   1. Selezionare la propria sottoscrizione. 

   1. A sinistra, selezionare **Provider di risorse**e verificare che **Microsoft.Batch** sia un provider registrato. 

   ![Conferma dello stato "Registrato"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se non viene visualizzato **Microsoft.Batch** nell'elenco, per registrarlo [creare un account Azure Batch vuoto](../batch/batch-account-create-portal.md) nella propria sottoscrizione. È possibile eliminarlo in un secondo momento. 

### <a name="configure-a-classic-virtual-network"></a>Configurare una rete virtuale classica

Usare il portale per configurare una rete virtuale classica prima di provare ad aggiungere un componente di accesso Azure-SSIS. 

1. Avviare Microsoft Edge o Google Chrome. Attualmente, solo questi Web browser supportano l'interfaccia utente di Data Factory.Currently, only these web browsers support the Data Factory UI. 

1. Accedere al [portale](https://portal.azure.com)di Azure . 

1. Selezionare **Altri servizi**. Filtrare e selezionare **Reti virtuali (classiche)**. 

1. Filtrare e selezionare la propria rete virtuale dall'elenco. 

1. Nella pagina **Rete virtuale (classico)** selezionare **Proprietà**. 

   ![ID risorsa della rete virtuale classica](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Fare clic sul pulsante di copia per l'**ID RISORSA** per copiare l'ID risorsa della rete classica negli Appunti. Salvare l'ID dagli Appunti in OneNote o in un file. 

1. Nel menu a sinistra selezionare **Subnet.** Verificare che il numero di indirizzi disponibili sia maggiore dei nodi nel componente di riferimento Azure-SSIS. 

   ![Numero di indirizzi disponibili nella rete virtuale](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Aggiungere **MicrosoftAzureBatch** al ruolo **Collaboratore Macchina virtuale classica** per la rete virtuale. 

   1. Nel menu a sinistra selezionare **Controllo di accesso (IAM)** e quindi selezionare la scheda **Assegnazioni ruolo.** 

       ![Pulsanti "Controllo di accesso" e "Aggiungi"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Selezionare **Aggiungi assegnazione ruolo**.

   1. Nella pagina **Aggiungi assegnazione ruolo** selezionare **Collaboratore macchina virtuale classica**in **Ruolo**. Nella casella **Seleziona** incollare **ddbf3205-c6bd-46ae-8127-60eb93363864**e quindi selezionare **Microsoft Azure Batch** dall'elenco dei risultati della ricerca. 

       ![Risultati della ricerca nella pagina "Aggiungi assegnazione ruolo"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Selezionare **Salva** per salvare le impostazioni e chiudere la pagina. 

       ![Salvare le impostazioni di accesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Verificare che venga visualizzato **Microsoft Azure Batch** nell'elenco dei collaboratori. 

       ![Confermare l'accesso di Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Verificare che il provider di Azure Batch sia registrato nella sottoscrizione di Azure che ha la rete virtuale. In alternativa, registrare il provider batch di Azure.Or can register the Azure Batch provider. Se nella sottoscrizione è già presente un account Batch di Azure, la sottoscrizione è registrata per Azure Batch.If you already have an Azure Batch account in your subscription, your subscription is registered for Azure Batch. Se si crea il runtime di integrazione Azure-SSIS nel portale di Data Factory, il provider di Azure Batch viene registrato automaticamente. 

   1. Nel portale di Azure scegliere **Sottoscrizioni**nel menu a sinistra. 

   1. Selezionare la propria sottoscrizione. 

   1. A sinistra, selezionare **Provider di risorse**e verificare che **Microsoft.Batch** sia un provider registrato. 

   ![Conferma dello stato "Registrato"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se non viene visualizzato **Microsoft.Batch** nell'elenco, per registrarlo [creare un account Azure Batch vuoto](../batch/batch-account-create-portal.md) nella propria sottoscrizione. È possibile eliminarlo in un secondo momento. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Aggiungere il runtime di integrazione Azure-SSIS alla rete virtuale

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

   1. Selezionare la casella di controllo Porta indirizzi IP pubblici statici per il runtime di **integrazione Azure-SSIS** per scegliere se si desidera portare i propri indirizzi IP pubblici statici per il runtime di integrazione Azure-SSIS, in modo da poterli consentire nel firewall per le origini dati.

      Se si seleziona la casella di controllo, completare i passaggi seguenti.

      1. Per **Primo indirizzo IP pubblico statico**, selezionare il primo indirizzo IP pubblico statico che soddisfa i requisiti per [il](#publicIP) componente di accesso Azure-SSIS. Se non ne hai, fai clic su **Crea nuovo** link per creare indirizzi IP pubblici statici nel portale di Azure, quindi fai clic sul pulsante Aggiorna qui, in modo da poterli selezionare.
      
      1. Per **Secondo indirizzo IP pubblico statico**, selezionare il secondo indirizzo IP pubblico statico che soddisfa i requisiti per [il](#publicIP) componente di accesso Azure-SSIS. Se non ne hai, fai clic su **Crea nuovo** link per creare indirizzi IP pubblici statici nel portale di Azure, quindi fai clic sul pulsante Aggiorna qui, in modo da poterli selezionare.

   1. Selezionare **Convalida rete virtuale**. Se la convalida ha esito positivo, selezionare **Continua**. 

   ![Impostazioni avanzate con una rete virtuale](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Nella sezione **Riepilogo** esaminare tutte le impostazioni per il componente di ricambio Azure-SSIS. Quindi selezionare **Aggiorna**.

1. Avviare il componente di ri-azione Azure-SSIS selezionando il pulsante **Start** nella colonna **Azioni** per il componente di ri-acqua Azure-SSIS. L'avvio del componente di accesso Azure-SSIS che si unisce a una rete virtuale richiede da 20 a 30 minuti. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>Definire le variabili

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>Configurare una rete virtuale

Prima di poter aggiungere il raggio di accesso Azure-SSIS a una rete virtuale, è necessario configurare la rete virtuale. Per configurare automaticamente le autorizzazioni e le impostazioni della rete virtuale per il codeR Azure-SSIS per l'aggiunta alla rete virtuale, aggiungere lo script seguente:To automatically configure virtual network permissions and settings for your Azure-SSIS IR to join the virtual network, add the following script:

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

È possibile creare un runtime di integrazione Azure-SSIS e contemporaneamente aggiungerlo a una rete virtuale. Per lo script completo e le istruzioni, vedere Creare un oggetto dei raggi di proprietà [Azure-SSIS.](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Aggiungere un runtime di integrazione Azure-SSIS esistente a una rete virtuale

L'articolo [Creare un iR Azure-SSIS](create-azure-ssis-integration-runtime.md) illustra come creare un codice a/ vale vocabolo Azure-SSIS e aggiungerlo a una rete virtuale nello stesso script. Se si dispone già di un componente di accesso Azure-SSIS, seguire questi passaggi per aggiungerlo alla rete virtuale:If you already have an Azure-SSIS IR, follow these steps to join it to the virtual network: 
1. Arrestare il runtime di integrazione Azure-SSIS. 
1. Configurare il runtime di integrazione Azure-SSIS per aggiungerlo alla rete virtuale. 
1. Avviare il runtime di integrazione Azure-SSIS. 

### <a name="stop-the-azure-ssis-ir"></a>Arrestare il runtime di integrazione Azure-SSIS

È necessario arrestare il componente di accesso Azure-SSIS prima di poterlo aggiungere a una rete virtuale. Questo comando rilascia tutti i nodi e arresta la fatturazione:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configurare le impostazioni della rete virtuale per aggiungere il runtime di integrazione Azure-SSIS

Per configurare le impostazioni per la rete virtuale a cui si unirà Azure-SSIS, usare questo script:To configure settings for the virtual network that the Azure-SSIS will join, use this script: 

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

Per aggiungere il runtime di accesso Azure-SSIS `Set-AzDataFactoryV2IntegrationRuntime` a una rete virtuale, eseguire il comando:To join your Azure-SSIS IR to a virtual network, run the command: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-azure-ssis-ir"></a>Avviare il runtime di integrazione Azure-SSIS

Per avviare il runtime di iR Azure-SSIS, eseguire il comando seguente:To start the Azure-SSIS IR, run the following command: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

L'esecuzione di questo comando richiede dai 20 ai 30 minuti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul sistema di informazioni sul sistema di informazioni Azure-SSIS, vedere gli articoli seguenti:For more information about Azure-SSIS IR, see the following articles: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Questo articolo fornisce informazioni concettuali generali sui raggi di gestione delle informazioni, tra cui il codice IR Azure-SSIS. 
- [Esercitazione: Distribuire pacchetti SSIS in Azure .Tutorial: Deploy SSIS packages to Azure](tutorial-create-azure-ssis-runtime-portal.md). Questa esercitazione fornisce istruzioni dettagliate per creare il proprio iR Azure-SSIS. Viene usato il database SQL di Azure per ospitare il catalogo SSIS. 
- Creare un oggetto [iR Azure-SSIS](create-azure-ssis-integration-runtime.md). Questo articolo si espande sull'esercitazione. Fornisce istruzioni sull'uso del database SQL di Azure con gli endpoint del servizio di rete virtuale o l'istanza gestita in una rete virtuale per ospitare il catalogo SSIS. Viene illustrato come aggiungere il componente di riutilizzo Di Azure-SSIS a una rete virtuale. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). Questo articolo illustra come ottenere informazioni sul componente di accesso Azure-SSIS. Fornisce descrizioni dello stato per le informazioni restituite. 
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). Questo articolo illustra come arrestare, avviare o eliminare il raggio di ir Azure-SSIS. Viene anche illustrato come scalare orizzontalmente il runtime di integrazione Azure-SSIS tramite l'aggiunta di nodi.