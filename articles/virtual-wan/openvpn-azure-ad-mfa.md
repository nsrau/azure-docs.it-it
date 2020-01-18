---
title: "Abilitare l'autenticazione a più fattori per gli utenti VPN: autenticazione Azure AD"
description: Abilitare la funzionalità di autenticazione a più fattori per gli utenti VPN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: alzam
ms.openlocfilehash: fcb60e80189da89b3f634c14582be606307536e6
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166662"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Abilitare Azure Multi-Factor Authentication (multi-factor authentication) per gli utenti VPN

Se si desidera che agli utenti venga richiesto un secondo fattore di autenticazione prima di concedere l'accesso, è possibile configurare il Multi-Factor Authentication di Azure per il tenant di Azure AD. I passaggi descritti in questo articolo consentono di abilitare un requisito per la verifica in due passaggi.

## <a name="prereq"></a>Prerequisiti

Il prerequisito per questa configurazione è un tenant di Azure AD configurato usando la procedura descritta in [configurare un tenant](openvpn-azure-ad-tenant.md).

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="enablesign"></a>Configurare le impostazioni di accesso

Nella pagina **VPN di Azure-Proprietà** configurare le impostazioni di accesso.

1. Impostare **abilitata per l'accesso degli utenti?** a **Sì**. Ciò consente a tutti gli utenti nel tenant di Active Directory di connettersi alla VPN correttamente.
2. Impostare **assegnazione utente obbligatoria?** su **Sì** se si vuole limitare l'accesso solo agli utenti che dispongono delle autorizzazioni per la VPN di Azure.
3. Salvare le modifiche.

   ![Autorizzazioni](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Passaggi successivi

Per connettersi alla rete virtuale, è necessario creare e configurare un profilo client VPN. Vedere [configurare l'autenticazione Azure ad per la connessione da punto a sito ad Azure](virtual-wan-point-to-site-azure-ad.md).
