---

title: Assegnazione di licenze a un gruppo in Azure Active Directory | Microsoft Docs
description: Come assegnare licenze usando le licenze basate sui gruppi di Azure Active Directory
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
ms.openlocfilehash: cea745934c36297f5fe0867f4335a797a3ae2515
ms.lasthandoff: 02/22/2017


---

# <a name="assigning-licenses-to-a-group-in-azure-active-directory"></a>Assegnazione di licenze a un gruppo in Azure Active Directory

Questo articolo illustra in modo dettagliato uno scenario di base relativo all'assegnazione di licenze a un gruppo e alla verifica che tutti i membri del gruppo ottengano correttamente la licenza.

Nell'esempio, il tenant contiene un gruppo di sicurezza denominato **Reparto risorse umane** che include tutti i membri del reparto risorse umane, che in questo caso corrisponde a circa 1.000 utenti. L'amministratore desidera assegnare le licenze di Office 365 Enterprise E3 a tutto il reparto; il servizio Enterprise Yammer incluso nel prodotto deve essere disabilitato temporaneamente fino a un secondo momento quando il reparto sarà pronto per iniziare a usarlo. L'amministratore desidera inoltre distribuire le licenze Enterprise Mobility + Security allo stesso gruppo di utenti.

## <a name="step-1-assign-the-required-licenses"></a>Passaggio 1: assegnare le licenze necessarie

