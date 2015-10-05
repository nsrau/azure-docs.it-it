<properties 
   pageTitle="Come connettere reti virtuali classiche a reti virtuali di Gestione risorse di Azure in Azure - Guida alla soluzione"
   description="Informazioni su come creare una connessione VPN tra reti virtuali classiche e nuove reti virtuali"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/18/2015"
   ms.author="telmos" />

# Connessione di reti virtuali classiche a nuove reti virtuali

Azure attualmente ha due modalità di gestione: Service Manager di Azure (detta classica) e Gestione risorse di Azure. Se si utilizza Azure da qualche tempo, si dispone probabilmente di macchine virtuali e ruoli di istanza di Azure in esecuzione su una rete virtuale classica. Le macchine virtuali e le istanze del ruolo più recenti potrebbero invece essere in esecuzione su una rete virtuale creata in Gestione risorse di Azure.

In queste situazioni è consigliabile accertarsi che la nuova infrastruttura sia in grado di comunicare con le risorse classiche. A questo scopo è possibile creare una connessione VPN tra le due reti virtuali. La figura seguente illustra un ambiente di esempio con due reti virtuali (classica e di Gestione risorse di Azure), con connettività tramite un tunnel sicuro tra le reti virtuali.

![](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure01.png)

>[AZURE.NOTE]Questo documento descrive una soluzione end-to-end a scopo di test. Se le reti virtuali sono già configurate e si ha familiarità con i gateway VPN e la connessione da sito a sito in Azure, vedere [Configurare una VPN S2S tra una rete virtuale di Gestione risorse di Azure e una rete virtuale classica](../virtual-networks-arm-asm-s2s-howto.md).

Per testare questo scenario, si procederà come indicato di seguito:

