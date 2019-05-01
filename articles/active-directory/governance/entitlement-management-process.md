---
title: Richiedere le notifiche di posta elettronica e processo in Gestione dei diritti di Azure AD (anteprima) - Azure Active Directory
description: Informazioni sul processo di richiesta per un pacchetto di accesso e quando vengono inviate le notifiche di posta elettronica in Gestione dei diritti di Azure Active Directory (anteprima).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/26/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ab18c8f165fc30636cd05091be1181743f9972d
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873610"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Richiedere le notifiche di posta elettronica e processo in Gestione dei diritti di Azure AD (anteprima)

> [!IMPORTANT]
> Gestione dei diritti di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Quando un utente invia una richiesta a un pacchetto di accesso, viene avviato un processo per offrire tale richiesta. Gestione dei diritti di Azure AD invia anche notifiche di posta elettronica per i responsabili approvazione e i richiedenti quando si verificano gli eventi principali durante il processo.

Questo articolo descrive il processo di richiesta e le notifiche di posta elettronica inviati.

## <a name="request-process"></a>Processo di richiesta

Un utente che richiede l'accesso a un pacchetto di accesso può inviare una richiesta di accesso. A seconda della configurazione dei criteri, la richiesta potrebbe richiedere un'approvazione. Quando una richiesta viene approvata, un processo ha inizio da assegnare all'utente l'accesso a ogni risorsa nel pacchetto di accesso. Il diagramma seguente mostra una panoramica del processo e i diversi stati.

![Diagramma del processo di approvazione](./media/entitlement-management-process/request-process.png)

| Stato | DESCRIZIONE |
| --- | --- |
| Inviato | Utente invia una richiesta. |
| In attesa di approvazione | Se i criteri per un pacchetto di accesso richiedono l'approvazione, sposta una richiesta di approvazione in sospeso. |
| Scaduto | Se nessun responsabili approvazione esamina una richiesta entro il timeout della richiesta di approvazione, la richiesta scade. Per riprovare, l'utente dovrà eseguire nuovamente la richiesta. |
| Negata | Responsabile approvazione rifiuta una richiesta. |
| Approved | Responsabile approvazione la approva una richiesta. |
| Recapito | Abbuia **non** stato assegnato l'accesso a tutte le risorse nel pacchetto di accesso. Se si tratta di un utente esterno, l'utente non è ancora accessibile la directory delle risorse ed accettato la richiesta di autorizzazioni. |
| Recapitato | Utente è stato assegnato l'accesso a tutte le risorse nel pacchetto di accesso. |
| Accesso esteso | Se le estensioni sono consentite nei criteri, l'utente esteso l'assegnazione. |
| Accesso scaduto | Accesso dell'utente per il pacchetto di accesso è scaduto. Per ottenere l'accesso anche in questo caso, l'utente dovrà inviare una richiesta. |

## <a name="email-notifications"></a>Notifiche tramite posta elettronica

Se sei un responsabile approvazione, si riceve notifiche di posta elettronica quando è necessario approvare una richiesta di accesso e quando è stata completata una richiesta di accesso. Se sei un richiedente, si riceve le notifiche di posta elettronica che indicano lo stato della richiesta. Il diagramma seguente illustra quando queste notifiche tramite posta elettronica viene inviato.

![Processo di posta elettronica di gestione dei diritti](./media/entitlement-management-process/email-notifications.png)

Nella tabella seguente fornisce informazioni dettagliate su ognuna di queste notifiche di posta elettronica.

