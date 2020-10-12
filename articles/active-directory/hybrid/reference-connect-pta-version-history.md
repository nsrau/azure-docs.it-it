---
title: 'Azure AD autenticazione pass-through: cronologia delle versioni | Microsoft Docs'
description: Questo articolo elenca tutte le versioni dell'agente di autenticazione pass-through di Azure AD
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 854931273ffebd653615a44dbfa9ce1b846655c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333427"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD agente di autenticazione pass-through: cronologia delle versioni 
 
Gli agenti installati in locale che abilitano l'autenticazione pass-through vengono aggiornati regolarmente per offrire nuove funzionalità. Questo articolo elenca le versioni e le funzionalità che vengono aggiunte quando viene introdotta una nuova funzionalità. Gli agenti di autenticazione pass-through vengono aggiornati automaticamente quando viene rilasciata una nuova versione. 

Ecco gli argomenti correlati: 

- [Accesso utente con Azure AD autenticazione pass-through](how-to-connect-pta.md) 
- [Azure AD l'installazione dell'agente di autenticazione pass-through](how-to-connect-pta-quick-start.md) 

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>Stato versione: 
09/04/2020: resa disponibile per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

- Aggiunta del supporto per l'assegnazione di ambienti cloud al momento dell'installazione. Il bundle può essere aggiunto a un ambiente cloud specifico.



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Stato della versione 
1/22/2019: rilasciato per il download  
### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità 
- Aggiunta del supporto per i canali affidabili del bus di servizio per aggiungere un altro livello di resilienza della connessione per le connessioni in uscita 
- Applicare TLS 1,2 durante la registrazione dell'agente 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Stato della versione 
4/10/2018: rilasciato per il download  
### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità 
- Supporto della connessione Web socket 
- Impostare TLS 1,2 come protocollo predefinito per l'agente 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Stato della versione 
1/31/2018: rilasciato per il download  
### <a name="fixed-issues"></a>Problemi risolti 
- Correzione di un bug che causava alcune perdite di memoria nell'agente. 
- Aggiornamento della versione del bus di servizio di Azure, che include una correzione di bug per i problemi di timeout del connettore. 
### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità 
- Aggiunta del supporto per le connessioni basate su WebSocket tra l'agente e i servizi Azure AD per migliorare la resilienza della connessione

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Stato della versione 
11/25/2017: rilasciato per il download  
### <a name="fixed-issues"></a>Problemi risolti 
- Correzione dei bug relativi alla cache DNS per gli scenari proxy predefiniti 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Stato della versione 
10/17/2017: rilasciato per il download  
### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità 
- Aggiunta della funzionalità cache DNS per le connessioni in uscita per aggiungere resilienza da errori DNS 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Stato della versione 
08/31/2017: rilasciato per il download  
### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità 
- Versione GA dell'agente di autenticazione pass-through di Azure AD 

## <a name="next-steps"></a>Passaggi successivi

- [Accesso utente con l'autenticazione pass-through di Azure Active Directory](how-to-connect-pta.md)
