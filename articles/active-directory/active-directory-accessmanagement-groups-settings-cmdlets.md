---
title: Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo | Documentazione Microsoft
description: Come gestire le impostazioni per i gruppi con i cmdlet di Azure Active Directory.
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
ms.date: 02/10/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: c18ef38661e31e16114b88bdfad36320776ef12b
ms.lasthandoff: 12/28/2016


---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo
Nella directory è possibile configurare le seguenti impostazioni per i gruppi unificati:

1. Classificazioni: l'elenco separato da virgole delle classificazioni che gli utenti possono impostare su un gruppo, ad esempio "Contatto", "Segreto" e "Top secret".
2. URL sulle linee guida per l'utilizzo: un URL che dirige gli utenti alle condizioni di utilizzo dei gruppi unificati definite dall'organizzazione. Questo URL verrà visualizzato nell'interfaccia utente in cui gli utenti utilizzano i gruppi.
3. Creazione gruppo abilitata: con questa impostazione è possibile consentire a nessuno, alcuni o tutti gli utenti di creare gruppi unificati. Se impostata su on, tutti gli utenti possono creare gruppi. Se impostata su off, nessun utente può creare gruppi. In questo caso, sarà inoltre possibile specificare un gruppo di sicurezza, i cui utenti potranno comunque creare gruppi.

Queste impostazioni vengono configurate utilizzando gli oggetti Settings e SettingsTemplate. Inizialmente, nella directory non sarà presente alcun oggetto Settings. Ciò significa che la directory è configurata con le impostazioni predefinite. Per modificarle, sarà necessario creare un nuovo oggetto Settings utilizzando un modello di impostazioni. I modelli di impostazioni sono definiti da Microsoft.

Il modulo contenente i cmdlet utilizzati per queste operazioni può essere scaricato dal [sito Microsoft Connect](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).

## <a name="create-settings-at-the-directory-level"></a>Creare le impostazioni a livello di directory
I seguenti passaggi permettono di creare le impostazioni a livello di directory, che si applicheranno a tutti i gruppi di Office presenti nella directory stessa.

1. Se non si conosce il modello di impostazioni da utilizzare, questo cmdlet restituisce l'elenco dei modelli di impostazioni:

    `Get-MsolAllSettingTemplate`

    ![Elenco dei modelli di impostazioni](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)
2. Per aggiungere un URL alle linee guida sull'utilizzo, è necessario innanzitutto ottenere l'oggetto SettingsTemplate che definisce il valore di URL delle linee guida sull'utilizzo, vale a dire il modello Group.Unified:

    `$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`
3. Successivamente, creare un nuovo oggetto impostazioni sulla base del modello:

    `$setting = $template.CreateSettingsObject()`
4. Aggiornare quindi il valore delle linee guida sull'utilizzo:

    `$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`
5. Infine, applicare le impostazioni:

    `New-MsolSettings –SettingsObject $setting`

    ![Aggiungere l'URL delle linee guida sull'utilizzo](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

Di seguito sono riportate le impostazioni definite in SettingsTemplate di Group.Unified.

| **Impostazione** | **Descrizione** |
| --- | --- |
|  <ul><li>ClassificationList<li>Tipo: String<li>Predefinito: "" |Un elenco delimitato da virgole dei valori di classificazione validi che è possibile applicare ai gruppi unificati. |
|  <ul><li>EnableGroupCreation<li>Tipo: Boolean<li> Valore predefinito: True |La bandierina che indica se sia consentito creare il gruppo unificato nella directory. |
|  <ul><li>GroupCreationAllowedGroupId<li>Tipo: String<li>Predefinito: "" |GUID del gruppo di sicurezza autorizzato a creare gruppi unificati anche quando EnableGroupCreation = = false. |
|  <ul><li>UsageGuidelinesUrl<li>Tipo: String<li>Predefinito: "" |Collegamento alle linee guida sull'utilizzo dei gruppi. |

## <a name="read-settings-at-the-directory-level"></a>Leggere le impostazioni a livello di directory
Quelli che seguono sono i passaggi necessari per leggere le impostazioni a livello di directory, che si applicano a tutti i gruppi di Office presenti nella directory.

1. Leggere tutte le impostazioni della directory esistenti:

    `Get-MsolAllSettings`
2. Leggere tutte le impostazioni di un determinato gruppo:

    `Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`
3. Leggere impostazioni della directory specifiche utilizzando il GUID SettingId:

    `Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

    ![GUID dell'ID delle impostazioni](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## <a name="update-settings-at-the-directory-level"></a>Aggiornare le impostazioni a livello di directory
Quelli che seguono sono i passaggi necessari per aggiornare le impostazioni a livello di directory, che si applicano a tutti i gruppi di Office presenti nella directory.

1. Ottenere l'oggetto relativo alle impostazioni esistenti:

    `$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`
2. Ottenere il valore che si desidera da aggiornare:

    `$value = $Setting.GetSettingsValue()`
3. Aggiornare il valore:

    `$value["AllowToAddGuests"] = "false"`
4. Aggiornare l'impostazione:

    `Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## <a name="remove-settings-at-the-directory-level"></a>Rimuovere le impostazioni a livello di directory
Questo passaggio consente di rimuovere le impostazioni a livello di directory, che si applicano a tutti i gruppi di Office presenti nella directory.

    `Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## <a name="cmdlet-syntax-reference"></a>Riferimento alla sintassi cmdlet
Per ulteriore documentazione sulla PowerShell di Azure Active Directory, consultare [Cmdlet di Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

## <a name="settingstemplate-object-reference-groupunified-settingstemplate-object"></a>Riferimento all'oggetto SettingsTemplate (oggetto Group.Unified SettingsTemplate)
* "name": "EnableGroupCreation", "type": "System.Boolean", "defaultValue": "true", "description": "Un flag booleano che indica se è attivata la funzionalità per la creazione di gruppi unificati."
* "name": "GroupCreationAllowedGroupId", "type": "System.Guid", "defaultValue": "", "description": "GUID del gruppo di sicurezza incluso nella whitelist per la creazione di gruppi unificati."
* "name": "ClassificationList", "type": "System.String", "defaultValue": "", "description": "Elenco delimitato da virgole dei valori di classificazione validi applicabili ai gruppi unificati."
* "name": "UsageGuidelinesUrl", "type": "System.String", "defaultValue": "", "description": "Collegamento alle linee guida sull'utilizzo dei gruppi."

| name | type | defaultValue | Descrizione |
| --- | --- | --- | --- |
| "EnableGroupCreation" |"Boolean" |"true" |"Flag booleano che indica se è attivata la funzionalità per la creazione di gruppi unificati." |
| "GroupCreationAllowedGroupId" |"GUID" |"" |"GUID del gruppo di sicurezza incluso nella whitelist per creare gruppi unificati." |
| "ClassificationList" |"System. String" |"" |"Elenco delimitato da virgole dei valori di classificazione validi applicabili ai gruppi unificati." |
| "UsageGuidelinesUrl" |"System. String" |"" |"Collegamento alle linee guida sull'utilizzo dei gruppi." |

## <a name="next-steps"></a>Passaggi successivi
Per ulteriore documentazione sulla PowerShell di Azure Active Directory, consultare [Cmdlet di Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

Ulteriori istruzioni del responsabile del programma Microsoft Rob de Jong sono disponibili sul [Blog sui gruppi di Rob](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad).

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

