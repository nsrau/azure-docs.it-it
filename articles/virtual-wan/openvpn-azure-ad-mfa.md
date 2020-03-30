---
title: "Abilitare l'autenticazione a più fattori per gli utenti VPN: autenticazione di Azure ADEnable MFA for VPN users: Azure AD authentication"
description: Abilitare l'autenticazione a più fattori per gli utenti VPNEnable multi-factor authentication for VPN users
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: alzam
ms.openlocfilehash: 13f012af95bb2b6098317e59e5293fb72804a6a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471537"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Abilitare Azure Multi-Factor Authentication (MFA) per gli utenti VPNEnable Azure Multi-Factor Authentication (MFA) for VPN users

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>Abilita autenticazione

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>Configurare le impostazioni di accesso

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>Opzione 1 - Accesso per utenteOption 1 - Per User access

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>Opzione 2 - Accesso condizionaleOption 2 - Conditional Access

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Passaggi successivi

Per connettersi alla rete virtuale, è necessario creare e configurare un profilo client VPN. Vedere [Configurare l'autenticazione di Azure AD per la connessione da punto a sito ad Azure.See Configure Azure AD authentication for Point-to-Site connection to Azure](virtual-wan-point-to-site-azure-ad.md).
