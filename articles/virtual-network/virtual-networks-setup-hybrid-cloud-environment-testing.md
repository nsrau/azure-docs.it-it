<properties 
	pageTitle="Ambienti di test cloud ibridi | Microsoft Azure" 
	description="Informazioni su come creare un ambiente cloud ibrido per professionisti IT o per il test di sviluppo, inclusa una rete locale semplificata." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/28/2016" 
	ms.author="josephd"/>

# Configurazione di un ambiente cloud ibrido per l'esecuzione di test

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.
 

Questo argomento illustra la creazione di un ambiente cloud ibrido in Microsoft Azure per attività di test. Di seguito è riportata la configurazione risultante.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_5.png)

Viene simulato un ambiente di produzione reale ibrido dal percorso in Internet. È costituita da:

-  Una rete locale semplificata (la subnet Corpnet).
-  Una rete virtuale cross-premise ospitata in Azure (TestVNET).
-  Una connessione VPN da sito a sito.
-  Un controller di dominio secondario nella rete virtuale TestVNET.

Questa configurazione rappresenta una base e un punto di partenza comune da cui è possibile:

-  Sviluppare e testare applicazioni in un ambiente cloud ibrido.
-  Creare configurazioni di test di computer, alcune nella subnet Corpnet e altre nella rete virtuale TestVNET, per carichi di lavoro IT basati sul cloud.

L'impostazione di un ambiente di test del cloud ibrido comporta cinque fasi principali:

1.	Configurare i computer nella subnet Corpnet.
2.	Configurare RRAS1.
3.	Creare la rete virtuale di Azure cross-premise.
4.	Creare la connessione VPN da sito a sito.
5.	Configurare DC2. 

