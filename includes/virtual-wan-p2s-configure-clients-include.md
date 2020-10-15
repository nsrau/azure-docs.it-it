---
title: File di inclusione
description: File di inclusione
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9caf63fc90be7bae0461ddc24c94594a32199765
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812705"
---
#### <a name="microsoft-windows"></a>Microsoft Windows

##### <a name="openvpn"></a>OpenVPN

1. Scaricare e installare il client OpenVPN dal sito Web ufficiale.
1. Scaricare il profilo VPN per il gateway. Questa operazione può essere eseguita dalla scheda Configurazioni VPN utente nel portale di Azure oppure tramite New-AzureRmVpnClientConfiguration in PowerShell.
1. Decomprimere il profilo. Aprire il file di configurazione vpnconfig.ovpn dalla cartella OpenVPN nel Blocco note.
1. Completare la sezione relativa al certificato client da punto a sito con la chiave pubblica del certificato client da punto a sito in formato Base 64. In un certificato in formato PEM è possibile aprire il file con estensione cer e copiare la chiave in formato Base 64 tra le intestazioni del certificato. Per la procedura, vedere [Come esportare un certificato per ottenere la chiave pubblica codificata](../articles/virtual-wan/certificates-point-to-site.md).
1. Completare la sezione relativa alla chiave privata con la chiave privata del certificato client da punto a sito in formato Base 64. Per la procedura, vedere [Come estrarre la chiave privata](../articles/virtual-wan/howto-openvpn-clients.md#windows).
1. Lasciare invariati tutti gli altri campi. Usare la configurazione così completata nell'input del client per connettersi alla rete VPN.
1. Copiare il file vpnconfig.ovpn nella cartella C:\Programmi\OpenVPN\config.
1. Fare clic con il pulsante destro del mouse sull'icona OpenVPN nell'area di notifica e scegliere **Connetti**.

##### <a name="ikev2"></a>IKEv2

1. Selezionare i file di configurazione del client VPN corrispondenti all'architettura del computer Windows. Per un'architettura con processore a 64 bit, scegliere il pacchetto di installazione "VpnClientSetupAmd64". Per un'architettura con processore a 32 bit, scegliere il pacchetto di installazione "VpnClientSetupX86".
1. Fare doppio clic sul pacchetto per installarlo. Se viene visualizzato un popup SmartScreen, selezionare **Altre informazioni** e quindi **Esegui comunque**.
1. Nel computer client passare a **Impostazioni di rete** e selezionare **VPN**. La connessione VPN viene visualizzata con il nome della rete virtuale a cui si connette.
1. Prima di tentare di connettersi, verificare che nel computer client sia installato un certificato client. Quando si usa il tipo di autenticazione del certificato di Azure nativo, è necessario un certificato client per l'autenticazione. Per altre informazioni sulla generazione di certificati, vedere [Generare i certificati](../articles/virtual-wan/certificates-point-to-site.md). Per informazioni sull'installazione di un certificato client, vedere [Installare un certificato client](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).
