---
title: Aggiungere criteri di denominazione per i gruppi di Office 365 in Azure Active Directory | Microsoft Docs
description: Viene illustrato come aggiungere nuovi utenti o eliminare utenti esistenti in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/08/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 8ebdb22ba5ca04a5c811b3b368055f5f4371c75f
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2018
ms.locfileid: "40208904"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Guida introduttiva: Criteri di denominazione per i gruppi in Azure Active Directory

In questa guida introduttiva si configurano criteri di denominazione nel tenant di Azure Active Directory (Azure AD) per i gruppi di Office 365 creati dall'utente, con lo scopo di ordinare i gruppi del tenant ed eseguire ricerche al loro interno. È possibile, ad esempio, usare i criteri di denominazione per:

* Comunicare la funzione, l'appartenenza, l'area geografica o il creatore di un gruppo.
* Agevolare la classificazione dei gruppi nella Rubrica.
* Impedire l'uso di parole specifiche nei nomi dei gruppi e negli alias.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="install-powershell-cmdlets"></a>Installare i cmdlet di PowerShell

Assicurarsi di disinstallare qualsiasi versione precedente di Azure Active Directory PowerShell per Graph Module per Windows PowerShell e installare [Azure Active Directory PowerShell Graph, versione di anteprima pubblica 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137), prima di eseguire i comandi di PowerShell. 

1. Aprire l'app Windows PowerShell come amministratore.
2. Disinstallare qualsiasi versione precedente di AzureADPreview.
  
  ````
  Uninstall-Module AzureADPreview
  ````
3. Installare la versione più recente di AzureADPreview.
  
  ````
  Install-Module AzureADPreview
  ````
Se viene chiesto di accedere a un repository non attendibile, digitare **S**. L'installazione del nuovo modulo potrebbe richiedere alcuni minuti.

## <a name="set-up-naming-policy"></a>Configurare i criteri di denominazione

### <a name="step-1-sign-in-using-powershell-cmdlets"></a>Passaggio 1: Eseguire l'accesso usando i cmdlet di PowerShell

1. Aprire l'app Windows PowerShell. Non sono necessari privilegi elevati.

2. Eseguire i comandi seguenti per preparare l'esecuzione dei cmdlet.
  
  ````
  Import-Module AzureADPreview
  Connect-AzureAD
  ````
  Nella schermata **Accedi all'account** che viene aperta, immettere account e password amministratore per connettersi al servizio e selezionare **Accedi**.

3. Seguire i passaggi in [Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo](groups-settings-cmdlets.md) per creare impostazioni di gruppo per questo tenant.

### <a name="step-2-view-the-current-settings"></a>Passaggio 2: Visualizzare le impostazioni correnti

1. Visualizzare le impostazioni dei criteri di denominazione correnti.
  
  ````
  $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ````
  
2. Visualizzare le impostazioni del gruppo corrente.
  
  ````
  $Setting.Values
  ````
  
### <a name="step-3-set-the-naming-policy-and-any-custom-blocked-words"></a>Passaggio 3: Impostare i criteri di denominazione ed eventuali parole bloccate personalizzate

1. Impostare prefissi e suffissi dei nomi di gruppo in Azure AD PowerShell.
  
  ````
  $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
  ````
  
2. Impostare le parole bloccate personalizzate da limitare. Nell'esempio seguente viene illustrato come aggiungere le parole personalizzate.
  
  ````
  $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
  ````
  
3. Salvare le impostazioni per applicare i nuovi criteri, come nell'esempio seguente.
  
  ````
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ````
  
È tutto. Sono stati impostati criteri di denominazione e sono state aggiunte le parole bloccate personalizzate.

## <a name="clean-up-resources"></a>Pulire le risorse

1. Impostare prefissi e suffissi dei nomi di gruppo in Azure AD PowerShell.
  
  ````
  $Setting["PrefixSuffixNamingRequirement"] =""
  ````
  
2. Impostare le parole bloccate personalizzate da limitare. Nell'esempio seguente viene illustrato come aggiungere le parole personalizzate.
  
  ````
  $Setting["CustomBlockedWordsList"]=""
  ````
  
3. Salvare le impostazioni per applicare i nuovi criteri, come nell'esempio seguente.
  
  ````
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ````

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come usare i cmdlet di PowerShell per impostare i criteri di denominazione per il tenant di Azure AD.

Per altre informazioni su vincoli tecnici, aggiunta di un elenco di parole bloccate personalizzate o esperienze degli utenti finali nelle app di Office 365, passare all'articolo seguente che contiene i dettagli relativi ai criteri di denominazione.
> [!div class="nextstepaction"]
> [Dettagli sui criteri di denominazione](groups-naming-policy.md)