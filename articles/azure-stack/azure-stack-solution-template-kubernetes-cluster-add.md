---
title: Aggiungere Kubernetes per il Marketplace di Azure Stack | Microsoft Docs
description: Informazioni su come aggiungere Kubernetes in Azure Stack Marketplace.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: b7e293018f2dc248fae8de8bd9e82943606f4ead
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239503"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Aggiungere Kubernetes per il Marketplace di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

> [!note]  
> Kubernetes in Azure Stack è disponibile in anteprima.

È possibile offrire Kubernetes come un elemento del Marketplace per gli utenti. Gli utenti possono distribuire quindi Kubernetes in un'unica operazione coordinata.

Esaminare l'articolo seguente usando un modello di Azure Resource Manager per la distribuzione e provisioning delle risorse per un cluster autonomo di Kubernetes. L'elemento del Marketplace di Cluster Kubernetes 0.3.0 richiede la versione di Azure Stack 1808. Prima di iniziare, verificare le impostazioni di globale tenant di Azure e Azure Stack. Raccogliere le informazioni necessarie su Azure Stack. Aggiungere le risorse necessarie per il tenant e per il Marketplace di Azure Stack. Il cluster dipende da un server Ubuntu, uno script personalizzato e gli elementi di Kubernetes in marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Creare un piano, un'offerta e una sottoscrizione

Creare un piano, un'offerta e una sottoscrizione per l'elemento del Marketplace di Kubernetes. È anche possibile usare un piano esistente e dell'offerta.

1. Accedi al [portale di amministrazione.](https://adminportal.local.azurestack.external)

1. Creare un piano come piano base. Per istruzioni, vedere [creare un piano in Azure Stack](azure-stack-create-plan.md).

1. Creare un'offerta. Per istruzioni, vedere [creare un'offerta in Azure Stack](azure-stack-create-offer.md).

1. Selezionare **offre**e trovare l'offerta è stato creato.

1. Selezionare **Panoramica** nel pannello dell'offerta.

1. Selezionare **modifica stato**. Selezionare **Pubblica**.

1. Selezionare **+ crea una risorsa** > **offerte e piani** > **sottoscrizione** per creare una sottoscrizione.

    a. Immettere un **NomeVisualizzato**.

    b. Immettere un **utente**. Usare l'account di Azure AD associato al tenant.

    c. **Descrizione del provider**

    d. Impostare il **tenant di Directory** al tenant di Azure AD per Azure Stack. 

    e. Selezionare **offrono**. Selezionare il nome dell'offerta, è stato creato. Assicurarsi di annotare l'ID sottoscrizione.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Creare un'entità servizio e le credenziali in AD FS

Se si usa Active Directory Federated Services (ADFS) per il servizio di gestione di identità, è necessario creare un servizio principale per gli utenti di distribuire un cluster Kubernetes.

1. Creare ed esportare un certificato da usare per creare l'entità servizio. Il seguente frammento di codice seguente viene illustrato come creare un certificato autofirmato. 

    - Sono necessarie le seguenti informazioni:

       | Valore | DESCRIZIONE |
       | ---   | ---         |
       | Password | La password del certificato. |
       | Percorso del certificato locale | Il percorso e nome file del certificato. Ad esempio: `path\certfilename.pfx` |
       | Nome del certificato | Il nome del certificato. |
       | Percorso dell'archivio certificati |  Ad esempio: `Cert:\LocalMachine\My` |

    - Aprire PowerShell con privilegi elevati. Eseguire lo script seguente con i parametri aggiornati ai valori di:

        ```PowerShell  
        # Creates a new self signed certificate 
        $passwordString = "<password>"
        $certlocation = "<local certificate path>.pfx"
        $certificateName = "<certificate name>"
        #certificate store location. Eg. Cert:\LocalMachine\My
        $certStoreLocation="<certificate store location>"
        
        $params = @{
        CertStoreLocation = $certStoreLocation
        DnsName = $certificateName
        FriendlyName = $certificateName
        KeyLength = 2048
        KeyUsageProperty = 'All'
        KeyExportPolicy = 'Exportable'
        Provider = 'Microsoft Enhanced Cryptographic Provider v1.0'
        HashAlgorithm = 'SHA256'
        }
        
        $cert = New-SelfSignedCertificate @params -ErrorAction Stop
        Write-Verbose "Generated new certificate '$($cert.Subject)' ($($cert.Thumbprint))." -Verbose
        
        #Exports certificate with password in a .pfx format
        $pwd = ConvertTo-SecureString -String $passwordString -Force -AsPlainText
        Export-PfxCertificate -cert $cert -FilePath $certlocation -Password $pwd
        ```

