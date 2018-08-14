---
title: Come usare il log di controllo in Azure AD Privileged Identity Management | Documentazione Microsoft
description: Informazioni su come usare il log di controllo nell'estensione Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 922658f75a0d00998072c7bd8f2160e9db27f318
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627791"
---
# <a name="using-the-audit-log-in-pim"></a>Uso del log di controllo in PIM
È possibile usare il log di controllo di Privileged Identity Management (PIM) per visualizzare tutte le assegnazioni utente e le attivazioni per un periodo di tempo specificato. Se si desidera visualizzare la cronologia di controllo completa dell'attività nel tenant, inclusi amministratore, utente finale e attività di sincronizzazione, è possibile usare i [report di accesso e utilizzo di Azure Active Directory.](../reports-monitoring/overview-reports.md)

## <a name="navigate-to-the-audit-log"></a>Accedere al log di controllo
Nel dashboard del [portale di Azure](https://portal.azure.com) selezionare l'app **Azure AD Privileged Identity Management** . Da qui è possibile accedere al log di controllo facendo clic su **Gestione dei ruoli con privilegi** > **Cronologia dei controlli** nel dashboard di PIM.

## <a name="the-audit-log-graph"></a>Grafico del log di controllo
È possibile usare il log di controllo per visualizzare il totale delle attivazioni, il numero massimo di attivazioni per giorno e il numero medio di attivazioni per giorno in un grafico a linee.  È anche possibile filtrare i dati per ruolo se sono presenti più ruoli nella cronologia di controlli.

Usare i pulsanti relativi a **ora**, **azione** e **ruolo** per ordinare il log.

## <a name="the-audit-log-list"></a>Elenco del log di controllo
Le colonne nell'elenco del log di controllo sono le seguenti:

* **Richiedente** : utente che ha richiesto l'attivazione o la modifica del ruolo.  Se il valore è "Azure System", vedere il log di controllo di Azure per altre informazioni.
* **Utente** : utente che esegue l'attivazione o che è assegnato a un ruolo.
* **Ruolo** : ruolo assegnato o attivato dall'utente.
* **Azione** : azioni eseguite dal richiedente. Le azioni possono includere assegnazione, annullamento dell'assegnazione, attivazione o disattivazione.
* **Ora** : quando si è verificata l'azione.
* **Motivo** : eventuale testo immesso nel campo del motivo durante l'attivazione.
* **Scadenza** : rilevante solo per l'attivazione dei ruoli.

## <a name="filter-the-audit-log"></a>Filtrare il log di controllo
È possibile filtrare le informazioni visualizzate nel log di controllo facendo clic sul pulsante **Filtro** .  Verrà visualizzato il pannello **Aggiorna parametri grafico** .

Dopo aver impostato i filtri, fare clic su **Aggiorna** per filtrare i dati del log.  Se i dati non vengono visualizzati immediatamente, aggiornare la pagina.

### <a name="change-the-date-range"></a>Modificare l'intervallo di date
Usare i pulsanti **Oggi**, **Settimana precedente**, **Mese precedente** o **Personalizzato** per modificare l'intervallo di tempo del log di controllo.

Se si sceglie il pulsante **Personalizzato**, vengono visualizzati i campi di data **Da** e **A** per specificare un intervallo di date per il log.  È possibile immettere le date nel formato MM/GG/AAAA o fare clic sull'icona **calendario** e scegliere la data da un calendario.

### <a name="change-the-roles-included-in-the-log"></a>Modificare i ruoli inclusi nel log
Selezionare o deselezionare la casella di controllo **Ruolo** accanto a ogni ruolo per includerlo o escluderlo dal log.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

