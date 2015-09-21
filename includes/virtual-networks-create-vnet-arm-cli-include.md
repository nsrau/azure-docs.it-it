## Come creare una rete virtuale con l'interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per gestire le risorse di Azure dal prompt dei comandi di qualsiasi computer con Windows, Linux o OSX. Per creare una rete virtuale con l'interfaccia della riga di comando di Azure, seguire questa procedura.

1. Se non è mai stata usata l'interfaccia della riga di comando di Azure, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](xplat-cli.md) e seguire le istruzioni fino al punto in cui si seleziona l'account e la sottoscrizione di Azure.
2. Eseguire il comando **azure config mode** per passare alla modalità di gestione delle risorse, come illustrato di seguito.

		azure config mode arm

	Di seguito è riportato l'output previsto per il comando precedente:

		info:    New mode is arm

3. Se necessario, eseguire il comando **azure group create** per creare un nuovo gruppo di risorse, come illustrato di seguito. Si noti l'output del comando. Nell'elenco riportato dopo l'output sono indicati i parametri usati. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Gestione risorse di Azure](resource-group-overview.md/#resource-groups).

		azure group create -n TestRG -l centralus

	Di seguito è riportato l'output previsto per il comando precedente:

		info:    Executing command group create
		+ Getting resource group TestRG
		+ Creating resource group TestRG
		info:    Created resource group TestRG
		data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            centralus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

	- **-n (o --name)**. Nome del nuovo gruppo di risorse. Per questo scenario, *TestRG*.
	- **-l (o --location)**. L'area di Azure in cui verrà creato il nuovo gruppo di risorse. Per questo scenario, *centralus*.

4. Eseguire il comando per **creare reti virtuali di Azure** per creare una rete virtuale e una subnet, come illustrato di seguito.

		azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

	Di seguito è riportato l'output previsto per il comando precedente:

		info:    Executing command network vnet create
		+ Looking up virtual network "TestVNet"
		+ Creating virtual network "TestVNet"
		+ Loading virtual network state
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : centralus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		info:    network vnet create command OK

	- **-g (o --resource-group)**. Nome del gruppo di risorse in cui verrà creata la rete virtuale. Per questo scenario, *TestRG*.
	- **-n (o --name)**. Nome della rete virtuale da creare. Per questo scenario, *TestVNet*
	- **-a (o --address-prefixes)**. Elenco di blocchi CIDR usati per lo spazio degli indirizzi della rete virtuale. Per questo scenario, *192.168.0.0/16*
	- **-l (o --location)**. La regione in cui verrà creata la rete virtuale. Per questo scenario, *centralus*.

5. Eseguire il comando per **creare la subnet della rete virtuale di Azure** per creare una subnet, come illustrato di seguito. Notare l'output del comando. Nell'elenco riportato dopo l'output sono indicati i parametri usati.

		azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

	Di seguito è riportato l'output previsto per il comando precedente:

		info:    Executing command network vnet subnet create
		+ Looking up the subnet "FrontEnd"
		+ Creating subnet "FrontEnd"
		+ Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:
		info:    network vnet subnet create command OK

	- **-e (o --vnet-name**. Nome della rete virtuale in cui verrà creata la subnet. Per questo scenario, *TestVNet*.
	- **-n (o --name)**. Nome della nuova subnet. Per questo scenario, *FrontEnd*.
	- **-a (o --address-prefix)**. Blocco CIDR di subnet. Per questo scenario, *192.168.1.0/24*.

6. Ripetere il passaggio 5 per creare altre subnet, se necessario. Per questo scenario, eseguire il comando seguente per creare la subnet *BackEnd*.

		azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. Eseguire il comando **azure network vnet show** per visualizzare le proprietà della nuova rete virtuale, come illustrato di seguito.

		azure network vnet show -g TestRG -n TestVNet

	Di seguito è riportato l'output previsto per il comando precedente:

		info:    Executing command network vnet show
		+ Looking up virtual network "TestVNet"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : centralus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		data:    Subnets:
		data:      Name                          : FrontEnd
		data:      Address prefix                : 192.168.1.0/24
		data:
		data:      Name                          : BackEnd
		data:      Address prefix                : 192.168.2.0/24
		data:
		info:    network vnet show command OK

<!---HONumber=Sept15_HO2-->