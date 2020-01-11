---
title: Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale
description: Informazioni su come aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale di Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/23/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: b068eeeada842f2439f6135bfa8567a6c9709d12
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75862816"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale

Quando si usa SQL Server Integration Services (SSIS) in Azure Data Factory, è necessario aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale di Azure negli scenari seguenti:

- Si vuole connettersi a archivi dati locali da pacchetti SSIS eseguiti nel Azure-SSIS IR senza configurare o gestire un runtime di integrazione self-hosted come proxy. 

- Si vuole ospitare il database del catalogo SSIS (SSISDB) in un database SQL di Azure con regole del firewall IP/endpoint del servizio di rete virtuale o un'istanza gestita con endpoint privato. 

- Si vuole connettersi alle risorse di Azure configurate con gli endpoint di servizio della rete virtuale da pacchetti SSIS eseguiti nel Azure-SSIS IR.

- Si desidera connettersi a archivi dati/risorse configurati con regole del firewall IP da pacchetti SSIS eseguiti nel Azure-SSIS IR.

Data Factory consente di aggiungere i Azure-SSIS IR a una rete virtuale creata tramite il modello di distribuzione classica o il modello di distribuzione di Azure Resource Manager.

> [!IMPORTANT]
> La rete virtuale classica verrà deprecata. utilizzare invece la rete virtuale Azure Resource Manager.  Se si usa già la rete virtuale classica, passare alla rete virtuale Azure Resource Manager il prima possibile.

L'esercitazione [configurazione di un runtime di integrazione di Azure-SQL Server Integration Services (SSIS) per l'aggiunta a una rete virtuale](tutorial-deploy-ssis-virtual-network.md) Mostra la procedura minima tramite portale di Azure. Questo articolo espande l'esercitazione e descrive tutte le attività facoltative:

- Se si usa la rete virtuale (versione classica).
- Se si importano indirizzi IP pubblici personalizzati per la Azure-SSIS IR.
- Se si usa il proprio server di Domain Name System (DNS).
- Se si usa un gruppo di sicurezza di rete (NSG) nella subnet.
- Se si usa Azure ExpressRoute o una route definita dall'utente (UDR).
- Se si usano Azure-SSIS IR personalizzati.
- Se si usa il provisioning di Azure PowerShell.

## <a name="access-to-on-premises-data-stores"></a>Accesso agli archivi dati locali

