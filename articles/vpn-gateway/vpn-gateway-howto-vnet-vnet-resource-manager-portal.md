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
ms.date: 11/15/2017
ms.author: cherylmc
ms.openlocfilehash: b2da1c7148e27ca8dd8eb774d4201415a969fada
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2017
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

La connessione di una rete virtuale a un'altra rete virtuale (da rete virtuale a rete virtuale) è simile alla connessione di una rete virtuale a un percorso di sito locale. Entrambi i tipi di connettività utilizzano un gateway VPN per fornire un tunnel sicuro tramite IPsec/IKE. È possibile creare connessioni da sito a sito (IPsec) tra reti virtuali, invece che da rete virtuale a rete virtuale. Entrambi i tipi di connessione funzionano nello stesso modo durante la comunicazione. Quando tuttavia si crea una connessione da rete virtuale a rete virtuale, se si aggiorna lo spazio indirizzi per una rete virtuale, l'altra rete virtuale eseguirà automaticamente l'instradamento allo spazio indirizzi aggiornato. Se si creano connessioni da sito a sito (IPsec), è necessario configurare manualmente lo spazio indirizzi per il gateway di rete locale. Quando si usano configurazioni complesse, può essere preferibile creare connessioni IPsec, invece che da rete virtuale a rete virtuale. In questo modo è possibile specificare manualmente spazio indirizzi aggiuntivo per il gateway di rete locale.

Se le reti virtuali si trovano nella stessa area, è consigliabile considerare la possibilità di connetterle tramite peering reti virtuali. Peering reti virtuali non usa un gateway VPN. Per altre informazioni, vedere [Peering reti virtuali](../virtual-network/virtual-network-peering-overview.md).

La comunicazione tra reti virtuali può essere associata a configurazioni multisito. In questo modo è possibile definire topologie di rete che consentono di combinare la connettività cross-premise con la connettività tra reti virtuali, come illustrato nel diagramma seguente:

