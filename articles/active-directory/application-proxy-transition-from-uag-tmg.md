---
title: Eseguire l'aggiornamento al proxy di applicazione di Azure AD | Microsoft Docs
description: Scegliere la soluzione del proxy migliore se si esegue l'aggiornamento da Microsoft Forefront o Unified Access Gateway.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 6c9f70493155de6989b26fd4e8bcf1dff01c835c
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---
# <a name="compare-remote-access-solutions"></a>Confrontare le soluzioni di accesso remoto

Il proxy dell'applicazione di Azure Active Directory è una delle due soluzioni di accesso remoto offerte da Microsoft. L'altra è il Proxy applicazione Web, la versione locale. Queste due soluzioni sostituiscono i prodotti precedenti offerti da Microsoft: Microsoft Forefront Threat Management Gateway (TMG) e Unified Access Gateway (UAG). Usare questo articolo per comprendere come vengono confrontate tra loro queste quattro soluzioni. Se si continuano a usare le soluzioni TMG o UAG deprecate, usare questo articolo per pianificare la migrazione a uno dei proxy dell'applicazione. 


## <a name="feature-comparison"></a>Confronto tra le funzionalità

Usare questa tabella per comprendere come vengono confrontate tra loro Threat Management Gateway (TMG), Unified Access Gateway (UAG), Proxy applicazione Web (WAP) e Proxy dell'applicazione AD Azure (AP).

| Funzionalità | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Autenticazione del certificato | Sì | Sì | - | - |
| Pubblicare in modo selettivo le app del browser | Sì | Sì | Sì | Sì |
| Preautenticazione e Single Sign-On | Sì | Sì | Sì | Sì | 
| Firewall di livello 2/3 | Sì | Sì | - | - |
| Funzionalità proxy di inoltro | Sì | - | - | - |
| Funzionalità VPN | Sì | Sì | - | - |
| Supporto dei protocolli rich | - | Sì | Sì, se in esecuzione su HTTP | Sì, se in esecuzione su HTTP o tramite Gateway Desktop remoto |
| Funge da server proxy ADFS | - | Sì | Sì | - |
| Un portale per l'accesso all'applicazione | - | Sì | - | Sì |
| Conversione dei collegamenti al corpo della risposta | Sì | Sì | - | Sì | 
| Autenticazione con intestazioni | - | Sì | - | Sì, con PingAccess | 
| Sicurezza a livello di cloud | - | - | - | Sì | 
| Accesso condizionale | - | Sì | - | Sì |
| Nessun componente all'interno della zona demilitarizzata (DMZ) | - | - | - | Sì |
| Nessuna connessione in ingresso | - | - | - | Sì |

Per la maggior parte degli scenari, è consigliabile l'applicazione Azure AD come soluzione moderna. Il Proxy applicazione Web è preferito esclusivamente in scenari che richiedono un server proxy per il servizio federativo AD e non è possibile usare domini personalizzati in Azure Active Directory. 

Il proxy dell'applicazione Azure AD offre vantaggi esclusivi rispetto ai prodotti simili, tra cui:

- Estensione di Azure AD a risorse locali
   - Sicurezza e protezione a livello di scalabilità
   - Funzionalità quali l'accesso condizionale e l'autenticazione a più fattori sono facili da abilitare
- Nessun componente nell'area demilitarizzata
- Nessuna connessione in ingresso necessaria
- Un pannello di accesso che gli utenti possono visualizzare per tutte le applicazioni, comprese Office 365, app SaaS integrate di Azure AD e app Web locali. 


## <a name="next-steps"></a>Passaggi successivi

- [Come fornire l'accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md)
- [Transition from Forefront TMG and UAG to Application Proxy](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/) (Eseguire la transizione da Forefront TMG e UAG al proxy dell'applicazione).

