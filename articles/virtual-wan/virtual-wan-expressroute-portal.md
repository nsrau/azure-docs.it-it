---
title: Usare la rete WAN virtuale di Azure per creare connessioni ExpressRoute ad ambienti Azure e locali | Microsoft Docs
description: Questa esercitazione mostra come usare la rete WAN virtuale di Azure per creare connessioni ExpressRoute ad ambienti Azure e locali.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 8ad86280eab3041667bf9d1713ae2b4bc82a4c9e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491610"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Esercitazione: Creare un'associazione ExpressRoute con la rete WAN virtuale di Azure

Questa esercitazione illustra come usare la rete WAN virtuale per connettersi alle risorse in Azure in un circuito ExpressRoute. Per altre informazioni sulla rete WAN virtuale e sulle risorse WAN, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una rete WAN virtuale
> * Creare un hub e un gateway
> * Connettere una rete virtuale a un hub
> * Connettere un circuito a un gateway hub
> * Testare la connettività
> * Modificare le dimensioni del gateway
> * Creare una route predefinita

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

* Si ha un rete locale a cui ci si vuole collegare. Verificare che nessuna delle subnet della rete locale possa sovrapporsi alle reti virtuali a cui ci si vuole connettere. Per creare una rete virtuale nel portale di Azure, vedere l'[argomento di avvio rapido](../virtual-network/quick-create-portal.md).

* La rete virtuale non presenta nessun gateway di rete virtuale. Se la rete virtuale presenta un gateway (VPN o ExpressRoute) è necessario rimuovere tutti i gateway. Questa configurazione richiede che le reti virtuali siano invece connesse al gateway dell'hub rete WAN virtuale.

* Ottenere un intervallo di indirizzi IP per l'area dell'hub. L'hub è una rete virtuale che viene creata e usata dalla rete WAN virtuale. L'intervallo di indirizzi specificati per l'hub non può sovrapporsi ad alcuna delle reti virtuali esistenti a cui ci si connette. Inoltre non può sovrapporsi agli intervalli di indirizzi a cui ci si connette in locale. Se non si ha familiarità con gli intervalli degli indirizzi IP disponibili nella configurazione della rete locale, coordinarsi con qualcuno che possa fornire tali dettagli.

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="openvwan"></a>Creare una rete WAN virtuale

