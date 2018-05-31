---
title: Usare ruoli personalizzati per configurare le impostazioni di Privileged Identity Management per le risorse di Azure | Microsoft Docs
description: Descrive come usare ruoli personalizzati per le risorse di Azure con Privileged Identity Management.
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
ms.openlocfilehash: e3df34e761f17aa3c1949af390e57360d84a304f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32148941"
---
# <a name="use-custom-roles-to-target-privileged-identity-management-settings"></a>Usare ruoli personalizzati per configurare le impostazioni di Privileged Identity Management

Potrebbe essere necessario applicare impostazioni di Privileged Identity Management (PIM) rigide per alcuni membri di un ruolo, offrendo nello stesso tempo maggiore autonomia ad altri. Si consideri uno scenario in cui l'organizzazione assume diversi collaboratori a tempo determinato che contribuiscano allo sviluppo di un'applicazione che verrà eseguita in una sottoscrizione di Azure.

Come amministratore delle risorse, si vuole consentire ai dipendenti di accedere senza che sia necessaria l'approvazione. Tutti i collaboratori a tempo determinato devono invece essere approvati quando richiedono l'accesso alle risorse dell'organizzazione.

Seguire i passaggi descritti nella sezione seguente per configurare le impostazioni PIM per i ruoli delle risorse di Azure.

## <a name="create-the-custom-role"></a>Creare il ruolo personalizzato

Per creare un ruolo personalizzato per una risorsa, seguire la procedura descritta in [Creare ruoli personalizzati per il controllo degli accessi in base al ruolo di Azure](../role-based-access-control-custom-roles.md).

Durante la creazione del ruolo personalizzato, includere un nome descrittivo per poter ricordare facilmente il ruolo predefinito che si intende duplicare.

> [!NOTE]
> Assicurarsi che il ruolo personalizzato sia un duplicato del ruolo predefinito corretto e che il relativo ambito corrisponda al ruolo predefinito.

## <a name="apply-pim-settings"></a>Applicare le impostazioni PIM

Dopo aver creato il ruolo nel tenant, nel portale di Azure passare al riquadro **Privileged Identity Management - Risorse di Azure**. Selezionare la risorsa a cui si applica il ruolo.

![Riquadro Privileged Identity Management - Risorse di Azure](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Configurare le impostazioni dei ruoli PIM](pim-resource-roles-configure-role-settings.md) da applicare a questi membri del ruolo.

Infine, [assegnare i ruoli](pim-resource-roles-assign-roles.md) al gruppo distinto dei membri di destinazione di queste impostazioni.

## <a name="next-steps"></a>Passaggi successivi

[Verificare i proprietari della sottoscrizione e l'accesso](pim-resource-roles-perform-access-review.md)
