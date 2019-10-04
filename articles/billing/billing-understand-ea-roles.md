---
title: Informazioni sui ruoli di amministratore per Enterprise in Azure | Microsoft Docs
description: Informazioni sui ruoli di amministratore per Enterprise in Azure.
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2018
ms.author: banders
ms.openlocfilehash: 39526defb8f41ddacb0a26d7ad852f820ca6ea77
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70034538"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Informazioni sui ruoli amministrativi per il Contratto Enterprise di Azure

Per gestire l'utilizzo e la spesa per la propria organizzazione, i clienti di Azure con un Contratto Enterprise possono assegnare cinque distinti ruoli amministrativi:

- Amministratore dell'organizzazione
- Amministratore dell'organizzazione (sola lettura)
- Amministratore del reparto
- Amministratore del reparto (sola lettura)
- Proprietario dell'account
 
Questi ruoli sono specifici per la gestione del Contratto Enterprise di Azure e si aggiungono ai ruoli predefiniti disponibili in Azure per controllare l'accesso alle risorse. Per altre informazioni, vedere [Ruoli predefiniti per le risorse di Azure](../role-based-access-control/built-in-roles.md).

Le sezioni seguenti descrivono i limiti e le funzionalità di ogni ruolo.

## <a name="user-limit-for-admin-roles"></a>Limite di utenti per i ruoli di amministratore

|Ruolo| Limite di utenti|
|---|---|
|Amministratore dell'organizzazione|Illimitato|
|Amministratore dell'organizzazione (sola lettura)|Illimitato|
|Amministratore del reparto|Illimitato|
|Amministratore del reparto (sola lettura)|Illimitato|
|Proprietario dell'account|1 per account<sup>1</sup>|

<sup>1</sup> Ogni account richiede un account Microsoft univoco oppure un account aziendale o dell'istituto di istruzione.

## <a name="organization-structure-and-permissions-by-role"></a>Struttura dell'organizzazione e autorizzazioni in base al ruolo

|Attività| Amministratore dell'organizzazione|Amministratore dell'organizzazione (sola lettura)|Amministratore del reparto|Amministratore del reparto (sola lettura)|Proprietario dell'account|
|---|---|---|---|---|---|
|Visualizzare gli amministratori dell'organizzazione|✔|✔|✘|✘|✘|
|Aggiungere o rimuovere gli amministratori dell'organizzazione|✔|✘|✘|✘|✘|
|Visualizzare i contatti per le notifiche<sup>2</sup> |✔|✔|✘|✘|✘|
|Aggiungere o rimuovere i contatti per le notifiche<sup>2</sup> |✔|✘|✘|✘|✘|
|Creare e gestire i reparti |✔|✘|✘|✘|✘|
|Visualizzare gli amministratore di reparto|✔|✔|✔|✔|✘|
|Aggiungere o rimuovere gli amministratori di reparto|✔|✘|✔|✘|✘|
|Visualizzare gli account nella registrazione |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|Aggiungere gli account alla registrazione e cambiare il proprietario dell'account|✔|✘|✔<sup>3</sup>|✘|✘|
|Creare e gestire le sottoscrizioni e le relative autorizzazioni|✘|✘|✘|✘|✔|

- <sup>2</sup> Ai contatti per le notifiche vengono inviate comunicazioni sul Contratto Enterprise di Azure tramite posta elettronica.
- <sup>3</sup> L'attività è limitata agli account nel reparto.


## <a name="usage-and-costs-access-by-role"></a>Accesso all'utilizzo e ai costi per ruolo

