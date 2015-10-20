## Come creare una rete virtuale con PowerShell

Per creare una rete virtuale con PowerShell, seguire questa procedura.

1. Se è la prima volta che si usa Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md) e seguire le istruzioni fino al termine della procedura per accedere ad Azure e selezionare la sottoscrizione preferita.
	
3. Se necessario, eseguire il cmdlet **New-AzureRMResourceGroup** per creare un nuovo gruppo di risorse, come illustrato di seguito. Per questo scenario, creare un gruppo di risorse denominato *TestRG*. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Gestione risorse di Azure](resource-group-overview.md/#resource-groups).

		New-AzureRMResourceGroup -Name TestRG -Location centralus

	Output previsto:
	
		ResourceGroupName : TestRG
		Location          : centralus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		                    Actions  NotActions
		                    =======  ==========
		                    *
		
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG	

4. Eseguire il cmdlet **New-AzureRMVirtualNetwork** per creare una nuova rete virtuale, come illustrato di seguito.

		New-AzureRMVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
			-AddressPrefix 192.168.0.0/16 -Location centralus	
		
	Output previsto:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : []

5. Eseguire il cmdlet **Get-AzureRMVirtualNetwork** per archiviare l'oggetto rete virtuale in una variabile, come illustrato di seguito.

		$vnet = Get-AzureRMVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
	
	>[AZURE.TIP]È possibile combinare i passaggi 4 e 5 eseguendo **$vnet = New-AzureRMVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus**.

6. Eseguire il cmdlet **Add-AzureRMVirtualNetworkSubnetConfig** per aggiungere una subnet alla nuova rete virtuale, come illustrato di seguito.

		Add-AzureRMVirtualNetworkSubnetConfig -Name FrontEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
		
	Output previsto:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": null,
		                        "Id": null,
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": null,
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": null
		                      }
		                    ]

7. Ripetere il passaggio 6 per ogni subnet che si desidera creare. Il comando seguente crea la subnet *BackEnd* per questo scenario.

		Add-AzureRMVirtualNetworkSubnetConfig -Name BackEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

8. Quando si crea una subnet, questa rimane solo nella variabile locale usata per recuperare la rete virtuale creata nel passaggio 4. Per salvare le modifiche in Azure, eseguire il cmdlet **Set-AzureRMVirtualNetwork**, come illustrato di seguito.

		Set-AzureRMVirtualNetwork -VirtualNetwork $vnet	
		
	Output previsto:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": []
		                    }
		NetworkInterfaces : null
		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      },
		                      {
		                        "Name": "BackEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
		                        "AddressPrefix": "192.168.2.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]

<!---HONumber=Oct15_HO3-->