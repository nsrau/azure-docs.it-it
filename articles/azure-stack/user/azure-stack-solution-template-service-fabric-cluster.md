---
title: Distribuire un cluster di Service Fabric protetto in Azure Stack | Microsoft Docs
description: Informazioni su come distribuire un cluster di Service Fabric protetto in Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: shnatara
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 8041e7e02b117b8938f0f7c18da2d57c31dddb34
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58482265"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Distribuire un cluster di Service Fabric in Azure Stack

Usare la **Cluster di Service Fabric** elemento da Azure Marketplace per distribuire un cluster di Service Fabric protetto in Azure Stack. 

Per altre informazioni sull'utilizzo di Service Fabric, vedere [Panoramica di Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) e [scenari di sicurezza del cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security), nella documentazione di Azure.

Cluster di Service Fabric in Azure Stack non usa il provider di risorse Microsoft. servicefabric. In Azure Stack, il cluster di Service Fabric tratta piuttosto di scalabilità di macchine virtuali impostate con set di prodotti software preinstallati con Desired State Configuration (DSC).

## <a name="prerequisites"></a>Prerequisiti

Per distribuire il cluster di Service Fabric è necessario quanto segue:
1. **Certificato del cluster**  
   Questo è il certificato server X.509 che è aggiungere a Key Vault durante la distribuzione di Service Fabric. 
   - Il **CN** su questo certificato deve corrispondere il nome di dominio completo (FQDN) del cluster di Service Fabric si crea. 
   - Il formato del certificato deve essere PFX, come le chiavi pubbliche e private sono necessarie. 
     Visualizzare [requisiti](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) per la creazione di questo certificato sul lato server.

     > [!NOTE]  
     > È possibile usare un certificato autofirmato inplace del certificato server x.509 per scopi di test. I certificati autofirmati non sono necessario corrispondere al nome FQDN del cluster.

1. **Certificato Client amministratore** si tratta del certificato che il client userà per l'autenticazione a del cluster di Service Fabric, che può essere auto-firmato. Visualizzare [requisiti](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) per la creazione di questo certificato client.

1. **Gli elementi seguenti devono essere disponibili in Azure Stack Marketplace:**
    - **Windows Server 2016** : il modello Usa l'immagine di Windows Server 2016 per creare il cluster.  
    - **Estensione Script cliente** -estensione della macchina virtuale di Microsoft.  
    - **Configurazione fase desiderato tramite PowerShell** -estensione della macchina virtuale di Microsoft.


## <a name="add-a-secret-to-key-vault"></a>Aggiungere un segreto all'istanza di Key Vault
Per distribuire un cluster di Service Fabric, è necessario specificare l'insieme di credenziali corretto *identificatore di segreto* o l'URL per il cluster di Service Fabric. Il modello di Azure Resource Manager riceve un Key Vault come input. Il modello recupera quindi il certificato del Cluster durante l'installazione del cluster di Service Fabric.

> [!IMPORTANT]  
> È necessario usare PowerShell per aggiungere un segreto nell'insieme di credenziali per l'uso con Service Fabric. Non usare il portale.  

Usare lo script seguente per creare l'insieme di credenziali e aggiungere il *certificato cluster* ad esso. (Vedere la [prerequisiti](#prerequisites).) Prima di eseguire lo script, esaminare lo script di esempio e aggiornare i parametri indicati per adattarli all'ambiente. Questo script restituirà anche i valori che è necessario fornire al modello di Azure Resource Manager. 

