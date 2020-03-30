---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471535"
---
L'accesso condizionale consente un controllo dell'accesso granulare in base all'applicazione. Per usare l'accesso condizionale, è necessario applicare alle utenti le licenze di Azure AD Premium 1 o superiore che saranno soggette alle regole di accesso condizionale.

1. Passare alla pagina **Applicazioni aziendali - Tutte le applicazioni** e fare clic su VPN di **Azure.**

   - Fare clic su **Accesso condizionale**.
   - Fare clic su **Nuovi criteri** per aprire il riquadro **Nuovo**.
2. Nel riquadro **Nuovo** passare a **Assegnazioni -> Utenti e gruppi**. Nella scheda **>Includi** **di Utenti e gruppi:**

   - Fare clic su **Seleziona utenti e gruppi**.
   - Selezionare **Utenti e gruppi**.
   - Fare clic su **Seleziona** per selezionare un gruppo o un gruppo di utenti interessati dall'autenticazione a più fattori.
   - Fare clic su **Fine**.

   ![Assegnazioni](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. Nel riquadro **Nuovo** passare al riquadro **Concessione > controlli** di accesso:

   - Fare clic su **Concedi accesso**.
   - Fare clic su **Richiedi autenticazione a più fattori**.
   - Fare clic su **Richiedi tutti i controlli selezionati**.
   - Fare clic su **Seleziona**.
   
   ![Concedere l'accesso - Autenticazione a più fattoriGrant access - MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. Nella sezione **Abilita criteri:**

   - Selezionare **On (Attivato).**
   - Fare clic su **Crea**.

   ![Abilita criterio](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)