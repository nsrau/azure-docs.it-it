---
title: App Cloud o azioni nei criteri di accesso condizionale-Azure Active Directory
description: Cosa sono le app o le azioni cloud in un Azure AD criteri di accesso condizionale
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b15b022726c09ccbaf9674775d114c8dd1916e1d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457298"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Accesso condizionale: app Cloud o azioni

Le app Cloud o le azioni sono un segnale chiave in un criterio di accesso condizionale. I criteri di accesso condizionale consentono agli amministratori di assegnare controlli a specifiche applicazioni o azioni.

- Gli amministratori possono scegliere dall'elenco di applicazioni che includono applicazioni Microsoft incorporate e qualsiasi [Azure ad applicazioni integrate](../manage-apps/what-is-application-management.md) , tra cui raccolta, non raccolta e applicazioni pubblicate tramite il [proxy di applicazione](../manage-apps/what-is-application-proxy.md).
- Gli amministratori possono scegliere di definire un criterio non basato su un'applicazione cloud, ma in un'azione dell'utente. L'unica azione supportata consiste nel registrare le informazioni di sicurezza (anteprima), consentendo l'accesso condizionale per applicare i controlli sull' [esperienza di registrazione delle informazioni di sicurezza combinata](../authentication/howto-registration-mfa-sspr-combined.md).

![Definire un criterio di accesso condizionale e specificare le app Cloud](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Applicazioni cloud Microsoft

Molte delle applicazioni cloud Microsoft esistenti sono incluse nell'elenco di applicazioni che è possibile selezionare. 

Gli amministratori possono assegnare un criterio di accesso condizionale alle app Cloud seguenti da Microsoft. Alcune app come Office 365 (anteprima) e gestione Microsoft Azure includono più app o servizi figlio correlati. L'elenco seguente non è esaustivo ed è soggetto a modifiche.

- [Office 365 (anteprima)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Database SQL di Azure e Azure SQL Data Warehouse](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Gestione di Microsoft Azure](#microsoft-azure-management)
- Gestione delle sottoscrizioni Microsoft Azure
- Microsoft Cloud App Security
- Portale di controllo di accesso di Microsoft Commerce Tools
- Servizio di autenticazione di Microsoft Commerce Tools
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Registrazione di Microsoft Intune](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Search in Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Sway di Office
- Outlook Groups
- Servizio Power BI
- Project Online
- Skype for Business Online
- Rete privata virtuale (VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>Office 365 (anteprima)

Office 365 fornisce servizi di produttività e collaborazione basati sul cloud come Exchange, SharePoint e Microsoft teams. I servizi cloud di Office 365 sono profondamente integrati per garantire esperienze uniformi e collaborative. Questa integrazione può causare confusione quando si creano criteri perché alcune app, ad esempio i team Microsoft, presentano dipendenze da altri utenti, ad esempio SharePoint o Exchange.

L'app Office 365 (anteprima) consente di utilizzare tutti i servizi in una sola volta. È consigliabile usare la nuova app Office 365 (anteprima), anziché fare riferimento a singole app cloud. La definizione di questo gruppo di applicazioni consente di evitare problemi che possono verificarsi a causa di criteri e dipendenze non coerenti.

Gli amministratori possono scegliere di escludere app specifiche dai criteri se desiderano includere l'app di Office 365 (anteprima) ed escludere le app specifiche di loro scelta nei criteri.

Applicazioni principali incluse nell'app client Office 365 (anteprima):

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Office 365 servizio di ricerca
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype for Business Online
   - Sway

### <a name="microsoft-azure-management"></a>Gestione di Microsoft Azure

L'applicazione di gestione Microsoft Azure include più servizi sottostanti. 

   - Portale di Azure
   - Provider di Azure Resource Manager
   - API del modello di distribuzione classica
   - Azure PowerShell
   - Portale di amministrazione delle sottoscrizioni di Visual Studio
   - Azure DevOps
   - Portale di Azure Data Factory

> [!NOTE]
> L'applicazione di gestione Microsoft Azure si applica ai Azure PowerShell, che chiama l'API Azure Resource Manager. Non si applica ad Azure AD PowerShell che chiama Microsoft Graph.

## <a name="other-applications"></a>Altre applicazioni

Oltre alle app Microsoft, gli amministratori possono aggiungere qualsiasi Azure AD applicazione registrata ai criteri di accesso condizionale. Queste applicazioni possono comprendere: 

- Applicazioni pubblicate tramite il [proxy di applicazione Azure ad](../manage-apps/what-is-application-proxy.md)
- [Applicazioni aggiunte dalla raccolta](../manage-apps/add-application-portal.md)
- [Applicazioni personalizzate non presenti nella raccolta](../manage-apps/add-non-gallery-app.md)
- [Applicazioni legacy pubblicate tramite controller e reti per la distribuzione di app](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Azioni utente

Le azioni dell'utente sono attività che possono essere eseguite da un utente. L'unica azione attualmente supportata consiste nel **registrare le informazioni di sicurezza**, che consentono di applicare i criteri di accesso condizionale quando gli utenti abilitati per la registrazione combinata tentano di registrare le informazioni di sicurezza. Altre informazioni sono disponibili nell'articolo relativo alla [registrazione delle informazioni di sicurezza combinate](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Passaggi successivi

- [Accesso condizionale: Condizioni](concept-conditional-access-conditions.md)

- [Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)
- [Dipendenze dell'applicazione client](service-dependencies.md)
