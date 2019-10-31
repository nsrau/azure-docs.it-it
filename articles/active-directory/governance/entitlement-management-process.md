---
title: Richiedere il processo e le notifiche tramite posta elettronica in Azure AD gestione dei diritti-Azure Active Directory
description: Informazioni sul processo di richiesta per un pacchetto di accesso e quando le notifiche di posta elettronica vengono inviate in Azure Active Directory gestione dei diritti.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34d2c69cc808552a3b0c604804f3cd2597b379b
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199980"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Richiedere il processo e le notifiche tramite posta elettronica in Azure AD gestione dei diritti

Quando un utente invia una richiesta a un pacchetto di accesso, un processo inizia a consegnare la richiesta di accesso. Azure AD la gestione dei diritti Invia notifiche tramite posta elettronica ai responsabili approvazione e ai richiedenti quando si verificano eventi chiave durante il processo. Questo articolo descrive il processo di richiesta e le notifiche di posta elettronica inviate.

## <a name="request-process"></a>Richiedi processo

Un utente che deve accedere a un pacchetto di accesso può inviare una richiesta di accesso. A seconda della configurazione dei criteri, la richiesta potrebbe richiedere un'approvazione. Quando una richiesta viene approvata, un processo inizia a assegnare l'accesso utente a ogni risorsa nel pacchetto di accesso. Il diagramma seguente mostra una panoramica del processo e dei diversi stati:

![Diagramma del processo di approvazione](./media/entitlement-management-process/request-process.png)

| Statale | Description |
| --- | --- |
| Inviato | L'utente invia una richiesta. |
| In attesa di approvazione | Se il criterio per un pacchetto di accesso richiede l'approvazione, una richiesta passa a approvazione in sospeso. |
| Scaduto | Se nessun approvatore approva una richiesta entro il timeout della richiesta di approvazione, la richiesta scade. Per riprovare, l'utente dovrà inviare di nuovo la richiesta. |
| Negato | Il responsabile approvazione nega una richiesta. |
| Approved | Il responsabile approvazione approva una richiesta. |
| Recapito | All'utente **non** è stato assegnato l'accesso a tutte le risorse nel pacchetto di accesso. Se si tratta di un utente esterno, è possibile che l'utente non abbia ancora eseguito l'accesso alla directory delle risorse e abbia accettato la richiesta di consenso. |
| Recapitato | All'utente è stato assegnato l'accesso a tutte le risorse nel pacchetto di accesso. |
| Accesso esteso | Se nel criterio sono consentite le estensioni, l'utente ha esteso l'assegnazione. |
| Accesso scaduto | L'accesso dell'utente al pacchetto di accesso è scaduto. Per ottenere nuovamente l'accesso, l'utente dovrà inviare una richiesta. |

## <a name="email-notifications"></a>Notifiche tramite posta elettronica

Se si è un responsabile approvazione, vengono inviate notifiche tramite posta elettronica quando è necessario approvare una richiesta di accesso e quando una richiesta di accesso è stata completata. Se si è un richiedente, viene inviata una notifica tramite posta elettronica che indica lo stato della richiesta.

