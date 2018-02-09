---
title: 'Creare e installare i file di configurazione del client VPN per connessioni RADIUS da punto a sito: PowerShell - Azure | Microsoft Docs'
description: Questo articolo illustra come creare il file di configurazione del client VPN per connessioni da punto a sito che usano l'autenticazione RADIUS.
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
ms.date: 01/24/2018
ms.author: cherylmc
ms.openlocfilehash: 838065287279f1c17e7f294bc919c4a0421e2a58
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Creare e installare i file di configurazione del client VPN per l'autenticazione RADIUS da punto a sito

I file di configurazione del client VPN sono contenuti in un file ZIP. I file di configurazione forniscono le impostazioni necessarie a un client VPN IKEv2 Mac o Windows nativo per connettersi a una rete virtuale con una connessione da punto a sito. Il server RADIUS offre più opzioni di autenticazione e la configurazione del client VPN varia per ogni singola opzione.

### <a name="workflow"></a>Flusso di lavoro

  1. [Configurare il gateway VPN di Azure per la connettività da punto a sito](point-to-site-how-to-radius-ps.md).
  2. [Configurare il server RADIUS per l'autenticazione](point-to-site-how-to-radius-ps.md#radius). 
  3. (Questo articolo): ottenere la configurazione del client VPN per l'opzione di autenticazione scelta e usarla per configurare il client VPN nel dispositivo Windows. Sarà così possibile connettersi alle reti virtuali di Azure tramite una connessione da punto a sito.
  4. [Completare la configurazione da punto a sito e connettersi](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Se vengono apportate modifiche alla configurazione VPN da punto a sito, ad esempio al tipo di autenticazione o al tipo di protocollo VPN, dopo la generazione del profilo di configurazione del client VPN, è necessario generare e installare una nuova configurazione del client VPN nei dispositivi utente.
>
>

## <a name="adeap"></a>Informazioni sull'autenticazione con nome utente/password

* **Autenticazione di AD:** uno scenario comune è l'autenticazione del dominio di AD. In questo scenario, gli utenti si connettono alle reti virtuali di Azure usando le credenziali del dominio. È possibile creare i file di configurazione del client VPN per l'autenticazione RADIUS di AD.

* **Autenticazione senza AD:** è anche possibile configurare lo scenario di autenticazione RADIUS con nome utente/password senza AD.

Verificare che tutti gli utenti che effettuano la connessione abbiano credenziali nome utente/password autenticabili tramite RADIUS. È possibile creare una configurazione solo per il protocollo di autenticazione con nome utente/password EAP-MSCHAPv2. Come "-AuthenticationMethod" viene specificato "EapMSChapv2".

## <a name="usernamefiles"></a> 1. Generare i file di configurazione del client VPN

Generare i file di configurazione del client VPN per l'uso con l'autenticazione con nome utente/password. È possibile generare i file di configurazione del client VPN con il comando seguente:

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
L'esecuzione del comando restituisce un collegamento. Copiare e incollare il collegamento in un Web browser per scaricare 'VpnClientConfiguration.zip'. Decomprimendo il file verranno visualizzate le cartelle seguenti: 
 
* **WindowsAmd64** e **WindowsX86**: queste cartelle contengono il pacchetto del programma di installazione di Windows, rispettivamente a 64 bit e a 32 bit. 
* **Generic**: questa cartella contiene le informazioni generali usate per creare una configurazione del client VPN personalizzata. Questa cartella non è necessaria per le configurazioni di autenticazione con nome utente/password.
* **Mac**: se durante la creazione del gateway di rete virtuale è stato configurato IKEv2, è presente una cartella denominata 'Mac' contenente un file denominato **mobileconfig**. Questo file viene usato per configurare i client Mac.

Se sono già stati creati file di configurazione del client, è possibile recuperarli tramite il cmdlet 'Get-AzureRmVpnClientConfiguration'. Se tuttavia si apportano modifiche alla configurazione VPN da punto a sito, ad esempio al tipo di autenticazione o al tipo di protocollo VPN, la configurazione non viene aggiornata automaticamente. Per creare un nuovo download della configurazione, è necessario eseguire il cmdlet 'New-AzureRmVpnClientConfiguration'.

Per recuperare i file di configurazione client generati in precedenza, usare il comando seguente:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

## <a name="setupusername"></a> 2. Configurare client VPN Windows e Mac
 
### <a name="adwincli"></a>Configurazione di un client VPN Windows

È possibile usare lo stesso pacchetto di configurazione del client VPN in ogni computer client Windows, a condizione che la versione corrisponda all'architettura del client. Per l'elenco dei sistemi operativi client supportati, vedere la sezione Connessioni da punto a sito delle [domande frequenti](vpn-gateway-vpn-faq.md#P2S).

Per configurare il client VPN Windows nativo per l'autenticazione del certificato, usare questa procedura:

1. Selezionare i file di configurazione del client VPN corrispondenti all'architettura del computer Windows. Per un'architettura con processore a 64 bit, scegliere il pacchetto di installazione "VpnClientSetupAmd64". Per un'architettura con processore a 32 bit, scegliere il pacchetto di installazione "VpnClientSetupX86". 
2. Fare doppio clic sul pacchetto per installarlo. Se viene visualizzato un popup SmartScreen, fare clic su **Altre informazioni** e quindi su **Esegui comunque** per installare il pacchetto.
3. Nel computer client passare a **Impostazioni di rete** e fare clic su **VPN**. La connessione VPN viene visualizzata con il nome della rete virtuale a cui si connette. 

### <a name="admaccli"></a>Configurazione di un client VPN Mac (OSX)

1. Selezionare il file **VpnClientSetup mobileconfig** e inviarlo a ogni utente, tramite posta elettronica o un altro metodo.

2. Individuare il file **mobileconfig** nel computer Mac.

  ![Individuare il file mobilconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. Fare doppio clic sul profilo per installarlo e fare clic su **Continua**. Il nome del profilo corrisponde al nome della rete virtuale.

  ![Installazione](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. Fare clic su **Continua** per considerare attendibile il mittente del profilo e procedere con l'installazione.

  ![Continua](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. Durante l'installazione del profilo, viene offerta la possibilità di specificare il nome utente e la password usati per l'autenticazione VPN. Non è obbligatorio immettere queste informazioni. Se specificate, le informazioni vengono salvate e usate automaticamente quando si avvia una connessione. Fare clic su **Installa** per continuare.

  ![Scheda Impostazioni](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. Immettere un nome utente e una password per ottenere i privilegi necessari per installare il profilo nel computer. Fare clic su **OK**.

  ![Nome utente e password](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. Al termine dell'installazione, il profilo è visibile nella finestra di dialogo **Profili**. È possibile aprire questa finestra di dialogo anche in un secondo momento da **Preferenze di Sistema**.

  ![preferenze di sistema](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. Per accedere alla connessione VPN, aprire la finestra di dialogo **Network** da **Preferenze di Sistema**.

  ![Rete](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. La connessione VPN viene visualizzata come **IkeV2-VPN**. Il nome può essere modificato aggiornando il file **mobileconfig**.

  ![connessione](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. Fare clic su **Authentication Settings**. Selezionare **Nome utente** nell'elenco a discesa e immettere le proprie credenziali. Se si sono immesse in precedenza, l'opzione **Nome utente** è selezionata automaticamente nell'elenco a discesa e il nome utente e la password sono già inseriti. Fare clic su **OK** per salvare le impostazioni. Verrà così visualizzata di nuovo la finestra di dialogo Network.

  ![authenticate](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. Fare clic su **Applica** per salvare le modifiche. Per avviare la connessione, fare clic su **Connetti**.

## <a name="certeap"></a>Informazioni sull'autenticazione con certificato
 
È possibile creare i file di configurazione del client VPN per l'autenticazione del certificato RADIUS con il protocollo EAP-TLS. Per l'autenticazione di un utente per una VPN viene in genere usato un certificato rilasciato dalla CA globale (enterprise). Verificare che nel dispositivo di tutti gli utenti che effettuano la connessione sia installato un certificato e che questo possa essere convalidato dal server RADIUS.
 
* "-AuthenticationMethod" è "EapTls".
* Durante l'autenticazione del certificato, il client convalida il server RADIUS convalidando il certificato. "-RadiusRootCert" è il file CER contenente il certificato radice usato per convalidare il server RADIUS.  
* Un dispositivo Windows ha talvolta più certificati client. Durante l'autenticazione può pertanto essere visualizzata una finestra di dialogo popup con un elenco di tutti i certificati. L'utente deve quindi scegliere il certificato da usare. È possibile filtrare il certificato corretto specificando il certificato radice a cui dovrebbe essere concatenato il certificato client. "-ClientRootCert" è il file CER contenente il certificato radice ed è un parametro facoltativo. Se il dispositivo da cui si vuole effettuare la connessione ha un solo certificato client, non è necessario specificare questo parametro.

## <a name="certfiles"></a>1. Generare i file di configurazione del client VPN

Generare i file di configurazione del client VPN per l'uso con l'autenticazione con certificato. È possibile generare i file di configurazione del client VPN con il comando seguente:
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

L'esecuzione del comando restituisce un collegamento. Copiare e incollare il collegamento in un Web browser per scaricare 'VpnClientConfiguration.zip'. Decomprimendo il file verranno visualizzate le cartelle seguenti:

* **WindowsAmd64** e **WindowsX86**: queste cartelle contengono il pacchetto del programma di installazione di Windows, rispettivamente a 64 bit e a 32 bit. 
* **GenericDevice**: questa cartella contiene le informazioni generali usate per creare una configurazione del client VPN personalizzata.

Se sono già stati creati file di configurazione del client, è possibile recuperarli tramite il cmdlet 'Get-AzureRmVpnClientConfiguration'. Se tuttavia si apportano modifiche alla configurazione VPN da punto a sito, ad esempio al tipo di autenticazione o al tipo di protocollo VPN, la configurazione non viene aggiornata automaticamente. Per creare un nuovo download della configurazione, è necessario eseguire il cmdlet 'New-AzureRmVpnClientConfiguration'.

Per recuperare i file di configurazione client generati in precedenza, usare il comando seguente:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
## <a name="setupusername"></a> 2. Configurare client VPN Windows e Mac

### <a name="certwincli"></a>Configurazione di un client VPN Windows

1. Selezionare un pacchetto di configurazione e installarlo nel dispositivo client. Per un'architettura con processore a 64 bit, scegliere il pacchetto di installazione "VpnClientSetupAmd64". Per un'architettura con processore a 32 bit, scegliere il pacchetto di installazione "VpnClientSetupX86". Se viene visualizzato un popup SmartScreen, fare clic su **Altre informazioni** e quindi su **Esegui comunque** per installare il pacchetto. È anche possibile salvare il pacchetto per l'installazione su altri computer client.
2. Nel computer client passare a **Impostazioni di rete** e fare clic su **VPN**. La connessione VPN viene visualizzata con il nome della rete virtuale a cui si connette.

### <a name="certmaccli"></a>Configurazione di un client VPN Mac (OSX)

È necessario creare un profilo separato per ogni dispositivo Mac che si connette alla rete virtuale di Azure. Per questi dispositivi è infatti necessario che il certificato utente per l'autenticazione sia specificato nel profilo. La cartella **Generic** contiene tutte le informazioni necessarie per creare un profilo.

  * **VpnSettings.xml** contiene informazioni importanti come l'indirizzo del server e il tipo di tunnel.
  * **VpnServerRoot.cer** contiene il certificato radice necessario per convalidare il gateway VPN durante la configurazione della connessione da punto a sito.
  * **RadiusServerRoot.cer** contiene il certificato radice necessario per convalidare il server RADIUS durante l'autenticazione.

Per configurare il client VPN nativo in Mac per l'autenticazione del certificato, usare questa procedura:

1. Importare i certificati radice **VpnServerRoot** e **RadiusServerRoot** nel computer Mac. A tale scopo, copiare il file nel computer Mac e fare doppio clic su di esso.  
Fare clic su **Aggiungi** per eseguire l'importazione.

  **Aggiungere VpnServerRoot**

  ![Aggiungere il certificato](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

  **Aggiungere RadiusServerRoot**

  ![Aggiungere il certificato](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Aprire la finestra di dialogo **Rete** in **Network Preferences** (Preferenze di rete) e fare clic su **+** per creare un nuovo profilo di connessione del client VPN per una connessione da punto a sito alla rete virtuale di Azure.

  Il valore di **Interface** (Interfaccia) è "VPN" e quello di **Tipo VPN** è "IKEv2". Specificare un nome per il profilo nel campo **Nome servizio** e quindi fare clic su **Crea** per creare il profilo di connessione del client VPN.

  ![Rete](./media/point-to-site-vpn-client-configuration-radius/network.png)
3. Nella cartella **Generic** copiare il valore del tag **VpnServer** dal file **VpnSettings.xml**. Incollare tale valore nei campi **Server Address** (Indirizzo server) e **Remote ID** (ID remoto) del profilo. Lasciare vuoto il campo **Local ID** (ID locale).

  ![Informazioni sul server](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
4. Fare clic su **Authentication Settings** (Impostazioni autenticazione) e selezionare **Certificato**. 

  ![Impostazioni di autenticazione](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
5. Fare clic su **Seleziona** per scegliere il certificato da usare per l'autenticazione.

  ![certificato](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
6. In **Choose An Identity** (Scegli identità) viene visualizzato un elenco dei certificati tra cui scegliere. Selezionare il certificato corretto e quindi **Continua**.

  ![identity](./media/point-to-site-vpn-client-configuration-radius/identity.png)
7. Nel campo **Local ID** (ID locale) specificare il nome del certificato (dal passaggio 5). In questo esempio è "ikev2Client.com". Fare quindi clic sul pulsante **Applica** per salvare le modifiche.

  ![apply](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
8. Nella finestra di dialogo **Rete** fare clic su **Applica** per salvare tutte le modifiche. Fare quindi clic su **Connect** (Connetti) per avviare la connessione da punto a sito alla rete virtuale di Azure.

## <a name="otherauth"></a>Uso di altri protocolli o di altri tipi di autenticazione

Per usare un diverso tipo di autenticazione, ad esempio OTP anziché nome utente/password o certificati, oppure un diverso protocollo di autenticazione, ad esempio PEAP-MSCHAPv2 invece di EAP-MSCHAPv2, è necessario creare un profilo di configurazione del client VPN personalizzato. Per creare il profilo sono necessarie informazioni come l'indirizzo IP del gateway di rete virtuale, il tipo di tunnel e le route per lo split tunneling. Per ottenere tali informazioni, seguire questa procedura:

1. Usare il cmdlet "Get-AzureRmVpnClientConfiguration" per generare la configurazione del client VPN per EapMSChapv2. Per istruzioni, vedere [questa sezione](#ccradius) dell'articolo.

2. Decomprimere il file VpnClientConfiguration.zip e cercare la cartella GenenericDevice. Ignorare le cartelle contenenti i programmi di installazione Windows per le architetture a 64 e a 32 bit.
 
3. La cartella GenenericDevice include un file XML denominato VpnSettings che contiene tutte le informazioni necessarie.

  * VpnServer: FQDN del gateway VPN di Azure. È l'indirizzo a cui si connette il client.
  * VpnType: tipo di tunnel usato per la connessione.
  * Routes: route da configurare nel profilo affinché sul tunnel da punto a sito venga inviato solo il traffico associato alla rete virtuale di Azure.
  * La cartella GenenericDevice include anche un file CER denominato "VpnServerRoot" che contiene il certificato radice necessario per convalidare il gateway VPN di Azure durante la configurazione della connessione da punto a sito. Installare il certificato in tutti i dispositivi che si connetteranno alla rete virtuale di Azure. 
 
## <a name="next-steps"></a>Passaggi successivi

Tornare all'articolo per [completare la configurazione della connessione da punto a sito](point-to-site-how-to-radius-ps.md).
