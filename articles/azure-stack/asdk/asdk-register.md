---
title: Registrare il ASDK con Azure | Microsoft Docs
description: Viene descritto come registrare Azure Stack con Azure per abilitare la diffusione di marketplace e report sull'utilizzo.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: d06ad47dc2962b249b4e7aef5667492e642be35e
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830124"
---
# <a name="azure-stack-registration"></a>Registrazione con Azure Stack
È possibile registrare l'installazione di Azure Stack Development Kit (ASDK) con Azure per scaricare elementi di marketplace di Azure e per impostare i dati di e-commerce segnalazioni a Microsoft. È necessario eseguire la registrazione per supportare la funzionalità di Azure Stack completa, tra cui diffusione di marketplace. La registrazione è consigliata perché consente di testare le funzionalità di Azure Stack importanti come la diffusione di marketplace e report sull'utilizzo. Dopo la registrazione di Azure Stack, sull'utilizzo viene segnalato ad Azure commerce. È possibile visualizzarlo nella sottoscrizione che è usata per la registrazione. Tuttavia, gli utenti ASDK non vengono addebitate spese per qualsiasi utilizzo che generano report.

Se non si registrano i ASDK, è possibile visualizzare un **attivazione obbligatoria** messaggio di avviso che richiede di eseguire la registrazione di Azure Stack Development Kit. Si tratta di un comportamento previsto.

## <a name="prerequisites"></a>Prerequisiti
Prima di usare queste istruzioni per registrare il ASDK con Azure, assicurarsi di avere installato di PowerShell per Azure Stack e scaricare gli strumenti di Azure Stack come descritto nel [configurazione post-distribuzione](asdk-post-deploy.md) articolo.

Inoltre, la modalità di linguaggio di PowerShell deve essere impostata su **FullLanguageMode** nel computer usato per registrare il ASDK con Azure. Per verificare che la modalità linguaggio corrente è impostata su full, aprire una finestra di PowerShell con privilegi elevata ed eseguire i comandi PowerShell seguenti:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Verificare che l'output restituisce **FullLanguageMode**. Se viene restituita qualsiasi altra modalità di linguaggio, registrazione dovrà essere eseguito in un altro computer o la modalità di linguaggio dovrà essere impostata su **FullLanguageMode** prima di continuare.

## <a name="register-azure-stack-with-azure"></a>Registrare Azure Stack con Azure
Seguire questi passaggi per registrare il ASDK con Azure.

> [!NOTE]
> Tutti questi passaggi devono essere eseguiti da un computer dotato di accesso all'endpoint con privilegi. Per ASDK, ovvero il computer host kit di sviluppo.

1. Aprire una console PowerShell come amministratore.  

2. Eseguire i comandi di PowerShell seguenti per registrare l'installazione ASDK con Azure. È necessario accedere a sia la sottoscrizione di Azure e l'installazione ASDK locale. Se non hai una sottoscrizione di Azure, è possibile [crea qui un account Azure gratuito](https://azure.microsoft.com/free/?b=17.06). La registrazione di Azure Stack viene addebitata alcuna tariffa nella sottoscrizione di Azure.<br><br>Se si esegue lo script di registrazione in più di un'istanza di Azure Stack usando lo stesso ID di sottoscrizione di Azure, impostare un nome univoco per la registrazione quando si esegue la **Set-AzsRegistration** cmdlet. Il **RegistrationName** parametro ha valore predefinito è **AzureStackRegistration**. Tuttavia, se si usa lo stesso nome in più di un'istanza di Azure Stack, lo script avrà esito negativo.

    ```PowerShell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    $UsageReporting = True # Set to False if you using a Capacity Billing Model
    Set-AzsRegistration `
        -PrivilegedEndpointCredential $CloudAdminCred `
        -PrivilegedEndpoint AzS-ERCS01 `
        -BillingModel Development `
        -RegistrationName $RegistrationName `
        -EnableUsageReporting $UsageReporting
    ```
3. Al termine dell'esecuzione dello script, verrà visualizzato questo messaggio: **l'ambiente è ora registrato e attivati mediante i parametri specificati.**

    ![L'ambiente è ora registrato](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>Verificare che la registrazione ha avuto esito positivo
Seguire questi passaggi per verificare che la registrazione ASDK con Azure è riuscita.

1. Accedi per il [portale di amministrazione di Azure Stack](https://adminportal.local.azurestack.external).

2. Fare clic su **Marketplace Management** > **aggiungere da Azure**.

    ![](media/asdk-register/2.PNG)

3. Se viene visualizzato un elenco di elementi disponibili in Azure, l'attivazione è riuscita.

    ![](media/asdk-register/3.PNG)

## <a name="move-a-registration-resource"></a>Spostare una risorsa di registrazione
Lo spostamento di una risorsa di registrazione tra gruppi di risorse nella stessa sottoscrizione **è** supportati. Per altre informazioni sullo spostamento delle risorse in un nuovo gruppo di risorse, vedere [spostare le risorse in un nuovo gruppo di risorse o sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Passaggi successivi
[Aggiungere un elemento del marketplace Azure Stack](.\.\azure-stack-marketplace.md)
