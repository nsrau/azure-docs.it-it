---
title: 'Tenant di Azure AD per le connessioni VPN utente: autenticazione di Azure ADAzure AD tenant for User VPN connections: Azure AD authentication'
description: È possibile usare la VPN dell'utente WAN virtuale di Azure (da punto a sito) per connettersi alla rete virtuale usando l'autenticazione di Azure ADYou can use Azure Virtual WAN User VPN (point-to-site) to connect to your VNet using Azure AD authentication
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 74347ce969b6a5ffd57f5ca8396517e78590f3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059450"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Creare un tenant di Azure Active Directory per le connessioni di protocollo OpenVPN VPN utenteCreate an Azure Active Directory tenant for User VPN OpenVPN protocol connections

Quando ci si connette alla rete virtuale, è possibile utilizzare l'autenticazione basata su certificati o l'autenticazione RADIUS. Tuttavia, quando si usa il protocollo Open VPN, è anche possibile usare l'autenticazione di Azure Active Directory.However, when you use the Open VPN protocol, you can also use Azure Active Directory authentication. Questo articolo consente di configurare un tenant di Azure AD per l'autenticazione VPN aperta dell'utente WAN virtuale (da punto a sito) Open VPN.

> [!NOTE]
> L'autenticazione di Azure&reg; AD è supportata solo per le connessioni di protocollo OpenVPN.Azure AD authentication is supported only for OpenVPN protocol connections.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Creare il tenant di Azure AD

Creare un tenant di Azure AD seguendo la procedura descritta nell'articolo [Creare un nuovo tenant:Create](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) an Azure AD tenant using the steps in the Create a new tenant article:

* Nome dell'organizzazione
* Nome di dominio iniziale

Esempio:

   ![Nuovo tenant di Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Creare utenti tenant di Azure AD

Successivamente, creare due account utente. Creare un account amministratore globale e un account utente master. L'account utente master viene utilizzato come account di incorporamento principale (account di servizio). Quando si crea un account utente tenant di Azure AD, si modifica il ruolo Directory per il tipo di utente che si desidera creare.

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

5. Se richiesto, selezionare l'account **amministratore globale.**

    ![ID directory](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Selezionare **Accetta** quando richiesto.

    ![Accept](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. In Azure AD, in **Applicazioni aziendali,** nella VPN di **Azure** è elencata.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Configurare l'autenticazione di Azure AD per la VPN utente e assegnarla a un hub virtuale seguendo i passaggi descritti in Configurare l'autenticazione di Azure AD per la connessione da punto a sito ad AzureConfigure Azure AD authentication for User VPN and assign it to a Virtual Hub by following the steps in [Configure Azure AD authentication for Point-to-Site connection to Azure](virtual-wan-point-to-site-azure-ad.md)

## <a name="next-steps"></a>Passaggi successivi

Per connettersi alla rete virtuale, è necessario creare e configurare un profilo client VPN e associarlo a un hub virtuale. Vedere [Configurare l'autenticazione di Azure AD per la connessione da punto a sito ad Azure.See Configure Azure AD authentication for Point-to-Site connection to Azure](virtual-wan-point-to-site-azure-ad.md).
