---
title: 'Gateway VPN: client VPN per le connessioni P2S del protocollo OpenVPN: autenticazione Azure AD'
description: È possibile usare la VPN P2S per connettersi alla VNet usando l'autenticazione Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: alzam
ms.openlocfilehash: 4b9678f72dd69db24b105d4b1d708928e29a09ba
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134507"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Configurare un client VPN per le connessioni del protocollo OpenVPN P2S: autenticazione Azure AD

Questo articolo illustra come configurare un client VPN per connettersi a una rete virtuale usando la VPN da punto a sito e l'autenticazione Azure Active Directory. Prima di poter connettersi ed eseguire l'autenticazione con Azure AD, è necessario innanzitutto configurare il tenant di Azure AD. Per altre informazioni, vedere [configurare un tenant di Azure ad](openvpn-azure-ad-tenant.md).

> [!NOTE]
> L'autenticazione di Azure AD è supportata solo per le connessioni tramite il protocollo OpenVPN®.
>

## <a name="profile"></a>Utilizzo dei profili client

Per connettersi, è necessario scaricare il client VPN di Azure e configurare un profilo client VPN in ogni computer che desidera connettersi a VNet. È possibile creare un profilo client in un computer, esportarlo e quindi importarlo in altri computer.

### <a name="to-download-the-azure-vpn-client"></a>Per scaricare il client VPN di Azure

Usare questo [collegamento](https://go.microsoft.com/fwlink/?linkid=2117554) per scaricare il client VPN di Azure.

### <a name="cert"></a>Per creare un profilo client basato su certificati

Quando si utilizza un profilo basato su certificato, verificare che nel computer client siano installati i certificati appropriati. Per ulteriori informazioni sui certificati, vedere [Install Client Certificates](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="radius"></a>Per creare un profilo client RADIUS

  ![radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Il segreto server può essere esportato nel profilo client VPN P2S.  Le istruzioni su come esportare un profilo client sono disponibili [qui](about-vpn-profile-download.md).
>

### <a name="export"></a>Per esportare e distribuire un profilo client

Quando si dispone di un profilo di lavoro ed è necessario distribuirlo ad altri utenti, è possibile esportarlo attenendosi alla procedura seguente:

1. Evidenziare il profilo client VPN che si vuole esportare, selezionare il **...** e quindi selezionare **Esporta**.

    ![export](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Selezionare il percorso in cui si desidera salvare il profilo, lasciare il nome del file così com'è, quindi selezionare **Salva** per salvare il file XML.

    ![export](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="import"></a>Per importare un profilo client

1. Nella pagina selezionare **Importa**.

    ![importare](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Individuare il file XML del profilo e selezionarlo. Con il file selezionato, selezionare **Apri**.

    ![importare](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Specificare il nome del profilo e selezionare **Salva**.

    ![importare](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Selezionare **Connetti** per connettersi alla VPN.

    ![importare](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Una volta stabilita la connessione, l'icona diventerà verde mostrerà lo stato **Connesso**.

    ![importare](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="delete"></a>Per eliminare un profilo client

1. Selezionare i puntini di sospensione accanto al profilo client che si desidera eliminare. Selezionare quindi **Rimuovi**.

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Per procedere all'eliminazione, selezionare **Rimuovi**.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="connection"></a>Creare una connessione

1. Nella pagina selezionare **+** , quindi **+ Aggiungi**.

    ![connection](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Inserire le informazioni di connessione. Se non si è certi dei valori, contattare l'amministratore. Dopo aver compilato i valori, selezionare **Salva**.

    ![connection](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Selezionare **Connetti** per connettersi alla VPN.

    ![connection](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Selezionare le credenziali appropriate e quindi fare clic su **continua**.

    ![connection](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Una volta stabilita la connessione, l'icona diventerà verde e si **disconnetterà**.

    ![connection](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="autoconnect"></a>Per connettersi automaticamente

Questi passaggi consentono di configurare la connessione per la connessione automatica con always-on.

1. Nella home page per il client VPN selezionare **Impostazioni VPN**.

    ![auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Selezionare **Sì** nella finestra di dialogo Cambia app.

    ![auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Verificare che la connessione che si vuole impostare non sia già connessa, quindi evidenziare il profilo e selezionare la casella di controllo **Connetti automaticamente** .

    ![auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Selezionare **Connetti** per avviare la connessione VPN.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose"></a>Diagnosticare i problemi di connessione

1. Per diagnosticare i problemi di connessione, è possibile usare lo strumento **Diagnosi**. Selezionare il **...** accanto alla connessione VPN che si desidera diagnosticare per visualizzare il menu. Selezionare quindi **Diagnosi**.

    ![diagnosi](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Nella pagina **Proprietà connessione** selezionare **Esegui diagnosi**.

    ![diagnosi](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Accedere con le credenziali personali.

    ![diagnosi](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Visualizzare i risultati della diagnosi.

    ![diagnosi](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Domande frequenti

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Ricerca per categorie aggiungere suffissi DNS al client VPN?

È possibile modificare il file XML del profilo scaricato e aggiungere il **\<dnssuffixes >\<dnssufix > \</dnssufix >\</dnssuffixes** > Tag

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

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Ricerca per categorie aggiungere route personalizzate al client VPN?

È possibile modificare il file XML del profilo scaricato e aggiungere la **route\<>\<includeroutes >\<destinazione >\<mask > \</route >\</includeroutes >\</Destination** >\</Mask > Tag

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

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [creare un tenant di Azure Active Directory per le connessioni VPN aperte P2S che usano l'autenticazione Azure ad](openvpn-azure-ad-tenant.md).
