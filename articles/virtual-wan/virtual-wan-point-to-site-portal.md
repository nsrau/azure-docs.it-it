---
title: Usare la rete WAN virtuale di Azure per creare una connessione da punto a sito ad Azure | Microsoft Docs
description: In questa esercitazione si vedrà come usare la rete WAN virtuale di Azure per creare una connessione VPN da punto a sito ad Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 09/26/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect remote users to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 8a4c0c1426200e6c2d5041131fd0dd9cde4761cf
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47409287"
---
# <a name="tutorial-create-a-point-to-site-connection-using-azure-virtual-wan-preview"></a>Esercitazione: Creare una connessione da punto a sito con la rete WAN virtuale di Azure (anteprima)

Questa esercitazione illustra come usare la rete WAN virtuale per connettersi alle risorse in Azure tramite una connessione VPN IPSec/IKE (IKEv2) o OpenVPN. Questo tipo di connessione richiede la configurazione di un client nel computer client. Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una rete WAN
> * Creare una configurazione da punto a sito
> * Creare un hub
> * Applicare la configurazione da punto a sito a un hub
> * Connettere una rete virtuale a un hub
> * Scaricare e applicare la configurazione del client VPN
> * Visualizzare la rete WAN virtuale
> * Visualizzare lo stato di integrità delle risorse
> * Monitorare una connessione

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>Registrare questa funzionalità

Fare clic sul pulsante **Prova** per registrare facilmente questa funzionalità tramite Azure Cloud Shell.

>[!NOTE]
>Se non si registra la funzionalità, non sarà possibile usarla, né vederla nel portale.
>
>

