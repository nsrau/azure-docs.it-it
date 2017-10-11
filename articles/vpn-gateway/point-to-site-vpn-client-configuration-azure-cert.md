---
title: 'Creare e installare i file di configurazione del client VPN da punto a sito per l''autenticazione del certificato di Azure: PowerShell - Azure | Microsoft Docs'
description: Questo articolo illustra come creare e installare i file di configurazione del client VPN per connessioni da punto a sito che usano l'autenticazione del certificato.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 4abfdcc0a50c229555088dff0ac2c00c15f49218
ms.contentlocale: it-it
ms.lasthandoff: 09/28/2017

---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Creare e installare i file di configurazione del client VPN per le configurazioni da punto a sito con autenticazione del certificato nativa di Azure

I file di configurazione del client VPN sono contenuti in un file ZIP. I file di configurazione forniscono le impostazioni necessarie a un client VPN IKEv2 Mac o Windows nativo per connettersi a una rete virtuale con connessioni da punto a sito che usano l'autenticazione del certificato nativa di Azure.

>[!NOTE]
>IKEv2 per P2S è attualmente in fase di anteprima.
>

### <a name="workflow"></a>Flusso di lavoro per la VPN da punto a sito

  1. Configurare il gateway VPN di Azure per una connessione da punto a sito.
  2. Generare il certificato radice e i certificati client. Caricare la chiave pubblica del certificato radice in Azure e installare i certificati client nei dispositivi client. I certificati vengono usati per autenticare gli utenti che eseguono la connessione.
  3. Ottenere la configurazione del client VPN per l'opzione di autenticazione scelta e usarla per configurare il client VPN nei dispositivi Windows e Mac. Sarà così possibile connettersi alle reti virtuali di Azure tramite una connessione da punto a sito.

>[!NOTE]
>I file di configurazione del client sono specifici della configurazione VPN per la rete virtuale. Se vengono apportate modifiche alla configurazione VPN da punto a sito dopo la generazione dei file di configurazione del client VPN, ad esempio al tipo di autenticazione o al tipo di protocollo VPN, è necessario generare nuovi file di configurazione del client VPN per i dispositivi utente.
>
>

## <a name="generate"></a>Generare i file di configurazione del client VPN

Prima di iniziare, assicurarsi che tutti gli utenti che eseguono la connessione abbiano un certificato valido installato nel dispositivo. Per altre informazioni sull'installazione di un certificato client, vedere [Installare un certificato client](point-to-site-how-to-vpn-client-install-azure-cert.md).

È possibile generare i file di configurazione client usando PowerShell oppure il portale di Azure. Entrambi i metodi restituiscono lo stesso file con estensione zip. Decomprimendo il file verranno visualizzate le cartelle seguenti:

  * **WindowsAmd64** e **WindowsX86**, contenenti rispettivamente i pacchetti di installazione a 32 e 64 bit per Windows. Il pacchetto di installazione **WindowsAmd64** è valido per tutti i client Windows a 64 bit, non solo Amd.
  * **Generic**, contenente le informazioni generali usate per creare una propria configurazione del client VPN. Ignorare questa cartella. La cartella Generic è disponibile se nel gateway è stato configurato IKEv2 o SSTP + IKEv2. Se è stato configurato solo SSTP, la cartella Generic non è presente.

### <a name="zipportal"></a>Generare file tramite il portale di Azure

1. Nel portale di Azure passare al gateway di rete virtuale per la rete virtuale a cui ci si vuole connettere.
2. Nella pagina del gateway di rete virtuale fare clic su **Configurazione da punto a sito**.
3. Nella pagina Configurazione da punto a sito fare clic su **Scarica client VPN**. La generazione del pacchetto di configurazione client richiede qualche minuto.
4. Il browser indica che è disponibile un file con estensione zip per la configurazione client, che ha lo stesso nome del gateway. Decomprimere il file per visualizzare le cartelle.

### <a name="zipps"></a>Generare file usando PowerShell

1. Quando si generano file di configurazione del client VPN, il valore di '-AuthenticationMethod' è 'EapTls'. Generare i file di configurazione del client VPN con il comando seguente:

  ```powershell
  $profile=New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

  $profile.VPNProfileSASUrl
  ```
2. Copiare l'URL nel browser per scaricare il file con estensione zip, quindi decomprimere il file per visualizzare le cartelle.

