---
title: "Abilitare l'autenticazione a più fattori per gli utenti VPN: autenticazione Azure AD"
description: Abilitare la funzionalità di autenticazione a più fattori per gli utenti VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 7f05b850a0d886ac0df5c542de647f91fe62eb05
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382233"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Abilitare Azure Multi-Factor Authentication (multi-factor authentication) per gli utenti VPN

Se si desidera che agli utenti venga richiesto un secondo fattore di autenticazione prima di concedere l'accesso, è possibile configurare il Multi-Factor Authentication di Azure per il tenant di Azure AD. I passaggi descritti in questo articolo consentono di abilitare un requisito per la verifica in due passaggi.

## <a name="prereq"></a>Prerequisiti

Il prerequisito per questa configurazione è un tenant di Azure AD configurato usando la procedura descritta in [configurare un tenant](openvpn-azure-ad-tenant.md).

## <a name="mfa"></a>Aprire la pagina di autenticazione a più fattori

1. Accedere al portale di Azure.
2. Passare a **Azure Active Directory-> tutti gli utenti**.
3. Selezionare **multi-factor authentication** per aprire la pagina autenticazione a più fattori.

   ![Accedi](./media/openvpn-azure-ad-mfa/mfa1.jpg)

## <a name="users"></a>Seleziona utenti

1. Nella pagina **multi-factor authentication** selezionare gli utenti per i quali si desidera abilitare l'autenticazione a più fattori.
2. Selezionare **Abilita**.

   ![Selezionare](./media/openvpn-azure-ad-mfa/mfa2.jpg)

## <a name="enableauth"></a>Abilita autenticazione

1. Passare a **Azure Active Directory-> applicazioni aziendali-> tutte le applicazioni**.
2. Nella pagina **applicazioni aziendali-tutte le applicazioni** selezionare **VPN di Azure**.

   ![ID directory](./media/openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>Configurare le impostazioni di accesso

Nella pagina **VPN di Azure-Proprietà** configurare le impostazioni di accesso.

1. Impostare **abilitata per l'accesso degli utenti?** a **Sì**. Ciò consente a tutti gli utenti nel tenant di Active Directory di connettersi alla VPN correttamente.
2. Impostare **assegnazione utente obbligatoria?** su **Sì** se si vuole limitare l'accesso solo agli utenti che dispongono delle autorizzazioni per la VPN di Azure.
3. Salvare le modifiche.

   ![autorizzazioni](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Passaggi successivi

Per connettersi alla rete virtuale, è necessario creare e configurare un profilo client VPN. Vedere [configurare un client VPN per le connessioni VPN P2S](openvpn-azure-ad-client.md).
