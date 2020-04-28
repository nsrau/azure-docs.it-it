---
title: 'Gateway VPN: Azure AD tenant per gruppi di utenti diversi: autenticazione Azure AD'
description: È possibile usare la VPN P2S per connettersi alla VNet usando l'autenticazione Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77485727"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Creare un tenant di Azure Active Directory per le connessioni del protocollo OpenVPN P2S

Quando ci si connette alla VNet, è possibile usare l'autenticazione basata su certificati o l'autenticazione RADIUS. Tuttavia, quando si usa il protocollo VPN aperto, è anche possibile usare l'autenticazione Azure Active Directory. Se si vuole che un diverso set di utenti sia in grado di connettersi a diversi gateway VPN, è possibile registrare più app in Active Directory e collegarle a diversi gateway VPN. Questo articolo illustra come configurare un tenant di Azure AD per l'autenticazione di P2S OpenVPN e come creare e registrare più app in Azure AD per consentire l'accesso diverso per utenti e gruppi diversi.

> [!NOTE]
> L'autenticazione di Azure AD è supportata solo per le connessioni tramite il protocollo OpenVPN®.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. abilitare l'autenticazione sul gateway

In questo passaggio viene abilitata l'autenticazione Azure AD nel gateway VPN.

1. Abilitare l'autenticazione Azure AD nel gateway VPN eseguendo i comandi seguenti. Assicurarsi di modificare i comandi in modo da riflettere il proprio ambiente:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > Non usare l'ID dell'applicazione del client VPN di Azure nei comandi precedenti: concederà a tutti gli utenti l'accesso al gateway VPN. Usare l'ID dell'applicazione o delle applicazioni registrate.

2. Creare e scaricare il profilo eseguendo i comandi seguenti. Modificare i valori-ResourcGroupName e-Name in modo che corrispondano a quelli personalizzati.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. Dopo aver eseguito i comandi, viene visualizzato un risultato simile a quello riportato di seguito. Copiare l'URL dei risultati nel browser per scaricare il file zip del profilo.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Estrarre il file zip scaricato.

5. Passare alla cartella "AzureVPN" decompressa.

6. Prendere nota della posizione del file "azurevpnconfig. xml". Azurevpnconfig. XML contiene l'impostazione per la connessione VPN e può essere importato direttamente nell'applicazione client VPN di Azure. È anche possibile distribuire questo file a tutti gli utenti che devono connettersi tramite posta elettronica o altri mezzi. Per la corretta connessione, l'utente dovrà disporre di credenziali Azure AD valide.

## <a name="next-steps"></a>Passaggi successivi

Per connettersi alla rete virtuale, è necessario creare e configurare un profilo client VPN. Vedere [configurare un client VPN per le connessioni VPN P2S](openvpn-azure-ad-client.md).
