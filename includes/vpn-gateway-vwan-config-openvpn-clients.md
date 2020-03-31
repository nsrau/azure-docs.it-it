---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 921e22e637782ffd744af1a28e6bd43e7dd53c67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066077"
---
## <a name="windows-clients"></a><a name="windows"></a>Client Windows

1. Scaricare e installare il client OpenVPN (versione 2.4 o successiva) dal sito ufficiale [OpenVPN](https://openvpn.net/index.php/open-source/downloads.html).
2. Scaricare il profilo VPN per il gateway. Questa operazione può essere eseguita dalla scheda Configurazione da punto a sito nel portale di Azure o tramite il comando "New-AzVpnClientConfiguration" in PowerShell.
3. Decomprimere il profilo. Aprire quindi il file di configurazione *vpnconfig.ovpn* dalla cartella OpenVPN usando il Blocco note.
4. Esportare il certificato client da punto a sito creato e caricato nella configurazione P2S nel gateway. Utilizzare i seguenti collegamenti agli articoli:

   * [Istruzioni per](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) il gateway VPN
   
   * [Istruzioni per la WAN virtuale](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Estrarre la chiave privata e l'identificazione personale base64 dal file con estensione *pfx*. Per eseguire questa operazione è possibile procedere diversi modi. Un modo consiste nell'usare OpenSSL nel computer. Il file *profileinfo.txt* contiene la chiave privata e l'identificazione personale per l'Autorità di certificazione e il certificato client. Assicurarsi di usare l'identificazione personale del certificato client.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Aprire *profileinfo.txt* nel Blocco note. Per ottenere l'identificazione personale del certificato client (figlio), selezionare il testo compreso tra "-----BEGIN CERTIFICATE-----" e "-----END CERTIFICATE-----" (inclusi) per il certificato figlio e copiarlo. È possibile identificare il certificato figlio dalla riga subject=/.
7. Passare al file *vpnconfig.ovpn* aperto nel Blocco note al passaggio 3. Trovare la sezione mostrata di seguito e sostituire tutto il testo compreso tra "cert" e "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Aprire il file *profileinfo.txt* nel Blocco note. Per ottenere la chiave privata, selezionare il testo (incluso e tra) "-----BEGIN PRIVATE KEY-----" e "-----END PRIVATE KEY-----" e copiarlo.
9. Tornare al file vpnconfig.ovpn nel Blocco note e trovare questa sezione. Incollare la chiave privata sostituendo tutto il testo compreso tra "key" e "/key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Lasciare invariati tutti gli altri campi. Usare la configurazione così completata nell'input del client per connettersi alla rete VPN.
11. Copiare il file vpnconfig.ovpn nella cartella C:\Programmi\OpenVPN\config.
12. Fare clic con il pulsante destro del mouse sull'icona OpenVPN nell'area di notifica e fare clic su Connetti.

## <a name="mac-clients"></a><a name="mac"></a>Client Mac

1. Scaricare e installare un client OpenVPN, ad esempio [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Scaricare il profilo VPN per il gateway. Questa operazione può essere eseguita dalla scheda Configurazione da punto a sito nel portale di Azure o tramite il comando "New-AzVpnClientConfiguration" in PowerShell.
3. Decomprimere il profilo. Aprire il file di configurazione vpnconfig.ovpn dalla cartella OpenVPN in un editor di testo.
4. Completare la sezione relativa al certificato client da punto a sito con la chiave pubblica del certificato client da punto a sito in formato Base 64. In un certificato in formato PEM è possibile aprire semplicemente il file con estensione cer e copiare la chiave in formato Base 64 tra le intestazioni del certificato. Utilizzare i collegamenti agli articoli seguenti per informazioni su come esportare un certificato per ottenere la chiave pubblica codificata:Use the following article links for information about how to export a certificate to get the encoded public key:

   * [Istruzioni per](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) il gateway VPN 
   
   * [Istruzioni per la WAN virtuale](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. Completare la sezione relativa alla chiave privata con la chiave privata del certificato client da punto a sito in formato Base 64. Vedere [Esportare la chiave privata](https://openvpn.net/community-resources/how-to/#pki) nel sito OpenVPN per informazioni su come estrarre una chiave privata.
6. Lasciare invariati tutti gli altri campi. Usare la configurazione così completata nell'input del client per connettersi alla rete VPN.
7. Fare doppio clic sul file del profilo per creare il profilo in Tunnelblick.
8. Avviare Tunnelblick dalla cartella delle applicazioni.
9. Fare clic sull'icona Tunnelblick nella barra delle applicazioni e selezionare Connect.

> [!IMPORTANT]
>Solo iOS 11.0 e versioni successive; MacOS 10.13 e versioni successive sono supportati con protocollo OpenVPN.
>
## <a name="ios-clients"></a><a name="iOS"></a>Client iOS

1. Installare il client OpenVPN (versione 2.4 o successiva) dall'App Store.
2. Scaricare il profilo VPN per il gateway. Questa operazione può essere eseguita dalla scheda Configurazione da punto a sito nel portale di Azure o tramite il comando "New-AzVpnClientConfiguration" in PowerShell.
3. Decomprimere il profilo. Aprire il file di configurazione vpnconfig.ovpn dalla cartella OpenVPN in un editor di testo.
4. Completare la sezione relativa al certificato client da punto a sito con la chiave pubblica del certificato client da punto a sito in formato Base 64. In un certificato in formato PEM è possibile aprire semplicemente il file con estensione cer e copiare la chiave in formato Base 64 tra le intestazioni del certificato. Utilizzare i collegamenti agli articoli seguenti per informazioni su come esportare un certificato per ottenere la chiave pubblica codificata:Use the following article links for information about how to export a certificate to get the encoded public key:

   * [Istruzioni per](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) il gateway VPN 
   
   * [Istruzioni per la WAN virtuale](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. Completare la sezione relativa alla chiave privata con la chiave privata del certificato client da punto a sito in formato Base 64. Per informazioni su come estrarre una chiave privata, vedere [Esportare la chiave privata](https://openvpn.net/community-resources/how-to/#pki) nel sito OpenVPN.
6. Lasciare invariati tutti gli altri campi.
7. Inviare tramite posta elettronica il file del profilo (con estensione ovpn) all'account di posta elettronica configurato nell'app di posta elettronica sull'iPhone. 
8. Aprire l'e-mail nell'app di posta elettronica sull'iPhone e toccare il file allegato

    ![Aprire la posta elettronica](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. Toccare **Altro** se l'opzione **Copia in OpenVPN** non è visualizzata

    ![Altro](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. Toccare **Copia su OpenVPN** 

    ![Copia in OpenVPN](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. Tocca **AGGIUNGI** nella pagina **Importa profilo**

    ![Add](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. Tocca **AGGIUNGI** nella pagina **Profilo importato**

    ![Tocca AGGIUNGI](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. Avviare l'app OpenVPN e far scorrere l'interruttore nella pagina **Profilo** a destra per connettersi

    ![Connessione](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Client Linux

1. Aprire una nuova sessione del terminale. È possibile aprire una nuova sessione premendo contemporaneamente CTRL + ALT + T.
2. Immettere il comando seguente per installare i componenti necessari:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Scaricare il profilo VPN per il gateway. Questa operazione può essere eseguita dalla scheda Configurazione da punto a sito nel portale di Azure.
4. Esportare il certificato client per connessioni da punto a sito creato e caricato nella configurazione della connessione da punto a sito sul gateway. Utilizzare i seguenti collegamenti agli articoli:

   * [Istruzioni per](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) il gateway VPN 
   
   * [Istruzioni per la WAN virtuale](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Estrarre la chiave privata e l'identificazione personale base64 dal file con estensione pfx. Per eseguire questa operazione è possibile procedere diversi modi. Un modo consiste nell'usare OpenSSL nel computer.

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   Il file *profileinfo.txt* conterrà la chiave privata e l'identificazione personale per l'Autorità di certificazione e il certificato client. Assicurarsi di usare l'identificazione personale del certificato client.

6. Aprire *profileinfo.txt* in un editor di testo. Per ottenere l'identificazione personale del certificato client (figlio), selezionare il testo compreso tra "-----BEGIN CERTIFICATE-----" e "-----END CERTIFICATE-----" (inclusi) per il certificato figlio e copiarlo. È possibile identificare il certificato figlio dalla riga subject=/.

7. Aprire il file *vpnconfig.ovpn* e trovare la sezione riportata di seguito. Sostituire tutto il testo compreso tra "cert" e "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Aprire il file profileinfo.txt in un editor di testo. Per ottenere la chiave privata, selezionare il testo e tra "-----BEGIN PRIVATE KEY-----" e "-----END PRIVATE KEY-----" e copiarlo.

9. Aprire il file vpnconfig.ovpn in un editor di testo e trovare questa sezione. Incollare la chiave privata sostituendo tutto il testo compreso tra "key" e "/key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Lasciare invariati tutti gli altri campi. Usare la configurazione così completata nell'input del client per connettersi alla rete VPN.
11. Per connettersi usando la riga di comando, digitare il comando seguente:
  
    ```
    sudo openvpn –-config <name and path of your VPN profile file>&
    ```
12. Per connettersi usando l'interfaccia utente grafica, passare alle impostazioni di sistema.
13. Fare **+** clic per aggiungere una nuova connessione VPN.
14. Sotto **Add VPN** (Aggiungi VPN), selezionare **Import from file…** (Importa da file...)
15. Individuare il file del profilo e fare doppio clic o selezionare **Apri**.
16. Fare clic su **Add** (Aggiungi) nella finestra **Add VPN** (Aggiungi VPN).
  
    ![Importazione da file](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. La VPN può essere connessa selezionando **ON** nella pagina **Network Settings** (Impostazioni di rete), o sotto l'icona di rete nell'area di notifica.