I diagrammi seguenti mostrano quando queste notifiche di posta elettronica vengono inviate ai responsabili approvazione o al richiedente. Fare riferimento alla [tabella delle notifiche di posta elettronica](entitlement-management-process.md#email-notifications-table) per trovare il numero corrispondente alle notifiche di posta elettronica visualizzate nei diagrammi.

### <a name="primary-approvers-and-alternate-approvers"></a>Responsabili approvazione primari e responsabili approvazione alternativi
Il diagramma seguente illustra l'esperienza dei responsabili approvazione primari e degli approvatori alternativi e le notifiche di posta elettronica ricevute durante il processo di richiesta:

![Flusso del processo di responsabili approvazione primario e alternativo](./media/entitlement-management-process/primary-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Richiedenti
Il diagramma seguente illustra l'esperienza dei richiedenti e le notifiche di posta elettronica ricevute durante il processo di richiesta:

![Flusso del processo del richiedente](./media/entitlement-management-process/requestor-approval-and-expiration-request-flow.png)

### <a name="email-notifications-table"></a>Tabella delle notifiche di posta elettronica
Nella tabella seguente vengono fornite informazioni più dettagliate su ognuna di queste notifiche di posta elettronica. Per gestire questi messaggi di posta elettronica, è possibile usare le regole. In Outlook, ad esempio, è possibile creare regole per spostare i messaggi di posta elettronica in una cartella se l'oggetto contiene parole da questa tabella:

| # | Oggetto del messaggio di posta elettronica | Quando viene inviato | Inviato a |
| --- | --- | --- | --- |
| 1 | Azione richiesta: approvare o negare la richiesta con inoltri per *[DATE]* | Questo messaggio di posta elettronica verrà inviato ai responsabili approvazione alternativi di staging, dopo l'escalation della richiesta, per eseguire un'azione. | Fase 1 responsabile approvazione alternativo |
| 2 | Azione richiesta: approva o Nega richiesta da *[DATE]* | Questo messaggio di posta elettronica verrà inviato ai responsabili approvazione primari di staging, se l'escalation è disabilitata, per eseguire un'azione. | Fase 1 responsabile approvazione primario |
| 3 | Promemoria: approvare o rifiutare la richiesta per *[DATE]* per *[richiedente]* | Questo messaggio di posta elettronica di promemoria verrà inviato ai responsabili approvazione primari di staging, se l'escalation è disabilitata, per eseguire un'azione, solo se non sono ancora state intraprese azioni. | Fase 1 responsabile approvazione primario |
| 4 | Approva o rifiuta la richiesta da *[Time]* in *[DATE]* | Questo messaggio di posta elettronica verrà inviato ai responsabili approvazione primari di staging (se l'escalation è abilitato) per eseguire un'azione. | Fase 1 responsabile approvazione primario |
| 5 | Azione richiesta promemoria: approva o nega la richiesta per *[DATE]* per *[richiedente]* | Questo messaggio di posta elettronica di promemoria verrà inviato ai responsabili approvazione primari di staging, se l'escalation è abilitata, per eseguire un'azione, solo se non sono ancora state intraprese azioni. | Fase 1 responsabile approvazione primario |
| 6 | La richiesta è scaduta per *[access_package]* | Questo messaggio di posta elettronica verrà inviato ai responsabili approvazione primari e/o alla fase 1, di una richiesta a singola fase o in più fasi, dopo la scadenza della richiesta. | Fase 1 responsabile approvazione principale, responsabile approvazione alternativo |
| 7 | Richiesta approvata per *[richiedente]* a *[access_package]* | Al completamento di una richiesta, questo messaggio di posta elettronica verrà inviato ai responsabili approvazione primari e/o alla fase 1. | Fase 1 responsabile approvazione principale, responsabile approvazione alternativo |
| 8 | Richiesta approvata per *[richiedente]* a *[access_package]* | Questo messaggio di posta elettronica verrà inviato ai responsabili approvazione primario di staging e/o alla fase 1, di una richiesta a 2 fasi, solo quando viene approvata la fase-1. | Fase 1 responsabile approvazione principale, responsabile approvazione alternativo |
| 9 | Richiesta negata a *[access_package]* | Questo messaggio di posta elettronica verrà inviato al richiedente solo quando la richiesta viene rifiutata | Richiedente |
| 10 | La richiesta è scaduta per *[access_package]* | Questo messaggio di posta elettronica verrà inviato al richiedente alla fine della fase-1, di una richiesta a fase singola o in più fasi, dopo la scadenza della richiesta. | Richiedente |
| 18 | È ora possibile accedere a *[access_package]* | Questo messaggio di posta elettronica verrà inviato agli utenti finali per iniziare a usare l'accesso. | Richiedente |
| 19 | Estendi accesso per *[access_package]* di *[DATE]* | Questo messaggio di posta elettronica verrà inviato agli utenti finali prima della scadenza dell'accesso. | Richiedente |
| 20 | L'accesso è terminato per *[access_package]* | Questo messaggio di posta elettronica verrà inviato agli utenti finali dopo la scadenza dell'accesso. | Richiedente |

### <a name="access-request-emails"></a>Messaggi di posta elettronica di richiesta di accesso

Quando un richiedente invia una richiesta di accesso per un pacchetto di accesso configurato per richiedere l'approvazione, tutti i responsabili approvazione aggiunti al criterio riceveranno una notifica tramite posta elettronica con i dettagli della richiesta. I dettagli includono il nome, l'organizzazione, la data di inizio e di fine (se specificato) del richiedente, la motivazione aziendale, il momento in cui la richiesta è stata inviata e la scadenza della richiesta.

Il messaggio di posta elettronica include i responsabili approvazione dei collegamenti possono fare clic su per passare a accesso per approvare o negare la richiesta di accesso. Ecco una notifica di posta elettronica di esempio inviata a un responsabile approvazione quando un richiedente invia una richiesta di accesso:

![Approva la richiesta di accesso alla posta elettronica del pacchetto](./media/entitlement-management-shared/approver-request-email.png)

Ai responsabili approvazione primari viene inviata anche una notifica tramite posta elettronica con un promemoria per intervenire e prendere una decisione per la richiesta. Di seguito è riportato un messaggio di posta elettronica di esempio dei responsabili approvazione primari delle notifiche che ricevono per ricordare di eseguire le operazioni seguenti:

![Messaggio di richiesta di accesso di promemoria](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approver-request-emails"></a>Messaggi di posta elettronica di richiesta di approvazione alternativi

Se l'invio a responsabili approvazione alternativi è abilitato, in base ai criteri di invio, se la richiesta è ancora in sospeso, la richiesta verrà trasmessa. Il responsabile approvazione alternativo riceverà un messaggio di posta elettronica di notifica per approvare o rifiutare la richiesta. Di seguito è riportato un messaggio di posta elettronica di esempio della notifica ricevuta dagli approvatori alternativi:

![Messaggio di richiesta di approvazione alternativo](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Entrambi, i responsabili approvazione primari e gli approvatori alternativi possono approvare o rifiutare la richiesta.

### <a name="approved-or-denied-emails"></a>Email approvate o negate

I richiedenti ricevono una notifica quando la richiesta di accesso è approvata e disponibile per l'accesso o quando la richiesta di accesso viene negata. Quando un responsabile approvazione riceve una richiesta di accesso inviata da un richiedente, può approvare o rifiutare la richiesta di accesso. Il responsabile approvazione deve aggiungere una motivazione aziendale per la decisione. Ecco un messaggio di posta elettronica di esempio inviato a responsabili approvazione primari o alternativi dopo l'approvazione di una richiesta:

![Verifica indirizzo di posta elettronica richiesta di accesso](./media/entitlement-management-process/approver-request-email-approved.png)

Quando viene approvata una richiesta di accesso e viene effettuato il provisioning dell'accesso, viene inviata una notifica tramite posta elettronica al richiedente che ora ha accesso al pacchetto di accesso. Ecco una notifica di posta elettronica di esempio inviata a un richiedente quando viene concesso l'accesso a un pacchetto di accesso:

![Messaggio di richiesta di accesso scaduto](./media/entitlement-management-process/requestor-email-approved.png)

Quando una richiesta di accesso viene negata, viene inviata una notifica tramite posta elettronica al richiedente. Ecco una notifica di posta elettronica di esempio inviata a un richiedente quando viene negata la richiesta di accesso:

![Richiesta di messaggio di posta elettronica negato](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="expired-access-request-emails"></a>Messaggi di posta elettronica di richiesta di accesso scaduti

Le richieste di accesso possono scadere se nessun responsabile approvazione ha approvato o negato la richiesta. 

Quando la richiesta raggiunge la data di scadenza configurata e scade, non può più essere approvata o negata dai responsabili approvazione. Di seguito è riportato un messaggio di posta elettronica di esempio della notifica inviata a tutti i responsabili approvazione primari e alternativi:

 ![I responsabili approvazione hanno scaduto la posta elettronica richiesta di accesso](./media/entitlement-management-process/approver-request-email-expired.png)

 Al richiedente viene inviata anche una notifica tramite posta elettronica, che informa che la richiesta di accesso è scaduta e che è necessario inviare nuovamente la richiesta di accesso. Ecco una notifica di posta elettronica di esempio inviata a un richiedente quando la richiesta di accesso è scaduta:

![Messaggio di richiesta di accesso scaduto per il richiedente](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Passaggi successivi

- [Richiedere l'accesso a un pacchetto di accesso](entitlement-management-request-access.md)
- [Approva o rifiuta le richieste di accesso](entitlement-management-request-approve.md)
