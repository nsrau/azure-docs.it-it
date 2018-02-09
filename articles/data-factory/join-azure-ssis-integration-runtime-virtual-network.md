---
title: Aggiungere il runtime di integrazione SSIS di Azure a una rete virtuale | Documentazione Microsoft
description: Informazioni su come aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale di Azure.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: spelluru
ms.openlocfilehash: 2131aa75dcfb975f11cff9800087c3e4e7170378
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale
Aggiungere il runtime di integrazione (IR) SSIS di Azure a una rete virtuale (VNet) di Azure negli scenari seguenti: 

- Si ospita il database del catalogo SSIS in un'istanza gestita di SQL Server (anteprima privata) che fa parte di una VNet.
- Si intende connettersi ad archivi dati locali da pacchetti SSIS eseguiti in un runtime di integrazione SSIS di Azure.

 Azure Data Factory versione 2 (anteprima) consente di aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale classica o a una rete virtuale di Azure Resource Manager. 

 > [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-introduction.md).

## <a name="access-on-premises-data-stores"></a>Accesso agli archivi dati locali
Se i pacchetti SSIS permettono di accedere solo ad archivi dati cloud pubblici, non è necessario aggiungere il runtime di integrazione SSIS di Azure a una rete virtuale. Se i pacchetti SSIS permettono di accedere ad archivi dati locali, è necessario aggiungere il runtime di integrazione SSIS di Azure a una rete virtuale connessa alla rete locale. Se il catalogo SSIS è ospitato in un Database SQL di Azure che non è presente nella VNet, è necessario aprire le porte appropriate. Se il catalogo SSIS è ospitato nell'istanza SQL gestita di Azure che si trova in una rete virtuale classica o in una rete virtuale di Azure Resource Manager, è possibile aggiungere il runtime di integrazione Azure-SSIS nella stessa rete virtuale o in una rete virtuale diversa che abbia una connessione da rete virtuale a rete virtuale con la rete virtuale che include l'istanza SQL gestita di Azure. Per altre informazioni, vedere le sezioni seguenti.

Ecco alcuni elementi importanti da considerare: 

