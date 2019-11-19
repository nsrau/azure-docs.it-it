---
title: 'Creare & installare i file di configurazione del client VPN P2S: autenticazione del certificato'
titleSuffix: Azure VPN Gateway
description: Creare e installare i file di configurazione del client VPN in Windows, Linux, Linux (strongSwan) e Mac OS X per l'autenticazione del certificato da punto a sito.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/12/2019
ms.author: cherylmc
ms.openlocfilehash: ad6e912f976095ae7d8fd5276b0f1365566c181a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143790"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Creare e installare i file di configurazione del client VPN per le configurazioni da punto a sito con autenticazione del certificato nativa di Azure

I file di configurazione del client VPN sono contenuti in un file ZIP. I file di configurazione forniscono le impostazioni necessarie a un client Linux, VPN IKEv2 Mac o Windows nativo per connettersi a una rete virtuale con connessioni da punto a sito che usano l'autenticazione del certificato nativa di Azure.

I file di configurazione del client sono specifici della configurazione VPN per la rete virtuale. Se vengono apportate modifiche alla configurazione VPN da punto a sito dopo la generazione dei file di configurazione del client VPN, ad esempio al tipo di autenticazione o al tipo di protocollo VPN, è necessario generare nuovi file di configurazione del client VPN per i dispositivi utente. 

