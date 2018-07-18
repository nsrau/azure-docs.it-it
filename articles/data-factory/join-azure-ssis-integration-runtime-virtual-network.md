---
title: Aggiungere un runtime di integrazione Azure-SSIS a una rete virtuale | Microsoft Docs
description: Informazioni su come aggiungere un runtime di integrazione Azure-SSIS a una rete virtuale di Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: eae350f751788eb09271e70f71f79b12e27c4e16
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061402"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale
Aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale di Azure negli scenari seguenti: 

- Si intende connettersi ad archivi dati locali da pacchetti SSIS eseguiti in un runtime di integrazione SSIS di Azure. 

- Si ospita il database del catalogo SSIS (SQL Server Integration Services) nel database SQL di Azure con endpoint del servizio di rete virtuale/Istanza gestita (anteprima). 

 Azure Data Factory consente di aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale creata con il modello di distribuzione classico o con il modello di distribuzione Azure Resource Manager. 

## <a name="access-to-on-premises-data-stores"></a>Accesso agli archivi dati locali
Se i pacchetti SSIS consentono di accedere solo ad archivi dati cloud pubblici, non è necessario aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale. Se i pacchetti SSIS consentono di accedere ad archivi dati locali, è necessario aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale connessa alla rete locale. 

Ecco alcuni elementi importanti da considerare: 

- Se non è presente alcuna rete virtuale connessa alla rete locale, creare prima di tutto una [rete virtuale di Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) o una [rete virtuale classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) per aggiungere il runtime di integrazione Azure-SSIS. Configurare quindi una [connessione gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) o [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) da sito a sito da tale rete virtuale alla rete locale. 

- Se è già presente una rete virtuale classica o una rete virtuale di Azure Resource Manager connessa alla rete locale nello stesso percorso del runtime di integrazione Azure-SSIS, è possibile aggiungere il runtime di integrazione alla rete virtuale. 

