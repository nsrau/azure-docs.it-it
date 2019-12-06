---
title: Eseguire la migrazione dei criteri di accesso condizionale-Azure Active Directory
description: Questo articolo illustra come eseguire la migrazione di criteri classici che richiedono l'autenticazione a più fattori nel portale di Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: d637ac464b689a25ce5d5a79cf47da0c85d38d0f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846029"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Eseguire la migrazione di un criterio classico nel portale di Azure

Questo articolo illustra come eseguire la migrazione di criteri classici che richiedono  **l'autenticazione a più fattori** per un'applicazione cloud. Anche se non è un prerequisito, è consigliabile leggere [Migrare i criteri classici nel portale di Azure](policy-migration.md) prima di iniziare la migrazione dei criteri classici.

![Dettagli dei criteri classici che richiedono l'autenticazione a più fattori per l'app Salesforce](./media/policy-migration/33.png)

Il processo di migrazione è costituito dai passaggi seguenti:

1. [Aprire il criterio classico](#open-a-classic-policy) per ottenere le impostazioni di configurazione.
1. Creare un nuovo criterio di accesso condizionale di Azure Active Directory per sostituire il criterio classico. 
1. Disabilitare il criterio classico.

## <a name="open-a-classic-policy"></a>Aprire un criterio classico

1. Nella [portale di Azure](https://portal.azure.com)passare a **Azure Active Directory** > **sicurezza** > **accesso condizionale**.
1. Select, **criteri classici**.

   ![Visualizzazione criteri classici](./media/policy-migration-mfa/12.png)

1. Nell'elenco dei criteri classici selezionare i criteri di cui si vuole eseguire la migrazione. Documentare le impostazioni di configurazione in modo che sia possibile ricreare con nuovi criteri di accesso condizionale.

## <a name="create-a-new-conditional-access-policy"></a>Creare un nuovo criterio di accesso condizionale

1. Nella [portale di Azure](https://portal.azure.com)passare a **Azure Active Directory** > **sicurezza** > **accesso condizionale**.
1. Per creare nuovi criteri di accesso condizionale, selezionare **nuovo criterio**.
1. Nella pagina **Nuovo** digitare un nome per il criterio nella casella di testo **Nome**.
1. Nella sezione **Assegnazioni** fare clic su **Utenti e gruppi**.
   1. Se nel criterio classico sono selezionati tutti gli utenti, fare clic su **Tutti gli utenti**. 
   1. Se nel criterio classico sono selezionati i gruppi, fare clic su **Utenti e gruppi**, quindi selezionare i gruppi e gli utenti necessari.
   1. Se sono presenti gruppi esclusi, fare clic sulla scheda**Escludi** e quindi selezionare i gruppi e gli utenti necessari. 
   1. Selezionare **Operazione completata**
1. Nella sezione **assegnazione** fare clic su **app Cloud o azioni**.
1. Nella pagina **app o azioni cloud** seguire questa procedura:
   1. Fare clic su **Selezionare le app**.
   1. Fare clic su **Seleziona**.
   1. Nella pagina **Seleziona** scegliere l'app cloud e quindi fare clic su **Seleziona**.
   1. Nella pagina **App cloud** fare clic su **Fatto**.
1. Se è stato selezionato **Richiedi autenticazione a più fattori**:
   1. Nella sezione **Controlli di accesso** fare clic su **Concedi**.
   1. Nella pagina **Concedi**, fare clic su **Concedi accesso**e quindi su **Richiedi autenticazione a più fattori**.
   1. Fare clic su **Seleziona**.
1. Fare clic **su** attiva per abilitare i criteri e quindi selezionare **Salva**.

   ![Creazione dei criteri di accesso condizionale](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>Disabilitare i criteri classico

Per disabilitare i criteri classici, fare clic su **Disabilita** nella visualizzazione **Dettagli**.

![Disabilitare i criteri classici](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla migrazione dei criteri classici, vedere [Migrare i criteri classici nel portale di Azure](policy-migration.md).
- [Utilizzare la modalità solo report per l'accesso condizionale per determinare l'effetto delle nuove decisioni sui criteri.](concept-conditional-access-report-only.md)