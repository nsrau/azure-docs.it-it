---
title: Assegnare licenze a un gruppo - Azure Active Directory | Microsoft Docs
description: Come assegnare licenze agli utenti usando le licenze dei gruppi di Azure Active Directory
services: active-directory
keywords: Licenze di Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a54d1ad3ab809f2a2f8df6ae0e30b1b061c2be1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60471331"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Assegnare licenze agli utenti in base all'appartenenza ai gruppi in Azure Active Directory

Questo articolo spiega come assegnare licenze di prodotti a un gruppo di utenti in Azure Active Directory (Azure AD) e quindi verificare che le licenze siano state assegnate in modo corretto.

In questo esempio il tenant contiene un gruppo di sicurezza denominato **HR Department**. Il gruppo include tutti i membri del reparto Risorse umane, circa 1.000 utenti. Si vogliono assegnare licenze di Office 365 Enterprise E3 all'intero reparto. Il servizio Enterprise Yammer incluso nel prodotto deve essere temporaneamente disattivato finché il reparto è pronto per iniziare a usarlo. Si prevede anche di distribuire le licenze Enterprise Mobility + Security allo stesso gruppo di utenti.

> [!NOTE]
> Alcuni servizi Microsoft non sono disponibili in tutte le posizioni. Per poter assegnare una licenza a un utente, l'amministratore deve prima specificare la proprietà relativa alla località di utilizzo per l'utente.
> 
> Per l'assegnazione di licenze ai gruppi, eventuali utenti per cui non è specificata una località d'uso ereditano la località della directory. Se gli utenti si trovano in località diverse, è consigliabile impostare sempre la località di utilizzo nell'ambito del flusso di creazione utente in Azure AD, ad esempio nella configurazione di AAD Connect. In questo modo si garantisce che il risultato dell'assegnazione delle licenze sia sempre corretto e che gli utenti non ricevano i servizi in località che non sono consentite.

## <a name="step-1-assign-the-required-licenses"></a>Passaggio 1: assegnare le licenze necessarie

