---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/18/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e950d194ab48cec1a70c7bd17617332cb858a55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77485683"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. creare il tenant di Azure AD

Creare un tenant di Azure AD usando la procedura descritta nell'articolo [creare un nuovo tenant](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nome dell'organizzazione
* Nome di dominio iniziale

  Esempio:

   ![Nuovo tenant Azure AD](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. creare utenti tenant

In questo passaggio vengono creati due Azure AD utenti tenant: un account amministratore globale e un account utente Master. L'account utente master viene usato come account di incorporamento Master (account del servizio). Quando si crea un account utente Azure AD tenant, si regola il ruolo della directory per il tipo di utente che si desidera creare. Usare la procedura descritta in [questo articolo](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) per creare almeno due utenti per il tenant di Azure ad. Assicurarsi di modificare il **ruolo della directory** per creare i tipi di account:

* Amministratore globale
* Utente

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. registrare il client VPN

Registrare il client VPN nel tenant Azure AD.

1. Individuare l'ID directory della directory che si desidera utilizzare per l'autenticazione. Viene elencato nella sezione proprietà della pagina Active Directory.

    ![ID directory](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

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

    ![ID directory](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Selezionare **Accetto** quando richiesto.

    ![Accept](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Nel Azure AD, in **applicazioni aziendali**, viene visualizzata la rete **VPN di Azure** elencata.

     ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. registrare applicazioni aggiuntive

In questo passaggio si registrano applicazioni aggiuntive per diversi utenti e gruppi.

1. Nella Azure Active Directory fare clic su **registrazioni app** e quindi su **+ nuova registrazione**.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Nella pagina **registra un'applicazione** immettere il **nome**. Selezionare i **tipi di account supportati**desiderati, quindi fare clic su **registra**.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Una volta registrata la nuova app, fare clic su **esporre un'API** nel pannello dell'app.

4. Fare clic su **+ Aggiungi un ambito**.

5. Lasciare l' **URI dell'ID applicazione**predefinito. Fare clic su **Salva e continua**.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Compilare i campi obbligatori e verificare che **lo stato** sia **abilitato**. Fare clic su **Aggiungi ambito**.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Fare clic su **esporre un'API** e quindi **aggiungere un'applicazione client**.  Per **ID client**, immettere i valori seguenti a seconda del cloud:

    - Immettere **41b23e61-6c1e-4545-B367-cd054e0ed4b4** per il **pubblico** di Azure
    - Immettere **51bb15d4-3a4f-4EBF-9dca-40096fe32426** per Azure per **enti pubblici**
    - Immettere **538ee9e6-310A-468d-Afef-ea97365856a9** per Azure **Germania**
    - Immettere **49f817b6-84AE-4cc0-928C-73f27289b3aa** per Azure **China 21ViaNet**

8. Fare clic su **Aggiungi applicazione**.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Copiare l' **ID applicazione (client)** dalla pagina **Panoramica** . Queste informazioni sono necessarie per configurare i gateway VPN.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Ripetere i passaggi in questa sezione [registrare applicazioni aggiuntive](#register-apps) per creare tutte le applicazioni necessarie per i requisiti di sicurezza. Ogni applicazione verrà associata a un gateway VPN e potrà avere un diverso set di utenti. Solo un'applicazione può essere associata a un gateway.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. assegnare gli utenti alle applicazioni

Assegnare gli utenti alle applicazioni.

1. In **applicazioni aziendali Azure AD >** selezionare l'applicazione appena registrata e fare clic su **Proprietà**. Verificare che l' **assegnazione utente sia obbligatoria?** sia impostata su **Sì**. Fare clic su **Save**.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Nella pagina dell'app fare clic su **utenti e gruppi**e quindi su **+ Aggiungi utente**.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. In **Aggiungi assegnazione**fare clic su **utenti e gruppi**. Selezionare gli utenti a cui si vuole poter accedere a questa applicazione VPN. Fare clic su **Seleziona**.

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/user4.png)