---
title: Argomenti partner Auth0 con Griglia di eventi di Azure
description: Inviare eventi da Auth0 ai servizi di Azure con Griglia di eventi di Azure.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: e193b272460fe97f599adff414fcac6e80648104
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738371"
---
# <a name="auth0-partner-topics"></a>Argomenti partner Auth0
![Logo di Auth0](./media/auth0-overview/auth0-logo.png)

Auth0, la piattaforma di gestione delle identità per gli sviluppatori di applicazioni, fornisce a sviluppatori e aziende i blocchi predefiniti necessari per proteggere le applicazioni.

L'argomento partner Auth0 consente di usare eventi generati dal sistema di Auth0 per eseguire una serie di attività. Interagire con gli utenti in modo significativo dopo l'autenticazione o automatizzare le attività di sicurezza e dell'infrastruttura.

L'integrazione consente di trasmettere gli eventi del log di Auth0 con affidabilità elevata in Azure, dove è possibile utilizzare gli eventi con le risorse di Azure preferite. Questa integrazione consente di reagire agli eventi, ottenere informazioni dettagliate, eseguire il monitoraggio per rilevare eventuali problemi di sicurezza e interagire con altre pipeline di dati avanzate.

Grazie a questa funzionalità, le organizzazioni che usano Auth0 e Azure possono integrare facilmente i dati nell'intero stack. 
 
## <a name="available-event-types"></a>Tipi di evento disponibili
L'elenco completo dei tipi di evento Auth0 disponibili e le relative descrizioni sono disponibili [qui](https://auth0.com/docs/logs/references/log-event-type-codes).

## <a name="use-cases"></a>Casi d'uso

### <a name="engage-with-your-users"></a>Entrare in contatto con altri utenti
Garantire un'esperienza utente solida è fondamentale per mantenere gli utenti, riducendo l'abbandono. È possibile offrire un'esperienza più personalizzata delle applicazioni usando gli eventi Auth0 con Funzioni di Azure e App per la logica. 

### <a name="understand-user-behavior"></a>Capire il comportamento degli utenti
È possibile capire quando gli utenti accedono al prodotto, dove sono connessi e quali dispositivi usano e sviluppare la comprensione di quali siano delle aree del prodotto più importanti tenendo traccia di questi segnali. Questi consentono di determinare quali browser e dispositivi supportare, in quali lingue localizzare l'app e quali sono le ore di punta del traffico. 

### <a name="manage-user-data"></a>Gestire i dati degli utenti
Conservare e controllare le azioni degli utenti è fondamentale per garantire la sicurezza e rispettare le normative del settore. La possibilità di modificare, rimuovere ed esportare i dati degli utenti sta diventando sempre più importante per il rispetto delle leggi sulla privacy, ad esempio il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione europea.

### <a name="secure-your-application"></a>Proteggere l'applicazione
Per la protezione di un sistema distribuito è importante combinare procedure di monitoraggio della sicurezza e di risposta agli eventi imprevisti. Per questo motivo è importante mantenere tutti i dati in un'unica posizione e monitorare l'intero stack. 

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica degli argomenti partner](partner-topics-overview.md)
- [Come usare l'argomento partner Auth0](auth0-how-to.md)
- [Documentazione di Auth0](https://auth0.com/docs/azure-tutorial)
- [Diventare partner di Griglia di eventi](partner-onboarding-overview.md)

