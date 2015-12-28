<properties 
   pageTitle="Come impostare un indirizzo IP privato statico in modalità ARM mode mediante l'infrastruttura CLI| Microsoft Azure"
   description="Informazioni su indirizzi IP statici (DIP) e come gestirli in modalità ARM tramite l'infrastruttura CLI"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Come impostare un indirizzo IP statico privato nell'infrastruttura CLI di Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione Gestione risorse. È anche possibile [gestire un indirizzo IP statico privato nel modello di distribuzione classico](virtual-networks-static-private-ip-classic-cli.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

I comandi di esempio infrastruttura CLI di Azure riportati di seguito prevedono un ambiente semplice già creato. Per eseguire i comandi illustrati in questo documento, creare prima di tutto l'ambiente di prova descritto in [creare una rete virtuale](virtual-networks-create-vnet-arm-cli.md).

## Come specificare un indirizzo IP statico privato durante la creazione di una macchina virtuale
Per creare una macchina virtuale denominata *DNS01* nella subnet *FrontEnd* di una rete virtuale denominata *TestVNet* con un indirizzo IP statico privato di *192.168.1.101*, seguire questa procedura:

1. Se l'interfaccia della riga di comando di Azure non è mai stata usata, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si selezionano l'account e la sottoscrizione di Azure.

2. Eseguire il comando **azure config mode** per passare alla modalità Gestione risorse, come illustrato di seguito.

		azure config mode arm

	Output previsto:

		info:    New mode is arm

3. Eseguire il comando **azure network public-ip create** per creare un indirizzo IP per la VM. Nell'elenco riportato dopo l'output sono indicati i parametri usati.

		azure network public-ip create -g TestRG -n TestPIP -l centralus
	
	Output previsto:

		info:    Executing command network public-ip create
		+ Looking up the public ip "TestPIP"
		+ Creating public ip address "TestPIP"
		+ Looking up the public ip "TestPIP"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
		data:    Name                            : TestPIP
		data:    Type                            : Microsoft.Network/publicIPAddresses
		data:    Location                        : centralus
		data:    Provisioning state              : Succeeded
		data:    Allocation method               : Dynamic
		data:    Idle timeout                    : 4
		info:    network public-ip create command OK

	- **-g (o --resource-group)**. Nome del gruppo di risorse in cui verrà creato l'indirizzo IP pubblico.
	- **-n (o --name)**. Nome dell'indirizzo IP pubblico.
	- **-l (o --location)**. Area di Azure in cui verrà creato l'indirizzo IP pubblico. Per questo scenario, *centralus*.

3. Eseguire il comando **azure network nic create** per creare un gruppo NIC con indirizzo IP privato statico. Nell'elenco riportato dopo l'output sono indicati i parametri usati.

		azure network nic create -g TestRG -n TestNIC -l centralus -a 192.168.1.101 -m TestVNet -k FrontEnd

	Output previsto:

		+ Looking up the network interface "TestNIC"
		+ Looking up the subnet "FrontEnd"
		+ Creating network interface "TestNIC"
		+ Looking up the network interface "TestNIC"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
		data:    Name                            : TestNIC
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : centralus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.1.101
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:
		info:    network nic create command OK

	- **-a (o --private-ip-address)**. Indirizzo IP privato statico per il gruppo NIC.
	- **-m (o --subnet-vnet-name)**. Nome della rete virtuale in cui verrà creato il gruppo NIC.
	- **-k (o --subnet-name)**. Nome della subnet in cui verrà creato il gruppo NIC.

4. Eseguire il comando **azure vm create** per creare la VM usando l'indirizzo IP pubblico e il gruppo NIC creati in precedenza. Nell'elenco riportato dopo l'output sono indicati i parametri usati.

		azure vm create -g TestRG -n DNS01 -l centralus -y Windows -f TestNIC -i TestPIP -F TestVNet -j FrontEnd -o vnetstorage -q bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 -u adminuser -p AdminP@ssw0rd

	Output previsto:

		info:    Executing command vm create
		+ Looking up the VM "DNS01"
		info:    Using the VM Size "Standard_A1"
		warn:    The image "bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2" will be used for VM
		info:    The [OS, Data] Disk or image configuration requires storage account
		+ Looking up the storage account vnetstorage
		+ Looking up the NIC "TestNIC"
		info:    Found an existing NIC "TestNIC"
		info:    Found an IP configuration with virtual network subnet id "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "TestNIC"
		info:    Found public ip parameters, trying to setup PublicIP profile
		+ Looking up the public ip "TestPIP"
		info:    Found an existing PublicIP "TestPIP"
		info:    Configuring identified NIC IP configuration with PublicIP "TestPIP"
		+ Updating NIC "TestNIC"
		+ Looking up the NIC "TestNIC"
		+ Creating VM "DNS01"
		info:    vm create command OK

	- **-y (o --os-type)**. Tipo di sistema operativo per la VM, ovvero *Windows* o *Linux*.
	- **-f (o --nic-name)**. Nome del gruppo NIC che verrà usato dalla VM.
	- **-i (o --public-ip-name)**. Nome dell'indirizzo IP pubblico che verrà usato dalla VM.
	- **-F (o --vnet-name)**. Nome della rete virtuale in cui verrà creata la VM.
	- **-j (o --vnet-subnet-name)**. Nome della subnet in cui verrà creata la VM.

