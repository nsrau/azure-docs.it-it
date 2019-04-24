---
title: Visualizzare la cronologia di controllo per i ruoli di Azure AD in PIM - Azure Active Directory | Microsoft Docs
description: Informazioni su come visualizzare la cronologia di controllo per i ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c080173af8ddd31b077bb820ea19d82eb2b29300
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440791"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Cronologia di controllo di visualizzazione per i ruoli di Azure AD in PIM
È possibile utilizzare la cronologia di controllo di Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per visualizzare tutte le assegnazioni utente e le attivazioni entro un determinato periodo di tempo per i ruoli con tutti i privilegi. Se si desidera visualizzare la cronologia di controllo completa dell'attività nel tenant, inclusi amministratore, utente finale e attività di sincronizzazione, è possibile usare i [report di accesso e utilizzo di Azure Active Directory.](../reports-monitoring/overview-reports.md)

## <a name="navigate-to-audit-history"></a>Passare alla cronologia dei controlli
Nel dashboard del [portale di Azure](https://portal.azure.com) selezionare l'app **Azure AD Privileged Identity Management** . Da questo punto è possibile accedere alla cronologia dei controlli facendo clic su **Gestione dei ruoli con privilegi** > **Cronologia dei controlli** nel dashboard di PIM.

![Cronologia dei controlli](media/azure-ad-pim-approval-workflow/image021.png)

> [!NOTE]
> È possibile ordinare i dati per azione e ricercare “L’attivazione è stata approvata”.


## <a name="audit-history-graph"></a>Grafico della cronologia dei controlli
È possibile usare la cronologia dei controlli per visualizzare il totale delle attivazioni, il numero massimo di attivazioni per giorno e il numero medio di attivazioni per giorno in un grafico a linee.  È anche possibile filtrare i dati per ruolo se sono presenti più ruoli nella cronologia di controlli.

Usare i pulsanti relativi a **ora**, **azione** e **ruolo** per ordinare la cronologia.

## <a name="audit-history-list"></a>Elenco della cronologia dei controlli
Le colonne nell'elenco della cronologia dei controlli sono le seguenti:

* **Richiedente** : utente che ha richiesto l'attivazione o la modifica del ruolo.  Se il valore è "Azure System", vedere la cronologia dei controlli di Azure per altre informazioni.
* **Utente** : utente che esegue l'attivazione o che è assegnato a un ruolo.
* **Ruolo** : ruolo assegnato o attivato dall'utente.
* **Azione** : azioni eseguite dal richiedente. Le azioni possono includere assegnazione, annullamento dell'assegnazione, attivazione o disattivazione.
* **Ora** : quando si è verificata l'azione.
* **Motivo** : eventuale testo immesso nel campo del motivo durante l'attivazione.
* **Scadenza** : rilevante solo per l'attivazione dei ruoli.

## <a name="filter-audit-history"></a>Filtrare la cronologia dei controlli
Per filtrare le informazioni visualizzate nella cronologia dei controlli, fare clic sul pulsante **Filtro**.  Verrà visualizzato il pannello **Aggiorna parametri grafico** .

Dopo aver impostato i filtri, fare clic su **Aggiorna** per filtrare i dati della cronologia.  Se i dati non vengono visualizzati immediatamente, aggiornare la pagina.

### <a name="change-the-date-range"></a>Modificare l'intervallo di date
Usare i pulsanti **Oggi**, **Settimana precedente**, **Mese precedente** o **Personalizzato** per modificare l'intervallo di tempo della cronologia dei controlli.

Se si sceglie il pulsante **Personalizzato**, vengono visualizzati i campi di data **Da** e **A** per specificare un intervallo di date per la cronologia.  È possibile immettere le date nel formato MM/GG/AAAA o fare clic sull'icona **calendario** e scegliere la data da un calendario.

### <a name="change-the-roles-included-in-the-history"></a>Modificare i ruoli inclusi nella cronologia
Selezionare o deselezionare la casella di controllo **Ruolo** accanto a ogni ruolo per includerlo o escluderlo dal la cronologia.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare la cronologia delle attività e dei controlli per i ruoli delle risorse di Azure in PIM](azure-pim-resource-rbac.md)
