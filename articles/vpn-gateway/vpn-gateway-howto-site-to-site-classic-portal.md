---
title: 'Connettere la rete locale a una rete virtuale di Azure: VPN da sito a sito (distribuzione classica): portale | Microsoft Docs'
description: Creare una connessione IPsec dalla rete locale a una rete virtuale di Azure classica tramite la rete Internet pubblica.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: aae05c89cd56ffa16994cb5696054457f1d32bc3
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876142"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Creare una connessione da sito a sito usando il portale di Azure (distribuzione classica)

Questo articolo illustra come usare il portale di Azure per creare una connessione gateway VPN da sito a sito dalla rete locale alla rete virtuale. I passaggi descritti in questo articolo si applicano al modello di distribuzione classica e non si applicano al modello di distribuzione corrente Gestione risorse. È anche possibile creare questa configurazione usando strumenti o modelli di distribuzione diversi selezionando un'opzione differente nell'elenco seguente:

> [!div class="op_single_selector"]
> * [Portale di Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portale di Azure (classico)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 

Una connessione gateway VPN da sito a sito viene usata per connettere la rete locale a una rete virtuale di Azure tramite un tunnel VPN IPsec/IKE (IKEv1 o IKEv2). Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico esterno assegnato. Per altre informazioni sui gateway VPN, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).

![Diagramma della connessione cross-premise gateway VPN da sito a sito](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

* Assicurarsi di voler usare il modello di distribuzione classica. Per usare il modello di distribuzione Resource Manager, vedere [Creare una connessione da sito a sito (Resource Manager)](vpn-gateway-howto-site-to-site-resource-manager-portal.md). È consigliabile usare il modello di distribuzione Gestione risorse, perché il modello classico è legacy.
* Verificare di avere un dispositivo VPN compatibile e che sia presente un utente in grado di configurarlo. Per altre informazioni sui dispositivi VPN compatibili e sulla configurazione dei dispositivi, vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md).
* Verificare di avere un indirizzo IPv4 pubblico esterno per il dispositivo VPN.
* Se non si ha familiarità con gli intervalli degli indirizzi IP disponibili nella configurazione della rete locale, è necessario coordinarsi con qualcuno che possa fornire tali dettagli. Quando si crea questa configurazione, è necessario specificare i prefissi degli intervalli di indirizzi IP che Azure instraderà alla posizione locale. Nessuna delle subnet della rete locale può sovrapporsi alle subnet della rete virtuale a cui ci si vuole connettere.
* PowerShell è necessario per specificare la chiave condivisa e creare la connessione del gateway VPN. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>Valori di configurazione di esempio per questo esercizio

Gli esempi di questo articolo usano i valori seguenti. È possibile usare questi valori per creare un ambiente di test o farvi riferimento per comprendere meglio gli esempi di questo articolo. In genere, quando si utilizzano i valori degli indirizzi IP per lo spazio degli indirizzi, si desidera coordinarsi con l'amministratore di rete per evitare la sovrapposizione degli spazi di indirizzi, che possono influire sul routing. In questo caso, sostituire i valori degli indirizzi IP con quelli personalizzati se si vuole creare una connessione funzionante.

* **Gruppo di risorse:** TestRG1
* **Nome VNet:** TestVNet1
* **Spazio di indirizzi:** 10.11.0.0/16
* **Nome subnet:** FrontEnd
* **Intervallo di indirizzi subnet:** 10.11.0.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **Area:** (Stati Uniti) Stati Uniti orientali
* **Nome del sito locale:** Site2
* **Spazio indirizzi client:** spazio di indirizzi nel sito locale.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Crea rete virtuale

Quando si crea una rete virtuale da usare per una connessione da sito a sito, è necessario assicurarsi che gli spazi di indirizzi specificati non si sovrappongano a quelli usati nel client per i siti locali a cui ci si vuole connettere. Se sono presenti subnet che si sovrappongono, la connessione non funziona correttamente.

* Se si ha già una rete virtuale, verificare che le impostazioni siano compatibili con la progettazione del gateway VPN. Prestare particolare attenzione alle subnet che potrebbero sovrapporsi ad altre reti.

* Se non si ha una rete virtuale, crearne una. Gli screenshot sono forniti come esempio. Assicurarsi di sostituire i valori con i propri.

### <a name="to-create-a-virtual-network"></a>Per creare una rete virtuale

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-the-site-and-gateway"></a><a name="localsite"></a>Configurare il sito e il gateway

### <a name="to-configure-the-site"></a>Per configurare il sito

In genere il sito locale fa riferimento alla posizione locale. Contiene l'indirizzo IP del dispositivo VPN a cui si crea una connessione e gli intervalli di indirizzi IP che verranno distribuiti tramite il gateway VPN al dispositivo VPN.

