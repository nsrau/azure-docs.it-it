---
title: Usare la rete WAN virtuale di Azure per creare una connessione da punto a sito ad Azure | Microsoft Docs
description: In questa esercitazione si vedrà come usare la rete WAN virtuale di Azure per creare una connessione VPN da punto a sito ad Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 06/29/2020
ms.author: alzam
ms.openlocfilehash: 9c93ad0357011008c45b2898260a655509b02dc2
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560659"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Esercitazione: Creare una connessione VNP utente usando la rete WAN virtuale di Azure

Questa esercitazione illustra come usare la rete WAN virtuale per connettersi alle risorse in Azure tramite una connessione VPN IPSec/IKE (IKEv2) o OpenVPN. Questo tipo di connessione richiede la configurazione di un client nel computer client. Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una rete WAN
> * Creare una configurazione da punto a sito
> * Creare un hub
> * Specificare i server DNS
> * Scaricare un profilo client VPN
> * Visualizzare la rete WAN virtuale

![Diagramma della rete WAN virtuale](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

* Si ha una rete virtuale a cui ci si vuole connettere. Verificare che nessuna delle subnet della rete locale possa sovrapporsi alle reti virtuali a cui ci si vuole connettere. Per creare una rete virtuale nel portale di Azure, vedere l'[Avvio rapido](../virtual-network/quick-create-portal.md).

* La rete virtuale non presenta alcun gateway di rete virtuale. Se la rete virtuale presenta un gateway (VPN o ExpressRoute) è necessario rimuovere tutti i gateway. Questa configurazione richiede che le reti virtuali siano invece connesse al gateway dell'hub della rete WAN virtuale.

* Ottenere un intervallo di indirizzi IP per l'area dell'hub. L'hub è una rete virtuale che viene creata e usata dalla rete WAN virtuale. L'intervallo di indirizzi specificati per l'hub non può sovrapporsi ad alcuna delle reti virtuali esistenti a cui ci si connette. Inoltre non può sovrapporsi agli intervalli di indirizzi a cui ci si connette in locale. Se non si ha familiarità con gli intervalli degli indirizzi IP disponibili nella configurazione della rete locale, coordinarsi con qualcuno che possa fornire tali dettagli.

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Creare una rete WAN virtuale

In un browser passare al [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

1. Passare alla pagina WAN virtuale. Nel portale selezionare **+ Crea una risorsa**. Digitare **Rete WAN virtuale** nella casella di ricerca e premere **INVIO**.
1. Selezionare **Rete WAN virtuale** nei risultati. Nella pagina della rete WAN virtuale selezionare **Crea** per aprire la pagina Crea rete WAN.
1. Nella pagina **Crea rete WAN**, nella scheda **Nozioni di base**, compilare i campi seguenti:

   ![Rete WAN virtuale](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Sottoscrizione** - Selezionare la sottoscrizione che si vuole usare.
   * **Gruppo di risorse** - Creare un nuovo gruppo o usarne uno esistente.
   * **Posizione gruppo di risorse** - Scegliere una posizione per le risorse nell'elenco a discesa. Una rete WAN è una risorsa globale e non si trova in un'area specifica. Tuttavia, è necessario selezionare un'area per poter gestire e individuare più facilmente la risorsa WAN creata.
   * **Nome** - Digitare il nome che si vuole usare per la rete WAN.
   * **Tipo:** Standard. Con la creazione di una rete WAN di base è possibile creare solo un hub di base. Gli hub di base possono supportare solo la connettività VPN da sito a sito.
1. Dopo aver completato i campi, selezionare **Rivedi e crea**.
1. Al termine della convalida selezionare **Crea** per creare la rete WAN virtuale.

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Creare una configurazione da punto a sito

Una configurazione da punto a sito definisce i parametri per la connessione di client remoti.

1. Passare a **Tutte le risorse**.
1. Selezionare la rete WAN virtuale creata.
1. Selezionare **+ Crea configurazione VPN utente** nella parte superiore della pagina per aprire la pagina **Crea nuova configurazione VPN utente**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/p2s1.jpg" alt-text="Configurazioni VPN utente":::

1. Nella pagina **Crea nuova configurazione VPN utente** completare i campi seguenti:

   * **Nome configurazione**: nome da usare per fare riferimento alla configurazione.
   * **Tipo di tunnel**: protocollo da usare per il tunnel.
   * **Root Certificate Name** (Nome certificato radice): nome descrittivo per il certificato.
   * **Dati del certificato pubblico**: dati del certificato X.509 con codifica in base 64.
  
1. Selezionare **Crea** per creare la configurazione.

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Creare un hub con un gateway da punto a sito

1. Nella rete WAN virtuale selezionare Hub e quindi **+ Nuovo hub**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub1.jpg" alt-text="Nuovo hub":::

1. Nella pagina Crea hub virtuale completare i campi seguenti.

   * **Area**: selezionare l'area in cui si vuole distribuire l'hub virtuale.
   * **Nome**: immettere il nome con cui denominare l'hub virtuale.
   * **Spazio di indirizzi privato dell'hub**: intervallo di indirizzi dell'hub nella notazione CIDR.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub2.jpg" alt-text="Creare un hub virtuale":::

1. Nella scheda Da punto a sito completare i campi seguenti:

   * **Unità di scala gateway**, che rappresenta la capacità aggregata del gateway VPN utente.
   * **Configurazione da punto a sito**, creata nel passaggio precedente.
   * **Pool indirizzi client**, per gli utenti remoti.
   * **IP server DNS personalizzato**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub-with-p2s.png" alt-text="Hub con configurazione da punto a sito":::

1. Selezionare **Rivedi e crea**.
1. Nella pagina **Convalida superata** selezionare **Crea**.

## <a name="specify-dns-server"></a><a name="dns"></a>Specificare il server DNS

I gateway VPN utente della rete WAN virtuale consentono di specificare fino a 5 server DNS. È possibile eseguire questa configurazione durante il processo di creazione dell'hub oppure modificarla in un secondo momento. A questo scopo, individuare l'hub virtuale. In **VPN utente (da punto a sito)** fare clic su Configura e immettere gli indirizzi IP dei server DNS nella casella di testo **Server DNS personalizzati**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="DNS personalizzato" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>Scaricare il profilo VPN

Usare il profilo VPN per configurare i client.

1. Nella pagina della rete WAN virtuale selezionare **Configurazioni VPN utente**.
2. Nella parte superiore della pagina Selezionare **Download user VPN config** (Scarica configurazione VPN utente). Il download della configurazione a livello di rete WAB fornisce un profilo predefinito della VPN utente basato su Gestione traffico. Per altre informazioni sui profili globali o sui profili basati su hub, vedere [Profili degli hub](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile).   Gli scenari di failover sono semplificati con il profilo globale.

   Se, per qualche motivo, un hub non è disponibile, il profilo predefinito di Gestione traffico fornito dal servizio assicura la connettività tramite un hub diverso alle risorse di Azure per gli utenti da punto a sito. È sempre possibile scaricare una configurazione VPN specifica dell'hub passando all'hub specifico. In **VPN utente (da punto a sito)** scaricare il profilo **VPN utente** dell'hub virtuale.

1. Al termine della creazione del file è possibile selezionare il collegamento per scaricarlo.
1. Usare il file del profilo per configurare i client VPN.

### <a name="configure-user-vpn-clients"></a>Configurare i client VPN utente

Usare il profilo scaricato per configurare i client di accesso remoto. La procedura per ogni sistema operativo è diversa, seguire le istruzioni corrette riportate di seguito:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Scaricare e installare il client OpenVPN dal sito Web ufficiale.
1. Scaricare il profilo VPN per il gateway. Questa operazione può essere eseguita dalla scheda Configurazioni VPN utente nel portale di Azure oppure tramite New-AzureRmVpnClientConfiguration in PowerShell.
1. Decomprimere il profilo. Aprire il file di configurazione vpnconfig.ovpn dalla cartella OpenVPN nel Blocco note.
1. Completare la sezione relativa al certificato client da punto a sito con la chiave pubblica del certificato client da punto a sito in formato Base 64. In un certificato in formato PEM è possibile aprire semplicemente il file con estensione cer e copiare la chiave in formato Base 64 tra le intestazioni del certificato. Per la procedura, vedere [Come esportare un certificato per ottenere la chiave pubblica codificata](certificates-point-to-site.md).
1. Completare la sezione relativa alla chiave privata con la chiave privata del certificato client da punto a sito in formato Base 64. Per la procedura, vedere [Come estrarre la chiave privata](howto-openvpn-clients.md#windows).
1. Lasciare invariati tutti gli altri campi. Usare la configurazione così completata nell'input del client per connettersi alla rete VPN.
1. Copiare il file vpnconfig.ovpn nella cartella C:\Programmi\OpenVPN\config.
1. Fare clic con il pulsante destro del mouse sull'icona OpenVPN nell'area di notifica e scegliere **Connetti**.

##### <a name="ikev2"></a>IKEv2

1. Selezionare i file di configurazione del client VPN corrispondenti all'architettura del computer Windows. Per un'architettura con processore a 64 bit, scegliere il pacchetto di installazione "VpnClientSetupAmd64". Per un'architettura con processore a 32 bit, scegliere il pacchetto di installazione "VpnClientSetupX86".
1. Fare doppio clic sul pacchetto per installarlo. Se viene visualizzato un popup SmartScreen, selezionare **Altre informazioni** e quindi **Esegui comunque**.
1. Nel computer client passare a **Impostazioni di rete** e selezionare **VPN**. La connessione VPN viene visualizzata con il nome della rete virtuale a cui si connette.
1. Prima di tentare di connettersi, verificare che nel computer client sia installato un certificato client. Quando si usa il tipo di autenticazione del certificato di Azure nativo, è necessario un certificato client per l'autenticazione. Per altre informazioni sulla generazione di certificati, vedere [Generare i certificati](certificates-point-to-site.md). Per informazioni sull'installazione di un certificato client, vedere [Installare un certificato client](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Visualizzare la rete WAN virtuale

1. Passare alla rete WAN virtuale.
1. Nella pagina **Panoramica** ogni punto sulla mappa rappresenta un hub.
1. Nella sezione **Hub e connessioni** è possibile visualizzare lo stato dell'hub, il sito, l'area, lo stato della connessione VPN e i byte in entrata e in uscita.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Pulire le risorse

Quando queste risorse non sono più necessarie, è possibile usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute. Sostituire "myResourceGroup" con il nome del gruppo di risorse specifico ed eseguire il comando di PowerShell seguente:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).