## Come recuperare le informazioni relative all'indirizzo IP privato statico per una macchina virtuale

Per visualizzare le informazioni relative all'indirizzo IP privato statico per la macchina virtuale creata con lo script precedente, eseguire il comando dell'infrastruttura CLI di Azure e osservare i valori per *Private IP alloc-method* e *Private IP address*:

	azure vm show -g TestRG -n DNS01

Output previsto:

	info:    Executing command vm show
	+ Looking up the VM "DNS01"
	+ Looking up the NIC "TestNIC"
	+ Looking up the public ip "TestPIP
	data:    Id                              :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
	data:    ProvisioningState               :Succeeded
	data:    Name                            :DNS01
	data:    Location                        :centralus
	data:    Type                            :Microsoft.Compute/virtualMachines
	data:
	data:    Hardware Profile:
	data:      Size                          :Standard_A1
	data:
	data:    Storage Profile:
	data:      Source image:
	data:        Id                          :/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/services/images/bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
	data:
	data:      OS Disk:
	data:        OSType                      :Windows
	data:        Name                        :cli08d7bd987a0112a8-os-1441774961355
	data:        Caching                     :ReadWrite
	data:        CreateOption                :FromImage
	data:        Vhd:
	data:          Uri                       :https://vnetstorage2.blob.core.windows.net/vhds/cli08d7bd987a0112a8-os-1441774961355vhd
	data:
	data:    OS Profile:
	data:      Computer Name                 :DNS01
	data:      User Name                     :adminuser
	data:      Windows Configuration:
	data:        Provision VM Agent          :true
	data:        Enable automatic updates    :true
	data:
	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-90-1A-A8
	data:          Provisioning State        :Succeeded
	data:          Name                      :TestNIC
	data:          Location                  :centralus
	data:            Private IP alloc-method :Static
	data:            Private IP address      :192.168.1.101
	data:            Public IP address       :40.122.213.159
	info:    vm show command OK

## Come rimuovere un indirizzo IP statico privato da una macchina virtuale
Non è possibile rimuovere un indirizzo IP privato statico da un gruppo NIC nell'infrastruttura CLI di Azure per Gestione risorse. È necessario creare un nuovo gruppo NIC che usa un indirizzo IP dinamico, rimuovere il gruppo NIC precedente dalla VM, quindi aggiungere il nuovo gruppo NIC alla VM. Per cambiare il gruppo NIC per la VM usata nei comandi precedenti, seguire questa procedura.
	
1. Eseguire il comando **azure network nic create** per creare un nuovo gruppo NIC usando l'allocazione dell'indirizzo IP dinamico. Si noti che non è necessario specificare l'indirizzo IP in questo momento.

		azure network nic create -g TestRG -n TestNIC2 -l centralus -m TestVNet -k FrontEnd

	Output previsto:

		info:    Executing command network nic create
		+ Looking up the network interface "TestNIC2"
		+ Looking up the subnet "FrontEnd"
		+ Creating network interface "TestNIC2"
		+ Looking up the network interface "TestNIC2"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
		data:    Name                            : TestNIC2
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : centralus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.1.6
		data:      Private IP Allocation Method  : Dynamic
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:
		info:    network nic create command OK

2. Eseguire il comando **azure vm set** per cambiare il gruppo NIC usato dalla VM.

		azure vm set -g TestRG -n DNS01 -N TestNIC2

	Output previsto:

		info:    Executing command vm set
		+ Looking up the VM "DNS01"
		+ Looking up the NIC "TestNIC2"
		+ Updating VM "DNS01"
		info:    vm set command OK

3. Se necessario, eseguire il comando **azure network nic delete** per eliminare il gruppo NIC precedente.

		azure network nic delete -g TestRG -n TestNIC --quiet

	Output previsto:

		info:    Executing command network nic delete
		+ Looking up the network interface "TestNIC"
		+ Deleting network interface "TestNIC"
		info:    network nic delete command OK

## Come aggiungere un indirizzo IP statico privato a una macchina virtuale esistente
Per aggiungere un indirizzo IP privato statico al gruppo NIC usato dalla macchina virtuale creata mediante lo script precedente, eseguire il comando seguente:

	azure netwrok nic set -g TestRG -n TestNIC2 -a 192.168.1.101

Output previsto:

	info:    Executing command network nic set
	+ Looking up the network interface "TestNIC2"
	+ Updating network interface "TestNIC2"
	+ Looking up the network interface "TestNIC2"
	data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
	data:    Name                            : TestNIC2
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : centralus
	data:    Provisioning state              : Succeeded
	data:    MAC address                     : 00-0D-3A-90-29-25
	data:    Enable IP forwarding            : false
	data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
	data:    IP configurations:
	data:      Name                          : NIC-config
	data:      Provisioning state            : Succeeded
	data:      Private IP address            : 192.168.1.101
	data:      Private IP Allocation Method  : Static
	data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
	data:
	info:    network nic set command OK

## Passaggi successivi

- Informazioni su [indirizzi IP pubblici riservati](../virtual-networks-reserved-public-ip).
- Informazioni su [indirizzi IP pubblici a livello di istanza (ILPIP)](../virtual-networks-instance-level-public-ip).
- Consultare le [API REST dell'indirizzo IP riservato](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=AcomDC_1217_2015-->