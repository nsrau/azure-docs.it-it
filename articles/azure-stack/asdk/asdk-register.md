---
title: Registrare il ASDK con Azure | Documenti Microsoft
description: Viene descritto come registrare dello Stack di Azure con Azure per abilitare la diffusione di marketplace e report sull'utilizzo.
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
ms.date: 06/04/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: bc17045106b2656cdeb5f51037b3138aeac9ee0f
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757468"
---
# <a name="azure-stack-registration"></a>Registrazione di Azure Stack
È possibile registrare l'installazione di Azure Stack Development Kit (ASDK) con Azure per scaricare gli elementi di marketplace da Azure e per impostare i dati di commerce segnalazioni a Microsoft. La registrazione è necessario per supportare le funzionalità complete dello Stack di Azure, tra cui diffusione marketplace. La registrazione è consigliata perché consente di testare importanti funzionalità di Stack di Azure, ad esempio diffusione marketplace e report sull'utilizzo. Dopo la registrazione dello Stack di Azure, viene segnalato l'utilizzo per Azure commerce. È possibile visualizzarlo nella sottoscrizione che è usata per la registrazione. Tuttavia, gli utenti ASDK non sono applicati addebiti per qualsiasi utilizzo che fanno.

Se non si registra il ASDK, si potrebbero riscontrare un **attivazione necessaria** messaggio di avviso che consiglia di registrare il Kit di sviluppo dello Stack di Azure. Questo comportamento è previsto.

## <a name="prerequisites"></a>Prerequisiti
Prima di usare queste istruzioni per registrare il ASDK con Azure, assicurarsi di avere installato Azure Stack PowerShell e scaricare gli strumenti di Azure Stack, come descritto nel [configurazione post-distribuzione](asdk-post-deploy.md) articolo.

Inoltre, è necessario impostare la modalità di linguaggio di PowerShell **FullLanguageMode** nel computer usato per registrare il ASDK con Azure. Per verificare che la modalità di lingua corrente è impostata su full, aprire una finestra di PowerShell con privilegi elevata ed eseguire i comandi di PowerShell seguenti:

```powershell
$ExecutionContext.SessionState.LanguageMode
```

Verificare che l'output restituisce **FullLanguageMode**. Se viene restituita qualsiasi altra modalità di linguaggio, registrazione dovrà essere eseguito in un altro computer o la modalità di linguaggio dovrà essere impostata su **FullLanguageMode** prima di continuare.

## <a name="register-azure-stack-with-azure"></a>Registro dello Stack di Azure con Azure
Seguire questi passaggi per registrare il ASDK con Azure.

> [!NOTE]
> Da un computer dotato di accesso all'endpoint con privilegi, è necessario eseguire tutti questi passaggi. Per ASDK, ovvero il computer host kit di sviluppo.

1. Aprire la console PowerShell come amministratore.  

2. Eseguire i comandi di PowerShell seguenti per registrare l'installazione ASDK con Azure. È necessario accedere a sia la sottoscrizione di Azure e l'installazione ASDK locale. Se non si dispone di una sottoscrizione di Azure, è possibile [creare un account gratuito di Azure qui](https://azure.microsoft.com/free/?b=17.06). Registrazione dello Stack di Azure non comporta alcun costo nella sottoscrizione di Azure.

  ```powershell
  # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
  Add-AzureRmAccount -EnvironmentName "AzureCloud"

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  #Register Azure Stack
  $AzureContext = Get-AzureRmContext
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01 `
      -BillingModel Development
  ```
3. Al completamento dello script, si dovrebbe essere visualizzato questo messaggio: **l'ambiente è ora registrato e attivati mediante i parametri forniti.**

    ![](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>Verificare che la registrazione ha avuto esito positivo
Seguire questi passaggi per verificare che la registrazione ASDK con Azure completata correttamente.

1. Accedi ai [portale di amministrazione di Azure Stack](https://adminportal.local.azurestack.external).

2. Fare clic su **gestione Marketplace** > **aggiungere da Azure**.

    ![](media/asdk-register/2.PNG)

3. Se viene visualizzato un elenco di elementi disponibili in Azure, l'attivazione ha avuto successo.

    ![](media/asdk-register/3.PNG)

## <a name="next-steps"></a>Passaggi successivi
[Aggiungere un elemento del marketplace Azure Stack](asdk-marketplace-item.md)
