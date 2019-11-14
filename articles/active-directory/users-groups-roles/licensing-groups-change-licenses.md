---
title: Modificare i piani di licenza per utenti e gruppi-Azure AD | Microsoft Docs
description: Come eseguire la migrazione di utenti all'interno di un gruppo a piani di servizio diversi usando le licenze di gruppo in Azure Active Directory
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025898"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Modificare le assegnazioni delle licenze per un utente o un gruppo in Azure Active Directory

Questo articolo descrive come spostare utenti e gruppi tra i piani di licenza del servizio in Azure Active Directory (Azure AD). Lo scopo Azure AD è quello di garantire che non si verifichino perdite di dati o servizi durante la modifica della licenza. Gli utenti devono passare agevolmente tra i servizi. I passaggi di assegnazione del piano di licenza in questo articolo descrivono come modificare un utente o un gruppo in Office 365 E1 in Office 365 E3, ma i passaggi sono validi per tutti i piani di licenza. Quando si aggiornano le assegnazioni di licenze per un utente o un gruppo, le rimozioni di assegnazione delle licenze e le nuove assegnazioni vengono effettuate simultaneamente in modo che gli utenti non perdano l'accesso ai servizi durante le modifiche delle licenze o visualizzino conflitti di licenza tra i piani.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di aggiornare le assegnazioni di licenze, è importante verificare che alcuni presupposti siano veri per tutti gli utenti o i gruppi da aggiornare. Se i presupposti non sono veri per tutti gli utenti di un gruppo, la migrazione potrebbe non riuscire per alcuni. e, di conseguenza, questi potrebbero perdere l'accesso ai dati o ai servizi. Assicurarsi che:

- Gli utenti dispongono del piano di licenza corrente (in questo caso, Office 365 E1) assegnato a un gruppo e ereditato dall'utente e non assegnato direttamente.

- Si dispone di un numero sufficiente di licenze disponibili per il piano di licenza che si sta assegnando. Se non si dispone di un numero sufficiente di licenze, a alcuni utenti potrebbe non essere assegnato il nuovo piano di licenza. È possibile controllare il numero di licenze disponibili.

- Agli utenti non sono assegnate altre licenze di servizio che possono essere in conflitto con la licenza desiderata o impedire la rimozione della licenza corrente. Ad esempio, una licenza di un servizio, ad esempio l'analisi dell'area di lavoro o Project online, che ha una dipendenza da altri servizi.

- Se si gestiscono i gruppi in locale e li si sincronizza in Azure AD tramite Azure AD Connect, è possibile aggiungere o rimuovere utenti usando il sistema locale. La sincronizzazione delle modifiche con Azure AD può richiedere del tempo per essere prelevata dalle licenze di gruppo.

- Se si usa Azure AD appartenenza dinamica ai gruppi, è possibile aggiungere o rimuovere gli utenti cambiandone gli attributi, ma il processo di aggiornamento per le assegnazioni delle licenze rimane invariato.

## <a name="change-user-license-assignments"></a>Modificare le assegnazioni di licenze utente

Nella pagina **Aggiorna assegnazioni licenze** , se si nota che alcune caselle di controllo non sono disponibili, indica che i servizi non possono essere modificati perché sono ereditati da una licenza di gruppo.

1. Accedere al [portale di Azure](https://portal.azure.com/) usando un account amministratore licenze nell'organizzazione Azure ad.
1. Selezionare **Azure Active Directory** > **utenti**, quindi aprire la pagina del **profilo** per un utente.
1. Selezionare **Licenze**.
1. Selezionare **assegnazioni** per modificare l'assegnazione delle licenze per l'utente o il gruppo. Nella pagina **assegnazioni** è possibile risolvere i conflitti di assegnazione delle licenze.
1. Selezionare la casella di controllo per Office 366 E3 e assicurarsi che siano selezionati almeno tutti i servizi E1 assegnati all'utente.
1. Deselezionare la casella di controllo per Office 365 E1.

    ![pagina assegnazioni di licenze per un utente che visualizza Office 365 E1 cancellato e Office 365 E3 selezionato](media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Selezionare **Salva**.

Azure AD applica le nuove licenze e rimuove contemporaneamente le licenze precedenti per garantire la continuità del servizio.

## <a name="change-group-license-assignments"></a>Modificare le assegnazioni delle licenze di gruppo

1. Accedere al [portale di Azure](https://portal.azure.com/) usando un account amministratore licenze nell'organizzazione Azure ad.
1. Selezionare **Azure Active Directory** **gruppi**di > , quindi aprire la pagina **Panoramica** per un gruppo.
1. Selezionare **Licenze**.
1. Selezionare il comando **assegnazioni** per modificare l'assegnazione delle licenze per l'utente o il gruppo.
1. Selezionare la casella di controllo per Office 366 E3. Per mantenere la continuità del servizio, assicurarsi di selezionare tutti i servizi E1 già assegnati all'utente.
1. Deselezionare la casella di controllo per Office 365 E1.

    ![Selezionare il comando assegnazioni nella pagina licenze utente o gruppo](media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Selezionare **Salva**.

Per garantire la continuità del servizio, Azure AD applica le nuove licenze e rimuove contemporaneamente le licenze precedenti per tutti gli utenti del gruppo.

## <a name="next-steps"></a>Passaggi successivi

Maggiori informazioni su altri scenari per la gestione delle licenze tramite i gruppi sono disponibili negli articoli seguenti:

- [Assegnazione di licenze a un gruppo in Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
- [Identificazione e risoluzione dei problemi relativi alle licenze per un gruppo in Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
- [Come eseguire la migrazione di singoli utenti con licenza alle licenze di gruppo in Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
- [Scenari aggiuntivi di gestione licenze del gruppo Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
- [Esempi di PowerShell per le licenze di gruppo in Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
