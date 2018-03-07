---
title: Aggiungere un runtime di integrazione Azure-SSIS a una rete virtuale | Microsoft Docs
description: Informazioni su come aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale di Azure.
services: data-factory
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: 3a5b68729d587e1365c42125108e610705965c86
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale
Aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale di Azure negli scenari seguenti: 

- Si ospita il database del catalogo SSIS (SQL Server Integration Services) in un'istanza gestita di database SQL di Azure (anteprima privata) in una rete virtuale.
- Si intende connettersi ad archivi dati locali da pacchetti SSIS eseguiti in un runtime di integrazione SSIS di Azure.

 Azure Data Factory versione 2 (anteprima) consente di aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale creata con il modello di distribuzione classico o con il modello di distribuzione Azure Resource Manager. 

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione relativa a Data Factory versione 1](v1/data-factory-introduction.md).

## <a name="access-to-on-premises-data-stores"></a>Accesso agli archivi dati locali
Se i pacchetti SSIS consentono di accedere solo ad archivi dati cloud pubblici, non è necessario aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale. Se i pacchetti SSIS consentono di accedere ad archivi dati locali, è necessario aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale connessa alla rete locale. 

Se il catalogo SSIS è ospitato in un'istanza di database SQL di Azure che non è presente nella rete virtuale, è necessario aprire le porte appropriate. 

Se il catalogo SSIS è ospitato in un'istanza gestita di database SQL in una rete virtuale, è possibile aggiungere un runtime di integrazione Azure-SSIS a:

- La stessa rete virtuale.
- Un'altra rete virtuale che ha una connessione di tipo network-to-network con la rete che contiene l'istanza gestita di database SQL. 

La rete virtuale può essere distribuita con il modello di distribuzione classico o con il modello di distribuzione Azure Resource Manager. Se si prevede di aggiungere il runtime di integrazione Azure-SSIS alla *stessa rete virtuale* che contiene l'istanza gestita di database SQL, verificare che il runtime di integrazione Azure-SSIS si trovi in una *subnet diversa* da quella che contiene l'istanza gestita di database SQL.   

Per altre informazioni, vedere le sezioni seguenti.

Ecco alcuni elementi importanti da considerare: 

