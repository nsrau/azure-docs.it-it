---
title: Notifiche & processo di richiesta - Gestione dei diritti di Azure ADRequest process & notifications - Azure AD entitlement management
description: Informazioni sul processo di richiesta per un pacchetto di accesso e sull'invio di notifiche tramite posta elettronica nella gestione dei diritti di Azure Active Directory.Learn about the request process for an access package and when email notifications are sent in Azure Active Directory entitlement management.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4ff270977449bb80f97073342dc0c726a3f2316
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128531"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Richiedi il processo e le notifiche di posta elettronica nella gestione dei diritti di Azure ADRequest process and email notifications in Azure AD entitlement management

Quando un utente invia una richiesta a un pacchetto di accesso, un processo inizia a recapitare tale richiesta di accesso. La gestione dei diritti di Azure AD invia notifiche tramite posta elettronica agli approvatori e ai richiedenti quando si verificano eventi chiave durante il processo. In questo articolo viene descritto il processo di richiesta e le notifiche di posta elettronica inviate.

## <a name="request-process"></a>Processo di richiesta

Un utente che deve accedere a un pacchetto di accesso può inviare una richiesta di accesso. A seconda della configurazione del criterio, la richiesta potrebbe richiedere un'approvazione. Quando una richiesta viene approvata, un processo inizia ad assegnare all'utente l'accesso a ogni risorsa nel pacchetto di accesso. Il diagramma seguente mostra una panoramica del processo e dei diversi stati:

![Diagramma del processo di approvazione](./media/entitlement-management-process/request-process.png)

| State | Descrizione |
| --- | --- |
| Inviato | L'utente invia una richiesta. |
| In attesa di approvazione | Se i criteri per un pacchetto di accesso richiedono l'approvazione, una richiesta passa all'approvazione in sospeso. |
| Scaduto | Se nessun approvatore approva una richiesta entro il timeout della richiesta di approvazione, la richiesta scade. Per riprovare, l'utente dovrà inviare nuovamente la richiesta. |
| Negate | Il responsabile approvazione nega una richiesta. |
| Approved | Il responsabile approvazione approva una richiesta. |
| Recapito | All'utente **non** è stato assegnato l'accesso a tutte le risorse nel pacchetto di accesso. Se si tratta di un utente esterno, è possibile che l'utente non abbia ancora avuto accesso alla directory delle risorse. Inoltre, potrebbero non aver accettato la richiesta di consenso. |
| Recapitato | All'utente è stato assegnato l'accesso a tutte le risorse nel pacchetto di accesso. |
| Accesso esteso | Se le estensioni sono consentite nel criterio, l'utente ha esteso l'assegnazione. |
| Accesso scaduto | L'accesso dell'utente al pacchetto di accesso è scaduto. Per ottenere nuovamente l'accesso, l'utente dovrà inviare una richiesta. |

## <a name="email-notifications"></a>Notifiche di posta elettronica

Se sei un approvatore, ti vengono inviate notifiche e-mail quando devi approvare una richiesta di accesso. Si ricevono notifiche anche quando una richiesta di accesso è stata completata. Se sei un richiedente, ti vengono inoltre inviate notifiche e-mail che indicano lo stato della tua richiesta.

