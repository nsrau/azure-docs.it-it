---
title: Integrare l'offerta del marketplace commerciale Microsoft con Azure Active Directory
description: Usare Azure Active Directory per autenticare le offerte di Microsoft AppSource e Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/24/2020
ms.openlocfilehash: a6e304e5ffeab8f0a44cbdfe1566465f2b9bf34a
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607423"
---
# <a name="integrate-your-commercial-marketplace-listing-with-azure-active-directory"></a>Integrare l'inserzione del marketplace commerciale con Azure Active Directory

 Questo articolo illustra i requisiti per l'integrazione di un'offerta o un'inserzione del marketplace commerciale con Azure Active Directory (Azure AD). Azure AD è un servizio di gestione delle identità cloud che usa framework standard del settore per consentire l'autenticazione con un account Microsoft. [Altre informazioni su Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Vantaggi di Azure AD

Microsoft AppSource e i clienti di Azure Marketplace utilizzano esperienze nel prodotto per la ricerca di cataloghi online Store. Per poter eseguire queste azioni, tuttavia, devono eseguire l'accesso al prodotto. L'integrazione di Azure AD offre i vantaggi seguenti:

- Engagement più veloce e un'esperienza di accesso dei clienti ottimizzata
- Accesso Single Sign-On (SSO) per milioni di utenti aziendali
- Esperienza di accesso coerente per applicazioni pubblicate da partner diversi
- Autenticazione scalabile e multipiattaforma per app per dispositivi mobili e cloud

## <a name="offers-that-require-azure-ad"></a>Offerte che richiedono Azure AD

I diversi [tipi di offerta e opzioni di inserzione](determine-your-listing-type.md) per il marketplace commerciale hanno requisiti differenti per quanto riguarda l'implementazione di Azure AD. Per informazioni dettagliate, vedere la tabella seguente.

| Tipo di offerta    | Azure AD SSO è necessario per contattarmi?  | Azure AD SSO è necessario per la versione di valutazione? | Azure AD SSO necessario per test drive?  | Azure AD SSO necessario per Transact |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
| Macchina virtuale | N/D | No | No | No |
| App di Azure (modello di soluzione)  | N/D | N/D | N/D | N/D |
| App gestite  | N/D | N/D | N/D | No |
| SaaS  | No | Sì | Sì | Sì |
| Contenitori  | N/D | N/D | N/D | No |
| Servizi di consulenza  | No | N/D | N/D | N/D |

Per ulteriori informazioni sui requisiti tecnici SaaS, vedere [Azure ad e le offerte SaaS transazionali nel Marketplace commerciale](./azure-ad-saas.md).

## <a name="azure-ad-integration"></a>Integrazione di Azure AD

- Per informazioni dettagliate su come integrare Azure AD per le offerte SaaS (Software as a Service transazionale), vedere [Azure ad e le offerte SaaS transazionali nel Marketplace commerciale](./azure-ad-saas.md).
- Per informazioni su come abilitare l'accesso Single Sign-On mediante l'integrazione di Azure AD nell'inserzione, vedere [Azure Active Directory per sviluppatori](../active-directory/develop/index.yml).
- Per informazioni dettagliate sull'accesso Single Sign-On di Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md).

## <a name="enable-a-trial-listing"></a>Abilitare un'inserzione relativa a una versione di valutazione

La configurazione automatica dei clienti può aumentare le probabilità di conversione. Se il cliente seleziona l'inserzione relativa a una versione di valutazione e viene reindirizzato all'ambiente di prova, è possibile configurare il cliente direttamente senza bisogno di altri passaggi di accesso.

Durante l'autenticazione, Azure AD invia un token all'app o all'offerta. Le informazioni utente fornite dal token consentono la creazione di un account utente nell'app o nell'offerta. Per altre informazioni, vedere [Token di esempio](../active-directory/develop/id-tokens.md).

Se si usa Azure AD per abilitare l'autenticazione con un solo clic nell'app o nell'inserzione relativa alla versione di valutazione, è possibile:

- Semplifica l'esperienza del cliente dal Marketplace commerciale all'inserzione della versione di valutazione.
- Mantenere la sensazione di un'esperienza nel prodotto anche quando l'utente viene reindirizzato dal Marketplace commerciale al dominio o all'ambiente di valutazione.
- Ridurre le probabilità di abbandono al momento del reindirizzamento poiché non sono richiesti altri passaggi di accesso.
- Viene facilitata la distribuzione per l'ampia popolazione di utenti di Azure AD.

## <a name="verify-azure-ad-integration"></a>Verificare l'integrazione di Azure AD

### <a name="multitenant-solutions"></a>Soluzioni multi-tenant

Usare Azure AD per supportare le azioni seguenti:

- Registrare l'app in uno dei negozi online del Marketplace commerciale. Per altre informazioni, vedere [Registrazione delle app](../active-directory/develop/quickstart-register-app.md) o [Certificazione AppSource](../active-directory/azuread-dev/howto-get-appsource-certified.md).
- Abilitare la funzionalità per il supporto multi-tenancy in Azure AD per ottenere un'esperienza di valutazione con un solo clic.

Se non si ha familiarità con l'uso dell'accesso Single Sign-On (SSO) federato di Azure AD, seguire questa procedura:

1. Registrare l'app nel Marketplace commerciale.
1. Sviluppare l'accesso Single Sign-On con Azure AD usando [OAuth 2.0](../active-directory/azuread-dev/v1-protocols-oauth-code.md) o [OpenID Connect](../active-directory/azuread-dev/v1-protocols-openid-connect-code.md).
1. Abilitare la funzionalità per il supporto multi-tenancy in Azure AD per fornire un'esperienza di valutazione con un solo clic.

### <a name="single-tenant-solutions"></a>Soluzioni a tenant singolo

Usare Azure AD per supportare una delle azioni seguenti:

- Aggiungere utenti guest alla directory tramite [Azure AD B2B](../active-directory/b2b/what-is-b2b.md).
- Configurare manualmente le versioni di valutazione per i clienti mediante l'opzione di pubblicazione **Contattami**.
- Sviluppare una test drive per ogni cliente.
- Creare un'app demo di esempio multi-tenant che usa SSO.

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, 

- [Informazioni sul Marketplace](https://azuremarketplace.microsoft.com/sell) commerciale.

Per eseguire la registrazione nel Centro per i partner, iniziare a creare una nuova offerta o a lavorare su una esistente:

- [Accedere al Centro per i partner](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per creare o completare l'offerta.
