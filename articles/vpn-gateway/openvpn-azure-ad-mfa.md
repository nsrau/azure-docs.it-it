---
title: "Abilitare l'autenticazione a più fattori per gli utenti VPN: Azure AD autenticazione | Microsoft Docs"
description: Abilitare la funzionalità di autenticazione a più fattori per gli utenti VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: alzam
ms.openlocfilehash: 1b506f8439deabd110daaefab36442140e20d0d4
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185366"
---
# <a name="enable-multi-factor-authentication-mfa-for-vpn-users"></a>Abilitare Multi-Factor Authentication (multi-factor authentication) per gli utenti VPN

È possibile abilitare l'autenticazione a più fattori per gli utenti nel tenant di Azure AD in modo che agli utenti venga richiesta un'autenticazione a due fattori prima che venga concesso l'accesso.

> [!NOTE]
> Prerequisito: è stato configurato un tenant di Azure AD come descritto nel documento "configurare un tenant".
>

### <a name="tenant"></a>1. accedere al portale di Azure e passare a **Azure Active Directory** , **tutti gli utenti** e fare clic su **multi-factor authentication**


   ![Nuovo tenant Azure AD](./media/openvpn-azure-ad-mfa/mfa1.jpg)

### <a name="users"></a>2. Selezionare gli utenti per i quali si desidera abilitare l'autenticazione a più fattori e fare clic su **Abilita** .

   ![Nuovo tenant Azure AD](./media/openvpn-azure-ad-mfa/mfa2.jpg)

### <a name="enable-authentication"></a>3. passare a **Azure Active Directory** , **applicazioni aziendali**, **tutte le applicazioni** e fare clic su **VPN di Azure**


   ![ID directory](./media/openvpn-azure-ad-mfa/user1.jpg)

### <a name="users"></a>4. Assicurarsi che gli **utenti abilitati per l'accesso?** sia impostato su Sì. Se si desidera che solo gli utenti che dispongono delle autorizzazioni per la VPN di Azure siano in grado di effettuare l'accesso, impostare l' **assegnazione utente obbligatoria?** per Sì. in caso contrario, tutti gli utenti nel tenant di Active Directory potranno connettersi alla VPN correttamente.

   ![autorizzazioni](./media/openvpn-azure-ad-mfa/user2.jpg)


## <a name="next-steps"></a>Passaggi successivi

Per connettersi alla rete virtuale, è necessario creare e configurare un profilo client VPN. Vedere [configurare un client VPN per le connessioni VPN P2S](openvpn-azure-ad-client.md).
