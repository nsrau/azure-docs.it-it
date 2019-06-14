---
title: Eseguire l'aggiornamento al proxy di applicazione di Azure AD | Microsoft Docs
description: Scegliere la soluzione del proxy migliore se si esegue l'aggiornamento da Microsoft Forefront o Unified Access Gateway.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4790dc7ebeeee3407e89bcf38d7e3f25699ed328
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108419"
---
# <a name="compare-remote-access-solutions"></a>Confrontare le soluzioni di accesso remoto

Il proxy dell'applicazione di Azure Active Directory è una delle due soluzioni di accesso remoto offerte da Microsoft. L'altra è il Proxy applicazione Web, la versione locale. Queste due soluzioni sostituiscono i prodotti precedenti offerti da Microsoft: Microsoft Forefront Threat Management Gateway (TMG) e Unified Access Gateway (UAG). Usare questo articolo per comprendere come vengono confrontate tra loro queste quattro soluzioni. Se si continuano a usare le soluzioni TMG o UAG deprecate, usare questo articolo per pianificare la migrazione a uno dei proxy dell'applicazione. 


## <a name="feature-comparison"></a>Confronto tra le funzionalità

Usare questa tabella per comprendere come vengono confrontate tra loro Threat Management Gateway (TMG), Unified Access Gateway (UAG), Proxy applicazione Web (WAP) e Proxy dell'applicazione AD Azure (AP).

| Funzionalità | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Autenticazione del certificato | Yes | Yes | - | - |
| Pubblicare in modo selettivo le app del browser | Yes | Sì | Sì | Yes |
| Preautenticazione e Single Sign-On | Yes | Sì | Sì | Yes | 
| Firewall di livello 2/3 | Yes | Yes | - | - |
| Funzionalità proxy di inoltro | Yes | - | - | - |
| Funzionalità VPN | Yes | Yes | - | - |
| Supporto dei protocolli rich | - | Yes | Sì, se in esecuzione su HTTP | Sì, se in esecuzione su HTTP o tramite Gateway Desktop remoto |
| Funge da server proxy ADFS | - | Yes | Yes | - |
| Un portale per l'accesso all'applicazione | - | Yes | - | Yes |
| Conversione dei collegamenti al corpo della risposta | Yes | Sì | - | Yes | 
| Autenticazione con intestazioni | - | Yes | - | Sì, con PingAccess | 
| Sicurezza a livello di cloud | - | - | - | Yes | 
| Accesso condizionale | - | Yes | - | Sì |
| Nessun componente all'interno della rete perimetrale | - | - | - | Sì |
| Nessuna connessione in ingresso | - | - | - | Yes |

Per la maggior parte degli scenari, si consiglia di Proxy applicazione Azure AD come soluzione moderna. Il Proxy applicazione Web è preferito esclusivamente in scenari che richiedono un server proxy per il servizio federativo AD e non è possibile usare domini personalizzati in Azure Active Directory. 

Il proxy dell'applicazione Azure AD offre vantaggi esclusivi rispetto ai prodotti simili, tra cui:

- Estensione di Azure AD a risorse locali
   - Sicurezza e protezione a livello di scalabilità
   - Sono facili da abilitare funzionalità quali l'accesso condizionale e multi-Factor Authentication
- Nessun componente all'interno della rete perimetrale
- Nessuna connessione in ingresso necessaria
- Un pannello di accesso che gli utenti possono visualizzare per tutte le applicazioni, comprese Office 365, app SaaS integrate di Azure AD e app Web locali. 


## <a name="next-steps"></a>Passaggi successivi

- [Come fornire l'accesso remoto sicuro alle applicazioni locali](application-proxy.md)
