---
title: Che cosa sono le licenze basate sui gruppi in Azure AD? | Microsoft Docs
description: Informazioni sulle licenze basate sui gruppi di Azure Active Directory, come funzionano e procedure consigliate
services: active-directory
keywords: Licenze di Azure AD
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: fundamentals
ms.topic: overview
ms.workload: identity
ms.date: 06/13/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 5f5aab6a8514636e8be09deb773748080871b9d4
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005232"
---
# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Concetti base sulle licenze basate sui gruppi in Azure Active Directory

I servizi cloud Microsoft a pagamento come Office 365, Enterprise Mobility + Security, Dynamics 365 e altri prodotti simili richiedono la licenza. Tali licenze sono assegnate a ogni utente che deve accedere a tali servizi. Per gestire le licenze, gli amministratori usano uno dei portali di gestione di Office o di Azure e i cmdlet di PowerShell. Azure Active Directory è l'infrastruttura sottostante che supporta la gestione delle identità per tutti i servizi cloud Microsoft. Azure AD archivia le informazioni sugli stati di assegnazione delle licenze per gli utenti.

Fino ad ora, le licenze potevano essere assegnate solo a livello di utente singolo, ma questo può complicare la gestione su vasta scala. Ad esempio, per aggiungere o rimuovere licenze utente in base ai cambiamenti nell'organizzazione, come l'aggiunta o la rimozione di utenti dall'organizzazione o da un reparto, l'amministratore deve spesso scrivere un complesso script di PowerShell. Lo script esegue chiamate singole al servizio cloud.

Per risolvere tali problematiche, Azure AD comprende ora licenze basate sui gruppi. È possibile assegnare una o più licenze del prodotto a un gruppo. Azure AD fa in modo che le licenze vengano assegnate a tutti i membri del gruppo. A tutti i nuovi membri che si uniscono al gruppo vengono assegnate le licenze appropriate. Quando i membri abbandonano il gruppo, le licenze vengono rimosse. Questo elimina la necessità di automatizzare la gestione delle licenze tramite PowerShell per riflettere i cambiamenti nell'organizzazione e nella struttura dei reparti in base al singolo utente.

>[!Note]
>La gestione delle licenze basata su gruppi è una funzionalità in anteprima pubblica di Azure Active Directory (Azure AD) ed è disponibile con qualsiasi piano di licenza a pagamento di Azure AD. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="features"></a>Funzionalità

Di seguito sono riportate le licenze principali basate sui gruppi:

- È possibile assegnare le licenze a qualsiasi gruppo di sicurezza in Azure AD. I gruppi di sicurezza possono essere sincronizzati in locale tramite Azure AD Connect. È possibile anche creare gruppi di sicurezza direttamente in Azure AD (denominati anche gruppi solo cloud) o crearli automaticamente tramite la funzionalità gruppo dinamico di Azure AD.

- Quando viene assegnata una licenza del prodotto a un gruppo, l'amministratore può disabilitare uno o più piani di servizio nel prodotto. In genere, ciò avviene quando l'organizzazione non è ancora pronta a iniziare a usare un servizio incluso in un prodotto. Ad esempio, l'amministratore può assegnare Office 365 a un reparto, ma disabilitare temporaneamente il servizio Yammer.

- Sono supportati tutti i servizi cloud Microsoft che richiedono licenze a livello di utente. Sono inclusi tutti i prodotti di Office 365, Enterprise Mobility + Security e Dynamics 365.

- Le licenze basate sui gruppi sono attualmente disponibili solo tramite il [portale di Azure](https://portal.azure.com). Se si usano principalmente altri portali per la gestione di utenti e gruppi, ad esempio il portale di Office 365, è possibile continuare a farlo. Tuttavia, è necessario usare il portale di Azure per gestire le licenze a livello di gruppo.

- Azure AD gestisce automaticamente le modifiche alle licenze determinate da modifiche all'appartenenza a gruppi. In genere, le modifiche alle licenza sono attive dopo pochi minuti rispetto alle modifiche all'appartenenza.

- Un utente può essere membro di più gruppi con norme di licenza specificate. Un utente può anche disporre di alcune licenze assegnate in modo diretto, indipendentemente dai gruppi. Lo stato utente che ne risulta sarà una combinazione di tutte le licenze dei prodotti e servizi assegnati.

- In alcuni casi, non è possibile assegnare licenze a un utente. Ad esempio, potrebbe non essere disponibile un numero di licenze sufficiente nel tenant oppure è possibile che contemporaneamente siano stati assegnati servizi incompatibili. Gli amministratori hanno accesso alle informazioni sugli utenti per i quali Azure AD non ha potuto elaborare per intero le licenze di gruppo. Possono adottare misure correttive in base a tali informazioni.

- Durante l'anteprima pubblica, per usare la gestione delle licenze in base ai gruppi è necessaria una sottoscrizione a pagamento o di valutazione dell'edizione Basic o Premium di Azure AD nel tenant.

## <a name="your-feedback-is-welcome"></a>I commenti degli utenti sono molto apprezzati!

Se si hanno commenti o richieste di funzionalità, condividerle con Microsoft tramite [il forum di amministrazione di Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri scenari per la gestione delle licenze tramite i gruppi, vedere:

* [Assegnazione di licenze a un gruppo in Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Identificazione e risoluzione dei problemi relativi alle licenze per un gruppo in Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Come eseguire la migrazione di singoli utenti con licenza alle licenze basate sui gruppi in Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Scenari aggiuntivi relativi alle licenze basate sui gruppi in Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
