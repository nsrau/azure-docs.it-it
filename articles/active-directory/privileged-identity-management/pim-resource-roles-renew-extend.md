---
title: Estendere o rinnovare le assegnazioni di ruolo di risorse di Azure in PIM - Azure Active Directory | Microsoft Docs
description: Informazioni su come estendere o rinnovare le assegnazioni dei ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 147b1714c88fd93a3098ecf7a28164a227af29de
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476292"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-pim"></a>Estendere o rinnovare le assegnazioni dei ruoli delle risorse di Azure in PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) introduce nuovi controlli per la gestione del ciclo di vita di assegnazione e accesso per le risorse di Azure. Gli amministratori possono assegnare l'appartenenza usando proprietà di data/ora di inizio e fine. Quando ci si avvicina al termine dell'assegnazione, PIM invia notifiche tramite posta elettronica agli utenti o ai gruppi interessati. Inoltre, invia notifiche tramite posta elettronica agli amministratori della risorsa per garantire che venga mantenuto l'accesso appropriato. Anche qualora l'accesso non venga esteso, le assegnazioni possono essere rinnovate e rimanere visibili nello stato scaduti fino a 30 giorni.

## <a name="who-can-extend-and-renew"></a>Chi può estendere e rinnovare?

Solo gli amministratori della risorsa possono estendere o rinnovare le assegnazioni di ruolo. Il membro interessato può richiedere di estendere i ruoli in procinto di scadere e richiedere il rinnovo dei ruoli già scaduti.

## <a name="when-are-notifications-sent"></a>Quando vengono inviate le notifiche?

PIM invia notifiche tramite posta elettronica agli amministratori e ai membri interessati dei ruoli in scadenza entro 14 giorni e un giorno prima della scadenza. Alla scadenza ufficiale di un'assegnazione, viene inviato un ulteriore messaggio di posta elettronica. 

Gli amministratori ricevono notifiche quando un membro di un ruolo in scadenza o scaduto richiede l'estensione o il rinnovo. Quando un amministratore specifico risolve la richiesta, tutti gli altri amministratori ricevono la notifica con la decisione di risoluzione (approvazione o rifiuto). Quindi il membro richiedente riceve una notifica della decisione. 

## <a name="extend-role-assignments"></a>Estendere le assegnazioni di ruoli

I passaggi seguenti descrivono la procedura per la richiesta, la risoluzione o l'amministrazione di un'estensione o del rinnovo di un'assegnazione di ruolo. 

### <a name="member-extend"></a>Estensione richiesta dai membri

I membri di un'assegnazione di ruolo possono estendere le assegnazioni di ruolo in scadenza direttamente dalla scheda **Idonea** o **Attiva** nella pagina **Ruoli personali** di una risorsa e dalla pagina **Ruoli personali** di primo livello del portale PIM. I membri possono richiedere l'estensione dei ruoli idonei e attivi (assegnati) con scadenza entro 14 giorni.

