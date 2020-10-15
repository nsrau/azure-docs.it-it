---
title: 'Configurare BGP per il gateway VPN: portale'
titleSuffix: Azure VPN Gateway
description: Questo articolo illustra i passaggi per configurare BGP con il gateway VPN di Azure tramite PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: f52d684d1e6ef63fdf4287c610608061f30395f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90996869"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways"></a>Come configurare BGP nei gateway VPN di Azure

Questo articolo illustra i passaggi per abilitare BGP in una connessione VPN da sito a sito (S2S) cross-premise e una connessione da VNet a VNet usando il portale di Azure.

## <a name="about-bgp"></a><a name="about"></a>Informazioni su BGP

BGP è il protocollo di routing standard comunemente usato in Internet per lo scambio di informazioni di routing e raggiungibilità tra due o più reti. BGP Abilita i gateway VPN di Azure e i dispositivi VPN locali, detti peer BGP o router adiacenti, allo scambio di "Route" che indicano a entrambi i gateway la disponibilità e la raggiungibilità di tali prefissi per il passaggio attraverso i gateway o i router necessari. BGP può anche abilitare il routing di transito tra più reti propagando a tutti gli altri peer BGP le route che un gateway BGP apprende da un peer BGP.

Per altre informazioni sui vantaggi di BGP e per comprendere i requisiti tecnici e le considerazioni sull'uso di BGP, vedere [Panoramica di BGP con i gateway VPN di Azure](vpn-gateway-bgp-overview.md).

## <a name="getting-started"></a>Guida introduttiva

Ogni parte di questo articolo consente di creare un blocco predefinito di base per abilitare BGP nella connettività di rete. Se si completano tutte e tre le parti, la topologia viene compilata come illustrato nel diagramma 1.

**Diagramma 1**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Diagramma che mostra l'architettura di rete e le impostazioni" border="false":::

È possibile combinare le parti per creare una rete di transito a più hop più complessa per soddisfare le proprie esigenze.

### <a name="prerequisites"></a>Prerequisiti

Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

## <a name="part-1-configure-bgp-on-the-virtual-network-gateway"></a><a name ="config"></a>Parte 1: configurare BGP nel gateway di rete virtuale

In questa sezione viene creata e configurata una rete virtuale, viene creato e configurato un gateway di rete virtuale con parametri BGP e viene ottenuto l'indirizzo IP del peer BGP di Azure. Il diagramma 2 Mostra le impostazioni di configurazione da usare quando si lavora con i passaggi descritti in questa sezione.

**Diagramma 2**

:::image type="content" source="./media/bgp-howto/bgp-gateway.png" alt-text="Diagramma che mostra l'architettura di rete e le impostazioni" border="false":::

### <a name="1-create-and-configure-testvnet1"></a>1. creare e configurare TestVNet1

In questo passaggio viene creato e configurato TestVNet1. Usare la procedura descritta nell' [esercitazione creare un gateway](vpn-gateway-tutorial-create-gateway-powershell.md) per creare e configurare la rete virtuale e il gateway VPN di Azure. Usare le impostazioni di riferimento negli screenshot seguenti.

* Rete virtuale

   :::image type="content" source="./media/bgp-howto/testvnet-1.png" alt-text="Diagramma che mostra l'architettura di rete e le impostazioni":::

* Subnet:

   :::image type="content" source="./media/bgp-howto/testvnet-1-subnets.png" alt-text="Diagramma che mostra l'architettura di rete e le impostazioni":::

### <a name="2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. creare il gateway VPN per TestVNet1 con i parametri BGP

In questo passaggio viene creato un gateway VPN con i parametri BGP corrispondenti.

1. Nella portale di Azure passare alla risorsa gateway di **rete virtuale** dal Marketplace e selezionare **Crea**.

1. Inserire i parametri come illustrato di seguito:

   :::image type="content" source="./media/bgp-howto/create-gateway-1.png" alt-text="Diagramma che mostra l'architettura di rete e le impostazioni":::