## <a name="installwin"></a>Installare un pacchetto di configurazione del client VPN di Windows

È possibile usare lo stesso pacchetto di configurazione del client VPN in ogni computer client Windows, a condizione che la versione corrisponda all'architettura del client. Per l'elenco dei sistemi operativi client supportati, vedere la sezione relativa alle connessioni da punto a sito di [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md#P2S).

Per configurare il client VPN Windows nativo per l'autenticazione del certificato, usare questa procedura:

1. Selezionare i file di configurazione del client VPN corrispondenti all'architettura del computer Windows. Per un'architettura con processore a 64 bit, scegliere il pacchetto di installazione "VpnClientSetupAmd64". Per un'architettura con processore a 32 bit, scegliere il pacchetto di installazione "VpnClientSetupX86". 
2. Fare doppio clic sul pacchetto per installarlo. Se viene visualizzato un popup SmartScreen, fare clic su **Altre informazioni** e quindi su **Esegui comunque** per installare il pacchetto.
3. Nel computer client passare a **Impostazioni di rete** e fare clic su **VPN**. La connessione VPN viene visualizzata con il nome della rete virtuale a cui si connette. 

## <a name="installmac"></a>Installare una configurazione del client VPN di Mac (OSX)

È necessario creare una configurazione del client VPN separata per ogni dispositivo Mac che si connette a una rete virtuale di Azure. Non è possibile riusare gli stessi file di configurazione per più dispositivi Mac, perché per questi dispositivi è necessario specificare il certificato utente nei file di configurazione del client VPN. La cartella **Generic** contiene tutte le informazioni necessarie per creare una configurazione del client VPN. Se la cartella Generic non viene visualizzata nel download, è probabile che non sia stato selezionato IKEv2 come tipo di tunnel. Dopo la selezione di IKEv2, generare di nuovo il file con estensione zip per recuperare la cartella Generic. La cartella Generic contiene i file seguenti:

* **VpnSettings.xml**, che contiene impostazioni importanti come l'indirizzo del server e il tipo di tunnel. 
* **VpnServerRoot.cer**, che contiene il certificato radice necessario per convalidare il gateway VPN di Azure durante la configurazione della connessione da punto a sito.

Per configurare il client VPN nativo in Mac per l'autenticazione del certificato, usare questa procedura:

1. Importare il certificato radice **VpnServerRoot** nel computer Mac. A tale scopo, copiare il file nel computer Mac e fare doppio clic su di esso.  
Fare clic su **Aggiungi** per eseguire l'importazione.

  ![Aggiungere il certificato](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
2. Aprire la finestra di dialogo **Rete** in **Network Preferences** (Preferenze di rete) e fare clic su **+** per creare un nuovo profilo di connessione del client VPN per una connessione da punto a sito alla rete virtuale di Azure.

  Il valore di **Interface** (Interfaccia) è "VPN" e quello di **Tipo VPN** è "IKEv2". Specificare un nome per il profilo nel campo **Nome servizio** e quindi fare clic su **Crea** per creare il profilo di connessione del client VPN.

  ![Rete](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
3. Nella cartella **Generic** copiare il valore del tag **VpnServer** dal file **VpnSettings.xml**. Incollare tale valore nei campi **Server Address** (Indirizzo server) e **Remote ID** (ID remoto) del profilo.

  ![Informazioni sul server](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
4. Fare clic su **Authentication Settings** (Impostazioni autenticazione) e selezionare **Certificato**. 

  ![Impostazioni di autenticazione](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
5. Fare clic su **Seleziona** per scegliere il certificato da usare per l'autenticazione.

  ![certificato](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
6. In **Choose An Identity** (Scegli identità) viene visualizzato un elenco dei certificati tra cui scegliere. Selezionare il certificato corretto e quindi **Continua**.

  ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
7. Nel campo **Local ID** (ID locale) specificare il nome del certificato (dal passaggio 6). In questo esempio è "ikev2Client.com". Fare quindi clic sul pulsante **Applica** per salvare le modifiche.

  ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
8. Nella finestra di dialogo **Rete** fare clic su **Applica** per salvare tutte le modifiche. Fare quindi clic su **Connect** (Connetti) per avviare la connessione da punto a sito alla rete virtuale di Azure.

## <a name="next-steps"></a>Passaggi successivi

Tornare all'articolo per [completare la configurazione della connessione da punto a sito](vpn-gateway-howto-point-to-site-rm-ps.md).