![Le risorse di Azure - elenco di ruoli idonei con una colonna dell'azione di pagina ruoli personali](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Quando la data/ora di fine dell'assegnazione è entro 14 giorni, il pulsante **Estendi** diventa un collegamento attivo nell'interfaccia utente. Nell'esempio seguente, si suppone che la data corrente sia il 27 marzo.

![Colonna dell'azione con collegamenti a attiva o Estendi](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Per richiedere un'estensione di questa assegnazione di ruolo, selezionare **Estendi** per aprire il modulo di richiesta.

![Estendere riquadro di assegnazione di ruolo con una casella di motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Espandere **Dettagli dell'assegnazione** per visualizzare le informazioni sull'assegnazione originale. Immettere un motivo per la richiesta di estensione e fare clic su **Estendi**.

>[!Note]
>È consigliabile includere i dettagli del motivo per cui è necessaria l'estensione e il tempo di estensione da concedere (se noto).

![Estendere il riquadro di assegnazione di ruolo con i dettagli dell'assegnazione espansi](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

In pochi istanti, gli amministratori delle risorse riceveranno una notifica tramite posta elettronica con la richiesta di revisione della richiesta di estensione. Se è già stata inviata una richiesta di estensione, verrà visualizzata una notifica di tipo avviso popup nella parte superiore del portale di Azure che spiega l'errore.

![Notifica che spiega che è già presente in attesa estensione di assegnazione di ruolo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Andare alla **richieste in sospeso** pagina nel riquadro sinistro per visualizzare lo stato della richiesta o per annullare l'operazione.

![Risorse di Azure - in sospeso richiede una pagina elenco qualsiasi in sospeso richieste e un collegamento per annullare](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approve"></a>Approvazione degli amministratori

Quando un membro invia una richiesta per estendere un'assegnazione di ruolo, gli amministratori delle risorse ricevono una notifica tramite posta elettronica che contiene i dettagli dell'assegnazione originale e il motivo specificato dal richiedente. La notifica include un collegamento diretto alla richiesta che l'amministratore dovrà approvare o rifiutare. 

Oltre a seguire il collegamento dal messaggio di posta elettronica, gli amministratori possono approvare o rifiutare le richieste andando al portale di amministrazione di PIM e selezionando **Approva richieste** dal riquadro a sinistra.

![Le risorse di Azure - approvare le richieste di pagina elenco richieste e i collegamenti per approvare o rifiutare](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando un amministratore seleziona **Approva** o **Rifiuta**, vengono visualizzati i dettagli della richiesta insieme a un campo per fornire una giustificazione per i log di controllo.

![Approva richiesta di assegnazione di ruolo con il motivo del richiedente, tipo di assegnazione, ora di inizio, ora di fine e motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Quando approvano una richiesta di estensione di un'assegnazione di ruolo, gli amministratori delle risorse possono scegliere una nuova data di inizio e di fine e un tipo di assegnazione. La modifica del tipo di assegnazione potrebbe essere necessaria se l'amministratore vuole fornire accesso limitato per completare un'attività specifica (un giorno, ad esempio). In questo esempio, l'amministratore può modificare l'assegnazione da **Idonea** ad **Attiva**. Ciò significa che possono fornire l'accesso al richiedente senza necessità di attivazione.

### <a name="admin-extend"></a>Estensione richiesta dall'amministratore

Se un membro del ruolo si dimentica o non è in grado di richiedere l'estensione dell'appartenenza al ruolo, un amministratore può estendere un'assegnazione per conto del membro. Le estensioni amministrative dell'appartenenza ai ruoli non richiedono l'approvazione, ma vengono inviate notifiche a tutti gli altri amministratori al termine dell'estensione del ruolo.

Per estendere l'appartenenza a un ruolo, passare al ruolo della risorsa o alla visualizzazione dei membri in PIM. Trovare il membro che necessita di un'estensione. Quindi selezionare **Estendi** nella colonna relativa all'azione.

![Le risorse di Azure - elenco di ruoli idonei con collegamenti a estendere di pagina membri](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Rinnovare le assegnazioni di ruolo

Anche se concettualmente simile alla richiesta di estensione, il processo di rinnovo di un'assegnazione di ruolo scaduto è diverso da quello appena citato. Usando la procedura descritta di seguito i membri e gli amministratori possono rinnovare l'accesso ai ruoli scaduti quando necessario.

### <a name="member-renew"></a>Rinnovo richiesto dai membri

I membri che non possono più accedere alle risorse possono accedere alla cronologia di assegnazione scaduti fino a 30 giorni. A tale scopo, passare a **Ruoli personali** nel riquadro sinistro e quindi selezionare la scheda **Ruoli scaduti** nella sezione dei ruoli delle risorse di Azure.

![La pagina ruoli - scheda ruoli scaduti](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

L'elenco dei ruoli visualizzati include le impostazioni predefinite per i **Ruoli idonei**. Usare il menu a discesa per alternare i ruoli assegnati Idonea e Attiva.

Per richiedere il rinnovo per qualsiasi assegnazione di ruolo nell'elenco, selezionare l'azione **Rinnova**. Quindi, specificare un motivo per la richiesta. È utile indicare una durata oltre a qualsiasi altra informazione di contesto che possa essere utile all'amministratore delle risorse per decidere se approvare o rifiutare la richiesta.

![Riquadro di assegnazione di ruolo che mostra la finestra di motivo di rinnovo](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Dopo l'invio della richiesta, gli amministratori delle risorse ricevono una notifica della presenza di una richiesta di rinnovo di un'assegnazione di ruolo in sospeso.

### <a name="admin-approves"></a>Approvazione degli amministratori

Gli amministratori delle risorse possono accedere alla richiesta di rinnovo dal collegamento nella notifica tramite posta elettronica o accedendo a PIM dal portale di Azure e selezionando **Approva richieste** dal riquadro a sinistra.

![Le risorse di Azure - approvare le richieste di pagina elenco richieste e i collegamenti per approvare o rifiutare](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando un amministratore seleziona **Approva** o **Rifiuta**, vengono visualizzati i dettagli della richiesta insieme a un campo per fornire una giustificazione per i log di controllo.

![Approva richiesta di assegnazione di ruolo con il motivo del richiedente, tipo di assegnazione, ora di inizio, ora di fine e motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Quando approvano una richiesta di rinnovo di un'assegnazione di ruolo, gli amministratori delle risorse devono immettere una nuova data di inizio, di fine e un tipo di assegnazione. 

### <a name="admin-renew"></a>Rinnovo richiesto dagli amministratori

Gli amministratori delle risorse possono rinnovare le assegnazioni di ruolo scaduto dalla scheda **Membri** nel menu di spostamento a sinistra di una risorsa. Possono anche rinnovare le assegnazioni di ruolo scaduto nella scheda dei ruoli **Scaduti** di un ruolo di risorse.

Dalla schermata **Membri** selezionare **Ruoli scaduti** per visualizzare un elenco di tutte le assegnazioni di ruolo scadute.

![Risorse di Azure - pagina membri elencare ruoli scaduti con collegamenti per il rinnovo](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Passaggi successivi

- [Approvare o rifiutare le richieste per i ruoli delle risorse di Azure in PIM](pim-resource-roles-approval-workflow.md)
- [Configurare le impostazioni dei ruoli delle risorse di Azure in PIM](pim-resource-roles-configure-role-settings.md)
