---
title: 'Autenticazione pass-through di Azure AD: cronologia delle versioni Documenti Microsoft'
description: Questo articolo elenca tutte le versioni dell'agente di autenticazione pass-through di Azure ADThis article lists all releases of the Azure AD Pass-through Authentication agent
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 11/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c5d0efe8e662544dc69356c6b17dd7eca6f3a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786453"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Agente di autenticazione pass-through Azure AD: cronologia delle versioniAzure AD Pass-through Authentication agent: Version release history 
 
Gli agenti installati in locale che abilitano l'autenticazione pass-through vengono aggiornati regolarmente per fornire nuove funzionalità. In questo articolo vengono elencate le versioni e le funzionalità aggiunte quando vengono introdotte nuove funzionalità. Gli agenti di autenticazione pass-through vengono aggiornati automaticamente quando viene rilasciata una nuova versione. 

Di seguito sono riportati gli argomenti correlati: 

- [Accesso utente con l'autenticazione pass-through di Azure ADUser sign-in with Azure AD Pass-through Authentication](how-to-connect-pta.md) 
- [Installazione dell'agente di autenticazione pass-through di Azure ADAzure AD Pass-through Authentication agent installation](how-to-connect-pta-quick-start.md) 



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Stato della versione 
22/01/2019: Rilasciato per il download  
### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità 
- Aggiunto il supporto per i canali affidabili del bus di servizio per aggiungere un altro livello di resilienza della connessione per le connessioni in uscita 
- Applicare TLS 1.2 durante la registrazione dell'agenteEnforce TLS 1.2 during agent registration 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Stato della versione 
10/04/2018: Rilasciato per il download  
### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità 
- Supporto della connessione tramite socket Web 
- Impostare TLS 1.2 come protocollo predefinito per l'agente 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Stato della versione 
31/01/2018: Rilasciato per il download  
### <a name="fixed-issues"></a>Problemi risolti 

- Risolto un bug che causava alcune perdite di memoria nell'agente. 
- È stata aggiornata la versione del bus di servizio di Azure, che include una correzione di bug per i problemi di timeout del connettore. 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Stato della versione 
26/11/2017: Rilasciato per il download  
### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità 
- Aggiunta del supporto per le connessioni basate su websocket tra l'agente e i servizi di Azure AD per migliorare la resilienza delle connessioni 

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Stato della versione 
25/11/2017: Rilasciato per il download  
### <a name="fixed-issues"></a>Problemi risolti 
- Corretti i bug correlati alla cache DNS per gli scenari proxy predefiniti 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Stato della versione 
17/10/2017: Rilasciato per il download  
### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità 
- Aggiunta della funzionalità della cache DNS per le connessioni in uscita per aggiungere resilienza dagli errori DNS 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Stato della versione 
31/08/2017: Rilasciato per il download  
### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità 
- Versione GA dell'agente di autenticazione pass-through di Azure AD 

## <a name="next-steps"></a>Passaggi successivi

- [Accesso utente con l'autenticazione pass-through di Azure Active Directory](how-to-connect-pta.md)