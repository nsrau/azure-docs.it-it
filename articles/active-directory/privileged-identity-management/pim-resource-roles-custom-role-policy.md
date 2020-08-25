---
title: Usare i ruoli personalizzati di Azure in PIM-Azure AD | Microsoft Docs
description: Informazioni su come usare i ruoli personalizzati di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24b7845ec66a85e6ced4f1df9caec409a94016bf
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782601"
---
# <a name="use-azure-custom-roles-in-privileged-identity-management"></a>Usare i ruoli personalizzati di Azure in Privileged Identity Management

Potrebbe essere necessario applicare le impostazioni di Privileged Identity Management Strict (PIM) ad alcuni utenti in un ruolo con privilegi nell'organizzazione di Azure Active Directory (Azure AD), garantendo al tempo stesso una maggiore autonomia per altri utenti. Si consideri, ad esempio, uno scenario in cui l'organizzazione assume diversi partner contrattuali per facilitare lo sviluppo di un'applicazione che viene eseguita in una sottoscrizione di Azure.

Come amministratore delle risorse, si vuole consentire ai dipendenti di accedere senza che sia necessaria l'approvazione. Tutti i collaboratori a tempo determinato devono invece essere approvati quando richiedono l'accesso alle risorse dell'organizzazione.

Seguire i passaggi descritti nella sezione successiva per configurare le impostazioni di Privileged Identity Management di destinazione per i ruoli delle risorse di Azure.

## <a name="create-the-custom-role"></a>Creare il ruolo personalizzato

Per creare un ruolo personalizzato per una risorsa, seguire i passaggi descritti in [ruoli personalizzati di Azure](../../role-based-access-control/custom-roles.md).

Durante la creazione del ruolo personalizzato, includere un nome descrittivo per poter ricordare facilmente il ruolo predefinito che si intende duplicare.

> [!NOTE]
> Assicurarsi che il ruolo personalizzato sia un duplicato del ruolo predefinito corretto e che il relativo ambito corrisponda al ruolo predefinito.

## <a name="apply-pim-settings"></a>Applicare le impostazioni PIM

Dopo aver creato il ruolo nell'organizzazione Azure AD, passare alla pagina **Privileged Identity Management-risorse di Azure** nella portale di Azure. Selezionare la risorsa a cui si applica il ruolo.

![Riquadro Privileged Identity Management - Risorse di Azure](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Configurare le impostazioni del ruolo Privileged Identity Management](pim-resource-roles-configure-role-settings.md) da applicare a questi membri del ruolo.

Infine, [assegnare i ruoli](pim-resource-roles-assign-roles.md) al gruppo distinto dei membri di destinazione di queste impostazioni.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni del ruolo delle risorse di Azure in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Ruoli personalizzati in Azure](../../role-based-access-control/custom-roles.md)