1. Accedi per il [ **interfaccia di amministrazione di Azure AD** ](https://aad.portal.azure.com) con un account di amministratore di licenze. Per gestire le licenze, l'account deve essere un amministratore di licenze, l'utente amministratore o amministratore globale.

2. Selezionare **licenze** per aprire un riquadro in cui è possibile visualizzare e gestire tutti i prodotti con contratto multilicenza del tenant.

4. Sotto **tutti i prodotti**, selezionare sia Office 365 Enterprise E5 ed Enterprise Mobility + Security E3 selezionando i nomi di prodotto. Per avviare l'assegnazione, selezionare **Assegna** nella parte superiore del riquadro.

   ![Selezionare i prodotti per assegnare le licenze](./media/licensing-groups-assign/all-products-assign.png)
  
5. Nel **assegna licenza** riquadro, selezionare **utenti e gruppi** per aprire un elenco di utenti e gruppi.

6. Selezionare un utente o gruppo e quindi usare il **seleziona** nella parte inferiore del riquadro per confermare la selezione.

7. Nel **assegna licenza** riquadro, fare clic su **opzioni di assegnazione**, che consente di visualizzare tutti i piani di servizio inclusi nei due prodotti selezionati in precedenza. Trovare **Yammer Enterprise** e impostarlo su **Disattivato** per disabilitare il servizio dalla licenza del prodotto. Confermare facendo **OK** in fondo **opzioni di licenza**.

   ![Selezionare i piani di servizio per le licenze](./media/licensing-groups-assign/assignment-options.png)
  
8. Per completare l'assegnazione, fare clic su **Assegna** nella parte inferiore del riquadro **Assegna licenza**.

9. Verrà visualizzata una notifica nell'angolo in alto a destra per indicare lo stato e il risultato del processo. Se non è stato possibile completare l'assegnazione al gruppo, ad esempio a causa di licenze già esistenti nel gruppo, fare clic sulla notifica per visualizzare i dettagli dell'errore.

Quando assegnano licenze a un gruppo, Azure AD elabora tutti i membri esistenti di tale gruppo. Questo processo potrebbe richiedere un po' di tempo diversi con le dimensioni del gruppo. Nel passaggio successivo viene spiegato come verificare se il processo è stato completato e determinare se sono necessari altri interventi per la risoluzione dei problemi.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Passaggio 2: verificare che l'assegnazione iniziale sia terminata

1. Passare a **Azure Active Directory** > **gruppi**. Selezionare il gruppo che sono state assegnate licenze.

2. Nel riquadro del gruppo, selezionare **licenze**. Questo consente di verificare rapidamente se le licenze sono state completamente assegnate agli utenti e se ci sono errori che richiedono attenzione. Sono disponibili le informazioni seguenti:

   - Elenco delle licenze di prodotto attualmente assegnate al gruppo. Selezionare una voce per visualizzare i servizi specifici che sono stati abilitati e apportare modifiche.

   - Stato delle modifiche più recenti delle licenze apportate al gruppo, se è in corso l'elaborazione delle modifiche o se l'elaborazione è stata completata per tutti i membri utente.

   - Informazioni sugli utenti con stato di errore perché non è stato possibile assegnare le licenze a tali utenti.

   ![errori di gestione delle licenze e lo stato della licenza](./media/licensing-groups-assign/assignment-errors.png)

3. Per informazioni più dettagliate sull'elaborazione delle licenze, vedere **Azure Active Directory** > **Utenti e gruppi** > *nome gruppo* > **Log di controllo**. Si notino le attività seguenti:

   - Attività: **iniziare ad applicare agli utenti le licenze basate sui gruppi**. Viene registrato quando il sistema preleva la modifica dell'assegnazione della licenza per il gruppo e inizia ad applicarla a tutti i membri utente. Contiene informazioni sulla modifica apportata.

   - Attività: **finire di applicare agli utenti le licenze basate sui gruppi**. Viene registrato quando il nostro sistema completa l'elaborazione di tutti gli utenti del gruppo. Contiene un riepilogo di quanti utenti sono stati elaborati correttamente e il numero di utenti a cui non è stato possibile assegnare le licenze di gruppo.

   [Leggere questa sezione](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) per ulteriori informazioni su come usare i log di controllo per analizzare le modifiche apportate dalle licenze basate sui gruppi.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Passaggio 3: controllare i problemi relativi alle licenze e risolverli

1. Passare a **Azure Active Directory** > **gruppi**e trovare il gruppo che sono state assegnate licenze.
2. Nel riquadro del gruppo, selezionare **licenze**. La notifica nella parte superiore del riquadro indica che ci sono 10 utenti per cui non è stato possibile assegnare le licenze. Aprirlo per visualizzare un elenco di tutti gli utenti in uno stato di errore sulle licenze per questo gruppo.
3. La colonna **N. assegnazioni con errori** indica che non è stato possibile assegnare entrambe le licenze prodotto agli utenti. La colonna **Principali cause dell'errore** contiene la causa dell'errore. In questo caso si tratta di **Piani di servizio in conflitto**.

   ![licenze che non è state assegnate](./media/licensing-groups-assign/failed-assignments.png)

4. Selezionare un utente per aprire il riquadro **Licenze**. Questo riquadro mostra tutte le licenze attualmente assegnate all'utente. In questo esempio l'utente ha la licenza di Office 365 Enterprise E1 ereditata dal gruppo **Kiosk users**. Questo genera un conflitto con la licenza E3 che il sistema ha provato ad applicare dal gruppo **HR Department**. Nessuna licenza del gruppo è stata quindi assegnata all'utente.

   ![Visualizzare tutti i conflitti di licenza per un utente](./media/licensing-groups-assign/user-license-view.png)

5. Per risolvere questo conflitto, rimuovere l'utente dal gruppo **Kiosk users**. Dopo che Azure AD elabora la modifica, le licenze del **Reparto risorse umane** sono assegnate correttamente.

   ![Qui sono le licenze assegnate correttamente](./media/licensing-groups-assign/license-correctly-assigned.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità disponibili per la gestione delle licenze con i gruppi, vedere i seguenti articoli:

* [Che cosa sono le licenze basate sui gruppi in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Identificazione e risoluzione dei problemi relativi alle licenze per un gruppo in Azure Active Directory](licensing-groups-resolve-problems.md)
* [Come eseguire la migrazione di singoli utenti con licenza alle licenze basate sui gruppi in Azure Active Directory](licensing-groups-migrate-users.md)
* [Come eseguire la migrazione degli utenti tra licenze di prodotti diverse con la gestione delle licenze basate su gruppo in Azure Active Directory](licensing-groups-change-licenses.md)
* [Scenari aggiuntivi relativi alle licenze basate sui gruppi in Azure Active Directory](../active-directory-licensing-group-advanced.md)
* [Esempi di PowerShell per le licenze basate sui gruppi in Azure Active Directory](licensing-ps-examples.md)
