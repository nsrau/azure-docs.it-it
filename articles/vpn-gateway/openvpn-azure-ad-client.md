---
title: 'Gateway VPN: client VPN per connessioni P2S del protocollo OpenVPN: autenticazione di Azure AD'
description: È possibile usare la VPN P2S per connettersi alla rete virtuale usando l'autenticazione di Azure ADYou can use P2S VPN to connect to your VNet using Azure AD authentication
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: alzam
ms.openlocfilehash: 7bc28a03476e773325d14808e1c7ac99103b2d5d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879446"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Configurare un client VPN per le connessioni del protocollo OpenVPN P2S: autenticazione di Azure ADConfigure a VPN client for P2S OpenVPN protocol connections: Azure AD authentication

Questo articolo consente di configurare un client VPN per la connessione a una rete virtuale usando la VPN da punto a sito e l'autenticazione di Azure Active Directory.This article helps you configure a VPN client to connect to a virtual network using Point-to-Site VPN and Azure Active Directory authentication. Prima di potersi connettere e autenticare usando Azure AD, è necessario configurare il tenant di Azure AD. Per altre informazioni, vedere [Configurare un tenant](openvpn-azure-ad-tenant.md)di Azure AD.

> [!NOTE]
> L'autenticazione di Azure AD è supportata solo per le connessioni tramite il protocollo OpenVPN®.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Utilizzo dei profili client

Per connettersi, è necessario scaricare il client VPN di Azure e configurare un profilo client VPN in ogni computer che desidera connettersi alla rete virtuale. È possibile creare un profilo client in un computer, esportarlo e quindi importarlo in altri computer.

### <a name="to-download-the-azure-vpn-client"></a>Per scaricare il client VPN di Azure

Usare questo [collegamento](https://go.microsoft.com/fwlink/?linkid=2117554) per scaricare il client VPN di Azure. Assicurarsi che il client VPN di Azure disponga dell'autorizzazione per l'esecuzione in background. Per controllare/abilitare l'autorizzazione, attenersi alla seguente procedura:

1. Vai a Start , quindi seleziona Impostazioni > Privacy > App in background.
2. In App in background verificare che **l'opzione Consenti l'esecuzione** delle app in background sia attivata.
3. In Scegliere le app che possono essere eseguite in background attivare le impostazioni per Client VPN di **Azure.**

  ![autorizzazione](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Per creare un profilo client basato su certificatiTo create a certificate-based client profile

Quando si utilizza un profilo basato su certificati, assicurarsi che i certificati appropriati siano installati nel computer client. Per ulteriori informazioni sui certificati, vedere [Installare certificati client](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Per creare un profilo client RADIUS

  ![radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Il segreto del server può essere esportato nel profilo client VPN P2S.  Le istruzioni su come esportare un profilo cliente sono disponibili [qui](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Per esportare e distribuire un profilo client

Dopo aver utilizzato un profilo di lavoro e averlo distribuito ad altri utenti, è possibile esportarlo attenendosi alla seguente procedura:

1. Evidenziare il profilo client VPN che si desidera esportare, selezionare il **...**, quindi selezionare **Esporta**.

    ![esportare](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Selezionare il percorso in cui si desidera salvare il profilo, lasciare il nome del file così com'è, quindi selezionare **Salva** per salvare il file xml.

    ![esportare](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Per importare un profilo client

1. Nella pagina selezionare **Importa**.

    ![import](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Individuare il file XML del profilo e selezionarlo. Con il file selezionato, selezionare **Apri**.

    ![import](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Specificare il nome del profilo e selezionare **Salva**.

    ![import](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Selezionare **Connetti** per connettersi alla VPN.

    ![import](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Una volta stabilita la connessione, l'icona diventerà verde mostrerà lo stato **Connesso**.

    ![import](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Per eliminare un profilo client

1. Selezionare i puntini di sospensione accanto al profilo client che si desidera eliminare. Selezionare quindi **Rimuovi**.

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Per procedere all'eliminazione, selezionare **Rimuovi**.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Creare una connessione

1. Nella pagina, **+** selezionare , quindi **Aggiungi .**

    ![connessione](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Compilare le informazioni di connessione. Se non si è sicuri dei valori, contattare l'amministratore. Dopo aver compilato i valori, selezionare **Salva**.

    ![connessione](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Selezionare **Connetti** per connettersi alla VPN.

    ![connessione](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Selezionare le credenziali corrette, quindi selezionare **Continua**.

    ![connessione](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Una volta stabilita la connessione, l'icona diventerà verde e dirà **Connesso**.

    ![connessione](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Per connettersi automaticamente

Questi passaggi consentono di configurare la connessione per la connessione automatica con Always-on.

1. Nella home page del client VPN selezionare **Impostazioni VPN**.

    ![auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Selezionare **Sì** nella finestra di dialogo Cambia app.

    ![auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Assicurarsi che la connessione che si desidera impostare non sia già connessa, quindi evidenziare il profilo e selezionare la casella di controllo **Connetti automaticamente.**

    ![auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Selezionare **Connetti** per avviare la connessione VPN.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnosticare i problemi di connessione

1. Per diagnosticare i problemi di connessione, è possibile usare lo strumento **Diagnosi**. Selezionare il **...** accanto alla connessione VPN che si desidera diagnosticare per visualizzare il menu. Selezionare quindi **Diagnosi**.

    ![diagnosi](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Nella pagina **Proprietà connessione** selezionare **Esegui diagnosi**.

    ![diagnosi](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Accedere con le credenziali personali.

    ![diagnosi](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Visualizzare i risultati della diagnosi.

    ![diagnosi](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Domande frequenti

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Come si aggiungono suffissi DNS al client VPN?

È possibile modificare il file XML del profilo scaricato e aggiungere ** \<i dnssuffixes>\<dnssufix \<> /dnssufix>\<tag>dnssuffixes**

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

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Come si aggiungono server DNS personalizzati al client VPN?

È possibile modificare il file XML del profilo scaricato e aggiungere i ** \<tag dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** tag

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
> Il client OpenVPN Azure AD utilizza le voci della tabella dei criteri di risoluzione dei nomi `ipconfig /all`DNS (NRPT), il che significa che i server DNS non verranno elencati nell'output di . Per verificare le impostazioni DNS in uso, consultare [Get-DnsClientNrptPolicy](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) in PowerShell.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Come si aggiungono route personalizzate al client VPN?

È possibile modificare il file XML del profilo scaricato e aggiungere la ** \<route \<includeroutes \<>\<>\<> maschera di destinazione>\<> \</destination>mask>\</route>/includeroutes>** tag

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

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Come si bloccano (escludono) le route dal client VPN?

È possibile modificare il file XML del profilo scaricato e aggiungere il ** \<percorso excluderoutes>\<route>\<la maschera \< \<di destinazione>> destinazione>\</mask>\</route>\</excluderoutes>** tag

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

### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>È possibile importare il profilo da un prompt dei comandi?

È possibile importare il profilo da un prompt dei comandi inserendo il file **azurevpnconfig.xml** scaricato nella cartella **%userprofile%'AppData'Local'Packages'Microsoft.AzureVpn_8wekyb3d8bbwe'LocalState** ed eseguendo il comando seguente:

```
azurevpn -i azurevpnconfig.xml 
```
per forzare l'importazione utilizzare anche l'opzione **-f**


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere Creare un tenant di [Azure Active Directory per le connessioni P2S Open VPN che usano l'autenticazione](openvpn-azure-ad-tenant.md)di Azure AD.
