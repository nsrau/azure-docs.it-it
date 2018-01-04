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
ms.date: 09/27/2017
ms.author: spelluru
ms.openlocfilehash: aa570379890023c83383d291aa5d57fb79b2d5aa
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale
È necessario aggiungere il runtime di integrazione (IR) SSIS di Azure a una rete virtuale (VNet) di Azure se viene soddisfatta una delle condizioni seguenti: 

- Si ospita il database del catalogo SSIS in un'istanza gestita di SQL Server (anteprima privata) che fa parte di una VNet.
- Si intende connettersi ad archivi dati locali da pacchetti SSIS eseguiti in un runtime di integrazione SSIS di Azure.

 Azure Data Factory versione 2 (anteprima) consente di aggiungere il runtime di integrazione SSIS di Azure a una VNet classica. Attualmente, VNet Gestione risorse di Azure non è supportata. Tuttavia, è possibile risolvere come illustrato nella sezione seguente. 

 > [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-introduction.md).

## <a name="access-on-premises-data-stores"></a>Accesso agli archivi dati locali
Se i pacchetti SSIS permettono di accedere solo ad archivi dati cloud pubblici, non è necessario aggiungere il runtime di integrazione SSIS di Azure a una rete virtuale. Se i pacchetti SSIS permettono di accedere ad archivi dati locali, è necessario aggiungere il runtime di integrazione SSIS di Azure a una rete virtuale connessa alla rete locale. Se il catalogo SSIS è ospitato in un Database SQL di Azure che non è presente nella VNet, è necessario aprire le porte appropriate. Se il catalogo SSIS è ospitato in un’istanza gestita Azure SQL che si trova in una VNet classica, è possibile aggiungere il runtime di integrazione SSIS di Azure alla stessa VNet classica o a una VNet classica diversa che dispone di una connessione di rete virtuale di classe con quella che ha l'istanza gestita di SQL Azure. Per altre informazioni, vedere le sezioni seguenti.

Ecco alcuni elementi importanti da considerare: 

