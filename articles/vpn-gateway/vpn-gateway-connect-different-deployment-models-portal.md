<properties 
   pageTitle="Come connettere le reti virtuali classiche alle reti virtuali di Resource Manager nel portale| Microsoft Azure"
   description="Informazioni su come creare una connessione VPN tra le reti virtuali classiche e le reti virtuali di Resource Manager usando il gateway VPN e il portale"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>  
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="cherylmc" />  

# Connettere reti virtuali da modelli di distribuzione diversi nel portale

> [AZURE.SELECTOR]
- [Portale](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)


Azure offre attualmente due modelli di gestione: classico e Resource Manager. Se si usa Azure da qualche tempo, si dispone probabilmente di VM e istanze del ruolo di Azure in esecuzione su una rete virtuale classica. Le macchine virtuali e le istanze del ruolo più recenti potrebbero invece essere in esecuzione su una rete virtuale creata in Resource Manager. Questo articolo descrive come connettere le reti virtuali classiche alle reti virtuali di Resource Manager per consentire alle risorse presenti nei modelli di distribuzione separati di comunicare tra di loro tramite una connessione gateway.

È possibile creare una connessione tra reti virtuali in sottoscrizioni diverse, in aree diverse e in modelli di distribuzione diversi. È anche possibile connettere reti virtuali che già dispongono di connessioni alle reti locali, purché il gateway con cui sono state configurate sia dinamico o basato su route. Per altre informazioni sulle connessioni da rete virtuale a rete virtuale, vedere la sezione [Domande frequenti relative alla connessione da rete virtuale a rete virtuale](#faq) alla fine di questo articolo.

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## Prima di iniziare

I passaggi seguenti illustrano le impostazioni necessarie per configurare un gateway dinamico o basato su route per ogni rete virtuale e creare una connessione VPN tra i gateway. Questa configurazione non supporta i gateway con routing statico o basati su criteri. In questo articolo vengono usati il portale classico, il portale di Azure e PowerShell. Attualmente non è possibile creare questa configurazione usando solo il portale di Azure.

Prima di iniziare, verificare quanto segue:

 - Entrambe le reti virtuali sono già state create.
 - Gli intervalli di indirizzi per le reti virtuali non si sovrappongono l'uno con l'altro o non si sovrappongano con gli intervalli delle eventuali altre connessioni a cui i gateway potrebbero essere collegati.
 - Sono stati installati i cmdlet di PowerShell più recenti (versione 1.0.2 o successive). Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). Assicurarsi di installare sia i cmdlet di Gestione dei servizi (SM) che i cmdlet di Resource Manager (RM).

### <a name="values"></a>Impostazioni di esempio

È possibile usare le impostazioni di esempio come riferimento quando si usano i cmdlet di PowerShell nei passaggi seguenti:

**Impostazioni della rete virtuale classica**

Nome rete virtuale = ClassicVNet <br> Località = Stati Uniti occidentali <br> Spazi di indirizzi della rete virtuale = 10.0.0.0/24 <br> Subnet-1 = 10.0.0.0/27 <br> GatewaySubnet = 10.0.0.32/29 <br> Nome della rete locale = RMVNetLocal <br>

**Impostazioni della rete virtuale di Resource Manager**

Nome della rete virtuale = RMVNet <br> Gruppo di risorse = RG1 <br> Spazi di indirizzi IP della rete virtuale = 192.168.0.0/16 <br> Subnet -1 = 192.168.1.0/24 <br> GatewaySubnet = 192.168.0.0/26 <br> Località = Stati Uniti orientali <br> Nome del gateway di rete virtuale = RMGateway <br> Nome IP pubblico del gateway = gwpip <br> Tipo di gateway = VPN <br> Tipo di VPN = basata su route <br> Gateway di rete locale = ClassicVNetLocal <br>

## <a name="createsmgw"></a>Sezione 1: Configurare le impostazioni di una rete virtuale classica

In questa sezione vengono creati la rete locale e il gateway per una rete virtuale classica. Le istruzioni riportate in questa sezione usano il portale classico. Attualmente, il portale di Azure non offre tutte le impostazioni rilevanti per una rete virtuale classica.

### Parte 1 - Creare una nuova rete locale

