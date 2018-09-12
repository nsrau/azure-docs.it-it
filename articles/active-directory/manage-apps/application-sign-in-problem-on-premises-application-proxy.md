---
title: Problemi durante l'accesso a un'applicazione locale tramite il proxy di applicazione di Azure AD | Microsoft Docs
description: Risoluzione dei problemi comuni riscontrati quando non si è in grado di accedere a un'applicazione locale integrata con Azure AD tramite il proxy di applicazione di Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 6f34945750d8f37a175e0b500a0596de08cf8ce4
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44355852"
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-ad-application-proxy"></a>Problemi durante l'accesso a un'applicazione locale tramite il proxy di applicazione di Azure AD

Se si riscontrano problemi durante l'accesso a un'applicazione locale, è possibile provare a risolverli tramite le procedure seguenti.

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>È possibile caricare l'applicazione, ma sembrano essere presenti problemi relativi alla pagina

I documenti seguenti possono aiutare a risolvere alcuni dei problemi più comuni in questa categoria.

  * [I can get to my application, but the application page isn't displaying correctly](application-proxy-page-appearance-broken-problem.md) (La pagina dell'applicazione non viene visualizzata correttamente)
  * [I can get to my application, but the application page isn't displaying correctly](application-proxy-page-load-speed-problem.md) (Il caricamento dell'applicazione impiega troppo tempo)
  * [I can get to my application, but the links on the application page do not work](application-proxy-page-links-broken-problem.md) (I collegamenti nella pagina dell'applicazione non funzionano)

## <a name="im-having-a-connectivity-problem-my-application"></a>Si è verificato un problema di connettività dell'applicazione
  I documenti seguenti possono aiutare a risolvere alcuni dei problemi più comuni in questa categoria.
  * [I don't know what ports to open for my application](application-proxy-connectivity-ports-how-to.md) (Quali porte aprire per l'applicazione)
  * [I encountered a problem because there was no working connector in a connector group for my application](application-proxy-connectivity-no-working-connector.md) (Un gruppo di connettori non contiene alcun connettore funzionante per l'applicazione)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>Si è verificato un problema di configurazione del proxy di applicazione di Azure AD nel portale di amministrazione
  I documenti seguenti possono aiutare a risolvere alcuni dei problemi più comuni in questa categoria.
  * [I am having difficulty configuring an application Proxy application](application-proxy-config-how-to.md) (Come configurare un'applicazione proxy di applicazione)
  * [I don't know how to configure single sign-on to my application Proxy application](application-proxy-config-sso-how-to.md) (Come configurare Single Sign-On nell'applicazione proxy di applicazione)
  * [I encountered a problem when creating my application in admin portal](application-proxy-config-problem.md) (Problema di creazione dell'applicazione nel portale di amministrazione)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>Si è verificato un problema di configurazione dell'autenticazione back-end nell'applicazione
  I documenti seguenti possono aiutare a risolvere alcuni dei problemi più comuni in questa categoria.
  * [I don't know how to configure Kerberos Constrained Delegation](application-proxy-back-end-kerberos-constrained-delegation-how-to.md) (Come configurare la delega vincolata Kerberos)
  * [I don't know how to configure my application with PingAccess](application-proxy-back-end-ping-access-how-to.md) (Come configurare l'applicazione con PingAccess)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>Si è verificato un problema di accesso all'applicazione
  I documenti seguenti possono aiutare a risolvere alcuni dei problemi più comuni in questa categoria.
  * [I get a "Can't Access this Corporate Application" error](application-proxy-sign-in-bad-gateway-timeout-error.md) (Errore di accesso non riuscito all'applicazione aziendale)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>Si è verificato un problema con il connettore dell'agente proxy di applicazione
  I documenti seguenti possono aiutare a risolvere alcuni dei problemi più comuni in questa categoria.
  * [I am having issues installing the Application Proxy Agent Connector](application-proxy-connector-installation-problem.md) (Problemi di installazione del connettore dell'agente proxy di applicazione)

## <a name="next-steps"></a>Passaggi successivi
[Come fornire l'accesso remoto sicuro alle applicazioni locali](application-proxy.md)
