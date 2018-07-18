---
title: Abilitare la versione di valutazione con Azure AD | Azure
description: Abilitare il tipo di inserzione di versione di valutazione con Azure Active Directory (Azure AD) in Azure Marketplace e AppSource per editori di app e servizi
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
ms.openlocfilehash: 4140ba98c0c65c22674c61dc7266818af904e777
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2018
ms.locfileid: "34826062"
---
# <a name="enable-trial-using-azure-ad"></a>Abilitare la versione di valutazione con Azure AD  
Azure Active Directory (Azure AD) è un servizio di gestione delle identità cloud che consente di eseguire l'autenticazione con un account Microsoft aziendale o dell'istituto di istruzione tramite framework standard OAuth e OpenID Connect.  
*   Per altre informazioni su Azure AD, visitare la pagina di Azure Active Directory all'indirizzo [azure.microsoft.com/services/active-directory](https://azure.microsoft.com/services/active-directory).  

L'utente e i clienti vengono autenticati nel Marketplace con Azure AD. Dopo che il cliente seleziona la versione di valutazione in Marketplace, viene reindirizzato all'ambiente di prova.  Nell'ambiente di prova, è possibile configurare il cliente direttamente senza richiedere altri passaggi di accesso. L'app o l'offerta ricevono un token da Azure AD in fase di autenticazione che include informazioni importanti sull'utente per creare un account utente nell'app o nell'offerta. È possibile automatizzare la configurazione e aumentare la probabilità di conversione.  
*   Per altre informazioni sul token inviato da Azure AD durante l'autenticazione, visitare la sezione dei token di esempio all'indirizzo [docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)

Usare Azure AD per abilitare l'autenticazione con un solo clic nell'app o alla versione di valutazione.  
*   Ottimizzare l'esperienza di accesso dei clienti da marketplace alla versione di valutazione.  
*   Gestire l'aspetto di un'esperienza di prodotto, anche quando l'utente viene reindirizzato dal Marketplace al dominio o all'ambiente di valutazione dell'editore.  
*   Vengono ridotte le probabilità di abbandono al momento del reindirizzamento poiché non sono richiesti altri passaggi di accesso.  
*   Viene facilitata la distribuzione per l'ampia popolazione di utenti di Azure AD.  

## <a name="verify-your-azure-ad-integration-on-the-marketplace-multitenant-apps"></a>Verificare l'integrazione di Azure AD su Marketplace: app multi-tenant  
Supporta le seguenti opzioni per la soluzione con Azure AD.  
*   Registrare l'app nelle vetrine sul Marketplace.  
*   Abilitare la funzionalità per il supporto multi-tenancy in Azure AD per ottenere un'esperienza di valutazione con un solo clic.  
    *   Per altre informazioni sulla registrazione dell'app, visitare la pagina Integrazione di applicazioni con Azure Active Directory all'indirizzo [docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Se non si ha familiarità con l'uso dell'accesso Single Sign-On (SSO) federato di Azure AD, eseguire la procedura seguente.  
1.  Registrare l'app in Marketplace. 
2.  Sviluppare SSO con Azure AD usando OAuth 2.0 o OpenID Connect.  
    *   Per altre informazioni su OAuth 2.0, visitare la pagina relativa a OAuth 2.0 all'indirizzo [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).  
    *   Per altre informazioni su Open ID Connect, visitare la pagina relativa a Open ID Connect all'indirizzo [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  
3.  Abilitare la funzionalità per il supporto multi-tenancy in Azure AD per fornire un'esperienza di valutazione con un solo clic.  
    *   Per altre informazioni sulla certificazione AppSource, visitare la pagina della certificazione AppSource all'indirizzo[docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

## <a name="verify-your-azure-ad-integration-on-the-marketplace-single-tenant-apps"></a>Verificare l'integrazione di Azure AD su Marketplace: app a tenant singolo  
Supporta una delle seguenti opzioni per la soluzione a tenant singolo.  
*   Aggiungere utenti alla directory come utenti guest tramite Azure AD B2B.  
    *   Per altre informazioni su Azure AD B2B, visitare la pagina Informazioni su Collaborazione B2B di Azure AD all'indirizzo [docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Configurare manualmente le versioni di valutazione per i clienti tramite l'opzione Contattami.  
*   Sviluppare una test drive per ogni cliente.  
*   Creare un'app demo di esempio multi-tenant con SSO.  

## <a name="next-steps"></a>Passaggi successivi
*   Visitare la pagina [Guida alla pubblicazione per Azure Marketplace e AppSource](./marketplace-publishers-guide.md).  
 
---  

