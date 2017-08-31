---
title: 'Connettere una rete virtuale di Azure a un''altra rete virtuale: portale | Microsoft Docs'
description: Creare una connessione gateway VPN tra reti virtuali con Resource Manager e il portale di Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/02/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: 0293495a9cbdab1fc797d9948e4cbb7759b1ba54
ms.contentlocale: it-it
ms.lasthandoff: 08/03/2017

---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-the-azure-portal"></a>Configurare una connessione gateway VPN tra reti virtuali con il portale di Azure

Questo articolo descrive come creare una connessione gateway VPN tra reti virtuali. Le reti virtuali possono trovarsi in aree geografiche uguali o diverse e in sottoscrizioni uguali o diverse. Quando si connettono reti virtuali da sottoscrizioni diverse, non è necessario che le sottoscrizioni siano associate allo stesso tenant di Active Directory. 

La procedura illustrata in questo articolo si applica al modello di distribuzione Resource Manager e usano il portale di Azure. È anche possibile creare questa configurazione usando strumenti o modelli di distribuzione diversi selezionando un'opzione differente nell'elenco seguente:

> [!div class="op_single_selector"]
> * [Portale di Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Interfaccia della riga di comando di Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portale di Azure (classico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Connettersi tra modelli di distribuzione diversi - Portale di Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Connettersi tra modelli di distribuzione diversi - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagramma V2V](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

La connessione di una rete virtuale a un'altra rete virtuale (da rete virtuale a rete virtuale) è simile alla connessione di una rete virtuale a un percorso di sito locale. Entrambi i tipi di connettività utilizzano un gateway VPN per fornire un tunnel sicuro tramite IPsec/IKE. Se le reti virtuali si trovano nella stessa area, è consigliabile considerare la possibilità di connetterle tramite peering reti virtuali. Peering reti virtuali non usa un gateway VPN. Per altre informazioni, vedere [Peering reti virtuali](../virtual-network/virtual-network-peering-overview.md).

La comunicazione tra reti virtuali può essere associata a configurazioni multisito. In questo modo è possibile definire topologie di rete che consentono di combinare la connettività cross-premise con la connettività tra reti virtuali, come illustrato nel diagramma seguente:

![Informazioni sulle connessioni](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Informazioni sulle connessioni")

### <a name="why-connect-virtual-networks"></a>Perché connettere reti virtuali?

È possibile connettere reti virtuali per i seguenti motivi:

* **Presenza e ridondanza in più aree geografiche**
  
  * È possibile configurare la sincronizzazione o la replica geografica con connettività sicura senza passare da endpoint con connessione Internet.
  * Con Gestione traffico e il servizio di bilanciamento del carico di Azure è possibile configurare il carico di lavoro a disponibilità elevata con ridondanza geografica in più aree di Azure. Un esempio importante è rappresentato dalla configurazione SQL AlwaysOn con gruppi di disponibilità distribuiti in più aree di Azure.
* **Applicazioni multilivello in singole aree geografiche con isolamento o limite amministrativo**
  
  * All'interno di una stessa area è possibile configurare applicazioni multilivello con più reti virtuali connesse tra loro a causa dell'isolamento o di requisiti amministrativi.

Per altre informazioni sulle connessioni da rete virtuale a rete virtuale, vedere la sezione [Domande frequenti relative alla connessione da rete virtuale a rete virtuale](#faq) alla fine di questo articolo. Si noti che se le reti virtuali si trovano in sottoscrizioni diverse non sarà possibile creare la connessione nel portale. È possibile usare [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) o l'[interfaccia della riga di comando](vpn-gateway-howto-vnet-vnet-cli.md).

### <a name="values"></a>Impostazioni di esempio

Quando si segue questa procedura come esercizio, è possibile usare i valori delle impostazioni di esempio. A scopo esemplificativo, vengono usati più spazi di indirizzi per ogni rete virtuale, anche se per le configurazioni da rete virtuale a rete virtuale non sono necessari.

**Valori per TestVNet1:**

* Nome della rete virtuale: TestVNet1
* Spazio di indirizzi: 10.11.0.0/16
  * Nome subnet: FrontEnd
  * Intervallo di indirizzi subnet: 10.11.0.0/24
* Gruppo di risorse: TestRG1
* Location: Stati Uniti orientali
* Spazio di indirizzi: 10.12.0.0/16
  * Nome subnet: BackEnd
  * Intervallo di indirizzi subnet: 10.12.0.0/24
* Nome subnet del gateway: GatewaySubnet (compilato automaticamente nel portale)
  * Intervallo di indirizzi subnet del gateway: 10.11.255.0/27
* Server DNS: usare l'indirizzo IP del server DNS
* Nome gateway di rete virtuale: TestVNet1GW
* Tipo di gateway: VPN
* Tipo VPN: Basato su route
* SKU: selezionare lo SKU di gateway da usare
* Nome dell'indirizzo IP pubblico: TestVNet1GWIP
* Valori di connessione:
  * Nome: TestVNet1toTestVNet4
  * Chiave condivisa: è possibile creare personalmente la chiave condivisa. In questo esempio viene usato il valore abc123. Quando si crea la connessione tra le reti virtuali, è importante che il valore corrisponda.

**Valori per TestVNet4:**

* Nome della rete virtuale: TestVNet4
* Spazio di indirizzi: 10.41.0.0/16
  * Nome subnet: FrontEnd
  * Intervallo di indirizzi subnet: 10.41.0.0/24
* Gruppo di risorse: TestRG1
* Località: Stati Uniti occidentali
* Spazio di indirizzi: 10.42.0.0/16
  * Nome subnet: BackEnd
  * Intervallo di indirizzi subnet: 10.42.0.0/24
* Nome subnet del gateway: GatewaySubnet (compilato automaticamente nel portale)
  * Intervallo di indirizzi subnet del gateway: 10.41.255.0/27
* Server DNS: usare l'indirizzo IP del server DNS
* Nome gateway di rete virtuale: TestVNet4GW
* Tipo di gateway: VPN
* Tipo VPN: Basato su route
* SKU: selezionare lo SKU di gateway da usare
* Nome dell'indirizzo IP pubblico: TestVNet4GWIP
* Valori di connessione:
  * Nome: TestVNet4toTestVNet1
  * Chiave condivisa: è possibile creare personalmente la chiave condivisa. In questo esempio viene usato il valore abc123. Quando si crea la connessione tra le reti virtuali, è importante che il valore corrisponda.

## <a name="CreatVNet"></a>1. Creare e configurare TestVNet1
Se si ha già una rete virtuale, verificare che le impostazioni siano compatibili con la progettazione del gateway VPN. Prestare particolare attenzione alle subnet che potrebbero sovrapporsi ad altre reti. Se sono presenti subnet che si sovrappongono, la connessione non funziona correttamente. Se la rete virtuale è configurata con le impostazioni corrette, è possibile iniziare la procedura descritta nella sezione [Specificare un server DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Per creare una rete virtuale
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2. Aggiungere altri spazi degli indirizzi e creare subnet
Dopo aver creato la rete virtuale è possibile aggiungere altri spazi degli indirizzi e creare subnet.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. Creare una subnet del gateway
Prima di connettere la rete virtuale a un gateway, è necessario creare la subnet del gateway per la rete virtuale con cui si vuole stabilire la connessione. Se possibile, è consigliabile creare una subnet del gateway con un blocco CIDR di /28 o /27 per fornire indirizzi IP sufficienti a soddisfare altri requisiti di configurazione futuri.

Se si crea questa configurazione come esercizio, fare riferimento a queste [impostazioni di esempio](#values) quando si crea la subnet del gateway.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Per creare una subnet del gateway
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>4. Specificare un server DNS (facoltativo)
Il server DNS non è necessario per le connessioni da rete virtuale a rete virtuale. Se tuttavia si vuole ottenere la risoluzione dei nomi per le risorse distribuite nella rete virtuale, è necessario specificare un server DNS. Questa impostazione consente di specificare il server DNS da usare per la risoluzione dei nomi nella rete virtuale. Non comporta la creazione di un server DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. Creare un gateway di rete virtuale
Questo passaggio illustra come creare il gateway di rete virtuale per la rete virtuale. La creazione di un gateway spesso richiede anche più di 45 minuti di tempo a seconda dell'SKU gateway selezionato. Se si crea questa configurazione come esercizio, è possibile fare riferimento alle [impostazioni di esempio](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Per creare un gateway di rete virtuale
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6. Creare e configurare TestVNet4
Dopo aver configurato TestVNet1, creare TestVNet4 ripetendo i passaggi precedenti e sostituendo i valori con quelli di TestVNet4. Per configurare TestVNet4 non è necessario aspettare che sia terminata la creazione del gateway di rete virtuale per TestVNet1. Se si usano valori personalizzati, assicurarsi che gli spazi degli indirizzi non si sovrappongano alle reti virtuali a cui ci si vuole connettere.

## <a name="TestVNet1Connection"></a>7. Configurare la connessione per TestVNet1
Dopo aver completato i gateway di rete virtuale per TestVNet1 e TestVNet4, è possibile creare le connessioni al gateway di rete virtuale. Questa sezione illustra come creare una connessione da VNet1 a VNet4. Questa procedura funziona solo per le reti virtuali nella stessa sottoscrizione. Se le reti virtuali si trovano in diverse sottoscrizioni, è necessario usare PowerShell per creare la connessione. Vedere l'articolo su [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

1. In **Tutte le risorse** passare al gateway di rete virtuale per la rete virtuale. Ad esempio, **TestVNet1GW**. Fare clic su **TestVNet1GW** per aprire il pannello del gateway di rete virtuale.
   
    ![Pannello connessioni](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Pannello connessioni")
2. Fare clic su **+Aggiungi** per aprire il pannello **Aggiungi connessione**.
3. Nel pannello **Aggiungi connessione** digitare un nome per la connessione nel campo Nome. Ad esempio, **TestVNet1toTestVNet4**.
   
    ![Nome connessione](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Nome connessione")
4. Per **Tipo di connessione**, selezionare **Da rete virtuale a rete virtuale** nell'elenco a discesa.
5. Il valore del campo **Primo gateway di rete virtuale** viene compilato automaticamente, perché la connessione viene creata dal gateway di rete virtuale specificato.
6. Il campo **Secondo gateway di rete virtuale** corrisponde al gateway di rete virtuali della rete virtuale a cui si vuole creare una connessione. Fare clic su **Scegliere un altro gateway di rete virtuale** per aprire il pannello **Scegliere un gateway di rete virtuale**.
   
    ![Aggiungi connessione](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Aggiungi connessione")
7. Esaminare i gateway di rete virtuale visualizzati in questo pannello. Si noti che sono elencati solo i gateway di rete virtuale inclusi nella sottoscrizione. Per connettersi a un gateway di rete virtuale non incluso nella sottoscrizione, vedere l'[articolo relativo a PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). 
8. Fare clic sul gateway di rete virtuale a cui ci si vuole connettere.
9. Nel campo **Chiave condivisa** digitare una chiave condivisa per la connessione. La chiave può essere generata o creata dall'utente. Nel caso di una connessione da sito a sito si userebbe esattamente la stessa chiave sia per il dispositivo locale che per la connessione del gateway di rete virtuale. In questo caso si applica un concetto simile, ma anziché connettersi a un dispositivo VPN ci si connette a un altro gateway di rete virtuale.
   
    ![Chiave condivisa](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Chiave condivisa")
10. Fare clic su **OK** nella parte inferiore del pannello per salvare le modifiche.

## <a name="TestVNet4Connection"></a>8. Configurare la connessione per TestVNet4
Successivamente, creare una connessione da TestVNet4 a TestVNet1. Adottare lo stesso metodo usato per creare la connessione da TestVNet1 TestVNet4. Assicurarsi di usare la stessa chiave condivisa.

## <a name="VerifyConnection"></a>9. Verificare la connessione
Verificare la connessione. Per ogni gateway di rete virtuale, seguire questa procedura:

1. Trovare il pannello per il gateway di rete virtuale. Ad esempio, **TestVNet4GW**. 
2. Nel pannello per il gateway di rete virtuale fare clic su **Connessioni** per visualizzare il pannello delle connessioni per il gateway di rete virtuale.

Visualizzare le connessioni e verificare lo stato. Dopo aver creato la connessione, i valori di Stato visualizzati saranno **Operazione completata** e **Connesso**.

![Operazione riuscita](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Operazione riuscita")

Per visualizzare altre informazioni, è possibile fare doppio clic su ogni singola connessione.

![Informazioni di base](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Informazioni di base")

## <a name="faq"></a>Domande frequenti sulle connessioni da rete virtuale a rete virtuale
Visualizzare i dettagli delle frequenti per altre informazioni sulle connessioni da rete virtuale a rete virtuale.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali - Documentazione](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) .

