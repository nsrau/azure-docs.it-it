---
title: Configurare le impostazioni di gruppo usando PowerShell in Azure Active Directory | Microsoft Docs
description: Come gestire le impostazioni dei gruppi con i cmdlet di Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 10/12/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 1e8f5728697e63737ec44fedd8ed336366241f66
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310743"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo
In questo articolo sono contenute le istruzioni per l'uso di cmdlet PowerShell in Azure Active Directory (Azure AD) per creare e aggiornare i gruppi. Questo contenuto si applica solo ai gruppi di Office 365, noti anche come gruppi unificati. 

> [!IMPORTANT]
> Alcune impostazioni richiedono una licenza per Azure Active Directory Premium P1. Per altre informazioni, vedere la tabella relativa alle [impostazioni modello](#template-settings).

Per altre informazioni su come evitare che utenti non amministratori creino gruppi di sicurezza, configurare il valore `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` come illustrato in [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0). 

Le impostazioni di Gruppi di Office 365 vengono configurare con un oggetto Settings e un oggetto SettingsTemplate. Non vengono inizialmente visualizzati oggetti Settings nella directory, perché la directory è configurata con le impostazioni personalizzate. Per modificarle, è necessario creare un nuovo oggetto Settings usando un modello di impostazioni. I modelli di impostazioni sono definiti da Microsoft. Sono disponibili diversi modelli di impostazioni. Per configurare le impostazioni di gruppo di Office 365 per la directory, usare il modello denominato "Group.Unified". Per configurare le impostazioni di gruppo di Office 365 per un singolo gruppo, usare il modello denominato "Group.Unified.Guest". Questo modello viene usato per gestire l'accesso guest a un gruppo di Office 365. 

I cmdlet fanno parte del modulo Azure Active Directory PowerShell V2. Per istruzioni sul download e sull'installazione del modulo nel computer, vedere l'articolo [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/) (Azure Active Directory PowerShell versione 2). È possibile installare la versione 2 del modulo da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="retrieve-a-specific-settings-value"></a>Recuperare un valore di impostazione specifico
Se si conosce il nome dell'impostazione da recuperare, è possibile usare il cmdlet seguente per recuperare il valore corrente dell'impostazione. In questo esempio viene recuperato il valore per un'impostazione denominata "UsageGuidelinesUrl". Più avanti in questo capitolo sono disponibili altre informazioni sulle impostazioni di directory e i rispettivi nomi.

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>Creare le impostazioni a livello di directory
I passaggi seguenti permettono di creare le impostazioni a livello di directory, che si applicheranno a tutti i gruppi di Office 365 presenti nella directory stessa. Il cmdlet Get-AzureADDirectorySettingTemplate è disponibile solo nel [modulo di anteprima di Azure AD PowerShell per Graph](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

1. Nei cmdlet DirectorySettings è necessario specificare l'ID del SettingsTemplate che si vuole usare. Se non si conosce l'ID, questo cmdlet restituisce l'elenco di tutti i modelli di impostazioni:
  
  ```powershell
  PS C:> Get-AzureADDirectorySettingTemplate
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
  $setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
  ```  
5. Infine, applicare le impostazioni:
  
  ```powershell
  New-AzureADDirectorySetting -DirectorySetting $setting
  ```

Al termine, il cmdlet restituisce l'ID del nuovo oggetto Settings:

  ```powershell
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

## <a name="template-settings"></a>Impostazioni modello
Di seguito sono riportate le impostazioni definite in SettingsTemplate di Group.Unified. Se non diversamente indicato, queste funzionalità richiedono una licenza per Azure Active Directory Premium P1. 

| **Impostazione** | **Descrizione** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Tipo: Boolean<li>Valore predefinito: True |Flag che indica se utenti non amministratori sono autorizzati a creare gruppi di Office 365 nella directory. Questa impostazione non richiede una licenza per Azure Active Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Tipo: String<li>Predefinito: "" |GUID del gruppo di sicurezza i cui membri sono autorizzati a creare gruppi di Office 365 anche quando EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Tipo: String<li>Predefinito: "" |Collegamento alle linee guida sull'utilizzo dei gruppi. |
|  <ul><li>ClassificationDescriptions<li>Tipo: String<li>Predefinito: "" | Elenco delimitato da virgole di descrizioni di classificazione. Il valore di ClassificationDescriptions è valido solo nel formato seguente:
  $setting[“ClassificationDescriptions”] ="Classification:Description,Classification:Description", in cui Classification corrisponde alle stringhe di ClassificationList.|
|  <ul><li>DefaultClassification<li>Tipo: String<li>Predefinito: "" | Classificazione da usare come classificazione predefinita per un gruppo, se non specificata.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Tipo: String<li>Predefinito: "" | Stringa di lunghezza massima pari a 64 caratteri che definisce la convenzione di denominazione configurata per i gruppi di Office 365. Per altre informazioni, vedere [Enforce a naming policy for Office 365 groups](groups-naming-policy.md) (Applicare criteri di denominazione per i gruppi di Office 365). |
| <ul><li>CustomBlockedWordsList<li>Tipo: String<li>Predefinito: "" | Stringa di frasi delimitate da virgole che gli utenti non potranno usare in nomi o alias di gruppo. Per altre informazioni, vedere [Enforce a naming policy for Office 365 groups](groups-naming-policy.md) (Applicare criteri di denominazione per i gruppi di Office 365). |
| <ul><li>EnableMSStandardBlockedWords<li>Tipo: Boolean<li>Valore predefinito: "False". | Non usare
|  <ul><li>AllowGuestsToBeGroupOwner<li>Tipo: Boolean<li>Valore predefinito: False | Valore booleano che indica se un utente guest può essere o meno un proprietario di gruppi. |
|  <ul><li>AllowGuestsToAccessGroups<li>Tipo: Boolean<li>Valore predefinito: True | Valore booleano che indica se un utente guest ha o meno accesso al contenuto dei gruppi di Office 365.  Questa impostazione non richiede una licenza per Azure Active Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Tipo: String<li>Predefinito: "" | URL di un collegamento alle linee guida per l'utilizzo dei gruppi. |
|  <ul><li>AllowToAddGuests<li>Tipo: Boolean<li>Valore predefinito: True | Valore booleano che indica se è consentito o meno aggiungere utenti guest a questa directory.|
|  <ul><li>ClassificationList<li>Tipo: String<li>Predefinito: "" |Elenco delimitato da virgole dei valori di classificazione validi che è possibile applicare ai gruppi di Office 365. |

## <a name="read-settings-at-the-directory-level"></a>Leggere le impostazioni a livello di directory
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

3. Leggere tutti i valori delle impostazioni di directory di un oggetto Settings della directory specifico, usando il GUID delle impostazioni:
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
  AllowToAddGuests              True
  UsageGuidelinesUrl            https://guideline.example.com
  ClassificationList
  EnableGroupCreation           True
  ```

## <a name="update-settings-for-a-specific-group"></a>Aggiornare le impostazioni per un gruppo specifico

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
  $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
  ```
3. Creare un nuovo oggetto Settings dal modello:
  ```powershell
  $Setting = $Template.CreateDirectorySetting()
  ```

4. Impostare il valore richiesto:
  ```powershell
  $Setting["AllowToAddGuests"]=$False
  ```
5. Creare la nuova impostazione per il gruppo richiesto nella directory:
  ```powershell
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>Aggiornare le impostazioni a livello di directory

Quelli che seguono sono i passaggi necessari per aggiornare le impostazioni a livello di directory, che si applicano a tutti i gruppi di Office 365 presenti nella directory. Questi esempi presuppongono che nella directory esista già un oggetto Settings.

1. Trovare l'oggetto Settings esistente:
  ```powershell
  $setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ```
2. Aggiornare il valore:
  
  ```powershell
  $Setting["AllowToAddGuests"] = "false"
  ```
3. Aggiornare l'impostazione:
  
  ```powershell
  Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
  ```

## <a name="remove-settings-at-the-directory-level"></a>Rimuovere le impostazioni a livello di directory
Questo passaggio consente di rimuovere le impostazioni a livello di directory, che si applicano a tutti i gruppi di Office presenti nella directory.
  ```powershell
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>Riferimento alla sintassi cmdlet
Per altre informazioni su Azure Active Directory PowerShell, consultare la documentazione sui [cmdlet di Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Informazioni aggiuntive

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Integrazione delle identità locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
