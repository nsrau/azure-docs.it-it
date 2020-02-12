---
title: Aggiungere o rimuovere proprietari dei gruppi - Azure Active Directory | Microsoft Docs
description: Istruzioni su come aggiungere o rimuovere i proprietari dei gruppi usando Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f83a48ad13fe4ae217319b2aa85adf976aa6a7ae
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149848"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Aggiungere o rimuovere i proprietari dei gruppi in Azure Active Directory
I gruppi di Azure Active Directory (Azure AD) sono di proprietà e gestiti dai proprietari del gruppo. I proprietari del gruppo possono essere utenti o entità servizio e sono in grado di gestire il gruppo, inclusa l'appartenenza. Solo i proprietari del gruppo o gli amministratori di gestione dei gruppi esistenti possono assegnare i proprietari del gruppo. Non è necessario che il proprietario del gruppo sia un membro del gruppo.

Quando un gruppo non ha proprietario, gli amministratori di gestione dei gruppi possono comunque gestire il gruppo. Per ogni gruppo è consigliabile avere almeno un proprietario. Una volta che i proprietari sono assegnatiti a un gruppo, non è possibile rimuovere l'ultimo proprietario del gruppo. Assicurarsi di selezionare un altro proprietario prima di rimuovere l'ultimo proprietario dal gruppo.

## <a name="add-an-owner-to-a-group"></a>Aggiungere un proprietario a un gruppo
Di seguito sono riportate le istruzioni per l'aggiunta di un utente come proprietario a un gruppo tramite il portale di Azure AD. Per aggiungere un'entità servizio come proprietario di un gruppo, seguire le istruzioni per eseguire questa operazione usando [PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Per aggiungere un proprietario del gruppo
1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale per la directory.

2. Selezionare **Azure Active Directory**, **Gruppi** e quindi selezionare il gruppo per il quale si vuole aggiungere un proprietario. In questo esempio *MDM policy - West*.

3. Nella pagina **Panoramica MDM policy - West**  selezionare **Proprietari**.

    ![Pagina Panoramica MDM policy - West con l'opzione Proprietari evidenziata](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Nella pagina **MDM policy - West - Proprietari** selezionare **Aggiungi proprietari**, cercare e selezionare l'utente che sarà il nuovo proprietario del gruppo e quindi scegliere **Seleziona**.

    ![Pagina MDM policy - West - Proprietari con l'opzione Aggiungi proprietari evidenziata](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Dopo aver selezionato il nuovo proprietario, è possibile aggiornare la pagina **Proprietari** e visualizzare il nome aggiunto all'elenco dei proprietari.

## <a name="remove-an-owner-from-a-group"></a>Per rimuovere un proprietario da un gruppo
Rimuovere un proprietario da un gruppo con Azure AD.

### <a name="to-remove-an-owner"></a>Per rimuovere un proprietario
1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale per la directory.

2. Selezionare **Azure Active Directory**, **Gruppi** e quindi il gruppo per il quale si vuole rimuovere un proprietario. In questo esempio, *MDM policy - West*.

3. Nella pagina **Panoramica MDM policy - West**  selezionare **Proprietari**.

    ![Pagina Panoramica MDM policy - West con l'opzione Proprietari evidenziata](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Nella pagina **MDM policy - West - Proprietari** selezionare l'utente che si vuole rimuovere come proprietario del gruppo, scegliere **Rimuovi** dalla pagina delle informazioni dell'utente e selezionare **Sì** per confermare.

    ![Pagina delle informazioni dell'utente con l'opzione Rimuovi evidenziata](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Dopo aver rimosso il proprietario, è possibile tornare alla pagina **Proprietari** e verificare che il nome sia stato rimosso dall'elenco dei proprietari.

## <a name="next-steps"></a>Passaggi successivi
- [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)

- [Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo](../users-groups-roles/groups-settings-cmdlets.md)

- [Usare i gruppi per assegnare l'accesso a un'app SaaS integrata](../users-groups-roles/groups-saasapps.md)

- [Integrazione delle identità locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo](../users-groups-roles/groups-settings-v2-cmdlets.md)
