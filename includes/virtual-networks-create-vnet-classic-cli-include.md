## Come creare una rete virtuale classica con l’Interfaccia della riga di comando di Azure

È possibile utilizzare l'interfaccia della riga di comando (CLI) di Azure per gestire le risorse di Azure dal prompt dei comandi da qualsiasi computer con Windows, Linux o OSX. Per creare una rete virtuale utilizzando l'interfaccia della riga di comando di Azure seguire questa procedura.

1. Se l'interfaccia della riga di comando di Azure non è mai stata usata, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../articles/xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si selezionano l'account e la sottoscrizione di Azure.
2. Eseguire il comando per **creare reti virtuali di Azure** per creare una rete virtuale e una subnet, come illustrato di seguito. Nell'elenco riportato dopo l'output sono indicati i parametri usati.

			azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
	
	Output previsto:

			info:    Executing command network vnet create
			+ Looking up network configuration
			+ Looking up locations
			+ Setting network configuration
			info:    network vnet create command OK

	- **--vnet**. Nome della rete virtuale da creare. Per questo scenario, *TestVNet*
	- **-e (o --address-space)**. Spazio degli indirizzi della rete virtuale. Per questo scenario, *192.168.0.0*
	- **-i (o -cidr)**. Maschera di rete nel formato CIDR. Per questo scenario, *16*
	- **- n (o --subnet-name**). Nome della prima subnet. Per questo scenario, *FrontEnd*.
	- **-p (or --subnet-start-ip)**. Indirizzo IP iniziale per la subnet o spazio di indirizzi della subnet. Per questo scenario, *192.168.1.0*
	- **-r (o --subnet-cidr)**. Maschera di rete nel formato CIDR per la subnet. Per questo scenario, *24*
	- **-l (o --location)**. Area di Azure in cui verrà creata la rete virtuale. Per questo scenario, *Central US*.

3. Eseguire il comando per **creare la subnet della rete virtuale di Azure** per creare una subnet, come illustrato di seguito. Nell'elenco riportato dopo l'output sono indicati i parametri usati.

			azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
	
	Di seguito è riportato l'output previsto per il comando precedente:

			info:    Executing command network vnet subnet create
			+ Looking up network configuration
			+ Creating subnet "BackEnd"
			+ Setting network configuration
			+ Looking up the subnet "BackEnd"
			+ Looking up network configuration
			data:    Name                            : BackEnd
			data:    Address prefix                  : 192.168.2.0/24
			info:    network vnet subnet create command OK

	- **-t (o --vnet-name**. Nome della rete virtuale in cui verrà creata la subnet. Per questo scenario, *TestVNet*.
	- **-n (o --name)**. Nome della nuova subnet. Per questo scenario, *BackEnd*.
	- **-a (o --address-prefix)**. Blocco CIDR della subnet. Per questo scenario, *192.168.2.0/24*.

4. Eseguire il comando **azure network vnet show** per visualizzare le proprietà della nuova rete virtuale, come illustrato di seguito.

			azure network vnet show

	Di seguito è riportato l'output previsto per il comando precedente:

			info:    Executing command network vnet show
			Virtual network name: TestVNet
			+ Looking up the virtual network sites
			data:    Name                            : TestVNet
			data:    Location                        : Central US
			data:    State                           : Created
			data:    Address space                   : 192.168.0.0/16
			data:    Subnets:
			data:      Name                          : FrontEnd
			data:      Address prefix                : 192.168.1.0/24
			data:
			data:      Name                          : BackEnd
			data:      Address prefix                : 192.168.2.0/24
			data:
			info:    network vnet show command OK

<!---HONumber=AcomDC_0323_2016-->