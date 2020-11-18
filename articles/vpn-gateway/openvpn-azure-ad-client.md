---
title: 'Gateway VPN: client VPN per le connessioni del protocollo OpenVPN P2S: autenticazione Azure AD'
description: Informazioni su come configurare un client VPN per connettersi a una rete virtuale usando la VPN da punto a sito e l'autenticazione Azure Active Directory.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2020
ms.author: alzam
ms.openlocfilehash: 7c2c1930b8f801db7f70baa5b713a641606be644
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94661172"
---
# <a name="azure-active-directory-authentication-configure-a-vpn-client-for-p2s-openvpn-protocol-connections"></a>Autenticazione Azure Active Directory: configurare un client VPN per le connessioni del protocollo OpenVPN P2S

Questo articolo illustra come configurare un client VPN per connettersi a una rete virtuale usando la VPN da punto a sito e l'autenticazione Azure Active Directory. Prima di poter connettersi ed eseguire l'autenticazione con Azure AD, è necessario innanzitutto configurare il tenant di Azure AD. Per altre informazioni, vedere [configurare un tenant di Azure ad](openvpn-azure-ad-tenant.md).

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="working-with-client-profiles"></a><a name="profile"></a>Utilizzo dei profili client

Per connettersi, è necessario scaricare il client VPN di Azure e configurare un profilo client VPN in ogni computer che desidera connettersi a VNet. È possibile creare un profilo client in un computer, esportarlo e quindi importarlo in altri computer.

### <a name="to-download-the-azure-vpn-client"></a>Per scaricare il client VPN di Azure