Aprire il [portale classico](https://manage.windowsazure.com) e accedere con l'account Azure.

1. Nell'angolo in basso a sinistra della schermata fare clic su **Nuovo** > **Servizi di rete** > **Rete virtuale** > **Aggiungi rete locale**.

2. Nella finestra **Specifica i dettagli della rete locale** digitare un nome per la rete virtuale di Resource Manager a cui si vuole effettuare la connessione. Nella casella **Indirizzo IP dispositivi VPN (facoltativo)** digitare qualsiasi indirizzo IP pubblico valido. Questo è solo un segnaposto temporaneo. Questo indirizzo IP verrà modificato in un secondo momento. Nell'angolo in basso a destra della finestra fare clic sul pulsante con freccia.
 
3. Nella pagina **Specificare lo spazio di indirizzi**, nella casella di testo **IP iniziale**, digitare il prefisso di rete per la rete e il blocco CIDR della rete virtuale di Resource Manager a cui si vuole effettuare la connessione. Questa impostazione viene usata per specificare lo spazio indirizzi per il routing alla rete virtuale di Resource Manager.

### Parte 2 - Associare la rete locale alla rete virtuale

1. Fare clic su **Reti virtuali** nella parte superiore della pagina per passare alla schermata Reti virtuali e quindi selezionare la rete virtuale classica. Nella pagina della rete virtuale fare clic su **Configura** per passare alla pagina di configurazione.

2. Nella sezione **Connettività da sito a sito** selezionare la casella di controllo **Connetti alla rete locale**. Selezionare quindi la rete locale creata. Se sono state create più reti locali, assicurarsi di selezionare nell'elenco a discesa quella creata per rappresentare la rete virtuale di Resource Manager.

3. Fare clic su **Save** nella parte inferiore della pagina.

### Parte 3 - Creare il gateway

1. Dopo aver salvato le impostazioni, fare clic su **Dashboard** nella parte superiore della pagina per passare alla pagina Dashboard. Nella parte inferiore della pagina Dashboard fare clic su **Crea gateway** e quindi su **Routing dinamico**. Fare clic su **Sì** per avviare la creazione del gateway. Per questa configurazione è necessario un gateway di routing dinamico.

2. Attendere che il gateway venga creato. In alcuni casi, l'operazione può richiedere 45 minuti o più.

### <a name="ip"></a>Parte 4 - Visualizzare l'indirizzo IP pubblico del gateway

Dopo aver creato il gateway, è possibile visualizzare l'indirizzo IP del gateway nella pagina **Dashboard**. Questo è l'indirizzo IP pubblico del gateway. Scrivere o copiare l'indirizzo IP pubblico. Verrà usato nei passaggi successivi quando si crea la rete locale per la configurazione della rete virtuale di Resource Manager.


## <a name="creatermgw"></a>Sezione 2: Configurare le impostazioni della rete virtuale di Resource Manager

In questa sezione vengono creati il gateway della rete virtuale e la rete locale per la rete virtuale di Resource Manager. Eseguire i passaggi seguenti solo dopo aver recuperato l'indirizzo IP pubblico del gateway della rete virtuale classica.

Gli screenshot sono forniti come esempi. Assicurarsi di sostituire i valori con i propri. Se si crea questa configurazione come esercizio, fare riferimento a questi [valori](#values).


### Parte 1 - Creare una subnet del gateway

Prima di connettere la rete virtuale a un gateway, è necessario creare la subnet del gateway per la rete virtuale con cui si vuole stabilire la connessione. Creare una subnet del gateway con conteggio CIDR /28 o superiore (/27, /26 e così via).

In un browser passare al [portale di Azure](http://portal.azure.com) e accedere con l'account Azure.

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### Parte 2 - Creare un gateway di rete virtuale


[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


### Parte 3 - Creare un gateway di rete locale

Il gateway di rete locale fa in genere riferimento alla posizione locale ed indica ad Azure gli intervalli di indirizzi IP di cui eseguire il routing alla posizione e l'indirizzo IP pubblico del dispositivo per tale posizione. In questo caso, tuttavia, si riferisce all'intervallo di indirizzi e all'indirizzo IP pubblico associati alla rete virtuale classica e al gateway di rete virtuale.

Assegnare un nome al gateway di rete locale a cui Azure potrà fare riferimento. È possibile creare il gateway di rete locale durante la creazione del gateway di rete virtuale. Per questa configurazione viene usato l'indirizzo IP pubblico assegnato al gateway di rete virtuale classico nella [sezione precedente](#ip).

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


### Parte 4 - Copiare l'indirizzo IP pubblico

Al termine della creazione del gateway di rete virtuale, copiare l'indirizzo IP pubblico associato al gateway. Verrà usato al momento della configurazione delle impostazioni di rete locale per la rete virtuale classica.


## Sezione 3: Modificare la rete locale per la rete virtuale classica

Aprire il [portale classico](https://manage.windowsazure.com).

2. Nel portale classico scorrere verso il basso a sinistra e fare clic su **Reti**. Nella pagina **Reti** fare clic su **Reti locali** nella parte superiore della pagina.

3. Fare clic per selezionare la rete locale configurata nella parte 1. Nella parte inferiore della pagina fare clic su **Modifica**.

4. Nella pagina **Specificare i dettagli della rete locale** sostituire l'indirizzo IP segnaposto con l'indirizzo IP pubblico del gateway di Resource Manager che è stato creato nella sezione precedente. Fare clic sulla freccia per passare alla sezione successiva. Verificare che lo **spazio di indirizzi** sia corretto e quindi fare clic sul segno di spunta per accettare le modifiche.

## <a name="connect"></a>Sezione 4: Creare la connessione

In questa sezione viene creata la connessione tra le reti virtuali. Per eseguire i passaggi di questa operazione è richiesto PowerShell. Non è possibile creare questa connessione dai portali. Assicurarsi di aver scaricato e installato sia i cmdlet di PowerShell classici di Gestione dei servizi (SM) che di Resource Manager (RM).


1. Accedere all'account Azure nella console di PowerShell. Il cmdlet seguente richiede le credenziali di accesso per l'account Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell.

		Login-AzureRmAccount 

 	Ottenere un elenco delle sottoscrizioni di Azure se si dispone di più di una sottoscrizione.

		Get-AzureRmSubscription

	Specificare la sottoscrizione da usare.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2. Aggiungere l'account Azure per usare i cmdlet di PowerShell classici. A tale scopo, è possibile usare il comando seguente:

		Add-AzureAccount

3. Impostare la chiave condivisa eseguendo l'esempio seguente. In questo esempio, `-VNetName` è il nome della rete virtuale classica e `-LocalNetworkSiteName` è il nome specificato per la rete locale al momento della configurazione nel portale classico. `-SharedKey` è un valore che è possibile generare e specificare. Il valore specificato qui deve essere lo stesso valore specificato nel passaggio successivo quando si crea la connessione.

		Set-AzureVNetGatewayKey -VNetName ClassicVNet `
		-LocalNetworkSiteName RMVNetLocal -SharedKey abc123

4. Creare la connessione VPN eseguendo i comandi seguenti:
	
	**Impostare le variabili**

		$vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
		$vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

	**Creare la connessione**<br> Si noti che `-ConnectionType` è 'IPsec' e non 'Vnet2Vnet'. In questo esempio, `-Name` è il nome usato per chiamare la connessione. L'esempio seguente crea una connessione denominata '*rm-to-classic-connection*'.
		
		New-AzureRmVirtualNetworkGatewayConnection -Name rm-to-classic-connection -ResourceGroupName RG1 `
		-Location "East US" -VirtualNetworkGateway1 `
		$vnet02gateway -LocalNetworkGateway2 `
		$vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## Verificare la connessione

È possibile verificare la connessione usando il portale classico, il portale di Azure o PowerShell. È possibile usare la procedura seguente per verificare la connessione. Sostituire i valori con quelli personalizzati.

[AZURE.INCLUDE [vpn-gateway-verify connection](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="faq"></a>Domande frequenti sulle connessioni da rete virtuale a rete virtuale

Visualizzare i dettagli delle frequenti per altre informazioni sulle connessioni da rete virtuale a rete virtuale.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

<!---HONumber=AcomDC_0928_2016-->