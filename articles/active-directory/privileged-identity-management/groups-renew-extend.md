---
title: Rinnovare il proprietario del gruppo scaduto delle assegnazioni dei membri in Privileged Identity Management-Azure AD | Microsoft Docs
description: Informazioni su come estendere o rinnovare le assegnazioni di gruppi assegnabili ai ruoli in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 06/30/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cfbb0be22dee4550050d6af10314f3a3bb1f583
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505991"
---
# <a name="extend-or-renew-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Estendi o rinnova le assegnazioni dei gruppi di accesso con privilegi (anteprima) in Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) fornisce controlli per gestire l'accesso e il ciclo di vita dell'assegnazione per i gruppi di accesso con privilegi. Gli amministratori possono assegnare ruoli utilizzando le proprietà di data e ora di inizio e di fine. Quando l'estremità di assegnazione si avvicina, Privileged Identity Management invia notifiche tramite posta elettronica agli utenti o ai gruppi interessati. Inoltre, invia notifiche tramite posta elettronica agli amministratori della risorsa per garantire che venga mantenuto l'accesso appropriato. Anche qualora l'accesso non venga esteso, le assegnazioni possono essere rinnovate e rimanere visibili nello stato scaduti fino a 30 giorni.

## <a name="who-can-extend-and-renew"></a>Utenti che possono estendere e rinnovare

Solo gli amministratori della risorsa possono estendere o rinnovare le assegnazioni di gruppi di accesso con privilegi. L'utente o il gruppo interessato può richiedere di estendere le assegnazioni che stanno per scadere e richiedere di rinnovare le assegnazioni già scadute.

## <a name="when-notifications-are-sent"></a>Quando vengono inviate le notifiche

Privileged Identity Management invia notifiche tramite posta elettronica agli amministratori e agli utenti interessati di assegnazioni di gruppi di accesso con privilegi che scadono:

- Entro 14 giorni prima della scadenza
- Un giorno prima della scadenza
- Alla scadenza di un'assegnazione

Gli amministratori ricevono notifiche quando un utente o un gruppo richiede di estendere o rinnovare un'assegnazione in scadenza o scaduta. Quando un amministratore risolve la richiesta, tutti gli amministratori e l'utente richiedente ricevono una notifica dell'approvazione o del rifiuto.

## <a name="extend-group-assignments"></a>Estendi le assegnazioni di gruppo

I passaggi seguenti illustrano il processo di richiesta, risoluzione o amministrazione di un'estensione o di un rinnovo di un'assegnazione di gruppo.

### <a name="self-extend-expiring-assignments"></a>Assegnazioni con scadenza automatica

Gli utenti assegnati a un gruppo di accesso con privilegi possono estendere le assegnazioni di gruppo in scadenza direttamente dalla scheda **idonea** o **attiva** nella pagina **assegnazioni** del gruppo. Gli utenti o i gruppi possono richiedere di estendere le assegnazioni idonee e attive che scadono nei 14 giorni successivi.

![Pagina ruoli personali elenco di lei assegnati semplicemente idonei con una colonna azione](media/groups-renew-extend/self-extend-group-assignment.png)

Quando la data/ora di fine dell'assegnazione è entro 14 giorni, è disponibile il comando **Estendi** . Per richiedere un'estensione di un'assegnazione di gruppo, selezionare **Estendi** per aprire il modulo di richiesta.

![Riquadro Estendi assegnazione gruppo con una casella motivo e dettagli](media/groups-renew-extend/extend-request-details-group-assignment.png)

>[!NOTE]
>È consigliabile includere i dettagli del motivo per cui è necessaria l'estensione e il tempo di estensione da concedere (se noto).

In pochi istanti gli amministratori ricevono una notifica di posta elettronica che richiede la revisione della richiesta di estensione. Se una richiesta di estensione è già stata inviata, nel portale viene visualizzata una notifica di Azure.

Per visualizzare lo stato o annullare la richiesta, aprire la pagina **richieste in sospeso** per l'assegnazione del gruppo.