1. Nella sezione di **configurazione BGP** della pagina evidenziata configurare le impostazioni seguenti:

   :::image type="content" source="./media/bgp-howto/create-gateway-1-bgp.png" alt-text="Diagramma che mostra l'architettura di rete e le impostazioni":::

   * Selezionare **Configura BGP**  -  **abilitato** per visualizzare la sezione di configurazione BGP.

   * Compilare il numero di sistema autonomo (ASN).

   * Il campo **indirizzo IP BGP di Azure APIPA** è facoltativo. Se i dispositivi VPN locali usano l'indirizzo APIPA per BGP, è necessario selezionare un indirizzo dall'intervallo di indirizzi APIPA riservato da Azure per VPN, che è da **169.254.21.0** a **169.254.22.255**. Questo esempio USA 169.254.21.11.

   * Se si sta creando un gateway VPN attivo-attivo, nella sezione BGP verrà visualizzato un **altro secondo indirizzo IP BGP di Azure APIPA personalizzato**. Specificare un indirizzo diverso dall'intervallo di APIPA consentito (**169.254.21.0** a **169.254.22.255**).

   > [!IMPORTANT]
   >
   > * Per impostazione predefinita, Azure assegna automaticamente un indirizzo IP privato dall'intervallo di prefisso GatewaySubnet come indirizzo IP BGP di Azure nel gateway VPN di Azure. L'indirizzo BGP personalizzato di Azure APIPA è necessario quando i dispositivi VPN locali usano un indirizzo APIPA (169.254.0.1 to 169.254.255.254) come IP BGP. Il gateway VPN di Azure sceglierà l'indirizzo APIPA personalizzato se la risorsa gateway di rete locale corrispondente (rete locale) ha un indirizzo APIPA come IP peer BGP. Se il gateway di rete locale usa un normale indirizzo IP (non APIPA), il gateway VPN di Azure ripristinerà l'indirizzo IP privato dall'intervallo di GatewaySubnet.
   >
   > * Gli indirizzi BGP APIPA non devono sovrapporsi tra i dispositivi VPN locali e tutti i gateway VPN di Azure connessi.
   >

1. Selezionare **Verifica + crea** per eseguire la convalida. Al termine della convalida, selezionare **Crea** per distribuire il gateway VPN. Un gateway può richiedere fino a 45 minuti per la creazione e la distribuzione completa. È possibile visualizzare lo stato della distribuzione nella pagina Panoramica per il gateway.

### <a name="3-obtain-the-azure-bgp-peer-ip-addresses"></a>3. ottenere gli indirizzi IP del peer BGP di Azure

Una volta creato il gateway, è possibile ottenere gli indirizzi IP del peer BGP nel gateway VPN di Azure. Questi indirizzi sono necessari per configurare i dispositivi VPN locali per stabilire sessioni BGP con il gateway VPN di Azure.

1. Passare alla risorsa gateway di rete virtuale e selezionare la pagina **configurazione** per visualizzare le informazioni di configurazione BGP, come illustrato nello screenshot seguente. In questa pagina è possibile visualizzare tutte le informazioni di configurazione BGP nel gateway VPN di Azure: ASN, indirizzo IP pubblico e gli indirizzi IP del peer BGP corrispondenti sul lato Azure (predefinito e APIPA).

   :::image type="content" source="./media/bgp-howto/vnet-1-gw-bgp.png" alt-text="Diagramma che mostra l'architettura di rete e le impostazioni":::

1. Nella pagina **configurazione** è possibile apportare le modifiche di configurazione seguenti:

   * Se necessario, è possibile aggiornare il numero ASN o l'indirizzo IP BGP APIPA.
   * Se si dispone di un gateway VPN attivo-attivo, questa pagina visualizzerà gli indirizzi IP di indirizzo IP pubblico, predefinito e BGP BGP della seconda istanza del gateway VPN di Azure.

1. Se sono state apportate modifiche, selezionare **Salva** per eseguire il commit delle modifiche nel gateway VPN di Azure.

## <a name="part-2-configure-bgp-on-cross-premises-s2s-connections"></a><a name ="crosspremises"></a>Parte 2: configurare BGP sulle connessioni S2S cross-premise

