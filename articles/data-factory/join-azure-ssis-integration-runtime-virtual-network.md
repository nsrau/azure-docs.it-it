---
title: Aggiungere un runtime di integrazione Azure-SSIS a una rete virtuale | Microsoft Docs
description: Informazioni su come aggiungere un runtime di integrazione Azure-SSIS a una rete virtuale di Azure.
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
ms.openlocfilehash: 3a1e272fa332c0bf0ee4e5ececa3edd83aec1d46
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543159"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale
Quando si usa SQL Server Integration Services (SSIS) in Azure Data Factory (ADF), è necessario aggiungere il Integration Runtime (IR) Azure-SSIS a una rete virtuale di Azure negli scenari seguenti: 

- Si vuole connettersi agli archivi dati locali dai pacchetti SSIS in esecuzione nel runtime di integrazione Azure-SSIS senza configurare o gestire un runtime di integrazione self-hosted come proxy. 

- Si vuole connettersi alle risorse dei servizi di Azure supportate con gli endpoint di servizio della rete virtuale dai pacchetti SSIS in esecuzione nel runtime di integrazione Azure-SSIS.

- Si sta ospitando il database del catalogo SSIS (SSISDB) nel database SQL di Azure con gli endpoint di servizio della rete virtuale/Istanza gestita in una rete virtuale. 

ADF consente di aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale creata tramite il modello di distribuzione classica o il modello di distribuzione Azure Resource Manager. 

> [!IMPORTANT]
> La rete virtuale classica è attualmente deprecata, rendendo quindi necessario l'uso della rete virtuale di Azure Resource Manager.  Se si usa già la rete virtuale classica, passare alla rete virtuale di Azure Resource Manager appena possibile.

## <a name="access-to-on-premises-data-stores"></a>Accesso agli archivi dati locali
Se i pacchetti SSIS accedono ad archivi dati locali, è possibile aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale connessa alla rete locale o configurare/gestire un runtime di integrazione self-hosted come proxy per il runtime di integrazione Azure-SSIS, vedere Configurare il runtime di integrazione [self-hosted come articolo del proxy per Azure-SSIS](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) . Quando si aggiunge il runtime di integrazione Azure-SSIS a una rete virtuale, è necessario tenere presenti alcuni aspetti importanti: 

- Se non è presente alcuna rete virtuale connessa alla rete locale, creare prima di tutto una [rete virtuale di Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) o una [rete virtuale classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) per aggiungere il runtime di integrazione Azure-SSIS. Configurare quindi una [connessione gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) o [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) da sito a sito da tale rete virtuale alla rete locale. 

- Se è già presente una rete virtuale classica o una rete virtuale di Azure Resource Manager connessa alla rete locale nello stesso percorso del runtime di integrazione Azure-SSIS, è possibile aggiungere il runtime di integrazione alla rete virtuale. 

