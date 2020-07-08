---
title: App cloud o azioni nei criteri di accesso condizionale - Azure Active Directory
description: Cosa sono le app cloud o le azioni in un criterio di accesso condizionale di Azure AD
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
ms.openlocfilehash: dfdfcda0ce466299d57f79ba1e60a96309078587
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84014350"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Accesso condizionale: App o azioni cloud

Le app cloud o le azioni sono un segnale chiave in un criterio di accesso condizionale. I criteri di accesso condizionale consentono agli amministratori di assegnare controlli a specifiche applicazioni o azioni.

- Gli amministratori possono scegliere dall'elenco di applicazioni, che include applicazioni Microsoft incorporate e tutte le [applicazioni integrate di Azure AD](../manage-apps/what-is-application-management.md), tra cui quelle incluse o meno in una raccolta e applicazioni pubblicate tramite [proxy dell'applicazione](../manage-apps/what-is-application-proxy.md).
- Gli amministratori possono scegliere di definire un criterio basato non su un'applicazione cloud, ma su un'azione dell'utente. L'unica azione supportata è Registra le informazioni di sicurezza (anteprima), che consente l'accesso condizionale per l'applicazione di controlli relativi all'[esperienza di registrazione di informazioni di sicurezza combinate](../authentication/howto-registration-mfa-sspr-combined.md).

![Definire i criteri di accesso condizionale e specificare un'applicazione cloud](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Applicazioni cloud Microsoft

Molte delle applicazioni cloud Microsoft esistenti sono incluse nell'elenco selezionabile di applicazioni. 

Gli amministratori possono assegnare un criterio di accesso condizionale alle seguenti applicazioni cloud di Microsoft. Alcune app come Office 365 (anteprima) e gestione di Microsoft Azure includono più app o servizi figlio correlati. L'elenco riportato di seguito non è esaustivo ed è soggetto a modifiche.

- [Office 365 (anteprima)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Database SQL di Azure e Data Warehouse](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Gestione di Microsoft Azure](#microsoft-azure-management)
- Gestione delle sottoscrizioni di Microsoft Azure
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
- Office Sway
- Outlook Groups
- Servizio Power BI
- Project Online
- Skype for Business Online
- Rete privata virtuale (VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>Office 365 (anteprima)

Office 365 offre servizi di produttività e collaborazione basati sul cloud come Exchange, SharePoint e Microsoft Teams. I servizi cloud di Office 365 sono ampiamente integrati, per garantire esperienze uniformi e collaborative. Questa integrazione può causare confusione quando si creano criteri perché alcune app, come ad esempio Microsoft Teams, presentano dipendenze da altre, quali SharePoint o Exchange.

L'app Office 365 (anteprima) consente di fare riferimento a tutti i servizi in una sola volta. È consigliabile usare la nuova app Office 365 (anteprima), anziché fare riferimento a singole app cloud per evitare problemi con le [dipendenze del servizio](service-dependencies.md). Facendo riferimento a questo gruppo di applicazioni è possibile evitare problemi che possono verificarsi a causa di criteri e dipendenze non coerenti.

Se lo desiderano, gli amministratori possono scegliere di escludere app specifiche dai criteri includendo l'app Office 365 (anteprima) ed escludendo le app specifiche di loro scelta nei criteri.

Applicazioni chiave incluse nell'app client Office 365 (anteprima):

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Servizio di ricerca di Office 365
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype for Business Online
   - Sway

### <a name="microsoft-azure-management"></a>Gestione di Microsoft Azure

L'applicazione di gestione di Microsoft Azure include più servizi sottostanti. 

   - Portale di Azure
   - Provider di Azure Resource Manager
   - API del modello di distribuzione classica
   - Azure PowerShell
   - Portale di amministratore delle sottoscrizioni di Visual Studio
   - Azure DevOps
   - Portale di Azure Data Factory

> [!NOTE]
> L'applicazione di gestione di Microsoft Azure si applica ad Azure PowerShell che chiama l'API di Azure Resource Manager. Non si applica ad Azure AD PowerShell, che chiama Microsoft Graph.

## <a name="other-applications"></a>Altre applicazioni

Oltre alle app Microsoft, gli amministratori possono aggiungere ai criteri di accesso condizionale qualsiasi applicazione registrata di Azure AD. Queste applicazioni possono includere: 

- Applicazioni pubblicate tramite il [proxy di applicazione di Azure AD](../manage-apps/what-is-application-proxy.md)
- [Applicazioni aggiunte dalla raccolta](../manage-apps/add-application-portal.md)
- [Applicazioni personalizzate non presenti nella raccolta](../manage-apps/add-non-gallery-app.md)
- [Applicazioni legacy pubblicate tramite controller e reti per la distribuzione di app](../manage-apps/secure-hybrid-access.md)

> [!NOTE]
> Poiché i criteri di accesso condizionale consentono di impostare i requisiti per l'accesso a un servizio, non è possibile applicarli a un'applicazione client (pubblica/nativa). In altre parole, il criterio non è impostato direttamente su un'applicazione client (pubblica/nativa), ma viene applicato quando un client chiama un servizio. I criteri impostati nel servizio SharePoint, ad esempio, vengono applicati ai client che chiamano SharePoint. Un criterio impostato in Exchange si applica al tentativo di accedere alla posta elettronica tramite il client Outlook. Questo è il motivo per cui le applicazioni client (pubbliche/native) non sono disponibili nella selezione delle app cloud e l'opzione di accesso condizionale non è disponibile nelle impostazioni dell'applicazione client (pubblica/nativa) registrata nel tenant. 


## <a name="user-actions"></a>Azioni utente

Le azioni dell'utente sono attività che possono essere eseguite da un utente. L'unica azione attualmente supportata è **Registra le informazioni di sicurezza**, che consente di applicare criteri di accesso condizionale quando gli utenti abilitati per la registrazione combinata tentano di registrare le proprie informazioni di sicurezza. Altre informazioni sono disponibili nell'articolo [Registrazione di informazioni di sicurezza combinate](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Passaggi successivi

- [Accesso condizionale: condizioni](concept-conditional-access-conditions.md)

- [Criteri comuni di accesso condizionale ](concept-conditional-access-policy-common.md)
- [Dipendenze dell'applicazione client](service-dependencies.md)
