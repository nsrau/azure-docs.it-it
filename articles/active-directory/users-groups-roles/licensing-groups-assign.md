---
title: Assegnare licenze a un gruppo - Azure Active Directory | Microsoft Docs
description: Come assegnare licenze agli utenti usando le licenze dei gruppi di Azure Active Directory
services: active-directory
keywords: Licenze di Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 497efda857dcd7de3079d702be00a094d221b779
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034857"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Assegnare licenze agli utenti in base all'appartenenza ai gruppi in Azure Active Directory

In questo articolo viene illustrata l'assegnazione delle licenze dei prodotti a un gruppo di utenti e la verifica della corretta concessione della licenza in Azure Active Directory (Azure AD).

In questo esempio il tenant contiene un gruppo di sicurezza denominato **HR Department**. Il gruppo include tutti i membri del reparto Risorse umane, circa 1.000 utenti. Si vogliono assegnare licenze di Office 365 Enterprise E3 all'intero reparto. Il servizio Enterprise Yammer incluso nel prodotto deve essere temporaneamente disattivato finché il reparto è pronto per iniziare a usarlo. Si prevede anche di distribuire le licenze Enterprise Mobility + Security allo stesso gruppo di utenti.

> [!NOTE]
> Alcuni servizi Microsoft non sono disponibili in tutte le posizioni. Per poter assegnare una licenza a un utente, l'amministratore deve prima specificare la proprietà relativa alla località di utilizzo per l'utente.
>
> Per l'assegnazione di licenze ai gruppi, eventuali utenti per cui non è specificata una località d'uso ereditano la località della directory. Se gli utenti si trovano in località diverse, è consigliabile impostare sempre la località di utilizzo nell'ambito del flusso di creazione utente in Azure AD, ad esempio nella configurazione di AAD Connect. In questo modo si garantisce che il risultato dell'assegnazione delle licenze sia sempre corretto e che gli utenti non ricevano i servizi in località che non sono consentite.

## <a name="step-1-assign-the-required-licenses"></a>Passaggio 1: assegnare le licenze necessarie

1. Accedere all'interfaccia di [**amministrazione di Azure ad**](https://aad.portal.azure.com) con un account di amministratore delle licenze. Per gestire le licenze, l'account deve essere un amministratore di licenze, un amministratore utente o un amministratore globale.

1. Selezionare **licenze** per aprire una pagina in cui è possibile visualizzare e gestire tutti i prodotti con licenza nel tenant.

1. In **tutti i prodotti**selezionare Office 365 Enterprise E5 e Enterprise Mobility + Security E3 selezionando i nomi dei prodotti. Per avviare l'assegnazione, selezionare **assegna** nella parte superiore della pagina.

   ![Selezionare i prodotti per assegnare le licenze](./media/licensing-groups-assign/all-products-assign.png)
  
1. Nella pagina **assegnazione licenza** selezionare **utenti e gruppi** per aprire un elenco di utenti e gruppi.

1. Selezionare un utente o un gruppo, quindi usare il pulsante **Seleziona** nella parte inferiore della pagina per confermare la selezione.

1. Nella pagina **assegnazione licenza** fare clic su **Opzioni di assegnazione**per visualizzare tutti i piani di servizio inclusi nei due prodotti selezionati in precedenza. Trovare **Yammer Enterprise** e impostarlo su **Disattivato** per disabilitare il servizio dalla licenza del prodotto. Confermare facendo clic su **OK** nella parte inferiore delle **Opzioni di licenza**.

   ![Selezionare i piani di servizio per le licenze](./media/licensing-groups-assign/assignment-options.png)
  
1. Per completare l'assegnazione, nella pagina **assegnazione licenza** fare clic su **assegna** nella parte inferiore della pagina.

1. Verrà visualizzata una notifica nell'angolo in alto a destra per indicare lo stato e il risultato del processo. Se non è stato possibile completare l'assegnazione al gruppo, ad esempio a causa di licenze già esistenti nel gruppo, fare clic sulla notifica per visualizzare i dettagli dell'errore.

Quando si assegnano licenze a un gruppo, Azure AD elabora tutti i membri esistenti del gruppo. Questo processo potrebbe richiedere del tempo, variando con le dimensioni del gruppo. Nel passaggio successivo viene spiegato come verificare se il processo è stato completato e determinare se sono necessari altri interventi per la risoluzione dei problemi.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Passaggio 2: verificare che l'assegnazione iniziale sia terminata

