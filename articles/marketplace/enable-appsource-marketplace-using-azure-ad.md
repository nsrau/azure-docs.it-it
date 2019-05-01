---
title: Consentire la presentazione in Microsoft AppSource e Azure Marketplace con Azure Active Directory | Azure
description: Abilitare un tipo di inserzione usando Azure Active Directory nel Marketplace di Azure e in AppSource per gli editori di app e servizi.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 09/12/2018
ms.author: qianw211
ms.openlocfilehash: 247a45a38d732ace0455c6ca2ebbd5c44c384004
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734253"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Abilitare un'inserzione nel Marketplace e in AppSource con Azure Active Directory

 Azure Active Directory (Azure AD) è un servizio di gestione delle identità cloud che consente di eseguire l'autenticazione con un account Microsoft. Azure AD usa framework standard. [Altre informazioni su Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Vantaggi di Azure AD

I clienti di Microsoft AppSource e Azure Marketplace usano esperienze nel prodotto per cercare i cataloghi di inserzioni. Per poter eseguire queste azioni, tuttavia, devono eseguire l'accesso al prodotto. L'integrazione di Azure AD offre i vantaggi seguenti:

- Engagement più veloce e un'esperienza di accesso dei clienti ottimizzata
- Accesso Single Sign-On (SSO) per milioni di utenti aziendali
- Esperienza di accesso coerente per applicazioni pubblicate da partner diversi
- Autenticazione scalabile e multipiattaforma per app per dispositivi mobili e cloud

## <a name="offers-that-require-azure-ad"></a>Offerte che richiedono Azure AD

Le [opzioni di presentazione e i tipi di offerta](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) per AppSource e Azure Marketplace hanno requisiti diversi relativamente all'implementazione di Azure AD. Per informazioni dettagliate, vedere la tabella seguente:

| **Tipo di offerta**    | **SSO di AAD necessario?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Contattami | Versione di valutazione | Test drive | Transazione |
| Macchina virtuale | N/D | No  | No  | No  |
| App di Azure (modello di soluzione)  | N/D | N/D | N/D | N/D |
| App gestite  | N/D | N/D | N/D | No  |
| SaaS  | No  | Sì | Sì | Sì |
| Contenitori  | N/D | N/D | N/D | No  |
| Servizi di consulenza  | No  | N/D | N/D | N/D |

Per altre informazioni sui requisiti tecnici SaaS, vedere la [Guida alla pubblicazione dell'offerta di applicazioni SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Integrazione di Azure AD

- Per informazioni su come abilitare l'accesso Single Sign-On mediante l'integrazione di Azure AD nell'inserzione, vedere [Azure Active Directory per sviluppatori]( https://aka.ms/aaddev).
- Per informazioni dettagliate sull'accesso Single Sign-On di Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Abilitare un'inserzione relativa a una versione di valutazione

La configurazione automatica dei clienti può aumentare le probabilità di conversione. Se il cliente seleziona l'inserzione relativa a una versione di valutazione e viene reindirizzato all'ambiente di prova, è possibile configurare il cliente direttamente senza bisogno di altri passaggi di accesso.

Durante l'autenticazione, Azure AD invia un token all'app o all'offerta. Le informazioni utente fornite dal token consentono la creazione di un account utente nell'app o nell'offerta. Per altre informazioni, vedere [Token di esempio](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

Se si usa Azure AD per abilitare l'autenticazione con un solo clic nell'app o nell'inserzione relativa alla versione di valutazione, è possibile:

- Ottimizzare l'esperienza di accesso dei clienti dal Marketplace all'inserzione relativa alla versione di valutazione.
- Gestire l'aspetto di un'esperienza nel prodotto, anche quando l'utente viene reindirizzato dal Marketplace al dominio o all'ambiente di valutazione dell'editore.
- Ridurre le probabilità di abbandono al momento del reindirizzamento poiché non sono richiesti altri passaggi di accesso.
- Viene facilitata la distribuzione per l'ampia popolazione di utenti di Azure AD.

## <a name="verify-azure-ad-integration"></a>Verificare l'integrazione di Azure AD

### <a name="multitenant-solutions"></a>Soluzioni multi-tenant

Usare Azure AD per supportare le azioni seguenti:

- Registrare l'app in una delle vetrine del Marketplace. Per altre informazioni, vedere [Registrazione delle app](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) o [Certificazione AppSource](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).
- Abilitare la funzionalità per il supporto multi-tenancy in Azure AD per ottenere un'esperienza di valutazione con un solo clic.

Se non si ha familiarità con l'uso dell'accesso Single Sign-On (SSO) federato di Azure AD, seguire questa procedura:

1. Registrare l'app nel Marketplace.
1. Sviluppare l'accesso Single Sign-On con Azure AD usando [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) o [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Abilitare la funzionalità per il supporto multi-tenancy in Azure AD per fornire un'esperienza di valutazione con un solo clic.

### <a name="single-tenant-solutions"></a>Soluzioni a tenant singolo

Usare Azure AD per supportare una delle azioni seguenti:

- Aggiungere utenti guest alla directory tramite [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Configurare manualmente le versioni di valutazione per i clienti mediante l'opzione di pubblicazione **Contattami**.
- Sviluppare una test drive per ogni cliente.
- Creare un'app demo di esempio multi-tenant che usa SSO.

## <a name="next-steps"></a>Passaggi successivi

- Accertarsi di aver effettuato la [registrazione in Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
- Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/) per creare o completare l'offerta.
