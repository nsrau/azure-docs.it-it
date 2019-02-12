---
title: Configurare una connessione gateway VPN da rete virtuale a rete virtuale con il portale di Azure | Microsoft Docs
description: Creare una connessione gateway VPN tra reti virtuali con Resource Manager e il portale di Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: 0646488c3dde4b0702d58bbd8905f4ae6bee1485
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821672"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Configurare una connessione gateway VPN da rete virtuale a rete virtuale con il portale di Azure

Questo articolo illustra come connettere reti virtuali usando il tipo di connessione da rete virtuale a rete virtuale. Le reti virtuali possono trovarsi in aree e sottoscrizioni diverse. Quando si connettono reti virtuali di sottoscrizioni diverse, non è necessario che le sottoscrizioni siano associate allo stesso tenant di Active Directory. 

![Diagramma V2V](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

I passaggi illustrati in questo articolo si applicano al modello di distribuzione Azure Resource Manager e usano il portale di Azure. È possibile creare questa configurazione con un diverso modello o strumento di distribuzione usando le opzioni descritte negli articoli seguenti:

> [!div class="op_single_selector"]
> * [Portale di Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Interfaccia della riga di comando di Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portale di Azure (classico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Connettersi tra modelli di distribuzione diversi - Portale di Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Connettersi tra modelli di distribuzione diversi - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>


## <a name="about-connecting-vnets"></a>Informazioni sulla connessione di reti virtuali

Le sezioni seguenti illustrano i diversi modi in cui è possibile connettere le reti virtuali.

### <a name="vnet-to-vnet"></a>Tra reti virtuali

Un modo semplice per connettere le reti virtuali consiste nel configurare una connessione da rete virtuale a rete virtuale. La connessione di una rete virtuale a un'altra con il tipo di connessione da rete virtuale a rete virtuale è simile alla creazione di una connessione IPsec da sito a sito a una posizione locale. Entrambi i tipi di connessione usano un gateway VPN per offrire un tunnel sicuro con IPsec/IKE e presentano lo stesso funzionamento durante la comunicazione. Differiscono tuttavia nel modo in cui viene configurato il gateway di rete locale. 

Quando si crea una connessione da rete virtuale a rete virtuale, lo spazio indirizzi del gateway di rete locale viene creato e popolato automaticamente. Se si aggiorna lo spazio indirizzi per una rete virtuale, l'altra rete virtuale verrà automaticamente indirizzata allo spazio indirizzi aggiornato. La creazione di una connessione da rete virtuale a rete virtuale è in genere più semplice e rapida rispetto alla creazione di una connessione da sito a sito.

### <a name="site-to-site-ipsec"></a>Da sito a sito (IPsec)

Se si usa una configurazione di rete complessa, potrebbe essere preferibile connettere le reti virtuali usando invece una [connessione da sito a sito](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Con la procedura di connessione IPsec da sito a sito, i gateway di rete locali vengono creati e configurati manualmente. Il gateway di rete locale per ogni rete virtuale considera l'altra rete virtuale come sito locale. Tale procedura consente di specificare spazi indirizzi aggiuntivi per il routing del traffico con il gateway di rete locale. In caso di modifica dello spazio indirizzi per una rete virtuale, è necessario aggiornare manualmente il gateway di rete locale corrispondente.

### <a name="vnet-peering"></a>Peering reti virtuali

È anche possibile connettere le reti virtuali con il peering reti virtuali. Il peering di reti virtuali non usa alcun gateway VPN e presenta vincoli diversi. I [prezzi per il peering reti virtuali](https://azure.microsoft.com/pricing/details/virtual-network) vengono inoltre calcolati in modo diverso rispetto ai [prezzi per il gateway VPN da rete virtuale a rete virtuale](https://azure.microsoft.com/pricing/details/vpn-gateway). Per altre informazioni, vedere [Peering reti virtuali](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Vantaggi di una connessione da rete virtuale a rete virtuale

Connettere le reti virtuali con una connessione da rete virtuale a rete virtuale può essere opportuno per i motivi illustrati di seguito.

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Presenza e ridondanza in più aree geografiche

  * È possibile configurare la sincronizzazione o la replica geografica con connettività sicura senza passare da endpoint con connessione Internet.
  * Con Gestione traffico di Azure e Azure Load Balancer, è possibile configurare un carico di lavoro a disponibilità elevata con ridondanza geografica in più aree di Azure. Ad esempio, si possono configurare gruppi di disponibilità Always On di SQL Server in più aree di Azure.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Applicazioni multilivello a livello di area con isolamento o limiti amministrativi

  * All'interno di una stessa area è possibile configurare applicazioni multilivello con più reti virtuali connesse tra loro, a causa di requisiti amministrativi o di isolamento.

La comunicazione tra reti virtuali può essere associata a configurazioni multisito. Tali configurazioni consentono di definire topologie di rete che combinano connettività cross-premise e connettività tra reti virtuali, come illustrato nel diagramma seguente:

![Informazioni sulle connessioni](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Informazioni sulle connessioni")

Questo articolo illustra come connettere reti virtuali con il tipo di connessione da rete virtuale a rete virtuale. Se si segue questa procedura come esercizio, si possono usare i valori delle impostazioni di esempio riportati di seguito. Nell'esempio, le reti virtuali sono nella stessa sottoscrizione, ma in gruppi di risorse diversi. Se le reti virtuali si trovano in sottoscrizioni diverse, non sarà possibile creare la connessione nel portale. Usare invece [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) o l'[interfaccia della riga di comando](vpn-gateway-howto-vnet-vnet-cli.md). Per altre informazioni sulle connessioni da rete virtuale a rete virtuale, vedere la sezione [Domande frequenti sulle connessioni da rete virtuale a rete virtuale](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Impostazioni di esempio

**Valori per TestVNet1:**

- **Impostazioni della rete virtuale**
    - **Nome**: immettere *TestVNet1*.
    - **Spazio indirizzi**: immettere *10.11.0.0/16*.
    - **Sottoscrizione** selezionare la sottoscrizione da usare.
    - **Gruppo di risorse**: immettere *TestRG1*.
    - **Località**: Selezionare **Stati Uniti orientali**.
    - **Subnet**
        - **Nome**: immettere *FrontEnd*.
        - **Intervallo di indirizzi**: immettere *10.11.0.0/24*.
    - **Subnet del gateway**
        - **Nome**: *GatewaySubnet*, inserito automaticamente.
        - **Intervallo di indirizzi**: immettere *10.11.255.0/27*.
    - **Server DNS**: selezionare **Personalizzato** e immettere l'indirizzo IP del proprio server DNS.

- **Impostazioni del gateway di rete virtuale** 
    - **Nome**: immettere *TestVNet1GW*.
    - **Tipo di gateway**: Selezionare **VPN**.
    - **Tipo di VPN**: Selezionare **Basato su route**.
    - **SKU**: selezionare lo SKU di gateway da usare.
    - **Nome indirizzo IP pubblico**: immettere *TestVNet1GWIP*.
    - **Connection** 
       - **Nome**: immettere *TestVNet1toTestVNet4*.
       - **Chiave condivisa**: immettere *abc123*. È possibile creare personalmente la chiave condivisa. Quando si crea la connessione tra le reti virtuali, i valori devono corrispondere.

**Valori per TestVNet4:**

- **Impostazioni della rete virtuale**
   - **Nome**: immettere *TestVNet4*.
   - **Spazio indirizzi**: immettere *10.41.0.0/16*.
   - **Sottoscrizione** selezionare la sottoscrizione da usare.
   - **Gruppo di risorse**: immettere *TestRG4*.
   - **Località**: selezionare **Stati Uniti occidentali**.
   - **Subnet** 
      - **Nome**: immettere *FrontEnd*.
      - **Intervallo di indirizzi**: immettere *10.41.0.0/24*.
   - **GatewaySubnet** 
      - **Nome**: *GatewaySubnet*, inserito automaticamente.
      - **Intervallo di indirizzi**: immettere *10.41.255.0/27*.
   - **Server DNS**: selezionare **Personalizzato** e immettere l'indirizzo IP del proprio server DNS.

- **Impostazioni del gateway di rete virtuale** 
    - **Nome**: immettere *TestVNet4GW*.
    - **Tipo di gateway**: Selezionare **VPN**.
    - **Tipo di VPN**: Selezionare **Basato su route**.
    - **SKU**: selezionare lo SKU di gateway da usare.
    - **Nome indirizzo IP pubblico**: immettere *TestVNet4GWIP*.
    - **Connection** 
       - **Nome**: immettere *TestVNet4toTestVNet1*.
       - **Chiave condivisa**: immettere *abc123*. È possibile creare personalmente la chiave condivisa. Quando si crea la connessione tra le reti virtuali, i valori devono corrispondere.

## <a name="create-and-configure-testvnet1"></a>Creare e configurare TestVNet1
Se si ha già una rete virtuale, verificare che le impostazioni siano compatibili con la progettazione del gateway VPN. Prestare particolare attenzione alle subnet che potrebbero sovrapporsi ad altre reti. La connessione non funziona correttamente se le subnet si sovrappongono. Dopo aver configurato la rete virtuale con le impostazioni corrette, si può iniziare la procedura descritta nella sezione "Specificare un server DNS".

### <a name="to-create-a-virtual-network"></a>Per creare una rete virtuale
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="add-additional-address-space-and-create-subnets"></a>Aggiungere altri spazi degli indirizzi e creare subnet
Dopo aver creato la rete virtuale è possibile aggiungere altri spazi degli indirizzi e creare subnet.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="create-a-gateway-subnet"></a>Creare una subnet del gateway
Prima di creare un gateway per la rete virtuale, è necessario creare prima di tutto la subnet del gateway. La subnet del gateway contiene gli indirizzi IP usati dal gateway di rete virtuale. Se possibile, è consigliabile creare una subnet del gateway con un blocco CIDR /28 o /27 per fornire indirizzi IP sufficienti a soddisfare futuri requisiti di configurazione.

Se si crea questa configurazione come esercizio, per creare la subnet del gateway vedere le [impostazioni di esempio](#example-settings).

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Per creare una subnet del gateway
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="specify-a-dns-server-optional"></a>Specificare un server DNS (facoltativo)
Per le connessioni da rete virtuale a rete virtuale non è necessario un server DNS. Specificarne uno, tuttavia, se si vuole ottenere la risoluzione dei nomi per le risorse distribuite nella rete virtuale. Questa impostazione consente di specificare il server DNS da usare per la risoluzione dei nomi nella rete virtuale. Non determina la creazione di un server DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="create-a-virtual-network-gateway"></a>Creare un gateway di rete virtuale
Questo passaggio illustra come creare il gateway di rete virtuale per la rete virtuale. La creazione di un gateway spesso richiede anche più di 45 minuti di tempo a seconda dello SKU gateway selezionato. Se si crea questa configurazione come esercizio, vedere [Impostazioni di esempio](#example-settings).

### <a name="to-create-a-virtual-network-gateway"></a>Per creare un gateway di rete virtuale
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="create-and-configure-testvnet4"></a>Creare e configurare TestVNet4
Dopo aver configurato TestVNet1, creare TestVNet4 ripetendo la procedura precedente e sostituendo i valori con quelli di TestVNet4. Per configurare TestVNet4 non è necessario attendere che sia completata la creazione del gateway di rete virtuale per TestVNet1. Se si usano valori personalizzati, verificare che gli spazi indirizzi non si sovrappongano alle reti virtuali a cui ci si vuole connettere.

## <a name="configure-the-testvnet1-gateway-connection"></a>Configurare la connessione gateway per TestVNet1
Dopo aver completato i gateway di rete virtuale per TestVNet1 e TestVNet4, è possibile creare le connessioni al gateway di rete virtuale. Questa sezione illustra come creare una connessione da VNet1 a VNet4. Questa procedura funziona solo per le reti virtuali nella stessa sottoscrizione. Se le reti virtuali si trovano in sottoscrizioni diverse, per stabilire la connessione è necessario usare [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). Se invece le reti virtuali si trovano in gruppi di risorse diversi nella stessa sottoscrizione, è possibile connetterle usando il portale.

1. Nel portale di Azure selezionare **Tutte le risorse**, immettere *gateway di rete virtuale* nella casella di ricerca e quindi passare al gateway per la rete virtuale desiderata. Ad esempio, **TestVNet1GW**. Selezionarlo per aprire la pagina **Gateway di rete virtuale**.

  ![Pagina Connessioni](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "Pagina Connessioni")
2. In **Impostazioni** selezionare **Connessioni** e quindi **Aggiungi** per aprire la pagina **Aggiungi connessione**.

  ![Aggiungi connessione](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "Aggiungi connessione")
3. Nella pagina **Aggiungi connessione** inserire i valori per la connessione.

   - **Nome**: immettere un nome per la connessione. Ad esempio, *TestVNet1toTestVNet4*.

   - **Tipo di connessione**: selezionare **Da rete virtuale a rete virtuale** nell'elenco a discesa.

   - **Primo gateway di rete virtuale**: il valore di questo campo viene inserito automaticamente perché la connessione viene creata dal gateway di rete virtuale specificato.

   - **Secondo gateway di rete virtuale**: questo campo corrisponde al gateway della rete virtuale a cui si vuole creare una connessione. Selezionare **Scegli un altro gateway di rete virtuale** per aprire la pagina **Scegliere un gateway di rete virtuale**.

    - Esaminare i gateway di rete virtuale visualizzati in questa pagina. Si noti che sono elencati solo i gateway di rete virtuale inclusi nella sottoscrizione. Per connettersi a un gateway di rete virtuale non incluso nella sottoscrizione, usare [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

    - Selezionare il gateway di rete virtuale a cui ci si vuole connettere.

    - **Chiave condivisa (PSK)**: in questo campo immettere una chiave condivisa per la connessione. La chiave può essere generata o creata dall'utente. In una connessione da sito a sito, per il dispositivo locale e la connessione del gateway di rete virtuale viene usata la stessa chiave. In questo caso si applica un concetto simile, ma anziché a un dispositivo VPN ci si connette a un altro gateway di rete virtuale.
    
4. Selezionare **OK** per salvare le modifiche.

## <a name="configure-the-testvnet4-gateway-connection"></a>Configurare la connessione gateway per TestVNet4
Successivamente, creare una connessione da TestVNet4 a TestVNet1. Nel portale individuare il gateway di rete virtuale associato a TestVNet4. Seguire i passaggi della sezione precedente, sostituendo i valori per creare una connessione da TestVNet4 a TestVNet1. Assicurarsi di usare la stessa chiave condivisa.

## <a name="verify-your-connections"></a>Verificare le connessioni

Individuare il gateway di rete virtuale nel portale di Azure. Nella pagina **Gateway di rete virtuale** selezionare **Connessioni** per visualizzare la pagina **Connessioni** per il gateway di rete virtuale. Dopo che la connessione è stata stabilita, i valori visualizzati sotto **Stato** diventeranno **Operazione completata** e **Connesso**. Selezionare una connessione per aprire la pagina **Informazioni di base** e visualizzare altre informazioni.

![Operazione riuscita](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Operazione riuscita")

Quando inizia il flusso dei dati, vengono visualizzati valori per **Dati in entrata** e **Dati in uscita**.

![Informazioni di base](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Informazioni di base")

## <a name="add-additional-connections"></a>Aggiungere altre connessioni

Per aggiungere altre connessioni, passare al gateway di rete virtuale da cui si vuole creare la connessione e quindi selezionare **Connessioni**. È possibile creare un'altra connessione da rete virtuale a rete virtuale o creare una connessione IPsec da sito a sito in un percorso locale. Assicurarsi di impostare **Tipo di connessione** in modo che corrisponda al tipo di connessione che si vuole creare. Prima di creare connessioni aggiuntive, verificare che lo spazio indirizzi della rete virtuale non si sovrapponga agli spazi indirizzi a cui ci si vuole connettere. Per la procedura per a una connessione da sito a sito, vedere [Creare una connessione da sito a sito](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>Domande frequenti relative alla connessione di reti virtuali
Visualizzare i dettagli delle frequenti per altre informazioni sulle connessioni da rete virtuale a rete virtuale.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come limitare il traffico di rete verso le risorse in una rete virtuale, vedere l'articolo relativo alla [sicurezza di rete](../virtual-network/security-overview.md).

Per informazioni sul modo in cui Azure instrada il traffico tra Azure, l'ambiente locale e le risorse Internet, vedere [Routing del traffico di rete virtuale](../virtual-network/virtual-networks-udr-overview.md).
