---
title: 'Tenant Azure AD per le connessioni VPN utente: autenticazione Azure AD'
description: È possibile usare la VPN utente WAN virtuale di Azure (da punto a sito) per connettersi alla VNet usando l'autenticazione Azure AD
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 76c65d194d03dd1b7ff4cc2f3b45d84ff7909968
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753367"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Creare un tenant di Azure Active Directory per le connessioni del protocollo OpenVPN VPN utente

Quando ci si connette alla VNet, è possibile usare l'autenticazione basata su certificati o l'autenticazione RADIUS. Tuttavia, quando si usa il protocollo VPN aperto, è anche possibile usare l'autenticazione Azure Active Directory. Questo articolo illustra come configurare un tenant di Azure AD per l'autenticazione VPN per l'utente WAN virtuale (da punto a sito).

> [!NOTE]
> Azure AD autenticazione è supportata solo per le &reg; connessioni di protocollo OpenVPN.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Creare il tenant di Azure AD

Creare un tenant di Azure AD usando la procedura descritta nell'articolo [Creare un nuovo tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md):

* Nome dell'organizzazione
* Nome di dominio iniziale

Esempio:

   ![Nuovo tenant di Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. creare Azure AD utenti tenant

Successivamente, creare due account utente. Creare un account amministratore globale e un account utente Master. L'account utente master viene usato come account di incorporamento master (account del servizio). Quando si crea un account utente del tenant di Azure AD, si regola il ruolo della directory per il tipo di utente che si desidera creare.

Usare la procedura descritta in [questo articolo](../active-directory/fundamentals/add-users-azure-active-directory.md) per creare almeno due utenti per il tenant di Azure AD. Assicurarsi di modificare il **ruolo della directory** per creare i tipi di account:

* Amministratore globale
* Utente

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. abilitare l'autenticazione Azure AD nel gateway VPN

1. Individuare l'ID directory della directory da usare per l'autenticazione. Questo è elencato nella sezione Proprietà della pagina Active Directory.

    ![ID directory](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Copiare l'ID directory.

3. Accedere al portale di Azure come utente con il ruolo di **amministratore globale**.

4. Accordare quindi il consenso amministratore. Copiare e incollare l'URL relativo alla posizione di distribuzione nella barra degli indirizzi del browser:

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

5. Se richiesto, selezionare l'account di **amministratore globale**.

    ![ID directory](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Quando richiesto, selezionare **Accetta**.

    ![Accept](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Nel Azure AD, in **applicazioni aziendali**, viene visualizzata la rete **VPN di Azure** elencata.

    ![VPN di Azure](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Configurare Azure AD autenticazione per la VPN utente e assegnarla a un hub virtuale attenendosi alla procedura descritta in [configurare l'autenticazione Azure ad per la connessione da punto a sito ad Azure](virtual-wan-point-to-site-azure-ad.md)

## <a name="next-steps"></a>Passaggi successivi

Per connettersi alla rete virtuale, è necessario creare e configurare un profilo client VPN e associarlo a un hub virtuale. Vedere [configurare l'autenticazione Azure ad per la connessione da punto a sito ad Azure](virtual-wan-point-to-site-azure-ad.md).
