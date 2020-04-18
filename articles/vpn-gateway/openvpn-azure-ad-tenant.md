---
title: 'Gateway VPN: tenant di Azure AD per connessioni VPN P2S: autenticazione di Azure AD'
description: È possibile usare la VPN P2S per connettersi alla rete virtuale usando l'autenticazione di Azure ADYou can use P2S VPN to connect to your VNet using Azure AD authentication
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: alzam
ms.openlocfilehash: 00db2ed05285a1637414aa1e3adbe3b047ff0568
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641337"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Creare un tenant di Azure Active Directory per le connessioni di protocollo OpenVPN P2SCreate an Azure Active Directory tenant for P2S OpenVPN protocol connections

Quando ci si connette alla rete virtuale, è possibile utilizzare l'autenticazione basata su certificati o l'autenticazione RADIUS. Tuttavia, quando si usa il protocollo Open VPN, è anche possibile usare l'autenticazione di Azure Active Directory.However, when you use the Open VPN protocol, you can also use Azure Active Directory authentication. Questo articolo consente di configurare un tenant di Azure AD per l'autenticazione P2S Open VPN.

> [!NOTE]
> L'autenticazione di Azure AD è supportata solo per le connessioni tramite il protocollo OpenVPN®.
>


## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Verificare il tenant di Azure AD

Verificare di disporre di un tenant di Azure AD. Se non si dispone di un tenant di Azure AD, è possibile crearne uno usando la procedura descritta nell'articolo Creare un nuovo tenant:If you don't have an Azure AD tenant, you can create one using the steps in [the Create a new tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) article:

* Nome dell'organizzazione
* Nome di dominio iniziale

Esempio:

   ![Nuovo tenant di Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Creare utenti tenant di Azure AD

Il tenant di Azure AD richiede gli account seguenti: un account amministratore globale e un account utente master. L'account utente master viene utilizzato come account di incorporamento principale (account di servizio). Quando si crea un account utente tenant di Azure AD, si modifica il ruolo Directory per il tipo di utente che si desidera creare.

Usare i passaggi descritti in [questo articolo](../active-directory/fundamentals/add-users-azure-active-directory.md) per creare almeno due utenti per il tenant di Azure AD. Assicurarsi di modificare il **ruolo di directory** per creare i tipi di account:

* Amministratore globale
* Utente

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Abilitare l'autenticazione di Azure AD nel gateway VPN

1. Individuare l'ID directory della directory che si desidera utilizzare per l'autenticazione. È elencato nella sezione delle proprietà della pagina di Active Directory.

    ![ID directory](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Copiare l'ID directory.

3. Accedere al portale di Azure come utente a cui è assegnato il ruolo **di amministratore globale.**

4. Successivamente, dare il consenso dell'amministratore. Copiare e incollare l'URL che riguarda il percorso di distribuzione nella barra degli indirizzi del browser:

    Pubblico

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    21Vianet per Azure Cina

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Se richiesto, selezionare l'account **amministratore globale.**

    ![ID directory](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Selezionare **Accetta** quando richiesto.

    ![Accept](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. In Azure AD, in **Applicazioni aziendali,** nella VPN di **Azure** è elencata.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Se non è già presente un ambiente da punto a sito in funzione, seguire le istruzioni per crearne uno. Visualizzare [Creare una VPN Point-to-Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) per creare e configurare un gateway VPN da punto a sito con autenticazione del certificato di Azure nativa. 

    > [!IMPORTANT]
    > Lo SKU Basic non è supportato per OpenVPN.

9. Abilitare l'autenticazione di Azure AD nel gateway VPN eseguendo i comandi seguenti, assicurandosi di modificare il comando in modo che rifletta il proprio ambiente:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>/" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

   > [!NOTE]
   > Assicurarsi di includere una barra finale `AadIssuerUri` alla fine del valore. In caso contrario, il comando avrà esito negativo.

10. Creare e scaricare il profilo eseguendo i comandi seguenti. Modificare i valori -ResourceGroupName e -Name in modo che corrispondano ai propri.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. Dopo aver eseguito i comandi, viene visualizzato un risultato simile a quello riportato di seguito. Copiare l'URL del risultato nel browser per scaricare il file zip del profilo.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/profile.png)

12. Estrarre il file zip scaricato.

13. Passare alla cartella "AzureVPN" decompressa.

14. Prendere nota del percorso del file "azurevpnconfig.xml". Il file azurevpnconfig.xml contiene l'impostazione per la connessione VPN e può essere importato direttamente nell'applicazione client VPN di Azure.The azurevpnconfig.xml contains the setting for the VPN connection and can be imported directly into the Azure VPN Client application. È inoltre possibile distribuire questo file a tutti gli utenti che devono connettersi tramite posta elettronica o altri mezzi. L'utente avrà bisogno di credenziali di Azure AD valide per connettersi correttamente.

## <a name="next-steps"></a>Passaggi successivi

Per connettersi alla rete virtuale, è necessario creare e configurare un profilo client VPN. Vedere Configurare un client VPN per le [connessioni VPN P2S.](openvpn-azure-ad-client.md)
