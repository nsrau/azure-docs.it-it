---
title: Abilitare la gestione delle sottoscrizioni nel tenant - Azure | Microsoft Docs
description: Informazioni su come abilitare la gestione delle sottoscrizioni nel tenant quando si usa Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: bcaea64484010672a53050cb806c4256a17b19a8
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901176"
---
# <a name="enable-subscription-management-in-your-tenant"></a>Abilitare la gestione delle sottoscrizioni nel tenant

L'amministratore globale della directory potrebbe non avere accesso a tutte le risorse della sottoscrizione nel tenant per impostazione predefinita. Questo articolo illustra i passaggi per consentire a se stessi l'accesso a tutte le sottoscrizioni nel tenant. Fornisce inoltre un approccio consigliato per rimanere conforme ai controlli di sicurezza richiesti dall'organizzazione dopo avere ottenuto l'accesso.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>Chi può abilitare la gestione delle sottoscrizioni nella directory?

Ogni utente assegnato al ruolo di amministratore globale deve seguire la procedura seguente per abilitare la gestione delle sottoscrizioni. Dopo aver abilitato la gestione delle sottoscrizioni per se stessi, è possibile aggiungere altri amministratori globali che potrebbero avere l'esigenza di accedere alle risorse. Non esiste un'impostazione della directory che consente l'accesso a tutti i membri del ruolo di amministratore globale.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Eseguire l'accesso al portale di Azure con un account membro idoneo o attivo del ruolo di amministratore globale. Se l'account è un amministratore globale idoneo, è necessario attivare il ruolo prima di procedere con il passaggio successivo.

## <a name="access-the-azure-active-directory-admin-center"></a>Accedere all'interfaccia di amministrazione di Azure Active Directory

Dopo avere eseguito l'accesso al portale di Azure come amministratore globale, è possibile modificare le impostazioni che consentono l'accesso alle sottoscrizioni di Azure. Passare all'interfaccia di amministrazione di Azure Active Directory e selezionare **Proprietà**.

![Screenshot dell'interfaccia di amministrazione di Azure AD con la voce Proprietà evidenziata](media/azure-pim-resource-rbac/aad_properties.png)

Nell'elenco delle proprietà sotto **Gli amministratori globali possono gestire le sottoscrizioni di Azure** selezionare **Sì**.

![Screenshot della pagina Proprietà con il pulsante di opzione imposto su Sì](media/azure-pim-resource-rbac/aad_properties_save.png)

A questo punto l'account viene aggiunto automaticamente al ruolo Amministratore Accesso utenti per ogni risorsa della sottoscrizione nel tenant.

## <a name="browse-to-azure-ad-pim"></a>Passare ad Azure AD Privileged Identity Management

 Da qui passare ad Azure AD Privileged Identity Management (PIM). In **Gestisci** selezionare **Risorse di Azure**.

![Screenshot di PIM con le risorse di Azure evidenziate](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Gestire e individuare le risorse

Se l'organizzazione sta già usando Azure AD Privileged Identity Management per proteggere gli amministratori in Azure Active Directory, verrà visualizzato un elenco di sottoscrizioni al caricamento del pannello.

![Screenshot di PIM con l'elenco di sottoscrizioni visualizzate nel pannello](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Se non viene visualizzata alcuna risorsa, verificare che:
>- Il ruolo di amministratore globale non sia scaduto 
>- L'organizzazione abbia una sottoscrizione di Azure

![Screenshot di PIM con l'elenco di risorse vuoto](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Configurare le assegnazioni

Selezionare una sottoscrizione nell'elenco e assegnare se stessi (o un gruppo di cui si è membri) come proprietario idoneo della risorsa. 
[Altre informazioni sull'assegnazione dei ruoli](pim-resource-roles-assign-roles.md).

Ripetere questa procedura per ogni risorsa prima di procedere al passaggio successivo.

## <a name="clean-up-standing-access"></a>Deselezionare l'accesso permanente

Ora che sono disponibili le assegnazioni idonee per le sottoscrizioni importanti nell'organizzazione, è possibile deselezionare l'accesso permanente disabilitando l'opzione nelle proprietà della directory.

![Screenshot della pagina Proprietà con il pulsante di opzione impostato su No](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>Passaggi successivi

- [Individuare le risorse di Azure per la gestione in PIM](pim-resource-roles-discover-resources.md)
- [Configurare le impostazioni dei ruoli delle risorse di Azure in PIM](pim-resource-roles-configure-role-settings.md)
