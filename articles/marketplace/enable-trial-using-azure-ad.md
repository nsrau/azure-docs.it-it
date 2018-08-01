---
title: Abilitare una versione di valutazione nel Marketplace di Azure usando Azure Active Directory | Azure
description: Abilitare il tipo di inserzione versione di valutazione con Azure Active Directory nel Marketplace di Azure e in AppSource per editori di app e servizi.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/04/2018
ms.author: ellacroi
ms.openlocfilehash: c5b7b4967c1acef733d366e651d50706db42aace
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160468"
---
# <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Attivare un'inserzione relativa a una versione di valutazione usando Azure Active Directory

Azure Active Directory (Azure AD) è un servizio di gestione delle identità cloud che consente di eseguire l'autenticazione con un account Microsoft aziendale o dell'istituto di istruzione tramite framework standard. Azure AD supporta l'autenticazione OAuth e OpenID Connect. Il [Marketplace di Azure](https://azuremarketplace.microsoft.com) usa Azure AD per autenticare gli utenti e i loro clienti.

Per altre informazioni su Azure AD, vedere [Azure Active Directory](https://azure.microsoft.com/services/active-directory).

Dopo che il cliente ha selezionato la versione di valutazione nel Marketplace, viene reindirizzato all'ambiente di prova. Nell'ambiente di prova è possibile configurare il cliente direttamente senza bisogno di altri passaggi di accesso. L'app o l'offerta riceve un token da Azure AD durante l'autenticazione. Il token include informazioni pertinenti sull'utente che consentono di creare un account utente nell'app o offerta. È possibile automatizzare la configurazione del cliente e aumentare la probabilità di conversione.

Per altre informazioni sul token che viene inviato da Azure AD durante l'autenticazione, vedere [Token di esempio](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Usare Azure AD per abilitare l'autenticazione con un solo clic nell'app o alla versione di valutazione. Azure AD offre i vantaggi seguenti: 
*   Ottimizzare l'esperienza di accesso dei clienti dal Marketplace alla versione di valutazione.
*   Gestire l'aspetto di un'esperienza di prodotto, anche quando l'utente viene reindirizzato dal Marketplace al dominio o all'ambiente di valutazione dell'editore.
*   Ridurre le probabilità di abbandono al momento del reindirizzamento poiché non sono richiesti altri passaggi di accesso.
*   Viene facilitata la distribuzione per l'ampia popolazione di utenti di Azure AD.

## <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Verificare l'integrazione di Azure AD su Marketplace: app multi-tenant
Usare Azure AD per supportare le seguenti opzioni per la soluzione:
*   Registrare l'app nelle vetrine sul Marketplace.
*   Abilitare la funzionalità per il supporto multi-tenancy in Azure AD per ottenere un'esperienza di valutazione con un solo clic.

Per altre informazioni sulla registrazione delle app, vedere [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

Se non si ha familiarità con l'uso dell'accesso Single Sign-On (SSO) federato di Azure AD, eseguire la procedura seguente:
1.  Registrare l'app nel Marketplace. 
2.  Sviluppare SSO con Azure AD usando OAuth 2.0 o OpenID Connect.
    *   Per altre informazioni su OAuth 2.0, vedere [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
    *   Per altre informazioni su Openid Connect, vedere [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
3.  Abilitare la funzionalità per il supporto multi-tenancy in Azure AD per fornire un'esperienza di valutazione con un solo clic.
    
    Per altre informazioni sulla certificazione AppSource, vedere [Certificazione AppSource](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

## <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Verificare l'integrazione di Azure AD sul Marketplace: app a tenant singolo
Usare Azure AD per supportare una delle seguenti opzioni per la soluzione a tenant singolo: 
*   Aggiungere utenti alla directory come utenti guest tramite Azure Active Directory B2B (Azure AD B2B).
    
    Per altre informazioni su Azure AD B2B, vedere [Informazioni su Collaborazione B2B di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Configurare manualmente le versioni di valutazione per i clienti mediante l'opzione di pubblicazione Contattami.
*   Sviluppare una test drive per ogni cliente.
*   Creare un'app demo di esempio multitenant che usa SSO.

## <a name="next-steps"></a>Passaggi successivi
*   Rivedere la [Guida alla pubblicazione per Azure Marketplace e AppSource](./marketplace-publishers-guide.md).
