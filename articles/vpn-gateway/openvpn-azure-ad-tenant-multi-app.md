---
title: 'Gateway VPN: Azure AD tenant per gruppi di utenti diversi: autenticazione Azure AD'
description: È possibile usare la VPN P2S per connettersi alla VNet usando l'autenticazione Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: alzam
ms.openlocfilehash: 73a47b20010be0b4eadec790dd8fb254f912ff2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477174"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Creare un tenant di Azure Active Directory per le connessioni del protocollo OpenVPN P2S

Quando ci si connette alla VNet, è possibile usare l'autenticazione basata su certificati o l'autenticazione RADIUS. Tuttavia, quando si usa il protocollo VPN aperto, è anche possibile usare l'autenticazione Azure Active Directory. Se si vuole che un diverso set di utenti sia in grado di connettersi a diversi gateway VPN, è possibile registrare più app in Active Directory e collegarle a diversi gateway VPN. Questo articolo illustra come configurare un tenant di Azure AD per l'autenticazione di P2S OpenVPN e come creare e registrare più app in Azure AD per consentire l'accesso diverso per utenti e gruppi diversi.

> [!NOTE]
> L'autenticazione di Azure AD è supportata solo per le connessioni tramite il protocollo OpenVPN®.
>

## <a name="tenant"></a>1. creare il tenant di Azure AD

Creare un tenant di Azure AD usando la procedura descritta nell'articolo [creare un nuovo tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nome dell'organizzazione
* Nome di dominio iniziale

Esempio:

   ![Nuovo tenant Azure AD](./media/openvpn-azure-ad-tenant-multi-app/newtenant.png)

## <a name="users"></a>2. creare Azure AD utenti tenant

Successivamente, creare due account utente. Creare un account amministratore globale e un account utente Master. L'account utente master viene usato come account di incorporamento Master (account del servizio). Quando si crea un account utente Azure AD tenant, si regola il ruolo della directory per il tipo di utente che si desidera creare.

Usare la procedura descritta in [questo articolo](../active-directory/fundamentals/add-users-azure-active-directory.md) per creare almeno due utenti per il tenant di Azure ad. Assicurarsi di modificare il **ruolo della directory** per creare i tipi di account:

* Amministratore globale
* Utente

## <a name="enable-authentication"></a>3. registrare il client VPN di Azure nel tenant Azure AD

1. Individuare l'ID directory della directory che si desidera utilizzare per l'autenticazione. Viene elencato nella sezione proprietà della pagina Active Directory.

    ![ID directory](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Copiare l'ID directory.

3. Accedere al portale di Azure come utente a cui viene assegnato il ruolo di **amministratore globale** .

4. Quindi, concedere il consenso dell'amministratore. Copiare e incollare l'URL relativo alla posizione di distribuzione nella barra degli indirizzi del browser:

    Pubblico

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure per enti pubblici

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

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/azurevpn.png)

## <a name="enable-authentication"></a>4. registrare applicazioni aggiuntive per diversi utenti/gruppi

1. Nella Azure Active Directory fare clic su **registrazioni app** e quindi su **+ nuova registrazione**

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Nel pannello **registra un'applicazione** immettere il **nome** e selezionare i tipi di **account supportati** desiderati e fare clic su **registra** .

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Una volta registrata la nuova app, fare clic su **esporre un'API** nel pannello dell'app

4. Fare clic su **+ Aggiungi un ambito**
5. Lasciare l' **URI dell'ID applicazione** predefinito e fare clic su **Salva e continua**

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app3.png)
6. Compilare i campi obbligatori e verificare che **lo stato** sia **abilitato**. Fare clic su **Aggiungi ambito**

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app4.png)
7. Fare clic su **esporre un'API** e quindi **aggiungere un'applicazione client**.  Per **ID client**, immettere i valori seguenti a seconda del cloud:
    -   Immettere **41b23e61-6c1e-4545-B367-cd054e0ed4b4** per il **pubblico** di Azure
    -   Immettere **51bb15d4-3a4f-4EBF-9dca-40096fe32426** per Azure per **enti pubblici**
    -   Immettere **538ee9e6-310A-468d-Afef-ea97365856a9** per Azure **Germania**
    -   Immettere **49f817b6-84AE-4cc0-928C-73f27289b3aa** per Azure **China 21ViaNet**


8. Fare clic su **Aggiungi applicazione**

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app5.png)
9. Copiare l' **ID applicazione (client)** dalla pagina **Panoramica** . Sarà necessario per configurare i gateway VPN

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Ripetere i passaggi descritti in questa sezione (4) per creare tutte le applicazioni necessarie per i requisiti di sicurezza. Ogni applicazione verrà associata a un gateway VPN e potrà avere un diverso set di utenti. Solo un'applicazione può essere associata a un gateway.

## <a name="enable-authentication"></a>5. assegnare gli utenti alle applicazioni

1. In Azure AD, **applicazioni aziendali**, selezionare l'applicazione appena registrata e fare clic su **Proprietà**. Verificare che l' **assegnazione utente sia obbligatoria?** sia impostata su **Sì**. Fare clic su **Save** (Salva).

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Nella pagina dell'app fare clic su **utenti e gruppi** e quindi su **Aggiungi utente**

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/user3.png)
3. In **Aggiungi assegnazione**fare clic su **utenti e gruppi**. Selezionare gli utenti a cui si vuole poter accedere a questa applicazione VPN. Fare clic su **Seleziona**

    ![VPN di Azure](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

## <a name="enable-authentication"></a>6. abilitare l'autenticazione Azure AD nel gateway VPN

1. Abilitare l'autenticazione Azure AD nel gateway VPN eseguendo i comandi seguenti, assicurandosi di modificare il comando in modo da riflettere il proprio ambiente:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
> [!NOTE]
> Non usare l'ID dell'applicazione del client VPN di Azure nei comandi precedenti perché consentirà a tutti gli utenti di accedere al gateway VPN. Usare l'ID dell'applicazione o delle applicazioni registrate.

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
