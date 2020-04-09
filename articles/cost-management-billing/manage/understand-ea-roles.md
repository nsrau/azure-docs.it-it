---
title: Informazioni sui ruoli di amministratore dell'organizzazione in Azure
description: Informazioni sui ruoli di amministratore per Enterprise in Azure.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: banders
ms.openlocfilehash: 28fff2a24f703e0cff967bdde29a7b1238e197ee
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586143"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Informazioni sui ruoli amministrativi per il Contratto Enterprise di Azure

Per gestire l'utilizzo e la spesa per la propria organizzazione, i clienti di Azure con un Contratto Enterprise possono assegnare cinque distinti ruoli amministrativi:

- Amministratore dell'organizzazione
- Amministratore dell'organizzazione (sola lettura)<sup>1</sup>
- Amministratore del reparto
- Amministratore del reparto (sola lettura)
- Proprietario dell'account<sup>2</sup>

<sup>1</sup> Il contatto per la fatturazione del contratto EA sarà incluso in questo ruolo.

<sup>2</sup> Il contatto per la fatturazione non può essere aggiunto o cambiato in Azure EA Portal e verrà aggiunto alla registrazione EA in base all'utente configurato come contatto per la fatturazione a livello di contratto. Per cambiare il contatto per la fatturazione, è necessario effettuare una richiesta al Centro operativo regionale tramite un partner/software advisor.

Questi ruoli sono specifici per la gestione del Contratto Enterprise di Azure e si aggiungono ai ruoli predefiniti disponibili in Azure per controllare l'accesso alle risorse. Per altre informazioni, vedere [Ruoli predefiniti per le risorse di Azure](../../role-based-access-control/built-in-roles.md).

Le sezioni seguenti descrivono i limiti e le funzionalità di ogni ruolo.

## <a name="user-limit-for-admin-roles"></a>Limite di utenti per i ruoli di amministratore

|Ruolo| Limite di utenti|
|---|---|
|Amministratore dell'organizzazione|Nessuna limitazione|
|Amministratore dell'organizzazione (sola lettura)|Nessuna limitazione|
|Amministratore del reparto|Nessuna limitazione|
|Amministratore del reparto (sola lettura)|Nessuna limitazione|
|Proprietario dell'account|1 per account<sup>3</sup>|

<sup>3</sup> Ogni account richiede un account Microsoft univoco oppure un account aziendale o dell'istituto di istruzione.

## <a name="organization-structure-and-permissions-by-role"></a>Struttura dell'organizzazione e autorizzazioni in base al ruolo

|Attività| Amministratore dell'organizzazione|Amministratore dell'organizzazione (sola lettura)|Amministratore del reparto|Amministratore del reparto (sola lettura)|Proprietario dell'account| Partner|
|---|---|---|---|---|---|---|
|Visualizzare gli amministratori dell'organizzazione|✔|✔|✘|✘|✘|✔|
|Aggiungere o rimuovere gli amministratori dell'organizzazione|✔|✘|✘|✘|✘|✘|
|Visualizzare i contatti per le notifiche<sup>4</sup> |✔|✔|✘|✘|✘|✔|
|Aggiungere o rimuovere i contatti per le notifiche<sup>4</sup> |✔|✘|✘|✘|✘|✘|
|Creare e gestire i reparti |✔|✘|✘|✘|✘|✘|
|Visualizzare gli amministratore di reparto|✔|✔|✔|✔|✘|✔|
|Aggiungere o rimuovere gli amministratori di reparto|✔|✘|✔|✘|✘|✘|
|Visualizzare gli account nella registrazione |✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|Aggiungere gli account alla registrazione e cambiare il proprietario dell'account|✔|✘|✔<sup>5</sup>|✘|✘|✘|
|Creare e gestire le sottoscrizioni e le relative autorizzazioni|✘|✘|✘|✘|✔|✘|

- <sup>4</sup> Ai contatti per le notifiche vengono inviate comunicazioni sul Contratto Enterprise di Azure tramite posta elettronica.
- <sup>5</sup> L'attività è limitata agli account nel reparto.


## <a name="usage-and-costs-access-by-role"></a>Accesso all'utilizzo e ai costi per ruolo

