---
title: 'Gateway VPN: Azure AD tenant per gruppi di utenti diversi: autenticazione Azure AD'
description: È possibile usare la VPN P2S per connettersi alla VNet usando l'autenticazione Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/25/2020
ms.author: alzam
ms.openlocfilehash: 0ef0c7d3a269753067e53a69b9da680db969e25d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85414428"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Creare un tenant Azure Active Directory per le connessioni del protocollo OpenVPN da punto a sito

Quando ci si connette alla VNet, è possibile usare l'autenticazione basata su certificati o l'autenticazione RADIUS. Tuttavia, quando si usa il protocollo VPN aperto, è anche possibile usare l'autenticazione Azure Active Directory. Se si vuole che un diverso set di utenti sia in grado di connettersi a diversi gateway VPN, è possibile registrare più app in Active Directory e collegarle a diversi gateway VPN. Questo articolo illustra come configurare un tenant di Azure AD per l'autenticazione di P2S OpenVPN e come creare e registrare più app in Azure AD per consentire l'accesso diverso per utenti e gruppi diversi.

> [!NOTE]
> L'autenticazione di Azure AD è supportata solo per le connessioni tramite il protocollo OpenVPN®.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. abilitare l'autenticazione sul gateway

In questo passaggio verrà abilitata l'autenticazione Azure AD nel gateway VPN.

1. Abilitare l'autenticazione Azure AD sul gateway VPN passando alla configurazione da **punto a sito** e selezionando **OpenVPN (SSL)** come **tipo di tunnel**. Selezionare **Azure Active Directory** come **tipo di autenticazione** , quindi immettere le informazioni nella sezione **Azure Active Directory** .

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/azure-ad-auth-portal.png)

    > [!NOTE]
    > Non usare l'ID applicazione del client VPN di Azure: concederà a tutti gli utenti l'accesso al gateway VPN. Usare l'ID dell'applicazione o delle applicazioni registrate.

2. Per creare e scaricare il profilo, fare clic sul collegamento **Scarica client VPN** .

3. Estrarre il file zip scaricato.

4. Passare alla cartella "AzureVPN" decompressa.

5. Prendere nota della posizione del file "azurevpnconfig.xml". Il azurevpnconfig.xml contiene l'impostazione per la connessione VPN e può essere importato direttamente nell'applicazione client VPN di Azure. È anche possibile distribuire questo file a tutti gli utenti che devono connettersi tramite posta elettronica o altri mezzi. Per la corretta connessione, l'utente dovrà disporre di credenziali Azure AD valide.

## <a name="next-steps"></a>Passaggi successivi

Per connettersi alla rete virtuale, è necessario creare e configurare un profilo client VPN. Vedere [configurare un client VPN per le connessioni VPN P2S](openvpn-azure-ad-client.md).