![Informazioni sulle connessioni](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Informazioni sulle connessioni")

### <a name="why-connect-virtual-networks"></a>Perché connettere reti virtuali?

È possibile connettere reti virtuali per i seguenti motivi:

* **Presenza e ridondanza in più aree geografiche**

  * È possibile configurare la sincronizzazione o la replica geografica con connettività sicura senza passare da endpoint con connessione Internet.
  * Con Gestione traffico e il servizio di bilanciamento del carico di Azure è possibile configurare il carico di lavoro a disponibilità elevata con ridondanza geografica in più aree di Azure. Un esempio importante è rappresentato dalla configurazione SQL AlwaysOn con gruppi di disponibilità distribuiti in più aree di Azure.
* **Applicazioni multilivello in singole aree geografiche con isolamento o limite amministrativo**

  * All'interno di una stessa area è possibile configurare applicazioni multilivello con più reti virtuali connesse tra loro a causa dell'isolamento o di requisiti amministrativi.

Quando si segue questa procedura come esercizio, è possibile usare i valori delle impostazioni di esempio. Nell'esempio, le reti virtuali sono nella stessa sottoscrizione, ma in gruppi di risorse diversi. Se le reti virtuali si trovano in sottoscrizioni diverse, non sarà possibile creare la connessione nel portale. È possibile usare [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) o l'[interfaccia della riga di comando](vpn-gateway-howto-vnet-vnet-cli.md). Per altre informazioni sulle connessioni da rete virtuale a rete virtuale, vedere la sezione [Domande frequenti relative alla connessione da rete virtuale a rete virtuale](#faq) alla fine di questo articolo.

### <a name="values"></a>Impostazioni di esempio

**Valori per TestVNet1:**

* Nome della rete virtuale: TestVNet1
* Spazio di indirizzi: 10.11.0.0/16
* Sottoscrizione: selezionare la sottoscrizione che si vuole usare
* Gruppo di risorse: TestRG1
* Location: Stati Uniti orientali
* Nome subnet: FrontEnd
* Intervallo di indirizzi subnet: 10.11.0.0/24
* Nome subnet del gateway: GatewaySubnet (compilato automaticamente nel portale)
* Intervallo di indirizzi subnet del gateway: 10.11.255.0/27
* Server DNS: usare l'indirizzo IP del server DNS
* Nome gateway di rete virtuale: TestVNet1GW
* Tipo di gateway: VPN
* Tipo VPN: Basato su route
* SKU: selezionare lo SKU di gateway da usare
* Nome dell'indirizzo IP pubblico: TestVNet1GWIP
* Nome connessione: TestVNet1toTestVNet4
* Chiave condivisa: è possibile creare personalmente la chiave condivisa. In questo esempio viene usato il valore abc123. Quando si crea la connessione tra le reti virtuali, è importante che il valore corrisponda.

**Valori per TestVNet4:**

* Nome della rete virtuale: TestVNet4
* Spazio di indirizzi: 10.41.0.0/16
* Sottoscrizione: selezionare la sottoscrizione che si vuole usare
* Gruppo di risorse: TestRG4
* Località: Stati Uniti occidentali
* Nome subnet: FrontEnd
* Intervallo di indirizzi subnet: 10.41.0.0/24
* Nome subnet del gateway: GatewaySubnet (compilato automaticamente nel portale)
* Intervallo di indirizzi subnet del gateway: 10.41.255.0/27
* Server DNS: usare l'indirizzo IP del server DNS
* Nome gateway di rete virtuale: TestVNet4GW
* Tipo di gateway: VPN
* Tipo VPN: Basato su route
* SKU: selezionare lo SKU di gateway da usare
* Nome dell'indirizzo IP pubblico: TestVNet4GWIP
* Nome connessione: TestVNet4toTestVNet1
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

## <a name="TestVNet1Connection"></a>7. Configurare la connessione gateway per TestVNet1
Dopo aver completato i gateway di rete virtuale per TestVNet1 e TestVNet4, è possibile creare le connessioni al gateway di rete virtuale. Questa sezione illustra come creare una connessione da VNet1 a VNet4. Questa procedura funziona solo per le reti virtuali nella stessa sottoscrizione. Se le reti virtuali si trovano in diverse sottoscrizioni, è necessario usare PowerShell per creare la connessione. Vedere l'articolo su [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). Se tuttavia le reti virtuali si trovano in gruppi di risorse diversi nella stessa sottoscrizione, è possibile connetterle usando il portale.

1. In **Tutte le risorse** passare al gateway di rete virtuale per la rete virtuale. Ad esempio, **TestVNet1GW**. Fare clic su **TestVNet1GW** per aprire la pagina del gateway di rete virtuale.

  ![Pagina Connessioni](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "Pagina Connessioni")
2. Fare clic su **+Aggiungi** per aprire la pagina **Aggiungi connessione**.

  ![Aggiungi connessione](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "Aggiungi connessione")
3. Nella pagina **Aggiungi connessione** digitare un nome per la connessione nel campo Nome. Ad esempio, **TestVNet1toTestVNet4**.
4. In **Tipo di connessione** selezionare **Da rete virtuale a rete virtuale** nell'elenco a discesa.
5. Il valore del campo **Primo gateway di rete virtuale** viene compilato automaticamente, perché la connessione viene creata dal gateway di rete virtuale specificato.
6. Il campo **Secondo gateway di rete virtuale** corrisponde al gateway di rete virtuali della rete virtuale a cui si vuole creare una connessione. Fare clic su **Scegliere un altro gateway di rete virtuale** per aprire la pagina **Scegliere un gateway di rete virtuale**.
7. Esaminare i gateway di rete virtuale visualizzati in questa pagina. Si noti che sono elencati solo i gateway di rete virtuale inclusi nella sottoscrizione. Per connettersi a un gateway di rete virtuale non incluso nella sottoscrizione, vedere l'[articolo relativo a PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).
8. Fare clic sul gateway di rete virtuale a cui ci si vuole connettere.
9. Nel campo **Chiave condivisa** digitare una chiave condivisa per la connessione. La chiave può essere generata o creata dall'utente. Nel caso di una connessione da sito a sito si userebbe esattamente la stessa chiave sia per il dispositivo locale che per la connessione del gateway di rete virtuale. In questo caso si applica un concetto simile, ma anziché connettersi a un dispositivo VPN ci si connette a un altro gateway di rete virtuale.
10. Fare clic su **OK** nella parte inferiore della pagina per salvare le modifiche.

## <a name="TestVNet4Connection"></a>8. Configurare la connessione gateway per TestVNet4
Successivamente, creare una connessione da TestVNet4 a TestVNet1. Nel portale individuare il gateway di rete virtuale associato a TestVNet4. Seguire i passaggi della sezione precedente, sostituendo i valori per creare una connessione da TestVNet4 a TestVNet1. Assicurarsi di usare la stessa chiave condivisa.

## <a name="VerifyConnection"></a>9. Verificare le connessioni

Trovare il gateway di rete virtuale nel portale. Nella pagina del gateway di rete virtuale fare clic su **Connessioni** per visualizzare la pagina delle connessioni per il gateway di rete virtuale. Dopo aver stabilito la connessione, i valori di Stato visualizzati diventeranno **Operazione completata** e **Connesso**. È possibile fare doppio clic su una connessione per aprire la pagina **Informazioni di base** e visualizzare altre informazioni.

![Operazione riuscita](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Operazione riuscita")

Quando inizia il flusso dei dati, vengono visualizzati i valori per Dati in entrata e Dati in uscita.

![Informazioni di base](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Informazioni di base")

## <a name="to-add-additional-connections"></a>Per aggiungere altre connessioni

Per aggiungere altre connessioni, passare al gateway di rete virtuale da cui si vuole creare la connessione, quindi fare clic su **Connessioni**. È possibile creare un'altra connessione da rete virtuale a rete virtuale o creare una connessione IPsec da sito a sito in un percorso locale. Assicurarsi di impostare **Tipo di connessione** in modo che corrisponda al tipo di connessione che si vuole creare. Prima di creare connessioni aggiuntive, verificare che lo spazio indirizzi della rete virtuale non si sovrapponga a nessuno degli spazi indirizzi a cui si vuole creare la connessione. Per la procedura per a una connessione da sito a sito, vedere [Creare una connessione da sito a sito](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="faq"></a>Domande frequenti sulle connessioni da rete virtuale a rete virtuale
Visualizzare i dettagli delle frequenti per altre informazioni sulle connessioni da rete virtuale a rete virtuale.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come limitare il traffico di rete verso le risorse in una rete virtuale, vedere [Sicurezza di rete](../virtual-network/security-overview.md).

Per informazioni sul modo in cui Azure instrada il traffico tra Azure, l'ambiente locale e le risorse Internet, vedere [Routing del traffico di rete virtuale](../virtual-network/virtual-networks-udr-overview.md).