|Attività| Amministratore dell'organizzazione|Amministratore dell'organizzazione (sola lettura)|Amministratore del reparto|Amministratore del reparto (sola lettura) |Proprietario dell'account| Partner|
|---|---|---|---|---|---|---|
|Visualizzare il saldo del credito incluso l'impegno monetario|✔|✔|✘|✘|✘|✔|
|Visualizzare le quote di spesa del reparto|✔|✔|✘|✘|✘|✔|
|Impostare le quote di spesa del reparto|✔|✘|✘|✘|✘|✘|
|Visualizzare l'elenco prezzi per il Contratto Enterprise dell'organizzazione|✔|✔|✘|✘|✘|✔|
|Visualizzare i dettagli relativi a utilizzo e costi|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Gestire le risorse nel portale di Azure|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> È necessario che l'amministratore dell'organizzazione abiliti il criterio **DA view charges** (Visualizzazione addebiti per amministratori di reparto) nell'Enterprise Portal. L'amministratore del reparto potrà quindi visualizzare i dettagli dei costi per il reparto.
- <sup>7</sup> È necessario che l'amministratore dell'organizzazione abiliti il criterio **AO view charges** (Visualizzazione addebiti per proprietari dell'account) nell'Enterprise Portal. Il proprietario dell'account può quindi visualizzare i dettagli dei costi per l'account.


## <a name="pricing-in-azure-portal"></a>Prezzi nel portale di Azure

È possibile riscontrare differenze di prezzi nel portale di Azure a seconda del ruolo amministrativo e della modalità in cui l'amministratore dell'organizzazione ha impostato i criteri di visualizzazione degli addebiti. Nell'Enterprise Portal i due criteri che determinano i prezzi visualizzati nel portale di Azure sono:

- DA view charges (Visualizzazione addebiti per amministratori di reparto)
- AO view charges (Visualizzazione addebiti per proprietari dell'account)

Per informazioni su come impostare questi criteri, vedere [Gestire l'accesso alle informazioni di fatturazione per Azure](manage-billing-access.md).

La tabella seguente illustra la relazione tra i ruoli di amministratore del Contratto Enterprise, i criteri per la visualizzazione degli addebiti, il ruolo di controllo degli accessi in base al ruolo nel portale di Azure e il piano tariffario visualizzato nel portale di Azure. L'amministratore dell'organizzazione esamina i dettagli di utilizzo in base ai prezzi per il Contratto Enterprise dell'organizzazione. Tuttavia, l'amministratore di reparto e il proprietario dell'account esaminano visualizzazioni diverse dei prezzi in base ai criteri di visualizzazione degli addebiti e del relativo ruolo di controllo degli accessi in base al ruolo. Il ruolo di amministratore di reparto elencato nella tabella seguente si riferisce sia al ruolo Amministratore di reparto sia al ruolo Amministratore di reparto (sola lettura).

|Ruolo di amministratore per il Contratto Enterprise|Criteri per la visualizzazione degli addebiti per ruolo|Ruolo di controllo degli accessi in base al ruolo|Visualizzazione dei prezzi|
|---|---|---|---|
|Proprietario dell'account OPPURE Amministratore del reparto|✔ Abilitato|Proprietario|Prezzi per il Contratto Enterprise dell'organizzazione|
|Proprietario dell'account OPPURE Amministratore del reparto|✘ Disabilitato|Proprietario|Prezzi al dettaglio|
|Proprietario dell'account OPPURE Amministratore del reparto|✔ Abilitato |none|Nessun prezzo|
|Proprietario dell'account OPPURE Amministratore del reparto|✘ Disabilitato |none|Nessun prezzo|
|nessuno|Non applicabile |Proprietario|Prezzi al dettaglio|

Si impostano il ruolo di amministratore dell'organizzazione e i criteri di visualizzazione degli addebiti nell'Enterprise Portal. Il ruolo di controllo degli accessi in base al ruolo può essere aggiornato nel portale di Azure. Per altre informazioni, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'accesso alle informazioni di fatturazione per Azure](manage-billing-access.md)
- [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../../role-based-access-control/role-assignments-portal.md)
- [Ruoli predefiniti per le risorse di Azure](../../role-based-access-control/built-in-roles.md)
