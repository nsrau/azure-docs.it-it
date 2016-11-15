---
title: Confronto tra Azure MFA nel cloud e Azure MFA Server| Microsoft Docs
description: "Scegliere la soluzione di sicurezza Multi-Factor Authentication adatta alle proprie esigenze ponendosi i relativi quesiti.  Scegliere quindi cloud, Server di autenticazione a più fattori o AD FS."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: ec2270ea-13d7-4ebc-8a00-fa75ce6c746d
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/14/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 72347099d980f2ca73f39f984787197e1f87e45a


---
# <a name="choose-the-azure-multifactor-authentication-solution-for-you"></a>Scegliere la soluzione Azure Multi-Factor Authentication più adatta alle proprie esigenze
Esistono diverse versioni di Azure Multi-Factor Authentication (MFA) ed è necessario rispondere ad alcune domande per individuare la versione corretta da usare.  Le domande sono:

* [Cosa si sta tentando di proteggere?](#what-am-i-trying-to-secure)
* [Dove si trovano gli utenti?](#where-are-the-users-located)
* [Quali funzionalità sono necessarie?](#what-featured-do-i-need)

Le sezioni seguenti contengono istruzioni per determinare ciascuna risposta.

## <a name="what-am-i-trying-to-secure"></a>Cosa si sta tentando di proteggere?
Per individuare la soluzione di verifica in due passaggi corretta, è necessario prima stabilire ciò che si sta tentando di proteggere con un secondo metodo di autenticazione.  Si tratta di un'applicazione in Azure?  Oppure di un sistema di accesso remoto?  La determinazione degli elementi da proteggere consente di stabilire dove abilitare la modalità Multi-Factor Authentication.  

| Cosa si intende proteggere | Multi-Factor Authentication nel cloud | Server Multi-Factor Authentication |
| --- |:---:|:---:|
| App prodotte direttamente da Microsoft |● |● |
| App SaaS nella Raccolta di app |● |● |
| Le applicazioni IIS pubblicate tramite proxy app per Azure AD |● |● |
| Le applicazioni IIS non pubblicate tramite proxy app per Azure AD | |● |
| Accesso remoto, ad esempio VPN, RDG | |● |

## <a name="where-are-the-users-located"></a>Dove si trovano gli utenti?
Successivamente, a seconda di dove si trovano gli utenti, è possibile determinare la soluzione corretta da usare: nel cloud o in locale con il server MFA.

| Ubicazione degli utenti | Multi-Factor Authentication nel cloud | Server Multi-Factor Authentication |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD e AD locale usando la federazione con AD FS |● |● |
| Azure AD e AD locale con DirSync, Azure AD Sync, Azure AD Connect, senza sincronizzazione di password |● |● |
| Azure AD e AD locale con DirSync, Azure AD Sync, Azure AD Connect, con sincronizzazione di password |● | |
| Active Directory locale | |● |

## <a name="what-features-do-i-need"></a>Quali funzionalità sono necessarie?
La tabella seguente confronta le funzionalità disponibili nella soluzione Multi-Factor Authentication nel cloud e nella soluzione server Multi-Factor Authentication.

| Multi-Factor Authentication nel cloud | Server Multi-Factor Authentication |
| --- |:---:|:---:|
| Notifica dell'app per dispositivi mobili come secondo fattore |● |
| Codice di verifica dell'app per dispositivi mobili come secondo fattore |● |
| Chiamata telefonica come secondo fattore |● |
| SMS unidirezionale come secondo fattore |● |
| SMS bidirezionale come secondo fattore | |
| Token hardware come secondo fattore | |
| Password di app per i client che non supportano MFA |● |
| Controllo amministrazione sui metodi di autenticazione |● |
| Modalità PIN | |
| Avviso di illecito |● |
| Report MFA |● |
| Bypass monouso | |
| Messaggi di saluto personalizzati per le telefonate |● |
| ID chiamante personalizzabile per le telefonate |● |
| IP attendibili |● |
| Memorizzazione di MFA per dispositivi attendibili |● |
| Accesso condizionale |● |
| Cache | |

Dopo aver stabilito se è necessario utilizzare la modalità Multi-Factor Authentication nel cloud o il server MFA locale, sarà possibile iniziare a impostare e utilizzare Azure Multi-Factor Authentication. **Selezionare l'icona che rappresenta lo scenario.**

<center>




[![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>



<!--HONumber=Nov16_HO2-->


