---
title: Distribuire un cluster di Service Fabric protetto nello Stack di Azure | Documenti Microsoft
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
ms.date: 05/08/2018
ms.author: mattbriggs
ms.reviewer: shnatara
ms.openlocfilehash: a88d8dd2af94ac796a3b2e3c667fd40a308f02a1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
ms.locfileid: "33883023"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Distribuire un cluster di Service Fabric nello Stack di Azure

Usare la **Cluster di Service Fabric** elemento da Azure Marketplace per distribuire un cluster di Service Fabric protetto nello Stack di Azure. 

Per ulteriori informazioni sull'utilizzo di Service Fabric, vedere [Panoramica di Azure Service Frabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) e [scenari di sicurezza di Service Fabric cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security), nella documentazione di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per distribuire il cluster di Service Fabric è richiesto quanto segue:
1. **Certificato di cluster**  
   Questo è il certificato del server x. 509 che aggiungere KeyVault durante la distribuzione di Service Fabric. 
   - Il **CN** in questo certificato deve corrispondere il nome di dominio completo (FQDN) del cluster di Service Fabric è creare. 
   - Il formato del certificato deve essere PFX, come le chiavi pubbliche e private sono necessarie. 
   Vedere [requisiti](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) per la creazione di questo certificato sul lato server.

    > [!NOTE]  
    > È possibile utilizzare un certificato autofirmato inplace del certificato del server x.509 per scopi di test. I certificati autofirmati non sono necessario associare il nome FQDN del cluster.

2.  **Certificato Admin Client** si tratta del certificato che il client utilizzerà per l'autenticazione per il cluster di Service Fabric, può essere autofirmato. Vedere [requisiti](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) per la creazione di questo certificato client.

3.  **Gli elementi seguenti devono essere disponibili in Azure Marketplace dello Stack:**
     - **Windows Server 2016** : il modello Usa l'immagine di Windows Server 2016 per creare il cluster.  
     - **Estensione dello Script cliente** -estensione della macchina virtuale da Microsoft.  
     - **Configurazione della fase desiderato tramite PowerShell** -estensione della macchina virtuale da Microsoft.


## <a name="add-a-secret-to-key-vault"></a>Aggiungere un segreto all'istanza di Key Vault
Per distribuire un cluster di Service Fabric, è necessario specificare il corretto KeyVault *identificatore di segreto* o l'URL per il cluster di Service Fabric. Il modello di gestione risorse di Azure accetta un KeyVault come input e quindi recupera il certificato del Cluster durante l'installazione del cluster di Service Fabric. 

> [!IMPORTANT]  
> È necessario usare PowerShell per aggiungere una chiave privata a KeyVault per l'utilizzo con Service Fabric. Non usare il portale.  

