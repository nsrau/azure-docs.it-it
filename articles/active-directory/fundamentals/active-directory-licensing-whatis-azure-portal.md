---
title: Che cosa sono le licenze basate sui gruppi - Azure Active Directory | Microsoft Docs
description: Informazioni sulle licenze basate sui gruppi di Azure Active Directory, tra cui modalità di funzionamento e procedure consigliate.
services: active-directory
keywords: Licenze di Azure AD
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: be7094db14f53af939ded2313823bf2749e59a46
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183153"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Che cosa sono le licenze basate sui gruppi in Azure Active Directory?

I servizi cloud Microsoft a pagamento come Office 365, Enterprise Mobility + Security, Dynamics 365 e altri prodotti simili richiedono la licenza. Tali licenze sono assegnate a ogni utente che deve accedere a tali servizi. Per gestire le licenze, gli amministratori usano uno dei portali di gestione di Office o di Azure e i cmdlet di PowerShell. Azure Active Directory è l'infrastruttura sottostante che supporta la gestione delle identità per tutti i servizi cloud Microsoft. Azure AD archivia le informazioni sugli stati di assegnazione delle licenze per gli utenti.

Fino ad ora, le licenze potevano essere assegnate solo a livello di utente singolo, ma questo può complicare la gestione su vasta scala. Ad esempio, per aggiungere o rimuovere licenze utente in base ai cambiamenti nell'organizzazione, come l'aggiunta o la rimozione di utenti dall'organizzazione o da un reparto, l'amministratore deve spesso scrivere un complesso script di PowerShell. Lo script esegue chiamate singole al servizio cloud.

Per risolvere tali problematiche, Azure AD comprende ora licenze basate sui gruppi. È possibile assegnare una o più licenze del prodotto a un gruppo. Azure AD fa in modo che le licenze vengano assegnate a tutti i membri del gruppo. A tutti i nuovi membri che si uniscono al gruppo vengono assegnate le licenze appropriate. Quando i membri abbandonano il gruppo, le licenze vengono rimosse. Questa gestione delle licenze elimina la necessità di automatizzare la gestione delle licenze tramite PowerShell in base ai cambiamenti avvenuti nell'organizzazione e nella struttura dei reparti per ogni utente.

## <a name="licensing-requirements"></a>Requisiti di licenza
Per usare licenze basate sui gruppi, è necessario avere una delle licenze seguenti:

- Sottoscrizione di una versione a pagamento o di valutazione di Azure AD Basic

- Edizione a pagamento o di valutazione di Office 365 Enterprise E3 o Office 365 A3 e versioni successive

### <a name="required-number-of-licenses"></a>Numero necessario di licenze
Per tutti i gruppi cui è assegnata una licenza, è anche necessario avere una licenza per ogni membro univoco. Anche se non è necessario assegnare una licenza a ogni membro del gruppo, è necessario avere un numero sufficiente di licenze per includere tutti i membri. Ad esempio, in presenza di 1.000 membri univoci che fanno parte di gruppi con licenze nel tenant, è necessario avere almeno 1.000 licenze per soddisfare il contratto di licenza.

## <a name="features"></a>Funzionalità

Di seguito sono riportate le licenze principali basate sui gruppi:

- È possibile assegnare le licenze a qualsiasi gruppo di sicurezza in Azure AD. I gruppi di sicurezza possono essere sincronizzati in locale tramite Azure AD Connect. È possibile anche creare gruppi di sicurezza direttamente in Azure AD (denominati anche gruppi solo cloud) o crearli automaticamente tramite la funzionalità gruppo dinamico di Azure AD.

- Quando viene assegnata una licenza del prodotto a un gruppo, l'amministratore può disabilitare uno o più piani di servizio nel prodotto. In genere, questa assegnazione avviene quando l'organizzazione non è ancora pronta a iniziare a usare un servizio incluso in un prodotto. Ad esempio, l'amministratore può assegnare Office 365 a un reparto, ma disabilitare temporaneamente il servizio Yammer.

- Sono supportati tutti i servizi cloud Microsoft che richiedono licenze a livello di utente. Sono inclusi tutti i prodotti Office 365, Enterprise Mobility + Security e Dynamics 365.

- Gestione delle licenze basate su gruppo è attualmente disponibile solo tramite il [portale di Azure](https://portal.azure.com). Se si usano principalmente altri portali di gestione per la gestione utenti e gruppi, ad esempio la [interfaccia di amministrazione di Microsoft 365](https://admin.microsoft.com), è possibile continuare a eseguire questa operazione. Tuttavia, è necessario usare il portale di Azure per gestire le licenze a livello di gruppo.

- Azure AD gestisce automaticamente le modifiche alle licenze determinate da modifiche all'appartenenza a gruppi. In genere, le modifiche alle licenza sono attive dopo pochi minuti rispetto alle modifiche all'appartenenza.

- Un utente può essere membro di più gruppi con norme di licenza specificate. Un utente può anche disporre di alcune licenze assegnate in modo diretto, indipendentemente dai gruppi. Lo stato utente che ne risulta sarà una combinazione di tutte le licenze dei prodotti e servizi assegnati. Se a un utente viene assegnata la stessa licenza da più origini, la licenza verrà utilizzata una sola volta.

- In alcuni casi, non è possibile assegnare licenze a un utente. Ad esempio, potrebbe non essere disponibile un numero di licenze sufficiente nel tenant oppure è possibile che contemporaneamente siano stati assegnati servizi incompatibili. Gli amministratori hanno accesso alle informazioni sugli utenti per i quali Azure AD non ha potuto elaborare per intero le licenze di gruppo. Possono adottare misure correttive in base a tali informazioni.

## <a name="your-feedback-is-welcome"></a>I commenti degli utenti sono molto apprezzati!

In caso di commenti e suggerimenti o richieste di funzionalità, è possibile condividerli con Microsoft nel [forum per amministratori di Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri scenari per la gestione delle licenze tramite i gruppi, vedere:

* [Assegnazione di licenze a un gruppo in Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Identificazione e risoluzione dei problemi relativi alle licenze per un gruppo in Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Come eseguire la migrazione di singoli utenti con licenza alle licenze basate sui gruppi in Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Come eseguire la migrazione degli utenti tra licenze di prodotti diverse con la gestione delle licenze basate su gruppo in Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Scenari aggiuntivi relativi alle licenze basate sui gruppi in Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
* [Esempi di PowerShell per le licenze basate sui gruppi in Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