- Se non è presente alcuna rete virtuale connessa alla rete locale, creare innanzitutto una [VNet classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) per aggiungere il runtime di integrazione SSIS di Azure. Quindi, configurare una [connessione gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) di tipo sito a sito da tale rete virtuale alla rete locale.
- Se è presente una rete virtuale classica connessa alla rete locale nello stesso percorso del runtime di integrazione SSIS di Azure, è possibile aggiungere il runtime di integrazione SSIS di Azure a essa.
- Se è presente una rete virtuale classica connessa alla rete locale in un percorso diverso dal runtime di integrazione SSIS di Azure, è possibile creare prima una [VNet classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) per aggiungere il runtime di integrazione SSIS di Azure. Quindi, configurare una connessione di [rete virtuale di tipo classica a classica](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md).
- Se è presente una rete virtuale Azure Resource Manager connessa alla rete locale, creare innanzitutto una [VNet classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) per aggiungere il runtime di integrazione SSIS di Azure. Quindi, configurare una connessione di [tipo classica a rete virtuale Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="domain-name-services-server"></a>Server Domain Name Service 
Se è necessario usare il proprio server di servizi DNS (Domain Name Service) in una rete virtuale aggiunta tramite il runtime di integrazione SSIS di Azure, seguire il materiale sussidiario per [assicurarsi che i nodi del runtime di integrazione SSIS di Azure nella rete virtuale possano risolvere gli endpoint di Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>Gruppo di sicurezza di rete
Se è necessario implementare il gruppo di sicurezza di rete (NSG) in una rete virtuale aggiunta tramite il runtime di integrazione SSIS di Azure, consentire il traffico in ingresso/uscita tramite le porte seguenti:

| Porte | Direzione | Protocollo di trasporto | Scopo | Origine in entrata/destinazione in uscita |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100<br/>20100<br/>30100  | In ingresso | TCP | I servizi di Azure usano queste porte per comunicare con i nodi del runtime di integrazione SSIS di Azure nella rete virtuale. | Internet | 
| 443 | In uscita | TCP | I nodi del runtime di integrazione SSIS di Azure nella rete virtuale usano questa porta per accedere ai servizi di Azure, ad esempio, archiviazione di Azure, Hub eventi e così via. | INTERNET | 
| 1433<br/>11000-11999<br/>14000-14999  | In uscita | TCP | I nodi del runtime di integrazione SSIS di Azure nella rete virtuale usano queste porte per accedere al database SSIS ospitato dal server del database SQL di Azure (non applicabile al database SSIS ospitato dall'istanza gestita di SQL Azure). | Internet | 

## <a name="configure-vnet"></a>Configurare una rete virtuale
È innanzitutto necessario configurare una rete virtuale usando uno dei modi seguenti (rispetto a script. Portale di Azure) poter aggiungere un IR Azure SSIS per la rete virtuale. 

### <a name="script-to-configure-vnet"></a>Script per configurare una rete virtuale 
Aggiungere lo script seguente per configurare automaticamente le autorizzazioni di rete virtuale le impostazioni per l'integrazione di Azure-SSIS runtime aggiungere la rete virtuale.

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
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
}
```

### <a name="use-portal-to-configure-vnet"></a>Uso del portale per configurare una rete virtuale
L’esecuzione dello script è il modo più semplice per configurare una rete virtuale. Se non si dispone di un accesso per configurare tale rete virtuale/la configurazione automatica non riesce, il proprietario di tale rete virtuale/l’utente può provare la configurazione manuale nei passaggi seguenti:

### <a name="find-the-resource-id-for-your-azure-vnet"></a>Trovare l'ID di risorsa per la rete virtuale di Azure.
 
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
    2. Nel pannello **Aggiungi autorizzazioni** selezionare **Collaboratore classico alla macchina virtuale** per il **Ruolo**. Copiare e incollare **ddbf3205-c6bd-46ae-8127-60eb93363864** nel **selezionare** casella di testo e quindi selezionare **Microsoft Azure Batch** dall'elenco dei risultati della ricerca. 
    
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

## <a name="create-an-azure-ssis-ir-and-join-it-to-a-vnet"></a>Creare un IR Azure SSIS e aggiungerla a una rete virtuale
È possibile creare un IR Azure SSIS e aggiungerla a una rete virtuale nello stesso momento. Per il completamento dello script e istruzioni per creare un IR Azure SSIS e aggiungerla a una rete virtuale nello stesso momento, vedere [creare Azure SSIS IR](create-azure-ssis-integration-runtime.md).

## <a name="join-an-existing-azure-ssis-ir-to-a-vnet"></a>Aggiungere un IR SSIS di Azure esistente a una rete virtuale
Lo script di [runtime di integrazione di Azure-SSIS creare](create-azure-ssis-integration-runtime.md) articolo viene illustrato come creare un IR Azure SSIS e aggiungerla a una rete virtuale nello stesso script. Se si dispone di un SSIS di Azure esistente, eseguire i passaggi seguenti per creare un join per la rete virtuale. 

1. Arrestare l'infrarossi SSIS di Azure.
2. Configurare la IR SSIS di Azure per aggiungere la rete virtuale. 
3. Avviare l'infrarossi SSIS di Azure. 

## <a name="define-the-variables"></a>Definire le variabili

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
# Get the following information from the properties page for your Classic Virtual Network in the Azure portal
# It should be in the format: 
# $VnetId = "/subscriptions/<Azure Subscription ID>/resourceGroups/<Azure Resource Group>/providers/Microsoft.ClassicNetwork/virtualNetworks/<Class Virtual Network Name>"
$VnetId = "<Name of your Azure classic virtual netowrk>"
$SubnetName = "<Name of the subnet in VNet>"
```

### <a name="stop-the-azure-ssis-ir"></a>Arrestare il IR Azure SSIS
Arrestare il runtime di integrazione di Azure SSIS prima che sia possibile aggiungerlo a una rete virtuale. Questo comando rilascia tutti i nodi e arresta la fatturazione.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-vnet-settings-for-the-azure-ssis-ir-to-join"></a>Configurare le impostazioni di rete virtuale per il IR SSIS di Azure creare un join
Registrazione al provider di risorse di Azure Batch:

```powershell
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
}
```

### <a name="configure-the-azure-ssis-ir"></a>Configurare la IR Azure SSIS
Eseguire il comando Set-AzureRmDataFactoryV2IntegrationRuntime per configurare il runtime di integrazione di Azure SSIS per unire in join la rete virtuale: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

## <a name="start-the-azure-ssis-ir"></a>Avviare la IR Azure SSIS
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
- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-deploy-ssis-packages-azure.md). In questo articolo vengono fornite le istruzioni passo per passo per creare un runtime di integrazione SSIS di Azure e utilizzare un database Azure SQL per ospitare il catalogo SSIS. 
- [Procedura: come creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e fornisce istruzioni sull'utilizzo dell’istanza gestita di database SQL di Azure (anteprima privata) e sull’aggiunta del runtime di integrazione a un VNet. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo viene illustrato come recuperare informazioni su un runtime di integrazione SSIS di Azure e le descrizioni degli stati nelle informazioni restituite. 
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). In questo articolo viene illustrato come arrestare, avviare o rimuovere un runtime di integrazione SSIS di Azure. Viene inoltre mostrato come scalare orizzontalmente il runtime di integrazione SSIS di Azure aggiungendo più nodi al runtime di integrazione. 
