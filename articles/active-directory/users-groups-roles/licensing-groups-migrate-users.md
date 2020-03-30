---
title: Aggiungere utenti con licenze dirette alle licenze di gruppo - Azure AD Documenti Microsoft
description: Come eseguire la migrazione da licenze utente individuali a licenze basate su gruppo utilizzando Azure Active Directory
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
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c06d81f2f3f6cee781889d05ae08a1fd125df52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025671"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Come eseguire la migrazione di utenti con singole licenze a gruppi per la gestione delle licenze

È possibile che le licenze esistenti siano distribuite agli utenti delle organizzazioni tramite l'assegnazione diretta. ovvero l'utilizzo di script di PowerShell o di altri strumenti per assegnare licenze utente individuali. Prima di iniziare a utilizzare le licenze basate su gruppo per gestire le licenze nell'organizzazione, è possibile utilizzare questo piano di migrazione per sostituire senza problemi le soluzioni esistenti con le licenze basate su gruppo.

È importante evitare situazioni in cui la migrazione di licenze basate sui gruppi possa causare una perdita temporanea delle licenze attualmente assegnate agli utenti. Per non rischiare che gli utenti perdano l'accesso ai servizi e ai loro dati, è consigliabile evitare qualsiasi processo che possa comportare la rimozione delle licenze.

## <a name="recommended-migration-process"></a>Processo di migrazione consigliato

1. L'automazione esistente, ad esempio PowerShell, gestisce l'assegnazione di licenze agli utenti e la loro rimozione. Questo processo deve rimanere in esecuzione.

1. Creare un nuovo gruppo di licenze, o decidere quali usare tra i gruppi esistenti, e assicurarsi che tutti gli utenti necessari vengano aggiunti come membri.

1. Assegnare le licenze necessarie a tali gruppi facendo in modo di ricreare lo stesso stato delle licenze applicato agli utenti dall'automazione esistente, ad esempio PowerShell.

1. Verificare che le licenze siano state applicate a tutti gli utenti in tali gruppi. Tale applicazione è possibile verificando lo stato di elaborazione in ogni gruppo e consultando i log di controllo.

   - È possibile eseguire un controllo a campione su singoli utenti esaminandone i dettagli della licenza. Si noterà che le stesse licenze sono sia assegnate direttamente che ereditate dai gruppi.

   - È possibile eseguire uno script di PowerShell per [verificare la modalità di assegnazione delle licenze agli utenti](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Quando la licenza di uno stesso prodotto viene assegnata all'utente sia direttamente che tramite un gruppo, l'utente utilizza solo una licenza. Di conseguenza, non sono necessarie licenze aggiuntive per eseguire la migrazione.

1. Assicurarsi che non ci siano assegnazioni di licenze non riuscite verificando l'assenza di utenti in stato di errore in ogni gruppo. Per altre informazioni, vedere [Identificazione e risoluzione dei problemi relativi alle licenze per un gruppo in Azure Active Directory](licensing-groups-resolve-problems.md)

Valutare la possibilità di rimuovere le assegnazioni dirette originali. È consigliabile farlo gradualmente e monitorare prima il risultato su un sottoinsieme di utenti. Se è possibile lasciare le assegnazioni dirette originali agli utenti, ma quando gli utenti lasciano i gruppi con licenza mantengono le licenze assegnate direttamente, che potrebbe non essere quello desiderato.

## <a name="an-example"></a>Esempio

Un'organizzazione ha 1.000 utenti. Tutti gli utenti richiedono licenze di Office 365 Enterprise E3. Attualmente l'organizzazione ha uno script di PowerShell, in esecuzione in locale, che aggiunge e rimuove le licenze dagli utenti man mano che vengono aggiunti e rimossi. Tuttavia l'organizzazione vuole sostituire lo script con licenze basate sui gruppi in modo che queste ultime possano essere gestite automaticamente da Azure AD.

Di seguito è riportato un esempio di processo di migrazione:

1. Usando il portale di Azure, assegnare la licenza di Office 365 E3 al gruppo **Tutti gli utenti** in Azure AD.

1. Verificare che l'assegnazione della licenza sia stata completata per tutti gli utenti. Passare alla pagina di panoramica del gruppo, selezionare **Licenze**e controllare lo stato di elaborazione nella parte superiore del pannello **Licenze.**

   - Cercare "Le ultime modifiche della licenza sono state applicate a tutti gli utenti" per confermare che l'elaborazione è stata completata.

   - Nella parte superiore del pannello cercare una notifica relativa a eventuali utenti per i quali l'assegnazione della licenza non è riuscita. È possibile che il numero di licenze non sia sufficiente per tutti gli utenti. Alcuni utenti dispongono di piani di licenza in conflitto che impediscono loro di ereditare licenze di gruppo?

1. Eseguire un controllo a campione su alcuni utenti per verificare che siano state applicate sia le licenze dirette che quelle di gruppo. Passare alla pagina del profilo di un utente, selezionare **Licenze**ed esaminare lo stato delle licenze.

   - Di seguito è riportato lo stato previsto per l'utente durante la migrazione:

      ![lo stato utente previsto durante la migrazione](./media/licensing-groups-migrate-users/expected-user-state.png)

     Questo conferma che l'utente ha sia le licenze dirette che quelle ereditate. Vediamo che Office 365 E3 è assegnato.

   - Selezionare ogni licenza per vedere quali servizi sono abilitati. Per verificare che le licenze dirette e di gruppo abilitino esattamente gli stessi servizi per l'utente, selezionare **Assegnazioni**.

1. Dopo aver confermato che le licenze dirette e quelle di gruppo sono equivalenti, è possibile iniziare a rimuovere le licenze dirette dagli utenti. A scopo di test, è possibile rimuovere le licenze per singoli utenti dal portale e quindi eseguire gli script di automazione per rimuoverle in blocco. Di seguito è riportato un esempio dello stesso utente a cui sono state rimosse le licenze dirette mediante il portale. Si noti che lo stato della licenza rimane invariato, ma non vengono più visualizzate le assegnazioni dirette.

   ![confermare che le licenze dirette vengono rimosse](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni su altri scenari per la gestione delle licenze di gruppo:Learn more about other scenarios for group license management:

- [Che cosa sono le licenze basate sui gruppi in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Assegnazione di licenze a un gruppo in Azure Active Directory](licensing-groups-assign.md)
- [Identificazione e risoluzione dei problemi relativi alle licenze per un gruppo in Azure Active Directory](licensing-groups-resolve-problems.md)
- [Come eseguire la migrazione degli utenti tra licenze di prodotti diverse con la gestione delle licenze basate su gruppo in Azure Active Directory](licensing-groups-change-licenses.md)
- [Scenari aggiuntivi relativi alle licenze basate sui gruppi in Azure Active Directory](licensing-group-advanced.md)
- [Esempi di PowerShell per le licenze basate sui gruppi in Azure Active Directory](licensing-ps-examples.md)
