---
title: "Abilitare l'autenticazione a più fattori per gli utenti VPN: autenticazione Azure AD"
description: Abilitare la funzionalità di autenticazione a più fattori per gli utenti VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 771dea2d9ae2979bc71880368ed3a9a538e8a803
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964719"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Abilitare Azure Multi-Factor Authentication (multi-factor authentication) per gli utenti VPN

Se si desidera che agli utenti venga richiesto un secondo fattore di autenticazione prima di concedere l'accesso, è possibile configurare Azure Multi-Factor Authentication (multi-factor authentication) per ogni utente o sfruttare Multi-Factor Authentication (multi-factor authentication) tramite [l'accesso condizionale](../active-directory/conditional-access/overview.md) per un controllo più granulare. La configurazione Multi-Factor Authentication per utente può essere abilitata senza costi aggiuntivi. Tuttavia, quando si Abilita l'autenticazione a più fattori per utente, all'utente verrà richiesto di eseguire l'autenticazione a due fattori rispetto a tutte le applicazioni associate al tenant di Azure AD. L'accesso condizionale consente un controllo più granulare sulla modalità di promozione di un secondo fattore e può consentire l'assegnazione dell'autenticazione a più fattori solo alla VPN e non ad altre applicazioni legate al tenant Azure AD.

## <a name="enableauth"></a>Abilita autenticazione

1. Passare a **Azure Active Directory-> applicazioni aziendali-> tutte le applicazioni**.
2. Nella pagina **applicazioni aziendali-tutte le applicazioni** selezionare **VPN di Azure**.

   ![ID directory](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>Configurare le impostazioni di accesso

Nella pagina **VPN di Azure-Proprietà** configurare le impostazioni di accesso.

1. Impostare **abilitata per l'accesso degli utenti?** a **Sì**. Ciò consente a tutti gli utenti nel tenant di Active Directory di connettersi alla VPN correttamente.
2. Impostare **assegnazione utente obbligatoria?** su **Sì** se si vuole limitare l'accesso solo agli utenti che dispongono delle autorizzazioni per la VPN di Azure.
3. Salvare le modifiche.

   ![Autorizzazioni](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="option-1---enable-multi-factor-authentication-mfa-via-conditional-access"></a>Opzione 1: abilitare Multi-Factor Authentication (multi-factor authentication) tramite l'accesso condizionale

L'accesso condizionale consente il controllo di accesso con granularità fine in base alle singole applicazioni.  Si noti che per sfruttare l'accesso condizionale, è necessario che siano state applicate Azure AD Premium 1 o più licenze agli utenti che saranno soggette alle regole di accesso condizionale.

1. Nella pagina **applicazioni aziendali-tutte le applicazioni** selezionare **VPN di Azure**, selezionare **accesso condizionale**e quindi fare clic su **nuovo criterio**.
2. In utenti e gruppi, nella scheda *Includi* **selezionare utenti e gruppi**, controllare **utenti e gruppi**e selezionare un gruppo o un set di utenti che devono essere soggetti a multi-factor authentication.  Fare clic su **Done**.
![Assegnazioni](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. In **Concedi**selezionare **Concedi accesso**, selezionare **Richiedi autenticazione**a più fattori, selezionare **Richiedi tutti i controlli selezionati**e fare clic sul pulsante **Seleziona** .
![Concedi accesso-autenticazione a più fattori](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. Controllare in **Abilita criterio** e fare **clic sul pulsante** **Crea** .
![abilitare i criteri](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)

## <a name="option-2---enable-multi-factor-authentication-mfa-per-user"></a>Opzione 2: abilitare Multi-Factor Authentication (multi-factor authentication) per utente

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="next-steps"></a>Passaggi successivi

Per connettersi alla rete virtuale, è necessario creare e configurare un profilo client VPN. Vedere [configurare un client VPN per le connessioni VPN P2S](openvpn-azure-ad-client.md).
