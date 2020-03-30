---
title: Rinnovare le assegnazioni dei ruoli delle risorse di Azure in PIM - Azure AD Documenti Microsoft
description: Informazioni su come estendere o rinnovare le assegnazioni dei ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 577c028582bc3b23d13e71522bb83db558065ee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022903"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Estendere o rinnovare le assegnazioni dei ruoli delle risorse di Azure in Gestione delle identità con privilegiExtend or renew Azure resource role assignments in Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) fornisce controlli per gestire il ciclo di vita di accesso e assegnazione per le risorse di Azure.Azure Active Directory (Azure AD) Privileged Identity Management (PIM) provides controls to manage the access and assignment lifecycle for Azure resources. Gli amministratori possono assegnare ruoli utilizzando le proprietà di data e ora di inizio e di fine. Quando si avvicina la fine dell'assegnazione, Gestione identità privilegiate invia notifiche tramite posta elettronica agli utenti o ai gruppi interessati. Inoltre, invia notifiche tramite posta elettronica agli amministratori della risorsa per garantire che venga mantenuto l'accesso appropriato. Anche qualora l'accesso non venga esteso, le assegnazioni possono essere rinnovate e rimanere visibili nello stato scaduti fino a 30 giorni.

## <a name="who-can-extend-and-renew"></a>Chi può estendere e rinnovare?

Solo gli amministratori della risorsa possono estendere o rinnovare le assegnazioni di ruolo. L'utente o il gruppo interessato può richiedere di estendere i ruoli che stanno per scadere e richiedere di rinnovare i ruoli già scaduti.

## <a name="when-are-notifications-sent"></a>Quando vengono inviate le notifiche?

Gestione identità con privilegi invia notifiche tramite posta elettronica agli amministratori e agli utenti interessati o a gruppi di ruoli che scadono entro 14 giorni e un giorno prima della scadenza. Alla scadenza ufficiale di un'assegnazione, viene inviato un ulteriore messaggio di posta elettronica.

Gli amministratori ricevono notifiche quando un utente o un gruppo a cui è assegnato un ruolo in scadenza o scaduto richiede di estendere o rinnovare. Quando un amministratore specifico risolve la richiesta, tutti gli altri amministratori ricevono la notifica con la decisione di risoluzione (approvazione o rifiuto). Quindi l'utente o il gruppo richiedente riceve una notifica della decisione.

## <a name="extend-role-assignments"></a>Estendere le assegnazioni di ruoli

I passaggi seguenti descrivono la procedura per la richiesta, la risoluzione o l'amministrazione di un'estensione o del rinnovo di un'assegnazione di ruolo.

### <a name="self-extend-expiring-assignments"></a>Assegnazioni in scadenza auto-estendere

Gli utenti o i gruppi assegnati a un ruolo possono estendere le assegnazioni di ruolo in scadenza direttamente dalla scheda **Idonee** o **Attivo** nella pagina **Ruoli personali** di una risorsa e dalla pagina **Ruoli personali** di livello superiore del portale Gestione identità con privilegi. Gli utenti o i gruppi possono richiedere di estendere i ruoli idonei e attivi (assegnati) che scadono nei 14 giorni successivi.

