---
title: 'Creare una connessione tra reti virtuali: classica: portale di Azure'
description: Connettere tra loro diverse reti virtuali di Azure tramite PowerShell e il portale di Azure.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 0d81e0474d898ffee7f128c0bcea61f077c3d758
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103221"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Configurare una connessione da rete virtuale a rete virtuale (versione classica)

Questo articolo consente di creare una connessione gateway VPN tra reti virtuali. Le reti virtuali possono trovarsi in aree geografiche uguali o diverse e in sottoscrizioni uguali o diverse.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png" alt-text="Diagramma che mostra l'architettura classica da VNet a VNet":::

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

I passaggi di questo articolo sono applicabili al modello di distribuzione classico e al portale di Azure. È anche possibile creare questa configurazione usando strumenti o modelli di distribuzione diversi selezionando un'opzione differente nell'elenco seguente:

> [!div class="op_single_selector"]
> * [Classico](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Resource Manager](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Connettere reti virtuali in modelli di distribuzione diversi](vpn-gateway-connect-different-deployment-models-portal.md)
>
>

## <a name="about-vnet-to-vnet-connections"></a>Informazioni sulla connessione da rete virtuale a rete virtuale

La connessione di una rete virtuale a un'altra rete virtuale nel modello di distribuzione classico che usa un gateway VPN è simile alla connessione di una rete virtuale a un percorso di sito locale. Entrambi i tipi di connettività utilizzano un gateway VPN per fornire un tunnel sicuro tramite IPsec/IKE.

Le reti virtuali possono trovarsi in diverse sottoscrizioni e aree geografiche diverse. È possibile combinare una comunicazione da rete virtuale a rete virtuale con configurazioni multisito. Questo permette di definire topologie di rete che consentono di combinare la connettività cross-premise con la connettività tra reti virtuali.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png" alt-text="Diagramma che mostra l'architettura classica da VNet a VNet":::

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Perché connettere reti virtuali?

È possibile connettere reti virtuali per i seguenti motivi:

* **Presenza e ridondanza in più aree geografiche**

  * È possibile configurare la sincronizzazione o la replica geografica con connettività sicura senza passare da endpoint con connessione Internet.
  * Con Azure Load Balancer e una tecnologia di clustering Microsoft o di terze parti, è possibile configurare il carico di lavoro a disponibilità elevata con ridondanza geografica in più aree di Azure. Un esempio importante è rappresentato dalla configurazione SQL AlwaysOn con gruppi di disponibilità distribuiti in più aree di Azure.
* **Applicazioni multilivello in singole aree geografiche con alto grado di isolamento**

  * Nella stessa area, è possibile configurare applicazioni multilivello con più reti virtuali collegate tra loro tramite forte isolamento e comunicazione tra livelli sicura.
* **Comunicazione tra sottoscrizioni e organizzazioni in Azure**

  * Se si dispone di più sottoscrizioni di Azure, si possono connettere tra loro i carichi di lavoro di sottoscrizioni diverse in modo sicuro tra reti virtuali.
  * Per le aziende o i provider di servizi, è ora possibile abilitare la comunicazione tra organizzazioni con tecnologia VPN sicura in Azure.

