---
title: 'Tenant Azure AD per le connessioni VPN utente: autenticazione Azure AD'
description: È possibile usare la VPN utente WAN virtuale di Azure (da punto a sito) per connettersi alla VNet usando l'autenticazione Azure AD
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: a54397fe7cfecef6813105645b7f2b218894875e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91367853"
---
# <a name="prepare-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Preparare Azure Active Directory tenant per le connessioni del protocollo OpenVPN VPN utente

Quando ci si connette all'hub virtuale tramite il protocollo IKEv2, è possibile usare l'autenticazione basata su certificati o l'autenticazione RADIUS. Tuttavia, quando si usa il protocollo OpenVPN, è anche possibile usare l'autenticazione Azure Active Directory. Questo articolo consente di configurare un tenant di Azure AD per la VPN utente WAN virtuale (da punto a sito) usando l'autenticazione OpenVPN.

> [!NOTE]
> Azure AD autenticazione è supportata solo per le &reg; connessioni di protocollo OpenVPN.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Creare il tenant di Azure AD

Verificare di disporre di un tenant Azure AD. Se non si ha un tenant di Azure AD, è possibile crearne uno seguendo la procedura descritta nell'articolo [creare un nuovo tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nome organizzazione
* Nome di dominio iniziale

Esempio:

   ![Nuovo tenant di Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. creare Azure AD utenti tenant

Successivamente, creare due account utente nel tenant di Azure AD appena creato, un account amministratore globale e un account utente. L'account utente può essere usato per testare l'autenticazione OpenVPN e l'account amministratore globale verrà usato per concedere il consenso alla registrazione dell'app VPN di Azure. Dopo aver creato un account utente Azure AD, assegnare un ruolo della **directory** all'utente per delegare le autorizzazioni amministrative.

Usare la procedura descritta in [questo articolo](../active-directory/fundamentals/add-users-azure-active-directory.md) per creare i due utenti per il tenant di Azure ad. Assicurarsi di modificare il **ruolo della directory** in uno degli account creati in **amministratore globale**.

## <a name="3-grant-consent-to-the-azure-vpn-app-registration"></a><a name="enable-authentication"></a>3. concedere il consenso alla registrazione dell'app VPN di Azure

1. Accedere al portale di Azure come utente a cui è assegnato il ruolo di **amministratore globale** .

2. Successivamente, concedere il consenso dell'amministratore per l'organizzazione, in modo da consentire all'applicazione VPN di Azure di accedere e leggere i profili utente. Copiare e incollare l'URL relativo alla posizione di distribuzione nella barra degli indirizzi del browser:

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

3. Se richiesto, selezionare l'account **amministratore globale** .

    ![ID directory](./media/openvpn-create-azure-ad-tenant/pick.png)

4. Quando richiesto, selezionare **Accetta**.

    ![Screenshot Visualizza la finestra di dialogo con le autorizzazioni per i messaggi accettate per l'organizzazione e altre informazioni.](./media/openvpn-create-azure-ad-tenant/accept.jpg)

5. Nel Azure AD, in **applicazioni aziendali**, verrà visualizzata la VPN di **Azure** elencata.

    ![VPN di Azure](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

## <a name="next-steps"></a>Passaggi successivi

Per connettersi alle reti virtuali usando Azure AD autenticazione, è necessario creare una configurazione VPN utente e associarla a un hub virtuale. Vedere [configurare l'autenticazione Azure ad per la connessione da punto a sito ad Azure](virtual-wan-point-to-site-azure-ad.md).
