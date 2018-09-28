---
title: Come configurare i client OpenVPN per il Gateway VPN di Azure | Microsoft Docs
description: Procedura per configurare i client OpenVPN per il Gateway VPN di Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 141f29ea1c7ebdd8208dda6bc39a60f7873dab7d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977843"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway-preview"></a>Configurare i client OpenVPN per il Gateway VPN di Azure (anteprima)

Questo articolo consente di configurare i client OpenVPN.

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di aver completato i passaggi per configurare OpenVPN per il gateway VPN. Per informazioni dettagliate, vedere [configurare OpenVPN per il Gateway VPN di Azure](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Client Windows

1. Scaricare e installare il client OpenVPN dal [sito Web ufficiale di OpenVPN](https://openvpn.net/index.php/open-source/downloads.html).
2. Scaricare il profilo VPN per il gateway. Questa operazione può essere eseguita dalla scheda Point-to-site configuration (Configurazione da punto a sito) nel portale di Azure o tramite "New-AzureRmVpnClientConfiguration" in PowerShell.
3. Decomprimere il profilo. Aprire il file di configurazione vpnconfig.ovpn dalla cartella OpenVPN nel Blocco note.
4. Completare la sezione relativa al certificato client della connessione da punto a sito con la chiave pubblica del certificato client della connessione da punto a sito in formato base64. In un certificato in formato PEM è possibile aprire semplicemente il file con estensione cer e copiare la chiave in formato base64 tra le intestazioni del certificato. Sono disponibili altre informazioni su come esportare un certificato per ottenere la chiave pubblica codificata.
5. Completare la sezione relativa alla chiave privata con la chiave privata del certificato client della connessione da punto a sito in formato base64. Per informazioni su come estrarre la chiave privata, vedere [come esportare la chiave](vpn-gateway-certificates-point-to-site.md#clientexport).
6. Lasciare invariati tutti gli altri campi. Usare la configurazione così completata nell'input del client per connettersi alla rete VPN.
7. Copiare il file vpnconfig.ovpn nella cartella C:\Programmi\OpenVPN\config.
8. Fare clic con il pulsante destro del mouse sull'icona OpenVPN nell'area di notifica e fare clic su Connetti.

## <a name="mac"></a>Client Mac

1. Scaricare e installare un client OpenVPN, ad esempio [TunnelBlik](https://tunnelblick.net/downloads.html). 
2. Scaricare il profilo VPN per il gateway. Questa operazione può essere eseguita dalla scheda Point-to-site configuration (Configurazione da punto a sito) nel portale di Azure o tramite "New-AzureRmVpnClientConfiguration" in PowerShell.
3. Decomprimere il profilo. Aprire il file di configurazione vpnconfig.ovpn dalla cartella OpenVPN nel Blocco note.
4. Completare la sezione relativa al certificato client della connessione da punto a sito con la chiave pubblica del certificato client della connessione da punto a sito in formato base64. In un certificato in formato PEM è possibile aprire semplicemente il file con estensione cer e copiare la chiave in formato base64 tra le intestazioni del certificato. Visualizzare come [esportare la chiave pubblica](vpn-gateway-certificates-point-to-site.md#cer) per informazioni sull'esportazione di un certificato per ottenere la chiave pubblica codificata.
5. Completare la sezione relativa alla chiave privata con la chiave privata del certificato client della connessione da punto a sito in formato base64. Per informazioni su come estrarre la chiave privata, vedere [come esportare la chiave privata](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/).
6. Lasciare invariati tutti gli altri campi. Usare la configurazione così completata nell'input del client per connettersi alla rete VPN.
7. Fare doppio clic sul file del profilo per creare il profilo in Tunnelblik.
8. Avviare Tunnelblik dalla cartella delle applicazioni.
9. Fare clic sull'icona Tunneblik nell'area di notifica e selezionare Connetti.

## <a name="linux"></a>Client Linux

1. Aprire una nuova sessione del terminale. È possibile aprire una nuova sessione premendo contemporaneamente "Ctrl + Alt + t"
2. Immettere il comando seguente per installare i componenti necessari:

  ```
  sudo apt-get install openvpn
  sudo apt-get -y install network-manager-openvpn
  sudo service network-manager restart
  ```
3. Scaricare il profilo VPN per il gateway. Questa operazione può essere eseguita dalla scheda Point-to-site configuration (Configurazione da punto a sito) nel portale di Azure o tramite "New-AzureRmVpnClientConfiguration" in PowerShell.
4. Completare la sezione relativa alla chiave privata con la chiave privata del certificato client della connessione da punto a sito in formato base64. Per informazioni su come estrarre la chiave privata, vedere [come esportare la chiave privata](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/).
5. Per connettersi usando la riga di comando, digitare quanto segue:
  
  ```
  Sudo openvpn –config <name and path of your VPN profile file>
  ```
5. Per connettersi usando l'interfaccia utente grafica, passare alle impostazioni di sistema.
6. Fare clic su **+** per aggiungere una nuova connessione VPN.
7. Sotto **Add VPN** (Aggiungi VPN), selezionare **Import from file…** (Importa da file...)
8. Individuare il file del profilo e fare doppio clic o selezionare **Open** (Apri)
9. Fare clic su **Add** (Aggiungi) nella finestra **Add VPN** (Aggiungi VPN).
  
  ![Importazione da file](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
10. La VPN può essere connessa selezionando **ON** nella pagina **Network Settings** (Impostazioni di rete), o sotto l'icona di rete nell'area di notifica.

## <a name="next-steps"></a>Passaggi successivi

Se si desidera che i client VPN accedano alle risorse in un'altra rete virtuale (produzione), seguire le istruzioni riportate nell'articolo [Vnet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) per configurare una connessione da rete virtuale a rete virtuale. Assicurarsi di abilitare BGP su gateway e connessioni, altrimenti il traffico non verrà propagato.