- Se è già presente una rete virtuale classica connessa alla rete locale in un percorso diverso rispetto al runtime di integrazione Azure-SSIS, creare prima una [rete virtuale classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) per aggiungere il runtime di integrazione Azure-SSIS. Configurare quindi una connessione [da rete virtuale classica a rete virtuale classica](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md). In alternativa, è possibile creare una [rete virtuale di Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) per aggiungere il runtime di integrazione Azure-SSIS. Configurare quindi una connessione [da rete virtuale classica a rete virtuale di Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 
 
- Se è già presente una rete virtuale di Azure Resource Manager connessa alla rete locale in un percorso diverso rispetto al runtime di integrazione Azure-SSIS, creare prima una [rete virtuale di Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) per aggiungere il runtime di integrazione Azure-SSIS. Configurare quindi una connessione da rete virtuale di Azure Resource Manager a rete virtuale di Azure Resource Manager. In alternativa, è possibile creare una [rete virtuale classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) per aggiungere il runtime di integrazione Azure-SSIS. Configurare quindi una connessione [da rete virtuale classica a rete virtuale di Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance-preview"></a>Ospitare il database del catalogo SSIS nel database SQL di Azure con endpoint del servizio di rete virtuale/Istanza gestita (anteprima)
Se il catalogo SSIS è ospitato nel database SQL di Azure con endpoint del servizio di rete virtuale o Istanza gestita (anteprima), è possibile aggiungere il runtime di integrazione Azure-SSIS per: 

- La stessa rete virtuale 
- Un'altra rete virtuale che ha una connessione da rete a rete con la rete usata per il database SQL di Azure con endpoint del servizio di rete virtuale/Istanza gestita (anteprima) 

Unendo il runtime di integrazione Azure-SSIS alla stessa rete virtuale di Istanza gestita, assicurarsi che il runtime di integrazione Azure-SSIS si trovi in una subnet diversa rispetto a Istanza gestita. Unendo il runtime di integrazione Azure-SSIS a una rete virtuale diversa da quella di Istanza gestita, è consigliabile effettuare un peering di rete virtuale (limitato alla stessa area) o stabilire una connessione tra reti virtuali. Vedere [Connettere un'applicazione a Istanza gestita di database SQL di Azure](../sql-database/sql-database-managed-instance-connect-app.md).

La rete virtuale può essere distribuita con il modello di distribuzione classico o con il modello di distribuzione Azure Resource Manager.

Per altre informazioni, vedere le sezioni seguenti. 

## <a name="requirements-for-virtual-network-configuration"></a>Requisiti per la configurazione della rete virtuale
-   Assicurarsi che `Microsoft.Batch` sia un provider registrato nella sottoscrizione della subnet della rete virtuale che ospita il runtime di integrazione Azure-SSIS. Se si usa una rete virtuale classica, aggiungere anche `MicrosoftAzureBatch` al ruolo Collaboratore Macchina virtuale classica per tale rete virtuale. 

-   Selezionare la subnet appropriata per ospitare il runtime di integrazione Azure-SSIS. Vedere [Selezionare la subnet](#subnet). 

-   Se si usa il proprio server Domain Name Service (DNS) nella rete virtuale, vedere [Server Domain Name Service](#dns_server). 

-   Se si usa un gruppo di sicurezza di rete (NSG) nella subnet, vedere [Gruppo di sicurezza di rete](#nsg). 

-   Se si usa Azure ExpressRoute o si configura una route definita dall'utente, vedere [Usare Azure ExpressRoute o una route definita dall'utente](#route). 

-   Assicurarsi che il gruppo di risorse della rete virtuale sia in grado di creare ed eliminare determinate risorse di rete di Azure. Vedere [Requisiti per il gruppo di risorse](#resource-group). 

### <a name="subnet"></a> Selezionare la subnet
-   Non selezionare GatewaySubnet per distribuire un runtime di integrazione Azure-SSIS perché è una subnet dedicata per i gateway di rete virtuale. 

-   Verificare che la subnet selezionata abbia sufficiente spazio degli indirizzi per il runtime di integrazione Azure-SSIS. Lasciare almeno un numero di indirizzi IP disponibili pari a 2 * numero di nodi del runtime di integrazione. Azure riserva alcuni indirizzi IP all'interno di ogni subnet e questi indirizzi non possono essere usati. Il primo e l'ultimo indirizzo IP delle subnet sono riservati per motivi di conformità al protocollo, insieme ad altri tre indirizzi usati per i servizi di Azure. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

-   Non usare una subnet occupata in modo esclusivo da altri servizi di Azure, ad esempio, Istanza gestita di database SQL (anteprima), Servizio app e così via. 

### <a name="dns_server"></a> Server Domain Name Service 
Se è necessario usare il proprio server Domain Name Service (DNS) in una rete virtuale unita tramite il runtime di integrazione Azure-SSIS, assicurarsi che sia in grado di risolvere i nomi host di Azure pubblico (ad esempio, un nome del BLOB di Archiviazione di Azure, `<your storage account>.blob.core.windows.net`). 

Sono consigliati i passaggi seguenti: 

-   Configurare un DNS personalizzato per inoltrare le richieste al servizio DNS di Azure. È possibile inoltrare i record DNS non risolti all'indirizzo IP dei resolver ricorsivi di Azure (168.63.129.16) nel proprio server DNS. 

-   Configurare il DNS personalizzato come primario e il DNS di Azure come secondario per la rete virtuale. Registrare l'indirizzo IP dei resolver ricorsivi di Azure (168.63.129.16) come server DNS secondario nel caso in cui il proprio server DNS non sia disponibile. 

Per altre informazioni, vedere [Risoluzione dei nomi con l'uso del proprio server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a> Gruppo di sicurezza di rete
Se è necessario implementare un gruppo di sicurezza di rete in una rete virtuale aggiunta tramite il runtime di integrazione Azure-SSIS, consentire il traffico in ingresso/in uscita tramite le porte seguenti: 

| Direzione | Protocollo di trasporto | Sorgente | Intervallo di porte di origine | Destination | Intervallo di porte di destinazione | Commenti |
|---|---|---|---|---|---|---|
| In ingresso | TCP | Internet | * | VirtualNetwork | 29876, 29877 (se si aggiunge il runtime di integrazione a una rete virtuale di Azure Resource Manager) <br/><br/>10100, 20100, 30100 (se si aggiunge il runtime di integrazione a una rete virtuale classica)| Il servizio di Data Factory usa queste porte per comunicare con i nodi del runtime di integrazione Azure-SSIS nella rete virtuale. <br/><br/> Anche se non è stato specificato un gruppo di sicurezza di rete, Data Factory configura sempre un gruppo di sicurezza di rete a livello di schede di interfaccia di rete (NIC) collegato alle macchine virtuali che ospitano il runtime di integrazione Azure-SSIS. È consentito solo il traffico in entrata dagli indirizzi IP di Data Factory. Anche se si aprono queste porte al traffico Internet, il traffico proveniente da indirizzi IP diversi dagli indirizzi IP di Data Factory è bloccato a livello di scheda di interfaccia di rete. |
| In uscita | TCP | VirtualNetwork | * | Internet | 443 | I nodi del runtime di integrazione Azure-SSIS nella rete virtuale usano questa porta per accedere ai servizi di Azure, ad esempio Archiviazione di Azure e Hub eventi di Azure. |
| In uscita | TCP | VirtualNetwork | * | Internet o Sql | 1433, 11000-11999, 14000-14999 | I nodi del runtime di integrazione Azure-SSIS nella rete virtuale usano queste porte per accedere al database SSIS ospitato dal server di database SQL di Azure. Questo non è applicabile per il database SSIS ospitato da Istanza gestita (anteprima). |
||||||||

### <a name="route"></a> Usare Azure ExpressRoute o una route definita dall'utente
È possibile connettere un circuito [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) all'infrastruttura di rete virtuale per estendere la rete locale ad Azure. 

Una configurazione comune consiste nell'usare il tunneling forzato (annunciando una route BGP, 0.0.0.0/0 alla rete virtuale) che impone l'instradamento del traffico Internet in uscita dal flusso della rete virtuale all'appliance di rete locale per l'ispezione e la registrazione. Questo flusso di traffico interrompe la connettività tra il runtime di integrazione Azure-SSIS nella rete virtuale con i servizi di Azure Data Factory dipendenti. La soluzione consiste nel configurare una o più [route definite dall'utente (UDR)](../virtual-network/virtual-networks-udr-overview.md) nella subnet contenente il runtime di integrazione Azure-SSIS. Un'UDR definisce le route specifiche della subnet che vengono accettate in alternativa alla route BGP. 

In alternativa, è possibile definire route definite dall'utente per imporre l'instradamento del traffico Internet in uscita dalla subnet che ospita il runtime di integrazione Azure-SSIS a un'altra subnet che ospita un'appliance di rete virtuale come un firewall o un host di rete perimetrale per l'ispezione e la registrazione. 

In entrambi i casi applicare una route 0.0.0.0/0 con il tipo di hop successivo impostato su **Internet** nella subnet che ospita il runtime di integrazione Azure-SSIS, in modo che la comunicazione tra il servizio di Data Factory e il runtime di integrazione Azure-SSIS abbia esito positivo. 

![Aggiungere una route](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Se si teme di non poter più ispezionare il traffico Internet in uscita dalla subnet, è anche possibile aggiungere una regola del gruppo di sicurezza di rete (NSG) nella subnet per limitare le destinazioni in uscita agli [indirizzi IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

Per un esempio, vedere [questo script di PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c). È necessario eseguire lo script ogni settimana per mantenere aggiornato l'elenco di indirizzi IP del data center di Azure. 

### <a name="resource-group"></a> Requisiti per il gruppo di risorse
-   Il runtime di integrazione Azure-SSIS deve creare alcune risorse di rete nello stesso gruppo di risorse della rete virtuale. Tali risorse includono le seguenti:
    -   Un servizio di bilanciamento del carico di Azure, denominato *<Guid>-azurebatch-cloudserviceloadbalancer*.
    -   Un indirizzo IP pubblico di Azure, denominato *<Guid>-azurebatch-cloudservicepublicip*.
    -   Un gruppo di sicurezza di lavoro di rete, denominato *<Guid>-azurebatch-cloudservicenetworksecuritygroup*. 

-   Assicurarsi di non avere blocchi di risorse per il gruppo di risorse o la sottoscrizione a cui appartiene la rete virtuale. Se si configura un blocco di sola lettura o un blocco di eliminazione, l'avvio e l'arresto del runtime di integrazione può avere esito negativo o interrompersi. 

-   Verificare di non avere criteri di Azure che impediscano la creazione delle risorse seguenti nel gruppo di risorse o nella sottoscrizione a cui appartiene la rete virtuale: 
    -   Microsoft.Network/LoadBalancers 
    -   Microsoft.Network/NetworkSecurityGroups 
    -   Microsoft.Network/PublicIPAddresses 

## <a name="azure-portal-data-factory-ui"></a>Portale di Azure (interfaccia utente di Data Factory)
Questa sezione illustra come aggiungere un runtime Azure-SSIS esistente a una rete virtuale (classica o di Azure Resource Manager) tramite il portale di Azure e l'interfaccia utente di Data Factory. Prima di aggiungere il runtime di integrazione Azure-SSIS alla rete virtuale, è necessario configurarla in modo appropriato. Eseguire una delle due sezioni successive, a seconda del tipo di rete virtuale (classica o di Azure Resource Manager). Continuare quindi con la terza sezione per aggiungere il runtime di integrazione Azure-SSIS alla rete virtuale. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Usare il portale per configurare una rete virtuale di Azure Resource Manager
Prima di aggiungere un runtime di integrazione Azure-SSIS alla rete virtuale, è necessario configurarla. 

1. Avviare Microsoft Edge o Google Chrome. L'interfaccia utente di Data Factory attualmente è supportata solo in questi Web browser. 

2. Accedere al [portale di Azure](https://portal.azure.com). 

3. Selezionare **Altri servizi**. Filtrare e selezionare **Reti virtuali**. 

4. Filtrare e selezionare la propria rete virtuale dall'elenco. 

5. Nella pagina **Rete virtuale** selezionare **Proprietà**. 

6. Fare clic sul pulsante di copia per l'**ID RISORSA** per copiare l'ID risorsa per la rete virtuale negli Appunti. Salvare l'ID dagli Appunti in OneNote o in un file. 

7. Selezionare **Subnet** nel menu a sinistra. Assicurarsi che il numero di **indirizzi disponibili** sia maggiore del numero di nodi nel runtime di integrazione Azure-SSIS. 

8. Verificare che il provider di Azure Batch sia registrato nella sottoscrizione di Azure che ha la rete virtuale. In alternativa, registrare il provider di Azure Batch. Se si dispone già di un account Azure Batch nella sottoscrizione, allora la sottoscrizione viene registrata per Azure Batch. Se si crea il runtime di integrazione Azure-SSIS nel portale di Data Factory, il provider di Azure Batch viene registrato automaticamente. 

   a. Nel portale di Azure selezionare **Sottoscrizioni** nel menu a sinistra. 

   b. Selezionare la propria sottoscrizione. 

   c. Selezionare **Provider di risorse** a sinistra e confermare che **Microsoft.Batch** è un provider registrato. 

   ![Conferma dello stato "Registrato"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se non viene visualizzato **Microsoft.Batch** nell'elenco, per registrarlo [creare un account Azure Batch vuoto](../batch/batch-account-create-portal.md) nella propria sottoscrizione. È possibile eliminarlo in un secondo momento. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Usare il portale per configurare una rete virtuale classica
Prima di aggiungere un runtime di integrazione Azure-SSIS alla rete virtuale, è necessario configurarla. 

1. Avviare Microsoft Edge o Google Chrome. L'interfaccia utente di Data Factory attualmente è supportata solo in questi Web browser. 

2. Accedere al [portale di Azure](https://portal.azure.com). 

3. Selezionare **Altri servizi**. Filtrare e selezionare **Reti virtuali (classiche)**. 

4. Filtrare e selezionare la propria rete virtuale dall'elenco. 

5. Nella pagina **Rete virtuale (classico)** selezionare **Proprietà**. 

   ![ID risorsa della rete virtuale classica](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

6. Fare clic sul pulsante di copia per l'**ID RISORSA** per copiare l'ID risorsa della rete classica negli Appunti. Salvare l'ID dagli Appunti in OneNote o in un file. 

7. Selezionare **Subnet** nel menu a sinistra. Assicurarsi che il numero di **indirizzi disponibili** sia maggiore del numero di nodi nel runtime di integrazione Azure-SSIS. 

   ![Numero di indirizzi disponibili nella rete virtuale](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

8. Aggiungere **MicrosoftAzureBatch** al ruolo **Collaboratore Macchina virtuale classica** per la rete virtuale. 

    a. Selezionare **Controllo di accesso (IAM)** nel menu a sinistra e fare clic su **Aggiungi** nella barra degli strumenti. 

    ![Pulsanti "Controllo di accesso" e "Aggiungi"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Nella pagina **Aggiungi autorizzazioni** selezionare **Collaboratore Macchina virtuale classica** per il **Ruolo**. Incollare **ddbf3205-c6bd-46ae-8127-60eb93363864** nella casella di testo **Seleziona** e quindi selezionare **Microsoft Azure Batch** nell'elenco di risultati della ricerca. 

    ![Risultati della ricerca nella pagina "Aggiungi autorizzazioni"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    c. Selezionare **Salva** per salvare le impostazioni e chiudere la pagina. 

    ![Salvare le impostazioni di accesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    d. Verificare che venga visualizzato **Microsoft Azure Batch** nell'elenco dei collaboratori. 

    ![Confermare l'accesso di Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

9. Verificare che il provider di Azure Batch sia registrato nella sottoscrizione di Azure che ha la rete virtuale. In alternativa, registrare il provider di Azure Batch. Se si dispone già di un account Azure Batch nella sottoscrizione, allora la sottoscrizione viene registrata per Azure Batch. Se si crea il runtime di integrazione Azure-SSIS nel portale di Data Factory, il provider di Azure Batch viene registrato automaticamente. 

   a. Nel portale di Azure selezionare **Sottoscrizioni** nel menu a sinistra. 

   b. Selezionare la propria sottoscrizione. 

   c. Selezionare **Provider di risorse** a sinistra e confermare che **Microsoft.Batch** è un provider registrato. 

   ![Conferma dello stato "Registrato"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se non viene visualizzato **Microsoft.Batch** nell'elenco, per registrarlo [creare un account Azure Batch vuoto](../batch/batch-account-create-portal.md) nella propria sottoscrizione. È possibile eliminarlo in un secondo momento. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Aggiungere il runtime di integrazione Azure-SSIS alla rete virtuale
1. Avviare Microsoft Edge o Google Chrome. L'interfaccia utente di Data Factory attualmente è supportata solo in questi Web browser. 

2. Nel [portale di Azure](https://portal.azure.com) selezionare **Data factory** nel menu a sinistra. Se la voce **Data factory** non è visualizzata nel menu, selezionare **Altri servizi** e quindi **Data factory** nella sezione **INTELLIGENCE E ANALISI**. 

   ![Elenco di data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

3. Selezionare la data factory con il runtime di integrazione Azure-SSIS nell'elenco. Verrà visualizzata la home page della data factory. Selezionare il riquadro **Creare e distribuire**. Verrà visualizzata l'interfaccia utente di Data Factory in una scheda separata. 

   ![Home page di Data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

4. Nell'interfaccia utente di Data Factory passare alla scheda **Modifica**, selezionare **Connessioni** e passare alla scheda **Runtime di integrazione**. 

   ![Scheda "Runtime di integrazione"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

5. Se il runtime di integrazione Azure-SSIS è in esecuzione, nell'elenco dei runtime di integrazione selezionare il pulsante **Arresto** nella colonna **Azioni** corrispondente al runtime di integrazione Azure-SSIS di interesse. È possibile modificare un runtime di integrazione solo dopo averlo arrestato. 

   ![Arrestare il runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

6. Nell'elenco dei runtime di integrazione selezionare il pulsante **Modifica** nella colonna **Azioni** corrispondente al runtime di integrazione Azure-SSIS di interesse. 

   ![Modificare il runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

7. Nella pagina **Impostazioni generali** della finestra **Integration Runtime Setup** (Configurazione runtime di integrazione) selezionare **Avanti**. 

   ![Impostazioni generali per la configurazione del runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)

8. Nella pagina **Impostazioni SQL** immettere la password dell'amministratore e selezionare **Avanti**. 

   ![Impostazioni di SQL Server per la configurazione del runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)

9. Nella pagina **Impostazioni avanzate** eseguire le operazioni seguenti: 

   a. Selezionare la casella di controllo **Select a VNet for your Azure-SSIS Integration Runtime to join and allow Azure services to configure VNet permissions/settings** (Selezionare una rete virtuale per l'aggiunta del runtime di integrazione Azure-SSIS e consentire ai servizi di Azure di configurare le autorizzazioni/impostazioni della rete virtuale). 

   b. In **Tipo** specificare se la rete virtuale è una rete virtuale classica o una rete virtuale di Azure Resource Manager. 

   c. In **Nome della rete virtuale** selezionare la rete virtuale. 

   d. In **Nome subnet** selezionare la subnet nella rete virtuale. 

   e. Fare clic su **VNet Validation** (Convalida rete virtuale) e, in caso di esito positivo, fare clic su **Aggiorna**. 

   ![Impostazioni avanzate per la configurazione del runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

10. È ora possibile avviare il runtime di integrazione tramite il pulsante **Inizia** nella colonna **Azioni** corrispondente al runtime di integrazione Azure-SSIS di interesse. L'avvio di un runtime di integrazione Azure-SSIS richiede circa dai 20 ai 30 minuti. 

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>Configurare una rete virtuale
Prima di aggiungere un runtime di integrazione Azure-SSIS a una rete virtuale, è necessario configurare la rete virtuale. Per configurare automaticamente le autorizzazioni/impostazioni della rete virtuale per l'aggiunta del runtime di integrazione Azure-SSIS, aggiungere lo script seguente:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Creare un runtime di integrazione Azure-SSIS e aggiungerlo a una rete virtuale
È possibile creare un runtime di integrazione Azure-SSIS e contemporaneamente aggiungerlo a una rete virtuale. Per istruzioni e per lo script completo, vedere [Creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Aggiungere un runtime di integrazione Azure-SSIS esistente a una rete virtuale
Lo script nell'articolo [Creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md) mostra come creare un runtime di integrazione Azure-SSIS e aggiungerlo a una rete virtuale nello stesso script. Se è disponibile un runtime di integrazione Azure-SSIS esistente, eseguire i passaggi seguenti per aggiungerlo alla rete virtuale: 
1. Arrestare il runtime di integrazione Azure-SSIS. 
2. Configurare il runtime di integrazione Azure-SSIS per aggiungerlo alla rete virtuale. 
3. Avviare il runtime di integrazione Azure-SSIS. 

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
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Configurare il runtime di integrazione Azure-SSIS
Per configurare l'aggiunta del runtime di integrazione Azure-SSIS alla rete virtuale, eseguire il comando `Set-AzureRmDataFactoryV2IntegrationRuntime`: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Avviare il runtime di integrazione Azure-SSIS
Per avviare il runtime di integrazione Azure-SSIS, eseguire il comando seguente: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

L'esecuzione di questo comando richiede dai 20 ai 30 minuti.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui runtime di Azure-SSIS, vedere gli argomenti seguenti: 
- [Runtime di integrazione Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Questo articolo offre informazioni sui runtime di integrazione in generale, incluso il runtime di integrazione Azure-SSIS. 
- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). Questo articolo offre istruzioni dettagliate per creare un runtime di integrazione Azure-SSIS. Viene usato il database SQL di Azure per ospitare il catalogo SSIS. 
- [Creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e fornisce istruzioni sull'uso del database SQL di Azure con endpoint del servizio di rete virtuale/Istanza gestita (anteprima) per ospitare il catalogo SSIS e sull'aggiunta del runtime di integrazione a una rete virtuale. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo viene illustrato come recuperare informazioni su un runtime di integrazione SSIS di Azure e le descrizioni degli stati nelle informazioni restituite. 
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). In questo articolo viene illustrato come arrestare, avviare o rimuovere un runtime di integrazione SSIS di Azure. Viene anche illustrato come scalare orizzontalmente il runtime di integrazione Azure-SSIS tramite l'aggiunta di nodi. 