Usare lo script seguente per creare il KeyVault e aggiungere la *certificato cluster* a esso. (Vedere la [prerequisiti](#prerequisites).) Prima di eseguire lo script, rivedere lo script di esempio e aggiornare i parametri indicati in base all'ambiente. Questo script verrà anche restituiti i valori che è necessario fornire al modello di gestione risorse di Azure. 

> [!TIP]  
> Prima che lo script può avere esito positivo, deve essere presente un'offerta pubblica che include i servizi di calcolo, rete, archiviazione e KeyVault. 

  ```PowerShell
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


Per altre informazioni, vedere [gestire KeyVault nello Stack di Azure con PowerShell](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-kv-manage-powershell).

## <a name="deploy-the-marketplace-item"></a>Distribuire l'elemento del Marketplace

1. Nel portale per gli utenti, passare a **New** > **calcolo** > **Cluster di Service Fabric**. 

   ![Selezionare i Cluster di Service Fabric](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

2. Per ogni pagina, ad esempio *nozioni di base*, compilare il modulo di distribuzione. Utilizzare i valori predefiniti se non si è certi di un valore. Selezionare **OK** per passare alla pagina successiva:

   ![Nozioni di base](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

3. Nel *le impostazioni di rete* pagina, è possibile specificare specifiche porte da aprire per le applicazioni:

   ![Impostazioni di rete](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

4. Nel *protezione* pagina, aggiungere i valori ottenuti nel [creazione il KeyVault Azure](#add-a-secret-to-key-vault) e caricare la chiave privata.

   Per il *identificazione personale del certificato Client Admin*, immettere l'identificazione personale del *certificato Client di amministrazione*. (Vedere la [prerequisiti](#prerequisites).)
   
   - Insieme di credenziali chiave di origine: Specificare intera *keyVault id* stringa dai risultati dello script. 
   - URL di certificato del cluster: Specificare l'URL intero dal *segreto Id* dai risultati dello script. 
   - Identificazione personale del certificato del cluster: specificare il *identificazione personale del certificato Cluster* dai risultati dello script.
   - Identificazioni personali del certificato Client di amministrazione: Specificare il *identificazione personale del certificato Client Admin* creato nei prerequisiti. 

   ![Output dello script](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Sicurezza](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

5. Completare la procedura guidata e quindi selezionare **crea** per distribuire il Cluster di Service Fabric.



## <a name="access-the-service-fabric-cluster"></a>Accedere al Cluster di Service Fabric
Il cluster di Service Fabric è possibile accedere usando Service Fabric Explorer o PowerShell per Service Fabric.


### <a name="use-service-fabric-explorer"></a>Utilizzare Service Fabric Explorer
1.  Verificare che il browser ha accesso al certificato client di amministrazione e possa autenticarsi con il cluster di Service Fabric.  

    a. Aprire Internet Explorer e passare a **Opzioni Internet** > **contenuto** > **certificati**.
  
    b. Sui certificati, selezionare **importazione** per avviare la *importazione guidata certificati*, quindi fare clic su **Avanti**. Nel *File da importare* pagina fare clic su **Sfoglia**e selezionare il **certificato Admin Client** fornito per il modello di gestione risorse di Azure.
        
       > [!NOTE]  
       > Questo certificato non è il certificato del Cluster che è stato aggiunto in precedenza al KeyVault.  

    c. Assicurarsi di aver "Scambio di informazioni personali" selezionato nell'elenco a discesa estensione della finestra di Esplora File.  

       ![Scambio di informazioni personali](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. Nel *archivio certificati* pagina, selezionare **personale**e quindi completare la procedura guidata.  
       ![Archivio certificati](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
2. Per trovare il nome FQDN del cluster di Service Fabric:  

    a. Passare al gruppo di risorse associato con l'infrastruttura di servizio del cluster e individuare il *indirizzo IP pubblico* risorse. Selezionare l'oggetto associato all'indirizzo IP pubblico per aprire la *indirizzo IP pubblico* blade.  

      ![Indirizzo IP pubblico](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. Nel Pannello di indirizzo IP pubblico, il nome di dominio completo viene visualizzato come *nome DNS*.  

      ![Nome DNS](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

3. Per trovare l'URL di Service Fabric Explorer e l'endpoint della connessione Client, esaminare i risultati della distribuzione del modello.

4. Nel browser, passare a https://*FQDN*: 19080. Sostituire *FQDN* con il nome FQDN del cluster di Service Fabric dal passaggio 2.   
   Se è stato usato un certificato autofirmato, si otterrà un avviso che la connessione non sicura. Per procedere con il sito web, selezionare **informazioni**, quindi **andare alla pagina Web**. 

5. Per l'autenticazione con il sito è necessario selezionare un certificato da usare. Selezionare **altre scelte**, selezionare il certificato appropriato e quindi fare clic su **OK** per la connessione a Service Fabric Explorer. 

   ![Autentica](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>Utilizzare PowerShell per Service Fabric

1. Installare il *Microsoft Azure Service Fabric SDK* da [preparare l'ambiente di sviluppo in Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) nella documentazione di Azure Service Fabric.  

2. Al termine dell'installazione, configurare le variabili di ambiente di sistema per verificare che i cmdlet di Service Fabric siano accessibili da PowerShell.  
    
    a. Passare a **Pannello di controllo** > **sistema e sicurezza** > **sistema**, quindi selezionare **impostazionidisistemaavanzate**.  
    
      ![Pannello di controllo](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. Nel **avanzate** scheda della *le proprietà di sistema*, selezionare **le variabili di ambiente**.  

    c. Per *variabili di sistema*, modificare **percorso** e verificare che **c:\\programmi\\Microsoft Service Fabric\\bin\\dell'infrastruttura \\Fabric.Code** si trovi all'inizio dell'elenco di variabili di ambiente.  

      ![Elenco di variabili di ambiente](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

3. Dopo aver modificato l'ordine delle variabili di ambiente, riavviare PowerShell e quindi eseguire lo script di PowerShell seguente per ottenere l'accesso al cluster di Service Fabric:

   ````PowerShell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ````
   
   > [!NOTE]  
   > È presente alcun *https://* prima del nome del cluster nello script. Porta 19000 è obbligatoria.
 
