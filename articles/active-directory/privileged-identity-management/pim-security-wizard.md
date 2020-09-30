---
title: Individuazione e informazioni dettagliate dei ruoli Azure AD (anteprima) in Privileged Identity Management procedura guidata per la sicurezza precedente-Azure Active Directory
description: Discovery and Insights (in precedenza Security Wizard) consentono di convertire le assegnazioni di ruolo Azure AD permanenti in assegnazioni JIT con Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/01/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff5d15ef66b597fdf56fefe90f35cbf122bb093f
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534438"
---
# <a name="discovery-and-insights-preview-for-azure-ad-roles-formerly-security-wizard"></a>Discovery and Insights (anteprima) for Azure AD Roles (in precedenza procedura guidata sicurezza)

Se si inizia con Privileged Identity Management (PIM) nell'organizzazione Azure Active Directory (Azure AD), è possibile usare la pagina **Discovery and Insights (anteprima)** per iniziare. Questa funzionalità Mostra gli utenti assegnati ai ruoli con privilegi nell'organizzazione e come usare PIM per modificare rapidamente le assegnazioni di ruolo permanenti in assegnazioni JIT. È possibile visualizzare o modificare le assegnazioni di ruolo con privilegi permanenti in **Discovery e Insights (anteprima)**. Si tratta di uno strumento di analisi e uno strumento di azione.

## <a name="discovery-and-insights-preview"></a>Discovery e Insights (anteprima)

Prima che l'organizzazione inizi a usare Privileged Identity Management, tutte le assegnazioni di ruolo sono permanenti. Gli utenti sono sempre nei loro ruoli assegnati anche quando non sono necessari i loro privilegi. Discovery and Insights (anteprima), che sostituisce la procedura guidata di sicurezza precedente, Visualizza un elenco di ruoli con privilegi e il numero di utenti attualmente presenti in tali ruoli. È possibile elencare le assegnazioni per un ruolo per ottenere ulteriori informazioni sugli utenti assegnati se uno o più di essi non hanno familiarità.

: heavy_check_mark: **Microsoft consiglia** di gestire due account break Glass che vengono assegnati in modo permanente al ruolo di amministratore globale. Assicurarsi che questi account non richiedano lo stesso meccanismo di autenticazione a più fattori dei normali account amministrativi per l'accesso, come descritto in [gestire gli account di accesso di emergenza in Azure ad](../users-groups-roles/directory-emergency-access.md).

Tenere inoltre permanenti le assegnazioni di ruolo se un utente ha un account Microsoft (in altre parole, un account usato per accedere a servizi Microsoft come Skype o Outlook.com). Se è richiesta l'autenticazione a più fattori per un utente con account Microsoft per attivare un'assegnazione di ruolo, l'utente verrà bloccato.

## <a name="open-discovery-and-insights-preview"></a>Apri Discovery e Insights (anteprima)

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **Azure ad Roles (ruoli** ) e quindi selezionare **Discovery and Insights (anteprima)**. L'apertura della pagina Avvia il processo di individuazione per individuare le assegnazioni di ruolo pertinenti.

    ![Azure AD Roles-pagina Discovery and Insights che mostra le 3 opzioni](./media/pim-security-wizard/new-preview-link.png)

1. Selezionare **reduce Global Administrators**.

    ![Screenshot che mostra l'opzione "Discovery and Insights (anteprima)" con l'azione "reduce Global Administrators" selezionata.](./media/pim-security-wizard/new-preview-page.png)

1. Esaminare l'elenco di assegnazioni di ruolo amministratore globale.

    ![Riduzione degli amministratori globali-riquadro ruoli che Mostra tutti gli amministratori globali](./media/pim-security-wizard/new-global-administrator-list.png)

1. Selezionare **Avanti** per selezionare gli utenti o i gruppi che si desidera rendere idonei e quindi selezionare **Rendi idonea** o **Rimuovi assegnazione**.

    ![Convertire i membri in una pagina idonea con le opzioni per selezionare i membri che si desidera rendere idonei per i ruoli](./media/pim-security-wizard/new-global-administrator-buttons.png)

1. È anche possibile richiedere a tutti gli amministratori globali di verificare il proprio accesso.

    ![Pagina degli amministratori globali che visualizza la sezione verifiche di accesso](./media/pim-security-wizard/new-global-administrator-access-review.png)

1. Dopo aver selezionato una di queste modifiche, viene visualizzata una notifica di Azure.

1. È quindi possibile selezionare **Elimina accesso permanente** o **Controlla entità servizio** per ripetere i passaggi precedenti su altri ruoli con privilegi e sulle assegnazioni di ruolo dell'entità servizio. Per le assegnazioni di ruolo dell'entità servizio, è possibile rimuovere solo le assegnazioni di ruolo.

    ![Opzioni aggiuntive di Insights per eliminare l'accesso in modo permanente ed esaminare le entità servizio ](./media/pim-security-wizard/new-preview-page-service-principals.png)

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure AD in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Concedere l'accesso ad altri amministratori per gestire Privileged Identity Management](pim-how-to-give-access-to-pim.md)
