---
title: Guida tecnica alla pubblicazione di applicazioni SaaS in Azure Marketplace
description: Guida dettagliata ed elenchi di controllo per la pubblicazione di applicazioni SaaS in Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/28/2018
ms.author: pabutler
ms.openlocfilehash: 64becc80192e69bd332d6657637c845acf93748b
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="saas-applications-technical-publishing-guide"></a>Guida tecnica alla pubblicazione di applicazioni SaaS

La Guida tecnica alla pubblicazione di applicazioni SaaS in Azure Marketplace è stata pensata per aiutare gli editori potenziali e quelli già operativi a presentare i propri servizi e applicazioni in Azure Marketplace sfruttando l'offerta esistente di applicazioni SaaS.  
L'editore potrà ricorrere alle applicazioni SaaS esistenti quando distribuirà la soluzione nella propria sottoscrizione di Azure e i clienti effettueranno l'accesso tramite un'interfaccia progettata e gestita dall'editore per testare l'applicazione. Questa operazione viene eseguita usando [Azure Active Directory (Azure AD)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis) per sfruttare l'ambiente di prova esistente. In altre parole, si tratta di una versione di valutazione gratuita gestita dal cliente e ospitata da un partner. È fondamentale esporre la soluzione in modo che offra agli acquirenti di soluzioni cloud l'opportunità di sperimentare la soluzione in modo indipendente e a titolo gratuito, pertanto questo tipo di offerta fornisce un'esperienza di valutazione corrispondente al modo in cui i clienti cercano le soluzioni cloud.  

Per una panoramica di tutte le altre offerte in Marketplace, fare riferimento alla [Guida alla pubblicazione per Azure Marketplace](https://aka.ms/sellerguide).

## <a name="saas-application-technical-guidance"></a>Indicazioni tecniche per le applicazioni SaaS
I requisiti tecnici per le applicazioni SaaS sono semplici. È indispensabile che gli editori garantiscano l'integrazione con Azure AD per la pubblicazione.  L'integrazione di Azure AD con le applicazioni è documentata in modo dettagliato e Microsoft mette a disposizione una vasta gamma di risorse e SDK a tale scopo.  

Per iniziare, è consigliabile disporre di una sottoscrizione dedicata per la pubblicazione in Azure Marketplace, in modo da isolare questo tipo di attività da altre iniziative. Inoltre, se non sono già installati, si consiglia di installare gli strumenti seguenti nell'ambiente di sviluppo: 
- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)  
- [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-5.0.0)  
- [Strumenti di sviluppo di Azure (verificare gli strumenti disponibili)](https://azure.microsoft.com/tools/)  
- [Visual Studio Code](https://code.visualstudio.com/)  

### <a name="resources"></a>Risorse
Di seguito sono riportati i collegamenti alle risorse di Azure AD più indicate per iniziare: 

**Documentazione**

- [Guida per gli sviluppatori di Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide)

- [Integrazione con Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-how-to-integrate)

- [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

- [Roadmap per Azure - Sicurezza e identità](https://azure.microsoft.com/roadmap/?category=security-identity)

**Video**

- [Azure Active Directory Authentication with Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration) (Autenticazione di Azure Active Directory con Vittorio Bertocci)

- [Azure Active Directory Identity Technical Briefing - Part 1 of 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration) (Briefing tecnico sulle identità di Azure Active Directory - Parte 1 di 2)

- [Azure Active Directory Identity Technical Briefing - Part 2 of 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration) (Briefing tecnico sulle identità di Azure Active Directory - Parte 2 di 2)

- [Building Apps with Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration) (Compilazione di app con Microsoft Azure Active Directory)

- [Video di Microsoft Azure incentrati su Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

**Training**  
- [Microsoft Azure for IT Pros Content Series: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

**Aggiornamenti del servizio Azure Active Directory**  
- [Aggiornamenti del servizio Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

Per il supporto, è possibile usare le risorse seguenti:
- [Forum MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
- [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="the-azure-active-directory-gallery"></a>Raccolta di Azure Active Directory
Oltre a presentare la propria applicazione in Azure Marketplace/AppSource, è possibile presentarla anche nella raccolta di applicazioni Azure AD inclusa in Azure Marketplace/AppStore. I clienti che usano Azure AD come provider di identità possono trovare i diversi connettori di applicazioni SaaS pubblicati qui. Gli amministratori IT possono aggiungere i connettori dalla raccolta di app e quindi configurarli e usarli per l'accesso Single Sign-On (SSO) e il provisioning. Azure AD supporta tutti i principali protocolli di federazione per SSO, come SAML 2.0, OpenID Connect, OAuth e WS-Fed.  

Dopo avere verificato il funzionamento dell'integrazione dell'applicazione con Azure AD, inviare la richiesta per l'accesso nel portale di rete delle applicazioni. Se si dispone di un account Office 365, usarlo per accedere al portale. Se non si dispone di un account Office 365, è possibile usare l'account Microsoft (ad esempio Outlook o Hotmail) per eseguire l'accesso.

## <a name="program-benefits"></a>Vantaggi del programma
- Offre un'esperienza Single Sign-On ottimale per i clienti
- Configurazione minima e semplice dell'applicazione
- I clienti possono cercare l'applicazione e trovarla nella raccolta
- Tutti i clienti possono usare questa integrazione indipendentemente dallo SKU di Azure AD usato (Gratuito, Basic o Premium)
- Esercitazione dettagliata per la configurazione per i clienti reciproci
- Abilita il provisioning degli utenti per la stessa app, se si usa SCIM

## <a name="prerequisites"></a>prerequisiti
Per presentare un'applicazione nella raccolta di Azure AD, l'applicazione deve prima di tutto implementare uno dei protocolli di federazione supportati da Azure AD. Leggere le condizioni della raccolta di applicazioni Azure AD disponibili nelle [Informazioni legali su Microsoft Azure](https://azure.microsoft.com/support/legal/).  

Di seguito sono riportate altre informazioni sui prerequisiti a seconda del protocollo in uso:

**OpenID Connect**: creare l'applicazione multi-tenant in Azure AD e implementare il framework di consenso per l'applicazione. Inviare la richiesta di accesso all'endpoint comune, in modo che qualsiasi cliente possa fornire il consenso all'applicazione. È possibile controllare l'accesso utente dei clienti in base all'ID del tenant e all'UPN dell'utente ricevuti nel token.  
**SAML 2.0 o WS-Fed**: l'applicazione deve supportare l'integrazione SSO SAML o WS-Fed in modalità SP o IdP.