Usare questo [collegamento](https://go.microsoft.com/fwlink/?linkid=2117554) per scaricare il client VPN di Azure. Verificare che il client VPN di Azure disponga delle autorizzazioni per l'esecuzione in background. Per selezionare o abilitare l'autorizzazione, attenersi alla procedura seguente:

1. Passare a Start, quindi selezionare impostazioni > privacy > app in background.
2. In app in background verificare che **le app eseguite in background** siano attivate.
3. In scegliere le app che possono essere eseguite in background, attivare le impostazioni per il client VPN di Azure **su on**.

  ![autorizzazione](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Per creare un profilo client basato su certificati

Quando si utilizza un profilo basato su certificato, verificare che nel computer client siano installati i certificati appropriati. Per ulteriori informazioni sui certificati, vedere [Install Client Certificates](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Per creare un profilo client RADIUS

  ![radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Il segreto server può essere esportato nel profilo client VPN P2S.  Le istruzioni su come esportare un profilo client sono disponibili [qui](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Per esportare e distribuire un profilo client

Quando si dispone di un profilo di lavoro ed è necessario distribuirlo ad altri utenti, è possibile esportarlo attenendosi alla procedura seguente:

1. Evidenziare il profilo client VPN che si vuole esportare, selezionare il **...** e quindi selezionare **Esporta**.

    ![Screenshot che mostra la pagina "client VPN di Azure" con i puntini di sospensione selezionati e "Esporta" evidenziati.](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Selezionare il percorso in cui si desidera salvare il profilo, lasciare il nome del file così com'è, quindi selezionare **Salva** per salvare il file XML.

    ![esportare](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Per importare un profilo client

1. Nella pagina selezionare **Importa**.

    ![Screenshot che mostra il pulsante "Aggiungi" selezionato e l'azione "Importa" evidenziata nella parte inferiore sinistra della finestra.](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Individuare il file XML del profilo e selezionarlo. Con il file selezionato, selezionare **Apri**.

    ![Screenshot che mostra un file del profilo x m l selezionato.](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Specificare il nome del profilo e selezionare **Salva**.

    ![Screenshot che mostra il "nome della connessione" evidenziato e il pulsante "Salva" selezionato.](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Selezionare **Connetti** per connettersi alla VPN.

    ![Screenshot che mostra la VPN e il pulsante "Connetti" selezionato.](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Una volta stabilita la connessione, l'icona diventerà verde mostrerà lo stato **Connesso**.

    ![importazione](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Per eliminare un profilo client

1. Selezionare i puntini di sospensione accanto al profilo client che si desidera eliminare. Selezionare quindi **Rimuovi**.

    ![Screenshot che mostra i puntini di sospensione e l'opzione "Rimuovi" selezionata.](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Per procedere all'eliminazione, selezionare **Rimuovi**.

    ![eliminare](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Creare una connessione

1. Nella pagina selezionare **+** , quindi **+ Aggiungi**.

    ![Screenshot che mostra il pulsante "Aggiungi" selezionato.](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Inserire le informazioni di connessione. Se non si è certi dei valori, contattare l'amministratore. Dopo aver compilato i valori, selezionare **Salva**.

    ![Screenshot che mostra le proprietà della connessione VPN evidenziate e il pulsante "Salva" selezionato.](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Selezionare **Connetti** per connettersi alla VPN.

    ![Screenshot che mostra il pulsante "Connetti" selezionato.](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Selezionare le credenziali appropriate e quindi fare clic su **continua**.

    ![Screenshot che mostra le credenziali di esempio evidenziate e il pulsante "continua" selezionato.](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Una volta stabilita la connessione, l'icona diventerà verde e si **disconnetterà**.

    ![connection](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Per connettersi automaticamente

Questi passaggi consentono di configurare la connessione per la connessione automatica con always-on.

1. Nella home page per il client VPN selezionare **Impostazioni VPN**.

    ![Screenshot della home page VPN con "impostazioni VPN" selezionato.](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Selezionare **Sì** nella finestra di dialogo Cambia app.

    ![Screenshot del "si intendeva passare alle app?" finestra di dialogo con il pulsante "Sì" selezionato.](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Verificare che la connessione che si vuole impostare non sia già connessa, quindi evidenziare il profilo e selezionare la casella di controllo **Connetti automaticamente** .

    ![Screenshot della finestra "Impostazioni" con la casella "Connetti automaticamente" selezionata.](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Selezionare **Connetti** per avviare la connessione VPN.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnosticare i problemi di connessione

1. Per diagnosticare i problemi di connessione, è possibile usare lo strumento **Diagnosi**. Selezionare il **...** accanto alla connessione VPN che si desidera diagnosticare per visualizzare il menu. Selezionare quindi **Diagnosi**.

    ![Screenshot dei puntini di sospensione e "diagnosi selezionata".](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Nella pagina **Proprietà connessione** selezionare **Esegui diagnosi**.

    ![Screenshot che mostra la pagina "Proprietà connessione" con l'opzione "Esegui diagnosi" selezionata.](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Accedere con le credenziali personali.

    ![Screenshot che mostra la finestra di dialogo "è stato eseguito l'accesso" con un account aziendale o dell'Istituto di istruzione selezionato.](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Visualizzare i risultati della diagnosi.

    ![diagnose](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Domande frequenti

### <a name="is-the-azure-vpn-client-supported-with-windows-fips-mode"></a>Il client VPN di Azure è supportato con la modalità FIPS di Windows?

Sì, con l'hotfix [KB4577063](https://support.microsoft.com/help/4577063/windows-10-update-kb4577063) .

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Ricerca per categorie aggiungere suffissi DNS al client VPN?

È possibile modificare il file XML del profilo scaricato e aggiungere **\<dnssuffixes> \<dnssufix> \</dnssufix> \</dnssuffixes>** i tag

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Ricerca per categorie aggiungere server DNS personalizzati al client VPN?

È possibile modificare il file XML del profilo scaricato e aggiungere **\<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers>** i tag

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> Il client Azure AD OpenVPN usa le voci della tabella dei criteri di risoluzione dei nomi DNS, che significa che i server DNS non saranno elencati nell'output di `ipconfig /all` . Per confermare le impostazioni DNS in uso, vedere [Get-DnsClientNrptPolicy](/powershell/module/dnsclient/get-dnsclientnrptpolicy?preserve-view=true&view=win10-ps) in PowerShell.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Ricerca per categorie aggiungere route personalizzate al client VPN?

È possibile modificare il file XML del profilo scaricato e aggiungere **\<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes>** i tag

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Ricerca per categorie le route di blocco (escluse) dal client VPN?

È possibile modificare il file XML del profilo scaricato e aggiungere **\<excluderoutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</excluderoutes>** i tag

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>È possibile importare il profilo da un prompt della riga di comando?

È possibile importare il profilo da un prompt della riga di comando inserendo il file di **azurevpnconfig.xml** scaricato nella cartella **%USERPROFILE%\appdata\local\packages\ Microsoft.AzureVpn_8wekyb3d8bbwe \localstate** ed eseguendo il comando seguente:

```
azurevpn -i azurevpnconfig.xml 
```
per forzare l'importazione, usare anche l'opzione **-f**


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [creare un tenant di Azure Active Directory per le connessioni VPN aperte P2S che usano l'autenticazione Azure ad](openvpn-azure-ad-tenant.md).