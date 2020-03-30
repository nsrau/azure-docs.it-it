---
title: Usare ruoli personalizzati per le risorse di Azure in PIM - Azure AD Documenti Microsoft
description: Informazioni su come usare ruoli personalizzati per le risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbe08cff2b57155f8f3315f5d3454abfbdad47a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847058"
---
# <a name="use-custom-roles-for-azure-resources-in-privileged-identity-management"></a>Usare ruoli personalizzati per le risorse di Azure in Gestione identità con privilegiUse custom roles for Azure resources in Privileged Identity Management

Potrebbe essere necessario applicare impostazioni PIM (Privileged Identity Management) rigorose ad alcuni utenti con un ruolo privilegiato nell'organizzazione di Azure Active Directory (Azure AD), fornendo al contempo una maggiore autonomia ad altri utenti. Si consideri ad esempio uno scenario in cui l'organizzazione assume diversi associazioni di contratti per facilitare lo sviluppo di un'applicazione che verrà eseguita in una sottoscrizione di Azure.Consider for example a scenario in which your organization hires several contract associates to assist in the development of an application that will run in an Azure subscription.

Come amministratore delle risorse, si vuole consentire ai dipendenti di accedere senza che sia necessaria l'approvazione. Tutti i collaboratori a tempo determinato devono invece essere approvati quando richiedono l'accesso alle risorse dell'organizzazione.

Seguire i passaggi descritti nella sezione successiva per configurare le impostazioni di gestione delle identità con privilegi di destinazione per i ruoli delle risorse di Azure.Follow the steps outlined in the next section to set up targeted Privileged Identity Management settings for Azure resource roles.

## <a name="create-the-custom-role"></a>Creare il ruolo personalizzato

Per creare un ruolo personalizzato per una risorsa, seguire la procedura descritta in [Creare ruoli personalizzati per il controllo degli accessi in base al ruolo di Azure](../role-based-access-control-custom-roles.md).

Durante la creazione del ruolo personalizzato, includere un nome descrittivo per poter ricordare facilmente il ruolo predefinito che si intende duplicare.

> [!NOTE]
> Assicurarsi che il ruolo personalizzato sia un duplicato del ruolo predefinito corretto e che il relativo ambito corrisponda al ruolo predefinito.

## <a name="apply-pim-settings"></a>Applicare le impostazioni PIM

Dopo aver creato il ruolo nell'organizzazione di Azure AD, passare alla pagina **Gestione identità con privilegi - Risorse** di Azure nel portale di Azure.After the role is created in your Azure AD organization, go to the Privileged Identity Management - Azure resources page in the Azure portal. Selezionare la risorsa a cui si applica il ruolo.

![Riquadro Privileged Identity Management - Risorse di Azure](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Configurare le impostazioni](pim-resource-roles-configure-role-settings.md) del ruolo Gestione identità con privilegi che devono essere applicate a questi membri del ruolo.

Infine, [assegnare i ruoli](pim-resource-roles-assign-roles.md) al gruppo distinto dei membri di destinazione di queste impostazioni.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni del ruolo delle risorse di Azure in Gestione delle identità con privilegiConfigure Azure resource role settings in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Ruoli personalizzati in Azure](../../role-based-access-control/custom-roles.md)
