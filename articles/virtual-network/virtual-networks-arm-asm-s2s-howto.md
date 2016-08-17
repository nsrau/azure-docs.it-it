<properties 
   pageTitle="Come connettere reti virtuali classiche a reti virtuali di Gestione risorse di Azure in Azure"
   description="Informazioni su come creare una connessione VPN tra reti virtuali classiche e reti virtuali nuove"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="cherylmc" />

# Connessione di reti virtuali classiche a nuove reti virtuali

Azure attualmente ha due modalità di gestione: Service Manager di Azure (detta classica) e Gestione risorse di Azure. Se si utilizza Azure da qualche tempo, si dispone probabilmente di macchine virtuali e ruoli di istanza di Azure in esecuzione su una rete virtuale classica. Le macchine virtuali e le istanze del ruolo più recenti potrebbero invece essere in esecuzione su una rete virtuale creata in Gestione risorse di Azure.

In queste situazioni è consigliabile accertarsi che la nuova infrastruttura sia in grado di comunicare con le risorse classiche. È possibile farlo creando una connessione VPN tra le due reti virtuali.

In questo articolo verrà illustrato come creare una connessione VPN da sito a sito (S2S) tra una rete virtuale classica e una rete virtuale di Gestione risorse di Azure.

>[AZURE.NOTE] In questo articolo si presuppone che si disponga già di reti virtuali classiche e di reti virtuali di Gestione risorse di Azure e che si abbia familiarità con la configurazione di una connessione VPN S2S per reti virtuali classiche. Per una soluzione end-to-end dettagliata sulla connettività VPN S2S tra reti virtuali classiche e di Gestione risorse di Azure, visitare [Guida alle soluzioni: connessione di una rete virtuale classica a una rete virtuale di Gestione risorse di Azure utilizzando una VPN S2S](virtual-networks-arm-asm-s2s.md).

È possibile visualizzare una panoramica delle attività da eseguire per creare una connessione VPN S2S tra una rete virtuale classica e una rete virtuale di Gestione risorse di Azure utilizzando i gateway di Azure riportati di seguito.