- Se è già presente una rete virtuale classica connessa alla rete locale in un percorso diverso rispetto al runtime di integrazione Azure-SSIS, creare prima una [rete virtuale classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) per aggiungere il runtime di integrazione Azure-SSIS. Configurare quindi una connessione [da rete virtuale classica a rete virtuale classica](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md). In alternativa, è possibile creare una [rete virtuale di Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) per aggiungere il runtime di integrazione Azure-SSIS. Configurare quindi una connessione [da rete virtuale classica a rete virtuale di Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 
 
- Se è già presente una rete virtuale di Azure Resource Manager connessa alla rete locale in un percorso diverso rispetto al runtime di integrazione Azure-SSIS, creare prima una [rete virtuale di Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) per aggiungere il runtime di integrazione Azure-SSIS. Configurare quindi una connessione da rete virtuale di Azure Resource Manager a rete virtuale di Azure Resource Manager. In alternativa, è possibile creare una [rete virtuale classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) per aggiungere il runtime di integrazione Azure-SSIS. Configurare quindi una connessione [da rete virtuale classica a rete virtuale di Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 

## <a name="access-to-azure-services-with-virtual-network-service-endpoints"></a>Accesso ai servizi di Azure con gli endpoint del servizio rete virtuale
Se i pacchetti SSIS accedono alle risorse del servizio di Azure supportate con gli [endpoint del servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) e si vuole proteggere tali risorse nel runtime di integrazione Azure-SSIS, è possibile aggiungere il runtime di integrazione Azure-SSIS alla subnet della rete virtuale configurata con la rete virtuale endpoint di servizio e aggiungere la regola della rete virtuale alla risorsa del servizio di Azure per consentire l'accesso dalla stessa subnet.

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance"></a>Ospitare il database del catalogo SSIS nel database SQL di Azure con endpoint servizio di rete virtuale/Istanza gestita
Se si ospita il catalogo SSIS nel database SQL di Azure con gli endpoint servizio di rete virtuale, assicurarsi di aggiungere il runtime di integrazione Azure-SSIS alla stessa rete virtuale e subnet.

Unendo il runtime di integrazione Azure-SSIS alla stessa rete virtuale dell'istanza gestita, assicurarsi che il runtime di integrazione Azure-SSIS si trovi in una subnet diversa rispetto all'istanza gestita. Unendo il runtime di integrazione Azure-SSIS a una rete virtuale diversa da quella dell'istanza gestita, è consigliabile effettuare un peering di rete virtuale (limitato alla stessa area) o stabilire una connessione tra reti virtuali. Vedere [Connettere un'applicazione a Istanza gestita di database SQL di Azure](../sql-database/sql-database-managed-instance-connect-app.md).

In ogni caso, la rete virtuale può essere distribuita solo con il modello di distribuzione Azure Resource Manager.

Per altre informazioni, vedere le sezioni seguenti. 

## <a name="requirements-for-virtual-network-configuration"></a>Requisiti per la configurazione della rete virtuale
-   Assicurarsi che `Microsoft.Batch` sia un provider registrato nella sottoscrizione della subnet della rete virtuale che ospita il runtime di integrazione Azure-SSIS. Se si usa una rete virtuale classica, aggiungere anche `MicrosoftAzureBatch` al ruolo Collaboratore Macchina virtuale classica per tale rete virtuale. 

-   Assicurarsi di avere le autorizzazioni necessarie. Vedere [Autorizzazioni necessarie](#perms).

-   Selezionare la subnet appropriata per ospitare il runtime di integrazione Azure-SSIS. Vedere [Selezionare la subnet](#subnet). 

-   Se si usa il proprio server Domain Name Service (DNS) nella rete virtuale, vedere [Server Domain Name Service](#dns_server). 

-   Se si usa un gruppo di sicurezza di rete (NSG) nella subnet, vedere [Gruppo di sicurezza di rete](#nsg). 

-   Se si usa Azure ExpressRoute o si configura una route definita dall'utente, vedere [Usare Azure ExpressRoute o una route definita dall'utente](#route). 

-   Assicurarsi che il gruppo di risorse della rete virtuale sia in grado di creare ed eliminare determinate risorse di rete di Azure. Vedere [Requisiti per il gruppo di risorse](#resource-group). 

-   Se si Personalizza il runtime di integrazione Azure-SSIS come descritto nell'articolo [installazione personalizzata per](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) il runtime di integrazione Azure-SSIS, ai nodi del runtime di integrazione SSIS di Azure verranno allocati indirizzi IP privati da un intervallo predefinito di 172.16.0.0-172.31.255.255, quindi assicurarsi che l'indirizzo IP privato gli intervalli di indirizzi delle reti virtuali/locali non entrano in conflitto con questo intervallo.

Ecco un diagramma che illustra le connessioni necessarie per Azure-SSIS Integration Runtime:

![Runtime di integrazione Azure-SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a> Autorizzazioni necessarie

L'utente che crea il runtime di integrazione Azure-SSIS deve avere le autorizzazioni seguenti:

- Se si sta aggiungendo il runtime di integrazione SSIS a una rete virtuale di Azure Resource Manager, sono disponibili due opzioni:

  - Usare il ruolo predefinito *Collaboratore di rete*. Questo ruolo richiede l'autorizzazione _Microsoft.Network/\*_ , la quale ha un ambito molto maggiore del necessario.

  - Creare un ruolo personalizzato che include solo l'autorizzazione _Microsoft.Network/virtualNetworks/\*/join/action_ necessaria. 

- Se si sta aggiungendo il runtime di integrazione SSIS a una rete virtuale classica, è consigliabile usare il ruolo predefinito *Collaboratore Macchina virtuale classica*. In caso contrario, è necessario definire un ruolo personalizzato che include l'autorizzazione per accedere alla rete virtuale.

### <a name="subnet"></a> Selezionare la subnet
-   Non selezionare GatewaySubnet per distribuire un runtime di integrazione Azure-SSIS perché è una subnet dedicata per i gateway di rete virtuale. 

-   Verificare che la subnet selezionata abbia sufficiente spazio degli indirizzi per il runtime di integrazione Azure-SSIS. Lasciare almeno un numero di indirizzi IP disponibili pari a 2 * numero di nodi del runtime di integrazione. Azure riserva alcuni indirizzi IP all'interno di ogni subnet e questi indirizzi non possono essere usati. Il primo e l'ultimo indirizzo IP delle subnet sono riservati per motivi di conformità al protocollo, insieme ad altri tre indirizzi usati per i servizi di Azure. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

-   Non usare una subnet occupata in modo esclusivo da altri servizi di Azure, ad esempio Istanza gestita di database SQL o Servizio app. 

### <a name="dns_server"></a> Server Domain Name Service 
Se è necessario usare il proprio server DNS (Domain Name Services) in una rete virtuale unita in join dal runtime di integrazione SSIS di Azure, assicurarsi che sia in grado di risolvere i nomi host di Azure globali, `<your storage account>.blob.core.windows.net`ad esempio il nome di un BLOB di archiviazione di Azure. 

Sono consigliati i passaggi seguenti: 

-   Configurare un DNS personalizzato per inoltrare le richieste al servizio DNS di Azure. È possibile inoltrare i record DNS non risolti all'indirizzo IP dei resolver ricorsivi di Azure (168.63.129.16) nel proprio server DNS. 

-   Configurare il DNS personalizzato come primario e il DNS di Azure come secondario per la rete virtuale. Registrare l'indirizzo IP dei resolver ricorsivi di Azure (168.63.129.16) come server DNS secondario nel caso in cui il proprio server DNS non sia disponibile. 

Per altre informazioni, vedere [Risoluzione dei nomi con l'uso del proprio server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a> Gruppo di sicurezza di rete
Se è necessario implementare un gruppo di sicurezza di rete per la subnet usata dal runtime di integrazione Azure-SSIS, consentire il traffico in ingresso/in uscita tramite le porte seguenti: 

| Direzione | Protocollo di trasporto | Source (Sorgente) | Intervallo di porte di origine | Destination | Intervallo di porte di destinazione | Commenti |
|---|---|---|---|---|---|---|
| In ingresso | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (se si aggiunge il runtime di integrazione a una rete virtuale di Azure Resource Manager) <br/><br/>10100, 20100, 30100 (se si aggiunge il runtime di integrazione a una rete virtuale classica)| Il servizio di Data Factory usa queste porte per comunicare con i nodi del runtime di integrazione Azure-SSIS nella rete virtuale. <br/><br/> Anche se non è stato creato un gruppo di sicurezza di rete a livello di subnet, Data Factory configura sempre un gruppo di sicurezza di rete a livello di schede di interfaccia di rete collegato alle macchine virtuali che ospitano il runtime di integrazione Azure-SSIS. Il gruppo di sicurezza di rete a livello di scheda di interfaccia di rete consente solo il traffico in entrata dagli indirizzi IP di Data Factory nelle porte specificate. Anche se si aprono queste porte al traffico Internet a livello di subnet, il traffico proveniente da indirizzi IP diversi dagli indirizzi IP di Data Factory è bloccato a livello di scheda di interfaccia di rete. |
| In uscita | TCP | VirtualNetwork | * | AzureCloud | 443 | I nodi del runtime di integrazione Azure-SSIS nella rete virtuale usano questa porta per accedere ai servizi di Azure, ad esempio Archiviazione di Azure e Hub eventi di Azure. |
| In uscita | TCP | VirtualNetwork | * | Internet | 80 | I nodi del runtime di integrazione Azure-SSIS nella rete virtuale usano questa porta per scaricare l'elenco di revoche di certificati da Internet. |
| In uscita | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | I nodi del runtime di integrazione Azure-SSIS nella rete virtuale usano queste porte per accedere al database SSIS ospitato dal server di database SQL di Azure Se il criterio di connessione del server di database SQL di Azure è impostato su **proxy** anziché su **Reindirizzamento**, è necessaria solo la porta 1433. Questa regola di sicurezza in uscita non è applicabile a SSISDB ospitato dal Istanza gestita nella rete virtuale. |
||||||||

### <a name="route"></a> Usare Azure ExpressRoute o una route definita dall'utente
Quando si connette un circuito [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) all'infrastruttura di rete virtuale per estendere la rete locale ad Azure, una configurazione comune consiste nell'usare il tunneling forzato (annunciare una route BGP, 0.0.0.0/0 alla rete virtuale) che impone traffico Internet in uscita dal flusso della rete virtuale al dispositivo di rete locale per l'ispezione e la registrazione. 
 
In alternativa, è possibile definire le [route definite dall'utente (UDR)](../virtual-network/virtual-networks-udr-overview.md) per forzare il traffico Internet in uscita dalla subnet che ospita il runtime di integrazione Azure-SSIS a un'altra subnet, che ospita un'appliance di rete virtuale (NVA) come firewall o firewall di Azure per l'ispezione e la registrazione.
 
In entrambi i casi, la route di traffico interrompe la connettività in ingresso richiesta da servizi di Azure Data Factory dipendenti (in particolare Azure Batch Management Services) al runtime di integrazione Azure-SSIS nella rete virtuale. 
 
La soluzione consiste nel definire una o più route definite dall'utente (UDR) nella subnet che contiene il runtime di integrazione Azure-SSIS. 

- È possibile applicare una route 0.0.0.0/0 con il tipo di hop successivo come **Internet** nella subnet che ospita il runtime di integrazione Azure-SSIS nello scenario ExpressRoute di Azure o modificare la Route 0.0.0.0/0 esistente dal tipo di hop successivo come appliance virtuale a Internet in **appliance virtuale** di **rete** scenario.

![Aggiungere una route](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)
- Se si è preoccupati di perdere la possibilità di controllare il traffico Internet in uscita da tale subnet. È possibile definire UdR specifici per instradare solo il traffico tra Azure Batch Management Services e il runtime di integrazione Azure-SSIS con il tipo di hop successivo **Internet**.
ad esempio Se il runtime di integrazione Azure-SSIS si `UK South`trova in, è necessario ottenere l'elenco di intervalli IP del `BatchNodeManagement.UKSouth` tag del servizio dal [collegamento di download dell'intervallo IP dei tag](https://www.microsoft.com/en-us/download/details.aspx?id=56519) del servizio o tramite l'API di individuazione dei tag di [servizio](https://aka.ms/discoveryapi). Quindi applicare sotto UdR di route di intervallo IP correlato con il tipo di hop successivo **Internet**.

![Impostazioni UDR di AzureBatch](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)
> [!NOTE]
> Il costo di manutenzione aggiuntivo di questo approccio è necessario per controllare regolarmente l'intervallo di indirizzi IP e aggiungere un nuovo intervallo di indirizzi IP nella UDR per evitare interruzioni del runtime di integrazione SSIS di Azure. Quando viene visualizzato un nuovo indirizzo IP nel tag del servizio, il nuovo IP verrà applicato per un altro mese. Si consiglia quindi di controllare l'intervallo di indirizzi IP mensili. 

### <a name="resource-group"></a> Requisiti per il gruppo di risorse
-   Il runtime di integrazione Azure-SSIS deve creare alcune risorse di rete nello stesso gruppo di risorse della rete virtuale. Tali risorse includono le seguenti:
    -   Un servizio di bilanciamento del carico di Azure, con il nome  *\<GUID >-azurebatch-cloudserviceloadbalancer*.
    -   Un indirizzo IP pubblico di Azure, con il nome  *\<GUID >-azurebatch-cloudservicepublicip*.
    -   Un gruppo di sicurezza del lavoro di rete, con il nome  *\<GUID >-azurebatch-cloudservicenetworksecuritygroup*. 

    Queste risorse verranno create quando si avvia e si elimina l'integrazione IR quando si arresta. Non riutilizzarli nelle altre risorse. in caso contrario, il blocco IR verrà interrotto. 

-   Assicurarsi di non avere blocchi di risorse per il gruppo di risorse o la sottoscrizione a cui appartiene la rete virtuale. Se si configura un blocco di sola lettura o un blocco di eliminazione, l'avvio e l'arresto del runtime di integrazione possono avere esito negativo o interrompere la risposta. 

-   Verificare di non avere criteri di Azure che impediscano la creazione delle risorse seguenti nel gruppo di risorse o nella sottoscrizione a cui appartiene la rete virtuale: 
    -   Microsoft.Network/LoadBalancers 
    -   Microsoft.Network/NetworkSecurityGroups 
    -   Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a> Domande frequenti

- Come si protegge l'indirizzo IP pubblico esposto sul runtime di integrazione SSIS di Azure per la connessione in ingresso? Ed è possibile rimuovere l'indirizzo IP pubblico?
 
    Al momento, l'indirizzo IP pubblico verrà creato automaticamente quando si aggiunge il runtime di integrazione Azure-SSIS VNet. Sono disponibili NSG a livello di scheda di interfaccia di rete per consentire solo ai servizi di gestione Azure Batch di connettersi al runtime di integrazione SSIS di Azure ed è possibile specificare NSG a livello di subnet anche per la protezione in ingresso.

    Se non si vuole che l'indirizzo IP pubblico venga esposto, è possibile prendere in considerazione l'approccio per configurare il runtime di integrazione [self-hosted come proxy per](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) il runtime di integrazione Azure-SSIS anziché VNet se applicato nello scenario.
 
- È presente un indirizzo IP statico del runtime di integrazione SSIS di Azure che può essere inserito nell'elenco Consenti accesso ai firewall per accedere all'origine dati?
 
    - Se l'origine dati è locale, dopo la connessione della rete virtuale alla rete locale e l'aggiunta del runtime di integrazione Azure-SSIS alla subnet della rete virtuale, è possibile inserire l'intervallo IP della subnet in elenco Consenti.
    - Se l'origine dati è supportata dal servizio di Azure con l'endpoint del servizio di rete virtuale, è possibile configurare il punto di servizio della rete virtuale nella rete virtuale e aggiungere il runtime di integrazione Azure-SSIS alla subnet della rete virtuale, quindi sarà possibile usare la regola della rete virtuale dei servizi di Azure anziché di un intervallo di indirizzi IP per consentire l'accesso.
    - Se l'origine dati è un'altra origine dati cloud, è possibile usare UDR per instradare il traffico in uscita dal runtime di integrazione SSIS di Azure all'appliance virtuale di Azure con un indirizzo IP pubblico statico, in modo da poter inserire l'indirizzo IP pubblico di appliance virtuale di Azure o il firewall di Azure nell'elenco Consenti.
    - Se nessuno dei precedenti soddisfa i requisiti, è possibile valutare se l'accesso all'origine dati può essere eseguito tramite configura il runtime di integrazione [self-hosted come proxy per](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)il runtime di integrazione Azure-SSIS, quindi è possibile inserire l'indirizzo IP del computer che ospita il runtime di integrazione self-hosted nell'elenco Consenti anziché Aggiunta del runtime di integrazione SSIS di Azure in VNet.

## <a name="azure-portal-data-factory-ui"></a>Portale di Azure (interfaccia utente di Data Factory)
Questa sezione illustra come aggiungere un runtime Azure-SSIS esistente a una rete virtuale (classica o di Azure Resource Manager) tramite il portale di Azure e l'interfaccia utente di Data Factory. Prima di aggiungere il runtime di integrazione Azure-SSIS alla rete virtuale, è necessario configurarla in modo appropriato. Eseguire una delle due sezioni successive, a seconda del tipo di rete virtuale (classica o di Azure Resource Manager). Continuare quindi con la terza sezione per aggiungere il runtime di integrazione Azure-SSIS alla rete virtuale. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Usare il portale per configurare una rete virtuale di Azure Resource Manager
Prima di aggiungere un runtime di integrazione Azure-SSIS alla rete virtuale, è necessario configurarla. 

1. Avviare Microsoft Edge o Google Chrome. L'interfaccia utente di Data Factory attualmente è supportata solo in questi Web browser. 

1. Accedere al [portale di Azure](https://portal.azure.com). 

1. Selezionare **Altri servizi**. Filtrare e selezionare **Reti virtuali**. 

1. Filtrare e selezionare la propria rete virtuale dall'elenco. 

1. Nella pagina **Rete virtuale** selezionare **Proprietà**. 

1. Fare clic sul pulsante di copia per l'**ID RISORSA** per copiare l'ID risorsa per la rete virtuale negli Appunti. Salvare l'ID dagli Appunti in OneNote o in un file. 

1. Selezionare **Subnet** nel menu a sinistra. Assicurarsi che il numero di **indirizzi disponibili** sia maggiore del numero di nodi nel runtime di integrazione Azure-SSIS. 

1. Verificare che il provider di Azure Batch sia registrato nella sottoscrizione di Azure che ha la rete virtuale. In alternativa, registrare il provider di Azure Batch. Se si dispone già di un account Azure Batch nella sottoscrizione, allora la sottoscrizione viene registrata per Azure Batch. Se si crea il runtime di integrazione Azure-SSIS nel portale di Data Factory, il provider di Azure Batch viene registrato automaticamente. 

   a. Nel portale di Azure selezionare **Sottoscrizioni** nel menu a sinistra. 

   b. Selezionare la propria sottoscrizione. 

   c. Selezionare **Provider di risorse** a sinistra e confermare che **Microsoft.Batch** è un provider registrato. 

   ![Conferma dello stato "Registrato"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se non viene visualizzato **Microsoft.Batch** nell'elenco, per registrarlo [creare un account Azure Batch vuoto](../batch/batch-account-create-portal.md) nella propria sottoscrizione. È possibile eliminarlo in un secondo momento. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Usare il portale per configurare una rete virtuale classica
Prima di aggiungere un runtime di integrazione Azure-SSIS alla rete virtuale, è necessario configurarla. 

1. Avviare Microsoft Edge o Google Chrome. L'interfaccia utente di Data Factory attualmente è supportata solo in questi Web browser. 

1. Accedere al [portale di Azure](https://portal.azure.com). 

1. Selezionare **Altri servizi**. Filtrare e selezionare **Reti virtuali (classiche)** . 

1. Filtrare e selezionare la propria rete virtuale dall'elenco. 

1. Nella pagina **Rete virtuale (classico)** selezionare **Proprietà**. 

   ![ID risorsa della rete virtuale classica](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Fare clic sul pulsante di copia per l'**ID RISORSA** per copiare l'ID risorsa della rete classica negli Appunti. Salvare l'ID dagli Appunti in OneNote o in un file. 

1. Selezionare **Subnet** nel menu a sinistra. Assicurarsi che il numero di **indirizzi disponibili** sia maggiore del numero di nodi nel runtime di integrazione Azure-SSIS. 

   ![Numero di indirizzi disponibili nella rete virtuale](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Aggiungere **MicrosoftAzureBatch** al ruolo **Collaboratore Macchina virtuale classica** per la rete virtuale. 

    a. Selezionare **Controllo di accesso (IAM)** nel menu a sinistra e selezionare la scheda **Assegnazioni di ruolo**. 

    ![Pulsanti "Controllo di accesso" e "Aggiungi"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Selezionare **Aggiungi assegnazione di ruolo**.

    c. Nella pagina **Aggiungi un'assegnazione di ruolo** selezionare **Collaboratore Macchina virtuale classica** per **Ruolo**. Incollare **ddbf3205-c6bd-46ae-8127-60eb93363864** nella casella di testo **Seleziona** e quindi selezionare **Microsoft Azure Batch** nell'elenco di risultati della ricerca. 

    ![Risultati della ricerca nella pagina "Aggiungi un'assegnazione di ruolo"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. Selezionare **Salva** per salvare le impostazioni e chiudere la pagina. 

    ![Salvare le impostazioni di accesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Verificare che venga visualizzato **Microsoft Azure Batch** nell'elenco dei collaboratori. 

    ![Confermare l'accesso di Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Verificare che il provider di Azure Batch sia registrato nella sottoscrizione di Azure che ha la rete virtuale. In alternativa, registrare il provider di Azure Batch. Se si dispone già di un account Azure Batch nella sottoscrizione, allora la sottoscrizione viene registrata per Azure Batch. Se si crea il runtime di integrazione Azure-SSIS nel portale di Data Factory, il provider di Azure Batch viene registrato automaticamente. 

   a. Nel portale di Azure selezionare **Sottoscrizioni** nel menu a sinistra. 

   b. Selezionare la propria sottoscrizione. 

   c. Selezionare **Provider di risorse** a sinistra e confermare che **Microsoft.Batch** è un provider registrato. 

   ![Conferma dello stato "Registrato"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se non viene visualizzato **Microsoft.Batch** nell'elenco, per registrarlo [creare un account Azure Batch vuoto](../batch/batch-account-create-portal.md) nella propria sottoscrizione. È possibile eliminarlo in un secondo momento. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Aggiungere il runtime di integrazione Azure-SSIS alla rete virtuale
1. Avviare Microsoft Edge o Google Chrome. L'interfaccia utente di Data Factory attualmente è supportata solo in questi Web browser. 

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Data factory** nel menu a sinistra. Se la voce **Data factory** non è visualizzata nel menu, selezionare **Altri servizi** e quindi **Data factory** nella sezione **INTELLIGENCE E ANALISI**. 

   ![Elenco di data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selezionare la data factory con il runtime di integrazione Azure-SSIS nell'elenco. Verrà visualizzata la home page della data factory. Selezionare il riquadro **Creare e distribuire**. Verrà visualizzata l'interfaccia utente di Data Factory in una scheda separata. 

   ![Home page di Data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Nell'interfaccia utente di Data Factory passare alla scheda **Modifica**, selezionare **Connessioni** e passare alla scheda **Runtime di integrazione**. 

   ![Scheda "Runtime di integrazione"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Se il runtime di integrazione Azure-SSIS è in esecuzione, nell'elenco dei runtime di integrazione selezionare il pulsante **Arresto** nella colonna **Azioni** corrispondente al runtime di integrazione Azure-SSIS di interesse. È possibile modificare un runtime di integrazione solo dopo averlo arrestato. 

   ![Arrestare il runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Nell'elenco dei runtime di integrazione selezionare il pulsante **Modifica** nella colonna **Azioni** corrispondente al runtime di integrazione Azure-SSIS di interesse. 

   ![Modificare il runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Nel pannello **Integration Runtime installazione** , fare clic sul pulsante **Avanti** per passare alle pagine **Impostazioni generali** e **Impostazioni SQL** . 

1. Nella pagina **Impostazioni avanzate** eseguire le operazioni seguenti: 

   a. Selezionare la casella **di controllo Seleziona un VNet** .... 

   b. Per **Subscription (sottoscrizione**) selezionare la sottoscrizione di Azure in cui è possibile selezionare una rete virtuale esistente. 
  
   c. In **Nome della rete virtuale** selezionare la rete virtuale. 

   d. In **Nome subnet** selezionare la subnet nella rete virtuale. 

   e. Se si vuole anche configurare/gestire un runtime di integrazione self-hosted come proxy per il runtime di integrazione Azure-SSIS, controllare la casella di controllo Configura **self-hosted...** e vedere Configurare il runtime di integrazione [self-hosted come proxy per Azure-SSIS](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) .

   f. Fare clic sul pulsante **convalida VNet** e, in caso di esito positivo, fare clic sul pulsante **Avanti** . 

   ![Impostazioni avanzate per la configurazione del runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Nella pagina **Riepilogo** esaminare tutte le impostazioni per il runtime di integrazione Azure-SSIS e fare clic sul pulsante **Aggiorna** .

1. A questo punto, è possibile avviare il runtime di integrazione Azure-SSIS facendo clic sul pulsante **Avvia** nella colonna **azioni** per il runtime di integrazione Azure-SSIS. Sono necessari circa 20-30 minuti per avviare il runtime di integrazione Azure-SSIS che aggiunge una rete virtuale. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>Configurare una rete virtuale
Prima di aggiungere un runtime di integrazione Azure-SSIS a una rete virtuale, è necessario configurare la rete virtuale. Per configurare automaticamente le autorizzazioni/impostazioni della rete virtuale per l'aggiunta del runtime di integrazione Azure-SSIS, aggiungere lo script seguente:

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
È possibile creare un runtime di integrazione Azure-SSIS e contemporaneamente aggiungerlo a una rete virtuale. Per istruzioni e per lo script completo, vedere [Creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Aggiungere un runtime di integrazione Azure-SSIS esistente a una rete virtuale
Lo script nell'articolo [Creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md) mostra come creare un runtime di integrazione Azure-SSIS e aggiungerlo a una rete virtuale nello stesso script. Se è disponibile un runtime di integrazione Azure-SSIS esistente, eseguire i passaggi seguenti per aggiungerlo alla rete virtuale: 
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
Arrestare il runtime di integrazione Azure-SSIS prima di aggiungerlo a una rete virtuale. Questo comando rilascia tutti i nodi e arresta la fatturazione:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configurare le impostazioni della rete virtuale per aggiungere il runtime di integrazione Azure-SSIS
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
Per configurare l'aggiunta del runtime di integrazione Azure-SSIS alla rete virtuale, eseguire il comando `Set-AzDataFactoryV2IntegrationRuntime`: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Avviare il runtime di integrazione Azure-SSIS
Per avviare il runtime di integrazione Azure-SSIS, eseguire il comando seguente: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

L'esecuzione di questo comando richiede dai 20 ai 30 minuti.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul runtime di integrazione SSIS di Azure, vedere gli argomenti seguenti: 
- [Runtime di integrazione Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Questo articolo fornisce informazioni concettuali sui runtime di integrazione in generale, incluso il runtime di integrazione Azure-SSIS. 
- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). Questa esercitazione include istruzioni dettagliate per la creazione del runtime di integrazione Azure-SSIS. Viene usato il database SQL di Azure per ospitare il catalogo SSIS. 
- [Creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo espande l'esercitazione e fornisce istruzioni sull'uso del database SQL di Azure con gli endpoint di servizio della rete virtuale/Istanza gestita in una rete virtuale per ospitare il catalogo SSIS e l'aggiunta del runtime di integrazione Azure-SSIS a una rete virtuale. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). Questo articolo illustra come recuperare informazioni sul runtime di integrazione Azure-SSIS e fornisce descrizioni dello stato nelle informazioni restituite. 
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). Questo articolo illustra come arrestare, avviare o eliminare il runtime di integrazione Azure-SSIS. Viene anche illustrato come scalare orizzontalmente il runtime di integrazione Azure-SSIS tramite l'aggiunta di nodi.
