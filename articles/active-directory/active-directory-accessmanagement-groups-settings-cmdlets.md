---
title: Configurare le impostazioni di gruppo usando i cmdlet di Azure Active Directory | Microsoft Docs
description: Come gestire le impostazioni dei gruppi con i cmdlet di Azure Active Directory
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 9f2090e6-3af4-4f07-bbb2-1d18dae89b73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: rodejo
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 7dd4833000e8550b172e0451c48788b1e627a1fb
ms.contentlocale: it-it
ms.lasthandoff: 07/28/2017

---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo

> [!IMPORTANT]
> Questo contenuto si applica solo ai gruppi di Office 365. 

Le impostazioni di Gruppi di Office 365 vengono configurare con un oggetto Settings e un oggetto SettingsTemplate. Inizialmente, nella directory non sarà presente alcun oggetto Settings. Ciò significa che la directory è configurata con le impostazioni predefinite. Per modificarle, è necessario creare un nuovo oggetto Settings usando un modello di impostazioni. I modelli di impostazioni sono definiti da Microsoft. Sono disponibili diversi modelli di impostazioni. Per configurare le impostazioni di gruppo per la directory, si userà il modello denominato "Group.Unified". Per configurare le impostazioni di gruppo per un singolo gruppo, usare il modello denominato "Group.Unified.Guest". Questo modello viene usato per gestire l'accesso guest a un gruppo. 

