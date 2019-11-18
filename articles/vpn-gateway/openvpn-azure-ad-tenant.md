---
title: 'Gateway VPN: Azure AD tenant per le connessioni VPN P2S: Azure AD autenticazione'
description: È possibile usare la VPN P2S per connettersi alla VNet usando l'autenticazione Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: alzam
ms.openlocfilehash: 73c379d914f37de351165c19e3d73425e9a202b2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151859"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Creare un tenant di Azure Active Directory per le connessioni del protocollo OpenVPN P2S

Quando ci si connette alla VNet, è possibile usare l'autenticazione basata su certificati o l'autenticazione RADIUS. Tuttavia, quando si usa il protocollo VPN aperto, è anche possibile usare l'autenticazione Azure Active Directory. Questo articolo consente di configurare un tenant di Azure AD per l'autenticazione VPN aperta P2S.

> [!NOTE]
> Azure AD autenticazione è supportata solo per le connessioni di protocollo OpenVPN®.
>

## <a name="tenant"></a>1. creare il tenant di Azure AD

Creare un tenant di Azure AD usando la procedura descritta nell'articolo [creare un nuovo tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nome dell'organizzazione
* Nome di dominio iniziale

Esempio:

   ![Nuovo tenant Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="users"></a>2. creare Azure AD utenti tenant

Successivamente, creare due account utente. Creare un account amministratore globale e un account utente Master. L'account utente master viene usato come account di incorporamento Master (account del servizio). Quando si crea un account utente Azure AD tenant, si regola il ruolo della directory per il tipo di utente che si desidera creare.

Usare la procedura descritta in [questo articolo](../active-directory/fundamentals/add-users-azure-active-directory.md) per creare almeno due utenti per il tenant di Azure ad. Assicurarsi di modificare il **ruolo della directory** per creare i tipi di account:

* Amministratore globale
* User

## <a name="enable-authentication"></a>3. abilitare l'autenticazione Azure AD nel gateway VPN

1. Individuare l'ID directory della directory che si desidera utilizzare per l'autenticazione. Viene elencato nella sezione proprietà della pagina Active Directory.

    ![ID directory](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Copiare l'ID directory.

3. Accedere al portale di Azure come utente a cui viene assegnato il ruolo di **amministratore globale** .

4. Quindi, concedere il consenso dell'amministratore. Copiare e incollare l'URL relativo alla posizione di distribuzione nella barra degli indirizzi del browser:

    Pubblico

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    21Vianet per Azure Cina

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Se richiesto, selezionare l'account **amministratore globale** .

    ![ID directory](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Selezionare **Accetto** quando richiesto.

    ![Accept](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Nel Azure AD, in **applicazioni aziendali**, viene visualizzata la rete **VPN di Azure** elencata.

    ![VPN di Azure](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Abilitare l'autenticazione Azure AD nel gateway VPN eseguendo i comandi seguenti, assicurandosi di modificare il comando in modo da riflettere il proprio ambiente:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/"
    ```

9. Creare e scaricare il profilo eseguendo i comandi seguenti. Modificare i valori-ResourcGroupName e-Name in modo che corrispondano a quelli personalizzati.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

10. Dopo aver eseguito i comandi, viene visualizzato un risultato simile a quello riportato di seguito. Copiare l'URL dei risultati nel browser per scaricare il file zip del profilo.

    ![VPN di Azure](./media/openvpn-create-azure-ad-tenant/profile.png)

11. Estrarre il file zip scaricato.

12. Passare alla cartella "AzureVPN" decompressa.

13. Prendere nota della posizione del file "azurevpnconfig. xml". Azurevpnconfig. XML contiene l'impostazione per la connessione VPN e può essere importato direttamente nell'applicazione client VPN di Azure. È anche possibile distribuire questo file a tutti gli utenti che devono connettersi tramite posta elettronica o altri mezzi. Per la corretta connessione, l'utente dovrà disporre di credenziali Azure AD valide.

## <a name="next-steps"></a>Passaggi successivi

Per connettersi alla rete virtuale, è necessario creare e configurare un profilo client VPN. Vedere [configurare un client VPN per le connessioni VPN P2S](openvpn-azure-ad-client.md).
