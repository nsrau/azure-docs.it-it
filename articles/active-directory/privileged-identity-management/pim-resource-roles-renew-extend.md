---
title: Privileged Identity Management per risorse di Azure - Estendere e rinnovare i ruoli | Microsoft Docs
description: Questo documento descrive come estendere e rinnovare i ruoli delle risorse di Azure per le risorse PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 2547b3793688eb51a4114f30bfcf61a9402f2cd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---extend-or-renew"></a>Privileged Identity Management - Ruoli delle risorse - Estendere e rinnovare

PIM per le risorse di Azure introduce nuovi controlli per la gestione del ciclo di vita di accesso e assegnazione per le risorse di Azure. Gli amministratori possono assegnare l'appartenenza usando proprietà di data/ora di inizio e fine. Quando si avvicina la fine dell'assegnazione, PIM invia notifiche tramite posta elettronica ai membri interessati (potrebbe trattarsi di un utente o gruppo) e agli amministratori della risorsa per garantire che venga mantenuto l'accesso appropriato. Nel caso in cui l'accesso non viene esteso a causa di inattività, le assegnazioni possono essere rinnovate e restano visibili in uno stato scaduto fino a 30 giorni.

## <a name="who-can-extend-and-renew"></a>Chi può estendere e rinnovare?

Solo gli amministratori della risorsa possono estendere o rinnovare le assegnazioni di ruolo. Il membro interessato può richiedere di estendere i ruoli in procinto di scadere e richiedere il rinnovo dei ruoli già scaduti.

## <a name="when-are-notifications-sent"></a>Quando vengono inviate le notifiche?

PIM invia notifiche tramite posta elettronica agli amministratori e ai membri interessati dei ruoli in scadenza entro 14 giorni e un giorno prima della scadenza. Alla scadenza ufficiale di un'assegnazione, viene inviato un ulteriore messaggio di posta elettronica. 

Gli amministratori ricevono notifiche quando un membro di un ruolo in scadenza o scaduto richiede l'estensione o il rinnovo. Quando un amministratore specifico risolve la richiesta, tutti gli altri amministratori ricevono una notifica della decisione di risoluzione (approvazione o rifiuto) e il membro richiedente riceve notifica della decisione. 

## <a name="extend-role-assignments"></a>Estendere le assegnazioni di ruoli

I passaggi seguenti descrivono la procedura e l'interfaccia utente per la richiesta, la risoluzione o l'amministrazione di un'estensione o del rinnovo di un'assegnazione di ruolo. 

### <a name="member-extend"></a>Estensione richiesta dai membri

I membri di un'assegnazione di ruolo possono richiedere di estendere le assegnazioni di ruolo in scadenza direttamente dalla scheda "Idoneo" o "Attivo" nella pagina "Ruoli personali" di una risorsa e dall'area Ruoli personali di primo livello del portale PIM. I membri possono richiedere l'estensione dei ruoli idonei e attivi (assegnati) con scadenza entro 14 giorni.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

Quando la data/ora di fine dell'assegnazione è entro 14 giorni, il pulsante "Estendi" diventa un collegamento attivo nell'interfaccia utente. Nell'esempio riportato di seguito, si suppone che la data corrente sia il 27 marzo.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

Per richiedere un'estensione di questa assegnazione di ruolo, fare clic su "Estendi" per aprire il modulo di richiesta.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

Espandere "Dettagli dell'assegnazione" per visualizzare le informazioni sull'assegnazione originale. Immettere un motivo per la richiesta di estensione e fare clic su "Estendi".

>[!Note]
>È consigliabile includere i dettagli del motivo per cui è necessaria l'estensione e il tempo di estensione richiesto (se noto).

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

In pochi istanti, gli amministratori delle risorse riceveranno una notifica tramite posta elettronica con la richiesta di revisione della richiesta di estensione. Se è già stata inviata una richiesta di estensione, verrà visualizzata una notifica di tipo avviso popup nella parte superiore del portale di Azure che spiega l'errore.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

Passare alla scheda "Richieste in sospeso" nel menu di spostamento a sinistra per visualizzare lo stato o annullare la richiesta.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>Approvazione degli amministratori

Quando un membro invia una richiesta per estendere un'assegnazione di ruolo, gli amministratori delle risorse ricevono una notifica tramite posta elettronica che contiene i dettagli dell'assegnazione originale e il motivo specificato dal richiedente. La notifica include un collegamento diretto alla richiesta che l'amministratore dovrà approvare o rifiutare. 

