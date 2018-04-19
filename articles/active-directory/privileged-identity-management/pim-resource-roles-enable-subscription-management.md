---
title: Privileged Identity Management per le risorse di Azure - Abilitare la gestione delle sottoscrizioni | Microsoft Docs
description: Informazioni su come gli amministratori globali possono gestire le sottoscrizioni nel tenant.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/27/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4c6ae3da34fe5157314b8ea422591f7ecbd2a667
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="enable-subscription-management"></a>Abilitare la gestione delle sottoscrizioni

L'amministratore globale della directory potrebbe non avere accesso a tutte le risorse della sottoscrizione nel tenant per impostazione predefinita. Questo articolo descriverà le procedere per assegnare a se stessi l'accesso a tutte le sottoscrizioni nel tenant e un approccio consigliato per mantenere la conformità a eventuali controlli di sicurezza richiesti dall'organizzazione dopo aver ottenuto l'accesso.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>Chi può abilitare la gestione delle sottoscrizioni nella directory?

Ogni utente assegnato al ruolo di amministratore globale deve seguire la procedura seguente per abilitare la gestione delle sottoscrizioni. Dopo aver abilitato gestione delle sottoscrizioni per se stessi, è possibile aggiungere altri amministratori globali che potrebbero avere l'esigenza di accedere alle risorse. Non esiste un'impostazione della directory che consente l'accesso a tutti i membri del ruolo di amministratore globale.

## <a name="log-on-to-the-azure-portal"></a>Accedere al portale di Azure

Iniziare eseguendo l'accesso al portale di Azure con un account membro idoneo o attivo del ruolo di amministratore globale. Se l'account è un amministratore globale idoneo, è necessario attivare il ruolo prima di procedere con il passaggio successivo.

## <a name="access-the-azure-ad-admin-center"></a>Accesso all'interfaccia di amministrazione di Azure AD

Dopo aver eseguito l'accesso al portale di Azure come amministratore globale, è possibile modificare le impostazioni che consentiranno l'accesso alle sottoscrizioni di Azure. Passare all'interfaccia di amministrazione di Azure AD, quindi individuare e selezionare la scheda Proprietà nel menu di spostamento a sinistra.

![](media/azure-pim-resource-rbac/aad_properties.png)

Nell'elenco delle proprietà impostare l'opzione "Gli amministratori globali possono gestire le sottoscrizioni di Azure" su "Sì".

![](media/azure-pim-resource-rbac/aad_properties_save.png)

## <a name="navigate-to-azure-ad-pim"></a>Passare ad Azure AD Privileged Identity Management

Con questa opzione abilitata, l'account viene aggiunto automaticamente al ruolo "Amministratore Accesso utenti" per ogni risorsa della sottoscrizione nel tenant. Da qui, passare ad Azure AD Privileged Identity Management e selezionare le risorse di Azure nella sezione Gestisci del menu di spostamento a sinistra.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Gestire e individuare le risorse

Se l'organizzazione sta già usando Azure AD Privileged Identity Management per proteggere gli amministratori di Azure Active Directory, verrà visualizzato un elenco di sottoscrizioni al caricamento del pannello.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Se non viene visualizzata alcuna risorsa, verificare che:
>- Il ruolo di amministratore globale non sia scaduto 
>- L'organizzazione abbia una sottoscrizione di Azure

![](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Configurare le assegnazioni

Selezionare una sottoscrizione nell'elenco e assegnare se stessi (o un gruppo di cui si è membri) come proprietario idoneo della risorsa. 
[Altre informazioni sull'assegnazione dei ruoli](pim-resource-roles-assign-roles.md).

Ripetere questa procedura per ogni risorsa prima di procedere al passaggio successivo.

## <a name="clean-up-standing-access"></a>Deselezionare l'accesso permanente

Ora che sono disponibili le assegnazioni idonee per le sottoscrizioni importanti nell'organizzazione, è possibile deselezionare l'accesso permanente disabilitando l'opzione nelle proprietà della directory:

![](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>Passaggi successivi

[Individuare le risorse](pim-resource-roles-discover-resources.md)

[Configurare le impostazioni dei ruoli](pim-resource-roles-configure-role-settings.md)








