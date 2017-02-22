---
title: 'Connettere le reti virtuali classiche alle reti virtuali di Azure Resource Manager: portale| Documentazione Microsoft'
description: Informazioni su come creare una connessione VPN tra le reti virtuali classiche e le reti virtuali di Resource Manager usando il gateway VPN e il portale
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 5a90498c-4520-4bd3-a833-ad85924ecaf9
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 10985eaebd9148ef4fbd22eac0ba50076ca71ef5
ms.openlocfilehash: c20d72f25571e582310ae65ad1d887dbdb7b011c


---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Connettere reti virtuali da modelli di distribuzione diversi usando il portale
> [!div class="op_single_selector"]
> * [Portale](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Azure offre attualmente due modelli di gestione: classico e Resource Manager. Se si usa Azure da qualche tempo, si dispone probabilmente di VM e istanze del ruolo di Azure in esecuzione su una rete virtuale classica. Le macchine virtuali e le istanze del ruolo più recenti potrebbero invece essere in esecuzione su una rete virtuale creata in Resource Manager. Questo articolo descrive come connettere le reti virtuali classiche alle reti virtuali di Resource Manager per consentire alle risorse presenti nei modelli di distribuzione separati di comunicare tra di loro tramite una connessione gateway. 

È possibile creare una connessione tra reti virtuali in sottoscrizioni diverse e in aree diverse. È anche possibile connettere reti virtuali che già dispongono di connessioni alle reti locali, purché il gateway con cui sono state configurate sia dinamico o basato su route. Per altre informazioni sulle connessioni tra reti virtuali, vedere la sezione [Considerazioni sulle connessioni tra reti virtuali](#faq) alla fine di questo articolo.

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Metodi e modelli di distribuzione per connessioni da rete virtuale a rete virtuale
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

La tabella seguente verrà aggiornata man mano che per questa configurazione diventano disponibili nuovi articoli e altri strumenti. Quando un articolo risulterà disponibile, nella tabella sarà presente un collegamento diretto.<br><br>

**Tra reti virtuali**

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**Peering reti virtuali**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="a-namebeforeabefore-beginning"></a><a name="before"></a>Prima di iniziare
I passaggi seguenti illustrano le impostazioni necessarie per configurare un gateway dinamico o basato su route per ogni rete virtuale e creare una connessione VPN tra i gateway. Questa configurazione non supporta i gateway con routing statico o basati su criteri. 

In questo articolo vengono usati il portale di Azure e PowerShell. PowerShell è necessario per creare le connessioni tra le reti virtuali. Non è possibile creare le connessioni per questa configurazione usando il portale.

### <a name="prerequisites"></a>Prerequisiti
* Entrambe le reti virtuali sono già state create.
* Gli intervalli di indirizzi per le reti virtuali non si sovrappongono l'uno con l'altro o non si sovrappongano con gli intervalli delle eventuali altre connessioni a cui i gateway potrebbero essere collegati.
* Sono stati installati i cmdlet di PowerShell più recenti. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) . Assicurarsi di installare sia i cmdlet di Gestione dei servizi (SM) che i cmdlet di Resource Manager (RM). 

### <a name="a-namevaluesaexample-settings"></a><a name="values"></a>Impostazioni di esempio
È possibile usare le impostazioni di esempio come riferimento.

**Impostazioni della rete virtuale classica**

Nome rete virtuale = ClassicVNet  <br>
Spazio indirizzi = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
Gruppo di risorse = ClassicRG <br>
Località = Stati Uniti occidentali <br>
GatewaySubnet: 10.0.0.32/28 <br>
Sito locale = RMVNetLocal <br>

**Impostazioni della rete virtuale di Resource Manager**

Nome della rete virtuale = RMVNet  <br>
Spazio indirizzi = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Gruppo di risorse= RG1 <br>
Località = Stati Uniti orientali <br>
Nome gateway di rete virtuale = RMGateway <br>
Tipo di gateway = VPN <br>
Tipo VPN= Basato su route <br>
Nome IP pubblico del gateway = gwpip <br>
Gateway di rete locale = ClassicVNetLocal <br>

## <a name="a-namecreatesmgwasection-1-configure-classic-vnet-settings"></a><a name="createsmgw"></a>Sezione 1: Configurare le impostazioni di una rete virtuale classica
In questa sezione verranno creati la rete locale (sito locale) e il gateway di rete virtuale per la rete virtuale classica. Le istruzioni riportate in questa sezione usano il portale di Azure. I passaggi indicati di seguito presuppongono che non sia stato creato un gateway VPN per la rete virtuale classica. Se si ha già un gateway, verificare che sia un gateway dinamico. Se è statico, è prima necessario eliminare il gateway VPN, quindi procedere con i passaggi seguenti.

### <a name="part-1---configure-the-local-site"></a>Parte 1: Configurare il sito locale

Aprire il [portale di Azure](https://ms.portal.azure.com) e accedere con l'account Azure.

1. Passare a **Tutte le risorse** e trovare la rete virtuale classica.
2. Nel pannello **Panoramica** della sezione **Connessioni VPN** fare clic sull'elemento grafico **Gateway** per creare un gateway.

    ![Configurare un gateway VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Configurare un gateway VPN")
3. Nel pannello **Nuova connessione VPN** selezionare **Da sito a sito** in **Tipo di connessione**.
4. In **Sito locale** fare clic su **Configurare le impostazioni necessarie**. Si aprirà il pannello **Sito locale**.
5. Nel pannello **Sito locale** creare un nome da usare per fare riferimento alla rete virtuale di Resource Manager, ad esempio RMVNetLocal.
6. Se il gateway VPN per la rete virtuale di Resource Manager ha già un indirizzo IP pubblico, usare il valore del campo **Indirizzo IP del gateway VPN**. Se non è stato ancora creato un gateway di rete virtuale per la rete virtuale di Resource Manager è possibile usare un indirizzo IP segnaposto. Assicurarsi che l'indirizzo IP segnaposto usi un formato valido. In seguito sarà necessario sostituire l'indirizzo IP segnaposto con l'indirizzo IP pubblico del gateway di rete virtuale di Resource Manager.
7. Per **Spazio indirizzi client** usare i valori per gli spazi di indirizzi IP di rete virtuale per la rete virtuale di Resource Manager. Questa impostazione viene usata per specificare gli spazi indirizzi per il routing alla rete virtuale di Resource Manager.
8. Fare clic su **OK** per salvare i valori e tornare al pannello **Nuova connessione VPN**.

### <a name="part-2---create-the-virtual-network-gateway"></a>Parte 2: Creare il gateway di rete virtuale
1. Nel pannello **Nuova connessione VPN** selezionare la casella di controllo **Crea gateway immediatamente** e fare clic su **Configurazione gateway facoltativa** per aprire il pannello **Configurazione gateway**. 

    ![Aprire il pannello Configurazione gateway](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Aprire il pannello Configurazione gateway")
2. Fare clic su **Subnet - Configurare le impostazioni necessarie** per aprire il pannello **Aggiungi subnet**. In questo pannello si noterà che il nome è già configurato con il valore **GatewaySubnet** richiesto.
3. **Intervallo indirizzi** si riferisce all'intervallo per la subnet del gateway. Anche se è possibile creare una subnet del gateway con un intervallo di indirizzi /29 (3 indirizzi), è consigliabile creare una subnet del gateway con più indirizzi IP disponibili per eventuali configurazioni future che possono richiederli. Se possibile, usare /27 o /28. Fare clic su **OK** per creare la subnet del gateway.
4. Nel pannello **Configurazione gateway**, **Dimensioni** si riferisce allo SKU del gateway. Selezionare lo SKU del gateway per il gateway VPN.
5. Verificare che **Tipo di routing** sia **Dinamico**, quindi fare clic su **OK** per tornare al pannello **Nuova connessione VPN**.
6. Nel pannello **Nuova connessione VPN** fare clic su **OK** per iniziare a creare il gateway VPN. Per completare questa operazione possono essere necessari fino a 45 minuti.


### <a name="a-nameipapart-3---copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>Parte 3: Copiare l'indirizzo IP pubblico del gateway di rete virtuale
Dopo aver creato il gateway di rete virtuale è possibile visualizzare l'indirizzo IP del gateway. 

1. Passare alla rete virtuale classica e fare clic su **Panoramica**.
2. Fare clic su **Connessioni VPN** per aprire il pannello Connessioni VPN. Nel pannello Connessioni VPN è possibile visualizzare l'indirizzo IP pubblico. Si tratta dell'indirizzo IP pubblico assegnato al gateway di rete virtuale. Scrivere o copiare l'indirizzo IP. Sarà necessario nei passaggi successivi quando si useranno le impostazioni di configurazione del gateway di rete locale di Resource Manager. È anche possibile visualizzare lo stato delle connessioni gateway. Si noti che il sito di rete locale creato viene elencato come "Connessione". Lo stato verrà modificato dopo aver creato le connessioni. Chiudere il pannello dopo aver copiato l'indirizzo IP del gateway.

## <a name="a-namecreatermgwasection-2-configure-resource-manager-vnet-settings"></a><a name="creatermgw"></a>Sezione 2: Configurare le impostazioni della rete virtuale di Resource Manager
In questa sezione vengono creati il gateway di rete virtuale e la rete locale per la rete virtuale di Resource Manager. Gli screenshot sono forniti come esempio. Assicurarsi di sostituire i valori con i propri. Se si crea questa configurazione come esercizio, fare riferimento a questi [valori](#values).

### <a name="part-1---create-a-gateway-subnet"></a>Parte 1 - Creare una subnet del gateway
Prima di creare un gateway di rete virtuale è necessario creare una subnet del gateway. Creare una subnet del gateway con conteggio CIDR /28 o superiore, ad esempio /27, /26 e così via.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

In un browser passare al [portale di Azure](http://portal.azure.com) e accedere con l'account Azure.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>Parte 2 - Creare un gateway di rete virtuale
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

La creazione di un gateway di rete virtuale può richiedere fino a 45 minuti. È possibile attendere il termine della creazione del gateway di rete virtuale di Resource Manager o passare a [Parte 3: Creare un gateway di rete locale](#createlng). Dopo la creazione, al gateway viene assegnato un indirizzo IP pubblico. Nei passaggi successivi, questo indirizzo IP verrà usato per sostituire le informazioni di indirizzo IP segnaposto per le impostazioni del sito locale di rete virtuale classica create nella sezione 1. 

### <a name="a-namecreatelngapart-3---create-a-local-network-gateway"></a><a name="createlng"></a>Parte 3: Creare un gateway di rete locale

Il gateway di rete locale specifica l'intervallo di indirizzi e l'indirizzo IP pubblico associati alla rete virtuale classica e al relativo gateway di rete virtuale.

- Usare l'indirizzo IP pubblico assegnato al gateway di rete virtuale classica nella [sezione precedente](#ip). 
- Assegnare un nome al gateway di rete locale a cui Azure potrà fare riferimento, ad esempio 'ClassicVNetLocal'.
- Usare lo spazio indirizzi assegnato alla rete virtuale classica, non solo la subnet.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


## <a name="section-3-modify-the-classic-vnet-local-site-settings"></a>Sezione 3: Modificare le impostazioni del sito locale della rete virtuale classica

In questa sezione verrà usata la rete virtuale classica. Verrà sostituito l'indirizzo IP segnaposto usato quando sono state specificate le impostazioni del sito locale. Questa sezione usa i cmdlet PowerShell versione classica (SM). Se non è già stato fatto, visualizzare il gateway di rete virtuale per la rete virtuale di Resource Manager e copiare l'indirizzo IP pubblico. Si tratta dell'indirizzo IP che verrà usato per sostituire l'indirizzo IP segnaposto.

Verificare che sia stata scaricata la versione più recente di questi cmdlet, come specificato nella sezione [Prima di iniziare](#before).

1. Nel portale di Azure passare alla rete virtuale classica.
2. Nel pannello della rete virtuale fare clic su **Panoramica**.
3. Nella sezione **Connessioni VPN** fare clic sul nome del sito locale nell'elemento grafico.

    ![Connessioni VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "Connessioni VPN")
4. Nel pannello **Connessioni VPN da sito a sito** fare clic sul nome del sito.

    ![Site-name](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Nome del sito locale")
5. Nel pannello della connessione per il sito locale fare clic sul nome del sito locale per aprire il pannello **Sito locale**.

    ![Open-local-site](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Aprire il sito locale")
6. Nel pannello **Sito locale** sostituire l'indirizzo IP del gateway VPN con l'indirizzo IP del gateway di Resource Manager.

    ![Gateway-ip-address](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Indirizzo IP del gateway")
7. Fare clic su **OK** per aggiornare l'indirizzo IP.


## <a name="a-nameconnectasection-4-create-the-connection"></a><a name="connect"></a>Sezione 4: Creare la connessione
In questa sezione verrà creata la connessione tra le reti virtuali. Per questa procedura è necessario PowerShell. Non è possibile creare questa connessione nei portali. Assicurarsi di aver scaricato e installato sia i cmdlet di PowerShell classici di Gestione dei servizi (SM) che di Resource Manager (RM).

### <a name="part-1---log-in-to-your-azure-account"></a>Passaggio 1: Accedere all'account Azure

1. Aprire la console di PowerShell con diritti elevati e accedere all'account Azure. Il cmdlet seguente richiede le credenziali di accesso per l'account Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell.
   
        Login-AzureRmAccount 
   
2. Ottenere un elenco delle sottoscrizioni di Azure se si dispone di più di una sottoscrizione.
   
        Get-AzureRmSubscription
   
3. Specificare la sottoscrizione da usare. 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. Aggiungere l'account Azure per usare i cmdlet di PowerShell classici. A tale scopo, è possibile usare il comando seguente:
   
        Add-AzureAccount

### <a name="part-2---download-your-network-configuration-file"></a>Parte 2: Scaricare il file di configurazione di rete

In alcuni casi, i nomi dei siti di rete locale e di rete virtuale classica vengono modificati nel file di configurazione di rete durante la creazione di impostazioni di rete virtuale classica nel portale di Azure, a causa delle differenze nei modelli di distribuzione. Usando il portale di Azure, ad esempio, la rete virtuale classica è stata denominata 'Classic VNet' ed è stata creata in un gruppo di risorse denominato 'ClassicRG'. Il nome contenuto nel file di configurazione di rete viene convertito in 'Group ClassicRG Classic VNet'. Quando si specifica il nome di una rete virtuale che contiene spazi, racchiudere il valore tra virgolette.

1. Esportare il file di configurazione di rete di Azure eseguendo il comando seguente. Se necessario è possibile modificare il percorso del file da esportare.
   
        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
3. Usare un editor di testo, ad esempio Blocco note, per aprire il file XML e visualizzarne il contenuto. Verificare i valori per i nomi della rete virtuale classica e del sito di rete locale.

### <a name="part-3---set-the-shared-key"></a>Parte 3: Impostare la chiave condivisa

Impostare la chiave condivisa per la connessione dalla rete virtuale classica alla rete virtuale di Resource Manager. Quando si usano questi cmdlet, verificare i valori di `-VNetName` e `-LocalNetworkSiteName` usando il file di configurazione di rete. Quando si specificano nomi contenenti spazi, racchiudere il valore tra virgolette. 

- In questo esempio `-VNetName` è il nome della rete virtuale classica. 
- `-LocalNetworkSiteName` è il nome specificato per il sito locale.
- `-SharedKey` è un valore che è possibile generare e specificare. Per questo esempio è stato usato *abc123*, ma è possibile generare un elemento più complesso. È importante che il valore specificato qui sia lo stesso valore specificato nel passaggio successivo quando si crea la connessione.

Nella console di PowerShell impostare la chiave condivisa eseguendo questo esempio, sostituendo i valori con i propri. Il valore restituito per questo esempio deve visualizzare lo **stato: Operazione completata**.
   
        Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
        -LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
### <a name="part-4---create-the-vpn-connection-by-running-the-following-commands"></a>Parte 4: Creare la connessione VPN eseguendo questi comandi:
   
1. Impostare le variabili.
   
        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   
2. Creare la connessione. In questo esempio, `-Name` è il nome che si vuole assegnare alla connessione, non un elemento già creato. L'esempio seguente crea una connessione denominata 'RM-Classic'. Si noti che `-ConnectionType` è 'IPsec', non 'Vnet2Vnet', e che `-SharedKey` corrisponde alla chiave impostata in precedenza.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="section-5-verify-your-connections"></a>Sezione 5: Verificare le connessioni
È possibile verificare le connessioni usando il portale di Azure o PowerShell. Durante la verifica potrebbe essere necessario attendere un minuto o due per la creazione della connessione. Quando una connessione ha esito positivo, lo stato di connettività passerà da 'Connessione' a 'Connesso'.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Per verificare la connessione dalla rete virtuale classica alla rete virtuale di Resource Manager

####<a name="verify-your-connection-using-powershell"></a>Verificare la connessione tramite PowerShell


[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

####<a name="verify-your-connection-using-the-azure-portal"></a>Verificare la connessione tramite il portale di Azure

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


###<a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Per verificare la connessione dalla rete virtuale di Resource Manager alla rete virtuale classica

####<a name="verify-your-connection-using-powershell"></a>Verificare la connessione tramite PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

####<a name="verify-your-connection-using-the-azure-portal"></a>Verificare la connessione tramite il portale di Azure

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="a-namefaqavnet-to-vnet-considerations"></a><a name="faq"></a>Considerazioni sulle connessioni tra reti virtuali

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]




<!--HONumber=Jan17_HO4-->


