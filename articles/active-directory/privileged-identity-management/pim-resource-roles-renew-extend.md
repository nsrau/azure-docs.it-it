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
ms.openlocfilehash: a064fc67bf94ba6aa443e429fe83179d84cada84
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602548"
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

### <a name="member-extend"></a>Estensione del membro

I membri di un'assegnazione di ruolo possono estendere le assegnazioni di ruolo in scadenza direttamente dalla scheda **Idonea** o **Attiva** nella pagina **Ruoli personali** di una risorsa e dalla pagina **Ruoli personali** di primo livello del portale PIM. I membri possono richiedere l'estensione dei ruoli idonei e attivi (assegnati) con scadenza entro 14 giorni.

![Estensione dei ruoli](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Quando la data/ora di fine dell'assegnazione è entro 14 giorni, il pulsante **Estendi** diventa un collegamento attivo nell'interfaccia utente. Nell'esempio seguente, si suppone che la data corrente sia il 27 marzo.

![Estendere pulsante](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Per richiedere un'estensione di questa assegnazione di ruolo, selezionare **Estendi** per aprire il modulo di richiesta.

![Aprire il modulo di richiesta](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Espandere **Dettagli dell'assegnazione** per visualizzare le informazioni sull'assegnazione originale. Immettere un motivo per la richiesta di estensione e fare clic su **Estendi**.

>[!Note]
>È consigliabile includere i dettagli del motivo per cui è necessaria l'estensione e il tempo di estensione da concedere (se noto).

![Estendere le assegnazioni di ruoli](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

In pochi istanti, gli amministratori delle risorse riceveranno una notifica tramite posta elettronica con la richiesta di revisione della richiesta di estensione. Se è già stata inviata una richiesta di estensione, verrà visualizzata una notifica di tipo avviso popup nella parte superiore del portale di Azure che spiega l'errore.

![Notifica che spiega errore](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Passare alla scheda **Richieste in sospeso** nel riquadro a sinistra per visualizzare lo stato o annullare la richiesta.

![Richieste in sospeso](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approve"></a>Approvazione degli amministratori

Quando un membro invia una richiesta per estendere un'assegnazione di ruolo, gli amministratori delle risorse ricevono una notifica tramite posta elettronica che contiene i dettagli dell'assegnazione originale e il motivo specificato dal richiedente. La notifica include un collegamento diretto alla richiesta che l'amministratore dovrà approvare o rifiutare. 

Oltre a seguire il collegamento dal messaggio di posta elettronica, gli amministratori possono approvare o rifiutare le richieste andando al portale di amministrazione di PIM e selezionando **Approva richieste** dal riquadro a sinistra.

![Screenshot del problema](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando un amministratore seleziona **Approva** o **Rifiuta**, vengono visualizzati i dettagli della richiesta insieme a un campo per fornire una giustificazione per i log di controllo.

![Approva la richiesta di assegnazione di ruolo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Quando approvano una richiesta di estensione di un'assegnazione di ruolo, gli amministratori delle risorse possono scegliere una nuova data di inizio e di fine e un tipo di assegnazione. La modifica del tipo di assegnazione potrebbe essere necessaria se l'amministratore vuole fornire accesso limitato per completare un'attività specifica (un giorno, ad esempio). In questo esempio, l'amministratore può modificare l'assegnazione da **Idonea** ad **Attiva**. Ciò significa che possono fornire l'accesso al richiedente senza necessità di attivazione.

### <a name="admin-extend"></a>Estensione amministratore

Se un membro del ruolo si dimentica o non è in grado di richiedere l'estensione dell'appartenenza al ruolo, un amministratore può estendere un'assegnazione per conto del membro. Le estensioni amministrative dell'appartenenza ai ruoli non richiedono l'approvazione, ma vengono inviate notifiche a tutti gli altri amministratori al termine dell'estensione del ruolo.

Per estendere l'appartenenza a un ruolo, passare al ruolo della risorsa o alla visualizzazione dei membri in PIM. Trovare il membro che necessita di un'estensione. Quindi selezionare **Estendi** nella colonna relativa all'azione.

![Estensione dell'appartenenza ai ruoli](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Rinnovare le assegnazioni di ruolo

Anche se concettualmente simile alla richiesta di estensione, il processo di rinnovo di un'assegnazione di ruolo scaduto è diverso da quello appena citato. Usando la procedura descritta di seguito i membri e gli amministratori possono rinnovare l'accesso ai ruoli scaduti quando necessario.

### <a name="member-renew"></a>Rinnovo del membro

I membri che non possono più accedere alle risorse possono accedere alla cronologia di assegnazione scaduti fino a 30 giorni. A tale scopo, passare a **Ruoli personali** nel riquadro sinistro e quindi selezionare la scheda **Ruoli scaduti** nella sezione dei ruoli delle risorse di Azure.

![Scheda ruoli scaduti](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

L'elenco dei ruoli visualizzati include le impostazioni predefinite per i **Ruoli idonei**. Usare il menu a discesa per alternare i ruoli assegnati Idonea e Attiva.

Per richiedere il rinnovo per qualsiasi assegnazione di ruolo nell'elenco, selezionare l'azione **Rinnova**. Quindi, specificare un motivo per la richiesta. È utile indicare una durata oltre a qualsiasi altra informazione di contesto che possa essere utile all'amministratore delle risorse per decidere se approvare o rifiutare la richiesta.

![Rinnovare l'assegnazione di ruolo](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Dopo l'invio della richiesta, gli amministratori delle risorse ricevono una notifica della presenza di una richiesta di rinnovo di un'assegnazione di ruolo in sospeso.

### <a name="admin-approves"></a>Approvazione degli amministratori

Gli amministratori delle risorse possono accedere alla richiesta di rinnovo dal collegamento nella notifica tramite posta elettronica o accedendo a PIM dal portale di Azure e selezionando **Approva richieste** dal riquadro a sinistra.

![Approvare le richieste](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando un amministratore seleziona **Approva** o **Rifiuta**, vengono visualizzati i dettagli della richiesta insieme a un campo per fornire una giustificazione per i log di controllo.

![Approvare l'assegnazione di ruolo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Quando approvano una richiesta di rinnovo di un'assegnazione di ruolo, gli amministratori delle risorse devono immettere una nuova data di inizio, di fine e un tipo di assegnazione. 

### <a name="admin-renew"></a>Rinnovo amministratore

Gli amministratori delle risorse possono rinnovare le assegnazioni di ruolo scaduto dalla scheda **Membri** nel menu di spostamento a sinistra di una risorsa. Possono anche rinnovare le assegnazioni di ruolo scaduto nella scheda dei ruoli **Scaduti** di un ruolo di risorse.

Dalla schermata **Membri** selezionare **Ruoli scaduti** per visualizzare un elenco di tutte le assegnazioni di ruolo scadute.

![Ruoli scaduti](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Passaggi successivi

- [Approvare o rifiutare le richieste per i ruoli delle risorse di Azure in PIM](pim-resource-roles-approval-workflow.md)
- [Configurare le impostazioni dei ruoli delle risorse di Azure in PIM](pim-resource-roles-configure-role-settings.md)