- Se non è presente alcuna rete virtuale connessa alla rete locale, creare prima di tutto una [rete virtuale di Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) o una [rete virtuale classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) per aggiungere il runtime di integrazione Azure-SSIS. Configurare quindi una [connessione gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) o [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) da sito a sito da tale rete virtuale alla rete locale.
- Se è già presente una rete virtuale classica o una rete virtuale di Azure Resource Manager connessa alla rete locale nello stesso percorso del runtime di integrazione Azure-SSIS, è possibile aggiungere il runtime di integrazione alla rete virtuale.
- Se è già presente una rete virtuale classica connessa alla rete locale in un percorso diverso rispetto al runtime di integrazione Azure-SSIS, creare prima una [rete virtuale classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) per aggiungere il runtime di integrazione Azure-SSIS. Configurare quindi una connessione [da rete virtuale classica a rete virtuale classica](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md). In alternativa, è possibile creare una [rete virtuale di Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) per aggiungere il runtime di integrazione Azure-SSIS. Configurare quindi una connessione [da rete virtuale classica a rete virtuale di Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).
- Se è già presente una rete virtuale di Azure Resource Manager connessa alla rete locale in un percorso diverso rispetto al runtime di integrazione Azure-SSIS, creare prima una [rete virtuale di Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) per aggiungere il runtime di integrazione Azure-SSIS. Configurare quindi una connessione da rete virtuale di Azure Resource Manager a rete virtuale di Azure Resource Manager. In alternativa, è possibile creare una [rete virtuale classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) per aggiungere il runtime di integrazione Azure-SSIS. Configurare quindi una connessione [da rete virtuale classica a rete virtuale di Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="domain-name-services-server"></a>Server Domain Name Service 
Se è necessario usare il proprio server DNS (Domain Name Service) in una rete virtuale aggiunta tramite il runtime di integrazione Azure-SSIS, seguire il materiale sussidiario per [assicurarsi che i nodi del runtime di integrazione Azure-SSIS nella rete virtuale possano risolvere gli endpoint di Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>Gruppo di sicurezza di rete
Se è necessario implementare un gruppo di sicurezza di rete in una rete virtuale aggiunta tramite il runtime di integrazione Azure-SSIS, consentire il traffico in ingresso/in uscita tramite le porte seguenti:

| Porte | Direzione | Protocollo di trasporto | Scopo | Origine in ingresso/destinazione in uscita |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100, 20100, 30100 (se si aggiunge il runtime di integrazione a una rete virtuale classica)<br/><br/>29876, 29877 (se si aggiunge il runtime di integrazione a una rete virtuale di Azure Resource Manager) | In ingresso | TCP | I servizi di Azure usano queste porte per comunicare con i nodi del runtime di integrazione Azure-SSIS nella rete virtuale. | Internet | 
| 443 | In uscita | TCP | I nodi del runtime di integrazione Azure-SSIS nella rete virtuale usano questa porta per accedere ai servizi di Azure, ad esempio Archiviazione di Azure e Hub eventi di Azure. | Internet | 
| 1433<br/>11000-11999<br/>14000-14999  | In uscita | TCP | I nodi del runtime di integrazione Azure-SSIS nella rete virtuale usano queste porte per accedere al database SSIS ospitato dal server del database SQL di Azure (non applicabile al database SSIS ospitato dall'istanza gestita di database SQL). | Internet | 

## <a name="azure-portal-data-factory-ui"></a>Portale di Azure (interfaccia utente di Data Factory)
Questa sezione illustra come aggiungere un runtime Azure-SSIS esistente a una rete virtuale (classica o di Azure Resource Manager) tramite il portale di Azure e l'interfaccia utente di Data Factory. Prima di aggiungere il runtime di integrazione Azure-SSIS alla rete virtuale, è necessario configurarla in modo appropriato. Eseguire una delle due sezioni successive, a seconda del tipo di rete virtuale (classica o di Azure Resource Manager). Continuare quindi con la terza sezione per aggiungere il runtime di integrazione Azure-SSIS alla rete virtuale. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Usare il portale per configurare una rete virtuale classica
Prima di aggiungere un runtime di integrazione Azure-SSIS alla rete virtuale, è necessario configurarla.

1. Avviare Microsoft Edge o Google Chrome. L'interfaccia utente di Data Factory attualmente è supportata solo in questi Web browser.
2. Accedere al [portale di Azure](https://portal.azure.com).
3. Selezionare **Altri servizi**. Filtrare e selezionare **Reti virtuali (classiche)**.
4. Filtrare e selezionare la propria rete virtuale dall'elenco. 
5. Nella pagina **Rete virtuale (classico)** selezionare **Proprietà**. 

    ![ID risorsa della rete virtuale classica](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Fare clic sul pulsante di copia per l'**ID RISORSA** per copiare l'ID risorsa della rete classica negli Appunti. Salvare l'ID dagli Appunti in OneNote o in un file.
6. Selezionare **Subnet** nel menu a sinistra. Assicurarsi che il numero di **indirizzi disponibili** sia maggiore del numero di nodi nel runtime di integrazione Azure-SSIS.

    ![Numero di indirizzi disponibili nella rete virtuale](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Aggiungere **MicrosoftAzureBatch** al ruolo **Collaboratore Macchina virtuale classica** per la rete virtuale.

    a. Selezionare **Controllo di accesso (IAM)** nel menu a sinistra e fare clic su **Aggiungi** nella barra degli strumenti. 
       ![Pulsanti "Controllo di accesso" e "Aggiungi"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Nella pagina **Aggiungi autorizzazioni** selezionare **Collaboratore Macchina virtuale classica** per il **Ruolo**. Incollare **ddbf3205-c6bd-46ae-8127-60eb93363864** nella casella di testo **Seleziona** e quindi selezionare **Microsoft Azure Batch** nell'elenco di risultati della ricerca.   
       ![Risultati della ricerca nella pagina "Aggiungere autorizzazioni"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    c. Selezionare **Salva** per salvare le impostazioni e chiudere la pagina.  
       ![Salvare le impostazioni di accesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    d. Verificare che venga visualizzato **Microsoft Azure Batch** nell'elenco dei collaboratori.  
       ![Confermare l'accesso di Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

5. Verificare che il provider di Azure Batch sia registrato nella sottoscrizione di Azure che ha la rete virtuale. In alternativa, registrare il provider di Azure Batch. Se si dispone già di un account Azure Batch nella sottoscrizione, allora la sottoscrizione viene registrata per Azure Batch.

   a. Nel portale di Azure selezionare **Sottoscrizioni** nel menu a sinistra.

   b. Selezionare la propria sottoscrizione.

   c. Selezionare **Provider di risorse** a sinistra e confermare che **Microsoft.Batch** è un provider registrato.     
      ![Conferma dello stato "Registrato"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se non viene visualizzato **Microsoft.Batch** nell'elenco, per registrarlo [creare un account Azure Batch vuoto](../batch/batch-account-create-portal.md) nella propria sottoscrizione. È possibile eliminarlo in un secondo momento. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Usare il portale per configurare una rete virtuale di Azure Resource Manager
Prima di aggiungere un runtime di integrazione Azure-SSIS alla rete virtuale, è necessario configurarla.

1. Avviare Microsoft Edge o Google Chrome. L'interfaccia utente di Data Factory attualmente è supportata solo in questi Web browser.
2. Accedere al [portale di Azure](https://portal.azure.com).
3. Selezionare **Altri servizi**. Filtrare e selezionare **Reti virtuali**.
4. Filtrare e selezionare la propria rete virtuale dall'elenco. 
5. Nella pagina **Rete virtuale** selezionare **Proprietà**. 
6. Fare clic sul pulsante di copia per l'**ID RISORSA** per copiare l'ID risorsa per la rete virtuale negli Appunti. Salvare l'ID dagli Appunti in OneNote o in un file.
7. Selezionare **Subnet** nel menu a sinistra. Assicurarsi che il numero di **indirizzi disponibili** sia maggiore del numero di nodi nel runtime di integrazione Azure-SSIS.
8. Verificare che il provider di Azure Batch sia registrato nella sottoscrizione di Azure che ha la rete virtuale. In alternativa, registrare il provider di Azure Batch. Se si dispone già di un account Azure Batch nella sottoscrizione, allora la sottoscrizione viene registrata per Azure Batch.

   a. Nel portale di Azure selezionare **Sottoscrizioni** nel menu a sinistra.

   b. Selezionare la propria sottoscrizione. 
   
   c. Selezionare **Provider di risorse** a sinistra e confermare che **Microsoft.Batch** è un provider registrato.     
      ![Conferma dello stato "Registrato"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se non viene visualizzato **Microsoft.Batch** nell'elenco, per registrarlo [creare un account Azure Batch vuoto](../batch/batch-account-create-portal.md) nella propria sottoscrizione. È possibile eliminarlo in un secondo momento.

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Aggiungere il runtime di integrazione Azure-SSIS alla rete virtuale


1. Avviare Microsoft Edge o Google Chrome. L'interfaccia utente di Data Factory attualmente è supportata solo in questi Web browser.
2. Nel [portale di Azure](https://portal.azure.com) selezionare **Data factory** nel menu a sinistra. Se la voce **Data factory** non è visualizzata nel menu, selezionare **Altri servizi** e quindi **Data factory** nella sezione **INTELLIGENCE E ANALISI**. 
    
   ![Elenco di data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. Selezionare la data factory con il runtime di integrazione Azure-SSIS nell'elenco. Verrà visualizzata la home page della data factory. Selezionare il riquadro **Creare e distribuire**. Verrà visualizzata l'interfaccia utente di Data Factory in una scheda separata. 

   ![Home page di Data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. Nell'interfaccia utente di Data Factory passare alla scheda **Modifica**, selezionare **Connessioni** e passare alla scheda **Runtime di integrazione**. 

   ![Scheda "Runtime di integrazione"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. Se il runtime di integrazione Azure-SSIS è in esecuzione, nell'elenco dei runtime di integrazione selezionare il pulsante **Arresto** nella colonna **Azioni** corrispondente al runtime di integrazione Azure-SSIS di interesse. È possibile modificare un runtime di integrazione solo dopo averlo arrestato. 

   ![Arrestare il runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. Nell'elenco dei runtime di integrazione selezionare il pulsante **Modifica** nella colonna **Azioni** corrispondente al runtime di integrazione Azure-SSIS di interesse.

   ![Modificare il runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. Nella pagina **Impostazioni generali** della finestra **Integration Runtime Setup** (Configurazione di Integration Runtime) selezionare **Avanti**. 

   ![Impostazioni generali per la configurazione del runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. Nella pagina **Impostazioni SQL** immettere la password dell'amministratore e selezionare **Avanti**.

   ![Impostazioni di SQL Server per la configurazione del runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. Nella pagina **Impostazioni avanzate** eseguire le operazioni seguenti: 

   a. Selezionare la casella di controllo **Select a VNet for your Azure-SSIS Integration Runtime to join and allow Azure services to configure VNet permissions/settings** (Selezionare una rete virtuale per l'aggiunta del runtime di integrazione Azure-SSIS e consentire ai servizi di Azure di configurare le autorizzazioni/impostazioni della rete virtuale).

   b. In **Tipo** specificare se la rete virtuale è una rete virtuale classica o una rete virtuale di Azure Resource Manager. 

   c. In **Nome della rete virtuale** selezionare la rete virtuale.

   d. In **Nome subnet** selezionare la subnet nella rete virtuale.

   e. Selezionare **Aggiorna**. 

   ![Impostazioni avanzate per la configurazione del runtime di integrazione](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. È ora possibile avviare il runtime di integrazione tramite il pulsante **Inizia** nella colonna **Azioni** corrispondente al runtime di integrazione Azure-SSIS di interesse. L'avvio di un runtime di integrazione Azure-SSIS richiede circa 20 minuti. 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>Configurare una rete virtuale
Prima di aggiungere un runtime di integrazione Azure-SSIS alla rete virtuale, è necessario configurarla. Per configurare automaticamente le autorizzazioni/impostazioni della rete virtuale per l'aggiunta del runtime di integrazione Azure-SSIS, aggiungere lo script seguente:

```powershell
# Register to the Azure Batch resource provider
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
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a virtual network and Azure SQL Database Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network.
$VnetId = "<Name of your Azure virtual network>"
$SubnetName = "<Name of the subnet in the virtual network>"
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
Per configurare l'aggiunta del runtime di integrazione Azure-SSIS alla rete virtuale, eseguire il comando `Set-AzureRmDataFactoryV2IntegrationRuntime`: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
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
- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). Questo articolo offre istruzioni dettagliate per creare un runtime di integrazione Azure-SSIS. Nell'articolo viene usato un database Azure SQL per ospitare il catalogo SSIS. 
- [Creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e offre istruzioni sull'uso dell'istanza gestita di database SQL di Azure (anteprima privata) e sull'aggiunta del runtime di integrazione a una rete virtuale. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo viene illustrato come recuperare informazioni su un runtime di integrazione SSIS di Azure e le descrizioni degli stati nelle informazioni restituite. 
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). In questo articolo viene illustrato come arrestare, avviare o rimuovere un runtime di integrazione SSIS di Azure. Viene anche illustrato come scalare orizzontalmente il runtime di integrazione Azure-SSIS tramite l'aggiunta di nodi. 