Se i pacchetti SSIS accedono agli archivi dati locali, è possibile aggiungere il Azure-SSIS IR a una rete virtuale connessa alla rete locale. In alternativa, è possibile configurare e gestire un runtime di integrazione self-hosted come proxy per la Azure-SSIS IR. Per altre informazioni, vedere [configurare un runtime di integrazione self-hosted come proxy per un Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

Quando si aggiunge il Azure-SSIS IR a una rete virtuale, tenere presenti le considerazioni seguenti: 

- Se nessuna rete virtuale è connessa alla rete locale, creare prima di tutto una [rete virtuale Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) per l'aggiunta del Azure-SSIS IR. Configurare quindi una connessione [gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) da sito a sito o una connessione [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) da tale rete virtuale alla rete locale. 

- Se una rete virtuale Azure Resource Manager è già connessa alla rete locale nello stesso percorso del Azure-SSIS IR, è possibile aggiungere il runtime di integrazione alla rete virtuale. 

- Se una rete virtuale classica è già connessa alla rete locale in un percorso diverso rispetto alla Azure-SSIS IR, è possibile creare una [rete virtuale Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) per l'aggiunta di Azure-SSIS IR. Configurare quindi una connessione [da rete virtuale classica a rete virtuale di Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 
 
- Se una rete virtuale Azure Resource Manager è già connessa alla rete locale in un percorso diverso rispetto al Azure-SSIS IR, è possibile creare prima di tutto una [rete virtuale Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) per aggiungere il Azure-SSIS IR. Configurare quindi una connessione di rete virtuale da Azure Resource Manager a Azure Resource Manager. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hosting del catalogo SSIS nel database SQL

Se il catalogo SSIS è ospitato in un database SQL di Azure con gli endpoint del servizio di rete virtuale, assicurarsi di aggiungere il Azure-SSIS IR alla stessa rete virtuale e alla stessa subnet.

Se il catalogo SSIS è ospitato in un'istanza gestita con endpoint privato, assicurarsi di aggiungere la Azure-SSIS IR alla stessa rete virtuale, ma in una subnet diversa da quella dell'istanza gestita. Per aggiungere la Azure-SSIS IR a una rete virtuale diversa da quella dell'istanza gestita, è consigliabile usare il peering di rete virtuale (che è limitato alla stessa area) o una connessione dalla rete virtuale alla rete virtuale. Per altre informazioni, vedere [connettere l'applicazione a istanza gestita di database SQL di Azure](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="access-to-azure-services"></a>Accesso ai servizi di Azure

Se i pacchetti SSIS accedono alle risorse di Azure che supportano gli [endpoint di servizio della rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) e si vuole proteggere l'accesso a tali risorse da Azure-SSIS IR, è possibile aggiungere i Azure-SSIS IR a una subnet di rete virtuale configurata per gli endpoint del servizio rete virtuale e quindi aggiungere una regola della rete virtuale alle risorse di Azure pertinenti per consentire l'accesso dalla stessa subnet.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Accesso alle origini dati protette dalla regola del firewall IP

Se i pacchetti SSIS accedono a archivi dati/risorse che consentono solo indirizzi IP pubblici statici specifici e si vuole proteggere l'accesso a tali risorse da Azure-SSIS IR, è possibile importare i propri [indirizzi IP pubblici](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address) per Azure-SSIS IR durante l'aggiunta a una rete virtuale e quindi aggiungere una regola del firewall IP alle risorse rilevanti per consentire l'accesso da tali indirizzi IP.

In tutti i casi, la rete virtuale può essere distribuita solo tramite il modello di distribuzione Azure Resource Manager.

Per altre informazioni, vedere le sezioni seguenti. 

## <a name="virtual-network-configuration"></a>Configurazione della rete virtuale

Configurare la rete virtuale in modo che soddisfi questi requisiti: 

- Assicurarsi che `Microsoft.Batch` sia un provider registrato nella sottoscrizione della subnet della rete virtuale che ospita il Azure-SSIS IR. Se si usa una rete virtuale classica, aggiungere anche `MicrosoftAzureBatch` al ruolo di collaboratore macchina virtuale classica per la rete virtuale. 

- Assicurarsi di avere le autorizzazioni necessarie. Per altre informazioni, vedere [configurare le autorizzazioni](#perms).

- Selezionare la subnet appropriata per ospitare il runtime di integrazione Azure-SSIS. Per ulteriori informazioni, vedere [selezionare la subnet](#subnet). 

- Se si importano indirizzi IP pubblici per la Azure-SSIS IR, vedere [selezionare gli indirizzi IP pubblici statici](#publicIP)

- Se si usa il proprio server di Domain Name System (DNS) nella rete virtuale, vedere [configurare il server DNS](#dns_server). 

- Se si usa un gruppo di sicurezza di rete (NSG) nella subnet, vedere la pagina relativa alla [configurazione di un NSG](#nsg). 

- Se si usa Azure ExpressRoute o una route definita dall'utente (UDR), vedere [usare Azure ExpressRoute o un UdR](#route). 

- Verificare che il gruppo di risorse della rete virtuale (o il gruppo di risorse indirizzi IP pubblici se si portano i propri indirizzi IP pubblici) possa creare ed eliminare alcune risorse di rete di Azure. Per altre informazioni, vedere [configurare il gruppo di risorse](#resource-group). 

- Se si Personalizza il Azure-SSIS IR come descritto in [installazione personalizzata per Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup), i nodi di Azure-SSIS IR otterranno indirizzi IP privati da un intervallo predefinito di 172.16.0.0 a 172.31.255.255. Assicurarsi quindi che gli intervalli di indirizzi IP privati delle reti virtuali o locali non entrino in conflitto con questo intervallo.

Questo diagramma mostra le connessioni necessarie per il Azure-SSIS IR:

![Runtime di integrazione Azure-SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>Configurare le autorizzazioni

L'utente che crea il Azure-SSIS IR deve disporre delle autorizzazioni seguenti:

- Se si sta aggiungendo il runtime di integrazione SSIS a una rete virtuale di Azure Resource Manager, sono disponibili due opzioni:

  - Usare il ruolo predefinito collaboratore rete. Questo ruolo richiede l'autorizzazione _Microsoft.Network/\*_ , la quale ha un ambito molto maggiore del necessario.

  - Creare un ruolo personalizzato che include solo l'autorizzazione _Microsoft.Network/virtualNetworks/\*/join/action_ necessaria. Se si vuole anche usare indirizzi IP pubblici per Azure-SSIS IR durante l'aggiunta a una rete virtuale Azure Resource Manager, includere anche l'autorizzazione _Microsoft. Network/publicIPAddresses/*/join/Action_ per il ruolo.

- Se si aggiunge il runtime di integrazione SSIS a una rete virtuale classica, è consigliabile usare il ruolo di collaboratore macchina virtuale classico incorporato. In caso contrario, è necessario definire un ruolo personalizzato che include l'autorizzazione per accedere alla rete virtuale.

### <a name="subnet"></a> Selezionare la subnet

Quando si sceglie una subnet: 

- Non selezionare il GatewaySubnet per distribuire un Azure-SSIS IR. È dedicata ai gateway di rete virtuale. 

- Assicurarsi che la subnet selezionata disponga di spazio di indirizzi disponibile sufficiente per l'utilizzo da parte del Azure-SSIS IR. Lasciare gli indirizzi IP disponibili almeno due volte il numero del nodo IR. Azure riserva alcuni indirizzi IP all'interno di ogni subnet. Non è possibile usare questi indirizzi. Il primo e l'ultimo indirizzo IP delle subnet sono riservati per la conformità al protocollo e vengono usati altri tre indirizzi per i servizi di Azure. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Non usare una subnet occupata esclusivamente da altri servizi di Azure, ad esempio istanza gestita di database SQL, servizio app e così via. 

### <a name="publicIP"></a>Selezionare gli indirizzi IP pubblici statici

Se si vuole portare gli indirizzi IP pubblici statici per Azure-SSIS IR durante l'aggiunta a una rete virtuale, assicurarsi che soddisfino i requisiti seguenti:

- Devono essere forniti esattamente due quelli non usati che non sono già associati ad altre risorse di Azure. Il supplemento verrà usato quando si aggiorna periodicamente il Azure-SSIS IR.

- Devono essere entrambi statici di tipo standard. Per altri dettagli, vedere gli [SKU dell'indirizzo IP pubblico](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) .

- Devono avere un nome DNS. Se non è stato specificato un nome DNS durante la creazione, è possibile farlo in portale di Azure.

![Runtime di integrazione Azure-SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- Tali utenti e la rete virtuale devono trovarsi nella stessa sottoscrizione e nella stessa area.

### <a name="dns_server"></a>Configurare il server DNS 

Se è necessario usare il proprio server DNS in una rete virtuale unita dall'Azure-SSIS IR, assicurarsi che sia in grado di risolvere i nomi host di Azure globali (ad esempio, un BLOB di archiviazione di Azure denominato `<your storage account>.blob.core.windows.net`). 

Sono consigliati i passaggi seguenti: 

- Configurare il DNS personalizzato per l'invio di richieste al servizio DNS di Azure. È possibile inviare i record DNS non risolti all'indirizzo IP dei resolver ricorsivi di Azure (168.63.129.16) nel server DNS. 

- Configurare il DNS personalizzato come server DNS primario per la rete virtuale. Configurare DNS di Azure come server DNS secondario. Registrare l'indirizzo IP dei resolver ricorsivi di Azure (168.63.129.16) come server DNS secondario nel caso in cui il server DNS non sia disponibile. 

Per altre informazioni, vedere [risoluzione dei nomi che usa il proprio server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a>Configurare un NSG

Se è necessario implementare un NSG per la subnet usata dal Azure-SSIS IR, consentire il traffico in ingresso e in uscita attraverso le porte seguenti: 

| Direction | Protocollo di trasporto | Origine | Intervallo di porte di origine | Destinazione | Destination port range | Commenti |
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

Se, ad esempio, il Azure-SSIS IR si trova in `UK South`, si otterrebbe un elenco di intervalli IP di tag di servizio `BatchNodeManagement.UKSouth` dal [collegamento di download dell'intervallo IP dei tag del servizio](https://www.microsoft.com/en-us/download/details.aspx?id=56519) o tramite l'API di individuazione dei tag di [servizio](https://aka.ms/discoveryapi). Applicare quindi le seguenti UdR di route di intervallo IP correlate con il tipo di hop successivo come **Internet**.

![Impostazioni Azure Batch UDR](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Questo approccio comporta un costo aggiuntivo per la manutenzione. Controllare regolarmente l'intervallo di indirizzi IP e aggiungere nuovi intervalli IP nella UDR per evitare di suddividere il Azure-SSIS IR. Si consiglia di controllare l'intervallo di indirizzi IP mensilmente perché quando il nuovo IP viene visualizzato nel tag del servizio, l'indirizzo IP verrà applicato un altro mese. 

### <a name="resource-group"></a>Configurare il gruppo di risorse

Il runtime di integrazione Azure-SSIS deve creare alcune risorse di rete nello stesso gruppo di risorse della rete virtuale. Queste risorse includono:
- Un servizio di bilanciamento del carico di Azure, con il nome *\<Guid >-azurebatch-cloudserviceloadbalancer*.
- Un indirizzo IP pubblico di Azure, con il nome *\<Guid >-azurebatch-cloudservicepublicip*.
- Un gruppo di sicurezza del lavoro di rete, con il nome *\<Guid >-azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> È ora possibile importare indirizzi IP pubblici statici personalizzati per Azure-SSIS IR. In questo scenario vengono creati solo il servizio di bilanciamento del carico di Azure e il gruppo di sicurezza di rete nello stesso gruppo di risorse degli indirizzi IP pubblici statici invece che nella rete virtuale.

Queste risorse verranno create all'avvio del Azure-SSIS IR. Verranno eliminati quando si arresta il Azure-SSIS IR. Se si riportano gli indirizzi IP pubblici statici per Azure-SSIS IR, questi non verranno eliminati quando il Azure-SSIS IR si arresta. Per evitare di bloccare il Azure-SSIS IR l'arresto, non riutilizzare queste risorse di rete nelle altre risorse. 

Assicurarsi di non avere un blocco di risorsa per il gruppo di risorse o la sottoscrizione a cui appartengono la rete virtuale o gli indirizzi IP pubblici statici. Se si configura un blocco di sola lettura/eliminazione, l'avvio e l'arresto del Azure-SSIS IR avranno esito negativo o la risposta non verrà interrotta.

Assicurarsi di non avere un criterio di Azure che impedisce la creazione delle seguenti risorse nel gruppo di risorse/sottoscrizione a cui appartiene la rete virtuale/gli indirizzi IP pubblici statici: 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a> Domande frequenti

- Come è possibile proteggere l'indirizzo IP pubblico esposto nel Azure-SSIS IR per la connessione in ingresso? È possibile rimuovere l'indirizzo IP pubblico?
 
  Al momento, viene creato automaticamente un indirizzo IP pubblico quando il Azure-SSIS IR viene aggiunto a una rete virtuale. Si dispone di un NSG a livello di scheda di interfaccia di rete per consentire solo ai servizi di gestione Azure Batch per la connessione in ingresso al Azure-SSIS IR. È anche possibile specificare un NSG a livello di subnet per la protezione in ingresso.

  Se non si vuole che l'indirizzo IP pubblico venga esposto, è consigliabile configurare un runtime di integrazione [self-hosted come proxy per la Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) anziché creare un join della Azure-SSIS IR a una rete virtuale, se applicabile allo scenario.
 
- È possibile aggiungere l'indirizzo IP pubblico del Azure-SSIS IR all'elenco Consenti del firewall per le origini dati?

  È ora possibile importare indirizzi IP pubblici statici personalizzati per Azure-SSIS IR. In questo caso, è possibile aggiungere gli indirizzi IP all'elenco Consenti del firewall per le origini dati. È anche possibile prendere in considerazione altre opzioni per proteggere l'accesso ai dati dal Azure-SSIS IR a seconda dello scenario:

  - Se l'origine dati è locale, dopo la connessione di una rete virtuale alla rete locale e l'aggiunta del Azure-SSIS IR alla subnet della rete virtuale, è possibile aggiungere l'intervallo di indirizzi IP privati della subnet all'elenco Consenti del firewall per l'origine dati .
  - Se l'origine dati è un servizio di Azure che supporta gli endpoint di servizio della rete virtuale, è possibile configurare un endpoint del servizio di rete virtuale nella subnet della rete virtuale e aggiungere la Azure-SSIS IR a tale subnet. È quindi possibile aggiungere una regola della rete virtuale con tale subnet al firewall per l'origine dati.
  - Se l'origine dati è un servizio cloud non di Azure, è possibile usare un UDR per instradare il traffico in uscita dal Azure-SSIS IR a un firewall appliance virtuale di Azure tramite un indirizzo IP pubblico statico. È quindi possibile aggiungere l'indirizzo IP pubblico statico del firewall di appliance virtuale di Azure all'elenco Consenti del firewall per l'origine dati.
  - Se nessuna delle opzioni precedenti soddisfa le proprie esigenze, è consigliabile configurare un runtime di integrazione [self-hosted come proxy per la Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). È quindi possibile aggiungere l'indirizzo IP pubblico statico del computer che ospita il runtime di integrazione self-hosted all'elenco Consenti del firewall per l'origine dati.

- Perché è necessario fornire due indirizzi pubblici statici se si vuole usare il proprio Azure-SSIS IR?

  Azure-SSIS IR viene automaticamente aggiornato a intervalli regolari. I nuovi nodi vengono creati durante l'aggiornamento e quelli precedenti verranno eliminati. Tuttavia, per evitare tempi di inattività, i nodi precedenti non verranno eliminati finché quelli nuovi non saranno pronti. Il primo indirizzo IP pubblico statico usato dai nodi precedenti, quindi, non può essere rilasciato immediatamente ed è necessario il secondo indirizzo IP pubblico statico per creare i nuovi nodi.

- Ho introdotto indirizzi IP pubblici statici per Azure-SSIS IR, ma perché non è ancora in grado di accedere alle origini dati?

  - Verificare che i due indirizzi IP pubblici statici siano entrambi aggiunti all'elenco Consenti del firewall per le origini dati. Ogni volta che il Azure-SSIS IR viene aggiornato, l'indirizzo IP pubblico statico viene scambiato tra i due rilevati dall'utente. Se si aggiunge solo uno di questi elementi all'elenco Consenti, l'accesso ai dati per il Azure-SSIS IR verrà rotto dopo l'aggiornamento.
  - Se l'origine dati è un servizio di Azure, verificare che sia stata configurata con gli endpoint del servizio rete virtuale. In tal caso, il traffico da Azure-SSIS IR all'origine dati passerà a usare gli indirizzi IP privati gestiti dai servizi di Azure e l'aggiunta degli indirizzi IP pubblici statici all'elenco Consenti del firewall per l'origine dati non verrà applicata.

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

   1. Nel portale di Azure, nel menu a sinistra, selezionare **sottoscrizioni**. 

   1. Selezionare la propria sottoscrizione. 

   1. A sinistra selezionare provider di **risorse**e verificare che **Microsoft. batch** sia un provider registrato. 

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

   1. Nel menu a sinistra selezionare **controllo di accesso (IAM)** e selezionare la scheda **assegnazioni di ruolo** . 

   ![Pulsanti "Controllo di accesso" e "Aggiungi"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Selezionare **Aggiungi assegnazione ruolo**.

   1. Nella pagina **Aggiungi assegnazione ruolo** selezionare **collaboratore macchina virtuale classica**per **ruolo**. Nella casella **Seleziona** incollare **ddbf3205-c6bd-46AE-8127-60eb93363864**, quindi selezionare **Microsoft Azure batch** dall'elenco dei risultati della ricerca. 

   ![Risultati della ricerca nella pagina "Aggiungi assegnazione ruolo"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Selezionare **Save (Salva** ) per salvare le impostazioni e chiudere la pagina. 

   ![Salvare le impostazioni di accesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Verificare che venga visualizzato **Microsoft Azure Batch** nell'elenco dei collaboratori. 

   ![Confermare l'accesso di Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Verificare che il provider di Azure Batch sia registrato nella sottoscrizione di Azure che ha la rete virtuale. In alternativa, registrare il provider di Azure Batch. Se si dispone già di un account Azure Batch nella sottoscrizione, la sottoscrizione viene registrata per l'Azure Batch. Se si crea il runtime di integrazione Azure-SSIS nel portale di Data Factory, il provider di Azure Batch viene registrato automaticamente. 

   1. Nel portale di Azure, nel menu a sinistra, selezionare **sottoscrizioni**. 

   1. Selezionare la propria sottoscrizione. 

   1. A sinistra selezionare provider di **risorse**e verificare che **Microsoft. batch** sia un provider registrato. 

   ![Conferma dello stato "Registrato"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se non viene visualizzato **Microsoft.Batch** nell'elenco, per registrarlo [creare un account Azure Batch vuoto](../batch/batch-account-create-portal.md) nella propria sottoscrizione. È possibile eliminarlo in un secondo momento. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Aggiungere il runtime di integrazione Azure-SSIS alla rete virtuale

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

   1. Per **Nome della rete virtuale** selezionare il nome della rete virtuale. Deve corrispondere a quello usato per il server di database SQL di Azure con endpoint di servizio della rete virtuale o istanza gestita con endpoint privato per ospitare SSISDB. Oppure deve essere lo stesso connesso alla rete locale. In caso contrario, può trattarsi di qualsiasi rete virtuale per portare gli indirizzi IP pubblici statici per Azure-SSIS IR.

   1. Per **Nome subnet** selezionare il nome della subnet nella rete virtuale. Deve corrispondere a quello usato per il server di database SQL di Azure con gli endpoint del servizio rete virtuale per ospitare SSISDB. Oppure deve essere una subnet diversa da quella usata per l'istanza gestita con endpoint privato per l'hosting di SSISDB. In caso contrario, può essere una qualsiasi subnet per portare gli indirizzi IP pubblici statici per Azure-SSIS IR.

   1. Selezionare la casella di controllo **Bring static IP Public Addresss for your Azure-SSIS Integration Runtime** per scegliere se si desidera portare gli indirizzi IP pubblici statici per Azure-SSIS IR, in modo da poterli consentire nel firewall per le origini dati.

      Se si seleziona la casella di controllo, completare i passaggi seguenti.

      1. Per il **primo indirizzo IP pubblico statico**selezionare il primo indirizzo IP pubblico statico che [soddisfi i requisiti](#publicIP) per la Azure-SSIS IR. Se non è disponibile, fare clic su **Crea nuovo** collegamento per creare indirizzi IP pubblici statici in portale di Azure, quindi fare clic sul pulsante Aggiorna qui, in modo da poterli selezionare.
      
      1. Per il **secondo indirizzo IP pubblico statico**selezionare il secondo indirizzo IP pubblico statico che [soddisfa i requisiti](#publicIP) per la Azure-SSIS IR. Se non è disponibile, fare clic su **Crea nuovo** collegamento per creare indirizzi IP pubblici statici in portale di Azure, quindi fare clic sul pulsante Aggiorna qui, in modo da poterli selezionare.

   1. Selezionare **convalida VNet**. Se la convalida ha esito positivo, selezionare **continua**. 

   ![Impostazioni avanzate con una rete virtuale](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Nella sezione **Riepilogo** esaminare tutte le impostazioni per il Azure-SSIS IR. Quindi selezionare **Aggiorna**.

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

È possibile creare un runtime di integrazione Azure-SSIS e contemporaneamente aggiungerlo a una rete virtuale. Per istruzioni e script completi, vedere [creare un Azure-SSIS IR](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

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

Per aggiungere la Azure-SSIS IR a una rete virtuale, eseguire il comando `Set-AzDataFactoryV2IntegrationRuntime`: 

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
- [Esercitazione: distribuire pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). In questa esercitazione vengono fornite istruzioni dettagliate per la creazione del Azure-SSIS IR. Viene usato il database SQL di Azure per ospitare il catalogo SSIS. 
- [Creare un Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Questo articolo si espande nell'esercitazione. Fornisce istruzioni sull'uso del database SQL di Azure con gli endpoint del servizio di rete virtuale o l'istanza gestita in una rete virtuale per ospitare il catalogo SSIS. Viene illustrato come aggiungere il Azure-SSIS IR a una rete virtuale. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). Questo articolo illustra come ottenere informazioni sulle Azure-SSIS IR. Fornisce descrizioni dello stato per le informazioni restituite. 
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). Questo articolo illustra come arrestare, avviare o eliminare i Azure-SSIS IR. Viene anche illustrato come scalare orizzontalmente il runtime di integrazione Azure-SSIS tramite l'aggiunta di nodi.