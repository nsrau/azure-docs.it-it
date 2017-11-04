---
title: Registro dello Stack di Azure | Documenti Microsoft
description: Registro dello Stack di Azure con la sottoscrizione di Azure.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: erikje
ms.openlocfilehash: 24cde66a132ae2e1ba0eb9b1564915746e5ca448
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2017
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Registro dello Stack di Azure con la sottoscrizione di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile registrare [Azure Stack](azure-stack-poc.md) con Azure per scaricare gli elementi di marketplace da Azure e per impostare i dati di commerce segnalazioni a Microsoft. 

> [!NOTE]
>La registrazione è consigliata perché consente di testare importanti funzionalità di Stack di Azure, ad esempio diffusione marketplace e report di utilizzo. Dopo la registrazione dello Stack di Azure, viene segnalato l'utilizzo per Azure commerce. È possibile visualizzarlo nella sottoscrizione che è usata per la registrazione. Gli utenti di Azure il Kit di sviluppo dello Stack non sono addebitati qualsiasi utilizzo che fanno.
>


## <a name="get-azure-subscription"></a>Ottenere una sottoscrizione di Azure

Prima di registrare dello Stack di Azure con Azure, è necessario disporre di:

- L'ID di sottoscrizione per una sottoscrizione di Azure. Per ottenere l'ID, accedere a Azure, fare clic su **più servizi** > **sottoscrizioni**, fare clic sulla sottoscrizione che si desidera utilizzare, quindi in **Essentials** è possibile trovare il **ID sottoscrizione**. Cina, Germania e noi sottoscrizioni cloud per enti pubblici non sono attualmente supportati.
- Il nome utente e password per un account che è un proprietario per la sottoscrizione (sono supportati account MSA/2FA).
- Azure Active Directory per la sottoscrizione di Azure. È possibile trovare la directory in Azure passando sul proprio avatar in alto a destra del portale di Azure. 
- [Registrare il provider di risorse di Azure Stack](#register-azure-stack-resource-provider-in-azure).

Se non si dispone di una sottoscrizione di Azure che soddisfa questi requisiti, è possibile [creare un account gratuito di Azure qui](https://azure.microsoft.com/en-us/free/?b=17.06). Registrazione dello Stack di Azure non comporta alcun costo nella sottoscrizione di Azure.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Registrare il provider di risorse di Azure Stack in Azure
> [!NOTE] 
> Questo passaggio solo deve essere completata una volta in un ambiente dello Stack di Azure.
>

1. Avviare Powershell ISE come amministratore.
2. Accedi all'account di Azure che è un proprietario della sottoscrizione di Azure con parametro - EnvironmentName impostato su "Cloud".
3. Registrare il provider di risorse di Azure "Microsoft.AzureStack".

Esempio: 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```


## <a name="register-azure-stack-with-azure"></a>Registro dello Stack di Azure con Azure

> [!NOTE]
>Tutti questi passaggi da completare sul computer host.
>

1. [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md). 
2. Copia il [RegisterWithAzure.psm1 script](https://go.microsoft.com/fwlink/?linkid=842959) in una cartella (ad esempio C:\Temp).
3. Avviare PowerShell ISE come amministratore e importare il modulo RegisterWithAzure.    
4. Lo script RegisterWithAzure.psm1, eseguire il modulo Aggiungi AzsRegistration. Sostituire i segnaposto seguenti: 
    - *YourCloudAdminCredential* è un oggetto PowerShell che contiene le credenziali di dominio locale per il domain\cloudadmin (per il kit di sviluppo, si tratta di azurestack\cloudadmin).
    - *YourAzureSubscriptionID* è l'ID della sottoscrizione di Azure che si desidera utilizzare per registrare dello Stack di Azure.
    - *YourAzureDirectoryTenantName* è il nome della directory tenant di Azure associati alla sottoscrizione di Azure. La risorsa di registrazione verrà creata in questo tenant di directory. 
    - *YourPrivilegedEndpoint* è il nome del [punto finale con privilegi](azure-stack-privileged-endpoint.md).

    ```powershell
    Add-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -AzureDirectoryTenantName $YourAzureDirectoryTenantName  -AzureSubscriptionId $YourAzureSubscriptionId -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Development 
    ```
5. Nella finestra popup di accesso, immettere le credenziali di sottoscrizione di Azure.

## <a name="verify-the-registration"></a>Verificare la registrazione

1. Accedere al portale di amministrazione (https://adminportal.local.azurestack.external).
2. Fare clic su **più servizi** > **gestione Marketplace** > **aggiungere da Azure**.
3. Se viene visualizzato un elenco di prodotti disponibili in Azure (ad esempio WordPress), l'attivazione è riuscita.

> [!NOTE]
> Una volta completata la registrazione, l'avviso attivo per la registrazione non verrà più visualizzato.

## <a name="next-steps"></a>Passaggi successivi

[Connettersi ad Azure Stack](azure-stack-connect-azure-stack.md)

