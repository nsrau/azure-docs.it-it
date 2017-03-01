---

title: Che cosa sono le licenze basate sui gruppi in Azure Active Directory? | Microsoft Docs
description: Informazioni sulle licenze basate sui gruppi di Azure Active Directory, come funzionano, come iniziare e procedure consigliate
services: active-directory
keywords: Licenze di Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 6a9cebafd1ad8f513bfab897970241f7b82b2a53
ms.openlocfilehash: 1f4e0904c9025ce0b21d904acc7b959e823039ef
ms.lasthandoff: 02/22/2017


---

# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Che cosa sono le licenze basate sui gruppi in Azure Active Directory?

Per i servizi cloud Microsoft come Office 365, Enterprise Mobility + Security, Dynamics CRM e altri prodotti simili, le licenze devono essere assegnate a ogni utente che deve accedere a tali servizi. La gestione delle licenze viene esposta agli amministratori tramite uno dei portali di gestione di Office o di Azure e i cmdlet di PowerShell. Lo stato di assegnazione delle licenze viene archiviato in Azure Active Directory. L'infrastruttura sottostante supporta la gestione delle identità per tutti i servizi cloud Microsoft.

Fino ad ora, le licenze potevano essere assegnate solo a livello di utente singolo, ma questo può complicare la gestione su vasta scala per i clienti. Ad esempio, per aggiungere o rimuovere licenze utente in base ai cambiamenti nell'organizzazione, come l'aggiunta o la rimozione di utenti dall'organizzazione o da un reparto, l'amministratore deve spesso scrivere un complesso script di PowerShell per l'esecuzione di chiamate singole al servizio cloud.

Per risolvere questi problemi, è stata introdotta una nuova funzionalità del sistema di gestione delle licenze di Azure AD, ovvero le licenze basate sui gruppi. Ora è possibile assegnare una o più licenze del prodotto a un gruppo. Azure AD fa in modo che le licenze vengano assegnate a tutti i membri del gruppo. Ai nuovi membri che vengono aggiunti al gruppo vengono assegnate le licenze appropriate, che verranno rimosse quando gli utenti lasciano il gruppo. Questo elimina la necessità di automatizzare la gestione delle licenze tramite PowerShell per riflettere i cambiamenti nell'organizzazione e nella struttura dei reparti in base al singolo utente.

## <a name="features"></a>Funzionalità

Di seguito sono riportate le funzionalità principali delle licenze basate sui gruppi:

- È possibile assegnare le licenze a qualsiasi gruppo di sicurezza in Azure AD. È possibile sincronizzare i gruppi di sicurezza in locale con Azure AD Connect, crearli direttamente in Azure AD come gruppi solo cloud o crearli automaticamente tramite la funzionalità gruppo dinamico di Azure AD.

- Quando viene assegnata una licenza del prodotto a un gruppo, l'amministratore può disabilitare uno o più piani di servizio nel prodotto. In genere, questa operazione viene eseguita quando l'organizzazione non è ancora pronta a iniziare a usare un servizio incluso in un prodotto. Ad esempio, l'amministratore potrebbe voler assegnare Office 365 E3 a un reparto, ma disabilitare temporaneamente il servizio Yammer Enterprise.

- Sono supportati tutti i servizi cloud Microsoft che richiedono licenze a livello di utente. Sono inclusi tutti i prodotti di Office 365, Enterprise Mobility + Security, Dynamics CRM e così via.

- Le licenze basate sui gruppi sono attualmente disponibili solo tramite il [portale di Azure](https://portal.azure.com). I clienti che usano principalmente altri portali per la gestione di utenti e gruppi, ad esempio il portale di Office 365, possono continuare a farlo, ma dovranno usare il portale di Azure per gestire le licenze a livello di gruppo.

- Azure AD gestisce automaticamente le modifiche alle licenze determinate da modifiche all'appartenenza a gruppi. In genere, per un utente che viene aggiunto o rimosso da un gruppo le licenze vengono modificate entro pochi minuti dalla modifica all'appartenenza.

- Un utente potrebbe essere membro di più gruppi con criteri di licenza specificati e potrebbe avere licenze assegnategli direttamente all'esterno dei gruppi. Lo stato utente che ne risulta sarà una combinazione di tutte le licenze dei prodotti e servizi assegnati.

- In alcuni casi non è possibile assegnare licenze a un utente, ad esempio perché le licenze disponibili nel tenant non sono sufficienti oppure a causa di servizi in conflitto assegnati contemporaneamente. Gli amministratori hanno accesso alle informazioni sugli utenti per i quali Azure AD non ha potuto elaborare per intero le licenze di gruppo e possono adottare misure correttive in base a tali informazioni.

- Durante l'anteprima pubblica, per usare la gestione delle licenze in base ai gruppi è necessaria una sottoscrizione a pagamento o di valutazione di Azure AD Basic o versione successiva nel tenant. A ogni utente che eredita licenze da gruppi deve anche essere assegnata la licenza di Azure AD a pagamento.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri scenari per la gestione delle licenze tramite i gruppi, vedere:

* [Assegnazione di licenze a un gruppo in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identificazione e risoluzione dei problemi relativi alle licenze per un gruppo in Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Come eseguire la migrazione di singoli utenti con licenza alle licenze basate sui gruppi in Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Scenari aggiuntivi relativi alle licenze basate sui gruppi in Azure Active Directory](active-directory-licensing-group-advanced.md)

