---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2eb2dbc43c59f4f6301c7f5073a73462639d35b2
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797164"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Creare il tenant di Azure AD

Creare un tenant di Azure AD usando la procedura descritta nell'articolo [Creare un nuovo tenant](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md):

* Nome dell'organizzazione
* Nome di dominio iniziale

  Esempio:

   ![Nuovo tenant di Azure AD](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. Creare utenti del tenant

In questo passaggio vengono creati due utenti del tenant di Azure AD: Un account amministratore globale e un account utente master. L'account utente master viene usato come account di incorporamento master (account del servizio). Quando si crea un account utente del tenant di Azure AD, si regola il ruolo della directory per il tipo di utente che si desidera creare. Usare la procedura descritta in [questo articolo](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) per creare almeno due utenti per il tenant di Azure AD. Assicurarsi di modificare il **ruolo della directory** per creare i tipi di account:

* Amministratore globale
* Utente

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Registrare il client VPN

Registrare il client VPN nel tenant di Azure AD.

1. Individuare l'ID directory della directory da usare per l'autenticazione. Questo è elencato nella sezione Proprietà della pagina Active Directory.

    ![ID directory](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Copiare l'ID directory.

3. Accedere al portale di Azure come utente con il ruolo di **amministratore globale**.

4. Accordare quindi il consenso amministratore. Copiare e incollare l'URL relativo alla posizione di distribuzione nella barra degli indirizzi del browser:

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Se richiesto, selezionare l'account di **amministratore globale**.

    ![ID directory](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Quando richiesto, selezionare **Accetta**.

    ![Accept](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. In Azure AD, sotto **Applicazioni aziendali**, verrà visualizzato **VPN di Azure**.

     ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. Registrare applicazioni aggiuntive

In questo passaggio si registreranno applicazioni aggiuntive per diversi utenti e gruppi.

1. In Azure Active Directory, fare clic su **Registrazioni app** e quindi su **+ Nuova registrazione**.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Nella pagina **Registra un'applicazione** immettere il **Nome**. Selezionare i **tipi di account supportati** desiderati, quindi fare clic su **Registra**.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Una volta registrata la nuova app, fare clic su **Esporre un'API** nel pannello dell'app.

4. Fare clic su **+ Aggiungi ambito**.

5. Lasciare l'**URI ID applicazione** predefinito. Fare clic su **Salva e continua**.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Compilare i campi obbligatori e verificare che lo **Stato** sia **Abilitato**. Fare clic su **Aggiungi ambito**.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Fare clic su **Esporre un'API**, quindi su **+ Aggiungi un'applicazione client**.  Per **ID client**, immettere i valori seguenti a seconda del cloud:

    - Immettere **41b23e61-6c1e-4545-b367-cd054e0ed4b4** per Azure **Public**
    - Immettere **51bb15d4-3a4f-4ebf-9dca-40096fe32426** per Azure **per enti pubblici**
    - Immettere **538ee9e6-310a-468d-afef-ea97365856a9** per Azure **Germania**
    - Immettere **49f817b6-84ae-4cc0-928c-73f27289b3aa** per Azure **China (21Vianet)**

8. Fare clic su **Aggiungi applicazione**.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Copiare il valore del campo **ID applicazione (client)** dalla pagina di **Panoramica**. Queste informazioni sono necessarie per configurare i gateway VPN.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Ripetere i passaggi descritti in questa sezione [Registrare applicazioni aggiuntive](#register-apps) per creare tutte le applicazioni necessarie per i requisiti di sicurezza. Ogni applicazione verrà associata a un gateway VPN e potrà avere un diverso set di utenti. A un gateway può essere associata solo un'applicazione.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. Assegnare utenti alle applicazioni

Assegnare gli utenti alle applicazioni.

1. In **Azure AD -> Applicazioni aziendali**, selezionare l'applicazione appena registrata e fare clic su **Proprietà**. Assicurarsi che **Assegnazione utenti obbligatoria** sia impostato su **Sì**. Fare clic su **Salva**.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Nella pagina dell'app, fare clic su **Utenti e gruppi**, quindi fare clic su **+ Aggiungi utente**.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. In **Aggiungi assegnazione**, fare clic su **Utenti e gruppi**. Selezionare gli utenti a cui si vuole garantire l'accesso a questa applicazione VPN. Fare clic su **Seleziona**.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/user4.png)