|Attività| Amministratore dell'organizzazione|Amministratore dell'organizzazione (sola lettura)|Amministratore del reparto|Amministratore del reparto (sola lettura) |Proprietario dell'account|
|---|---|---|---|---|---|
|Visualizzare il saldo del credito incluso l'impegno monetario|✔|✔|✘|✘|✘|
|Visualizzare le quote di spesa del reparto|✔|✔|✘|✘|✘|
|Impostare le quote di spesa del reparto|✔|✘|✘|✘|✘|
|Visualizzare l'elenco prezzi per il Contratto Enterprise dell'organizzazione|✔|✔|✘|✘|✘|
|Visualizzare i dettagli relativi a utilizzo e costi|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Gestire le risorse nel portale di Azure|✘|✘|✘|✘|✔|

- <sup>4</sup> È necessario che l'amministratore dell'organizzazione abiliti il criterio **DA view charges** (Visualizzazione addebiti per amministratori di reparto) nell'Enterprise Portal. L'amministratore del reparto potrà quindi visualizzare i dettagli dei costi per il reparto.
- <sup>5</sup> È necessario che l'amministratore dell'organizzazione abiliti il criterio **AO view charges** (Visualizzazione addebiti per proprietari dell'account) nell'Enterprise Portal. Il proprietario dell'account può quindi visualizzare i dettagli dei costi per l'account.


## <a name="pricing-in-azure-portal"></a>Prezzi nel portale di Azure

È possibile riscontrare differenze di prezzi nel portale di Azure a seconda del ruolo amministrativo e della modalità in cui l'amministratore dell'organizzazione ha impostato i criteri di visualizzazione degli addebiti. Nell'Enterprise Portal i due criteri che determinano i prezzi visualizzati nel portale di Azure sono:

- DA view charges (Visualizzazione addebiti per amministratori di reparto)
- AO view charges (Visualizzazione addebiti per proprietari dell'account)

Per informazioni su come impostare questi criteri, vedere [Gestire l'accesso alle informazioni di fatturazione per Azure](billing-manage-access.md).

La tabella seguente illustra la relazione tra i ruoli di amministratore del Contratto Enterprise, i criteri per la visualizzazione degli addebiti, il ruolo di controllo degli accessi in base al ruolo nel portale di Azure e il piano tariffario visualizzato nel portale di Azure. L'amministratore dell'organizzazione esamina i dettagli di utilizzo in base ai prezzi per il Contratto Enterprise dell'organizzazione. Tuttavia, l'amministratore di reparto e il proprietario dell'account esaminano visualizzazioni diverse dei prezzi in base ai criteri di visualizzazione degli addebiti e del relativo ruolo di controllo degli accessi in base al ruolo. Il ruolo di amministratore di reparto elencato nella tabella seguente si riferisce sia al ruolo Amministratore di reparto sia al ruolo Amministratore di reparto (sola lettura).

|Ruolo di amministratore per il Contratto Enterprise|Criteri per la visualizzazione degli addebiti per ruolo|Ruolo di controllo degli accessi in base al ruolo|Visualizzazione dei prezzi|
|---|---|---|---|
|Proprietario dell'account OPPURE Amministratore del reparto|✔ Abilitato|Proprietario|Prezzi per il Contratto Enterprise dell'organizzazione|
|Proprietario dell'account OPPURE Amministratore del reparto|✘ Disabilitato|Proprietario|Prezzi al dettaglio|
|Proprietario dell'account OPPURE Amministratore del reparto|✔ Abilitato |Nessuno|Nessun prezzo|
|Proprietario dell'account OPPURE Amministratore del reparto|✘ Disabilitato |Nessuno|Nessun prezzo|
|Nessuna|Non applicabile |Proprietario|Prezzi al dettaglio|

Si impostano il ruolo di amministratore dell'organizzazione e i criteri di visualizzazione degli addebiti nell'Enterprise Portal. Il ruolo di controllo degli accessi in base al ruolo può essere aggiornato nel portale di Azure. Per altre informazioni, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'accesso alle informazioni di fatturazione per Azure](billing-manage-access.md)
- [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md)
- [Ruoli predefiniti per le risorse di Azure](../role-based-access-control/built-in-roles.md)
