---
title: 'Gateway VPN: tenant di Azure AD per gruppi di utenti diversi: autenticazione di Azure AD'
description: È possibile usare la VPN P2S per connettersi alla rete virtuale usando l'autenticazione di Azure ADYou can use P2S VPN to connect to your VNet using Azure AD authentication
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485727"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Creare un tenant di Azure Active Directory per le connessioni di protocollo OpenVPN P2SCreate an Azure Active Directory tenant for P2S OpenVPN protocol connections

Quando ci si connette alla rete virtuale, è possibile utilizzare l'autenticazione basata su certificati o l'autenticazione RADIUS. Tuttavia, quando si usa il protocollo Open VPN, è anche possibile usare l'autenticazione di Azure Active Directory.However, when you use the Open VPN protocol, you can also use Azure Active Directory authentication. Se si desidera che un set diverso di utenti sia in grado di connettersi a gateway VPN diversi, è possibile registrare più app in Active Directory e collegarle a gateway VPN diversi. Questo articolo illustra come configurare un tenant di Azure AD per l'autenticazione OpenVPN P2S e creare e registrare più app in Azure AD per consentire l'accesso diverso a utenti e gruppi diversi.

> [!NOTE]
> L'autenticazione di Azure AD è supportata solo per le connessioni tramite il protocollo OpenVPN®.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Abilitare l'autenticazione sul gateway

In questo passaggio si abilita l'autenticazione di Azure AD nel gateway VPN.

1. Abilitare l'autenticazione di Azure AD nel gateway VPN eseguendo i comandi seguenti. Assicurarsi di modificare i comandi per riflettere il proprio ambiente:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > Non usare l'ID applicazione del client VPN di Azure nei comandi precedenti: concederà a tutti gli utenti l'accesso al gateway VPN. Utilizzare l'ID dell'applicazione o delle applicazioni registrate.

2. Creare e scaricare il profilo eseguendo i comandi seguenti. Modificare i valori -ResourcGroupName e -Name in modo che corrispondano ai propri.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. Dopo aver eseguito i comandi, viene visualizzato un risultato simile a quello riportato di seguito. Copiare l'URL del risultato nel browser per scaricare il file zip del profilo.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Estrarre il file zip scaricato.

5. Passare alla cartella "AzureVPN" decompressa.

6. Prendere nota del percorso del file "azurevpnconfig.xml". Il file azurevpnconfig.xml contiene l'impostazione per la connessione VPN e può essere importato direttamente nell'applicazione client VPN di Azure.The azurevpnconfig.xml contains the setting for the VPN connection and can be imported directly into the Azure VPN Client application. È inoltre possibile distribuire questo file a tutti gli utenti che devono connettersi tramite posta elettronica o altri mezzi. L'utente avrà bisogno di credenziali di Azure AD valide per connettersi correttamente.

## <a name="next-steps"></a>Passaggi successivi

Per connettersi alla rete virtuale, è necessario creare e configurare un profilo client VPN. Vedere Configurare un client VPN per le [connessioni VPN P2S.](openvpn-azure-ad-client.md)