1. Passare a **Azure Active Directory** > **gruppi**. Consente di selezionare il gruppo a cui sono state assegnate le licenze.

1. Nella pagina gruppo selezionare **licenze**. Questo consente di verificare rapidamente se le licenze sono state completamente assegnate agli utenti e se ci sono errori che richiedono attenzione. Sono disponibili le informazioni seguenti:

   - Licenze di servizio attualmente assegnate al gruppo. Selezionare una voce per visualizzare i servizi specifici che sono stati abilitati e apportare modifiche.

   - Aggiornamenti dello stato delle ultime modifiche della licenza, che sono disponibili se le modifiche vengono elaborate o se l'elaborazione è stata completata per tutti i membri utente.

   - Informazioni sulle assegnazioni di licenze utente in stato di errore.

   ![errori di licenza e stato delle licenze](./media/licensing-groups-assign/assignment-errors.png)

1. Per informazioni più dettagliate sull'elaborazione delle licenze, vedere **Azure Active Directory** > **Utenti e gruppi** > *nome gruppo* > **Log di controllo**. Controllare le attività seguenti:

   - Attività: `Start applying group based license to users`. Viene registrato quando il sistema preleva la modifica dell'assegnazione della licenza per il gruppo e avvia l'applicazione a tutti i membri utente. Contiene informazioni sulla modifica apportata.

   - Attività: `Finish applying group based license to users`. Viene registrato quando il nostro sistema completa l'elaborazione di tutti gli utenti del gruppo. Contiene un riepilogo di quanti utenti sono stati elaborati correttamente e il numero di utenti a cui non è stato possibile assegnare le licenze di gruppo.

   [Leggere questa sezione](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) per ulteriori informazioni su come usare i log di controllo per analizzare le modifiche apportate dalle licenze basate sui gruppi.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Passaggio 3: controllare i problemi relativi alle licenze e risolverli

1. Passare a **Azure Active Directory** > **gruppi**e individuare il gruppo a cui sono state assegnate le licenze.
1. Nella pagina gruppo selezionare **licenze**. La notifica nella parte superiore della pagina mostra che sono presenti 10 utenti a cui non è stato possibile assegnare le licenze. Aprirlo per visualizzare un elenco di tutti gli utenti in uno stato di errore di licenza per questo gruppo.
1. La colonna **N. assegnazioni con errori** indica che non è stato possibile assegnare entrambe le licenze prodotto agli utenti. La colonna **Principali cause dell'errore** contiene la causa dell'errore. In questo caso si tratta di **Piani di servizio in conflitto**.

   ![licenze che non è stato possibile assegnare](./media/licensing-groups-assign/failed-assignments.png)

1. Selezionare un utente per aprire la pagina **licenze** dell'utente. In questa pagina vengono visualizzate tutte le licenze attualmente assegnate all'utente. In questo esempio l'utente ha la licenza di Office 365 Enterprise E1 ereditata dal gruppo **Kiosk users**. Questo genera un conflitto con la licenza E3 che il sistema ha provato ad applicare dal gruppo **HR Department**. Nessuna licenza del gruppo è stata quindi assegnata all'utente.

   ![Visualizza tutti i conflitti di licenza per un utente](./media/licensing-groups-assign/user-license-view.png)

1. Per risolvere questo conflitto, rimuovere l'utente dal gruppo **Kiosk users**. Dopo che Azure AD elabora la modifica, le licenze del **Reparto risorse umane** sono assegnate correttamente.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul set di funzionalità per l'assegnazione delle licenze tramite i gruppi, vedere gli articoli seguenti:

- [Che cosa sono le licenze basate sui gruppi in Azure Active Directory?](/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)
- [Identificazione e risoluzione dei problemi relativi alle licenze per un gruppo in Azure Active Directory](licensing-groups-resolve-problems.md)
- [Come eseguire la migrazione di singoli utenti con licenza alle licenze basate sui gruppi in Azure Active Directory](licensing-groups-migrate-users.md)
- [Come eseguire la migrazione degli utenti tra licenze di prodotti diverse con la gestione delle licenze basate su gruppo in Azure Active Directory](licensing-groups-change-licenses.md)
- [Scenari aggiuntivi relativi alle licenze basate sui gruppi in Azure Active Directory](../active-directory-licensing-group-advanced.md)
- [Esempi di PowerShell per le licenze basate sui gruppi in Azure Active Directory](licensing-ps-examples.md)
