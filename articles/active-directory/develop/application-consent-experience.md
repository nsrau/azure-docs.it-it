---
title: Informazioni sulle esperienze di consenso per le applicazioni di Azure AD | Microsoft Docs
description: Altre informazioni sulle esperienze di consenso di Azure AD per sapere come sfruttarne le potenzialità per la gestione e lo sviluppo di applicazioni in Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: celested
ms.reviewer: zachowd
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7de892143e1c69953cc60429ea0d24df194f0df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300289"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Informazioni sulle esperienze di consenso per le applicazioni di Azure AD

Approfondendo il concetto di esperienza di consenso utente per le applicazioni di Azure Active Directory (Azure AD), è possibile gestire in modo intelligente le applicazioni dell'organizzazione e/o sviluppare applicazioni con un'esperienza di consenso più semplice.

## <a name="consent-and-permissions"></a>Consenso e autorizzazioni

Il consenso è il processo con cui un utente autorizza un'applicazione ad accedere per proprio conto a risorse protette. A un amministratore o un utente può essere chiesto il consenso per permettere l'accesso ai dati individuali o a quelli dell'organizzazione.

L'esperienza utente effettiva per la concessione del consenso può variare a seconda dei criteri impostati per il tenant dell'utente, dell'ambito di autorità (o ruolo) dell'utente e del tipo di [autorizzazioni](https://docs.microsoft.com/azure/active-directory/develop/active-directory-permissions) richieste dall'applicazione client. In altre parole, l'esperienza di consenso ricade in parte sotto il controllo degli sviluppatori di applicazioni e degli amministratori del tenant. Gli amministratori hanno la possibilità di impostare e disabilitare i criteri per un tenant o un'app per controllare l'esperienza di consenso nel tenant. Gli sviluppatori di applicazioni possono stabilire quali tipi di autorizzazioni sono richieste e se vogliono fornire agli utenti informazioni dettagliate attraverso il flusso di consenso utente o il flusso di consenso amministratore.

- Il **flusso di consenso utente** è quello per cui uno sviluppatore di applicazioni indirizza gli utenti all'endpoint di autorizzazione allo scopo di registrare il consenso per il solo utente corrente.
- Il **flusso di consenso amministratore** è quello per cui uno sviluppatore di applicazioni indirizza gli utenti all'endpoint di consenso amministratore allo scopo di registrare il consenso per l'intero tenant. Per garantire il corretto funzionamento del flusso di consenso amministratore, gli sviluppatori di applicazioni devono inserire l'elenco di tutte le autorizzazioni nella proprietà `RequiredResourceAccess` del manifesto dell'applicazione. Per altre informazioni, vedere [Manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest).

## <a name="building-blocks-of-the-consent-prompt"></a>Blocchi predefiniti della richiesta di consenso

La richiesta di consenso è stata pensata per garantire che gli utenti abbiano a disposizione informazioni sufficienti per determinare l'affidabilità dell'applicazione client che accede a risorse protette per loro conto. La comprensione dei blocchi predefiniti aiuterà gli utenti che concedono il consenso a prendere decisioni più informate e gli sviluppatori a creare esperienze utente più efficaci.

La figura e la tabella seguenti includono informazioni sui blocchi predefiniti della richiesta di consenso.

![Blocchi predefiniti della richiesta di consenso](./media/application-consent-experience/consent_prompt.png)

| # | Componente | Scopo |
| ----- | ----- | ----- |
| 1 | ID utente | Questo ID rappresenta l'utente per conto del quale l'applicazione client sta richiedendo di accedere a risorse protette. |
| 2 | Title | Il titolo varia a seconda che gli utenti siano inseriti nel flusso di consenso utente o nel flusso di consenso amministratore. Nel flusso di consenso utente il titolo sarà "Autorizzazioni richieste", mentre nel flusso di consenso amministratore conterrà la riga aggiuntiva "Accetta per l'organizzazione". |
| 3 | Logo dell'app | Questa immagine deve consentire agli utenti di avere un'indicazione visiva se l'app in questione è quella a cui intendevano accedere. L'immagine viene fornita dagli sviluppatori dell'applicazione e la relativa proprietà non è convalidata. |
| 4 | Nome app | Questo valore deve comunicare agli utenti il nome dell'applicazione che sta richiedendo l'accesso ai dati. Si noti che il nome dell'app viene fornito dagli sviluppatori e la relativa proprietà non è convalidata. |
| 5 | Dominio dell'entità di pubblicazione | Questo valore deve fornire agli utenti un dominio di cui possano essere in grado di valutare il livello di affidabilità. Il dominio dell'entità di pubblicazione viene fornito dagli sviluppatori e la relativa proprietà è convalidata. |
| 6 | Autorizzazioni | Questo elenco contiene le autorizzazioni richieste dall'applicazione client. Gli utenti devono sempre valutare i tipi di autorizzazioni richieste per comprendere, in caso di accettazione, a quali dati l'applicazione client sarà autorizzata ad accedere per loro conto. Si consiglia agli sviluppatori di applicazioni di richiedere l'accesso alle autorizzazioni con privilegi minimi. |
| 7 | Descrizione dell'autorizzazione | Questo valore viene fornito dal servizio che espone le autorizzazioni. Per visualizzare le descrizioni delle autorizzazioni, è necessario attivare o disattivare la freccia di espansione accanto all'autorizzazione. |
| 8 | Condizioni dell'app | Queste condizioni includono collegamenti alle condizioni d'uso e all'informativa sulla privacy dell'applicazione. L'entità di pubblicazione è responsabile della definizione delle regole nelle condizioni d'uso. Inoltre, deve dichiarare nell'informativa sulla privacy in che modo intende usare e condividere i dati utente. Se l'entità di pubblicazione non fornisce i collegamenti a questi valori per le applicazioni multi-tenant, verrà visualizzato un avviso in grassetto nella richiesta di consenso. |
| 9 | https://myapps.microsoft.com | Questo collegamento consente agli utenti di controllare e rimuovere le applicazioni non Microsoft che attualmente hanno accesso ai dati. |

## <a name="common-consent-scenarios"></a>Scenari di consenso comuni

Di seguito sono illustrate le esperienze di consenso che un utente può visualizzare negli scenari di consenso comuni:

1. Utenti singoli che accedono a un'app che li indirizza al flusso di consenso utente richiedendo un set di autorizzazioni incluso nel loro ambito di autorità.
    
    1. Nella richiesta di consenso tradizionale gli amministratori visualizzeranno un controllo aggiuntivo che permetterà loro di acconsentire per conto dell'intero tenant. Per impostazione predefinita, il controllo sarà disattivato. Solo quando gli amministratori selezionano la casella verrà concesso il consenso per conto dell'intero tenant. Ad oggi questa casella di controllo viene visualizzata solo per il ruolo di amministratore globale, non per i ruoli di amministratore del cloud e amministratore dell'app.

        ![Richiesta di consenso per lo scenario 1a](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Gli utenti visualizzeranno la richiesta di consenso tradizionale.

        ![Richiesta di consenso per lo scenario 1b](./media/application-consent-experience/consent_prompt_1b.png)

2. Utenti singoli che accedono a un'app che richiede almeno un'autorizzazione non compresa nel loro ambito di autorità.
    1. Gli amministratori visualizzeranno la stessa richiesta illustrata in precedenza al punto 1.i.
    2. Gli utenti non potranno concedere il consenso all'applicazione e saranno invitati a chiedere all'amministratore di concedere l'accesso all'app. 
                
        ![Richiesta di consenso per lo scenario 1b](./media/application-consent-experience/consent_prompt_2b.png)

3. Utenti singoli che si spostano o vengono indirizzati verso il flusso di consenso amministratore.
    1. Gli utenti amministratore visualizzeranno la richiesta di consenso amministratore. In questa richiesta il titolo e le descrizioni delle autorizzazioni risultano modificati. Le modifiche evidenziano il fatto che l'accettazione di questa richiesta concederà all'app l'accesso ai dati richiesti per conto dell'intero tenant.
        
        ![Richiesta di consenso per lo scenario 1b](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Gli utenti non amministratore visualizzeranno la stessa schermata illustrata in precedenza al punto 2.ii.

## <a name="next-steps"></a>Passaggi successivi
- Panoramica dettagliata di [come il framework di consenso di Azure AD implementa il consenso](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Per informazioni più approfondite, scoprire [come un'applicazione multi-tenant può usare il framework di consenso](active-directory-devhowto-multi-tenant-overview.md) per implementare il consenso "user" e "admin", che supporta più modelli di applicazione avanzati a più livelli.
- Scopri [come configurare il dominio di pubblicazione dell'app](howto-configure-publisher-domain.md).