* Per altre informazioni sulle connessioni da punto a sito, vedere [About Point-to-Site VPN](point-to-site-about.md) (Informazioni sulla VPN da punto a sito).
* Per le istruzioni su OpenVPN, vedere [Configurare OpenVPN per connessioni da punto a sito](vpn-gateway-howto-openvpn.md) e [Configurare client OpenVPN](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate"></a>Generare i file di configurazione del client VPN

Prima di iniziare, assicurarsi che tutti gli utenti che eseguono la connessione abbiano un certificato valido installato nel dispositivo. Per altre informazioni sull'installazione di un certificato client, vedere [Installare un certificato client](point-to-site-how-to-vpn-client-install-azure-cert.md).

È possibile generare i file di configurazione client usando PowerShell oppure il portale di Azure. Entrambi i metodi restituiscono lo stesso file con estensione zip. Decomprimendo il file verranno visualizzate le cartelle seguenti:

  * **WindowsAmd64** e **WindowsX86**, contenenti rispettivamente i pacchetti di installazione a 32 e 64 bit per Windows. Il pacchetto di installazione **WindowsAmd64** è valido per tutti i client Windows a 64 bit, non solo Amd.
  * **Generic**, contenente le informazioni generali usate per creare una propria configurazione del client VPN. La cartella Generic è disponibile se nel gateway è stato configurato IKEv2 o SSTP + IKEv2. Se è stato configurato solo SSTP, la cartella Generic non è presente.

### <a name="zipportal"></a>Generare file tramite il portale di Azure

1. Nel portale di Azure passare al gateway di rete virtuale per la rete virtuale a cui ci si vuole connettere.
2. Nella pagina del gateway di rete virtuale fare clic su **Configurazione da punto a sito**.
3. Nella pagina Configurazione da punto a sito fare clic su **Scarica client VPN**. La generazione del pacchetto di configurazione client richiede qualche minuto.
4. Il browser indica che è disponibile un file con estensione zip per la configurazione client, che ha lo stesso nome del gateway. Decomprimere il file per visualizzare le cartelle.

### <a name="zipps"></a>Generare file usando PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Quando si generano file di configurazione del client VPN, il valore di '-AuthenticationMethod' è 'EapTls'. Generare i file di configurazione del client VPN con il comando seguente:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Copiare l'URL nel browser per scaricare il file con estensione zip, quindi decomprimere il file per visualizzare le cartelle.

## <a name="installwin"></a>Windows

È possibile usare lo stesso pacchetto di configurazione del client VPN in ogni computer client Windows, a condizione che la versione corrisponda all'architettura del client. Per l'elenco dei sistemi operativi client supportati, vedere la sezione relativa alle connessioni da punto a sito di [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>È necessario avere diritti di amministratore per il computer client Windows da cui ci si vuole connettere.
>
>

Per configurare il client VPN Windows nativo per l'autenticazione del certificato, usare questa procedura:

1. Selezionare i file di configurazione del client VPN corrispondenti all'architettura del computer Windows. Per un'architettura con processore a 64 bit, scegliere il pacchetto di installazione "VpnClientSetupAmd64". Per un'architettura con processore a 32 bit, scegliere il pacchetto di installazione "VpnClientSetupX86". 
2. Fare doppio clic sul pacchetto per installarlo. Se viene visualizzato un popup SmartScreen, fare clic su **Altre informazioni** e quindi su **Esegui comunque** per installare il pacchetto.
3. Nel computer client passare a **Impostazioni di rete** e fare clic su **VPN**. La connessione VPN viene visualizzata con il nome della rete virtuale a cui si connette. 
4. Prima di tentare di connettersi, verificare che nel computer client sia installato un certificato client. Quando si usa il tipo di autenticazione del certificato di Azure nativo, è necessario un certificato client per l'autenticazione. Per altre informazioni sulla generazione di certificati, vedere [Generare i certificati](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Per informazioni sull'installazione di un certificato client, vedere [Installare un certificato client](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>Mac (OS X)

 È necessario configurare manualmente il client VPN IKEv2 nativo in ogni Mac che si connetterà ad Azure. Azure non fornisce il file mobileconfig per l'autenticazione del certificato di Azure nativo. **Generic** contiene tutte le informazioni che occorrono per la configurazione. Se la cartella Generic non viene visualizzata nel download, è probabile che non sia stato selezionato IKEv2 come tipo di tunnel. Si noti che lo SKU Basic del gateway VPN non supporta IKEv2. Dopo la selezione di IKEv2, generare di nuovo il file con estensione zip per recuperare la cartella Generic.<br>La cartella Generic contiene i file seguenti:

* **VpnSettings.xml**, che contiene impostazioni importanti come l'indirizzo del server e il tipo di tunnel. 
* **VpnServerRoot.cer**, che contiene il certificato radice necessario per convalidare il gateway VPN di Azure durante la configurazione della connessione da punto a sito.

Usare questa procedura per configurare il client VPN nativo in Mac per l'autenticazione del certificato. È necessario eseguire questi passaggi in ogni Mac che si connetterà ad Azure:

1. Importare il certificato radice **VpnServerRoot** nel computer Mac. A tale scopo, copiare il file nel computer Mac e fare doppio clic su di esso. Fare clic su **Aggiungi** per eseguire l'importazione.

   ![Aggiungere il certificato](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Facendo doppio clic sul certificato la finestra di dialogo **Aggiungi** potrebbe non essere visualizzata, ma il certificato è installato nell'archivio corretto. È possibile cercare il certificato nel portachiavi di login sotto la categoria dei certificati.
    >
  
2. Verificare di aver installato un certificato client rilasciato dal certificato radice caricato in Azure durante la configurazione delle impostazioni da punto a sito. È diverso dal certificato VPNServerRoot installato nel passaggio precedente. Il certificato client viene usato per l'autenticazione ed è necessario. Per altre informazioni sulla generazione di certificati, vedere [Generare i certificati](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Per informazioni sull'installazione di un certificato client, vedere [Installare un certificato client](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Aprire la finestra di dialogo **Rete** in **Network Preferences** (Preferenze di rete) e fare clic su **+** per creare un nuovo profilo di connessione del client VPN per una connessione da punto a sito alla rete virtuale di Azure.

   Il valore di **Interface** (Interfaccia) è "VPN" e quello di **Tipo VPN** è "IKEv2". Specificare un nome per il profilo nel campo **Nome servizio** e quindi fare clic su **Crea** per creare il profilo di connessione del client VPN.

   ![Rete](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. Nella cartella **Generic** copiare il valore del tag **VpnServer** dal file **VpnSettings.xml**. Incollare tale valore nei campi **Server Address** (Indirizzo server) e **Remote ID** (ID remoto) del profilo.

   ![Informazioni sul server](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Fare clic su **Authentication Settings** (Impostazioni autenticazione) e selezionare **Certificato**. 

   ![Impostazioni di autenticazione](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. Fare clic su **Seleziona** per scegliere il certificato client da usare per l'autenticazione. Questo è il certificato installato nel passaggio 2.

   ![certificato](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. In **Choose An Identity** (Scegli identità) viene visualizzato un elenco dei certificati tra cui scegliere. Selezionare il certificato corretto e quindi **Continua**.

   ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. Nel campo **Local ID** (ID locale) specificare il nome del certificato (dal passaggio 6). In questo esempio è "ikev2Client.com". Fare quindi clic sul pulsante **Applica** per salvare le modifiche.

   ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. Nella finestra di dialogo **Rete** fare clic su **Applica** per salvare tutte le modifiche. Fare quindi clic su **Connect** (Connetti) per avviare la connessione da punto a sito alla rete virtuale di Azure.

## <a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="installstrongswan"></a>Installare strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="genlinuxcerts"></a>Genera certificati

Se non sono ancora stati generati certificati, attenersi alla procedura seguente:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install"></a>Installare e configurare

In Ubuntu 18.0.4 sono state create le istruzioni seguenti. Ubuntu 16.0.10 non supporta l'interfaccia utente grafica strongSwan. Se si intende comunque usare Ubuntu 16.0.10, sarà quindi necessario ricorrere alla [riga di comando](#linuxinstallcli). A seconda delle versioni di Linux e strongSwan in uso, è possibile che le schermate riportate negli esempi seguenti non corrispondano a quelle effettivamente visualizzate.

1. Aprire il **Terminale** per installare **strongSwan** e il relativo gestore di rete eseguendo il comando riportato nell'esempio.

   ```
   sudo apt install network-manager-strongswan
   ```
2. Selezionare **Impostazioni** , quindi fare clic su **rete**.

   ![Modificare le connessioni](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Fare clic sul pulsante **+** per creare una nuova connessione.

   ![Aggiungere una connessione](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Selezionare **IPSec/IKEv2 (strongSwan)** dal menu e fare doppio clic su. In questo passaggio è possibile assegnare un nome alla connessione.

   ![Scegliere un tipo di connessione](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Aprire il file **VpnSettings.xml** dalla cartella **Generic** (Generale) contenuta nei file di configurazione client scaricati. Trovare il tag denominato **VpnServer** e copiare il nome, che inizia con "azuregateway" e termina con ".cloudapp.net".

   ![Copiare il nome](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Incollare il nome nel campo **Address** (Indirizzo) della nuova connessione VPN nella sezione **Gateway**. Successivamente, selezionare l'icona della cartella alla fine del campo **Certificate** (Certificato), passare alla cartella **Generic** e selezionare il file **VpnServerRoot**.
7. Nella sezione **Client** della connessione, per **Authentication** (Autenticazione) selezionare **Certificate/private key** (Certificato/Chiave privata). Per **Certificate** (Certificato) e **Private key** (Chiave privata) scegliere il certificato e la chiave privata creati in precedenza. In **Options** (Opzioni) selezionare **Request an inner IP address** (Richiedi un indirizzo IP interno). Fare quindi clic su **Aggiungi**.

   ![Richiedere un indirizzo IP interno](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. **Attivare la connessione.**

## <a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>Installare strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Generare i certificati

Se non sono ancora stati generati certificati, attenersi alla procedura seguente:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Installare e configurare

1. Scaricare il pacchetto VPNClient dal portale di Azure.
2. Estrarre il file.
3. Copiare o spostare dalla cartella **Generic** VpnServerRoot.cer to /etc/ipsec.d/cacerts.
4. Copiare o spostare cp client.p12 in /etc/ipsec.d/private/. Questo file è il certificato client per Gateway VPN di Azure.
5. Aprire file Vpnsettings XML e copiare il valore `<VpnServer>`. Questo valore verrà usato nel passaggio successivo.
6. Modificare i valori nell'esempio seguente, quindi aggiungere l'esempio alla configurazione /etc/ipsec.conf.
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
6. Aggiungere quanto segue a */etc/ipsec.secrets*.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Eseguire i comandi seguenti:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Passaggi successivi

Tornare all'articolo per [completare la configurazione della connessione da punto a sito](vpn-gateway-howto-point-to-site-rm-ps.md).

Per risolvere i problemi relativi alle connessioni da punto a sito, vedere gli articoli seguenti:

  * [Risoluzione dei problemi di connessione da punto a sito di Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Risolvere i problemi di connessione VPN dai client Mac OS X](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
