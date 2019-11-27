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
ms.date: 11/11/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: f336e9f2bdf1553a72bdc35fecc1b0b735fad274
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74206967"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Richiedere il processo e le notifiche tramite posta elettronica in Azure AD gestione dei diritti

Quando un utente invia una richiesta a un pacchetto di accesso, un processo inizia a consegnare la richiesta di accesso. Azure AD la gestione dei diritti Invia notifiche tramite posta elettronica ai responsabili approvazione e ai richiedenti quando si verificano eventi chiave durante il processo. Questo articolo descrive il processo di richiesta e le notifiche di posta elettronica inviate.

## <a name="request-process"></a>Richiedi processo

Un utente che deve accedere a un pacchetto di accesso può inviare una richiesta di accesso. A seconda della configurazione dei criteri, la richiesta potrebbe richiedere un'approvazione. Quando una richiesta viene approvata, un processo inizia a assegnare l'accesso utente a ogni risorsa nel pacchetto di accesso. Il diagramma seguente mostra una panoramica del processo e dei diversi stati:

![Diagramma del processo di approvazione](./media/entitlement-management-process/request-process.png)

| Stato | DESCRIZIONE |
| --- | --- |
| Inviato | L'utente invia una richiesta. |
| In attesa di approvazione | Se il criterio per un pacchetto di accesso richiede l'approvazione, una richiesta passa a approvazione in sospeso. |
| Scaduto | Se nessun approvatore approva una richiesta entro il timeout della richiesta di approvazione, la richiesta scade. Per riprovare, l'utente dovrà inviare di nuovo la richiesta. |
| Negato | Il responsabile approvazione nega una richiesta. |
| Approved | Il responsabile approvazione approva una richiesta. |
| Recapito | All'utente **non** è stato assegnato l'accesso a tutte le risorse nel pacchetto di accesso. Se si tratta di un utente esterno, è possibile che l'utente non abbia ancora eseguito l'accesso alla directory delle risorse. Potrebbero anche non avere accettato la richiesta di consenso. |
| Recapitato | All'utente è stato assegnato l'accesso a tutte le risorse nel pacchetto di accesso. |
| Accesso esteso | Se nel criterio sono consentite le estensioni, l'utente ha esteso l'assegnazione. |
| Accesso scaduto | L'accesso dell'utente al pacchetto di accesso è scaduto. Per ottenere nuovamente l'accesso, l'utente dovrà inviare una richiesta. |

## <a name="email-notifications"></a>Notifiche tramite posta elettronica

Se si è un responsabile approvazione, vengono inviate notifiche tramite posta elettronica quando è necessario approvare una richiesta di accesso. È anche possibile ricevere notifiche quando una richiesta di accesso è stata completata. Vengono inoltre inviate notifiche tramite posta elettronica che indicano lo stato della richiesta, se si è un richiedente.

