## Come creare una rete virtuale utilizzando un file di configurazione di rete da PowerShell

Azure utilizza un file xml per definire tutte le reti virtuali disponibili per una sottoscrizione. È possibile scaricare questo file e modificarlo per eliminare o modificare le reti virtuali esistenti oppure per crearne di nuove. In questo documento viene illustrato come scaricare questo file, definito come file di configurazione di rete (o netcgf), e modificarlo per creare una nuova rete virtuale. Per altre informazioni sul file di configurazione di rete, vedere [Schema di configurazione della rete virtuale di Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Per creare una rete virtuale utilizzando un file netcfg tramite PowerShell, attenersi alla procedura seguente.

1. Se è la prima volta che si utilizza Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md) e seguire le istruzioni fino al termine della procedura per accedere ad Azure e selezionare la sottoscrizione desiderata.
2. Dalla console di Azure PowerShell, utilizzare il cmdlet **Get AzureVnetConfig** per scaricare il file di configurazione di rete eseguendo il comando riportato di seguito. 

		Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml

	Output previsto:

		XMLConfiguration                                                                                                     
		----------------                                                                                                     
		<?xml version="1.0" encoding="utf-8"?>...  

3. Aprire il file salvato nel passaggio 2 utilizzando un qualsiasi editor di testo o XML e cercare l’elemento **<VirtualNetworkSites>**. Se esistono altre reti già create, ogni rete sarà visualizzata con il relativo elemento **<VirtualNetworkSite>**.
4. Per creare la rete virtuale descritta in questo scenario, aggiungere il codice XML seguente sotto l’elemento **<VirtualNetworkSites>**:

		<VirtualNetworkSite name="TestVNet" Location="Central US">
		  <AddressSpace>
		    <AddressPrefix>192.168.0.0/16</AddressPrefix>
		  </AddressSpace>
		  <Subnets>
		    <Subnet name="FrontEnd">
		      <AddressPrefix>192.168.1.0/24</AddressPrefix>
		    </Subnet>
		    <Subnet name="BackEnd">
		      <AddressPrefix>192.168.2.0/24</AddressPrefix>
		    </Subnet>
		  </Subnets>
		</VirtualNetworkSite>

9.  Salvare il file di configurazione di rete.
10. Dalla console di Azure PowerShell, utilizzare il cmdlet **Set AzureVnetConfig** per caricare il file di configurazione di rete eseguendo il comando riportato di seguito. Notare l'output del comando. Deve essere visualizzato **Succeeded** in **OperationStatus**. In caso contrario, controllare se il file xml contiene errori.

		Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml

	Di seguito è riportato l'output previsto per il comando precedente:

		OperationDescription OperationId                          OperationStatus
		-------------------- -----------                          ---------------
		Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 
	
11. Nella console di Azure PowerShell utilizzare il cmdlet **Get-AzureVnetSite** per verificare che la nuova rete sia stata aggiunta eseguendo il comando riportato di seguito.

		Get-AzureVNetSite -VNetName TestVNet

	Di seguito è riportato l'output previsto per il comando precedente:

		AddressSpacePrefixes : {192.168.0.0/16}
		Location             : Central US
		AffinityGroup        : 
		DnsServers           : {}
		GatewayProfile       : 
		GatewaySites         : 
		Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
		InUse                : False
		Label                : 
		Name                 : TestVNet
		State                : Created
		Subnets              : {FrontEnd, BackEnd}
		OperationDescription : Get-AzureVNetSite
		OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
		OperationStatus      : Succeeded

<!---HONumber=Oct15_HO3-->