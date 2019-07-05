---
title: Come configurare i client OpenVPN per il Gateway VPN di Azure | Microsoft Docs
description: Procedura per configurare i client OpenVPN per il Gateway VPN di Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: cherylmc
ms.openlocfilehash: b8f1626da730178d2cd9c2f31c4f9876102b3d46
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477839"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurare i client OpenVPN per Gateway VPN di Azure

Questo articolo consente di configurare **OpenVPN® protocollo** client.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Verificare di aver completato i passaggi per configurare OpenVPN per il gateway VPN. Per informazioni dettagliate, vedere [configurare OpenVPN per il Gateway VPN di Azure](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Client Windows

1. Scaricare e installare il client OpenVPN (versione 2.4 o versioni successive) da ufficiale [sito Web OpenVPN](https://openvpn.net/index.php/open-source/downloads.html).
2. Scaricare il profilo VPN per il gateway. Questa operazione può essere eseguita dalla scheda Configurazione da punto a sito nel portale di Azure o tramite il comando "New-AzVpnClientConfiguration" in PowerShell.
3. Decomprimere il profilo. Aprire quindi il file di configurazione *vpnconfig.ovpn* dalla cartella OpenVPN usando il Blocco note.
4. [Esportare](vpn-gateway-certificates-point-to-site.md#clientexport) il certificato client per connessioni da punto a sito creato e caricato nella configurazione della connessione da punto a sito sul gateway.
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
8. Aprire il file *profileinfo.txt* nel Blocco note. Per ottenere la chiave privata, selezionare il testo (tra cui e tra) "---BEGIN PRIVATE KEY---" e "---END PRIVATE KEY---" e copiarlo.
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

## <a name="mac"></a>Client Mac

1. Scaricare e installare, ad esempio un client, OpenVPN [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Scaricare il profilo VPN per il gateway. Questa operazione può essere eseguita dalla scheda Configurazione da punto a sito nel portale di Azure o tramite il comando "New-AzVpnClientConfiguration" in PowerShell.
3. Decomprimere il profilo. Aprire il file di configurazione vpnconfig.ovpn dalla cartella OpenVPN nel Blocco note.
4. Completare la sezione relativa al certificato client da punto a sito con la chiave pubblica del certificato client da punto a sito in formato Base 64. In un certificato in formato PEM è possibile aprire semplicemente il file con estensione cer e copiare la chiave in formato base64 tra le intestazioni del certificato. Visualizzare come [esportare la chiave pubblica](vpn-gateway-certificates-point-to-site.md#cer) per informazioni sull'esportazione di un certificato per ottenere la chiave pubblica codificata.
5. Completare la sezione relativa alla chiave privata con la chiave privata del certificato client da punto a sito in formato Base 64. Per informazioni su come estrarre una chiave privata, vedere [Export your private key](https://openvpn.net/community-resources/how-to/#pki) (Esportare la chiave privata).
6. Lasciare invariati tutti gli altri campi. Usare la configurazione così completata nell'input del client per connettersi alla rete VPN.
7. Fare doppio clic sul file del profilo per creare il profilo in Tunnelblick.
8. Avviare Tunnelblick dalla cartella di applicazioni.
9. Fare clic sull'icona Tunnelblick nell'area di notifica e scegliere la connessione.

> [!IMPORTANT]
>Solo iOS 11.0 e versioni successive; MacOS 10.13 e versioni successive sono supportati con protocollo OpenVPN.
>

## <a name="linux"></a>Client Linux

1. Aprire una nuova sessione del terminale. È possibile aprire una nuova sessione premendo contemporaneamente CTRL + ALT + T.
2. Immettere il comando seguente per installare i componenti necessari:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Scaricare il profilo VPN per il gateway. Questa operazione può essere eseguita dalla scheda Configurazione da punto a sito nel portale di Azure.
4. [Esportare](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site#clientexport) il certificato client per connessioni da punto a sito creato e caricato nella configurazione della connessione da punto a sito sul gateway. 
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
8. Aprire il file profileinfo.txt in un editor di testo. Per ottenere la chiave privata, selezionare il testo incluso tra "---BEGIN PRIVATE KEY---" e "---END PRIVATE KEY---" e copiarlo.

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
    sudo openvpn –-config <name and path of your VPN profile file>
    ```
12. Per connettersi usando l'interfaccia utente grafica, passare alle impostazioni di sistema.
13. Fare clic su **+** per aggiungere una nuova connessione VPN.
14. Sotto **Add VPN** (Aggiungi VPN), selezionare **Import from file…** (Importa da file...)
15. Individuare il file del profilo e fare doppio clic o selezionare **Apri**.
16. Fare clic su **Add** (Aggiungi) nella finestra **Add VPN** (Aggiungi VPN).
  
    ![Importazione da file](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
17. La VPN può essere connessa selezionando **ON** nella pagina **Network Settings** (Impostazioni di rete), o sotto l'icona di rete nell'area di notifica.

## <a name="next-steps"></a>Passaggi successivi

Se si desidera che i client VPN per essere in grado di accedere alle risorse in un'altra rete virtuale, quindi seguire le istruzioni [VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) articolo per configurare una connessione di vnet-to-vnet. Assicurarsi di abilitare BGP sui gateway e le connessioni, altrimenti il flusso del traffico non sarà possibile.

**"OpenVPN" è un marchio registrato di OpenVPN Inc.**