- Se non c'è alcuna rete virtuale connessa alla rete locale, creare prima di tutto una [rete virtuale di Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) o una [rete virtuale classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) per aggiungere il runtime di integrazione Azure-SSIS. Quindi, configurare una [connessione gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) di tipo sito a sito da tale rete virtuale alla rete locale.
- Se alla rete locale è già connesso Azure Resource Manager o una rete virtuale classica nello stesso percorso del runtime di integrazione SSIS di Azure, è possibile aggiungere il runtime di integrazione alla rete virtuale.
- Se alla rete locale è già connessa una rete virtuale classica in un percorso diverso dal runtime di integrazione SSIS di Azure, è possibile creare prima una [rete virtuale classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) a cui aggiungere il runtime di integrazione SSIS di Azure. Quindi, configurare una connessione di [rete virtuale di tipo classica a classica](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md). In alternativa, è possibile creare una [rete virtuale di Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) per aggiungere il runtime di integrazione Azure-SSIS. Configurare quindi una connessione [da rete virtuale classica a rete virtuale di Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).
- Se alla rete locale è già connessa una rete virtuale di Azure Resource Manager in un percorso diverso rispetto al runtime di integrazione SSIS di Azure, è possibile prima creare una [rete virtuale di Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) a cui aggiungere il runtime di integrazione SSIS di Azure. Configurare quindi una connessione da rete virtuale di Azure Resource Manager a rete virtuale di Azure Resource Manager. In alternativa, è possibile creare una [rete virtuale classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) a cui aggiungere il runtime di integrazione SSIS di Azure. Quindi, configurare una connessione di [tipo classica a rete virtuale Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="domain-name-services-server"></a>Server Domain Name Service 
Se è necessario usare il proprio server di servizi DNS (Domain Name Service) in una rete virtuale aggiunta tramite il runtime di integrazione SSIS di Azure, seguire il materiale sussidiario per [assicurarsi che i nodi del runtime di integrazione SSIS di Azure nella rete virtuale possano risolvere gli endpoint di Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>Gruppo di sicurezza di rete
Se è necessario implementare il gruppo di sicurezza di rete (NSG) in una rete virtuale aggiunta tramite il runtime di integrazione SSIS di Azure, consentire il traffico in ingresso/uscita tramite le porte seguenti:

| Porte | Direzione | Protocollo di trasporto | Scopo | Origine in entrata/destinazione in uscita |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100, 20100, 30100 (se si aggiunge il runtime di integrazione a una rete virtuale classica)<br/><br/>29876, 29877 (se si aggiunge il runtime di integrazione a una rete virtuale di Azure Resource Manager) | In ingresso | TCP | I servizi di Azure usano queste porte per comunicare con i nodi del runtime di integrazione SSIS di Azure nella rete virtuale. | Internet | 
| 443 | In uscita | TCP | I nodi del runtime di integrazione SSIS di Azure nella rete virtuale usano questa porta per accedere ai servizi di Azure, ad esempio, archiviazione di Azure, Hub eventi e così via. | INTERNET | 
| 1433<br/>11000-11999<br/>14000-14999  | In uscita | TCP | I nodi del runtime di integrazione SSIS di Azure nella rete virtuale usano queste porte per accedere al database SSIS ospitato dal server del database SQL di Azure (non applicabile al database SSIS ospitato dall'istanza gestita di SQL Azure). | Internet | 

## <a name="azure-portal-data-factory-ui"></a>Portale di Azure (interfaccia utente di Data Factory)
Questa sezione illustra come aggiungere un runtime SSIS di Azure esistente a una rete virtuale (classica o di Azure Resource Manager) tramite il portale di Azure e l'interfaccia utente di Data Factory. Prima di aggiungere il runtime di integrazione SSIS di Azure alla rete virtuale, è necessario configurare quest'ultima in modo appropriato. Eseguire una delle due prossime sezioni a seconda del tipo di rete virtuale (classica o di Azure Resource Manager). Continuare quindi con la terza sezione per aggiungere il runtime di integrazione SSIS di Azure alla rete virtuale. 

### <a name="use-portal-to-configure-a-classic-vnet"></a>Usare il portale per configurare una rete virtuale classica
Prima di aggiungere un runtime di integrazione SSIS di Azure alla rete virtuale è necessario configurare quest'ultima.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Altri servizi**. Filtrare e selezionare **Reti virtuali (classiche)**.
3. Filtrare e selezionare la propria **rete virtuale** dall’elenco. 
4. Nella pagina Rete virtuale (classica), selezionare **Proprietà**. 

    ![ID di risorsa di rete virtuale classica](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Fare clic sul pulsante copia per l’**ID RISORSA** per copiare l'ID di risorsa per la rete classica negli Appunti. Salvare l'ID dagli Appunti in OneNote o in un file.
6. Fare clic su **Subnet** nel menu a sinistra e assicurarsi che il numero di **indirizzi disponibili** sia maggiore dei nodi nel runtime di integrazione SSIS di Azure.

    ![Numero di indirizzi disponibili nella rete virtuale](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Aggiungere **MicrosoftAzureBatch** al ruolo **collaboratore classico alla macchina virtuale** per la rete virtuale. 
    1. Fare clic su Controllo di accesso (IAM) nel menu a sinistra e fare clic su **Aggiungi** nella barra degli strumenti.
    
        ![Controllo di accesso -> Aggiungi](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png) 
    2. Nel pannello **Aggiungi autorizzazioni** selezionare **Collaboratore classico alla macchina virtuale** per il **Ruolo**. Copiare/incollare **ddbf3205-c6bd-46ae-8127-60eb93363864** nella casella di testo **Seleziona** e quindi selezionare **Microsoft Azure Batch** nell'elenco di risultati della ricerca. 
    
        ![Aggiungere autorizzazioni - Ricerca](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
    3. Fare clic su Salva per salvare le impostazioni e chiudere la pagina.
    
        ![Salvare le impostazioni di accesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
    4. Verificare che sia visualizzato **MicrosoftAzureBatch** nell'elenco dei collaboratori.
    
        ![Confermare l'accesso di AzureBatch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
5. Verificare che il provider di Azure Batch sia registrato nella sottoscrizione di Azure che ha la rete virtuale o registrare il provider di Azure Batch. Se si dispone già di un account Azure Batch nella sottoscrizione, allora la sottoscrizione viene registrata per Azure Batch.
    1. Nel portale di Azure, fare clic su **Sottoscrizioni** nel menu a sinistra. 
    2. Selezionare la propria **sottoscrizione**. 
    3. Fare clic su **Provider di risorse** a sinistra e verificare che `Microsoft.Batch` è un provider registrato. 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    Se non viene visualizzato `Microsoft.Batch` nell'elenco, per la registrazione [creare un account Azure Batch vuoto](../batch/batch-account-create-portal.md) nella propria sottoscrizione. È possibile eliminarlo in un secondo momento. 

### <a name="use-portal-to-configure-an-azure-resource-manager-vnet"></a>Usare il portale per configurare una rete virtuale di Azure Resource Manager
Prima di aggiungere un runtime di integrazione SSIS di Azure alla rete virtuale è necessario configurare quest'ultima.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Altri servizi**. Filtrare e selezionare **Reti virtuali**.
3. Filtrare e selezionare la propria **rete virtuale** dall’elenco. 
4. Nella pagina Rete virtuale selezionare **Proprietà**. 
5. Fare clic sul pulsante di copia per l'**ID RISORSA** per copiare l'ID risorsa per la rete virtuale negli Appunti. Salvare l'ID dagli Appunti in OneNote o in un file.
6. Fare clic su **Subnet** nel menu a sinistra e assicurarsi che il numero di **indirizzi disponibili** sia maggiore dei nodi nel runtime di integrazione SSIS di Azure.
5. Verificare che il provider di Azure Batch sia registrato nella sottoscrizione di Azure che ha la rete virtuale o registrare il provider di Azure Batch. Se si dispone già di un account Azure Batch nella sottoscrizione, allora la sottoscrizione viene registrata per Azure Batch.
    1. Nel portale di Azure, fare clic su **Sottoscrizioni** nel menu a sinistra. 
    2. Selezionare la propria **sottoscrizione**. 
    3. Fare clic su **Provider di risorse** a sinistra e verificare che `Microsoft.Batch` è un provider registrato. 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    Se non viene visualizzato `Microsoft.Batch` nell'elenco, per la registrazione [creare un account Azure Batch vuoto](../batch/batch-account-create-portal.md) nella propria sottoscrizione. È possibile eliminarlo in un secondo momento.

### <a name="join-the-azure-ssis-ir-to-a-vnet"></a>Aggiungere il runtime di integrazione SSIS di Azure a una rete virtuale


1. Nel [portale di Azure](https://portal.azure.com) selezionare **Data factory** nel menu a sinistra. Se la voce **Data factory** non è visualizzata nel menu, selezionare **Altri servizi**, **Data factory** nella sezione **INTELLIGENCE E ANALISI**. 
    
    ![Elenco Data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. Selezionare la data factory con il runtime di integrazione SSIS di Azure nell'elenco. Verrà visualizzata la home page della data factory. Selezionare il riquadro **Creare e distribuire**. Verrà visualizzata l'interfaccia utente di Data Factory in una scheda separata. 

    ![Home page di Data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. Nell'interfaccia utente di Data Factory passare alla scheda **Modifica**, selezionare **Connessioni** e passare alla scheda **Runtime di integrazione**. 

    ![Scheda Runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. Se il runtime di integrazione SSIS di Azure è in esecuzione, nell'elenco dei runtime di integrazione selezionare il pulsante **Arresto** nella colonna **Azioni** corrispondente al runtime di integrazione SSIS di Azure di interesse. È possibile modificare un runtime di integrazione solo dopo averlo arrestato. 

    ![Arresta runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. Nell'elenco dei runtime di integrazione selezionare il pulsante **Modifica** nella colonna **Azioni** corrispondente al runtime di integrazione SSIS di Azure di interesse.

    ![Modifica runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. Nella pagina **Impostazioni generali** della finestra **Integration Runtime Setup** (Configurazione di Integration Runtime) selezionare **Avanti**. 

    ![Configurazione del runtime di integrazione: impostazioni generali](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. Nella pagina **Impostazioni SQL** immettere la **password** dell'amministratore e selezionare **Avanti**.

    ![Configurazione del runtime di integrazione: impostazioni SQL](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. Nella pagina **Impostazioni avanzate** eseguire le operazioni seguenti: 

    1. Selezionare la casella di controllo **Select a VNet for your Azure-SSIS Integration Runtime to join and allow Azure services to configure VNet permissions/settings** (Selezionare una rete virtuale per l'aggiunta del runtime di integrazione SSIS di Azure e consentire ai servizi di Azure di configurare le autorizzazioni/impostazioni della rete virtuale). 
    2. In **Tipo** specificare se la rete virtuale è classica o di Azure Resource Manager. 
    3. In **Nome della rete virtuale** selezionare la rete virtuale.
    4. In **Nome subnet** selezionare la subnet nella rete virtuale. 
    5. Selezionare **Aggiorna**. 

        ![Configurazione del runtime di integrazione: impostazioni avanzate](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. È ora possibile avviare il runtime di integrazione tramite il pulsante **Inizia** nella colonna **Azioni** corrispondente al runtime di integrazione SSIS di Azure di interesse. L'avvio di un runtime di integrazione SSIS di Azure richiede circa 20 minuti. 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-vnet"></a>Configurare la rete virtuale
Prima di aggiungere un runtime di integrazione SSIS di Azure alla rete virtuale è necessario configurare quest'ultima. Aggiungere lo script seguente per configurare automaticamente le autorizzazioni/impostazioni della rete virtuale per l'aggiunta del runtime di integrazione Azure-SSIS.

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-vnet"></a>Creare un runtime di integrazione Azure-SSIS e aggiungerlo a una rete virtuale
È possibile creare un runtime di integrazione Azure-SSIS e contemporaneamente aggiungerlo a una rete virtuale. Per lo script completo e istruzioni per creare un runtime di integrazione Azure-SSIS e contemporaneamente aggiungerlo a una rete virtuale, vedere [Creare un runtime di integrazione Azure-SSIS](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-vnet"></a>Aggiungere un runtime di integrazione Azure-SSIS esistente a una rete virtuale
Lo script nell'articolo [Creare un runtime di integrazione Azure-SSIS](create-azure-ssis-integration-runtime.md) mostra come creare un runtime di integrazione Azure-SSIS e aggiungerlo a una rete virtuale con lo stesso script. Se si ha un runtime di integrazione Azure-SSIS, eseguire i passaggi seguenti per aggiungerlo alla rete virtuale. 

1. Arrestare il runtime di integrazione Azure-SSIS.
2. Configurare il runtime di integrazione Azure-SSIS per aggiungerlo alla rete virtuale. 
3. Avviare il runtime di integrazione Azure-SSIS. 

### <a name="define-the-variables"></a>Definire le variabili

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet).
$VnetId = "<Name of your Azure virtual netowrk>"
$SubnetName = "<Name of the subnet in VNet>"
```

### <a name="stop-the-azure-ssis-ir"></a>Arrestare il runtime di integrazione Azure-SSIS
Arrestare il runtime di integrazione Azure-SSIS prima di aggiungerlo a una rete virtuale. Questo comando rilascia tutti i nodi e arresta la fatturazione.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-vnet-settings-for-the-azure-ssis-ir-to-join"></a>Configurare le impostazioni della rete virtuale per aggiungere il runtime di integrazione Azure-SSIS
Eseguire la registrazione nel provider di risorse di Azure Batch:

```powershell
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
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
Eseguire il comando Set-AzureRmDataFactoryV2IntegrationRuntime per configurare il runtime di integrazione Azure-SSIS per l'aggiunta alla rete virtuale: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Avviare il runtime di integrazione Azure-SSIS
Eseguire questo comando per avviare il runtime di integrazione SSIS di Azure: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
Il completamento di questo comando richiede **da 20 a 30 minuti**.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui runtime SSIS di Azure, vedere gli argomenti seguenti: 

- [Runtime di integrazione SSIS di Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo vengono fornite le informazioni sui runtime di integrazione in generale incluso il runtime di integrazione SSIS di Azure. 
- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). In questo articolo vengono fornite le istruzioni passo per passo per creare un runtime di integrazione SSIS di Azure e utilizzare un database Azure SQL per ospitare il catalogo SSIS. 
- [Procedura: come creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e fornisce istruzioni sull'utilizzo dell’istanza gestita di database SQL di Azure (anteprima privata) e sull’aggiunta del runtime di integrazione a un VNet. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo viene illustrato come recuperare informazioni su un runtime di integrazione SSIS di Azure e le descrizioni degli stati nelle informazioni restituite. 
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). In questo articolo viene illustrato come arrestare, avviare o rimuovere un runtime di integrazione SSIS di Azure. Viene inoltre mostrato come scalare orizzontalmente il runtime di integrazione SSIS di Azure aggiungendo più nodi al runtime di integrazione. 
