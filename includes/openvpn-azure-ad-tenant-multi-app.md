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
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Creare il tenant di Azure AD

Creare un tenant di Azure AD seguendo la procedura descritta nell'articolo [Creare un nuovo tenant:Create](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md) an Azure AD tenant using the steps in the Create a new tenant article:

* Nome dell'organizzazione
* Nome di dominio iniziale

  Esempio:

   ![Nuovo tenant di Azure AD](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. Creare utenti tenant

In questo passaggio vengono creati due utenti tenant di Azure AD: un account amministratore globale e un account utente master. L'account utente master viene utilizzato come account di incorporamento principale (account di servizio). Quando si crea un account utente tenant di Azure AD, si modifica il ruolo Directory per il tipo di utente che si desidera creare. Usare i passaggi descritti in [questo articolo](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) per creare almeno due utenti per il tenant di Azure AD. Assicurarsi di modificare il **ruolo di directory** per creare i tipi di account:

* Amministratore globale
* Utente

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Registrare il client VPN

Registrare il client VPN nel tenant di Azure AD.

1. Individuare l'ID directory della directory che si desidera utilizzare per l'autenticazione. È elencato nella sezione delle proprietà della pagina di Active Directory.

    ![ID directory](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

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

    ![ID directory](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Selezionare **Accetta** quando richiesto.

    ![Accept](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. In Azure AD, in **Applicazioni aziendali,** verrà elencata la VPN di Azure.Under your Azure AD, in Enterprise applications , you will see **Azure VPN** listed.

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. Registrare applicazioni aggiuntive

In questo passaggio vengono registrate applicazioni aggiuntive per vari utenti e gruppi.

1. In Azure Active Directory fare clic su **Registrazioni app** e quindi su **Nuova registrazione**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Nella pagina **Registra un'applicazione** immettere il **nome**. Selezionare i **tipi di account supportati**desiderati, quindi fare clic su **Registra**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Dopo aver registrato la nuova app, fai clic su **Esporre un'API** nel pannello dell'app.

4. Fare clic su **Aggiungi un ambito**.

5. Lasciare **l'URI dell'ID applicazione**predefinito. Fare clic su **Salva e continua**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Compilare i campi obbligatori e verificare che **Stato** sia **Abilitato**. Fare clic su **Aggiungi ambito**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Fare clic su **Esponi un'API,** quindi **su Aggiungi un'applicazione client**.  Per **ID client**, immettere i seguenti valori a seconda del cloud:

    - Immettere **41b23e61-6c1e-4545-b367-cd054e0ed4b4** per **pubblico** di Azure
    - Immettere **51bb15d4-3a4f-4ebf-9dca-40096fe32426** per Azure **Government**
    - Immettere **538ee9e6-310a-468d-afef-ea97365856a9** per Azure **Germania**
    - Immettere **49f817b6-84ae-4cc0-928c-73f27289b3aa** per Azure **China 21Vianet**

8. Fare clic su **Aggiungi applicazione**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Copiare **l'ID applicazione (client)** dalla pagina **Panoramica.** Queste informazioni sono necessarie per configurare i gateway VPN.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Ripetere i passaggi in questa sezione [registrare applicazioni aggiuntive](#register-apps) per creare tutte le applicazioni necessarie per i requisiti di sicurezza. Ogni applicazione verrà associata a un gateway VPN e può avere un set di utenti diverso. A un gateway può essere associata una sola applicazione.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. Assegnare gli utenti alle applicazioni

Assegnare gli utenti alle applicazioni.

1. In **Azure AD -> applicazioni Enterprise**selezionare l'applicazione appena registrata e fare clic su **Proprietà**. Assicurarsi che **L'assegnazione utente richiesta?** sia impostata su **yes**. Fare clic su **Salva**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Nella pagina dell'app fare clic su **Utenti e gruppi**e quindi su Aggiungi **utente**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. In **Aggiungi assegnazione**fare clic su **Utenti e gruppi**. Selezionare gli utenti a cui si desidera accedere a questa applicazione VPN. Fare clic su **Seleziona**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)