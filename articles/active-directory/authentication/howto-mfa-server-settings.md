---
title: Configurare il server multi-factor authentication-Azure Active Directory
description: Informazioni su come configurare le impostazioni per il server di autenticazione a più fattori di Azure nel portale di Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3da7c3afa6b414e3d7df3bb58766d1ab7657dbc4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94838962"
---
# <a name="configure-mfa-server-settings"></a>Configurare le impostazioni del server multi-factor authentication

Questo articolo illustra come gestire le impostazioni del server Azure multi-factor authentication nel portale di Azure.

> [!IMPORTANT]
> A partire dal 1° luglio 2019, Microsoft non offrirà più il server MFA per le nuove distribuzioni. I nuovi clienti che desiderano richiedere l'autenticazione a più fattori dagli utenti devono usare Multi-Factor Authentication di Azure AD basati sul cloud. Gli attuali clienti che anno attivato il server MFA prima del 1° luglio potranno scaricare la versione più recente e gli aggiornamenti futuri, oltre a generare le credenziali di attivazione come di consueto.

Sono disponibili le seguenti impostazioni del server di autenticazione a più fattori:

| Funzionalità | Descrizione |
| ------- | ----------- |
| Impostazioni del server | Permette di scaricare il server MFA e di generare le credenziali di attivazione per inizializzare l'ambiente |
| [Bypass monouso](#one-time-bypass) | Consentire a un utente di eseguire l'autenticazione senza eseguire l'autenticazione a più fattori per un periodo di tempo limitato. |
| [Regole di memorizzazione nella cache](#caching-rules) |  La memorizzazione nella cache viene usata principalmente quando i sistemi locali, ad esempio VPN, inviano più richieste di verifica mentre la prima richiesta è ancora in corso. Dopo che l'utente ha completato la prima verifica in corso, grazie a questa funzionalità le richieste successive hanno automaticamente esito positivo. |
| Stato del server | Visualizza lo stato dei server MFA locali, insieme a informazioni su versione, stato, indirizzo IP e data e ora dell'ultima comunicazione. |

## <a name="one-time-bypass"></a>Bypass monouso

La funzionalità di bypass monouso consente a un utente di eseguire l'autenticazione una sola volta senza eseguire l'autenticazione a più fattori. Il bypass è temporaneo e scade dopo un numero di secondi specificato. In situazioni in cui l'app per dispositivi mobili o il telefono non ricevono una notifica o una chiamata telefonica, è possibile abilitare un bypass monouso in modo che l'utente possa accedere alla risorsa desiderata.

Per creare un bypass monouso, completare i passaggi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
1. Cercare e selezionare **Azure Active Directory**, quindi passare a **sicurezza** autenticazione a più fattori per  >  **MFA**  >  **bypass** monouso.
1. Selezionare **Aggiungi**.
1. Se necessario, selezionare il gruppo di replica per il bypass.
1. Immettere il nome utente come `username\@domain.com`. Immettere il numero di secondi di durata del bypass e il motivo del bypass.
1. Selezionare **Aggiungi**. Il limite di tempo diventa immediatamente effettivo. L'utente deve accedere prima della scadenza del bypass monouso.

È inoltre possibile visualizzare il report bypass monouso dalla stessa finestra.

## <a name="caching-rules"></a>Regole di memorizzazione nella cache

È possibile impostare un periodo di tempo per attivare i tentativi di autenticazione in seguito all'autenticazione dell'utente tramite la funzionalità di _memorizzazione nella cache_. I tentativi di autenticazione successivi dell'utente entro un periodo di tempo specifico hanno automaticamente esito positivo.

La memorizzazione nella cache viene usata principalmente quando i sistemi locali, ad esempio VPN, inviano più richieste di verifica mentre la prima richiesta è ancora in corso. Dopo che l'utente ha completato la prima verifica in corso, grazie a questa funzionalità le richieste successive hanno automaticamente esito positivo.

>[!NOTE]
> La funzionalità di memorizzazione nella cache non deve essere usata per accedere ad Azure Active Directory, ovvero Azure AD.

Per configurare la memorizzazione nella cache, attenersi alla procedura seguente:

1. Passare a **Azure Active Directory** > **Sicurezza** > **MFA** > **Regole di memorizzazione nella cache**.
1. Selezionare **Aggiungi**.
1. Selezionare il **tipo di cache** nell'elenco a discesa. Immettere il numero massimo di **secondi cache**.
1. Se necessario, selezionare un tipo di autenticazione e specificare un'applicazione.
1. Selezionare **Aggiungi**.

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili opzioni di configurazione del server multi-factor authentication aggiuntive dalla console Web del server di autenticazione a più fattori. È anche possibile [configurare il server di autenticazione a più fattori di Azure per la disponibilità elevata](howto-mfaserver-deploy-ha.md).