Per approvare o rifiutare le richieste, oltre a seguire il collegamento dal messaggio di posta elettronica, gli amministratori possono passare al portale di amministrazione di PIM e selezionare "Approva richieste" dal menu di spostamento a sinistra.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Quando un amministratore seleziona Approva o Rifiuta, vengono visualizzati i dettagli della richiesta insieme a un campo per fornire una giustificazione per i log di controllo.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Quando approvano una richiesta di estensione di un'assegnazione di ruolo, gli amministratori delle risorse possono scegliere una nuova data/ora di inizio e fine e un tipo di assegnazione. La modifica del tipo di assegnazione potrebbe essere necessaria se l'amministratore vuole fornire accesso limitato per completare un'attività specifica (un giorno, ad esempio). In questo esempio, l'amministratore può modificare l'assegnazione da Idoneo ad Attivo consentendo al richiedente l'accesso senza necessità di eseguire l'attivazione.

### <a name="admin-extend"></a>Estensione richiesta dall'amministratore

Nel caso un membro del ruolo si dimentichi o non sia in grado di richiedere l'estensione dell'appartenenza al ruolo, un amministratore può estendere un'assegnazione per conto del membro. Le estensioni amministrative dell'appartenenza ai ruoli non richiedono l'approvazione, ma vengono inviate notifiche a tutti gli altri amministratori al termine dell'estensione del ruolo.

Per estendere l'appartenenza a un ruolo, passare al ruolo della risorsa o alla visualizzazione dei membri in PIM. Trovare il membro che richiede un'estensione e fare clic su "Estendi" nella colonna Azione.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>Rinnovare le assegnazioni di ruolo

Anche se concettualmente simile, il processo per rinnovare un'assegnazione di ruolo scaduta è diverso da quello per richiedere un'estensione, sia per i membri che per gli amministratori. Usando la procedura descritta di seguito i membri e gli amministratori possono rinnovare l'accesso ai ruoli scaduti quando necessario.

### <a name="member-renew"></a>Rinnovo richiesto dai membri

I membri che non hanno più accesso alle risorse possono accedere fino a 30 giorni della cronologia delle assegnazioni scadute passando a Ruoli personali nel riquadro di spostamento a sinistra di PIM e selezionando la scheda "Ruoli scaduti" nella sezione dei ruoli delle risorse di Azure.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

L'elenco dei ruoli visualizzati include per impostazione predefinita le assegnazioni di tipo Idoneo. Usare l'elenco a discesa per alternare i ruoli assegnati Idoneo e Attivo.

Per richiedere il rinnovo per qualsiasi assegnazione di ruolo nell'elenco, selezionare l'azione "Rinnova" e specificare un motivo per la richiesta. È utile indicare una durata oltre a qualsiasi altra informazione di contesto che possa essere utile all'amministratore delle risorse per decidere se approvare o rifiutare la richiesta.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

Dopo l'invio della richiesta, gli amministratori delle risorse ricevono una notifica della presenza di una richiesta di rinnovo di un'assegnazione di ruolo in sospeso.

### <a name="admin-approves"></a>Approvazione degli amministratori

Gli amministratori delle risorse possono accedere alla richiesta di rinnovo dal collegamento nella notifica tramite posta elettronica o accedendo a PIM dal portale di Azure e selezionando "Approva richieste" dal menu di spostamento a sinistra.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Quando un amministratore seleziona Approva o Rifiuta, vengono visualizzati i dettagli della richiesta insieme a un campo per fornire una giustificazione per i log di controllo.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Quando approvano una richiesta di rinnovo di un'assegnazione di ruolo, gli amministratori delle risorse devono immettere una nuova data/ora di inizio e fine e un tipo di assegnazione. 

### <a name="admin-renew"></a>Rinnovo richiesto dagli amministratori

Gli amministratori delle risorse possono rinnovare le assegnazioni di ruolo scadute dalla scheda Membri nel menu di spostamento a sinistra di una risorsa o dall'interno della scheda Ruoli scaduti di un ruolo di risorse.

Dalla schermata Membri selezionare Ruoli scaduti per visualizzare un elenco di tutte le assegnazioni di ruolo scadute.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>Passaggi successivi

[Richiedere l'approvazione per l'attivazione](pim-resource-roles-approval-workflow.md)

[Attivare un ruolo](pim-resource-roles-use-the-audit-log.md)


