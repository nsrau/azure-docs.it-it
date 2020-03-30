---
title: Cerca e filtra i membri e i proprietari dei gruppi (anteprima) - Azure Active Directory Documenti Microsoft
description: Cercare e filtrare i membri e i proprietari dei gruppi nel portale di Azure.Search and filter groups members and owners in the Azure portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/28/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a815446b79b3e5ec0a75e5d179953956643b16c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206113"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Gruppi di ricerca e membri (anteprima) in Azure Active DirectorySearch groups and members (preview) in Azure Active Directory

Questo articolo illustra come cercare membri e proprietari di un gruppo e come usare i filtri di ricerca come parte dell'anteprima di miglioramento dei gruppi nel portale di Azure Active Directory (Azure AD). Ci sono molti miglioramenti nelle esperienze dei gruppi per aiutarti a gestire i tuoi gruppi, inclusi membri e proprietari, in modo rapido e semplice. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le modifiche in questa anteprima includono:

- Nuove funzionalità di ricerca di gruppi, ad esempio la ricerca di sottostringhe nei nomi dei gruppi
- Nuove opzioni di filtro e ordinamento negli elenchi di membri e proprietari
- Nuove funzionalità di ricerca per gli elenchi di membri e proprietari
- Conteggi di gruppi più accurati per gruppi di grandi dimensioni

## <a name="enabling-and-managing-the-preview"></a>Abilitazione e gestione dell'anteprima

Abbiamo semplificato l'iscrizione all'anteprima:

  1. Accedere al [portale](https://portal.azure.com)di Azure AD e selezionare **Gruppi**.
  2. Dalla pagina Gruppi - Tutti i gruppi, selezionare il banner nella parte superiore della pagina per partecipare all'anteprima.

È inoltre possibile estrarre le funzionalità e i miglioramenti più recenti selezionando **Anteprima informazioni** nella pagina Tutti **i gruppi.** Dopo aver unito l'anteprima, è possibile visualizzare il tag di anteprima in tutte le pagine di gruppo che presentano miglioramenti e fanno parte dell'anteprima. Non tutti i gruppi della pagina sono stati aggiornati come parte di questa anteprima.

In caso di problemi, è possibile tornare indietro all'esperienza legacy selezionando il banner nella parte superiore della pagina **Tutti i gruppi.** Apprezziamo il tuo feedback in modo da poter migliorare la nostra esperienza.

## <a name="group-search-and-sorting"></a>Ricerca e ordinamento dei gruppi

La ricerca nell'elenco dei gruppi è stata migliorata in modo `startswith` che quando è possibile immettere una stringa di ricerca, la ricerca esegua automaticamente una ricerca di sottostringa e di gruppo nell'elenco dei nomi dei gruppi. La ricerca di sottostringhe viene eseguita solo su parole intere e non include caratteri speciali. Per la ricerca di sottostringhe viene fatta distinzione tra maiuscole e minuscole.

![nuove ricerche di sottostringhe nella pagina Tutti i gruppi](./media/groups-members-owners-search/groups-search-preview.png)

Ad esempio, una ricerca di "criterio" ora restituirà sia "Criteri MDM – Ovest" che "Gruppo criteri". Un gruppo denominato "New_policy" non sarebbe restituito.

- È possibile eseguire la stessa ricerca anche negli elenchi di appartenenza ai gruppi.
- È ora possibile ordinare l'elenco dei gruppi per nome utilizzando le frecce a destra dell'intestazione di colonna del nome per ordinare l'elenco in ordine crescente o decrescente.

## <a name="group-member-search-and-filtering"></a>Ricerca e filtraggio dei membri del gruppo

### <a name="search-group-member-and-owner-lists"></a>Elenchi di membri e proprietari del gruppo di ricerca

È ora possibile cercare i membri di un gruppo specifico in base al nome ed eseguire la stessa ricerca nell'elenco dei proprietari di un gruppo. Nella nuova esperienza, se si immette una stringa nella casella di ricerca, un startswith search verrà eseguito automaticamente. Ad esempio, una ricerca di "Scott" restituirà Scott Wilkinson.

![nuove ricerche di sottostringhe negli elenchi dei membri e dei proprietari del gruppo](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Filtrare l'elenco dei membri e dei proprietari

Oltre alla ricerca, ora è possibile filtrare gli elenchi di membri e proprietari in base al tipo di utente. Queste sono le informazioni disponibili nella colonna Tipo di utente dell'elenco. In questo modo, è possibile filtrare l'elenco in base ai membri e agli ospiti per determinare se sono presenti ospiti nel gruppo.

### <a name="view-and-manage-membership"></a>Visualizzare e gestire l'appartenenza

Oltre a visualizzare i membri diretti di un gruppo specifico, è ora possibile visualizzare l'elenco di tutti i membri del gruppo all'interno della pagina Membri. L'elenco dei membri include tutti i membri univoci del gruppo, inclusi eventuali membri transitivi.

È inoltre possibile cercare e filtrare singolarmente l'elenco dei membri diretti e l'elenco di tutti i membri. Il filtro dell'elenco di tutti i membri non influisce sui filtri applicati all'elenco dei membri diretti.

## <a name="improved-group-member-counts"></a>Miglioramento dei conteggi dei membri del gruppo

Abbiamo migliorato la pagina **Panoramica** del gruppo per fornire i conteggi dei membri del gruppo per i gruppi di tutte le dimensioni. Puoi vedere i conteggi dei membri anche per i gruppi con più di 1.000 membri. È ora possibile visualizzare il numero totale di membri diretti per un gruppo e il numero totale di membri (tutti i membri univoci del gruppo, inclusi i membri transitivi) nella pagina **Panoramica.**

![Maggiore precisione nei conteggi dei membri del gruppo](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Passaggi successivi

Questi articoli forniscono informazioni aggiuntive sull'uso dei gruppi in Azure AD.

- [Visualizzare gruppi e membri](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Gestire l'appartenenza a gruppi](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gestire le regole dinamiche per gli utenti in un gruppo](groups-create-rule.md)
- [Modificare le impostazioni del gruppo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gestire l'accesso alle risorse con i gruppi](../fundamentals/active-directory-manage-groups.md)
- [Gestire l'accesso alle app SaaS con i gruppi](groups-saasapps.md)
- [Gestire i gruppi con i comandi di PowerShell](groups-settings-v2-cmdlets.md)
- [Aggiungere una sottoscrizione di Azure ad Azure Active DirectoryAdd an Azure subscription to Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