I cmdlet fanno parte del modulo Azure Active Directory PowerShell V2. Per altre informazioni su questo modulo e per istruzioni su come scaricare e installare il modulo nel computer in uso, vedere [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/) (PowerShell di Azure Active Directory versione 2). È possibile installare la release della versione 2 del modulo da [qui](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="retrieve-a-specific-settings-value"></a>Recuperare un valore di impostazione specifico
Se si conosce il nome dell'impostazione da recuperare, è possibile usare il cmdlet seguente per recuperare il valore corrente dell'impostazione. In questo esempio viene recuperato il valore per un'impostazione denominata "UsageGuidelinesUrl". Più avanti in questo capitolo sono disponibili altre informazioni sulle impostazioni di directory e i rispettivi nomi.

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>Creare le impostazioni a livello di directory
I passaggi seguenti consentono di creare le impostazioni a livello di directory, applicabili a tutti i gruppi unificati presenti nella directory stessa.

1. Nei cmdlet DirectorySettings sarà necessario specificare l'ID del SettingsTemplate che si vuole usare. Se non si conosce l'ID, questo cmdlet restituisce l'elenco di tutti i modelli di impostazioni:
  
  ```
  PS C:> Get-AzureADDirectorySettingTemplate
  ```
  Questa chiamata del cmdlet restituirà tutti i modelli disponibili:
  
  ```
  Id                                   DisplayName         Description
  --                                   -----------         -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Unified Group
  16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
  ```
2. Per aggiungere un URL alle linee guida sull'utilizzo, è necessario innanzitutto ottenere l'oggetto SettingsTemplate che definisce il valore di URL delle linee guida sull'utilizzo, vale a dire il modello Group.Unified:
  
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
  ```
3. Successivamente, creare un nuovo oggetto impostazioni sulla base del modello:
  
  ```
  $Setting = $template.CreateDirectorySetting()
  ```  
4. Aggiornare quindi il valore delle linee guida sull'utilizzo:
  
  ```
  $setting["UsageGuidelinesUrl"] = "<https://guideline.com>"
  ```  
5. Infine, applicare le impostazioni:
  
  ```
  New-AzureADDirectorySetting -DirectorySetting $setting
  ```

Al termine, il cmdlet restituisce l'ID del nuovo oggetto Settings:
  ```
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```
Di seguito sono riportate le impostazioni definite in SettingsTemplate di Group.Unified.

| **Impostazione** | **Descrizione** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Tipo: Boolean<li>Valore predefinito: True |La bandierina che indica se sia consentito creare il gruppo unificato nella directory. |
|  <ul><li>GroupCreationAllowedGroupId<li>Tipo: String<li>Predefinito: "" |GUID del gruppo di sicurezza i cui membri sono autorizzati a creare gruppi unificati anche quando EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Tipo: String<li>Predefinito: "" |Collegamento alle linee guida sull'utilizzo dei gruppi. |
|  <ul><li>ClassificationDescriptions<li>Tipo: String<li>Predefinito: "" | Elenco delimitato da virgole di descrizioni di classificazione. |
|  <ul><li>DefaultClassification<li>Tipo: String<li>Predefinito: "" | Classificazione da usare come classificazione predefinita per un gruppo, se non specificata.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Tipo: String<li>Predefinito: "" |Non ancora implementato
|  <ul><li>AllowGuestsToBeGroupOwner<li>Tipo: booleano<li>Valore predefinito: False | Valore booleano che indica se un utente guest può essere o meno un proprietario di gruppi. |
|  <ul><li>AllowGuestsToAccessGroups<li>Tipo: booleano<li>Valore predefinito: True | Valore booleano che indica se un utente guest ha o meno accesso al contenuto dei gruppi unificati. |
|  <ul><li>GuestUsageGuidelinesUrl<li>Tipo: String<li>Predefinito: "" | URL di un collegamento alle linee guida per l'utilizzo dei gruppi. |
|  <ul><li>AllowToAddGuests<li>Tipo: booleano<li>Valore predefinito: True | Valore booleano che indica se è consentito o meno aggiungere utenti guest a questa directory.|
|  <ul><li>ClassificationList<li>Tipo: String<li>Predefinito: "" |Un elenco delimitato da virgole dei valori di classificazione validi che è possibile applicare ai gruppi unificati. |
|  <ul><li>EnableGroupCreation<li>Tipo: Boolean<li>Valore predefinito: True | Valore booleano che indica se gli utenti non amministratori possono creare nuovi gruppi unificati. |


## <a name="read-settings-at-the-directory-level"></a>Leggere le impostazioni a livello di directory
Quelli che seguono sono i passaggi necessari per leggere le impostazioni a livello di directory, che si applicano a tutti i gruppi di Office presenti nella directory.

1. Leggere tutte le impostazioni della directory esistenti:
  ```
  Get-AzureADDirectorySetting -All $True
  ```
  Questo cmdlet restituisce un elenco di tutte le impostazioni della directory:
  ```
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

2. Leggere tutte le impostazioni di un determinato gruppo:
  ```
  Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
  ```

3. Leggere tutti i valori delle impostazioni di directory di un oggetto Settings della directory specifico, usando il GUID delle impostazioni:
  ```
  (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
  ```
  Questo cmdlet restituisce i nomi e valori in questo oggetto Settings per il gruppo specifico:
  ```
  Name                          Value
  ----                          -----
  ClassificationDescriptions
  DefaultClassification
  PrefixSuffixNamingRequirement
  AllowGuestsToBeGroupOwner     False 
  AllowGuestsToAccessGroups     True
  GuestUsageGuidelinesUrl
  GroupCreationAllowedGroupId
  AllowToAddGuests              True
  UsageGuidelinesUrl            <https://guideline.com>
  ClassificationList
  EnableGroupCreation           True
  ```

## <a name="update-settings-for-a-specific-group"></a>Aggiornare le impostazioni per un gruppo specifico

1. Cercare il modello di impostazioni denominato "Groups.Unified.Guest"
  ```
  Get-AzureADDirectorySettingTemplate
  
  Id                                   DisplayName            Description
  --                                   -----------            -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Unified Group
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
  ```
2. Recuperare l'oggetto modello per il modello Groups.Unified.Guest:
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
  ```
3. Creare un nuovo oggetto Settings dal modello:
  ```
  $Setting = $Template.CreateDirectorySetting()
  ```

4. Impostare il valore richiesto:
  ```
  $Setting["AllowToAddGuests"]=$False
  ```
5. Creare la nuova impostazione per il gruppo richiesto nella directory:
  ```
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>Aggiornare le impostazioni a livello di directory

Questi passaggi consentono di aggiornare le impostazioni a livello di directory, applicabili a tutti i gruppi unificati nella directory. Questi esempi presuppongono che nella directory esista già un oggetto Settings.

1. Trovare l'oggetto Settings esistente:
  ```
  Get-AzureADDirectorySetting | Where-object -Property Displayname -Value "Group.Unified" -EQ
  
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  
  $setting = Get-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323
  ```
2. Aggiornare il valore:
  
  ```
  $Setting["AllowToAddGuests"] = "false"
  ```
3. Aggiornare l'impostazione:
  
  ```
  Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
  ```

## <a name="remove-settings-at-the-directory-level"></a>Rimuovere le impostazioni a livello di directory
Questo passaggio consente di rimuovere le impostazioni a livello di directory, che si applicano a tutti i gruppi di Office presenti nella directory.
  ```
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>Riferimento alla sintassi cmdlet
Per altre informazioni su Azure Active Directory PowerShell, consultare la documentazione sui [cmdlet di Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Informazioni aggiuntive

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

