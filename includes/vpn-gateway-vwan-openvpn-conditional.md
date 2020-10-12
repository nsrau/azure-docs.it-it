---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77471535"
---
L'accesso condizionale consente il controllo di accesso con granularità fine in base alle singole applicazioni. Per usare l'accesso condizionale, è necessario che siano state applicate Azure AD Premium 1 o più licenze agli utenti che saranno soggette alle regole di accesso condizionale.

1. Passare alla pagina **applicazioni aziendali-tutte le applicazioni** e fare clic su **VPN di Azure**.

   - Fare clic su **accesso condizionale**.
   - Fare clic su **Nuovi criteri** per aprire il riquadro **Nuovo**.
2. Nel riquadro **nuovo** passare a **assegnazioni-> utenti e gruppi**. Nella scheda **utenti e gruppi->** **Includi** :

   - Fare clic su **Seleziona utenti e gruppi**.
   - Controllare **utenti e gruppi**.
   - Fare clic su **Seleziona** per selezionare un gruppo o un set di utenti che saranno interessati dall'autenticazione a più fattori.
   - Fare clic su **Fine**.

   ![Assegnazioni](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. Nel riquadro **nuovo** passare al riquadro **Access controls-> Grant** :

   - Fare clic su **Concedi accesso**.
   - Fare clic su **Richiedi autenticazione a più fattori**.
   - Fare clic su **Richiedi tutti i controlli selezionati**.
   - Fare clic su **Seleziona**.
   
   ![Concedi accesso-autenticazione a più fattori](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. Nella sezione **abilitare i criteri** :

   - Selezionare **Attivato**.
   - Fare clic su **Crea**.

   ![Abilita criteri](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)