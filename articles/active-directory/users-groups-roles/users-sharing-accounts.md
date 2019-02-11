---
title: Condivisione di account e credenziali - Azure Active Directory | Microsoft Docs
description: Descrive come Azure Active Directory consente alle organizzazioni di condividere in modo sicuro gli account per le app locali e i servizi cloud di livello consumer.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 5e9e5844d80e9f3fce0edb90dae210d3e16fee61
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55509491"
---
# <a name="sharing-accounts-with-azure-ad"></a>Condivisione di account con Azure AD
## <a name="overview"></a>Panoramica
Le organizzazioni devono usare un singolo nome e una singola password per più utenti nei due casi descritti di seguito:

* Quando si accede alle applicazioni che richiedono account di accesso e password univoci per ogni utente, in caso di app locali o servizi cloud di livello consumer (ad esempio, gli account aziendali di social media).
* Quando si creano ambienti multiutente. Potrebbe essere presente un singolo account locale che dispone di privilegi elevati che viene usato per attività di base, ad esempio installazione, amministrazione e ripristino. Ad esempio, l'account locale "amministratore globale" per Office 365 o l'account radice in Salesforce.

In genere, questi account possono essere condivisi distribuendo le credenziali (nome utente/password) agli individui appropriati o archiviandoli in un percorso condiviso a cui possono accedere più agenti attendibili.

Il modello di condivisione tradizionale presenta vari svantaggi:

* L'abilitazione dell'accesso alle nuove applicazioni richiede di distribuire le credenziali a tutti gli utenti che necessitano dell'accesso.
* Ogni applicazione condivisa potrebbe richiedere un proprio set univoco di credenziali condivise, che comporta la necessità da parte degli utenti di memorizzare più set di credenziali. Quando gli utenti devono ricordare molte credenziali, aumenta il rischio che incorrano in attività rischiose (ad esempio annotare le password).
* Non è possibile individuare gli utenti che dispongono dell'accesso a un'applicazione.
* Non è possibile individuare gli utenti che hanno *effettuato l'accesso* a un'applicazione.
* Per rimuovere l'accesso a un'applicazione, è necessario aggiornare le credenziali e distribuirle nuovamente a tutti gli utenti che richiedono l'accesso a tale applicazione.

## <a name="azure-active-directory-account-sharing"></a>Condivisione account di Azure Active Directory
Azure AD fornisce un nuovo approccio all'uso degli account condivisi che consente di eliminare questi svantaggi.

L'amministratore di Azure AD consente di configurare le applicazioni alle quali un utente può accedere usando il pannello di accesso e scegliendo il tipo di accesso Single Sign-On più adatto all'applicazione. Uno di questi tipi, *l'accesso Single Sign-On basato su password*, consente ad Azure AD di agire come una sorta di broker durante il processo di accesso a tale app.

Gli utenti accedono una volta con l'account aziendale. Si tratta dello stesso account che usano regolarmente per accedere al desktop o alla posta elettronica. Possono individuare e accedere solo alle applicazioni a cui sono assegnati. Con gli account condivisi, questo elenco di applicazioni può includere qualsiasi numero di credenziali condivise. L'utente finale non deve ricordare o annotare i vari account in uso.

Gli account condivisi non solo consentono di aumentare la supervisione e migliorare l'usabilità, ma anche di aumentare la sicurezza. Gli utenti con autorizzazioni per l'uso delle credenziali non visualizzano la password condivisa, ma ottengono autorizzazioni per l'uso della password come parte di un flusso di autenticazione orchestrato. Inoltre, alcune applicazioni con accesso Single Sign-On con password offrono la possibilità di usare Azure AD per eseguire il rollover periodico (aggiornamento) delle password. Il sistema usa le password complesse di grandi dimensioni e ciò incrementa la sicurezza dell'account. L'amministratore può facilmente concedere o revocare l'accesso a un'applicazione e può sapere chi può accedere all'account e chi ha avuto accesso in precedenza.

Azure AD supporta gli account condivisi per gli utenti con licenza Enterprise Mobility Suite (EMS), Premium o Basic, in tutti i tipi di applicazioni con accesso Single Sign-On basato su password. È possibile condividere gli account per una qualsiasi delle numerose applicazioni già integrate nella raccolta e integrare la propria applicazione con autenticazione tramite password in [app personalizzate con accesso Single Sign-On](../manage-apps/configure-single-sign-on-portal.md).

Le funzionalità di Azure AD che consentono la condivisione di account includono:

* [Password Single Sign-On](../manage-apps/what-is-single-sign-on.md#password-based-sso)
* Agente di password Single Sign-On
* [Assegnazione di gruppi](groups-self-service-management.md)
* App personalizzate basate su password
* [Dashboard/report sull'utilizzo di app](../active-directory-passwords-get-insights.md)
* Portali di accesso dell'utente finale
* [Proxy di app](../manage-apps/application-proxy.md)
* [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Condivisione di un account
Per usare Azure AD per la condivisione di un account è necessario:

* Aggiungere un'applicazione alla [raccolta di app](https://azure.microsoft.com/marketplace/active-directory/) o integrarla con un'[applicazione personalizzata](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* Configurare l'applicazione per l'accesso Single Sign-On basato su password
* Usare un' [assegnazione basata su gruppi](groups-saasapps.md) e selezionare l'opzione per immettere le credenziali condivise
* Facoltativo: in alcune applicazioni, ad esempio Facebook, Twitter o LinkedIn, è possibile abilitare l'opzione per l' [esecuzione automatizzata del rollover delle password di Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/)

Con Azure AD è possibile rendere più sicuro l'account condiviso tramite Multi-Factor Authentication (MFA) (vedere altre informazioni sulla [sicurezza delle applicazioni con Azure AD](../authentication/concept-mfa-whichversion.md)) ed è possibile delegare la capacità di gestire chi può accedere all'applicazione tramite la gestione dei gruppi in [modalità self-service di Azure AD](groups-self-service-management.md).

## <a name="related-articles"></a>Articoli correlati
* [Gestione di applicazioni con Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Sicurezza delle app con l'accesso condizionale](../active-directory-conditional-access-azure-portal.md)
* [Gestione di gruppi self-service/SSAA](groups-self-service-management.md)
