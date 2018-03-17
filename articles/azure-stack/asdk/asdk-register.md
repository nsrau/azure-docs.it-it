---
title: Registrare il ASDK con Azure | Documenti Microsoft
description: Viene descritto come registrare dello Stack di Azure con Azure per abilitare la diffusione di marketplace e report sull'utilizzo.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 9e2dbc71f6424b87945e346a42c86d4cde7f740e
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="register-azure-stack-with-azure"></a>Registro dello Stack di Azure con Azure
È possibile registrare l'installazione di Azure Stack Development Kit (ASDK) con Azure per scaricare gli elementi di marketplace da Azure e per impostare i dati di commerce segnalazioni a Microsoft. La registrazione è consigliata perché consente di testare importanti funzionalità di Stack di Azure, ad esempio diffusione marketplace e report sull'utilizzo. Dopo la registrazione dello Stack di Azure, viene segnalato l'utilizzo per Azure commerce. È possibile visualizzarlo nella sottoscrizione che è usata per la registrazione. Tuttavia, gli utenti ASDK non sono applicati addebiti per qualsiasi utilizzo che fanno.


## <a name="register-azure-stack-with-azure"></a>Registro dello Stack di Azure con Azure 
Seguire questi passaggi per registrare il ASDK con Azure.

> [!NOTE]
> Da un computer dotato di accesso all'endpoint con privilegi, è necessario eseguire tutti questi passaggi. Per ASDK, ovvero il computer host kit di sviluppo. 

Prima di usare questi passaggi per registrare il ASDK con Azure, assicurarsi di avere installato Azure Stack PowerShell e scaricare gli strumenti di Azure Stack, come descritto nel [configurazione post-distribuzione](asdk-post-deploy.md) articolo. 

1. Aprire la console PowerShell come amministratore.  

2. Eseguire i seguenti comandi di PowerShell per registrare l'installazione ASDK con Azure (è necessario accedere alla sottoscrizione di Azure sia l'installazione locale di ASDK):

    ```PowerShell
    # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
    Add-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
    Set-AzsRegistration `
        -CloudAdminCredential $CloudAdminCred `
        -PrivilegedEndpoint AzS-ERCS01 `
        -BillingModel Development

3. When the script completes, you should see this message: **Your environment is now registered and activated using the provided parameters.**

    ![](media/asdk-register/1.PNG) 

## Verify the registration was successful
Follow these steps to verify that the ASDK registration with Azure was successful.

1. Sign in to the [Azure Stack administration portal](https://adminportal.local.azurestack.external).

2. Click **Marketplace Management** > **Add from Azure**.

    ![](media/asdk-register/2.PNG) 

3. If you see a list of items available from Azure, your activation was successful.

    ![](media/asdk-register/3.PNG) 

## Next steps
[Add an Azure Stack marketplace item](asdk-marketplace-item.md)