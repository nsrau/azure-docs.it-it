---

title: Come eseguire la migrazione di singoli utenti con licenza in un gruppo in Azure Active Directory | Documentazione Microsoft
description: Come passare da licenze per utenti singoli a licenze basate sui gruppi usando Azure Active Directory
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
ms.date: 02/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: d7c5ba12738c4713517743ae8c44e236c5e1a210
ms.lasthandoff: 03/08/2017


---

# <a name="how-to-add-licensed-users-to-a-group-for-licensing-in-azure-active-directory"></a>Come aggiungere gli utenti con licenza a un gruppo per la gestione delle licenze in Azure Active Directory

Le licenze esistenti potrebbero essere state distribuite agli utenti nelle organizzazioni tramite "assegnazione diretta", vale a dire usando script di PowerShell o altri strumenti per assegnare le licenze utente individuali. Se si vuole iniziare a usare le licenze basate sui gruppi per la gestione delle licenze dell'organizzazione, è necessario un piano di migrazione che permetta di sostituire facilmente le soluzioni esistenti con le licenze basate sui gruppi.

È importante evitare situazioni in cui la migrazione di licenze basate sui gruppi possa causare una perdita temporanea delle licenze attualmente assegnate agli utenti. Per non rischiare che gli utenti perdano l'accesso ai servizi e ai loro dati, è consigliabile evitare qualsiasi processo che possa comportare la rimozione delle licenze.

## <a name="recommended-migration-process"></a>Processo di migrazione consigliato

1. L'automazione esistente, ad esempio PowerShell, gestisce l'assegnazione di licenze agli utenti e la loro rimozione. Questo processo deve rimanere in esecuzione.

2. Creare un nuovo gruppo di licenze, o decidere quali usare tra i gruppi esistenti, e assicurarsi che tutti gli utenti necessari vengano aggiunti come membri.

3. Assegnare le licenze necessarie a tali gruppi facendo in modo di ricreare lo stesso stato delle licenze applicato agli utenti dall'automazione esistente, ad esempio PowerShell.

4. Verificare che le licenze siano state applicate a tutti gli utenti in tali gruppi. A tale scopo, è possibile verificare lo stato di elaborazione in ogni gruppo e vedere i log di controllo.

  - È possibile eseguire un controllo a campione su singoli utenti esaminandone i dettagli della licenza. Si noterà che le stesse licenze sono sia assegnate direttamente che ereditate dai gruppi.

  - È possibile eseguire uno script di PowerShell per [verificare la modalità di assegnazione delle licenze agli utenti](active-directory-licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

  - Quando la licenza di uno stesso prodotto viene assegnata all'utente sia direttamente che tramite un gruppo, l'utente utilizza solo una licenza. Di conseguenza, non sono necessarie licenze aggiuntive per eseguire la migrazione.

5. Assicurarsi che non ci siano assegnazioni di licenze non riuscite verificando l'assenza di utenti in stato di errore in ogni gruppo. Per altre informazioni, vedere [Identificazione e risoluzione dei problemi relativi alle licenze per un gruppo in Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)

6. Valutare la possibilità di rimuovere le assegnazioni dirette originali. È possibile eseguire questa operazione in più fasi, per monitorare prima il risultato in un subset di utenti.

  È possibile lasciare le assegnazioni dirette originali. Così facendo, tuttavia, quando gli utenti lasciano i relativi gruppi con licenza, mantengono la licenza originale e questo potrebbe non essere auspicabile.

## <a name="an-example"></a>Esempio

Si consideri ad esempio un'organizzazione con 1.000 utenti. Tutti gli utenti devono avere licenze di Enterprise Mobility + Security (EMS). I 200 utenti nell'ufficio finanziario devono avere la licenza di Office 365 Enterprise E3. Uno script di PowerShell, in esecuzione in locale, aggiunge e rimuove le licenze dagli utenti man mano che vengono aggiunti e rimossi. L'obiettivo è sostituire lo script con licenze basate sui gruppi in modo che le licenze vengano gestite automaticamente da Azure AD.

Di seguito è riportato un esempio di processo di migrazione:

1. Usare il portale di Azure per assegnare la licenza di EMS al gruppo **Tutti gli utenti** in Azure AD. Assegnare la licenza di E3 al gruppo **Ufficio finanziario** che contiene tutti gli utenti necessari.

2. Per ogni gruppo, assicurarsi che l'assegnazione della licenza sia stata completata per tutti gli utenti. Passare al pannello di ogni singolo gruppo, selezionare **Licenze**e controllare lo stato di elaborazione nella parte superiore del pannello **Licenze**.

  - Cercare "Le ultime modifiche della licenza sono state applicate a tutti gli utenti" per confermare che l'elaborazione è stata completata.

  - Nella parte superiore del pannello cercare una notifica relativa a eventuali utenti per i quali l'assegnazione della licenza non è riuscita. È possibile che il numero di licenze non sia sufficiente per tutti gli utenti. È anche possibile che alcuni utenti abbiano SKU di licenza in conflitto che impediscono loro di ereditare le licenze di gruppo.

3. Eseguire un controllo a campione su alcuni utenti per verificare che siano state applicate sia le licenze dirette che quelle di gruppo. Passare al pannello di un utente, selezionare **Licenze**ed esaminare lo stato delle licenze.

  - Di seguito è riportato lo stato previsto per l'utente durante la migrazione:

      ![Stato previsto per l'utente](media/active-directory-licensing-group-migration-azure-portal/expected-user-state.png)

  Questo conferma che l'utente ha sia le licenze dirette che quelle ereditate. Risultano assegnate le licenze sia per **EMS** che per **E3**.

  - Selezionare ogni licenza per visualizzare i dettagli relativi ai servizi abilitati. Queste informazioni possono essere usate per verificare se le licenze dirette e quelle di gruppo abilitano esattamente gli stessi piani di servizio per l'utente.

      ![Verificare i piani di servizio](media/active-directory-licensing-group-migration-azure-portal/check-service-plans.png)

4. Dopo aver confermato che le licenze dirette e quelle di gruppo sono equivalenti, è possibile iniziare a rimuovere le licenze dirette dagli utenti. A scopo di test, è possibile rimuovere le licenze per singoli utenti dal portale e quindi eseguire gli script di automazione per rimuoverle in blocco. Di seguito è riportato un esempio dello stesso utente a cui sono state rimosse le licenze dirette mediante il portale. Si noti che lo stato della licenza rimane invariato, ma non vengono più visualizzate le assegnazioni dirette.

  ![Licenze dirette rimosse](media/active-directory-licensing-group-migration-azure-portal/direct-licenses-removed.png)


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri scenari per la gestione delle licenze tramite i gruppi, vedere:

* [Assegnazione di licenze a un gruppo in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Che cosa sono le licenze basate sui gruppi in Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Identificazione e risoluzione dei problemi relativi alle licenze per un gruppo in Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Scenari aggiuntivi relativi alle licenze basate sui gruppi in Azure Active Directory](active-directory-licensing-group-advanced.md)

