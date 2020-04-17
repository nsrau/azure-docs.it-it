---
title: Azioni o app cloud nei criteri di accesso condizionale - Azure Active Directory
description: Che cosa sono le app o le azioni cloud in un criterio di accesso condizionale di Azure ADWhat are cloud apps or actions in an Azure AD Conditional Access policy
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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457298"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Accesso condizionale: app o azioni cloudConditional Access: Cloud apps or actions

Le app o le azioni cloud sono un segnale chiave in un criterio di accesso condizionale. I criteri di accesso condizionale consentono agli amministratori di assegnare controlli ad applicazioni o azioni specifiche.

- Gli amministratori possono scegliere dall'elenco di applicazioni che includono applicazioni Microsoft incorporate e tutte le [applicazioni integrate di Azure AD,](../manage-apps/what-is-application-management.md) tra cui gallery, non raccolte e applicazioni pubblicate tramite il proxy [dell'applicazione](../manage-apps/what-is-application-proxy.md).
- Gli amministratori possono scegliere di definire i criteri non in base a un'applicazione cloud, ma a un'azione dell'utente. L'unica azione supportata è Registra informazioni di sicurezza (anteprima), consentendo l'accesso condizionale per applicare i controlli relativi all'esperienza combinata di registrazione delle informazioni di [sicurezza.](../authentication/howto-registration-mfa-sspr-combined.md)

![Definire criteri di accesso condizionale e specificare le app cloudDefine a Conditional Access policy and specify cloud apps](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Applicazioni cloud Microsoft

Molte delle applicazioni cloud Microsoft esistenti sono incluse nell'elenco delle applicazioni tra cui è possibile selezionare. 

Gli amministratori possono assegnare un criterio di accesso condizionale alle app cloud seguenti da Microsoft.Administrators can assign a Conditional Access policy to the following cloud apps from Microsoft. Alcune app come Office 365 (anteprima) e Gestione di Microsoft Azure includono più app o servizi figlio correlati. L'elenco seguente non è esaustivo ed è soggetto a modifiche.

- [Office 365 (anteprima)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Database SQL di Azure e Azure SQL Data Warehouse](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Analisi di Microsoft Application Insights
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Gestione di Microsoft Azure](#microsoft-azure-management)
- Microsoft Azure Subscription Management
- Microsoft Cloud App Security
- Microsoft Commerce Tools Access Control Portal
- Servizio di autenticazione Microsoft Commerce Tools
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Registrazione di Microsoft Intune](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Ricerca Microsoft in Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Sway di ufficio
- Outlook Groups
- Servizio Power BI
- Project Online
- Skype for Business Online
- Rete privata virtuale (VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>Office 365 (anteprima)

Office 365 offre servizi di produttività e collaborazione basati su cloud come Exchange, SharePoint e Microsoft Teams. I servizi cloud di Office 365 sono profondamente integrati per garantire esperienze fluide e collaborative. Questa integrazione può causare confusione durante la creazione di criteri come alcune applicazioni, ad esempio Microsoft Teams hanno dipendenze da altri, ad esempio SharePoint o Exchange.This integration can cause confusion when creating policies as some apps such as Microsoft Teams have dependencies on others such as SharePoint or Exchange.

L'app di Office 365 (anteprima) consente di gestire questi servizi contemporaneamente. È consigliabile usare la nuova app di Office 365 (anteprima), anziché scegliere come target singole app cloud. La destinazione di questo gruppo di applicazioni consente di evitare problemi che possono verificarsi a causa di criteri e dipendenze incoerenti.

Gli amministratori possono scegliere di escludere app specifiche dai criteri se lo desiderano includendo l'app di Office 365 (anteprima) ed escludendo le app specifiche di loro scelta nei criteri.

Applicazioni chiave incluse nell'app client di Office 365 (anteprima):

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
   - Portale di amministrazione delle sottoscrizioni di Visual StudioVisual Studio subscriptions administrator portal
   - Azure DevOps
   - Portale di Azure Data FactoryAzure Data Factory portal

> [!NOTE]
> The Microsoft Azure Management application applies to Azure PowerShell, which calls the Azure Resource Manager API. Non si applica ad Azure AD PowerShell che chiama Microsoft Graph.

## <a name="other-applications"></a>Altre applicazioni

Oltre alle app Microsoft, gli amministratori possono aggiungere qualsiasi applicazione registrata di Azure AD ai criteri di accesso condizionale. Queste applicazioni possono comprendere: 

- Applicazioni pubblicate tramite il proxy di applicazione di [Azure ADApplications](../manage-apps/what-is-application-proxy.md) published through Azure AD Application Proxy
- [Applicazioni aggiunte dalla galleria](../manage-apps/add-application-portal.md)
- [Applicazioni personalizzate non nella raccolta](../manage-apps/add-non-gallery-app.md)
- [Applicazioni legacy pubblicate tramite i controller e le reti di distribuzione delle app](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Azioni utente

Le azioni utente sono attività che possono essere eseguite da un utente. L'unica azione attualmente supportata è **Registra informazioni**di sicurezza , che consente l'applicazione dei criteri di accesso condizionale quando gli utenti abilitati per il tentativo di registrazione combinato di registrare le informazioni di sicurezza. Ulteriori informazioni sono disponibili nell'articolo Registrazione combinata delle informazioni di [sicurezza](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Passaggi successivi

- [Accesso condizionale: condizioniConditional Access: Conditions](concept-conditional-access-conditions.md)

- [Criteri comuni di Accesso condizionaleConditional Access common policies](concept-conditional-access-policy-common.md)
- [Dipendenze dell'applicazione clientClient application dependencies](service-dependencies.md)
