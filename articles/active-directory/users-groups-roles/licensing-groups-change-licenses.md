---
title: Modificare i piani di licenza per utenti e gruppi - Azure AD Documenti Microsoft
description: Come eseguire la migrazione degli utenti all'interno di un gruppo a piani di servizio diversi usando la gestione delle licenze di gruppo in Azure Active DirectoryHow to migrate users within a group to different service plans using group licensing in Azure Active Directory
services: active-directory
keywords: Licenze di Azure AD
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2f04e1728f94c89bddcc31c287cc017a79020f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025898"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Modificare le assegnazioni delle licenze per un utente o un gruppo in Azure Active DirectoryChange license assignments for a user or group in Azure Active Directory

Questo articolo descrive come spostare utenti e gruppi tra i piani di licenza del servizio in Azure Active Directory (Azure AD). L'approccio di Azure AD obiettivo consiste nel garantire che non si perdano alcuna perdita di servizio o dati durante la modifica della licenza. Gli utenti dovrebbero passare da un servizio all'altro senza problemi. I passaggi di assegnazione del piano di licenza descritti in questo articolo descrivono la modifica di un utente o di un gruppo in Office 365 E1 in Office 365 E3, ma i passaggi si applicano a tutti i piani di licenza. Quando si aggiornano le assegnazioni delle licenze per un utente o un gruppo, le rimozioni delle assegnazioni delle licenze e le nuove assegnazioni vengono eseguite contemporaneamente in modo che gli utenti non perdano l'accesso ai propri servizi durante le modifiche delle licenze o vedano conflitti di licenza tra i piani.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di aggiornare le assegnazioni delle licenze, è importante verificare che determinati presupposti siano veri per tutti gli utenti o gruppi da aggiornare. Se i presupposti non sono vere per tutti gli utenti di un gruppo, la migrazione potrebbe non riuscire per alcuni. e, di conseguenza, questi potrebbero perdere l'accesso ai dati o ai servizi. Assicurarsi che:

- Gli utenti hanno il piano di licenza corrente (in questo caso, Office 365 E1) che viene assegnato a un gruppo ed ereditato dall'utente e non assegnato direttamente.

- Si dispone di un numero sufficiente di licenze disponibili per il piano di licenze che si sta assegnando. Se non si dispone di licenze sufficienti, ad alcuni utenti potrebbe non essere assegnato il nuovo piano di licenza. È possibile verificare il numero di licenze disponibili.

- Gli utenti non dispongono di altre licenze di servizio assegnate che possono entrare in conflitto con la licenza desiderata o impedire la rimozione della licenza corrente. Ad esempio, una licenza di un servizio come Workplace Analytics o Project Online che ha una dipendenza da altri servizi.

- Se si gestiscono gruppi in locale e li si sincronizza in Azure AD tramite Azure AD Connect, è possibile aggiungere o rimuovere utenti usando il sistema locale. La sincronizzazione delle modifiche con Azure AD può richiedere del tempo tramite licenza di gruppo.

- Se si usano appartenenze a gruppi dinamici di Azure AD, aggiungere o rimuovere utenti modificandone gli attributi, ma il processo di aggiornamento per le assegnazioni delle licenze rimane invariato.

## <a name="change-user-license-assignments"></a>Modificare le assegnazioni delle licenze utente

Nella pagina **Aggiorna assegnazioni licenze,** se alcune caselle di controllo non sono disponibili, indica i servizi che non possono essere modificati perché sono ereditati da una licenza di gruppo.

1. Accedere al [portale](https://portal.azure.com/) di Azure usando un account di amministratore delle licenze nell'organizzazione di Azure AD.
1. Selezionare**Utenti** **di Azure Active Directory** > e quindi aprire la pagina **Profilo** per un utente.
1. Selezionare **Licenze**.
1. Selezionare **Assegnazioni** per modificare l'assegnazione delle licenze per l'utente o il gruppo. La pagina **Assegnazioni** consente di risolvere i conflitti di assegnazione delle licenze.
1. Selezionare la casella di controllo per Office 366 E3 e assicurarsi che siano selezionati almeno tutti i servizi E1 assegnati all'utente.
1. Deselezionare la casella di controllo per Office 365 E1.

    ![pagina assegnazioni licenze per un utente che mostra Office 365 E1 deselezionato e Office 365 E3 selezionato](media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Selezionare **Salva**.

Azure AD applica le nuove licenze e rimuove le licenze precedenti contemporaneamente per garantire la continuità del servizio.

## <a name="change-group-license-assignments"></a>Modificare le assegnazioni delle licenze di gruppo

1. Accedere al [portale](https://portal.azure.com/) di Azure usando un account di amministratore delle licenze nell'organizzazione di Azure AD.
1. Selezionare**Gruppi**di **Azure Active Directory** > e quindi aprire la pagina **Panoramica** per un gruppo.
1. Selezionare **Licenze**.
1. Selezionare il comando **Assegnazioni** per modificare l'assegnazione delle licenze per l'utente o il gruppo.
1. Selezionare la casella di controllo per Office 366 E3. Per mantenere la continuità del servizio, assicurarsi di selezionare tutti i servizi E1 già assegnati all'utente.
1. Deselezionare la casella di controllo per Office 365 E1.

    ![Selezionare il comando Assegnazioni nella pagina Licenze di un utente o di un gruppo](media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Selezionare **Salva**.

Per garantire la continuità del servizio, Azure AD applica le nuove licenze e rimuove le licenze precedenti contemporaneamente per tutti gli utenti del gruppo.

## <a name="next-steps"></a>Passaggi successivi

Maggiori informazioni su altri scenari per la gestione delle licenze tramite i gruppi sono disponibili negli articoli seguenti:

- [Assegnazione di licenze a un gruppo in Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
- [Identificazione e risoluzione dei problemi relativi alle licenze per un gruppo in Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
- [Come eseguire la migrazione di singoli utenti con licenza a licenze di gruppo in Azure Active DirectoryHow to migrate individual licensed users to group licensing in Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
- [Scenari aggiuntivi relativi alle licenze dei gruppi di Azure Active DirectoryAzure Active Directory group licensing additional scenarios](../users-groups-roles/licensing-group-advanced.md)
- [Esempi di PowerShell per la gestione delle licenze di gruppo in Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
