---
title: 'Azure AD Connect: Autenticazione pass-through -Aggiornare gli agenti di autenticazione di anteprima | Microsoft Docs'
description: Questo articolo descrive come aggiornare la configurazione dell'autenticazione pass-through di Azure Active Directory (Azure AD).
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: billmath
ms.openlocfilehash: 0a7293f2b3a366b25e780ee75601dfbb2b35ddaa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Autenticazione pass-through di Azure Active Directory - Aggiornare gli agenti di autenticazione di anteprima

## <a name="overview"></a>Panoramica

Questo articolo è destinato ai clienti che usano l'autenticazione pass-through di Azure AD tramite l'anteprima. Abbiamo recentemente aggiornato e personalizzato il software dell'agente di autenticazione. È necessario aggiornare _manualmente_ gli agenti di autenticazione di anteprima installati nei server locali. L'aggiornamento manuale è un'azione da eseguire una sola volta. Tutti gli aggiornamenti futuri degli agenti di autenticazione sono automatici. I motivi per eseguire l'aggiornamento sono i seguenti:

- Le versioni di anteprima degli agenti di autenticazione non riceveranno altre correzioni di bug o della sicurezza.
-   Non è possibile installare le versioni di anteprima degli agenti di autenticazione nei server aggiuntivi, per la disponibilità elevata.

## <a name="check-versions-of-your-authentication-agents"></a>Controllare le versioni degli agenti autenticazione

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Passaggio 1: Controllare se gli agenti di autenticazione sono installati

Eseguire la procedura seguente per verificare se gli agenti di autenticazione sono installati:

1. Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con le credenziali di amministratore globale del tenant.
2. Selezionare **Azure Active Directory** nell'opzione di spostamento a sinistra.
3. Selezionare **Azure AD Connect**. 
4. Selezionare **Autenticazione pass-through**. Questo pannello elenca i server sono installati gli agenti di autenticazione.

![Interfaccia di amministrazione di Azure Active Directory - Pannello Autenticazione pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Passaggio 2: Controllare le versioni degli agenti autenticazione

Per controllare le versioni degli agenti di autenticazione, in ogni server identificato nel passaggio precedente seguire queste istruzioni:

1. Passare a **Pannello di controllo -> Programmi -> Programmi e funzionalità** nel server locale.
2. Se è presente una voce per "**Agente di autenticazione di Microsoft Azure AD Connect**", non è necessario eseguire alcuna azione su questo server.
3. Se è presente una voce per "**Connettore proxy dell'applicazione Microsoft Azure AD**", versioni 1.5.132.0 o precedenti, è necessario eseguire manualmente l'aggiornamento in questo server.

![Versione di anteprima dell'agente di autenticazione](./media/active-directory-aadconnect-pass-through-authentication/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Procedure consigliate da seguire prima di iniziare l'aggiornamento

Prima dell'aggiornamento, assicurarsi di disporre degli elementi seguenti:

1. **Creare un account di amministratore globale solo cloud**: non effettuare l'aggiornamento senza disporre di un account di amministratore globale solo cloud da usare in situazioni di emergenza, in cui gli agenti di autenticazione pass-through non funzionano correttamente. Informazioni su come [aggiungere un account amministratore globale di tipo solo cloud](../active-directory-users-create-azure-portal.md). L'esecuzione di questo passaggio è fondamentale ed evita di rimanere bloccati fuori dal tenant.
2.  **Verificare la disponibilità elevata**: se l'operazione non è stata completata in precedenza, installare un secondo agente di autenticazione autonomo per garantire disponibilità elevata per le richieste di accesso, usando queste [istruzioni](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Aggiornamento dell'agente di autenticazione nel server di Azure AD Connect

Prima di aggiornare l'agente di autenticazione nello stesso server, è necessario aggiornare Azure AD Connect. Eseguire la procedura seguente nei server primario e di gestione temporanea di Azure AD Connect:

1. **Aggiornare Azure AD Connect**: seguire questo [articolo](./active-directory-aadconnect-upgrade-previous-version.md) e aggiornare alla versione più recente di Azure AD Connect.
2. **Disinstallare la versione di anteprima dell'agente di autenticazione**: scaricare [questo script di PowerShell](https://aka.ms/rmpreviewagent) ed eseguirlo come amministratore nel server.
3. **Scaricare la versione più recente dell'agente di autenticazione (versioni 1.5.193.0 o successive)**: accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con le credenziali di amministratore globale del tenant. Selezionare **Azure Active Directory -> Azure AD Connect -> Autenticazione pass-through -> Scarica agente**. Accettare le [condizioni del servizio](https://aka.ms/authagenteula) e scaricare la versione più recente dell'agente di autenticazione. È inoltre possibile scaricare l'agente di autenticazione da [qui](https://aka.ms/getauthagent).
4. **Installare la versione più recente dell'agente di autenticazione**: eseguire il file eseguibile scaricato nel passaggio 3. Fornire le credenziali di amministratore globale del tenant quando viene richiesto.
5. **Verificare che sia stata installata la versione più recente**: come illustrato in precedenza, passare a **Pannello di controllo -> Programmi -> Programmi e funzionalità** e verificare che sia presente una voce per "**Agente di autenticazione di Microsoft Azure AD Connect**".

>[!NOTE]
>Se si osserva il pannello Autenticazione pass-through nell'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) dopo aver completato i passaggi precedenti, si noteranno due voci dell'agente di autenticazione per ogni server, una che indica l'agente di autenticazione come **Attivo** e una che lo indica come **Inattivo**. Questo è il comportamento _previsto_. La voce **Inattivo** viene eliminata automaticamente dopo alcuni giorni.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Aggiornamento dell'agente di autenticazione su altri server

Eseguire la procedura seguente per aggiornare gli agenti di autenticazione su altri server (in cui Azure AD Connect non è installato):

1. **Disinstallare la versione di anteprima dell'agente di autenticazione**: scaricare [questo script di PowerShell](https://aka.ms/rmpreviewagent) ed eseguirlo come amministratore nel server.
2. **Scaricare la versione più recente dell'agente di autenticazione (versioni 1.5.193.0 o successive)**: accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con le credenziali di amministratore globale del tenant. Selezionare **Azure Active Directory -> Azure AD Connect -> Autenticazione pass-through -> Scarica agente**. Accettare le condizioni del servizio e scaricare la versione più recente.
3. **Installare la versione più recente dell'agente di autenticazione**: eseguire il file eseguibile scaricato nel passaggio 2. Fornire le credenziali di amministratore globale del tenant quando viene richiesto.
4. **Verificare che sia stata installata la versione più recente**: come illustrato in precedenza, passare a **Pannello di controllo -> Programmi -> Programmi e funzionalità** e verificare che sia presente una voce denominata **Agente di autenticazione di Microsoft Azure AD Connect**.

>[!NOTE]
>Se si osserva il pannello Autenticazione pass-through nell'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) dopo aver completato i passaggi precedenti, si noteranno due voci dell'agente di autenticazione per ogni server, una che indica l'agente di autenticazione come **Attivo** e una che lo indica come **Inattivo**. Questo è il comportamento _previsto_. La voce **Inattivo** viene eliminata automaticamente dopo alcuni giorni.

## <a name="next-steps"></a>Passaggi successivi
- [**Risoluzione dei problemi**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
