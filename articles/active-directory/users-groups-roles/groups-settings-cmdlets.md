---
title: Configurare le impostazioni di gruppo usando PowerShell - Azure Active Directory | Microsoft Docs
description: Come gestire le impostazioni dei gruppi con i cmdlet di Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 09/10/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18fbaad8ce15ab4eb9a08d5edc273098e7fb372e
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263956"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo
In questo articolo sono contenute le istruzioni per l'uso di cmdlet PowerShell in Azure Active Directory (Azure AD) per creare e aggiornare i gruppi. Questo contenuto si applica solo ai gruppi di Office 365, noti anche come gruppi unificati. 

> [!IMPORTANT]
> Alcune impostazioni richiedono una licenza per Azure Active Directory Premium P1. Per altre informazioni, vedere la tabella relativa alle [impostazioni modello](#template-settings).

Per altre informazioni su come impedire a utenti non amministratori di creare gruppi di sicurezza, impostare  `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` come descritto in [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

Le impostazioni di Gruppi di Office 365 vengono configurare con un oggetto Settings e un oggetto SettingsTemplate. Non vengono inizialmente visualizzati oggetti Settings nella directory, perché la directory è configurata con le impostazioni personalizzate. Per modificarle, è necessario creare un nuovo oggetto Settings usando un modello di impostazioni. I modelli di impostazioni sono definiti da Microsoft. Sono disponibili diversi modelli di impostazioni. Per configurare le impostazioni di gruppo di Office 365 per la directory, usare il modello denominato "Group.Unified". Per configurare le impostazioni di gruppo di Office 365 per un singolo gruppo, usare il modello denominato "Group.Unified.Guest". Questo modello viene usato per gestire l'accesso guest a un gruppo di Office 365. 

I cmdlet fanno parte del modulo Azure Active Directory PowerShell V2. Per istruzioni sul download e sull'installazione del modulo nel computer, vedere l'articolo [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/) (Azure Active Directory PowerShell versione 2). È possibile installare la versione 2 del modulo da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-powershell-cmdlets"></a>Installare i cmdlet di PowerShell

Assicurarsi di disinstallare qualsiasi versione precedente di Azure Active Directory PowerShell per Graph Module per Windows PowerShell e installare [Azure Active Directory PowerShell Graph, versione di anteprima pubblica 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137), prima di eseguire i comandi di PowerShell.

1. Aprire l'app Windows PowerShell come amministratore.
2. Disinstallare qualsiasi versione precedente di AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   Uninstall-Module azuread
   ```

3. Installare la versione più recente di AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```
   
## <a name="create-settings-at-the-directory-level"></a>Creare le impostazioni a livello di directory
I passaggi seguenti permettono di creare le impostazioni a livello di directory, che si applicheranno a tutti i gruppi di Office 365 presenti nella directory stessa. Il cmdlet Get-AzureADDirectorySettingTemplate è disponibile solo nel [modulo di anteprima di Azure AD PowerShell per Graph](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

1. Nei cmdlet DirectorySettings è necessario specificare l'ID del SettingsTemplate che si vuole usare. Se non si conosce l'ID, questo cmdlet restituisce l'elenco di tutti i modelli di impostazioni:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   Questa chiamata del cmdlet restituirà tutti i modelli disponibili:
  
   ```powershell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Per aggiungere un URL alle linee guida sull'utilizzo, è necessario innanzitutto ottenere l'oggetto SettingsTemplate che definisce il valore di URL delle linee guida sull'utilizzo, vale a dire il modello Group.Unified:
  
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Successivamente, creare un nuovo oggetto impostazioni sulla base del modello:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Aggiornare quindi il valore delle linee guida sull'utilizzo:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Applicare quindi l'impostazione:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. È possibile leggere i valori usando:

   ```powershell
   $Setting.Values
   ```  
## <a name="update-settings-at-the-directory-level"></a>Aggiornare le impostazioni a livello di directory
Per aggiornare il valore di UsageGuideLinesUrl nel modello di impostazione, è sufficiente modificare l'URL con il passaggio 4 precedente, quindi eseguire il passaggio 5 per impostare il nuovo valore.

Per rimuovere il valore di UsageGuideLinesUrl, modificare l'URL in modo che sia una stringa vuota usando il passaggio 4 precedente:

   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
Eseguire quindi il passaggio 5 per impostare il nuovo valore.

## <a name="template-settings"></a>Impostazioni modello
Di seguito sono riportate le impostazioni definite in SettingsTemplate di Group.Unified. Se non diversamente indicato, queste funzionalità richiedono una licenza per Azure Active Directory Premium P1. 

| **Impostazione** | **Descrizione** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Digitare:  Boolean<li>Valore predefinito: True |Flag che indica se utenti non amministratori sono autorizzati a creare gruppi di Office 365 nella directory. Questa impostazione non richiede una licenza per Azure Active Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Digitare:  string<li>Predefinito: "" |GUID del gruppo di sicurezza i cui membri sono autorizzati a creare gruppi di Office 365 anche quando EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Digitare:  string<li>Predefinito: "" |Collegamento alle linee guida sull'utilizzo dei gruppi. |
|  <ul><li>ClassificationDescriptions<li>Digitare:  string<li>Predefinito: "" | Elenco delimitato da virgole di descrizioni di classificazione. Il valore di ClassificationDescriptions è valido solo nel formato seguente:<br>$setting ["ClassificationDescriptions"] = "classificazione: descrizione, classificazione: Descrizione"<br>dove la classificazione corrisponde alle stringhe nell'oggetto Classification.|
|  <ul><li>DefaultClassification<li>Digitare:  string<li>Predefinito: "" | Classificazione da usare come classificazione predefinita per un gruppo, se non specificata.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Digitare:  string<li>Predefinito: "" | Stringa di lunghezza massima pari a 64 caratteri che definisce la convenzione di denominazione configurata per i gruppi di Office 365. Per altre informazioni, vedere [Enforce a naming policy for Office 365 groups](groups-naming-policy.md) (Applicare criteri di denominazione per i gruppi di Office 365). |
| <ul><li>CustomBlockedWordsList<li>Digitare:  string<li>Predefinito: "" | Stringa di frasi delimitate da virgole che gli utenti non potranno usare in nomi o alias di gruppo. Per altre informazioni, vedere [Enforce a naming policy for Office 365 groups](groups-naming-policy.md) (Applicare criteri di denominazione per i gruppi di Office 365). |
| <ul><li>EnableMSStandardBlockedWords<li>Digitare:  Boolean<li>Valore predefinito: "False" | Non usare
|  <ul><li>AllowGuestsToBeGroupOwner<li>Digitare:  Boolean<li>Valore predefinito: False | Valore booleano che indica se un utente guest può essere o meno un proprietario di gruppi. |
|  <ul><li>AllowGuestsToAccessGroups<li>Digitare:  Boolean<li>Valore predefinito: True | Valore booleano che indica se un utente guest ha o meno accesso al contenuto dei gruppi di Office 365.  Questa impostazione non richiede una licenza per Azure Active Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Digitare:  string<li>Predefinito: "" | URL di un collegamento alle linee guida per l'utilizzo dei gruppi. |
|  <ul><li>AllowAddGuests<li>Digitare:  Boolean<li>Valore predefinito: True | Valore booleano che indica se è consentito o meno aggiungere utenti guest a questa directory.|
|  <ul><li>ClassificationList<li>Digitare:  string<li>Predefinito: "" |Elenco delimitato da virgole dei valori di classificazione validi che è possibile applicare ai gruppi di Office 365. |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Esempio: Configurare i criteri Guest per i gruppi a livello di directory
1. Ottenere tutti i modelli di impostazione:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Per impostare i criteri Guest per i gruppi a livello di directory, è necessario il modello Group. Unified
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Successivamente, creare un nuovo oggetto impostazioni sulla base del modello:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Aggiornare l'impostazione di AllowAddGuests
   ```powershell
   $Setting["AllowAddGuests"] = $False
   ```  
5. Applicare quindi l'impostazione:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. È possibile leggere i valori usando:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Leggere le impostazioni a livello di directory

Se si conosce il nome dell'impostazione da recuperare, è possibile usare il cmdlet seguente per recuperare il valore corrente dell'impostazione. In questo esempio viene recuperato il valore per un'impostazione denominata "UsageGuidelinesUrl". 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
Quelli che seguono sono i passaggi necessari per leggere le impostazioni a livello di directory, che si applicano a tutti i gruppi di Office presenti nella directory.

1. Leggere tutte le impostazioni della directory esistenti:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Questo cmdlet restituisce un elenco di tutte le impostazioni della directory:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Leggere tutte le impostazioni di un determinato gruppo:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Leggere tutti i valori delle impostazioni di directory di un oggetto impostazioni directory specifico, usando il GUID ID impostazioni:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   Questo cmdlet restituisce i nomi e valori in questo oggetto Settings per il gruppo specifico:
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>Rimuovere le impostazioni a livello di directory
Questo passaggio consente di rimuovere le impostazioni a livello di directory, che si applicano a tutti i gruppi di Office presenti nella directory.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Creare le impostazioni per un gruppo specifico

1. Cercare il modello di impostazioni denominato "Groups.Unified.Guest"
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Recuperare l'oggetto modello per il modello Groups.Unified.Guest:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
   ```
3. Creare un nuovo oggetto Settings dal modello:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Impostare il valore richiesto:
   ```powershell
   $SettingCopy["AllowAddGuests"]=$False
   ```
5. Ottenere l'ID del gruppo a cui si vuole applicare questa impostazione:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Creare la nuova impostazione per il gruppo richiesto nella directory:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. Per verificare le impostazioni, eseguire questo comando:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Aggiornare le impostazioni per un gruppo specifico
1. Ottenere l'ID del gruppo di cui si desidera aggiornare l'impostazione:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. Recuperare l'impostazione del gruppo:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Aggiornare l'impostazione del gruppo in modo che sia necessario, ad esempio
   ```powershell
   $Setting["AllowAddGuests"] = $True
   ```
4. Ottenere quindi l'ID dell'impostazione per il gruppo specifico:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   Si otterrà una risposta simile alla seguente:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. È quindi possibile impostare il nuovo valore per questa impostazione:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. È possibile leggere il valore dell'impostazione per assicurarsi che sia stato aggiornato correttamente:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Riferimento alla sintassi cmdlet
Per altre informazioni su Azure Active Directory PowerShell, consultare la documentazione sui [cmdlet di Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Informazioni aggiuntive

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Integrazione delle identità locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