2. Creare un'entità utilizzando il certificato del servizio.

    - Sono necessarie le seguenti informazioni:

       | Valore | DESCRIZIONE                     |
       | ---   | ---                             |
       | IP ERCS | In ASDK, l'endpoint con privilegi è in genere `AzS-ERCS01`. |
       | Nome dell'applicazione | Un nome semplice dell'entità servizio dell'applicazione. |
       | Percorso dell'archivio certificati | Il percorso nel computer in cui è memorizzato il certificato. Ad esempio: `Cert:\LocalMachine\My\<someuid>` |

    - Aprire PowerShell con privilegi elevati. Eseguire lo script seguente con i parametri aggiornati ai valori di:

        ```PowerShell  
        #Create service principal using the certificate
        $privilegedendpoint="<ERCS IP>"
        $applicationName="<application name>"
        #certificate store location. Eg. Cert:\LocalMachine\My
        $certStoreLocation="<certificate store location>"
        
        # Get certificate information
        $cert = Get-Item $certStoreLocation
        
        # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
        $creds = Get-Credential

        # Creating a PSSession to the ERCS PrivilegedEndpoint
        $session = New-PSSession -ComputerName $privilegedendpoint -ConfigurationName PrivilegedEndpoint -Credential $creds

        # Get Service principal Information
        $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name "$using:applicationName" -ClientCertificates $using:cert}

        # Get Stamp information
        $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }

        # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
        $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

        # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
        $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

        # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
        $TenantID = $AzureStackInfo.AADTenantID

        # Register an AzureRM environment that targets your Azure Stack instance
        Add-AzureRMEnvironment `
        -Name "AzureStackUser" `
        -ArmEndpoint $ArmEndpoint

        # Set the GraphEndpointResourceId value
        Set-AzureRmEnvironment `
        -Name "AzureStackUser" `
        -GraphAudience $GraphAudience `
        -EnableAdfsAuthentication:$true
        Add-AzureRmAccount -EnvironmentName "azurestackuser" `
        -ServicePrincipal `
        -CertificateThumbprint $ServicePrincipal.Thumbprint `
        -ApplicationId $ServicePrincipal.ClientId `
        -TenantId $TenantID

        # Output the SPN details
        $ServicePrincipal
        ```

    - I dettagli dell'entità servizio di ricerca, ad esempio il frammento di codice seguente

        ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
        ```

## <a name="add-an-ubuntu-server-image"></a>Aggiungere un'immagine di Ubuntu server

Nel Marketplace, aggiungere l'immagine di Ubuntu Server seguente:

1. Accedi per il [portale di amministrazione](https://adminportal.local.azurestack.external).

1. Selezionare **tutti i servizi**, quindi nel **amministrazione** categoria, seleziona **gestione Marketplace**.

1. Selezionare **+ Aggiungi da Azure**.

1. Immettere `UbuntuServer`.

1. Selezionare la versione più recente del server. Controllare la versione completa e assicurarsi di avere la versione più recente:
    - **Publisher** (Editore): Canonical
    - **Offer** (Offerta): UbuntuServer
    - **Versione**: 16.04.201806120 (o versione più recente)
    - **SKU**: 16.04-LTS

1. Selezionare **scaricare.**

## <a name="add-a-custom-script-for-linux"></a>Aggiungere uno script personalizzato per Linux

Aggiunta di Kubernetes da Marketplace:

1. Aprire il [portale di amministrazione](https://adminportal.local.azurestack.external).

1. Selezionare **tutti i servizi** e quindi nel **amministrazione** categoria, seleziona **gestione Marketplace**.

1. Selezionare **+ Aggiungi da Azure**.

1. Immettere `Custom Script for Linux`.

1. Selezionare lo script con il seguente profilo:
    - **Offer** (Offerta): Script personalizzato per Linux 2.0
    - **Versione**: 2.0.6 (o versione più recente)
    - **Publisher** (Editore): Microsoft Corp

    > [!Note]  
    > Potrebbe essere presente più di una versione dello Script personalizzata per Linux. È necessario aggiungere l'ultima versione dell'elemento.

1. Selezionare **scaricare.**


## <a name="add-kubernetes-to-the-marketplace"></a>Aggiungere Kubernetes nel Marketplace

1. Aprire il [portale di amministrazione](https://adminportal.local.azurestack.external).

1. Selezionare **tutti i servizi** e quindi nel **amministrazione** categoria, seleziona **gestione Marketplace**.

1. Selezionare **+ Aggiungi da Azure**.

1. Immettere `Kubernetes`.

1. Selezionare `Kubernetes Cluster`.

1. Selezionare **scaricare.**

    > [!note]  
    > Può richiedere cinque minuti per l'elemento del marketplace venga visualizzato nel Marketplace.

    ![kubernetes](user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Aggiornare o rimuovere di Kubernetes 

Quando si aggiorna l'elemento di Kubernetes, è rimuoverai l'elemento precedente nel Marketplace. Seguire le istruzioni in questo articolo per aggiungere che il Kubernetes aggiornare nel Marketplace.

Per rimuovere l'elemento di Kubernetes:

1. Connettersi ad Azure Stack con PowerShell come un operatore. Per istruzioni, vedere [connettersi ad Azure Stack con PowerShell come un operatore](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-admin).

2. Trovare l'elemento di un Kubernetes Cluster corrente nella raccolta.

    ```PowerShell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Si noti, ad esempio nome dell'elemento corrente, `Microsoft.AzureStackKubernetesCluster.0.3.0`

4. Usare il cmdlet di PowerShell seguente per rimuovere l'elemento:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Passaggi successivi

[Distribuire un Kubernetes in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

[Panoramica dell'offerta di servizi in Azure Stack](azure-stack-offer-services-overview.md)