![Risorse di Azure - Pagina Ruoli personali che elenca i ruoli idonei con una colonna Azione](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Quando la data/ora di fine dell'assegnazione è entro 14 giorni, il pulsante **Estendi** diventa un collegamento attivo nell'interfaccia utente. Nell'esempio seguente, si suppone che la data corrente sia il 27 marzo.

![Colonna Azione con collegamenti ad Attiva o Estendi](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Per richiedere un'estensione di questa assegnazione di ruolo, selezionare **Estendi** per aprire il modulo di richiesta.

![Estendere il riquadro di assegnazione dei ruoli con una casella MotivoExtend role assignment pane with a Reason box](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Espandere **Dettagli dell'assegnazione** per visualizzare le informazioni sull'assegnazione originale. Immettere un motivo per la richiesta di estensione e fare clic su **Estendi**.

>[!NOTE]
>È consigliabile includere i dettagli del motivo per cui è necessaria l'estensione e il tempo di estensione da concedere (se noto).

![Estendi riquadro di assegnazione dei ruoli con i dettagli dell'assegnazione espansi](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

In pochi istanti, gli amministratori delle risorse ricevono una notifica tramite posta elettronica che richiede di esaminare la richiesta di estensione. Se una richiesta di estensione è già stata inviata, nel portale viene visualizzata una notifica di Azure.If a request to extend has already been submitted, an Azure notification appears in the portal.

![Notifica che spiega che esiste già un'estensione di assegnazione di ruolo in sospeso](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Vai alla pagina **Richieste in sospeso** per visualizzare lo stato della tua richiesta o per annullarla.

![Risorse di Azure: pagina delle richieste in sospeso in cui sono elencate le richieste in sospeso e un collegamento a AnnullaAzure resources - Pending requests page listing any pending requested and a link to Cancel](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Estensione approvata dall'amministratore

Quando un utente o un gruppo invia una richiesta di estensione di un'assegnazione di ruolo, gli amministratori delle risorse ricevono una notifica tramite posta elettronica contenente i dettagli dell'assegnazione originale e il motivo della richiesta. La notifica include un collegamento diretto alla richiesta che l'amministratore dovrà approvare o rifiutare.

Oltre a utilizzare il collegamento dalla posta elettronica, gli amministratori possono approvare o rifiutare le richieste accedendo al portale di amministrazione di Gestione identità con privilegi e selezionando **Approva richieste** nel riquadro sinistro.

![Risorse di Azure - Approvare le richieste della pagina che elenca le richieste e i collegamenti per approvare o rifiutare](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando un amministratore seleziona **Approva** o **Nega**, vengono visualizzati i dettagli della richiesta, insieme a un campo per fornire una motivazione aziendale per i registri di controllo.

![Approvare la richiesta di assegnazione di ruolo con motivo del richiedente, tipo di assegnazione, ora di inizio, ora di fine e motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Quando approvano una richiesta di estensione di un'assegnazione di ruolo, gli amministratori delle risorse possono scegliere una nuova data di inizio e di fine e un tipo di assegnazione. La modifica del tipo di assegnazione potrebbe essere necessaria se l'amministratore vuole fornire accesso limitato per completare un'attività specifica (un giorno, ad esempio). In questo esempio, l'amministratore può modificare l'assegnazione da **Idonea** ad **Attiva**. Ciò significa che possono fornire l'accesso al richiedente senza necessità di attivazione.

### <a name="admin-initiated-extension"></a>Estensione avviata dall'amministratore

Se un utente assegnato a un ruolo non richiede un'estensione per l'assegnazione di ruolo, un amministratore può estendere un'assegnazione per conto dell'utente. Le estensioni amministrative di assegnazione dei ruoli non richiedono l'approvazione, ma le notifiche vengono inviate a tutti gli altri amministratori dopo l'estensione del ruolo.

Per estendere un'assegnazione di ruolo, passare al ruolo della risorsa o alla visualizzazione dell'assegnazione in Gestione identità privilegiate. Trovare l'assegnazione che richiede un'estensione. Quindi selezionare **Estendi** nella colonna relativa all'azione.

![Risorse di Azure - pagina delle assegnazioni che elencano i ruoli idonei con collegamenti da estendereAzure resources - assignments page listing eligible roles with links to extend](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Rinnovare le assegnazioni di ruolo

Anche se concettualmente simile alla richiesta di estensione, il processo di rinnovo di un'assegnazione di ruolo scaduto è diverso da quello appena citato. Attenendosi alla procedura seguente, le assegnazioni e gli amministratori possono rinnovare l'accesso ai ruoli scaduti quando necessario.

### <a name="self-renew"></a>Auto-rinnovamento

Gli utenti che non possono più accedere alle risorse possono accedere fino a 30 giorni di cronologia delle assegnazioni scadute. A tale scopo, passare a **Ruoli personali** nel riquadro sinistro e quindi selezionare la scheda **Ruoli scaduti** nella sezione dei ruoli delle risorse di Azure.

![Pagina Ruoli personali - Scheda Ruoli scaduti](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

L'elenco dei ruoli visualizzati include le impostazioni predefinite per i **Ruoli idonei**. Usare il menu a discesa per alternare i ruoli assegnati Idonea e Attiva.

Per richiedere il rinnovo per qualsiasi assegnazione di ruolo nell'elenco, selezionare l'azione **Rinnova**. Quindi, specificare un motivo per la richiesta. È utile fornire una durata oltre a qualsiasi contesto aggiuntivo o una motivazione aziendale che può aiutare l'amministratore delle risorse a decidere di approvare o rifiutare.

![Riquadro Rinnova assegnazione ruolo con la casella Motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Dopo l'invio della richiesta, gli amministratori delle risorse ricevono una notifica della presenza di una richiesta di rinnovo di un'assegnazione di ruolo in sospeso.

### <a name="admin-approves"></a>Approvazione degli amministratori

Gli amministratori delle risorse possono accedere alla richiesta di rinnovo dal collegamento nella notifica di posta elettronica o accedendo a Gestione identità con privilegi dal portale di Azure e selezionando **Approva richieste** dal riquadro sinistro.

![Risorse di Azure - Approvare le richieste della pagina che elenca le richieste e i collegamenti per approvare o rifiutare](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando un amministratore seleziona **Approva** o **Nega**, i dettagli della richiesta vengono visualizzati insieme a un campo per fornire una giustificazione aziendale per i registri di controllo.

![Approvare la richiesta di assegnazione di ruolo con motivo del richiedente, tipo di assegnazione, ora di inizio, ora di fine e motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Quando approvano una richiesta di rinnovo di un'assegnazione di ruolo, gli amministratori delle risorse devono immettere una nuova data di inizio, di fine e un tipo di assegnazione.

### <a name="admin-renew"></a>Rinnovo richiesto dagli amministratori

Gli amministratori delle risorse possono rinnovare le assegnazioni di ruolo scaduto dalla scheda **Membri** nel menu di spostamento a sinistra di una risorsa. Possono anche rinnovare le assegnazioni di ruolo scaduto nella scheda dei ruoli **Scaduti** di un ruolo di risorse.

Dalla schermata **Membri** selezionare **Ruoli scaduti** per visualizzare un elenco di tutte le assegnazioni di ruolo scadute.

![Risorse di Azure - Pagina Membri che elenca i ruoli scaduti con collegamenti per il rinnovoAzure resources - Members page listing expired roles with links to renew](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Passaggi successivi

- [Approvare o rifiutare le richieste per i ruoli delle risorse di Azure in Gestione delle identità con privilegiApprove or deny requests for Azure resource roles in Privileged Identity Management](pim-resource-roles-approval-workflow.md)
- [Configurare le impostazioni del ruolo delle risorse di Azure in Gestione delle identità con privilegiConfigure Azure resource role settings in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