Per altre informazioni sulle connessioni da rete virtuale a rete virtuale, vedere la sezione [Considerazioni sulle connessioni da rete virtuale a rete virtuale](#faq) alla fine di questo articolo.

## <a name="prerequisites"></a>Prerequisiti

Il portale viene utilizzato per la maggior parte dei passaggi, ma per creare connessioni tra VNet, occorre utilizzare PoweShell. Non è possibile creare le connessioni usando il portale di Azure perché non è possibile specificare la chiave condivisa nel portale. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="planning"></a><a name="planning"></a>Pianificazione

È importante stabilire gli intervalli che verranno usati per configurare le reti virtuali. Per questa configurazione, controllare che nessun intervallo di rete virtuale si sovrapponga a un altro o a una delle reti locali alle quali si connette.

### <a name="vnets"></a><a name="vnet"></a>Reti virtuali

Per questo esercizio, vengono usati i valori di esempio seguenti:

**Valori per TestVNet1**

Nome: TestVNet1<br>
Spazio di indirizzi: 10.11.0.0/16, 10.12.0.0/16 (facoltativo)<br>
Subnet name: predefinito<br>
Intervallo di indirizzi subnet: 10.11.0.0/24<br>
Gruppo di risorse: ClassicRG<br>
Location: Stati Uniti orientali<br>
GatewaySubnet: 10.11.1.0/27

**Valori per TestVNet4**

Nome: TestVNet4<br>
Spazio di indirizzi: 10.41.0.0/16, 10.42.0.0/16 (facoltativo)<br>
Subnet name: predefinito<br>
Intervallo di indirizzi subnet: 10.41.0.0/24<br>
Gruppo di risorse: ClassicRG<br>
Località: Stati Uniti occidentali<br>
GatewaySubnet: 10.41.1.0/27

### <a name="connections"></a><a name="plan"></a>Connessioni

La tabella seguente illustra un esempio di come si connetterà la reti virtuali. Usare gli intervalli solo come linee guida. Annotare gli intervalli per le reti virtuali. Queste informazioni sono necessarie per i passaggi successivi.

In questo esempio, TestVNet1 si connette a un sito di rete locale creato con il nome "VNet4Local". Le impostazioni per VNet4Local contengono i prefissi di indirizzo per TestVNet4.
Il sito locale per ogni rete virtuale è l'altra rete virtuale. Per la configurazione vengono usati i valori nell'esempio seguente:

**Esempio**

| Rete virtuale | Spazio di indirizzi | Location | Si connette al sito della rete locale |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Stati Uniti orientali |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Stati Uniti occidentali |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="create-virtual-networks"></a><a name="vnetvalues"></a>Creare reti virtuali

In questo passaggio vengono create due reti virtuali classiche, TestVNet1 e TestVNet4. Se si usa questo articolo come esercizio, usare i valori di [esempio](#vnet).

**Quando si creano le reti virtuali, tenere presenti le seguenti impostazioni:**

* **Spazi di indirizzi della rete virtuale**: nella pagina Spazi di indirizzi della rete virtuale specificare l'intervallo di indirizzi che si desidera usare per la rete virtuale. Questi sono gli indirizzi IP dinamici che verranno assegnati alle macchine virtuali e alle istanze di altri ruoli distribuiti nella rete virtuale.<br>Gli spazi di indirizzi selezionati non possono sovrapporsi agli spazi di indirizzi delle altre reti virtuali o delle posizioni in locale a cui si connetterà la rete virtuale.

* **Località**. Quando viene creata, una rete virtuale viene associata a una località di Azure (regione). Ad esempio, se si desidera che le macchine virtuali distribuite nella rete virtuale vengano posizionate fisicamente in Stati Uniti occidentali, selezionare tale posizione. È possibile modificare il percorso associato alla rete virtuale dopo averla creata.

**Dopo aver creato le reti virtuali, è possibile aggiungere le seguenti impostazioni:**

* **Spazio indirizzi**: per questa configurazione non sono necessari altri spazi degli indirizzi, ma è possibile aggiungere altri spazi degli indirizzi dopo aver creato la rete virtuale.

* **Subnet** : per questa configurazione non sono necessarie altre subnet, ma è possibile collocare le macchine virtuali in una subnet separata dalle istanze del ruolo.

* **Server DNS**: per immettere il nome del server DNS e l'indirizzo IP. Questa impostazione non comporta la creazione di un server DNS. Consente di specificare i server DNS da usare per la risoluzione dei nomi per la rete virtuale.

### <a name="to-create-a-classic-virtual-network"></a>Per creare una rete virtuale classica

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-sites-and-gateways"></a><a name="localsite"></a>Configurare siti e gateway

Azure utilizza le impostazioni specificate in ogni sito di rete locale per stabilire come instradare il traffico tra le reti virtuali. Ogni rete virtuale deve puntare alla rete locale alla quale si desidera indirizzare il traffico. Il nome da assegnare a ogni sito di rete locale viene stabilito dall'utente. È consigliabile usare un nome descrittivo.

Ad esempio, TestVNet1 si connette al sito di rete locale creato dall'utente con il nome "VNet4Local". Le impostazioni per VNet4Local contengono i prefissi di indirizzo per TestVNet4.

Tenere presente che il sito locale per ogni VNet è l'altro VNet.

| Rete virtuale | Spazio di indirizzi | Location | Si connette al sito della rete locale |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Stati Uniti orientali |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Stati Uniti occidentali |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

### <a name="to-configure-a-site"></a><a name="site"></a>Per configurare un sito

In genere il sito locale fa riferimento alla posizione locale. Contiene l'indirizzo IP del dispositivo VPN a cui si crea una connessione e gli intervalli di indirizzi IP che verranno distribuiti tramite il gateway VPN al dispositivo VPN.

1. Nella pagina del VNet, in **Impostazioni**, selezionare **connessioni da sito a sito**.
1. Nella pagina connessioni da sito a sito selezionare **+ Aggiungi**.
1. Nella pagina **Configura connessione VPN e gateway** , per tipo di **connessione**, lasciare selezionato **da sito a sito** .

   * **Indirizzo IP del gateway VPN:** questo è l'indirizzo IP pubblico del dispositivo VPN per la rete locale. Per questo esercizio, è possibile inserire un indirizzo fittizio perché non si ha ancora l'indirizzo IP per il gateway VPN per l'altro sito. Ad esempio, 5.4.3.2. Successivamente, dopo aver configurato il gateway per l'altro VNet, è possibile modificare questo valore.

   * **Spazio indirizzi client:** Elencare gli intervalli di indirizzi IP che si vuole indirizzare agli altri VNet tramite questo gateway. È possibile aggiungere più intervalli di spazi indirizzi. Assicurarsi che gli intervalli specificati qui non si sovrappongano a quelli di altre reti a cui si connette la rete virtuale oppure agli intervalli di indirizzi della rete virtuale stessa.
1. Nella parte inferiore della pagina non selezionare Verifica + crea. Selezionare invece **Avanti: Gateway>**.

### <a name="to-configure-a-virtual-network-gateway"></a><a name="sku"></a>Per configurare un gateway di rete virtuale

1. Nella pagina **gateway** selezionare i valori seguenti:

   * **Dimensioni:** Questo è lo SKU del gateway usato per creare il gateway di rete virtuale. I gateway VPN classici usano gli SKU del gateway legacy. Per altre informazioni sugli SKU del gateway legacy, vedere [Lavorare con gli SKU del gateway di rete virtuale (SKU precedenti)](vpn-gateway-about-skus-legacy.md). Per questo esercizio è possibile selezionare **standard** .

   * **Tipo di routing:** Selezionare il tipo di routing per il gateway. Questo è noto anche come il tipo di VPN. È importante selezionare il tipo corretto perché non è possibile convertire il gateway da un tipo a un altro. Il dispositivo VPN deve essere compatibile con il tipo di routing selezionato. Per ulteriori informazioni sul tipo di routing, vedere [informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype). In alcuni articoli si potrebbe fare riferimento ai tipi di VPN "RouteBased" e "PolicyBased". "Dinamico" corrisponde a "RouteBased" e "Statico" corrisponde a "PolicyBased". Per questa configurazione, selezionare **dinamico**.

   * **Subnet del gateway:** Le dimensioni della subnet del gateway specificata dipendono dalla configurazione del gateway VPN che si vuole creare. Anche se è possibile creare una subnet del gateway pari a /29, è consigliabile usare /27 o /28. In questo modo viene creata una subnet più grande che include più indirizzi. L'uso di una subnet del gateway di dimensioni maggiori consente un numero sufficiente di indirizzi IP per eventuali configurazioni future.

1. Selezionare **Verifica + crea** nella parte inferiore della pagina per convalidare le impostazioni. Selezionare **Crea** per distribuire. La creazione di un gateway di rete virtuale può richiedere fino a 45 minuti, a seconda dello SKU del gateway selezionato.
1. È possibile iniziare a procedere al passaggio successivo durante la creazione del gateway.

### <a name="configure-testvnet4-settings"></a>Configurare le impostazioni di TestVNet4

Ripetere i passaggi per [creare un sito e un gateway](#localsite) per configurare TestVNet4, sostituendo i valori quando necessario. Se si esegue questa operazione come esercizio, usare i [valori di esempio](#planning).

## <a name="update-local-sites"></a><a name="updatelocal"></a>Aggiornare i siti locali

Dopo la creazione dei gateway di rete virtuale per entrambi reti virtuali, è necessario modificare le proprietà del sito locale per l' **indirizzo IP del gateway VPN**.

|Nome della rete virtuale|Sito collegato|Indirizzo IP del gateway|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|Indirizzo IP del gateway VPN per TestVNet4|
|TestVNet4|VNet1Local|Indirizzo IP del gateway VPN per TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Parte 1: ottenere l'indirizzo IP pubblico del gateway di rete virtuale

1. Passare alla VNet passando al **gruppo di risorse** e selezionando la rete virtuale.
1. Nella pagina della rete virtuale, nel riquadro **Essentials** a destra, individuare l' **indirizzo IP del gateway** e copiarlo negli Appunti.

### <a name="part-2---modify-the-local-site-properties"></a>Parte 2: modificare le proprietà del sito locale

1. In connessioni da sito a sito selezionare la connessione. Ad esempio, SiteVNet4.
1. Nella pagina **Proprietà** per la connessione da sito a sito selezionare **modifica sito locale**.
1. Nel campo **indirizzo IP del gateway VPN** incollare l'indirizzo IP del gateway VPN copiato nella sezione precedente.
1. Selezionare **OK**.
1. Il campo viene aggiornato nel sistema. È anche possibile usare questo metodo per aggiungere un indirizzo IP aggiuntivo che si vuole indirizzare a questo sito.

### <a name="part-3---repeat-steps-for-the-other-vnet"></a>Parte 3: ripetere i passaggi per gli altri VNet

Ripetere i passaggi per TestVNet4.

## <a name="retrieve-configuration-values"></a><a name="getvalues"></a>Recuperare i valori di configurazione

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-connections"></a><a name="createconnections"></a>Creare le connessioni

Quando tutti i passaggi precedenti sono stati completati, è possibile impostare le chiavi già condivise IPsec/IKE e creare la connessione. Questa procedura usa PowerShell. Le connessioni da VNet a VNet per il modello di distribuzione classica non possono essere configurate nel portale di Azure perché non è possibile specificare la chiave condivisa nel portale.

Negli esempi notare che la chiave condivisa è esattamente la stessa. La chiave condivisa deve sempre corrispondere. Assicurarsi di sostituire i valori in questi esempi con i nomi esatti delle reti virtuali e dei siti di rete locale.

1. Creare la connessione da TestVNet1 a TestVNet4. Assicurarsi di modificare i valori.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName 'value for _VNet4Local' -SharedKey A1b2C3D4
   ```
2. Creare la connessione da TestVNet4 a TestVNet1.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'value for _VNet1Local' -SharedKey A1b2C3D4
   ```
3. Attendere l'inizializzazione delle connessioni. Dopo l'inizializzazione del gateway, lo stato è "Riuscito".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq-and-considerations"></a><a name="faq"></a>Domande frequenti e considerazioni

Queste considerazioni si applicano alle reti virtuali classiche e ai gateway di rete virtuale classica.

* Le reti virtuali possono trovarsi in sottoscrizioni uguali o diverse.
* Le reti virtuali possono essere nelle sottoscrizioni uguale o diverse.
* Un servizio cloud o un endpoint di bilanciamento del carico non può estendersi tra reti virtuali, anche se sono connesse tra loro.
* Per il collegamento di più reti virtuali non sono necessari dispositivi VPN.
* VNet to VNet supporta la connessione di reti virtuali di Azure. Non supporta invece la connessione di macchine virtuali o servizi cloud non distribuiti in una rete virtuale.
* Per la connessione da rete virtuale a rete virtuale sono necessari gateway con routing dinamico. I gateway con routing statico di Azure non sono supportate.
* La connettività di rete virtuale può essere usata contemporaneamente con VPN multisito, con un massimo di 10 tunnel VPN per gateway VPN di rete virtuale con connessione ad altre reti virtuali o a siti locali.
* Gli spazi degli indirizzi delle reti virtuali e dei siti di rete locali non devono sovrapporsi. Spazi degli indirizzi sovrapposti causerà la creazione di reti virtuali o caricare i file di configurazione netcfg esito negativo.
* Non sono supportati tunnel ridondanti tra una coppia di reti virtuali.
* Tutti i tunnel VPN della rete virtuale, tra cui reti VPN da punto a sito, condividono la larghezza di banda disponibile sul gateway VPN e il contratto di servizio relativo al tempo di inattività del gateway VPN in Azure.
* Il traffico da rete virtuale a rete virtuale scorre attraverso il backbone di Azure.

## <a name="next-steps"></a>Passaggi successivi

Verificare le connessioni. Vedere [Verificare una connessione di Gateway VPN](vpn-gateway-verify-connection-resource-manager.md).
