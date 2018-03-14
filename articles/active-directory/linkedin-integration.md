---
title: Abilitare o disabilitare l'integrazione di LinkedIn per le applicazioni di Office in Azure Active Directory | Microsoft Docs
description: Illustra come abilitare o disabilitare l'integrazione di LinkedIn per le app Microsoft in Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 02/28/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: cdfb5458b020e9d3a3f33cecbeb0ee7b9a48909d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="linkedin-integration-for-office-applications"></a>Integrazione di LinkedIn per le applicazioni di Office
Questo articolo descrive come limitare gli utenti a cui fornire l'integrazione di LinkedIn in Azure Active Directory (Azure AD). Quando viene aggiunta al tenant, l'integrazione di LinkedIn è abilitata per impostazione predefinita. Ciò consente agli utenti di accedere ai dati di LinkedIn pubblici all'interno di alcune app Microsoft. Ogni utente può scegliere in modo indipendente di connettere il proprio account aziendale o dell'istituto di istruzione al proprio account LinkedIn.

> [!IMPORTANT]
> L'integrazione di LinkedIn non viene distribuita contemporaneamente a tutti i tenant di Azure AD. Dopo l'implementazione nel tenant di Azure, l'integrazione di LinkedIn è abilitata per impostazione predefinita. L'integrazione di LinkedIn non è disponibile per i tenant locali, sovrani e di enti pubblici. Per una visualizzazione aggiornata delle informazioni di implementazione, vedere la pagina [Roadmap di Office 365](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc).

## <a name="linkedin-integration-from-the-user-perspective"></a>Integrazione di LinkedIn dal punto di vista degli utenti
Quando gli utenti dell'organizzazione connettono il proprio account LinkedIn all'account aziendale o dell'istituto di istruzione, [accettano che LinkedIn fornisca dati](https://www.linkedin.com/help/linkedin/answer/84077) utilizzabili nelle app e nei servizi offerti dall'organizzazione. [Gli utenti possono disconnettere l'account](https://www.linkedin.com/help/linkedin/answer/85097). In questo modo ritirano a LinkedIn l'autorizzazione a condividere dati con Microsoft. L'integrazione di LinkedIn usa le informazioni dei profili LinkedIn disponibili pubblicamente. [Gli utenti possono controllare la modalità di visualizzazione del proprio profilo LinkedIn](https://www.linkedin.com/help/linkedin/answer/83) tramite le impostazioni di privacy di LinkedIn. Possono ad esempio stabilire se consentire la visualizzazione del proprio profilo all'interno delle app Microsoft.

## <a name="privacy-considerations"></a>Considerazioni sulla privacy
L'abilitazione dell'integrazione di LinkedIn in Azure AD consente alle app e ai servizi Microsoft di accedere ad alcune informazioni LinkedIn degli utenti. Leggere l'[Informativa sulla privacy di Microsoft](https://privacy.microsoft.com/privacystatement/) per altre informazioni sulle considerazioni relative alla privacy quando si abilita l'integrazione di LinkedIn in Azure AD. 

## <a name="manage-linkedin-integration"></a>Gestire l'integrazione di LinkedIn
L'integrazione di LinkedIn per le aziende è abilitata per impostazione predefinita in Azure AD. L'abilitazione dell'integrazione di LinkedIn consente a tutti gli utenti dell'organizzazione di usare le funzionalità di LinkedIn direttamente dai servizi Microsoft, ad esempio per visualizzare i profili LinkedIn in Outlook. Se si disabilita l'integrazione di LinkedIn, le funzionalità di LinkedIn vengono rimosse dalle app e dai servizi Microsoft e la condivisione dei dati tra LinkedIn e l'organizzazione tramite i servizi Microsoft si interrompe.

### <a name="enable-or-disable-linkedin-integration-for-your-organization-in-the-azure-portal"></a>Abilitare o disabilitare l'integrazione di LinkedIn per l'organizzazione nel portale di Azure

1. Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com/) con un account amministratore globale per il tenant di Azure AD.
2. Selezionare **Utenti**.
3. Nel pannello **Utenti** selezionare **Impostazioni utente**.
4. In **Integrazione di LinkedIn** selezionare **Sì** o **No** per abilitare o disabilitare l'integrazione di LinkedIn.
   ![Abilitazione dell'integrazione di LinkedIn](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="enable-or-disable-linkedin-integration-for-your-organizations-office-2016-apps-using-group-policy"></a>Abilitare o disabilitare l'integrazione di LinkedIn per le app di Office 2016 dell'organizzazione tramite Criteri di gruppo

1. Scaricare i [file dei modelli amministrativi di Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Estrarre i file **ADMX** e copiarli nel **repository centralizzato**.
3. Aprire Gestione Criteri di gruppo.
4. Creare un oggetto Criteri di gruppo con le impostazioni seguenti: **Configurazione utente** > **Modelli amministrativi** > **Microsoft Office 2016** > **Varie** > **Allow LinkedIn Integration** (Consenti integrazione di LinkedIn).
5. Selezionare **Abilitato** o **Disabilitato**.
  * Se il criterio è **Abilitato**, l'impostazione **Show LinkedIn features in Office applications** (Mostra funzionalità di LinkedIn nelle applicazioni di Office) nella finestra di dialogo Opzioni di Office 2016 è abilitata. Ciò significa anche che gli utenti dell'organizzazione possono usare le funzionalità di LinkedIn nelle applicazioni di Office.
  * Se il criterio è **Disabilitato**, l'impostazione **Show LinkedIn features in Office applications** (Mostra funzionalità di LinkedIn nelle applicazioni di Office) nella finestra di dialogo Opzioni di Office 2016 è disabilitata e gli utenti finali non possono modificare questa impostazione. Gli utenti dell'organizzazione non possono usare le funzionalità di LinkedIn nelle applicazioni di Office 2016. 

Questi Criteri di gruppo influiscono solo sulle app di Office 2016 per il computer locale. Gli utenti possono visualizzare in Office 365 le funzionalità di LinkedIn nella scheda del profilo anche se disabilitano LinkedIn per le app di Office 2016. 

### <a name="learn-more"></a>Altre informazioni 
* [About LinkedIn information and features in Microsoft apps and services](https://go.microsoft.com/fwlink/?linkid=850740) (Informazioni e funzionalità di LinkedIn nelle app e nei servizi Microsoft)

* [Centro assistenza LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Passaggi successivi
Usare il collegamento seguente per visualizzare le impostazioni correnti dell'integrazione di LinkedIn nel portale di Azure:

[Configurare l'integrazione di LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 