1. Nella pagina del VNet, in **Impostazioni**, selezionare **connessioni da sito a sito**.
1. Nella pagina connessioni da sito a sito selezionare **+ Aggiungi**.
1. Nella pagina **Configura connessione VPN e gateway** , per tipo di **connessione**, lasciare selezionato **da sito a sito** . Per questo esercizio, sarà necessario usare una combinazione dei [valori di esempio](#values) e i valori personalizzati.

   * **Indirizzo IP del gateway VPN:** questo è l'indirizzo IP pubblico del dispositivo VPN per la rete locale. Per il dispositivo VPN è necessario usare un indirizzo IP IPv4 pubblico. Specificare un indirizzo IP pubblico valido per il dispositivo VPN a cui ci si vuole connettere. Deve essere raggiungibile da Azure. Se non si conosce l'indirizzo IP del dispositivo VPN, è possibile inserire un valore segnaposto, purché sia nel formato di un indirizzo IP pubblico valido, e quindi modificarlo in un secondo momento.

   * **Spazio indirizzi client:** elencare gli intervalli di indirizzi IP che devono essere indirizzati alla rete locale tramite il gateway. È possibile aggiungere più intervalli di spazi indirizzi. Assicurarsi che gli intervalli specificati qui non si sovrappongano a quelli di altre reti a cui si connette la rete virtuale oppure agli intervalli di indirizzi della rete virtuale stessa.
1. Nella parte inferiore della pagina non selezionare Verifica + crea. Selezionare invece **Avanti: Gateway>**.

### <a name="to-configure-the-virtual-network-gateway"></a><a name="sku"></a>Per configurare il gateway di rete virtuale

1. Nella pagina **gateway** selezionare i valori seguenti:

   * **Dimensioni:** Questo è lo SKU del gateway usato per creare il gateway di rete virtuale. I gateway VPN classici usano gli SKU del gateway legacy. Per altre informazioni sugli SKU del gateway legacy, vedere [Lavorare con gli SKU del gateway di rete virtuale (SKU precedenti)](vpn-gateway-about-skus-legacy.md). Per questo esercizio è possibile selezionare **standard** .

   * **Tipo di routing:** Selezionare il tipo di routing per il gateway. Questo è noto anche come il tipo di VPN. È importante selezionare il tipo corretto perché non è possibile convertire il gateway da un tipo a un altro. Il dispositivo VPN deve essere compatibile con il tipo di routing selezionato. Per ulteriori informazioni sul tipo di routing, vedere [informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype). In alcuni articoli si potrebbe fare riferimento ai tipi di VPN "RouteBased" e "PolicyBased". "Dinamico" corrisponde a "RouteBased" e "Statico" corrisponde a "PolicyBased". In genere si desidera il routing dinamico.

   * **Subnet del gateway:** Le dimensioni della subnet del gateway specificata dipendono dalla configurazione del gateway VPN che si vuole creare. Anche se è possibile creare una subnet del gateway pari a /29, è consigliabile usare /27 o /28. In questo modo viene creata una subnet più grande che include più indirizzi. L'uso di una subnet del gateway di dimensioni maggiori consente un numero sufficiente di indirizzi IP per eventuali configurazioni future.

1. Selezionare **Verifica + crea** nella parte inferiore della pagina per convalidare le impostazioni. Selezionare **Crea** per distribuire. La creazione di un gateway di rete virtuale può richiedere fino a 45 minuti, a seconda dello SKU del gateway selezionato.

## <a name="configure-your-vpn-device"></a><a name="vpndevice"></a>Configurare il dispositivo VPN

Le connessioni da sito a sito verso una rete locale richiedono un dispositivo VPN. In questo passaggio viene configurato il dispositivo VPN. Quando si configura il dispositivo VPN, sono necessari i valori seguenti:

* Chiave condivisa. Si tratta della stessa chiave condivisa che viene specificata durante la creazione della connessione VPN da sito a sito. In questi esempi viene usata una chiave condivisa semplice. È consigliabile generare una chiave più complessa per l'uso effettivo.
* Indirizzo IP pubblico del gateway di rete virtuale. È possibile visualizzare l'indirizzo IP pubblico usando il portale di Azure, PowerShell o l'interfaccia della riga di comando.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="retrieve-values"></a><a name="getvalues"></a>Recuperare i valori

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-the-connection"></a><a name="CreateConnection"></a>Creare la connessione

In questo passaggio, usando i valori dei passaggi precedenti, si imposta la chiave condivisa e si crea la connessione. La chiave impostata deve essere la stessa usata nella configurazione del dispositivo VPN.

> [!NOTE]
> Per il modello di distribuzione classica, questo passaggio non è disponibile nel portale di Azure o tramite Azure Cloud Shell. È necessario usare la versione di gestione dei servizi (SM) dei cmdlet di Azure PowerShell localmente dal desktop.
>

1. Impostare la chiave condivisa e creare la connessione.

   * Modificare il valore-VNetName e il valore-LocalNetworkSiteName. Quando si specifica un nome che contiene spazi, racchiudere il valore tra virgolette singole.
   * '-SharedKey ' è un valore generato e quindi specificato. Nell'esempio è stato usato "abc123", ma è possibile (e) generare qualcosa di più complesso. È importante che il valore specificato qui sia lo stesso valore specificato durante la configurazione del dispositivo VPN.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName '6C74F6E6_Site2' -SharedKey abc123
   ```

1. Quando viene creata la connessione, il risultato è **Stato: Operazione completata**.

## <a name="verify-your-connection"></a><a name="verify"></a>Verificare la connessione

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

In caso di problemi di connessione, vedere la sezione relativa alla **risoluzione dei problemi** del sommario nel riquadro a sinistra.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Come reimpostare un gateway VPN

La reimpostazione del gateway VPN di Azure è utile se si perde la connettività VPN cross-premise in uno o più tunnel VPN da sito a sito. In questa situazione tutti i dispositivi VPN funzionano correttamente, ma non sono in grado di stabilire tunnel IPsec con i gateway VPN di Azure. Per la procedura da seguire, vedere [Reimpostare un gateway VPN](vpn-gateway-resetgw-classic.md#resetclassic).

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>Come modificare uno SKU del gateway

Per la procedura per modificare uno SKU del gateway, vedere [ridimensionare uno SKU del gateway](vpn-gateway-about-SKUS-legacy.md#classicresize).

## <a name="next-steps"></a>Passaggi successivi

* Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](https://docs.microsoft.com/azure/).
* Per informazioni sul tunneling forzato, vedere [informazioni sul tunneling forzato](vpn-gateway-about-forced-tunneling.md).