Per stabilire una connessione cross-premise, è necessario creare un *gateway di rete locale* per rappresentare il dispositivo VPN locale e una *connessione* per connettere il gateway VPN al gateway di rete locale come descritto in creare una [connessione da sito a sito](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Questo articolo contiene le proprietà aggiuntive necessarie per specificare i parametri di configurazione BGP.

**Diagramma 3**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises.png" alt-text="Diagramma che mostra l'architettura di rete e le impostazioni" border="false":::

### <a name="1-configure-bgp-on-the-local-network-gateway"></a>1. configurare BGP nel gateway di rete locale

In questo passaggio viene configurato BGP nel gateway di rete locale. Usare lo screenshot seguente come esempio. La schermata mostra il gateway di rete locale (Site5) con i parametri specificati nel diagramma 3.

:::image type="content" source="./media/bgp-howto/create-local-bgp.png" alt-text="Diagramma che mostra l'architettura di rete e le impostazioni":::

#### <a name="important-configuration-considerations"></a>Considerazioni importanti sulla configurazione

* Il numero ASN e l'indirizzo IP del peer BGP devono corrispondere alla configurazione del router VPN locale.
* È possibile lasciare vuoto lo **spazio degli indirizzi** solo se si utilizza BGP per connettersi a questa rete. Il gateway VPN di Azure aggiunge internamente una route dell'indirizzo IP del peer BGP al tunnel IPsec corrispondente. Se **non** si usa il protocollo BGP tra il gateway VPN di Azure e questa rete particolare, è **necessario** fornire un elenco di prefissi di indirizzo validi per lo **spazio degli indirizzi**.
* Se necessario, è possibile usare facoltativamente un **indirizzo IP APIPA** (169.254. x. x) come IP peer BGP locale. Sarà anche necessario specificare un indirizzo IP APIPA come descritto in precedenza in questo articolo per il gateway VPN di Azure, in caso contrario la sessione BGP non può stabilire per questa connessione.
* È possibile immettere le informazioni di configurazione BGP durante la creazione del gateway di rete locale oppure è possibile aggiungere o modificare la configurazione BGP dalla pagina **configurazione** della risorsa gateway di rete locale.

**Esempio**

Questo esempio usa un indirizzo APIPA (169.254.100.1) come indirizzo IP del peer BGP locale:

:::image type="content" source="./media/bgp-howto/local-apipa.png" alt-text="Diagramma che mostra l'architettura di rete e le impostazioni":::

### <a name="2-configure-a-s2s-connection-with-bgp-enabled"></a>2. configurare una connessione S2S con BGP abilitato

In questo passaggio viene creata una nuova connessione in cui è abilitato BGP. Se si dispone già di una connessione e si desidera abilitare BGP, è possibile [aggiornare una connessione esistente](#update).

#### <a name="to-create-a-connection-with-bgp-enabled"></a>Per creare una connessione con BGP abilitato

Per creare una nuova connessione con BGP abilitato, nella pagina **Aggiungi connessione** compilare i valori, quindi selezionare l'opzione **Abilita BGP** per abilitare BGP sulla connessione. Selezionare **OK** per creare la connessione.

:::image type="content" source="./media/bgp-howto/ipsec-connection-bgp.png" alt-text="Diagramma che mostra l'architettura di rete e le impostazioni":::

#### <a name="to-update-an-existing-connection"></a><a name ="update"></a>Per aggiornare una connessione esistente

Se si vuole modificare l'opzione BGP in una connessione, passare alla pagina di **configurazione** della risorsa di connessione, quindi impostare l'opzione **BGP** come evidenziato nell'esempio seguente. Selezionare **Salva** per salvare le eventuali modifiche.

:::image type="content" source="./media/bgp-howto/update-bgp.png" alt-text="Diagramma che mostra l'architettura di rete e le impostazioni":::

## <a name="part-3-configure-bgp-on-vnet-to-vnet-connections"></a><a name ="v2v"></a>Parte 3: configurare BGP per le connessioni da VNet a VNet

I passaggi per abilitare o disabilitare BGP in una connessione da VNet a VNet sono gli stessi dei passaggi S2S della [parte 2](#crosspremises). È possibile abilitare BGP quando si crea la connessione o aggiornare la configurazione in una connessione da VNet a VNet esistente.

>[!NOTE] 
>Una connessione da VNet a VNet senza BGP limiterà la comunicazione solo ai due reti virtuali connessi. Abilitare BGP per consentire la funzionalità di routing di transito ad altre connessioni S2S o da VNet a VNet di questi due reti virtuali.
>

Per il contesto, che fa riferimento al **diagramma 4**, se BGP venisse disabilitato tra TestVNet2 e TestVNet1, TestVNet2 non apprendeva le route per la rete locale, Site5 e pertanto non poteva comunicare con il sito 5. Una volta abilitato BGP, come illustrato nel diagramma 4, tutte e tre le reti saranno in grado di comunicare tramite le connessioni IPsec e da VNet a VNet.

**Diagramma 4**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Diagramma che mostra l'architettura di rete e le impostazioni" border="false":::

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per i passaggi, vedere [Creare la prima macchina virtuale](../virtual-machines/windows/quick-create-portal.md) .
