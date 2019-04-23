---
title: Eseguire la migrazione di criteri classici che richiedono l'autenticazione a più fattori nel portale di Azure
description: Questo articolo illustra come eseguire la migrazione di criteri classici che richiedono l'autenticazione a più fattori nel portale di Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: tutorial
ms.date: 06/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6661cee8ba6176bd706d31a10a8f20549e29e4d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795050"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Eseguire la migrazione di criteri classici che richiedono l'autenticazione a più fattori nel portale di Azure

Questa esercitazione illustra come eseguire la migrazione di criteri classici che richiedono l'**autenticazione a più fattori** per un'app cloud. Anche se non è un prerequisito, è consigliabile leggere [Migrare i criteri classici nel portale di Azure](policy-migration.md) prima di iniziare la migrazione dei criteri classici.

## <a name="overview"></a>Panoramica

Lo scenario in questo articolo illustra come eseguire la migrazione di criteri classici che richiedono  **l'autenticazione a più fattori** per un'applicazione cloud.

![Azure Active Directory](./media/policy-migration/33.png)

Il processo di migrazione è costituito dai passaggi seguenti:

1. [Aprire il criterio classico](#open-a-classic-policy) per ottenere le impostazioni di configurazione.
1. Creare un nuovo criterio di accesso condizionale di Azure Active Directory per sostituire il criterio classico. 
1. Disabilitare il criterio classico.

## <a name="open-a-classic-policy"></a>Aprire un criterio classico

1. Nel [portale di Azure](https://portal.azure.com), sulla barra di spostamento a sinistra, fare clic su **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration-mfa/01.png)

1. Nella sezione **Gestisci** della pagina **Azure Active Directory** fare clic su **Accesso condizionale**.

   ![Accesso condizionale](./media/policy-migration-mfa/02.png)

1. Nella sezione **Gestisci**, fare clic su **Criteri classici (anteprima)**.

   ![Criteri classici](./media/policy-migration-mfa/12.png)

1. Nell'elenco di criteri classici, fare clic sui criteri classici che richiedono l'**autenticazione a più fattori** per un'applicazione cloud.

   ![Criteri classici](./media/policy-migration-mfa/13.png)

## <a name="create-a-new-conditional-access-policy"></a>Creare un nuovo criterio di accesso condizionale

1. Nel [portale di Azure](https://portal.azure.com), sulla barra di spostamento a sinistra, fare clic su **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration/01.png)

1. Nella sezione **Gestisci** della pagina **Azure Active Directory** fare clic su **Accesso condizionale**.

   ![Accesso condizionale](./media/policy-migration/02.png)

1. Nella pagina **Accesso condizionale** aprire la **Nuovo** facendo clic su **Aggiungi** nella barra degli strumenti in alto.

   ![Accesso condizionale](./media/policy-migration/03.png)

1. Nella pagina **Nuovo** digitare un nome per il criterio nella casella di testo **Nome**.

   ![Accesso condizionale](./media/policy-migration/29.png)

1. Nella sezione **Assegnazioni** fare clic su **Utenti e gruppi**.

   ![Accesso condizionale](./media/policy-migration/05.png)

   1. Se nel criterio classico sono selezionati tutti gli utenti, fare clic su **Tutti gli utenti**. 

   ![Accesso condizionale](./media/policy-migration/35.png)

   1. Se nel criterio classico sono selezionati i gruppi, fare clic su **Utenti e gruppi**, quindi selezionare i gruppi e gli utenti necessari.

   ![Accesso condizionale](./media/policy-migration/36.png)

   1. Se sono presenti gruppi esclusi, fare clic sulla scheda**Escludi** e quindi selezionare i gruppi e gli utenti necessari. 

   ![Accesso condizionale](./media/policy-migration/37.png)

1. Nella pagina **Nuovo**, per aprire la pagina **App cloud**, nella sezione **Assegnazioni** fare clic su **App cloud**.

1. Nella pagina **App cloud** attenersi alla procedura seguente:

   ![Accesso condizionale](./media/policy-migration/08.png)

   1. Fare clic su **Selezionare le app**.

   1. Fare clic su **Seleziona**.

   1. Nella pagina **Seleziona** scegliere l'app cloud e quindi fare clic su **Seleziona**.

   1. Nella pagina **App cloud** fare clic su **Fatto**.

1. Se è stato selezionato **Richiedi autenticazione a più fattori**:

   ![Accesso condizionale](./media/policy-migration/26.png)

   1. Nella sezione **Controlli di accesso** fare clic su **Concedi**.

   ![Accesso condizionale](./media/policy-migration/27.png)

   1. Nella pagina **Concedi**, fare clic su **Concedi accesso**e quindi su **Richiedi autenticazione a più fattori**.

   1. Fare clic su **Seleziona**.

1. Fare clic su **On** per abilitare il criterio.

   ![Accesso condizionale](./media/policy-migration/30.png)

## <a name="disable-the-classic-policy"></a>Disabilitare i criteri classico

Per disabilitare i criteri classici, fare clic su **Disabilita** nella visualizzazione **Dettagli**.

![Criteri classici](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla migrazione dei criteri classici, vedere [Migrare i criteri classici nel portale di Azure](policy-migration.md).
- Per informazioni su come configurare criteri di accesso condizionale, vedere [Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory](app-based-mfa.md).
- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md).