Dopo aver fatto clic su **Prova** per aprire Azure Cloud Shell, copiare e incollare i comandi seguenti:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowP2SCortexAccess
```
 
```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowP2SCortexAccess
```

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Una volta che la funzionalità viene contrassegnata come registrata, registrare nuovamente la sottoscrizione nello spazio dei nomi Microsoft.Network.

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Crea rete virtuale

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. Creare una rete WAN virtuale

In un browser passare al [portale di Azure (anteprima)](http://aka.ms/azurevirtualwanpreviewfeatures) e accedere con l'account Azure.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Creare un hub

> [!NOTE]
> Non selezionare l'impostazione "Include point-to-site gateway" (Includi gateway da punto a sito) in questo passaggio.
>

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="site"></a>4. Creare una configurazione da punto a sito

Una configurazione da punto a sito definisce i parametri per la connessione di client remoti.

1. Passare a **Tutte le risorse**.
2. Fare clic sulla rete WAN virtuale creata.
3. In **Virtual WAN architecture** (Architettura di rete WAN virtuale) fare clic su **Point-to-site configurations** (Configurazioni da punto a sito).
4. Fare clic su **+Add point-to-site config** (Aggiungi configurazione da punto a sito) nella parte superiore della pagina per aprire la pagina **Create new point-to-site configuration** (Crea nuova configurazione da punto a sito).
5. Nella pagina **Create new point-to-site configuration** (Crea nuova configurazione da punto a sito) completare i campi seguenti:

  *  **Nome configurazione**: nome da usare per fare riferimento alla configurazione.
  *  **Tipo di tunnel**: protocollo da usare per il tunnel.
  *  **Pool di indirizzi**: pool di indirizzi IP da cui verranno assegnati gli IP ai client.
  *  **Root Certificate Name** (Nome certificato radice): nome descrittivo per il certificato.
  *  **Root Certificate Data** (Dati certificato radice): dati del certificato X.509 con codifica Base 64.

5. Fare clic su **Crea** per creare la configurazione.

## <a name="hub"></a>5. Modificare l'assegnazione dell'hub

1. Nella pagina della rete WAN virtuale fare clic su **Point-to-site configurations** (Configurazioni da punto a sito).
2. In **Hub** viene visualizzato un elenco di configurazioni che non sono ancora state connesse a un hub.
3. Selezionare la configurazione da associare e fare clic su **Edit hub assignment** (Modifica assegnazione dell'hub).
4. Dall'elenco a discesa selezionare uno o più hub a cui si vuole associare la configurazione.
5. Fare clic su **Assegna**. 
6. Il completamento dell'operazione potrà richiedere fino a 30 minuti.

## <a name="vnet"></a>6. Connettere la rete virtuale a un hub

In questo passaggio si crea la connessione di peering tra l'hub e una rete virtuale. Ripetere questi passaggi per ogni rete virtuale a cui ci si vuole connettere.

1. Nella pagina della rete WAN virtuale fare clic su **Connessione rete virtuale**.
2. Nella pagina di connessione alla rete virtuale fare clic su **+ Aggiungi connessione**.
3. Nella pagina **Aggiungi connessione** compilare i campi seguenti:

    * **Nome connessione** - Specificare un nome per la connessione.
    * **Hub** - Selezionare l'hub che si vuole associare a questa connessione.
    * **Sottoscrizione** - Verificare la sottoscrizione.
    * **Rete virtuale** - Selezionare la rete virtuale che si vuole connettere all'hub. La rete virtuale non può avere un gateway di rete virtuale già esistente.

## <a name="device"></a>7. Scaricare il profilo VPN

Usare il profilo VPN per configurare i client.

1. Nella pagina della rete WAN virtuale fare clic su **Point-to-site configurations** (Configurazioni da punto a sito).
2. Nella parte superiore della pagina fare clic su **Download point-to-site profile** (Scarica profilo da punto a sito). 
3. Al termine della creazione del file è possibile fare clic sul collegamento per scaricarlo.
4. Usare il file del profilo per configurare i client da punto a sito.

## <a name="device"></a>8. Configurare i client da punto a sito
Usare il profilo scaricato per configurare i client di accesso remoto. La procedura per ogni sistema operativo è diversa, seguire le istruzioni corrette riportate di seguito:

### <a name="microsoft-windows"></a>Microsoft Windows
#### <a name="openvpn"></a>OpenVPN

1.  Scaricare e installare il client OpenVPN dal sito Web ufficiale.
2.  Scaricare il profilo VPN per il gateway. Questa operazione può essere eseguita dalla scheda Point-to-site configurations (Configurazioni da punto a sito) nel portale di Azure o tramite New-AzureRmVpnClientConfiguration in PowerShell.
3.  Decomprimere il profilo. Aprire il file di configurazione vpnconfig.ovpn dalla cartella OpenVPN nel Blocco note.
4.  Completare la sezione relativa al certificato client da punto a sito con la chiave pubblica del certificato client da punto a sito in formato Base 64. In un certificato in formato PEM è possibile aprire semplicemente il file con estensione cer e copiare la chiave in formato Base 64 tra le intestazioni del certificato. Sono disponibili altre informazioni su come esportare un certificato per ottenere la chiave pubblica codificata.
5.  Completare la sezione relativa alla chiave privata con la chiave privata del certificato client da punto a sito in formato Base 64. Sono disponibili altre informazioni su come estrarre la chiave privata.
6.  Lasciare invariati tutti gli altri campi. Usare la configurazione così completata nell'input del client per connettersi alla rete VPN.
7.  Copiare il file vpnconfig.ovpn nella cartella C:\Programmi\OpenVPN\config.
8.  Fare clic con il pulsante destro del mouse sull'icona OpenVPN nell'area di notifica e fare clic su Connetti.

#### <a name="ikev2"></a>IKEv2

1. Selezionare i file di configurazione del client VPN corrispondenti all'architettura del computer Windows. Per un'architettura con processore a 64 bit, scegliere il pacchetto di installazione "VpnClientSetupAmd64". Per un'architettura con processore a 32 bit, scegliere il pacchetto di installazione "VpnClientSetupX86".
2. Fare doppio clic sul pacchetto per installarlo. Se viene visualizzato un popup SmartScreen, fare clic su Altre informazioni e quindi su Esegui comunque.
3. Nel computer client passare a Impostazioni di rete e fare clic su VPN. La connessione VPN viene visualizzata con il nome della rete virtuale a cui si connette.
4. Prima di tentare di connettersi, verificare che nel computer client sia installato un certificato client. Quando si usa il tipo di autenticazione del certificato di Azure nativo, è necessario un certificato client per l'autenticazione. Per altre informazioni sulla generazione di certificati, vedere Generare i certificati. Per informazioni sull'installazione di un certificato client, vedere Installare un certificato client.

### <a name="mac-os-x"></a>Mac (OS X)
#### <a name="openvpn"></a>OpenVPN

1.  Scaricare e installare un client OpenVPN, ad esempio TunnelBlik da https://tunnelblick.net/downloads.html 
2.  Scaricare il profilo VPN per il gateway. Questa operazione può essere eseguita dalla scheda Point-to-site configuration (Configurazione da punto a sito) nel portale di Azure o tramite New-AzureRmVpnClientConfiguration in PowerShell.
3.  Decomprimere il profilo. Aprire il file di configurazione vpnconfig.ovpn dalla cartella OpenVPN nel Blocco note.
4.  Completare la sezione relativa al certificato client da punto a sito con la chiave pubblica del certificato client da punto a sito in formato Base 64. In un certificato in formato PEM è possibile aprire semplicemente il file con estensione cer e copiare la chiave in formato Base 64 tra le intestazioni del certificato. Sono disponibili altre informazioni su come esportare un certificato per ottenere la chiave pubblica codificata.
5.  Completare la sezione relativa alla chiave privata con la chiave privata del certificato client da punto a sito in formato Base 64. Sono disponibili altre informazioni su come estrarre la chiave privata.
6.  Lasciare invariati tutti gli altri campi. Usare la configurazione così completata nell'input del client per connettersi alla rete VPN.
7.  Fare doppio clic sul file del profilo per creare il profilo in TunnelBlik.
8.  Avviare Tunnelblik dalla cartella applicazioni.
9.  Fare clic sull'icona TunnelBlik nell'area di notifica e selezionare Connetti.

#### <a name="ikev2"></a>IKEv2

Azure non fornisce il file mobileconfig per l'autenticazione del certificato di Azure nativo. È necessario configurare manualmente il client VPN IKEv2 nativo in ogni Mac che si connetterà ad Azure. La cartella Generic contiene tutte le informazioni necessarie per la configurazione. Se la cartella Generic non viene visualizzata nel download, è probabile che non sia stato selezionato IKEv2 come tipo di tunnel. Dopo la selezione di IKEv2, generare di nuovo il file con estensione zip per recuperare la cartella Generic. La cartella Generic contiene i file seguenti:

- VpnSettings.xml, che contiene impostazioni importanti come l'indirizzo del server e il tipo di tunnel.
- VpnServerRoot.cer, che contiene il certificato radice necessario per convalidare il gateway VPN di Azure durante la configurazione della connessione da punto a sito.

## <a name="viewwan"></a>9. Visualizzare la rete WAN virtuale

1. Passare alla rete WAN virtuale.
2. Nella pagina Panoramica ogni punto sulla mappa rappresenta un hub. Passare il mouse su qualsiasi punto per visualizzare il riepilogo dell'integrità dell'hub.
3. Nella sezione degli hub e delle connessioni è possibile visualizzare lo stato dell'hub, il sito, l'area, lo stato della connessione VPN e i byte in entrata e in uscita.

## <a name="viewhealth"></a>10. Visualizzare l'integrità delle risorse

1. Passare alla rete WAN.
2. Nella pagina della rete WAN, nella sezione **Supporto e risoluzione dei problemi** fare clic su **Integrità** e visualizzare la risorsa.

## <a name="connectmon"></a>11. Monitorare una connessione

Creare una connessione per monitorare la comunicazione tra una macchina virtuale di Azure e un sito remoto. Per informazioni su come configurare un monitoraggio della connessione, vedere [Monitorare la comunicazione di rete](~/articles/network-watcher/connection-monitor.md). Il campo di origine è l'IP della macchina virtuale in Azure e l'IP di destinazione è l'indirizzo IP del sito.

## <a name="cleanup"></a>12. Pulire le risorse

Quando queste risorse non sono più necessarie, è possibile usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute. Sostituire "myResourceGroup" con il nome del gruppo di risorse specifico ed eseguire il comando di PowerShell seguente:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare una rete WAN
> * Creare un sito
> * Creare un hub
> * Connettere un hub a un sito
> * Connettere una rete virtuale a un hub
> * Scaricare e applicare la configurazione del dispositivo VPN
> * Visualizzare la rete WAN virtuale
> * Visualizzare lo stato di integrità delle risorse
> * Monitorare una connessione

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).