Se non si ha ancora una sottoscrizione di Azure, è possibile iscriversi per ottenere una [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). Se si dispone di un abbonamento MSDN, vedere [Benefici di Azure per gli abbonati MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Le macchine virtuali e i gateway di rete virtuale in Azure generano addebiti monetari in caso di esecuzione. Il costo viene addebitato sulla base della versione di valutazione gratuita, dell'abbonamento MSDN o della sottoscrizione a pagamento. Per ridurre i costi di esecuzione dell'ambiente di test quando non viene utilizzato, vedere [Ridurre al minimo i costi correnti dell'ambiente](#costs) in questo argomento per ulteriori informazioni.

Questa configurazione richiede una subnet di test di fino a quattro computer connessi direttamente a Internet utilizzando un indirizzo IP pubblico. Se non si dispone di queste risorse, è anche possibile [impostare un ambiente di cloud ibrido simulato per i test](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md). L'ambiente di test cloud ibrida simulato richiede una sottoscrizione di Azure.

## Fase 1: Configurare i computer nella subnet Corpnet.

Utilizzare le istruzioni nella sezione "Passaggi per configurare la Subnet Corpnet" del [Guida dell'ambiente di Test: Base configurazione per Windows Server 2012 R2](http://www.microsoft.com/download/details.aspx?id=39638) per configurare i computer DC1, APP1 e CLIENT1 in una subnet denominata Corpnet. **Questa subnet deve essere isolata dalla rete dell'organizzazione perché verrà connessa direttamente a Internet tramite il computer RRAS1.**

Accedere a DC1 con le credenziali CORP\\User1. Per configurare il dominio CORP in modo che i computer e gli utenti usino il controller di dominio locale per l'autenticazione, eseguire questi comandi da un prompt dei comandi di Windows PowerShell a livello di amministratore.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet “Name "10.0.0.0/8" “Site "TestLab"
	New-ADReplicationSubnet “Name "192.168.0.0/16" “Site "TestVNET

Questa è la configurazione corrente.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_1.png)
 
## Fase 2: Configurare RRAS1.

RRAS1 fornisce servizi di indirizzamento del traffico e servizi di dispositivi VPN tra i computer della subnet Corpnet e la rete virtuale TestVNET. RRAS1 deve disporre di due schede di rete installate.

Innanzitutto, installare il sistema operativo in RRAS1.

1.	Avviare l'installazione di Windows Server 2012 R2.
2.	Seguire le istruzioni per completare l'installazione, specificando una password complessa per l'account amministratore locale. Accedere usando l'account amministratore locale.
3.	Connettere RRAS1 a una rete con accesso a Internet ed eseguire Windows Update per installare gli aggiornamenti più recenti per Windows Server 2012 R2.
4.	Connettere una scheda di rete alla subnet Corpnet e l'altra direttamente a Internet. RRAS1 può trovarsi dietro un firewall Internet ma non deve trovarsi dietro un dispositivo NAT (Network Address Translator).

Configurare quindi le proprietà TCP/IP di RRAS1. Sarà necessaria una configurazione di indirizzo IP pubblico che includa indirizzo, subnet mask (o lunghezza del prefisso) e il gateway predefinito con i server DNS del provider di servizi Internet (ISP).

Usare questi comandi in un prompt dei comandi di Windows PowerShell di livello amministratore su RRAS1. Per usare questi comandi, inserire i valori delle variabili e rimuovere i caratteri < and >. È possibile ottenere i nomi delle schede di rete correnti dalla visualizzazione del comando **Get-NetAdapter**.

	$corpnetAdapterName="<Name of the adapter attached to the Corpnet subnet>"
	$internetAdapterName="<Name of the adapter attached to the Internet>"
	[Ipaddress]$publicIP="<Your public IP address>"
	$publicIPpreflength=<Prefix length of your public IP address>
	[IPAddress]$publicDG="<Your ISP default gateway>"
	[IPAddress]$publicDNS="<Your ISP DNS server(s)>"
	Rename-NetAdapter -Name $corpnetAdapterName -NewName Corpnet
	Rename-NetAdapter -Name $internetAdapterName -NewName Internet
	New-NetIPAddress -InterfaceAlias "Internet" -IPAddress $publicIP -PrefixLength $publicIPpreflength “DefaultGateway $publicDG
	Set-DnsClientServerAddress -InterfaceAlias Internet -ServerAddresses $publicDNS
	New-NetIPAddress -InterfaceAlias "Corpnet" -IPAddress 10.0.0.2 -AddressFamily IPv4 -PrefixLength 24
	Set-DnsClientServerAddress -InterfaceAlias "Corpnet" -ServerAddresses 10.0.0.1
	Set-DnsClient -InterfaceAlias "Corpnet" -ConnectionSpecificSuffix corp.contoso.com
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Input" -Protocol ICMPv4
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Output" -Protocol ICMPv4 -Direction Outbound
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_msclient
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_server
	ping dc1.corp.contoso.com

Per l'ultimo comando, verificare che siano presenti quattro risposte dall'indirizzo IP 10.0.0.1.

Questa è la configurazione corrente.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_2.png)

## Fase 3: Creare la rete virtuale di Azure cross-premise.

In primo luogo, accedere al [Portale di gestione di Azure](https://manage.windowsazure.com/) con le credenziali della sottoscrizione di Azure e creare una rete virtuale denominata TestVNET.

1.	Nella barra delle applicazioni del portale di gestione di Azure fare clic su **Nuovo > Servizi di rete > Rete virtuale > Creazione personalizzata**.
2.	Nella pagina Dettagli della rete virtuale digitare **TestVNET** in **Nome**.
3.	In **Posizione**, selezionare il data center appropriato per la propria ubicazione.
4.	Fare clic sulla freccia Avanti.
5.	Nella pagina Server DNS e connettività VPN, in **Server DN**S, digitare **DC1** in **Selezionare o immettere nome**, digitare **10.0.0.1** in **Indirizzo IP**, quindi selezionare **Configura una VPN Site-to-Site**.
6.	In **Rete locale** selezionare **Specificare una nuova rete locale**. 
7.	Fare clic sulla freccia Avanti.
8.	Nella pagina Connettività da sito a sito:
	- In **Nome** digitare **Corpnet**. 
	- In **Indirizzo IP dispositivo VPN** digitare l'indirizzo IP pubblico assegnato all'interfaccia Internet di RRAS1.
	- In **Spazio degli indirizzi**, nella colonna **IP iniziale**, digitare **10.0.0.0**. In **CIDR (conteggio indirizzi)** selezionare **/8**, quindi fare clic su **Aggiungi spazio di indirizzi**.
9.	Fare clic sulla freccia Avanti.
10.	Nella pagina Spazi di indirizzi della rete virtuale:
	- In **Spazio degli indirizzi** selezionare **192.168.0.0** in **IP iniziale**.
	- In **Subnet** fare clic su **Subnet-1** e sostituire il nome con **TestSubnet**. 
	- Nella colonna **CIDR (conteggio indirizzi)** per TestSubnet fare clic su **/24 (256)**.
	- Fare clic su **Aggiungi subnet gateway**.
11.	Fare clic sull'icona Completa. Attendere il completamento della creazione della rete virtuale prima di continuare.

Usare quindi le istruzioni disponibili in [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per installare Azure PowerShell nel computer locale.

Creare quindi un nuovo servizio cloud per la rete virtuale TestVNET. È necessario selezionare un nome univoco. È ad esempio possibile specificare il nome TestVNET-*UniqueSequence*, in cui *UniqueSequence* è un'abbreviazione dell'organizzazione. Se ad esempio il nome dell'organizzazione è Tailspin Toys, è possibile assegnare il nome TestVNET-Tailspin al servizio cloud.

Per verificare l'univocità del nome, è possibile usare il seguente comando di Azure PowerShell nel computer locale.

	Test-AzureName -Service <Proposed cloud service name>

Se questo comando restituisce "False", il nome proposto è univoco. Creare il servizio cloud con questo comando.

	New-AzureService -Service <Unique cloud service name> -Location "<Same location name as your virtual network>"

Creare quindi un nuovo account di archiviazione per la rete virtuale TestVNET. È necessario selezionare un nome univoco. È possibile verificare l'univocità del nome dell'account di archiviazione con questo comando.

	Test-AzureName -Storage <Proposed storage account name>

Se questo comando restituisce "False", il nome proposto è univoco. Creare e impostare l'account di archiviazione con i seguenti comandi.

	New-AzureStorageAccount -StorageAccountName <Unique storage account name> -Location "<Same location name as your virtual network>"
	Set-AzureStorageAccount -StorageAccountName <Unique storage account name>

Questa è la configurazione corrente.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_3.png)

 
## Fase 4: Creare la connessione VPN da sito a sito

Creare innanzitutto un gateway di rete virtuale.

1.	Nel portale di gestione di Azure nel computer locale fare clic su **Reti** nel riquadro a sinistra, quindi verificare che la colonna **Stato** per TestVNET sia impostata su **Creata**.
2.	Fare clic su **TestVNET**. Nella pagina Dashboard dovrebbe essere visualizzato lo stato **Gateway non creato**.
3.	Nella barra delle applicazioni fare clic su **Crea gateway** e quindi su **Routing dinamico**. Fare clic su **Sì** quando richiesto. Attendere il completamento del gateway e l'impostazione del relativo stato su **Connessione**. L'operazione può richiedere alcuni minuti.
4.	Annotare l'**Indirizzo IP del gateway** dalla pagina Dashboard. Questo è l'indirizzo IP pubblico del gateway VPN di Azure per la rete virtuale TestVNET. Questo indirizzo IP è necessario per configurare RRAS1.
5.	Nella barra delle applicazioni fare clic su **Gestisci chiave**, quindi fare clic sull'icona Copia accanto alla chiave per copiarla negli Appunti. Incollare la chiave in un documento e salvare il documento. Questo valore della chiave è necessario per configurare RRAS1. 

Configurare quindi RRAS1 con il servizio Routing e Accesso remoto affinché agisca come dispositivo VPN per la subnet Corpnet. Accedere a RRAS1 come amministratore locale ed eseguire i seguenti comandi al prompt dei comandi di Windows PowerShell.

	Import-Module ServerManager
	Install-WindowsFeature RemoteAccess -IncludeManagementTools
	Add-WindowsFeature -name Routing -IncludeManagementTools

Configurare quindi RRAS1 per ricevere la connessione VPN da sito a sito dal gateway VPN di Azure. Riavviare RRAS1, accedere come amministratore locale e attivare i seguenti comandi al prompt dei comandi di Windows PowerShell. È necessario fornire l'indirizzo IP del gateway VPN di Azure e il valore della chiave.

	$PresharedKey="<Key value>"
	Import-Module RemoteAccess
	Install-RemoteAccess -VpnType VpnS2S
	Add-VpnS2SInterface -Protocol IKEv2 -AuthenticationMethod PSKOnly -Persistent -NumberOfTries 3 -ResponderAuthenticationMethod PSKOnly -Name S2StoTestVNET -Destination "<IP address of the Azure VPN gateway>" -IPv4Subnet @("192.168.0.0/24:100") -SharedSecret $PresharedKey
	Set-VpnServerIPsecConfiguration -EncryptionType MaximumEncryption
	Set-VpnServerIPsecConfiguration -SADataSizeForRenegotiationKilobytes 33553408
	New-ItemProperty -Path HKLM:\System\CurrentControlSet\Services\RemoteAccess\Parameters\IKEV2 -Name SkipConfigPayload -PropertyType DWord -Value 1
	Restart-Service RemoteAccess

Passare quindi al portale di gestione di Azure nel computer locale e attendere che per la rete virtuale TestVNET venga visualizzato lo stato **Connessa**.

Configurare quindi RRAS1 per supportare il traffico tradotto in percorsi Internet. In RRAS1:

1.	Nella schermata iniziale digitare**rras**, quindi fare clic su **Routing e Accesso remoto**. 
2.	Nell'albero della console, aprire il nome del server, quindi fare clic su **IPv4**.
3.	Fare doppio clic su **Generale**, quindi fare clic su **Nuovo protocollo di Routing**.
4.	Fare clic su **NAT**, quindi su **OK**.
5.	Nell'albero della console fare clic con il pulsante destro del mouse su **NAT**, fare clic su **Nuova interfaccia**, quindi su **Corpnet** e infine due volte su **OK**.
6.	Fare clic con il pulsante destro del mouse su **NAT**, scegliere **Nuova interfaccia**, fare clic su **Internet**, quindi su **OK**.
7.	Nella scheda **NAT** fare clic su **Interfaccia pubblica connessa a Internet**, selezionare **Abilita NAT su questa interfaccia**, quindi fare clic su **OK**.


Configurare DC1, APP1 e CLIENT1 per usare RRAS1 come gateway predefinito.
 
In DC1 eseguire questi comandi in un prompt dei comandi di Windows PowerShell a livello di amministratore.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2
	Set-DhcpServerv4OptionValue -Router 10.0.0.2

Se il nome dell'interfaccia non è Ethernet, usare il comando **Get-NetAdapter** per determinare il nome dell'interfaccia.

In APP1 eseguire questo comando in un prompt dei comandi di Windows PowerShell a livello di amministratore.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2

In CLIENT1 eseguire questo comando in un prompt dei comandi di Windows PowerShell a livello di amministratore.

	ipconfig /renew

Questa è la configurazione corrente.
 

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_4.png)


## Fase 5: configurare DC2.

Innanzitutto, creare una macchina virtuale di Azure per DC2 con questi comandi al prompt dei comandi di Azure PowerShell nel computer locale.

	$ServiceName="<Your cloud service name from Phase 3>"
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC2 -InstanceSize Medium -ImageName $image
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password 
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdminName -Password $LocalAdminPW	
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.0.4
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel ADFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET


Accedere quindi alla nuova macchina virtuale DC2.

1.	Nel riquadro sinistro del portale di gestione di Azure fare clic su **Macchine virtuali**, quindi su **In esecuzione** nella colonna **Stato** per DC2.
2.	Nella barra delle applicazioni fare clic su **Connetti**. 
3.	Quando viene richiesto di aprire DC2.rdp, fare clic su **Apri**.
4.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto, fare clic su **Connetti**.
5.	Alla richiesta di credenziali, usare le seguenti:
	- Nome: **DC2\**[Nome dell'account amministratore locale]
	- Password: [Nome dell'account amministratore locale]
6.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto che si riferisce ai certificati, fare clic su **Sì**.

Configurare quindi una regola di Windows Firewall per permettere il traffico per il test della connettività di base. Da un prompt dei comandi di Windows PowerShell a livello di amministratore in DC2 eseguire:

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Il comando ping dovrebbe restituire quattro risposte dall'indirizzo IP 10.0.0.1. Questo è un test del traffico per la connessione VPN da sito a sito.

Quindi, aggiungere un altro disco dati come nuovo volume con la lettera di unità F:.

1.	Nel riquadro sinistro di Server Manager fare clic su **Servizi file e archiviazione**, quindi scegliere **Dischi**.
2.	Nel riquadro del contenuto nel gruppo **Dischi** fare clic su **disco 2** (con la **Partizione** impostata su **Sconosciuto**).
3.	Fare clic su **Attività**, quindi su **Nuovo volume**.
4.	Nella pagina Operazioni preliminari della creazione guidata nuovo volume fare clic su **Avanti**.
5.	Nella pagina Selezionare il server e il disco fare clic su **Disco 2**, quindi fare clic su **Avanti**. Quando richiesto, fare clic su **OK**.
6.	Nella pagina Specifica dimensioni del volume fare clic su **Avanti**.
7.	Nella pagina Assegnare a una lettera di unità o cartella fare clic su **Avanti**.
8.	Nella pagina Selezionare le impostazioni del file system fare clic su **Avanti**.
9.	Nella pagina Conferma selezioni, fare clic su **Crea**.
10.	Al termine, fare clic su **Chiudi**.

Configurare quindi DC2 come controller di dominio di replica per il dominio corp.contoso.com. Eseguire questi comandi dal prompt dei comandi di Windows PowerShell in DC2:

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Si noti che viene richiesto di specificare sia la password per CORP\\User1 che la password per la Modalità ripristino servizi directory (DSRM, Directory Services Restore Mode) e quindi di riavviare DC2.

Quando la rete virtuale TestVNET è stata associata a un server DNS specifico (DC2), sarà necessario configurare la rete virtuale TestVNET per l'uso di tale server DNS.

1.	Nel riquadro sinistro del portale di gestione di Azure fare clic su **Reti** e quindi su **TestVNET**.
2.	Fare clic su **Configure**.
3.	In **Server DNS** rimuovere la voce **10.0.0.1**.
4.	In **Server DNS** aggiungere una voce con **DC2** come nome e **192.168.0.4** come indirizzo IP. 
5.	Nella barra dei comandi in basso fare clic su **Salva**.
6.	Nel riquadro sinistro del portale di gestione di Azure fare clic su **Macchine virtuali**, quindi sulla colonna **Stato** per DC2.
7.	Nella barra dei comandi fare clic su **Riavvia**. Attendere il riavvio di DC2.


Questa è la configurazione corrente.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_5.png)

 
L'ambiente cloud ibrido è ora pronto per il testing.

## Ridurre al minimo i costi di esercizio dell'ambiente

Per ridurre al minimo i costi di esecuzione delle macchine virtuali in questo ambiente, eseguire i test e le dimostrazioni necessari nel modo più rapido possibile, quindi eliminare o arrestare le macchine virtuali quando non sono in uso. È ad esempio possibile usare Automazione di Azure e un runbook per arrestare automaticamente le macchine virtuali nella rete virtuale Test\_VNET al termine di ogni giornata lavorativa.

Il gateway VPN di Azure viene implementato come set di due macchine virtuali di Azure che comportano un costo monetario continuativo. Per informazioni dettagliate, vedere [Rete virtuale - Prezzi](https://azure.microsoft.com/pricing/details/virtual-network/). Per ridurre al minimo i costi del gateway VPN, creare l'ambiente di test ed eseguire i test e le dimostrazioni necessari il più rapidamente possibile oppure eliminare il gateway seguendo questa procedura.

1.	Dal portale di gestione di Azure del computer locale fare clic su **Reti** nel riquadro sinistro, quindi su **TestVNET** e infine su **Dashboard**.
2.	Nella barra delle applicazioni fare clic su **Elimina gateway**. Fare clic su **Sì** quando richiesto. Attendere l'eliminazione del gateway e l'impostazione del relativo stato su **Gateway non creato**.

Se si elimina il gateway e si vuole ripristinare questo ambiente di test, sarà necessario creare prima di tutto un nuovo gateway.

1.	Nel portale di gestione di Azure del computer locale fare clic su **Reti** nel riquadro sinistro, quindi su **TestVNET**. Nella pagina Dashboard dovrebbe essere visualizzato lo stato **Gateway non creato**.
2.	Nella barra delle applicazioni fare clic su **Crea gateway** e quindi su **Routing dinamico**. Fare clic su **Sì** quando richiesto. Attendere il completamento del gateway e l'impostazione del relativo stato su **Connessione**. L'operazione può richiedere alcuni minuti.
3.	Annotare l'**Indirizzo IP del gateway** dalla pagina Dashboard. Questo è il nuovo indirizzo IP pubblico del gateway VPN di Azure per la rete virtuale TestVNET. Questo indirizzo IP è necessario per riconfigurare RRAS1.
4.	Nella barra delle applicazioni fare clic su **Gestisci chiave**, quindi fare clic sull'icona Copia accanto alla chiave per copiarla negli Appunti. Incollare il valore di chiave in un documento e salvare il documento. Questo valore della chiave è necessario per riconfigurare RRAS1. 

Accedere ora a RRAS1 come amministratore locale ed eseguire questi comandi in un prompt dei comandi di Windows PowerShell a livello di amministratore per riconfigurare RRAS1 con il nuovo indirizzo IP pubblico e la chiave già condivisa.

	$PresharedKey="<Key value>"
	Set-VpnS2SInterface -Name S2StoTestVNET -Destination "<IP address of the Azure VPN gateway>" -SharedSecret $PresharedKey

Passare quindi al portale di gestione di Azure nel computer locale e attendere che per la rete virtuale TestVNET venga visualizzato lo stato Connessa.
 
## Passaggi successivi

- Impostare una [farm Intranet di SharePoint](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), un'[applicazione line-of-business basata sul Web](virtual-networks-setup-lobapp-hybrid-cloud-testing.md) o un [server di sincronizzazione della directory di Office 365 (DirSync)](virtual-networks-setup-dirsync-hybrid-cloud-testing.md) in questo ambiente.

<!---HONumber=AcomDC_0413_2016-->