> [!TIP]  
> Prima che lo script può avere esito positivo, deve essere presente una pubblica offerta che include i servizi di calcolo, rete, archiviazione e Key Vault. 

  ```powershell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


Per altre informazioni, vedere [gestire Key Vault in Azure Stack con PowerShell](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-key-vault-manage-powershell).

## <a name="deploy-the-marketplace-item"></a>Distribuire l'elemento del Marketplace

1. Nel portale per gli utenti, passare a **+ crea una risorsa** > **calcolo** > **Cluster di Service Fabric**. 

   ![Selezionare i Cluster di Service Fabric](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

1. Per ogni pagina, ad esempio *nozioni di base*, compilare il modulo di distribuzione. Se non si è certi di un valore, usare le impostazioni predefinite. Selezionare **OK** per passare alla pagina successiva:

   ![Nozioni di base](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

1. Nel *le impostazioni di rete* pagina, è possibile specificare determinate porte da aprire per le applicazioni:

   ![Impostazioni di rete](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

1. Nel *sicurezza* pagina, aggiungere i valori ottenuti nel [creazione di Azure Key Vault](#add-a-secret-to-key-vault) e caricare il segreto.

   Per il *identificazione personale del certificato Client amministratore*, immettere l'identificazione personale del *certificato Client amministratore*. (Vedere la [prerequisiti](#prerequisites).)
   
   - Origine chiave dell'insieme di credenziali:  Specificare intera *keyVault id* stringa dai risultati dello script. 
   - URL del certificato del cluster: Specificare l'intero URL dal *Id segreto* dai risultati dello script. 
   - Identificazione personale del certificato del cluster: Specificare il *identificazione personale del certificato Cluster* dai risultati dello script.
   - Identificazioni personali del certificato Client amministratore: Specificare il *identificazione personale del certificato Client amministratore* creato nei prerequisiti. 

   ![Output dello script](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Security](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

1. Completare la procedura guidata e quindi selezionare **Create** per distribuire il Cluster di Service Fabric.



## <a name="access-the-service-fabric-cluster"></a>Accedere al Cluster di Service Fabric
È possibile accedere al cluster di Service Fabric usando Service Fabric Explorer o PowerShell in Service Fabric.


### <a name="use-service-fabric-explorer"></a>Uso di Service Fabric Explorer
1.  Verificare che il browser Web ha accesso al certificato client amministratore e che possa autenticarsi con il cluster di Service Fabric.  

    a. Aprire Internet Explorer e passare a **Opzioni Internet** > **contenuto** > **certificati**.
  
    b. Sui certificati, selezionare **importazione** per avviare la *importazione guidata certificati*, quindi fare clic su **Avanti**. Nel *File da importare* pagina fare clic su **Sfoglia**e selezionare il **certificato Client amministratore** specificato per il modello di Azure Resource Manager.
        
       > [!NOTE]  
       > Questo certificato non è il certificato del Cluster che è stata aggiunta in precedenza nell'insieme di credenziali.  

    c. Assicurarsi di aver "Scambio di informazioni personali" selezionato nell'elenco a discesa estensione della finestra di Esplora File.  

       ![Scambio informazioni personali](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. Nel *Store certificato* pagina, selezionare **personale**, quindi completare la procedura guidata.  
       ![Archivio certificati](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
1. Per trovare il nome FQDN del cluster di Service Fabric:  

    a. Passare al gruppo di risorse associato con l'infrastruttura di servizio del cluster e individuare il *indirizzo IP pubblico* risorsa. Selezionare l'oggetto associato all'indirizzo IP pubblico per aprire la *indirizzo IP pubblico* pannello.  

      ![Indirizzo IP pubblico](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. Nel pannello dell'indirizzo IP pubblico, viene visualizzato il nome di dominio completo come *nome DNS*.  

      ![Nome DNS](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

1. Per trovare l'URL per Service Fabric Explorer e l'endpoint della connessione Client, esaminare i risultati della distribuzione del modello.

1. Nel browser, passare a https://*FQDN*: 19080. Sostituire *FQDN* con il nome FQDN del cluster di Service Fabric dal passaggio 2.   
   Se si usa un certificato autofirmato, si riceverà un avviso che la connessione non è protetta. Per procedere al sito web, selezionare **per altre informazioni**e quindi **passare alla pagina Web**. 

1. Per eseguire l'autenticazione al sito, è necessario selezionare un certificato da usare. Selezionare **altre scelte**, selezionare il certificato appropriato e quindi fare clic su **OK** per connettersi a Service Fabric Explorer. 

   ![Autentica](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>Usare PowerShell per Service Fabric

1. Installare il *Microsoft Azure Service Fabric SDK* dalla [preparare l'ambiente di sviluppo in Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) nella documentazione di Azure Service Fabric.  

1. Al termine dell'installazione, configurare le variabili di ambiente di sistema per garantire che i cmdlet di Service Fabric sono accessibili da PowerShell.  
    
    a. Passare a **Pannello di controllo** > **sistema e sicurezza** > **sistema**, quindi selezionare **impostazionidisistemaavanzate**.  
    
      ![Pannello di controllo](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. Nel **avanzate** scheda della finestra *le proprietà di sistema*, selezionare **variabili di ambiente**.  

    c. Per *variabili di sistema*, modificare **Path** e verificare che **c:\\i file di programma\\Microsoft Service Fabric\\bin\\Fabric \\Fabric.Code** si trovi all'inizio dell'elenco di variabili di ambiente.  

      ![Elenco di variabili di ambiente](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

1. Dopo aver modificato l'ordine delle variabili di ambiente, riavviare PowerShell ed eseguire lo script di PowerShell seguente per ottenere l'accesso al cluster di Service Fabric:

   ```powershell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ```
   
   > [!NOTE]  
   > È presente alcun *https://* prima del nome del cluster nello script. Porta 19000 è obbligatoria.

## <a name="next-steps"></a>Passaggi successivi

[Distribuzione di Kubernetes in Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)