1. Accedere al [**Portale di Azure**](https://portal.azure.com) con un account amministratore. Per gestire le licenze, l'account richiede il ruolo amministratore globale o il ruolo amministratore dell'account utente.

2. Fare clic su **More Services** (Altri servizi) nel riquadro a sinistra e scegliere **Azure Active Directory**. È possibile inserire tra i preferiti questo pannello facendo clic sull'icona a stella o aggiungendolo al dashboard del portale.

3. Nel pannello **Azure Active Directory** selezionare **Licenze**. Verrà aperto un pannello in cui si possono visualizzare e gestire tutti i prodotti del tenant a cui è possibile assegnare una licenza.

4. In **All products** (Tutti i prodotti) selezionare sia Office 365 Enterprise E3 sia Enterprise Mobility + Security scegliendo i nomi di prodotto. Selezionare **Assegna** nella parte superiore del pannello per avviare l'assegnazione.

  ![tutti i prodotti, assegnare una licenza](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. Nel pannello **Assegna licenza** fare clic su **Users and groups** (Utenti e gruppi) per aprire il pannello relativo a utenti e gruppi. Cercare il nome del gruppo *Reparto risorse Umane*, selezionare il gruppo e quindi assicurarsi di confermare facendo clic su **Seleziona** nella parte inferiore del pannello.

  ![Selezionare un gruppo](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. Nel pannello **Assegna licenza** fare clic su **Opzioni di assegnazione (facoltativo)** che consente di visualizzare tutti i piani di servizio compresi i due prodotti selezionati in precedenza. Trovare Enterprise Yammer e disattivarlo (**Off**) per disattivare il servizio dalla licenza del prodotto. Confermare facendo clic su **OK** nella parte inferiore di **Opzioni di assegnazione**.

  ![opzioni di assegnazione](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. Infine, nel pannello **Assegna licenza** fare clic su **Assegna** nella parte inferiore del pannello per completare l'assegnazione.

8. Verrà visualizzata una notifica nell'angolo in alto a destra che mostra lo stato e il risultato del processo. Se non è stato possibile completare l'assegnazione al gruppo (ad esempio a causa di licenze già esistenti nel gruppo), fare clic sulla notifica per visualizzare i dettagli dell'errore.

Ora è stato specificato un modello di licenza nel gruppo del reparto risorse umane. È stato avviato un processo in background in Azure AD per elaborare tutti i membri esistenti di tale gruppo. L'operazione iniziale potrebbe richiedere molto tempo, in base alla dimensione corrente del gruppo. Nel passaggio successivo si descrive come verificare che il processo sia stato completato e se sono necessari ulteriori interventi per la risoluzione dei problemi.

> [!NOTE]
> La stessa assegnazione può essere avviata da un percorso alternativo: **Users and groups** (Utenti e gruppi) in Azure AD. Passare a **Azure Active Directory &gt; Utenti e gruppi &gt;All groups** (Azure Active Directory > Utenti e gruppi > Tutti i gruppi), trovare il gruppo, selezionarlo e visualizzare la scheda **Licenze**. Con il pulsante **Assegna** nella parte superiore del pannello è possibile aprire il pannello di assegnazione delle licenze.

## <a name="step-2-verify-that-the-initial-assignment-has-completed"></a>Passaggio 2: verificare che l'assegnazione iniziale sia stata completata

1. Passare a **Azure Active Directory &gt; Users and groups &gt; All groups** (Azure Active Directory > Utenti e gruppi > Tutti i gruppi) e trovare il gruppo *Reparto risorse umane* al quale sono state assegnate le licenze.

2. Nel pannello del gruppo *Reparto risorse umane* selezionare **Licenze** per verificare rapidamente se le licenze sono state completamente assegnate agli utenti e se sono presenti errori che richiedono attenzione, tra cui:

  - Licenze di prodotto assegnate al gruppo. Selezionare una voce per visualizzare i servizi specifici che sono stati abilitati e apportare modifiche.

  - Stato delle modifiche più recenti dell'assegnazione delle licenze: se le modifiche vengono elaborate o se è stata completata l'elaborazione per tutti i membri utente.

  - Se si sono verificati errori, informazioni sugli utenti in stato di errore, per cui non è stato possibile assegnare le licenze.

  ![opzioni di assegnazione](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. Informazioni più dettagliate sull'elaborazione delle licenze sono disponibili in * *Azure Active Directory &gt; Users and groups (Utenti e gruppi) &gt; 
     *groupname* &gt; Log di controllo * *:

  - Attività: **iniziare ad applicare agli utenti le licenze basate sui gruppi**. Viene registrato quando il sistema preleva la modifica dell'assegnazione della licenza per il gruppo e avvia l'applicazione a tutti i membri utente. Contiene informazioni sulla modifica apportata.

  - Attività: **finire di applicare agli utenti le licenze basate sui gruppi**. Viene registrato quando il nostro sistema completa l'elaborazione di tutti gli utenti del gruppo. Contiene un riepilogo di quanti utenti sono stati elaborati correttamente e il numero di utenti a cui non è stato possibile assegnare le licenze di gruppo.

## <a name="step-3-checking-for-license-problems-and-resolving-them"></a>Passaggio 3: controllare i problemi relativi alle licenze e risolverli

1. Passare a **Azure Active Directory &gt; Users and groups &gt; All groups** (Azure Active Directory > Utenti e gruppi > Tutti i gruppi) e trovare il gruppo *Reparto risorse umane* al quale sono state assegnate le licenze.

2. Nel pannello del gruppo **Reparto Risorse umane** selezionare **Licenze**. La notifica in alto nel pannello indica che esistono 10 utenti per cui non è possibile assegnare le licenze. Viene aperto un elenco di tutti gli utenti che presentano un errore di licenza per questo gruppo.

3. La colonna **Failed assignments** (Assegnazioni non riuscite) indica che è stato impossibile assegnare entrambe le licenze prodotto agli utenti. **Principali cause dell'errore** contiene la causa dell'errore, in questo caso **Piani di servizio in conflitto**.

  ![assegnazioni non riuscite](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. Selezionare un utente per aprire il pannello **Licenze** contenente tutte le licenze attualmente assegnate all'utente. In questo esempio, è possibile vedere che l'utente dispone della licenza di Office 365 Enterprise E1 ereditata dal gruppo **Utenti del chiosco**. È in conflitto con la licenza E3 che il sistema tenta di applicare dal gruppo **Reparto risorse umane**; di conseguenza, nessuna delle licenze da tale gruppo è stata assegnata all'utente.

  ![visualizza licenze per un utente](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. Per risolvere questo problema, è possibile rimuovere l'utente dal gruppo **Utenti del chiosco**. Dopo che Azure AD elabora la modifica, le licenze del **Reparto risorse umane** sono assegnate correttamente.

  ![licenza assegnata correttamente](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità disponibili per la gestione delle licenze tramite i gruppi, consultare

* [Che cosa sono le licenze basate sui gruppi in Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Identificazione e risoluzione dei problemi relativi alle licenze per un gruppo in Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Come eseguire la migrazione di singoli utenti con licenza alle licenze basate sui gruppi in Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Scenari aggiuntivi relativi alle licenze basate sui gruppi in Azure Active Directory](active-directory-licensing-group-advanced.md)

