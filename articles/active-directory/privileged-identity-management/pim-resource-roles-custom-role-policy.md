---
title: Usare ruoli personalizzati per le risorse di Azure in PIM | Microsoft Docs
description: Informazioni su come usare ruoli personalizzati per le risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2767d817d6f15d38aeef4c669b2b11c3d8a8a6b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168018"
---
# <a name="use-custom-roles-for-azure-resources-in-pim"></a>Usare ruoli personalizzati per le risorse di Azure in PIM

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

- [Configurare le impostazioni dei ruoli delle risorse di Azure in PIM](pim-resource-roles-configure-role-settings.md)
- [Ruoli personalizzati in Azure](../../role-based-access-control/custom-roles.md)