| # | Oggetto del messaggio di posta elettronica | Quando inviato | Inviato a |
| --- | --- | --- | --- |
| 1 | Azione necessaria: Richiesta di accesso di revisione dal *[richiedente]* al *[pacchetto di accesso]* da *[date]* | Quando un richiedente invia una richiesta per un pacchetto di accesso | Tutti i responsabili |
| 2 | Azione necessaria: Richiesta di accesso di revisione dal *[richiedente]* al *[pacchetto di accesso]* da *[date]* | Timeout della richiesta X giorni prima dell'approvazione | Tutti i responsabili |
| 3 | Notifica sullo stato: *[richiedente]* della richiesta di accesso per *[pacchetto di accesso]* è scaduto | Quando i responsabili approvazione non approvare o rifiutare una richiesta di accesso entro il periodo di tempo di richiesta | Richiedente |
| 4 | Notifica sullo stato: *[richiedente]* richiesta di accesso per *[pacchetto di accesso]* è stata completata | Quando il primo approvatore approva o rifiuta una richiesta di accesso | Tutti i responsabili |
| 5 | È stato negato l'accesso a *[pacchetto di accesso]* | Quando un richiedente è stato negato l'accesso al pacchetto di accesso | Richiedente |
| 6 | È ora possibile accedere a *[pacchetto di accesso]*  | Quando un richiedente è stato concesso l'accesso a tutte le risorse nel pacchetto di accesso | Richiedente |
| 7 | L'accesso al *[pacchetto di accesso]* scade fra X giorni: | X giorni prima dell'accesso del richiedente al pacchetto di accesso scade | Richiedente |
| 8 | L'accesso al *[pacchetto di accesso]* è scaduto | Alla scadenza l'accesso del richiedente a un pacchetto di accesso | Richiedente |

### <a name="review-access-request-emails"></a>Messaggi di richiesta di accesso di revisione

Quando un richiedente invia una richiesta di accesso per un pacchetto di accesso che è configurato per richiedere l'approvazione, tutti i responsabili configurati nel criterio di ricevano una notifica di posta elettronica con i dettagli della richiesta. I dettagli includono il nome del richiedente, organizzazione, accedere a data di inizio e fine, se specificato, una motivazione aziendale, quando è stata inviata la richiesta e quando la richiesta scadrà. Messaggio di posta elettronica include un collegamento in cui gli approvatori possono approvare o rifiutare la richiesta di accesso. Ecco una notifica di posta elettronica di esempio che viene inviata a un responsabile approvazione quando un richiedente invia una richiesta di accesso.

![Messaggio di posta elettronica richiesta revisione accesso](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>Messaggi di posta elettronica approvate o rifiutate

I richiedenti ricevono una notifica quando la richiesta di accesso è approvata e disponibili per l'accesso o quando la richiesta di accesso è negata. Quando un responsabile approvazione esamina una richiesta di accesso presentata da un richiedente, essi possono approvare o rifiutare la richiesta di accesso. Il responsabile approvazione deve aggiungere una motivazione aziendale per la propria decisione.

Quando viene approvata una richiesta di accesso, gestione entitlement avvia la procedura per concedere al richiedente l'accesso a ogni risorsa nel pacchetto di accesso. Dopo che il richiedente è stato concesso l'accesso a tutte le risorse nel pacchetto di accesso, viene inviata una notifica di posta elettronica al richiedente che la richiesta di accesso è stata approvata e che ora hanno accesso al pacchetto di accesso. Ecco una notifica tramite posta elettronica di esempio che viene inviata a un richiedente quando viene concesso a un pacchetto di accesso.

Quando viene negata una richiesta di accesso, viene inviata una notifica di posta elettronica al richiedente. Ecco una notifica tramite posta elettronica di esempio che viene inviata a un richiedente quando la richiesta di accesso viene negata.

### <a name="expired-access-request-emails"></a>Messaggi di richiesta di accesso scaduto

I richiedenti ricevono una notifica quando la richiesta di accesso è scaduto. Quando un richiedente invia una richiesta di accesso, la richiesta ha una durata richiesta, dopodiché scade. Se non sono presenti alcun responsabili approvazione che inviano una decisione approve/deny, la richiesta continua a rimanere in uno stato di approvazione in sospeso. Quando la richiesta raggiunga la durata di scadenza configurata, la richiesta principale scade e può non è più essere approvata o rifiutata dal responsabile approvazione. In questo caso, la richiesta viene inviata a uno stato scaduto. Una richiesta scaduta non è più possibile essere approvata o negata. Notifiche di posta elettronica viene inviato al richiedente che la richiesta di accesso è scaduto, e che è necessario inviare di nuovo la richiesta di accesso. Ecco una notifica tramite posta elettronica di esempio che viene inviata a un richiedente quando la richiesta di accesso è scaduto.

![Accedere alla posta elettronica richiesta scaduta](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>Passaggi successivi

- [Richiedere l'accesso a un pacchetto di accesso](entitlement-management-request-access.md)
- [Approvare o rifiutare le richieste di accesso](entitlement-management-request-approve.md)