1 - [Creazione di un gateway VPN per la rete virtuale classica](#Step-1:-Create-a-VPN-gateway-for-the-classic-VNet)

2 - [Creazione di un gateway VPN per la rete virtuale di Gestione risorse di Azure](#Step-2:-Create-a-VPN-gateway-for-the-ARM-VNet)

3 - [Creazione di una connessione tra i gateway](#Step-3:-Create-a-connection-between-the-gateways)

## Passaggio 1: creazione di un gateway VPN per la rete virtuale classica

Per creare il gateway VPN per la rete virtuale classica, seguire le istruzioni riportate di seguito.

1. Aprire il portale classico da https://manage.windowsazure.com e immettere le credenziali, se necessario.
2. Nell'angolo in basso a sinistra della schermata, fare clic sul pulsante **NUOVO**, su **SERVIZI DI RETE**, su **RETI VIRTUALI**, quindi su **AGGIUNGI RETE LOCALE**.
3. Nella finestra **Specifica i dettagli della rete locale**, digitare un nome per la rete virtuale di Gestione risorse di Azure a cui si desidera effettuare la connessione, quindi nell'angolo in basso a destra della finestra, fare clic sul pulsante con la freccia.
3. Nella casella di testo dello spazio degli indirizzi **IP INIZIALE**, digitare il prefisso di rete per la rete virtuale di Gestione risorse di Azure a cui si desidera effettuare la connessione.
4. Nell'elenco a discesa **CIDR (CONTEGGIO INDIRIZZI)**, selezionare il numero di bit utilizzati per la porzione di rete del blocco CIDR usato dalla rete virtuale di Gestione risorse di Azure a cui si desidera effettuare la connessione.
5. In **INDIRIZZO IP DISPOSITIVI VPN (FACOLTATIVO)**, digitare qualsiasi indirizzo IP pubblico valido. Questo indirizzo IP verrà modificato in un secondo momento. Quindi fare clic sul pulsante con il segno di spunta in basso a destra della schermata. Nella figura seguente vengono illustrate le impostazioni di esempio per questa pagina.

	![Impostazioni Rete locale](.\media\virtual-networks-arm-asm-s2s-howto\figurex1.png)

5. Nella pagina **Reti**, fare clic su**RETI VIRTUALI**, fare clic su una rete virtuale classica, quindi fare clic su **CONFIGURA**.
6. In **Connettività da sito a sito**, abilita la casella di controllo **Connetti alla rete locale**.
7. Selezionare la rete locale creata nel passaggio 4 nell'elenco delle reti disponibili dall'elenco a discesa **RETE LOCALE**, quindi fare clic su **SALVA**.
8. Una volta che le impostazioni vengono salvate, fare clic su **DASHBOARD**, quindi nella parte inferiore della pagina, fare clic su **CREA GATEWAY**, su **ROUTING DINAMICO** e su **SÌ**.
9. Attendere che il gateway venga creato e copiare il relativo indirizzo IP pubblico. Sarà necessario configurare il gateway nella rete virtuale di Gestione risorse di Azure.

## Passaggio 2: creazione di un gateway VPN per la rete virtuale di Gestione risorse di Azure

Per creare un gateway VPN per la rete virtuale di Gestione risorse di Azure, seguire le istruzioni riportate di seguito.

1. Da una console di PowerShell creare una rete locale eseguendo il comando riportato di seguito. La rete locale deve utilizzare il blocco CIDR della rete virtuale classica a cui si desidera effettuare la connessione e l'indirizzo IP pubblico del gateway creato nel passaggio 1 riportato in precedenza.

		New-AzureRmLocalNetworkGateway -Name VNetClassicNetwork `
			-Location "East US" -AddressPrefix "10.0.0.0/20" `
			-GatewayIpAddress "168.62.190.190" -ResourceGroupName RG1

3. Creare un indirizzo IP pubblico del gateway utilizzando il comando riportato di seguito.

		$ipaddress = New-AzureRmPublicIpAddress -Name gatewaypubIP `
			-ResourceGroupName RG1 -Location "East US" `
			-AllocationMethod Dynamic

4. Recuperare la subnet utilizzata per il gateway utilizzando il comando riportato di seguito.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
			-VirtualNetwork (Get-AzureRMVirtualNetwork -Name VNetARM -ResourceGroupName RG1) 

	>[AZURE.IMPORTANT] La subnet del gateway deve esistere già e deve essere denominata GatewaySubnet.

5. Creare un oggetto di configurazione IP per il gateway eseguendo il comando riportato di seguito. La subnet del gateway richiede un ID. Tale subnet deve esistere nella rete virtuale.


		$ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
		-Name ipconfig -SubnetId $subnet.id `
		-PublicIpAddressId $ipaddress.id

	>[AZURE.IMPORTANT] I parametri *SubnetId* e *PublicIpAddressId* devono ricevere rispettivamente la proprietà ID dalla subnet e gli oggetti degli indirizzi IP. Non è possibile utilizzare una stringa semplice.
	
5. Creare il gateway della rete virtuale di Gestione risorse di Azure utilizzando il comando riportato di seguito.

		New-AzureRmVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1 `
			-Location "East US" -GatewaySKU Standard -GatewayType Vpn -IpConfigurations $ipconfig `
			-EnableBgp $false -VpnType RouteBased

6. Una volta creato il gateway VPN, recuperare il relativo indirizzo IP pubblico utilizzando il comando riportato di seguito. Copiare l'indirizzo IP, sarà necessario configurare la rete locale per la rete virtuale classica.

		Get-AzureRmPublicIpAddress -Name gatewaypubIP -ResourceGroupName RG1

## Passaggio 3: creazione di una connessione tra i gateway

1. Aprire il portale classico da https://manage.windowsazure.com e immettere le credenziali, se necessario.
2. Nel portale classico, scorrere verso il basso e fare clic su **RETI**, su **RETI LOCALI**, sulla rete virtuale di Gestione risorse di Azure a cui si desidera effettuare la connessione, quindi sul pulsante **MODIFICA**.
3. In **INDIRIZZO IP DISPOSITIVI VPN (FACOLTATIVO)**, digitare l'indirizzo IP per il gateway della rete virtuale di Gestione risorse di Azure recuperato nel passaggio 2 riportato in precedenza, fare clic sulla freccia a destra nell'angolo in basso a destra, quindi fare clic sul pulsante con il segno di spunta.
4. Da una console di PowerShell impostare una chiave condivisa eseguendo il comando riportato di seguito. Assicurarsi di modificare i nomi delle reti virtuali con i propri.

		Set-AzureVNetGatewayKey -VNetName VNetClassic `
			-LocalNetworkSiteName VNetARM -SharedKey abc123

7. Creare la connessione VPN utilizzando i comandi riportati di seguito.

		$vnet01gateway = Get-AzureRmLocalNetworkGateway -Name VNetClassic -ResourceGroupName RG1
		$vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1
		
		New-AzureRmVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
			-ResourceGroupName RG1 -Location "East US" -VirtualNetworkGateway1 $vnet02gateway `
			-LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
			-RoutingWeight 10 -SharedKey 'abc123'

## Passaggi successivi

- Leggere ulteriori informazioni su [NRP (Network Resource Provider) per Gestione risorse di Azure](resource-groups-networking.md).
- Creare una [soluzione end-to-end che connette una rete virtuale classica a una rete virtuale di Gestione risorse di Azure utilizzando una VPN S2S](virtual-networks-arm-asm-s2s.md).

<!---HONumber=AcomDC_0803_2016-->