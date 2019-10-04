---
title: Eseguire ricerche e filtrare gruppi di membri e proprietari (anteprima)-Azure Active Directory | Microsoft Docs
description: Cercare e filtrare i membri e i proprietari dei gruppi nel portale di Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 09/10/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d11f5a9f2de8683365d663e85c275c3729ebf8a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901503"
---
# <a name="search-groups--and-members-preview-in-azure-active-directory"></a>Cerca in gruppi e membri (anteprima) in Azure Active Directory

Questo articolo illustra come cercare i membri e i proprietari di un gruppo e come usare i filtri di ricerca come parte dell'anteprima del miglioramento dei gruppi nel portale Azure Active Directory (Azure AD). Sono stati apportati numerosi miglioramenti all'esperienza dei gruppi che consentono di gestire i gruppi, inclusi i membri e i proprietari, in modo rapido e semplice. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le modifiche in questa versione di anteprima includono:

- Nuove funzionalità di ricerca di gruppi, ad esempio la ricerca di sottostringhe nei nomi dei gruppi
- Nuove opzioni di filtro e ordinamento per gli elenchi di membri e proprietari
- Nuove funzionalità di ricerca per gli elenchi di membri e proprietari
- Conteggi di gruppo più accurati per gruppi di grandi dimensioni

## <a name="enabling-and-managing-the-preview"></a>Abilitazione e gestione dell'anteprima

È stata semplificata la partecipazione all'anteprima:

  1. Accedere al portale di [Azure ad](https://portal.azure.com)e selezionare **gruppi**.
  2. Nella pagina gruppi-tutti i gruppi selezionare il banner nella parte superiore della pagina per partecipare all'anteprima.

È anche possibile consultare le funzionalità e i miglioramenti più recenti selezionando **informazioni sull'anteprima** nella pagina **tutti i gruppi** . Dopo aver partecipato all'anteprima, è possibile visualizzare il tag di anteprima in tutte le pagine dei gruppi che presentano miglioramenti e che fanno parte dell'anteprima. La pagina non ogni gruppo è stata aggiornata come parte di questa versione di anteprima.

Se si verificano problemi, è possibile tornare all'esperienza legacy selezionando il banner nella parte superiore della pagina tutti i **gruppi** . Microsoft è lieta di ricevere commenti e suggerimenti per migliorare l'esperienza.

## <a name="group-search-and-sorting"></a>Ricerca e ordinamento di gruppi

La ricerca nell'elenco dei gruppi è stata migliorata in modo che, quando è possibile immettere una stringa di ricerca `startswith` , eseguire automaticamente la ricerca di una sottostringa e un elenco di nomi di gruppi. La ricerca della sottostringa viene eseguita solo su parole intere e non include caratteri speciali.

![nuove ricerche di sottostringhe nella pagina tutti i gruppi](./media/groups-members-owners-search/groups-search-preview.png)

Ad esempio, una ricerca di "Policy" restituisce ora sia "MDM Policy-West" che "Group Policy". Non verrà restituito un gruppo denominato "New_policy".

- È possibile eseguire la stessa ricerca anche negli elenchi di appartenenza ai gruppi.
- È ora possibile ordinare l'elenco di gruppi in base al nome usando le frecce a destra dell'intestazione di colonna nome per ordinare l'elenco in ordine crescente o decrescente.

## <a name="group-member-search-and-filtering"></a>Ricerca e filtro di membri del gruppo

### <a name="search-group-member-and-owner-lists"></a>Elenchi di membri e proprietari del gruppo di ricerca

È ora possibile cercare i membri di un gruppo specifico in base al nome ed eseguire la stessa ricerca nell'elenco dei proprietari del gruppo. Nella nuova esperienza, se si immette una stringa nella casella di ricerca, verrà eseguita automaticamente una ricerca StartsWith. Ad esempio, una ricerca di "Scott" restituirà Scott Wilkinson.

![nuove ricerche di sottostringhe negli elenchi membri e proprietari del gruppo](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Elenco di membri e proprietari del filtro

Oltre alla ricerca, ora è possibile filtrare gli elenchi di membri e proprietari in base al tipo di utente. Si tratta delle informazioni disponibili nella colonna tipo utente dell'elenco. Quindi, è possibile filtrare l'elenco in base ai membri e ai guest per determinare se sono presenti Guest nel gruppo.

### <a name="view-and-manage-membership"></a>Visualizzare e gestire l'appartenenza

Oltre a visualizzare i membri diretti di un gruppo specifico, è ora possibile visualizzare l'elenco di tutti i membri del gruppo all'interno della pagina membri. L'elenco dei membri include tutti i membri univoci del gruppo, inclusi i membri transitivi.

È anche possibile cercare e filtrare individualmente l'elenco di membri diretti e l'elenco tutti i membri. Il filtro dell'elenco tutti i membri non influisce sui filtri applicati all'elenco dei membri diretti.

## <a name="improved-group-member-counts"></a>Conteggi dei membri del gruppo migliorati

È stata migliorata la pagina **Panoramica** gruppo per fornire i conteggi dei membri del gruppo per gruppi di tutte le dimensioni. È possibile visualizzare i conteggi dei membri anche per i gruppi con più di 1.000 membri. È ora possibile visualizzare il numero totale di membri diretti per un gruppo e il conteggio totale delle appartenenze (tutti i membri univoci del gruppo inclusi i membri transitivi) nella pagina **Panoramica** .

![Maggiore accuratezza nei conteggi delle appartenenze ai gruppi](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Passaggi successivi

Questi articoli forniscono informazioni aggiuntive sull'uso dei gruppi in Azure AD.

- [Visualizzare gruppi e membri](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Gestire l'appartenenza ai gruppi](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gestire le regole dinamiche per gli utenti in un gruppo](groups-create-rule.md)
- [Modificare le impostazioni del gruppo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gestire l'accesso alle risorse con i gruppi](../fundamentals/active-directory-manage-groups.md)
- [Gestire l'accesso alle app SaaS con i gruppi](groups-saasapps.md)
- [Gestire i gruppi con i comandi di PowerShell](groups-settings-v2-cmdlets.md)
- [Aggiungere una sottoscrizione di Azure a Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