I diagrammi seguenti mostrano quando queste notifiche di posta elettronica vengono inviate ai responsabili approvazione o al richiedente. Fare riferimento alla [tabella delle notifiche di posta elettronica](entitlement-management-process.md#email-notifications-table) per trovare il numero corrispondente alle notifiche di posta elettronica visualizzate nei diagrammi.

### <a name="first-approvers-and-alternate-approvers"></a>Primi responsabili approvazione e responsabili approvazione alternativi
Il diagramma seguente illustra l'esperienza dei primi responsabili approvazione e degli approvatori alternativi e delle notifiche di posta elettronica ricevute durante il processo di richiesta:

![Flusso del processo del primo e del responsabile approvazione alternativo](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Richiedenti
Il diagramma seguente illustra l'esperienza dei richiedenti e le notifiche di posta elettronica ricevute durante il processo di richiesta:

![Flusso del processo del richiedente](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>approvazione a 2 fasi
Il diagramma seguente illustra l'esperienza dei responsabili approvazione stage-1 e stage-2 e le notifiche di posta elettronica ricevute durante il processo di richiesta:

![flusso del processo di approvazione a 2 fasi](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>Tabella delle notifiche di posta elettronica
Nella tabella seguente vengono fornite informazioni più dettagliate su ognuna di queste notifiche di posta elettronica. Per gestire questi messaggi di posta elettronica, è possibile usare le regole. In Outlook, ad esempio, è possibile creare regole per spostare i messaggi di posta elettronica in una cartella se l'oggetto contiene parole da questa tabella:

| # | Oggetto del messaggio di posta elettronica | Quando viene inviato | Inviato a |
| --- | --- | --- | --- |
| 1 | Azione richiesta: approvare o negare la richiesta con inoltri per *[DATE]* | Questo messaggio di posta elettronica verrà inviato ai responsabili approvazione alternativi di staging, dopo l'escalation della richiesta, per eseguire un'azione. | Fase-1 responsabili approvazione alternativi |
| 2 | Azione richiesta: approva o Nega richiesta da *[DATE]* | Questo messaggio di posta elettronica verrà inviato al primo responsabile approvazione, se l'escalation è disabilitato, per intervenire. | Primo responsabile approvazione |
| 3 | Promemoria: approvare o rifiutare la richiesta per *[DATE]* per *[richiedente]* | Questo messaggio di promemoria verrà inviato al primo responsabile approvazione, se l'escalation è disabilitata. Il messaggio di posta elettronica chiede di intervenire in caso contrario. | Primo responsabile approvazione |
| 4 | Approva o rifiuta la richiesta da *[Time]* in *[DATE]* | Questo messaggio di posta elettronica verrà inviato al primo responsabile approvazione (se l'escalation è abilitato) per eseguire un'azione. | Primo responsabile approvazione |
| 5 | Azione richiesta promemoria: approva o nega la richiesta per *[DATE]* per *[richiedente]* | Questo messaggio di posta elettronica di promemoria verrà inviato al primo responsabile approvazione, se è abilitata l'escalation. Il messaggio di posta elettronica chiede di intervenire in caso contrario. | Primo responsabile approvazione |
| 6 | La richiesta è scaduta per *[access_package]* | Questo messaggio di posta elettronica verrà inviato al primo responsabile approvazione e alla prima scadenza della richiesta. | Primo responsabile approvazione, responsabili approvazione alternativi fase 1 |
| 7 | Richiesta approvata per *[richiedente]* a *[access_package]* | Questo messaggio di posta elettronica verrà inviato al primo responsabile approvazione e ai responsabili di approvazione alternativi di fase 1 al completamento della richiesta. | Primo responsabile approvazione, responsabili approvazione alternativi fase 1 |
| 8 | Richiesta approvata per *[richiedente]* a *[access_package]* | Questo messaggio di posta elettronica verrà inviato al primo responsabile approvazione e ai responsabili di approvazione alternativi di fase 1 di una richiesta a 2 fasi quando viene approvata la richiesta di fase 1. | Primo responsabile approvazione, responsabili approvazione alternativi fase 1 |
| 9 | Richiesta negata a *[access_package]* | Questo messaggio di posta elettronica verrà inviato al richiedente quando la richiesta viene negata | Richiedente |
| 10 | La richiesta è scaduta per *[access_package]* | Questo messaggio di posta elettronica verrà inviato al richiedente alla fine di una richiesta a una singola o a due fasi. Il messaggio di posta elettronica informa il richiedente che la richiesta è scaduta. | Richiedente |
| 11 | Azione richiesta: approva o Nega richiesta da *[DATE]* | Questo messaggio di posta elettronica verrà inviato al secondo responsabile approvazione, se l'escalation è disabilitato, per intervenire. | Secondo responsabile approvazione |
| 12 | Azione richiesta promemoria: approva o nega la richiesta per *[DATE]* | Questo messaggio di promemoria verrà inviato al secondo responsabile approvazione, se l'escalation è disabilitata. La notifica chiede di intervenire se non ancora. | Secondo responsabile approvazione |
| 13 | Azione richiesta: approvare o rifiutare la richiesta da *[DATE]* per *[richiedente]* | Questo messaggio di posta elettronica verrà inviato al secondo responsabile approvazione, se l'escalation è abilitato, per eseguire un'azione. | Secondo responsabile approvazione |
| 14 | Azione richiesta promemoria: approva o nega la richiesta per *[DATE]* per *[richiedente]* | Questo messaggio di posta elettronica di promemoria verrà inviato al secondo responsabile approvazione, se è abilitata l'escalation. La notifica chiede di intervenire se non ancora. | Secondo responsabile approvazione |
| 15 | Azione richiesta: approvare o negare la richiesta con inoltri per *[DATE]* | Questo messaggio di posta elettronica verrà inviato ai responsabili approvazione alternativi di staging-2, se l'escalation è abilitata, per eseguire un'azione. | Fase 2 responsabili approvazione alternativi |
| 16 | Richiesta approvata per *[richiedente]* a *[access_package]* | Questo messaggio di posta elettronica verrà inviato al secondo responsabile approvazione e al responsabile approvazione alternativo per fase 2 dopo l'approvazione della richiesta. | Secondo responsabile approvazione, fase 2 responsabili approvazione alternativi |
| 17 | Una richiesta è scaduta per *[access_package]* | Questo messaggio di posta elettronica verrà inviato al secondo responsabile approvazione o a responsabili approvazione alternativi, dopo la scadenza della richiesta. | Secondo responsabile approvazione, fase 2 responsabili approvazione alternativi |
| 18 | È ora possibile accedere a *[access_package]* | Questo messaggio di posta elettronica verrà inviato agli utenti finali per iniziare a usare l'accesso. | Richiedente |
| 19 | Estendi accesso per *[access_package]* di *[DATE]* | Questo messaggio di posta elettronica verrà inviato agli utenti finali prima della scadenza dell'accesso. | Richiedente |
| 20 | L'accesso è terminato per *[access_package]* | Questo messaggio di posta elettronica verrà inviato agli utenti finali dopo la scadenza dell'accesso. | Richiedente |

### <a name="access-request-emails"></a>Messaggi di posta elettronica di richiesta di accesso

Quando un richiedente invia una richiesta di accesso per un pacchetto di accesso configurato per richiedere l'approvazione, tutti i responsabili approvazione aggiunti al criterio riceveranno una notifica tramite posta elettronica con i dettagli della richiesta. I dettagli nel messaggio di posta elettronica includono: organizzazione del nome del richiedente e motivazione aziendale; e la data di inizio e di fine dell'accesso richiesta (se specificato). I dettagli includono anche il momento in cui la richiesta è stata inviata e la scadenza della richiesta.

Il messaggio di posta elettronica include un responsabile approvazione dei collegamenti può fare clic su on per passare a accesso personale per approvare o negare la richiesta di accesso. Ecco una notifica di posta elettronica di esempio inviata al primo responsabile approvazione o secondo responsabile approvazione (se è abilitata l'approvazione a 2 fasi) per completare una richiesta di accesso:

![Approva la richiesta di accesso alla posta elettronica del pacchetto](./media/entitlement-management-shared/approver-request-email.png)

I responsabili approvazione possono anche ricevere un messaggio di posta elettronica di promemoria. Il messaggio di posta elettronica richiede al responsabile approvazione di prendere una decisione sulla richiesta. Ecco una notifica di posta elettronica di esempio ricevuta dal responsabile approvazione per ricordare di eseguire le operazioni seguenti:

![Messaggio di richiesta di accesso di promemoria](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>Messaggi di posta elettronica di richiesta per responsabili approvazione alternativi

Se l'impostazione di approvazione alternativa è abilitata e la richiesta è ancora in sospeso, verrà trasmessa. Gli approvatori alternativi riceveranno un messaggio di posta elettronica per approvare o rifiutare la richiesta. È possibile abilitare responsabili approvazione alternativi in fase 1 e fase 2. Di seguito è riportato un messaggio di posta elettronica di esempio della notifica ricevuta dagli approvatori alternativi:

![Indirizzo di posta elettronica di richiesta per responsabili approvazione alternativi](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Sia il responsabile approvazione che gli approvatori alternativi possono approvare o rifiutare la richiesta.

### <a name="approved-or-denied-emails"></a>Email approvate o negate

 Quando un responsabile approvazione riceve una richiesta di accesso inviata da un richiedente, può approvare o rifiutare la richiesta di accesso. Il responsabile approvazione deve aggiungere una motivazione aziendale per la decisione. Di seguito è riportato un messaggio di posta elettronica di esempio inviato ai responsabili approvazione e agli approvatori alternativi dopo l'approvazione di una richiesta:

![Richiesta approvata per accedere alla posta elettronica del pacchetto](./media/entitlement-management-process/approver-request-email-approved.png)

Quando viene approvata una richiesta di accesso e viene effettuato il provisioning dell'accesso, viene inviata una notifica tramite posta elettronica al richiedente che ora ha accesso al pacchetto di accesso. Ecco una notifica di posta elettronica di esempio inviata a un richiedente quando viene concesso l'accesso a un pacchetto di accesso:

![Messaggio di richiesta di accesso al richiedente approvato](./media/entitlement-management-process/requestor-email-approved.png)

Quando una richiesta di accesso viene negata, viene inviata una notifica tramite posta elettronica al richiedente. Ecco una notifica di posta elettronica di esempio inviata a un richiedente quando viene negata la richiesta di accesso:

![Richiesta di messaggio di posta elettronica negato](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>messaggi di richiesta di accesso con approvazione a 2 fasi

Se l'approvazione a 2 fasi è abilitata, almeno due responsabili approvazione devono approvare la richiesta, una da ogni fase, prima che il richiedente possa ricevere l'accesso.

Durante la fase 1, il primo responsabile approvazione riceverà il messaggio di richiesta di accesso e prenderà una decisione. Se approvano la richiesta, tutti i primi responsabili approvazione e i responsabili dell'approvazione alternativi nella fase-1 (se l'escalation è abilitata) riceveranno una notifica di completamento della fase-1. Di seguito è riportato un messaggio di posta elettronica di esempio della notifica inviata al termine della fase 1:

![messaggio di richiesta di accesso a 2 fasi](./media/entitlement-management-process/approver-request-email-2stage.png)

Dopo che la richiesta è stata approvata dal primo o dal responsabile approvazione alternativo nella fase 1, inizia la fase-2. Durante la fase 2, il secondo responsabile approvazione riceverà il messaggio di posta elettronica di notifica della richiesta di accesso. Dopo il secondo responsabile approvazione o i responsabili approvazione alternativi in fase 2 (se è abilitata l'escalation) decidere di approvare o rifiutare la richiesta, i messaggi di posta elettronica di notifica vengono inviati al primo e al secondo approvatori e a tutti i responsabili di approvazione alternativi in fase 1 e fase 2, oltre al richiedente.

### <a name="expired-access-request-emails"></a>Messaggi di posta elettronica di richiesta di accesso scaduti

Le richieste di accesso possono scadere se nessun responsabile approvazione ha approvato o negato la richiesta. 

Quando la richiesta raggiunge la data di scadenza configurata e scade, non può più essere approvata o negata dai responsabili approvazione. Ecco un messaggio di posta elettronica di esempio della notifica inviata a tutti i primi, secondi (se è abilitata l'approvazione a 2 fasi) e responsabili approvazione alternativi:

![I responsabili approvazione hanno scaduto la posta elettronica richiesta di accesso](./media/entitlement-management-process/approver-request-email-expired.png)

Al richiedente viene inviata anche una notifica tramite posta elettronica, che informa che la richiesta di accesso è scaduta e che è necessario inviare nuovamente la richiesta di accesso. Il diagramma seguente illustra l'esperienza del richiedente e le notifiche di posta elettronica ricevute quando richiedono di estendere l'accesso:

![Il richiedente estende il flusso del processo di accesso](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Ecco una notifica di posta elettronica di esempio inviata a un richiedente quando la richiesta di accesso è scaduta:

![Messaggio di richiesta di accesso scaduto per il richiedente](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Passaggi successivi

- [Richiedere l'accesso a un pacchetto di accesso](entitlement-management-request-access.md)
- [Approva o rifiuta le richieste di accesso](entitlement-management-request-approve.md)
