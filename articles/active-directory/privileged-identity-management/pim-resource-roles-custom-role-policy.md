---
title: Privileged Identity Management per le risorse di Azure - Usare ruoli personalizzati per impostazioni PIM di destinazione | Microsoft Docs
description: Descrive come usare ruoli personalizzati in PIM per risorse di Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 6336d99df1bbdd71c66a9757af1d9fb356a91bf6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="use-custom-roles-to-target-pim-settings"></a>Usare ruoli personalizzati per impostazioni PIM di destinazione

Può talvolta essere necessario applicare impostazioni PIM rigide per alcuni membri di un ruolo, offrendo nello stesso tempo maggiore autonomia per altri. Si consideri uno scenario in cui l'organizzazione assume diversi collaboratori a tempo determinato che contribuiscano allo sviluppo di un'applicazione che verrà eseguita in una sottoscrizione di Azure. 

L'amministratore delle risorse vuole che i dipendenti dell'organizzazione siano idonei all'accesso senza approvazione, ma che per tutti i collaboratori a tempo determinato sia necessaria l'approvazione quando richiedono l'attivazione. Seguire i passaggi seguenti, che descrivono il processo con il quale vengono abilitate le impostazioni PIM relative ai ruoli delle risorse di Azure.

## <a name="create-the-custom-role"></a>Creare il ruolo personalizzato

[Usare questa guida per creare un ruolo personalizzato per una risorsa](../../role-based-access-control/custom-roles.md).

Includere un nome descrittivo per poter ricordare facilmente il ruolo predefinito che si intende duplicare.

>[!NOTE]
>Assicurarsi che il ruolo personalizzato sia un duplicato del ruolo voluto e che il relativo ambito corrisponda al ruolo predefinito.

## <a name="apply-pim-settings"></a>Applicare le impostazioni PIM

Dopo la creazione del ruolo nel tenant, passare a PIM e selezionare la risorsa che rappresenta l'ambito del ruolo.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Configurare le impostazioni dei ruoli PIM](pim-resource-roles-configure-role-settings.md) da applicare a questi membri

Infine, [assegnare i ruoli](pim-resource-roles-assign-roles.md) al gruppo distinto dei membri di destinazione di queste impostazioni.

## <a name="next-steps"></a>Passaggi successivi

[Rivedere i proprietari di una sottoscrizione](pim-resource-roles-perform-access-review.md)
