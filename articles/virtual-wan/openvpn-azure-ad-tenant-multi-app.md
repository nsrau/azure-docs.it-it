---
title: 'Gateway VPN: Azure AD tenant per gruppi di utenti diversi: autenticazione Azure AD'
description: È possibile usare la VPN P2S per connettersi alla VNet usando l'autenticazione Azure AD
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: 90244b9dcf30c2ef01d4e57c9d8e35fa1d71f434
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985646"
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

## <a name="site"></a>6. creare una nuova configurazione di P2S

Una configurazione da punto a sito definisce i parametri per la connessione di client remoti.

1. Impostare le variabili seguenti, sostituendo i valori in base alle esigenze per l'ambiente corrente.

   ```powershell
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Eseguire i comandi seguenti per creare la configurazione:

   ```powershell
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

> [!NOTE]
> Non usare l'ID dell'applicazione del client VPN di Azure nei comandi precedenti perché consentirà a tutti gli utenti di accedere al gateway VPN. Usare l'ID dell'applicazione o delle applicazioni registrate.

## <a name="hub"></a>7. Modifica assegnazione Hub

1. Passare al pannello **Hub** nella rete WAN virtuale.
2. Selezionare l'hub a cui si vuole associare la configurazione del server VPN e fare clic sui puntini di sospensione (...).

   ![nuovo sito](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Fare clic su **Modifica hub virtuale**.
4. Selezionare la casella di controllo **Includi gateway da punto a sito** e selezionare l'**unità di scala gateway** che si vuole usare.

   ![nuovo sito](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Immettere il **Pool di indirizzi** da cui gli indirizzi IP verranno assegnati ai client VPN.
6. Fare clic su **Conferma**.
7. Il completamento dell'operazione potrà richiedere fino a 30 minuti.

## <a name="device"></a>8. scaricare il profilo VPN

Usare il profilo VPN per configurare i client.

1. Nella pagina della rete WAN virtuale fare clic su **Configurazioni VPN utente**.
2. Nella parte superiore della pagina fare clic su **Download user VPN config** (Scarica configurazione VPN utente).
3. Al termine della creazione del file è possibile fare clic sul collegamento per scaricarlo.
4. Usare il file del profilo per configurare i client VPN.

4. Estrarre il file zip scaricato.

5. Passare alla cartella "AzureVPN" decompressa.

6. Prendere nota della posizione del file "azurevpnconfig. xml". Azurevpnconfig. XML contiene l'impostazione per la connessione VPN e può essere importato direttamente nell'applicazione client VPN di Azure. È anche possibile distribuire questo file a tutti gli utenti che devono connettersi tramite posta elettronica o altri mezzi. Per la corretta connessione, l'utente dovrà disporre di credenziali Azure AD valide.
## <a name="configure-user-vpn-clients"></a>Configurare i client VPN utente

Per connettersi, è necessario scaricare il client VPN di Azure (anteprima) e importare il profilo client VPN scaricato nei passaggi precedenti in ogni computer che dovrà connettersi alla rete virtuale.

> [!NOTE]
> L'autenticazione di Azure AD è supportata solo per le connessioni tramite il protocollo OpenVPN®.
>

#### <a name="to-download-the-azure-vpn-client"></a>Per scaricare il client VPN di Azure

Usare questo [collegamento](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) per scaricare il Client VPN di Azure (anteprima).

#### <a name="import"></a>Per importare un profilo client

1. Nella pagina selezionare **Importa**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Individuare il file XML del profilo e selezionarlo. Con il file selezionato, selezionare **Apri**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Specificare il nome del profilo e selezionare **Salva**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Selezionare **Connetti** per connettersi alla VPN.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Una volta stabilita la connessione, l'icona diventerà verde mostrerà lo stato **Connesso**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>Per eliminare un profilo client

1. Selezionare i puntini di sospensione (...) accanto al profilo client da eliminare. Selezionare quindi **Rimuovi**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Per procedere all'eliminazione, selezionare **Rimuovi**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnosticare i problemi di connessione

1. Per diagnosticare i problemi di connessione, è possibile usare lo strumento **Diagnosi**. Selezionare i puntini di sospensione (...) accanto alla connessione VPN che si vuole diagnosticare per visualizzare il menu. Selezionare quindi **Diagnosi**.

    ![diagnosi](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Nella pagina **Proprietà connessione** selezionare **Esegui diagnosi**.

    ![diagnosi](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Accedere con le credenziali personali.

    ![diagnosi](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Visualizzare i risultati della diagnosi.

    ![diagnosi](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>Visualizzare la rete WAN virtuale

1. Passare alla rete WAN virtuale.
2. Nella pagina Panoramica ogni punto sulla mappa rappresenta un hub. Passare il mouse su qualsiasi punto per visualizzare il riepilogo dell'integrità dell'hub.
3. Nella sezione degli hub e delle connessioni è possibile visualizzare lo stato dell'hub, il sito, l'area, lo stato della connessione VPN e i byte in entrata e in uscita.

## <a name="viewhealth"></a>Visualizzare l'integrità delle risorse

1. Passare alla rete WAN.
2. Nella pagina della rete WAN, nella sezione **Supporto e risoluzione dei problemi** fare clic su **Integrità** e visualizzare la risorsa.


## <a name="cleanup"></a>Pulire le risorse

Quando queste risorse non sono più necessarie, è possibile usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute. Sostituire "myResourceGroup" con il nome del gruppo di risorse specifico ed eseguire il comando di PowerShell seguente:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).