![Assegnazioni di gruppi di accesso con privilegi-pagina richieste in sospeso che Visualizza il collegamento da annullare](media/groups-renew-extend/group-assignment-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Estensione approvata dall'amministratore

Quando un utente o un gruppo invia una richiesta per estendere un'assegnazione di gruppo, gli amministratori ricevono una notifica tramite posta elettronica contenente i dettagli dell'assegnazione originale e il motivo della richiesta. La notifica include un collegamento diretto alla richiesta che l'amministratore dovrà approvare o rifiutare.

Oltre a usare il collegamento seguente da un messaggio di posta elettronica, gli amministratori possono approvare o rifiutare le richieste accedendo al portale di amministrazione Privileged Identity Management e selezionando **Approva richieste** nel riquadro sinistro.

![Assegnazioni dei gruppi di accesso con privilegi: pagina Approva richieste con elenco di richieste e collegamenti da approvare o negare](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

Quando un amministratore seleziona **approva** o **rifiuta**, vengono visualizzati i dettagli della richiesta, insieme a un campo per fornire una giustificazione aziendale per i log di controllo.

![Approva la richiesta di assegnazione del gruppo con motivo del richiedente, tipo di assegnazione, ora di inizio, ora di fine e motivo](media/groups-renew-extend/group-assignment-extend-admin-approve-reason.png)

Quando si approva una richiesta di estensione di un'assegnazione di gruppo, gli amministratori delle risorse possono scegliere una nuova data di inizio, una data di fine e un tipo di assegnazione. La modifica del tipo di assegnazione potrebbe essere necessaria se l'amministratore vuole fornire accesso limitato per completare un'attività specifica (un giorno, ad esempio). In questo esempio, l'amministratore può modificare l'assegnazione da **Idonea** ad **Attiva**. Ciò significa che possono fornire l'accesso al richiedente senza necessità di attivazione.

### <a name="admin-initiated-extension"></a>Estensione avviata dall'amministratore

Se un utente assegnato a un gruppo non richiede un'estensione per l'assegnazione del gruppo, un amministratore può estendere un'assegnazione per conto dell'utente. Le estensioni amministrative dell'assegnazione di gruppo non richiedono l'approvazione, ma le notifiche vengono inviate a tutti gli altri amministratori dopo che l'assegnazione è stata estesa.

Per estendere un'assegnazione di gruppo, passare alla visualizzazione assegnazione in Privileged Identity Management. Individuare l'assegnazione che richiede un'estensione. Quindi selezionare **Estendi** nella colonna relativa all'azione.

![Pagina assegnazioni elencando le assegnazioni di gruppi idonee con collegamenti da estendere](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

## <a name="renew-group-assignments"></a>Rinnovare le assegnazioni di gruppo

Sebbene concettualmente simile al processo di richiesta di un'estensione, il processo di rinnovo di un'assegnazione di gruppo scaduto è diverso. Usando i passaggi seguenti, le assegnazioni e gli amministratori possono rinnovare l'accesso alle assegnazioni scadute, quando necessario.

### <a name="self-renew"></a>Rinnovo automatico

Gli utenti che non possono più accedere alle risorse possono accedere a un massimo di 30 giorni di cronologia di assegnazione scaduta. A tale scopo, passare a **ruoli personali** nel riquadro sinistro, quindi selezionare la scheda le **assegnazioni scadute** .

![Pagina ruoli personali-scheda assegnazioni scadute](media/groups-renew-extend/groups-renew-from-my-roles.png)

L'elenco di assegnazioni indicato per impostazione predefinita è le **assegnazioni idonee**. Usare il menu a discesa per passare tra le assegnazioni idonee e quelle attive.

Per richiedere il rinnovo per qualsiasi assegnazione di gruppo nell'elenco, selezionare l'azione **rinnova** . Quindi, specificare un motivo per la richiesta. È utile fornire una durata oltre a qualsiasi contesto aggiuntivo o a una motivazione aziendale che può aiutare l'amministratore delle risorse a decidere di approvare o negare.

![Riquadro di assegnazione del gruppo di rinnovo che mostra la casella motivo](media/groups-renew-extend/groups-renew-request-form.png)

Dopo l'invio della richiesta, gli amministratori delle risorse ricevono una notifica di una richiesta in sospeso per rinnovare un'assegnazione di gruppo.

### <a name="admin-approves"></a>Approvazione degli amministratori

Gli amministratori delle risorse possono accedere alla richiesta di rinnovo dal collegamento nella notifica tramite posta elettronica o accedendo a Privileged Identity Management dal portale di Azure e selezionando **Approva richieste** dal riquadro sinistro.

Quando un amministratore seleziona **approva** o **rifiuta**, vengono visualizzati i dettagli della richiesta insieme a un campo per fornire una giustificazione aziendale per i log di controllo.

Quando si approva una richiesta di rinnovo di un'assegnazione di gruppo, gli amministratori delle risorse devono immettere una nuova data di inizio, una data di fine e un tipo di assegnazione.

## <a name="next-steps"></a>Passaggi successivi

- [Approva o rifiuta le richieste per le assegnazioni di gruppi di accesso con privilegi in Privileged Identity Management](groups-approval-workflow.md)
- [Configurare le impostazioni del gruppo di accesso con privilegi in Privileged Identity Management](groups-role-settings.md)
