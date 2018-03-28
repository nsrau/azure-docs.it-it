---
title: Abilitare le connessioni di LinkedIn per le app e i servizi Microsoft in Azure Active Directory | Microsoft Docs
description: Illustra come abilitare o disabilitare le connessioni dell'account LinkedIn per le app Microsoft in Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/15/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 3bf224edea9e6da0d0eadb6fb6a409248de3d0e3
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="linkedin-account-connections-for-microsoft-apps-and-services"></a>Connessioni dell'account LinkedIn per app e servizi Microsoft
Questo articolo spiega come gestire le connessioni dell'account LinkedIn per il tenant nell'interfaccia di amministrazione di Azure Active Directory (Azure AD). 

> [!IMPORTANT]
> La funzionalità delle connessioni dell'account LinkedIn è attualmente in corso di distribuzione nei tenant di Azure AD. Quando viene distribuita nel tenant, questa funzionalità è abilitata per impostazione predefinita. Non è disponibile per i clienti del settore pubblico degli Stati Uniti e le organizzazioni con cassette postali di Exchange Online ospitate in Australia, Canada, Cina, Francia, Germania, India, Corea del Sud, Regno Unito, Giappone e Sudafrica. Il supporto per queste località delle cassette postali sarà presto disponibile.  Per una visualizzazione aggiornata delle informazioni di implementazione, vedere la pagina [Roadmap di Office 365](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc).

## <a name="how-linkedin-account-connections-appear-to-the-user"></a>Modalità di visualizzazione delle connessioni dell'account LinkedIn per l'utente
Le connessioni dell'account LinkedIn consentono agli utenti di visualizzare le informazioni del profilo LinkedIn disponibili pubblicamente all'interno di alcune delle app Microsoft. Gli utenti nel tenant possono scegliere di connettere il proprio account LinkedIn e l'account Microsoft aziendale o dell'istituto di istruzione per visualizzare altre informazioni del profilo LinkedIn. Per altri dettagli, vedere [LinkedIn information and features in Microsoft apps and services](https://go.microsoft.com/fwlink/?linkid=850740) (Informazioni e funzionalità di LinkedIn nelle app e nei servizi Microsoft).

Quando gli utenti dell'organizzazione connettono il proprio account LinkedIn e l'account Microsoft aziendale o dell'istituto di istruzione, hanno due possibilità: 
* Concedere l'autorizzazione per condividere i dati tra entrambi gli account. Ciò significa che autorizzano il proprio account LinkedIn a condividere i dati con l'account Microsoft aziendale o dell'istituto di istruzione, nonché il proprio account Microsoft aziendale o dell'istituto di istruzione a condividere i dati con l'account LinkedIn. I dati condivisi con LinkedIn lasciano i servizi online. 
* Concedere l'autorizzazione per condividere i dati solo dal proprio account LinkedIn all'account Microsoft aziendale o dell'istituto di istruzione.

Per altre informazioni sui dati che vengono condivisi tra l'account LinkedIn e l'account Microsoft aziendale o dell'istituto di istruzione di un utente, vedere [LinkedIn in Microsoft applications at your work or school](https://www.linkedin.com/help/linkedin/answer/84077) (LinkedIn nelle applicazioni Microsoft in azienda o nell'istituto di istruzione). 
* [Gli utenti possono disconnettere gli account](https://www.linkedin.com/help/linkedin/answer/85097) e rimuovere le autorizzazioni alla condivisione dei dati in qualsiasi momento. 
* [Gli utenti possono controllare la modalità di visualizzazione del proprio profilo LinkedIn](https://www.linkedin.com/help/linkedin/answer/83), stabilendo ad esempio se consentire la visualizzazione del profilo all'interno delle app Microsoft.

## <a name="privacy-considerations"></a>Considerazioni sulla privacy
L'abilitazione delle connessioni dell'account LinkedIn consente alle app e ai servizi Microsoft di accedere ad alcune informazioni di LinkedIn degli utenti. Leggere l'[Informativa sulla privacy di Microsoft](https://privacy.microsoft.com/privacystatement/) per altre informazioni sulle considerazioni relative alla privacy quando si abilitano le connessioni dell'account LinkedIn in Azure AD. 

## <a name="manage-linkedin-account-connections"></a>Gestire le connessioni dell'account LinkedIn
La funzionalità delle connessioni dell'account LinkedIn è abilitata per impostazione predefinita per l'intero tenant. È possibile scegliere di disabilitare le connessioni dell'account LinkedIn per l'intero tenant oppure abilitare le connessioni dell'account LinkedIn per utenti selezionati nel tenant. 

### <a name="enable-or-disable-linkedin-account-connection-for-your-tenant-in-the-azure-portal"></a>Abilitare o disabilitare la connessione dell'account LinkedIn per il tenant nel portale di Azure

1. Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com/) con un account amministratore globale per il tenant di Azure AD.
2. Selezionare **Utenti**.
3. Nel pannello **Utenti** selezionare **Impostazioni utente**.
4. In **LinkedIn account connections** (Connessioni account LinkedIn):
  * Selezionare **Sì** per abilitare le connessioni dell'account LinkedIn per tutti gli utenti nel tenant
  * Selezionare **Selezionati** per abilitare le connessioni dell'account LinkedIn solo per gli utenti del tenant selezionati
  * Selezionare **No** per disabilitare le connessioni dell'account LinkedIn per tutti gli utenti ![Abilitazione delle connessioni dell'account LinkedIn](./media/linkedin-integration/LinkedIn-integration.png)
5. Al termine salvare le impostazioni facendo clic su **Salva**.

### <a name="enable-or-disable-linkedin-account-connections-for-your-organizations-office-2016-apps-using-group-policy"></a>Abilitare o disabilitare le connessioni dell'account LinkedIn per le app di Office 2016 dell'organizzazione tramite Criteri di gruppo

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
Usare il collegamento seguente per visualizzare le impostazioni correnti delle connessioni dell'account LinkedIn nel portale di Azure:

[Configurare le connessioni dell'account LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 