1. [Creare un ambiente di rete virtuale classica](#Create-a-classic-VNet-environment).
2. [Creare un nuovo ambiente di rete virtuale](#Create-a-new-VNet-environment).
3. [Connettere le due reti virtuali](#Connect-the-two-VNets).

Si eseguirà la procedura descritta sopra usando prima di tutto gli strumenti di gestione di Azure classici, tra cui il portale classico, i file di configurazione di rete e i cmdlet PowerShell di Service Manager di Azure. In seguito si passerà ai nuovi strumenti di gestione, ad esempio il nuovo portale di Azure, i modelli di Gestione risorse di Azure e i cmdlet PowerShell di Gestione risorse di Azure.

>[AZURE.IMPORTANT]Per consentire la connessione tra le reti virtuali, non dovranno esserci conflitti a livello di blocco CIDR. Verificare che ogni rete virtuale abbia un blocco CIDR univoco.

## Creare un ambiente di rete virtuale classica

È possibile usare una rete virtuale classica esistente per connettersi a una rete virtuale di Gestione risorse di Azure. In questo esempio si vedrà come creare una nuova rete virtuale classica con due subnet, un gateway e una macchina virtuale a scopo di test.

### Passaggio 1: Creare una rete virtuale classica

Per creare una nuova rete virtuale corrispondente alla figura 1 precedente, seguire le istruzioni riportate di seguito.

1. Dalla console PowerShell aggiungere l'account Azure tramite il comando seguente.

		Add-AzureAccount

2. Seguire le istruzioni nella finestra di dialogo visualizzata per accedere con l'account Azure.

3. Accertarsi di usare i cmdlet PowerShell di Gestione servizi di Azure tramite il comando seguente.

		Switch-AzureMode AzureServiceManagement

4. Scaricare il file di configurazione di rete di Azure tramite il comando seguente.

		Get-AzureVNetConfig -ExportToFile c:\Azure\classicvnets.netcfg

		XMLConfiguration                                OperationDescription                            OperationId                                     OperationStatus                                
		----------------                                --------------------                            -----------                                     ---------------                                
		<?xml version="1.0" encoding="utf-8"?>...       Get-AzureVNetConfig                             04ab3224-7e1c-cc1a-8b75-96c6c300ddb8            Succeeded       

5. Aprire il file scaricato e aggiungere un sito di rete locale denominato *vnet02* che usa *10.2.0.0/16* come prefisso dell'indirizzo e un indirizzo IP pubblico valido qualsiasi come indirizzo del gateway VPN. A questo scopo, aggiungere un elemento **LocalNetworkSite** all'elemento **LocalNetworkSites** nel file di configurazione. Il frammento di file riportato di seguito mostra un elemento **LocalNetworksSites** di esempio.

	    <LocalNetworkSites>
	      <LocalNetworkSite name="vnet02">
	        <AddressSpace>
	          <AddressPrefix>10.2.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <VPNGatewayAddress>2.0.0.2</VPNGatewayAddress>
	      </LocalNetworkSite>
	    </LocalNetworkSites>		

6. Nell'elemento **VirtualNetworkSites** aggiungere una nuova rete virtuale con un prefisso dell'indirizzo *10.1.0.0/16* e due subnet come nella figura dello scenario precedente. Il frammento di file riportato di seguito mostra un elemento **VirtualNetworkSites** di esempio.
	
	    <VirtualNetworkSites>
	      <VirtualNetworkSite name="vnet01" Location="East US">
	        <AddressSpace>
	          <AddressPrefix>10.1.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <Subnets>
	          <Subnet name="Subnet1">
	            <AddressPrefix>10.1.0.0/24</AddressPrefix>
	          </Subnet>
	          <Subnet name="GatewaySubnet">
	            <AddressPrefix>10.1.200.0/29</AddressPrefix>
	          </Subnet>
	        </Subnets>
	      </VirtualNetworkSite>
	    </VirtualNetworkSites>

7. Salvare il file, quindi caricarlo in Azure tramite il comando seguente. Assicurarsi di modificare il percorso del file in base al proprio ambiente.

		Set-AzureVNetConfig -ConfigurationPath c:\Azure\classicvnets.netcfg

		OperationDescription                                            OperationId                                                     OperationStatus                                                
		--------------------                                            -----------                                                     ---------------                                                
		Set-AzureVNetConfig                                             e0ee6e66-9167-cfa7-a746-7cab93c22013                            Succeeded 

### Passaggio 2: Creare una macchina virtuale nella rete virtuale classica

Per creare una macchina virtuale nella rete virtuale classica usando i cmdlet PowerShell di Service Manager di Azure, seguire le istruzioni seguenti.

1. Recuperare un'immagine VM da Azure. Il comando PowerShell seguente recupera l'ultima immagine di Windows Server 2012 R2 disponibile.

		$WinImage = (Get-AzureVMImage `
		    | ?{$_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"} `
		    | sort PublishedDate -Descending)[0]		

2. Creare un account di archiviazione per archiviare il disco rigido virtuale (VHD) per la VM tramite il comando seguente.

		$storage1 = New-AzureStorageAccount `
			-StorageAccountName v1v2teststorage1 `
		    -Location "East US"	

3.  Creare la VM tramite i comandi seguenti. Assicurarsi di sostituire i valori per nome utente e password.

		$vm1 = New-AzureVMConfig -Name "VM01" -InstanceSize "ExtraLarge" `
		    -Image $WinImage.ImageName –AvailabilitySetName "MyAVSet1" `
		    -MediaLocation "https://v1v2teststorage1.blob.core.windows.net/vhd/vm01.vhd"
		Add-AzureProvisioningConfig –VM $vm1 -Windows `
		    -AdminUserName "user" -Password "P@ssw0rd" 

4.  Connettere la VM a *Subnet1* tramite i comandi seguenti.

		Set-AzureSubnet -SubnetNames "Subnet1" -VM $vm1
		Set-AzureStaticVNetIP  -IPAddress "10.1.0.101" -VM $vm1

5. Creare un nuovo servizio cloud per ospitare la VM tramite il comando seguente.

		New-AzureService -ServiceName "v1v2svc01" -Location "East US"
 		New-AzureVM -ServiceName "v1v2svc01" –VNetName "vnet01" –VMs $vm1

### Passaggio 3: Creare un gateway VPN per la rete virtuale classica 

Per creare il gateway VPN per vnet01 tramite il portale di Azure classico, seguire le istruzioni riportate di seguito.

1. Aprire il portale di Azure classico all'indirizzo https://manage.windowsazure.com. Se necessario, specificare le credenziali.
2. Scorrere verso il basso l'elenco **TUTTI GLI ELEMENTI** e fare clic su **RETI**.
3. Nell'elenco di reti virtuali fare clic su **vnet01** e quindi su **CONFIGURA**.
4. In **CONNETTIVITÀ DA SITO A SITO** selezionare la casella di controllo **Connetti alla rete locale**.
5. Nell'elenco **RETE LOCALE** selezionare **vnet02**, fare clic su **SALVA** e quindi su **SÌ**.
6. Fare clic su **DASHBOARD** e notare il messaggio che indica che non è ancora stato creato un gateway, come illustrato nella figura 2.

	![Dashboard di rete virtuale](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure02.png)

7. Fare clic su **CREA GATEWAY** come illustrato nella figura 3 seguente per creare un gateway VPN per vnet01.

	![Dashboard di rete virtuale](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure03.png)

8. Nell'elenco dei tipi di gateway fare clic su **DINAMICO**, quindi fare clic su **SÌ**. Notare che l'immagine del dashboard cambia mentre il gateway viene creato (visualizzato in giallo).

	![Dashboard di rete virtuale](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure04.png)

	>[AZURE.NOTE]Questa operazione potrebbe richiedere alcuni minuti.

9. Prendere nota dell'indirizzo IP pubblico del gateway dopo che è stato creato, come illustrato di seguito. Questo indirizzo sarà necessario in seguito per creare una rete locale per la rete virtuale di Gestione risorse di Azure.

	![Dashboard di rete virtuale](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure05.png)

## Creare un nuovo ambiente di rete virtuale

Ora che la rete virtuale classica è in esecuzione con una VM e un gateway, è opportuno creare la rete virtuale di Gestione risorse di Azure.

### Passaggio 1: Creare una nuova rete virtuale in Gestione risorse di Azure

Per creare la rete virtuale in Gestione risorse di Azure con due subnet e una rete locale per la rete virtuale classica usando un modello di Gestione risorse di Azure, seguire le istruzioni seguenti.

1. Scaricare i file azuredeploy.json e azuredeploy parameters.json da [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/arm-asm-s2s).
2. Aprire il file azuredeploy.json in Visual Studio e notare che il modello crea quattro risorse: 

	- **Gateway locale**: questa risorsa rappresenta il gateway creato per la rete virtuale a cui connettersi. In questo scenario il gateway per vnet01.
	- **Rete virtuale**: questa risorsa rappresenta una rete virtuale di Gestione risorse di Azure da creare. In questo scenario vnet02.
	- **IP pubblico del gateway**: questa risorsa rappresenta l'indirizzo IP pubblico del gateway da creare per la rete virtuale di Gestione risorse di Azure. 
	- **Gateway**: questa risorsa rappresenta il gateway da creare per la rete virtuale di Gestione risorse di Azure (vnet02).

3. Notare i parametri usati in questo file. La maggior parte di essi ha un valore predefinito. Questi valori possono essere modificati in base alle esigenze.

4. Aprire il file azuredeploy-parameters.json in Visual Studio. Questo file contiene i valori da usare per i parametri nel file modello. Se necessario, modificare i valori seguenti.

	- **subscriptionId**: modificare e incollare l'ID sottoscrizione. Se non si conosce l'ID sottoscrizione, eseguire il cmdlet di PowerShell **Get-AzureSubscription** per recuperare l'ID.
	- **location**: specificare il percorso di Azure in cui verrà creata la rete virtuale. In questo scenario sarà **Stati Uniti centrali**.
	- **virtualNetworkName**: si tratta del nome della rete virtuale di Gestione risorse di Azure da creare. In questo scenario **vnet02**.
	- **localGatewayName**: questa è la rete locale a cui connettersi dalla nuova rete virtuale di Gestione risorse di Azure. In questo scenario **vnet01**.
	- **localGatewayIpAddress**: questo è l'indirizzo IP pubblico del gateway creato per la rete a cui connettersi. In questo scenario si tratta dell'indirizzo IP annotato nel passaggio 9 precedente durante la creazione del gateway VPN per **vnet01**.
	- **localGatewayAddressPrefix**: questo è il blocco CIDR per la rete locale alla quale si connetterà la rete virtuale. In questo scenario la rete virtuale a cui connettersi è **vnet01** e il relativo blocco CIDR è **10.1.0.0/16**.
	- **gatewayPublicIPName**: si tratta del nome dell'oggetto IP da creare per l'indirizzo IP pubblico del gateway che sarà creato per la rete virtuale di Gestione risorse di Azure.
	- **gatewayName**: si tratta del nome dell'oggetto gateway che verrà creato per la rete virtuale di Gestione risorse di Azure.
	- **addressPrefix**: questo è il blocco CIDR per la rete virtuale di Gestione risorse di Azure. In questo scenario **10.2.0.0/16**.
	- **subnet1Prefix**: questo è il blocco CIDR per una subnet nella rete virtuale di Gestione risorse di Azure. In questo scenario **10.2.0.0/24**.
	- **gatewaySubnetPrefix**: questo è il blocco CIDR per la subnet del gateway nella rete virtuale di Gestione risorse di Azure. In questo scenario **10.2.200.0/29**.
	- **connectionName**: si tratta del nome dell'oggetto connection da creare.
	- **sharedKey**: questa è la chiave condivisa IPSec per la connessione. In questo scenario **abc123**.

5. Per creare la rete virtuale di Gestione risorse di Azure e gli oggetti correlati in un nuovo gruppo di risorse denominato **RG1**, eseguire il comando di PowerShell seguente. Assicurarsi di modificare il percorso del file di modello e del file dei parametri.

		Switch-AzureMode AzureResourceManager
		New-AzureResourceGroup -Name RG1 -Location "Central US" `
		    -TemplateFile C:\Azure\azuredeploy.json `
		    -TemplateParameterFile C:\Azure\azuredeploy-parameters.json		

	>[AZURE.NOTE]Questa operazione potrebbe richiedere alcuni minuti.

7. Dal browser passare a https://portal.azure.com/ e immettere le credenziali, se necessario.
8. Fare clic sul riquadro del gruppo di risorse **RG1** nel portale di Azure, come illustrato di seguito.

	![Dashboard di rete virtuale](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure06.png)

9. Notare le risorse aggiunte al gruppo tramite il modello di Gestione risorse di Azure.

### Passaggio 2: Creare una nuova VM in Gestione risorse di Azure

Per creare una VM nella nuova rete virtuale, dal portale di Azure seguire le istruzioni riportate di seguito.

1. Nel portale fare clic sul pulsante **NUOVO**, fare clic su **Calcolo** e quindi su **Windows Server 2012 R2 Datacenter**.
2. Nella parte inferiore del riquadro destro selezionare **Use the Resource Manager stack** in **Select a compute stack** per creare la VM in Gestione risorse di Azure, come indicato di seguito, quindi fare clic su **Crea**.

	![Dashboard di rete virtuale](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure07.png)

3. Nel pannello **Nozioni di base** immettere nome della VM, nome utente, password, sottoscrizione, gruppo di risorse e percorso per la VM e quindi fare clic su**OK**. La figura seguente mostra le impostazioni per questo scenario.

	![Dashboard di rete virtuale](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure08.png)

4. Nel pannello **Scegli una dimensione** selezionare una dimensione e quindi fare clic su **Seleziona**. Per questo scenario selezionare **D2**.
5. Nel pannello **Impostazioni** fare clic su **Rete virtuale** e quindi fare clic su **vnet02**.
6. Fare clic su **Subnet**, fare clic su **Subnet1** e quindi fare clic su **OK**. Il pannello **Riepilogo** dovrebbe essere simile alla figura seguente.

	![Dashboard di rete virtuale](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure09.png)

7. Fare clic su **OK**, quindi fare clic su **Crea** per creare la VM. Nel portale verrà visualizzato un nuovo riquadro che mostra la VM in fase di provisioning, come illustrato di seguito.

	![Dashboard di rete virtuale](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure10.png)

	>[AZURE.NOTE]Questa operazione potrebbe richiedere alcuni minuti. È possibile passare alla parte successiva di questo documento.

## Connettere le due reti virtuali

Ora sono disponibili due reti virtuali con VM connesse, quindi si possono connettere le reti virtuali tramite i gateway definiti in precedenza e procedere al testa della connessione.

### Passaggio 1: Configurare il gateway per la rete virtuale classica

È necessario configurare la rete virtuale classica per l'uso dell'indirizzo IP del gateway creato per la rete virtuale di Gestione risorse di Azure (vnet02), quindi stabilire una connessione da ogni rete virtuale. A questo scopo, seguire le istruzioni riportate di seguito.

1. Per recuperare l'indirizzo IP usato per il gateway nella rete virtuale di Gestione risorse di Azure, eseguire il comando seguente e osservare l'output. Prendere nota dell'indirizzo che sarà necessario più avanti per modificare le impostazioni della rete locale per la rete virtuale classica.

		Get-AzurePublicIpAddress | ?{$_.Name -eq "ArmAsmS2sGatewayIp"}

		Name                     : ArmAsmS2sGatewayIp
		ResourceGroupName        : RG1
		Location                 : centralus
		Id                       : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/publicIPAddresses/ArmAsmS2sGatewayIp
		Etag                     : W/"1ee6c1bd-8be1-488e-a571-77b05b49e33a"
		ProvisioningState        : Succeeded
		Tags                     : 
		PublicIpAllocationMethod : Dynamic
		IpAddress                : 23.99.213.28
		IdleTimeoutInMinutes     : 4
		IpConfiguration          : {
		                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworkGateways/ArmAsmGateway/ipConfigurations/vn
		                           etGatewayConfig"
		                           }
		DnsSettings              : null

2. Accertarsi di usare l'API di gestione del servizio Azure per i comandi di PowerShell tramite il comando seguente.

		Switch-AzureMode AzureServiceManagement

3. Scaricare il file di configurazione di rete di Azure tramite il comando seguente.

		Get-AzureVNetConfig -ExportToFile c:\Azure\classicvnets.netcfg

4. Aprire il file scaricato e modificare l'elemento **LocalNetworkSite** per **vnet02** aggiungendo l'indirizzo IP del gateway per la nuova rete virtuale ottenuto nel passaggio 1. L'elemento dovrebbe essere simile all'esempio seguente.

	      <LocalNetworkSite name="vnet03">
	        <AddressSpace>
	          <AddressPrefix>10.3.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <VPNGatewayAddress>23.99.213.28</VPNGatewayAddress>
	      </LocalNetworkSite>

5. Salvare il file, quindi eseguire il comando seguente per configurare la rete virtuale classica. Assicurarsi di modificare il percorso in modo che punti al file salvato nel passaggio 4 precedente.

		Set-AzureVNetConfig -ConfigurationPath c:\Azure\classicvnets.netcfg

6. Impostare la chiave condivisa IPSec per il gateway di rete virtuale classica tramite il comando seguente. Verrà visualizzato un output simile a quello riportato di seguito. Se si usano reti virtuali personalizzate esistenti, assicurarsi di modificarne i nomi.

		Set-AzureVNetGatewayKey -VNetName vnet01 -LocalNetworkSiteName vnet02 -SharedKey abc123 

		Error          : 
		HttpStatusCode : OK
		Id             : b2154475-bf00-480e-ad1e-aed893014979
		Status         : Successful
		RequestId      : 08257a09d723cb8982c47b85edb0e08a
		StatusCode     : OK

### Passaggio 2: Configurare il gateway per la rete virtuale di Gestione risorse di Azure

Dopo avere configurato il gateway della rete virtuale classica, è possibile stabilire la connessione. A questo scopo, seguire le istruzioni riportate di seguito.

1. Dalla console di PowerShell eseguire il comando seguente per passare alla modalità Gestione risorse di Azure. 

		Switch-AzureMode AzureResourceManager

2. Creare la connessione tra i gateway tramite i comandi seguenti.

		$vnet01gateway = Get-AzureLocalNetworkGateway -Name vnet01 -ResourceGroupName RG1
		$vnet02gateway = Get-AzureVirtualNetworkGateway -Name ArmAsmGateway -ResourceGroupName RG1
		
		New-AzureVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
			-ResourceGroupName RG1 -Location "Central US" -VirtualNetworkGateway1 $vnet02gateway `
			-LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
			-RoutingWeight 10 -SharedKey 'abc123'

3. Aprire il portale di Azure all'indirizzo https://manage.windowsazure.com e, se necessario, immettere le proprie credenziali.
4. In **TUTTI GLI ELEMENTI** scorrere verso il basso e fare clic su **RETI**, quindi fare clic su **vnet01** e infine su **DASHBOARD**. La connessione tra **vnet01** e **vnet02** viene stabilita, come illustrato di seguito.

	![Dashboard di rete virtuale](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure11.png)

5. Anche se è possibile gestire la rete virtuale classica e la relativa connessione dal portale classico, è consigliabile usare il nuovo portale di Azure. Per aprire il nuovo portale, passare a https://portal.azure.com.
6. Nel nuovo portale fare clic su**ESPLORA TUTTO**, fare clic su **Reti virtuali (classica)**, quindi fare clic su **vnet01**. Osservare il riquadro **Connessioni VPN** illustrato di seguito.

	![Dashboard di rete virtuale](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure12.png)

### Passaggio 3: Testare la connettività

Ora che le due reti virtuali sono connesse, è opportuno testarne la connettività tramite il ping da una macchina virtuale all'altra. Si dovranno modificare le impostazioni del firewall in una delle VM per abilitare ICMP e quindi eseguire il ping di quella VM dall'altra VM. A questo scopo, seguire le istruzioni riportate di seguito.

1. Nel portale di Azure fare clic su **ESPLORA TUTTO**, fare clic su **Macchine virtuali** e quindi su **VM02**.
2. Nel pannello **VM02** fare clic su **Connetti**. Se necessario, fare clic su **Apri** nel banner di sicurezza del browser per aprire il file RDP.
3. Nella finestra di dialogo **Connessione Desktop remoto** fare clic su **Connetti**.
4. Nella finestra di dialogo **Sicurezza di Windows** immettere il nome utente e la password della VM. 
5. Nella finestra di dialogo **Connessione Desktop remoto** fare clic su **Sì**.
6. Dopo aver stabilito la connessione alla VM, da **Server Manager** fare clic su **Strumenti**, quindi su **Windows Firewall con sicurezza avanzata**.
7. Nella finestra **Windows Firewall con sicurezza avanzata** fare clic su **Regole in entrata**.
8. Nell'elenco **Regole in entrata** fare clic con il pulsante destro del mouse su **Condivisione file e stampanti (richiesta echo - ICMPv4-In)** e quindi scegliere **Abilita regola**.
9. Sulla barra delle applicazioni fare clic sul pulsante **Windows PowerShell**, quindi eseguire il comando seguente.

		ipconfig

		Connection-specific DNS Suffix  . : 4oxp4ce0c5rulb1iey4cdqhasg.gx.internal.cloudapp.net
		Link-local IPv6 Address . . . . . : fe80::8cea:a98a:5c48:4c58%12
		IPv4 Address. . . . . . . . . . . : 10.2.0.101
		Subnet Mask . . . . . . . . . . . : 255.255.255.0
		Default Gateway . . . . . . . . . : 10.2.0.101

10. Prendere nota dell'indirizzo IP per la VM. In questo scenario **10.2.0.101**. Si eseguirà il ping di questo indirizzo dall'altra VM per verificare la connettività tra le reti virtuali.
11. Nel portale di Azure fare clic su **Macchine virtuali (classica)** nel riquadro sinistro, fare clic su **VM01** e quindi su **Connetti**. Se necessario, fare clic su **Apri** nel banner di sicurezza del browser per aprire il file RDP.
12. Nella finestra di dialogo **Connessione Desktop remoto** fare clic su **Connetti**.
13. Nella finestra di dialogo **Sicurezza di Windows** immettere il nome utente e la password della VM. 
14. Nella finestra di dialogo **Connessione Desktop remoto** fare clic su **Sì**.
15. Sulla barra delle applicazioni della VM remota fare clic sul pulsante **Windows PowerShell**, quindi eseguire il comando seguente.

		ping 10.2.0.101

		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126

## Passaggi successivi

- Altre informazioni sul [Provider di risorse di rete per Gestione risorse di Azure](../resource-groups-networking.md).
- Visualizzare le linee guida generali su come [creare una connessione VPN S2S tra una rete virtuale classica e una rete virtuale di Gestione risorse di Azure](../virtual-networks-arm-asm-s2s-howto.md).

<!---HONumber=Sept15_HO4-->