I diagrammi seguenti mostrano quando queste notifiche di posta elettronica vengono inviate agli approvatori o al richiedente. Fare riferimento alla [tabella delle notifiche di posta elettronica](entitlement-management-process.md#email-notifications-table) per trovare il numero corrispondente alle notifiche di posta elettronica visualizzate nei diagrammi.

### <a name="first-approvers-and-alternate-approvers"></a>Primi approvatori e approvatori alternativi
Il diagramma seguente mostra l'esperienza dei primi approvatori e approvatori alternativi e le notifiche di posta elettronica ricevute durante il processo di richiesta:

![Flusso di processo del primo e dell'approvatore alternativo](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Richiedenti
Il diagramma seguente mostra l'esperienza dei richiedenti e le notifiche di posta elettronica che ricevono durante il processo di richiesta:The following diagram shows the experience of requestors and the email notifications they receive during the request process:

![Flusso del processo del richiedente](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>Approvazione in 2 fasi
Il diagramma seguente mostra l'esperienza degli approvatori della fase 1 e della fase 2 e le notifiche di posta elettronica ricevute durante il processo di richiesta:

![Flusso del processo di approvazione in due fasi](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>Tabella delle notifiche e-mail
Nella tabella seguente vengono fornite informazioni più dettagliate su ognuna di queste notifiche di posta elettronica. Per gestire queste e-mail, è possibile utilizzare le regole. Ad esempio, in Outlook, è possibile creare regole per spostare le e-mail in una cartella se l'oggetto contiene parole da questa tabella:

| # | Oggetto del messaggio di posta elettronica | Quando inviato | Inviato a |
| --- | --- | --- | --- |
| 1 | Azione richiesta: Approvare o rifiutare la richiesta inoltrata entro *[data]* | Questo messaggio di posta elettronica verrà inviato agli approvatori alternativi Stage-1 (dopo che la richiesta è stata riassegnata) per eseguire un'azione. | Approvatori alternativi Stage-1 |
| 2 | Azione richiesta: Approvare o rifiutare la richiesta entro *[data]* | Questo messaggio di posta elettronica verrà inviato al primo approvatore, se l'escalation è disabilitata, per intraprendere un'azione. | Primo approvatore |
| 3 | Promemoria: approvare o rifiutare la richiesta per *[data]* per *[richiedente]* | Questo messaggio di posta elettronica di promemoria verrà inviato al primo approvatore, se l'escalation è disabilitata. L'e-mail chiede loro di agire se non l'hanno fatto. | Primo approvatore |
| 4 | Approvare o rifiutare la richiesta entro *[ora]* il *[data]* | Questo messaggio di posta elettronica verrà inviato al primo approvatore (se l'escalation è abilitata) per eseguire un'azione. | Primo approvatore |
| 5 | Promemoria richiesto dall'azione: approvare o rifiutare la richiesta per *[data]* per *[richiedente]* | Questo messaggio di posta elettronica di promemoria verrà inviato al primo approvatore, se l'escalation è abilitata. L'e-mail chiede loro di agire se non l'hanno fatto. | Primo approvatore |
| 6 | La richiesta è scaduta per *[access_package]* | Questo messaggio di posta elettronica verrà inviato al primo approvatore e agli approvatori alternativi stage-1 dopo la scadenza della richiesta. | Primo approvatore, approvatori alternativi di fase 1 |
| 7 | Richiesta approvata per *[richiedente]* a *[access_package]* | Questo messaggio di posta elettronica verrà inviato al primo approvatore e agli approvatori alternativi di livello 1 al completamento della richiesta. | Primo approvatore, approvatori alternativi di fase 1 |
| 8 | Richiesta approvata per *[richiedente]* a *[access_package]* | Questo messaggio di posta elettronica verrà inviato al primo approvatore e agli approvatori alternativi di fase 1 di una richiesta in due fasi quando la richiesta di fase 1 viene approvata. | Primo approvatore, approvatori alternativi di fase 1 |
| 9 | Richiesta negata a *[access_package]* | Questa e-mail verrà inviata al richiedente quando la loro richiesta viene negata | Richiedente |
| 10 | La tua richiesta è scaduta per *[access_package]* | Questa e-mail verrà inviata al richiedente al termine di una richiesta singola o a due fasi. L'e-mail notifica al richiedente che la richiesta è scaduta. | Richiedente |
| 11 | Azione richiesta: Approvare o rifiutare la richiesta entro *[data]* | Questo messaggio di posta elettronica verrà inviato al secondo approvatore, se l'escalation è disabilitata, per intraprendere un'azione. | Secondo approvatore |
| 12 | Promemoria richiesto dall'azione: approvare o rifiutare la richiesta *entro [data]* | Questo messaggio di posta elettronica di promemoria verrà inviato al secondo approvatore, se l'escalation è disabilitata. La notifica chiede loro di intervenire se non l'hanno ancora fatto. | Secondo approvatore |
| 13 | Azione richiesta: Approvare o rifiutare la richiesta entro *[data]* per *[richiedente]* | Questo messaggio di posta elettronica verrà inviato al secondo approvatore, se l'escalation è abilitata, per eseguire un'azione. | Secondo approvatore |
| 14 | Promemoria richiesto dall'azione: approvare o rifiutare la richiesta per *[data]* per *[richiedente]* | Questo messaggio di posta elettronica di promemoria verrà inviato al secondo approvatore, se l'escalation è abilitata. La notifica chiede loro di intervenire se non l'hanno ancora fatto. | Secondo approvatore |
| 15 | Azione richiesta: Approvare o rifiutare la richiesta inoltrata entro *[data]* | Questo messaggio di posta elettronica verrà inviato agli approvatori alternativi di fase 2, se l'escalation è abilitata, per intervenire. | Approvatori alternativi Stage-2 |
| 16 | Richiesta approvata per *[richiedente]* a *[access_package]* | Questo messaggio di posta elettronica verrà inviato al secondo approvatore e agli approvatori alternativi di fase 2 all'approvazione della richiesta. | Secondo approvatore, approvatori alternativi Stage-2 |
| 17 | Una richiesta è scaduta per *[access_package]* | Questo messaggio di posta elettronica verrà inviato al secondo approvatore o agli approvatori alternativi, dopo la scadenza della richiesta. | Secondo approvatore, approvatori alternativi di fase 2 |
| 18 | Ora hai accesso a *[access_package]* | Questo messaggio di posta elettronica verrà inviato agli utenti finali per iniziare a utilizzare il loro accesso. | Richiedente |
| 19 | Estendere l'accesso per *[access_package]* per *[data]* | Questo messaggio di posta elettronica verrà inviato agli utenti finali prima della scadenza dell'accesso. | Richiedente |
| 20 | L'accesso è terminato per *[access_package]* | Questo messaggio di posta elettronica verrà inviato agli utenti finali dopo la scadenza dell'accesso. | Richiedente |

### <a name="access-request-emails"></a>Richieste di accesso e-mail di richiesta

Quando un richiedente invia una richiesta di accesso per un pacchetto di accesso configurato per richiedere l'approvazione, tutti gli approvatori aggiunti al criterio riceveranno una notifica tramite posta elettronica con i dettagli della richiesta. I dettagli nell'e-mail includono: organizzazione del nome del richiedente e motivazione aziendale; e la data di inizio e di fine dell'accesso richiesta (se disponibile). I dettagli includeranno anche quando la richiesta è stata inviata e quando la richiesta scadrà.

L'e-mail include un approvatore di link che può fare clic su per andare a Il mio accesso per approvare o rifiutare la richiesta di accesso. Di seguito è riportata una notifica tramite posta elettronica di esempio inviata al primo approvatore o al secondo approvatore (se è abilitata l'approvazione in due fasi) per completare una richiesta di accesso:Here is a sample email notification that is sent to the first approver or second approver (if 2-stage approval is enabled) to complete an access request:

![Approvare la richiesta di accesso alla posta elettronica del pacchetto](./media/entitlement-management-shared/approver-request-email.png)

I responsabili approvazione possono anche ricevere un'e-mail di promemoria. L'e-mail chiede all'approvatore di prendere una decisione sulla richiesta. Ecco una notifica e-mail di esempio che l'approvatore riceve per ricordare loro di eseguire un'azione:Here is a sample email notification the approver receives to remind them to take action:

![E-mail di richiesta di accesso promemoria](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>I responsabili degli approvatori alternativi richiedono e-mail

Se l'impostazione degli approvatori alternativi è abilitata e la richiesta è ancora in sospeso, verrà inoltrata. Gli approvatori alternativi riceveranno un'e-mail per approvare o rifiutare la richiesta. È possibile abilitare gli approvatori alternativi in fase 1 e fase 2. Di seguito è riportato un'e-mail di esempio della notifica ricevuta dagli approvatori alternativi:

![Gli approvatori alternativi richiedono l'email](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Sia l'approvatore che gli approvatori alternativi possono approvare o rifiutare la richiesta.

### <a name="approved-or-denied-emails"></a>Email approvate o negate

 Quando un approvatore riceve una richiesta di accesso inviata da un richiedente, può approvare o rifiutare la richiesta di accesso. L'approvatore deve aggiungere una motivazione aziendale per la decisione. Ecco un'e-mail di esempio inviata agli approvatori e agli approvatori alternativi dopo l'approvazione di una richiesta:Here is a sample email sent to the approvers and alternate approvers after a request is approved:

![Richiesta approvata per accedere alla posta elettronica del pacchetto](./media/entitlement-management-process/approver-request-email-approved.png)

Quando una richiesta di accesso viene approvata e viene eseguito il provisioning del relativo accesso, al richiedente viene inviata una notifica tramite posta elettronica che ora ha accesso al pacchetto di accesso. Ecco una notifica di posta elettronica di esempio che viene inviata a un richiedente quando viene loro concesso l'accesso a un pacchetto di accesso:Here is a sample email notification that is sent to a requestor when they's granted access to an access package:

![E-mail richiesta di accesso richiedente approvato](./media/entitlement-management-process/requestor-email-approved.png)

Quando una richiesta di accesso viene negata, viene inviata una notifica tramite posta elettronica al richiedente. Ecco una notifica e-mail di esempio che viene inviata a un richiedente quando la loro richiesta di accesso viene negata:Here is a sample email notification that is sent to a requestor when their access request is denied:

![Richiesta richiedente negata e-mail](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>Email di richiesta di approvazione in 2 fasi

Se l'approvazione in due fasi è abilitata, almeno due approvatori devono approvare la richiesta, una per ogni fase, prima che il richiedente possa ricevere l'accesso.

Durante la fase 1, il primo approvatore riceverà l'e-mail di richiesta di accesso e prenderà una decisione. Se approvano la richiesta, tutti i responsabili approvazione e gli approvatori alternativi nella fase 1 (se l'escalation è abilitata) riceveranno la notifica che la fase 1 è stata completata. Di seguito è riportato un messaggio di posta elettronica di esempio della notifica inviata al termine della fase 1:Here is a sample email of the notification that is sent when stage-1 is complete:

![E-mail di richiesta di accesso in 2 fasi](./media/entitlement-management-process/approver-request-email-2stage.png)

Dopo che il primo approvatore o l'approvatore alternativo hanno approvato la richiesta nella fase 1, inizia la fase 2. Durante la fase 2, il secondo approvatore riceverà l'e-mail di notifica della richiesta di accesso. Dopo che il secondo approvatore o gli approvatori alternativi nella fase 2 (se l'escalation è abilitata) decidono di approvare o rifiutare la richiesta, vengono inviati messaggi di posta elettronica di notifica al primo e al secondo approvatore e a tutti i responsabili approvazione nella fase 1 e nella fase 2, nonché al richiedente.

### <a name="expired-access-request-emails"></a>E-mail di richiesta di accesso scadute

Le richieste di accesso potrebbero scadere se nessun approvatore ha approvato o negato la richiesta. 

Quando la richiesta raggiunge la data di scadenza configurata e scade, non può più essere approvata o rifiutata dagli approvatori. Di seguito è riportato un messaggio di posta elettronica di esempio della notifica inviata a tutti i primi, la seconda (se è abilitata l'approvazione a due fasi) e gli approvatori alternativi:

![Responsabili approvazione messaggi di posta elettronica richiesta di accesso scaduti](./media/entitlement-management-process/approver-request-email-expired.png)

Una notifica e-mail viene inviata anche al richiedente, informandolo che la richiesta di accesso è scaduta e che devono inviare nuovamente la richiesta di accesso. Il diagramma seguente mostra l'esperienza del richiedente e le notifiche di posta elettronica che ricevono quando richiede di estendere l'accesso:The following diagram shows the experience of the requestor and the email notifications they receive when they request to extend access:

![Il richiedente estende il flusso del processo di accessoRequestor extend access process flow](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Ecco una notifica e-mail di esempio che viene inviata a un richiedente quando la richiesta di accesso è scaduta:Here is a sample email notification that is sent to a requestor when their access request is expired:

![Email richiesta di accesso scaduta del richiedente](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Passaggi successivi

- [Richiedere l'accesso a un pacchetto di accessoRequest access to an access package](entitlement-management-request-access.md)
- [Approvare o rifiutare le richieste di accesso](entitlement-management-request-approve.md)