In un browser passare al [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

1. Passare alla pagina WAN virtuale. Nel portale fare clic su **+Crea una risorsa**. Digitare **Rete WAN virtuale** nella casella di ricerca e premere INVIO.
2. Selezionare **Rete WAN virtuale** nei risultati. Nella pagina della rete WAN virtuale fare clic su **Crea** per aprire la pagina Crea rete WAN.
3. Nella pagina **Crea rete WAN**, nella scheda **Nozioni di base**, compilare i campi seguenti:

   ![Crea rete WAN](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Sottoscrizione** - Selezionare la sottoscrizione che si vuole usare.
   * **Gruppo di risorse** - Creare un nuovo gruppo o usarne uno esistente.
   * **Posizione gruppo di risorse** - Scegliere una posizione per le risorse nell'elenco a discesa. Una rete WAN è una risorsa globale e non si trova in un'area specifica. Tuttavia, è necessario selezionare un'area per poter gestire e individuare più facilmente la risorsa WAN creata.
   * **Nome** - Digitare il nome da usare per la rete WAN.
   * **Tipo** - Selezionare **Standard**. Non è possibile creare un gateway ExpressRoute tramite lo SKU Basic.
4. Dopo aver completato i campi, selezionare**Rivedi e crea**.
5. Al termine della convalida, selezionare **Crea** per creare la rete WAN virtuale.

## <a name="hub"></a>Creare un hub virtuale e un gateway

Un hub virtuale è una rete virtuale che viene creata e usata dalla rete WAN virtuale. Può contenere vari gateway, ad esempio VPN ed ExpressRoute. In questa sezione si crea un gateway ExpressRoute per l'hub virtuale. È possibile creare il gateway con la [creazione di un nuovo hub virtuale](#newhub) o con la sua modifica in un [hub esistente](#existinghub). 

I gateway ExpressRoute vengono sottoposti a provisioning in unità da 2 Gbps. 1 unità di scala = 2 Gbps con supporto fino a 10 unità di scala = 20 Gbps. Occorrono circa 30 minuti per la creazione completa di un hub virtuale e di un gateway.

### <a name="newhub"></a>Per la creazione di un nuovo hub virtuale e un gateway

Creare un nuovo hub virtuale. Dopo aver creato un hub, verranno addebitati i relativi costi, anche se si non collega alcun sito.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="existinghub"></a>Per la creazione di un gateway in un hub esistente

È anche possibile creare un gateway in un hub esistente modificando il gateway stesso.

1. Passare all'hub virtuale che si vuole modificare e selezionarlo.
2. Nella pagina **Modifica hub virtuale** selezionare la casella di controllo **Includi gateway ExpressRoute**.
3. Selezionare **Conferma** per confermare le modifiche. Occorrono circa 30 minuti per la creazione completa dell'hub virtuale e delle risorse dell'hub.

   ![hub esistente](./media/virtual-wan-expressroute-portal/edithub.png "modifica un hub")

### <a name="to-view-a-gateway"></a>Per la visualizzazione di un gateway

Dopo aver creato un gateway ExpressRoute, è possibile visualizzare i dettagli dello stesso. Passare all'hub, selezionare **ExpressRoute** e visualizzare il gateway.

![Visualizza gateway](./media/virtual-wan-expressroute-portal/viewgw.png "visualizza gateway")

## <a name="connectvnet"></a>Connettere la rete virtuale a un hub

In questa sezione si crea la connessione di peering tra l'hub e una rete virtuale. Ripetere questi passaggi per ogni rete virtuale a cui ci si vuole connettere.

1. Nella pagina della rete WAN virtuale fare clic su **Connessione rete virtuale**.
2. Nella pagina di connessione alla rete virtuale fare clic su **+ Aggiungi connessione**.
3. Nella pagina **Aggiungi connessione** compilare i campi seguenti:

    * **Nome connessione** - Specificare un nome per la connessione.
    * **Hub** - Selezionare l'hub che si vuole associare a questa connessione.
    * **Sottoscrizione** - Verificare la sottoscrizione.
    * **Rete virtuale** - Selezionare la rete virtuale che si vuole connettere all'hub. La rete virtuale non può avere un gateway di rete virtuale già esistente (né VPN, né ExpressRoute).

## <a name="connectcircuit"></a>Connettere il circuito al gateway hub

Una volta creato il gateway, è possibile connettervi un [circuito ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md). Si noti che i circuiti Premium di ExpressRoute che si trovano in posizioni supportate da ExpressRoute a copertura globale, possono connettersi a un gateway ExpressRoute rete WAN virtuale.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Per la connessione del circuito al gateway hub

Nel portale passare alla pagina **Hub virtuale-> Connettività > ExpressRoute**. Se nella sottoscrizione si ha accesso a un circuito ExpressRoute, viene visualizzato il circuito che si vuole usare nell'elenco dei circuiti. Se non vengono visualizzati circuiti, ma questi sono stati specificati con una chiave di autorizzazione e un URI del circuito peer, è possibile riscattare e connettere un circuito. Vedere [Connettersi riscattando una chiave di autorizzazione](#authkey).

1. Selezionare il circuito.
2. Selezionare **Connetti circuito/i**.

   ![connetti circuiti](./media/virtual-wan-expressroute-portal/cktconnect.png "connetti circuiti")

### <a name="authkey"></a>Connettersi riscattando una chiave di autorizzazione

Usare chiave di autorizzazione e URI del circuito specificati per la connessione.

1. Nella pagina ExpressRoute fare clic su **+Riscatta la chiave di autorizzazione**

   ![redeem](./media/virtual-wan-expressroute-portal/redeem.png "redeem")
2. Nella pagina Riscatta la chiave di autorizzazione, inserire i valori.

   ![riscatta coppie chiave-valore](./media/virtual-wan-expressroute-portal/redeemkey2.png "riscatta coppie chiave-valore")
3. Selezionare **Aggiungi** per aggiungere la chiave.
4. Visualizzare il circuito. Un circuito riscattato mostra soltanto il nome (senza tipo, provider e altre informazioni) perché si trova in una sottoscrizione diversa rispetto a quella dell'utente.

## <a name="to-test-connectivity"></a>Per testare la connettività

Una volta stabilita la connessione al circuito, lo stato della connessione dell'hub indicherà "questo hub", il che implica che la connessione viene stabilita verso l'hub gateway ExpressRoute. Attendere circa 5 minuti prima di testare la connettività da un client dietro il circuito ExpressRoute, come ad esempio una macchina virtuale nella rete virtuale creata in precedenza.

Se i siti sono connessi a un gateway VPN rete WAN virtuale nello stesso hub gateway ExpressRoute, è possibile avere connettività bidirezionale tra gli endpoint VPN ed ExpressRoute. È supportato il routing dinamico (BGP). L'ASN dei gateway nell'hub è fisso e non può essere modificato in questo momento.

## <a name="to-change-the-size-of-a-gateway"></a>Per la modifica delle dimensioni di un gateway

Per modificare le dimensioni del gateway ExpressRoute, individuarlo all'interno dell'hub e selezionare le unità di scala nel menu a discesa. Salvare la modifica. L'aggiornamento del gateway hub richiede circa 30 minuti.

![modifica le dimensioni del gateway](./media/virtual-wan-expressroute-portal/changescale.png "modifica le dimensioni del gateway")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Per annunciare la route predefinita 0.0.0.0/0 agli endpoint

Se si vuole che l'hub virtuale di Azure annunci la route predefinita 0.0.0.0/0 agli endpoint di ExpressRoute è necessario abilitare "Propaga route predefinita".

1. Selezionare **Circuito ->...-> Modifica connessione**.

   ![Modifica connessione](./media/virtual-wan-expressroute-portal/defaultroute1.png "Modificare la connessione")

2. Selezionare **Abilita** per propagare la route predefinita.

   ![Propaga route predefinita](./media/virtual-wan-expressroute-portal/defaultroute2.png "Propaga route predefinita")

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).