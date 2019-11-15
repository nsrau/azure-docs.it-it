---
title: Usare la rete WAN virtuale di Azure per creare una connessione da punto a sito ad Azure | Microsoft Docs
description: In questa esercitazione si vedrà come usare la rete WAN virtuale di Azure per creare una connessione VPN da punto a sito ad Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: alzam
ms.openlocfilehash: 0319e3aec71d37b49a094861fdcbb3b96b6def67
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585424"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Esercitazione: Creare una connessione VNP utente usando la rete WAN virtuale di Azure

Questa esercitazione illustra come usare la rete WAN virtuale per connettersi alle risorse in Azure tramite una connessione VPN IPSec/IKE (IKEv2) o OpenVPN. Questo tipo di connessione richiede la configurazione di un client nel computer client. Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una rete WAN
> * Creare un hub
> * Creare una configurazione da punto a sito
> * Scaricare un profilo client VPN
> * Applicare la configurazione da punto a sito a un hub
> * Connettere una rete virtuale a un hub
> * Scaricare e applicare la configurazione del client VPN
> * Visualizzare la rete WAN virtuale
> * Visualizzare lo stato di integrità delle risorse

![Diagramma della rete WAN virtuale](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

* Si ha una rete virtuale a cui ci si vuole connettere. Verificare che nessuna delle subnet della rete locale possa sovrapporsi alle reti virtuali a cui ci si vuole connettere. Per creare una rete virtuale nel portale di Azure, vedere l'[Avvio rapido](../virtual-network/quick-create-portal.md).

* La rete virtuale non presenta alcun gateway di rete virtuale. Se la rete virtuale presenta un gateway (VPN o ExpressRoute) è necessario rimuovere tutti i gateway. Questa configurazione richiede che le reti virtuali siano invece connesse al gateway dell'hub della rete WAN virtuale.

* Ottenere un intervallo di indirizzi IP per l'area dell'hub. L'hub è una rete virtuale che viene creata e usata dalla rete WAN virtuale. L'intervallo di indirizzi specificati per l'hub non può sovrapporsi ad alcuna delle reti virtuali esistenti a cui ci si connette. Inoltre non può sovrapporsi agli intervalli di indirizzi a cui ci si connette in locale. Se non si ha familiarità con gli intervalli degli indirizzi IP disponibili nella configurazione della rete locale, coordinarsi con qualcuno che possa fornire tali dettagli.

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="wan"></a>Creare una rete WAN virtuale

In un browser passare al [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

1. Passare alla pagina WAN virtuale. Nel portale fare clic su **+Crea una risorsa**. Digitare **Rete WAN virtuale** nella casella di ricerca e premere INVIO.
2. Selezionare **Rete WAN virtuale** nei risultati. Nella pagina della rete WAN virtuale fare clic su **Crea** per aprire la pagina Crea rete WAN.
3. Nella pagina **Crea rete WAN**, nella scheda **Nozioni di base**, compilare i campi seguenti:

   ![Rete WAN virtuale](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Sottoscrizione** - Selezionare la sottoscrizione che si vuole usare.
   * **Gruppo di risorse** - Creare un nuovo gruppo o usarne uno esistente.
   * **Posizione gruppo di risorse** - Scegliere una posizione per le risorse nell'elenco a discesa. Una rete WAN è una risorsa globale e non si trova in un'area specifica. Tuttavia, è necessario selezionare un'area per poter gestire e individuare più facilmente la risorsa WAN creata.
   * **Nome** - Digitare il nome che si vuole usare per la rete WAN.
   * **Tipo:** Standard. Con la creazione di una rete WAN di base è possibile creare solo un hub di base. Gli hub di base possono supportare solo la connettività VPN da sito a sito.
4. Dopo aver completato i campi selezionare **Rivedi e crea**.
5. Al termine della convalida selezionare **Crea** per creare la rete WAN virtuale.

## <a name="site"></a>Creare un hub virtuale vuoto

1. Nella rete WAN virtuale selezionare Hub e fare clic su **+Nuovo hub**

   ![nuovo sito](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. Nella pagina Crea hub virtuale completare i campi seguenti.

   **Area**: selezionare l'area in cui si vuole distribuire l'hub virtuale.

   **Nome**: immettere il nome con cui denominare l'hub virtuale.

   **Spazio di indirizzi privato dell'hub**: intervallo di indirizzi dell'hub nella notazione CIDR.

   ![nuovo sito](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. Fare clic su **Rivedi e crea**.
4. Nella pagina **Convalida superata** fare clic su **Crea**.

## <a name="site"></a>Creare una configurazione da punto a sito

Una configurazione da punto a sito definisce i parametri per la connessione di client remoti.

1. Passare a **Tutte le risorse**.
2. Fare clic sulla rete WAN virtuale creata.
3. Fare clic su **+Crea configurazione VPN utente** nella parte superiore della pagina per aprire la pagina **Crea nuova configurazione VPN utente**.

   ![nuovo sito](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. Nella pagina **Crea nuova configurazione VPN utente** completare i campi seguenti:

   **Nome configurazione**: nome da usare per fare riferimento alla configurazione.

   **Tipo di tunnel**: protocollo da usare per il tunnel.

   **Root Certificate Name** (Nome certificato radice): nome descrittivo per il certificato.

   **Dati del certificato pubblico**: dati del certificato X.509 con codifica in base 64.
  
   ![nuovo sito](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Fare clic su **Crea** per creare la configurazione.

## <a name="hub"></a>Modificare l'assegnazione dell'hub

1. Passare al pannello **Hub** nella rete WAN virtuale
2. Selezionare l'hub a cui si vuole associare la configurazione del server VPN e fare clic su **...**

   ![nuovo sito](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. Fare clic su **Modifica hub virtuale**.
4. Selezionare la casella di controllo **Includi gateway da punto a sito** e selezionare l'**unità di scala gateway** che si vuole usare.

   ![nuovo sito](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Immettere il **Pool di indirizzi** da cui gli indirizzi IP verranno assegnati ai client VPN.
6. Fare clic su **Conferma**
7. Il completamento dell'operazione potrà richiedere fino a 30 minuti.

## <a name="device"></a>Scaricare il profilo VPN

Usare il profilo VPN per configurare i client.

1. Nella pagina della rete WAN virtuale fare clic su **Configurazioni VPN utente**.
2. Nella parte superiore della pagina fare clic su **Download user VPN config** (Scarica configurazione VPN utente).
3. Al termine della creazione del file è possibile fare clic sul collegamento per scaricarlo.
4. Usare il file del profilo per configurare i client VPN.

### <a name="configure-user-vpn-clients"></a>Configurare i client VPN utente
Usare il profilo scaricato per configurare i client di accesso remoto. La procedura per ogni sistema operativo è diversa, seguire le istruzioni corrette riportate di seguito:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Scaricare e installare il client OpenVPN dal sito Web ufficiale.
2. Scaricare il profilo VPN per il gateway. Questa operazione può essere eseguita dalla scheda Configurazioni VPN utente nel portale di Azure oppure tramite New-AzureRmVpnClientConfiguration in PowerShell.
3. Decomprimere il profilo. Aprire il file di configurazione vpnconfig.ovpn dalla cartella OpenVPN nel Blocco note.
4. Completare la sezione relativa al certificato client da punto a sito con la chiave pubblica del certificato client da punto a sito in formato Base 64. In un certificato in formato PEM è possibile aprire semplicemente il file con estensione cer e copiare la chiave in formato Base 64 tra le intestazioni del certificato. Sono disponibili altre informazioni su come esportare un certificato per ottenere la chiave pubblica codificata.
5. Completare la sezione relativa alla chiave privata con la chiave privata del certificato client da punto a sito in formato Base 64. Sono disponibili altre informazioni su come estrarre la chiave privata.
6. Lasciare invariati tutti gli altri campi. Usare la configurazione così completata nell'input del client per connettersi alla rete VPN.
7. Copiare il file vpnconfig.ovpn nella cartella C:\Programmi\OpenVPN\config.
8. Fare clic con il pulsante destro del mouse sull'icona OpenVPN nell'area di notifica e fare clic su Connetti.

##### <a name="ikev2"></a>IKEv2

1. Selezionare i file di configurazione del client VPN corrispondenti all'architettura del computer Windows. Per un'architettura con processore a 64 bit, scegliere il pacchetto di installazione "VpnClientSetupAmd64". Per un'architettura con processore a 32 bit, scegliere il pacchetto di installazione "VpnClientSetupX86".
2. Fare doppio clic sul pacchetto per installarlo. Se viene visualizzato un popup SmartScreen, fare clic su Altre informazioni e quindi su Esegui comunque.
3. Nel computer client passare a Impostazioni di rete e fare clic su VPN. La connessione VPN viene visualizzata con il nome della rete virtuale a cui si connette.
4. Prima di tentare di connettersi, verificare che nel computer client sia installato un certificato client. Quando si usa il tipo di autenticazione del certificato di Azure nativo, è necessario un certificato client per l'autenticazione. Per altre informazioni sulla generazione di certificati, vedere Generare i certificati. Per informazioni sull'installazione di un certificato client, vedere Installare un certificato client.

## <a name="viewwan"></a>Visualizzare la rete WAN virtuale

1. Passare alla rete WAN virtuale.
2. Nella pagina Panoramica ogni punto sulla mappa rappresenta un hub. Passare il mouse su qualsiasi punto per visualizzare il riepilogo dell'integrità dell'hub.
3. Nella sezione degli hub e delle connessioni è possibile visualizzare lo stato dell'hub, il sito, l'area, lo stato della connessione VPN e i byte in entrata e in uscita.

## <a name="viewhealth"></a>Visualizzare l'integrità delle risorse

1. Passare alla rete WAN.
2. Nella pagina della rete WAN, nella sezione **Supporto e risoluzione dei problemi** fare clic su **Integrità** e visualizzare la risorsa.


## <a name="cleanup"></a>Pulire le risorse

Quando queste risorse non sono più necessarie, è possibile usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute. Sostituire "myResourceGroup" con il nome del gruppo di risorse specifico ed eseguire il comando di PowerShell seguente:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).