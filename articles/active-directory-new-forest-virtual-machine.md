<properties  linkid="manage-services-networking-active-directory-forest" urlDisplayName="Active Directory forest" pageTitle="Install an Active Directory forest on an Azure virtual network" metaKeywords="" description="A tutorial that explains how to create a new Active Directory forest on a virtual machine (VM) on an Azure Virtual Network." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha" solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft" />

# Installazione di una nuova foresta Active Directory in una rete virtuale di Azure

In questo argomento viene illustrato come creare un nuovo ambiente Windows Server Active Directory su una macchina virtuale (VM) in una [rete virtuale di Azure][1]. In questo caso, la rete virtuale di Azure non è connessa a una rete locale.

Altri argomenti di interesse:

* Facoltativamente, è possibile [configurare una VPN da sito a sito
  utilizzando la procedura guidata del portale di gestione][2] e quindi
  installare una nuova foresta, oppure estendere una foresta locale a
  una rete virtuale di Azure. Per la procedura, vedere [Installazione di
  un controller di dominio Active Directory di replica in una rete
  virtuale di Azure][3].
* Per le linee guida concettuali sull'installazione di Servizi di
  dominio Active Directory in una rete virtuale di Azure, vedere [Linee
  guida per la distribuzione di Active Directory di Windows Server in
  macchine virtuali di Azure][4].
* Per istruzioni dettagliate sulla creazione di un ambiente di
  laboratorio di test in Azure che include Servizi di dominio Active
  Directory, vedere [Guida del laboratorio di testing: Configurazione di
  base di Windows Server 2012 R2 in Azure][5].

## Sommario

* [Differenze rispetto all'installazione locale](#differ)
* [Passaggio 1: Creare una rete virtuale di Azure](#createvnet)
* [Passaggio 2: Creare una VM per l'esecuzione del controller di
  dominio e dei ruoli del server DNS](#createvm)
* [Passaggio 3: Installare Windows Server Active Directory](#installad)
* [Passaggio 4: Impostare il server DNS per la rete virtuale di
  Azure](#dns)
* [Passaggio 5: Creare VM per i membri del dominio e unire il
  dominio](#domainmembers)

<h2><a  id="differ" ></a>Differenze rispetto all'installazione locale</h2>


Non esistono molte differenze tra l'installazione locale di un controller di dominio e l'installazione in Azure. Le differenze principali sono elencate nella tabella seguente.

Configurazione | Locale | Rete virtuale di Azure
 ------------- |------------- | ------------
**Indirizzo IP per il controller di dominio** | Assegnare un indirizzo IP statico nelle proprietà della scheda di rete | Ottenere l'indirizzo IP tramite DHCP ed eseguire il cmdlet Set-AzureStaticVNetIP per renderlo statico
**Resolver del client DNS** |Impostare l'indirizzo del server DNS preferito e alternativo nelle proprietà della scheda di rete dei membri del dominio | Impostare l'indirizzo del server DNS nelle proprietà della rete virtuale
**Archiviazione del database di Active Directory** | Facoltativamente, modificare il percorso di archiviazione predefinito da C:\\ | È necessario modificare il percorso di archiviazione predefinito da C:\\

<h2><a  id="createvnet" ></a>Passaggio 1: Creare una rete virtuale di Azure</h2>


1.  Accedere al [portale di gestione di Azure][6]. 2.  Creare una rete virtuale. Fare clic su **Networks** > **Create a virtual network**. Utilizzare i valori nella tabella seguente per completare la procedura guidata.
    
    Pagina della procedura guidata | Valori da specificare
    ------------- | ------------- 
	**Dettagli della rete virtuale** | <p>Name: immettere un nome per la rete virtuale</p><p>Region: scegliere l'area più vicina</p><p>Affinity Group: **Creare un nuovo gruppo di affinità** </p><p>Affinity Group Name: Immettere un nome per il gruppo di affinità</p>
    **DNS e VPN** | <p>Lasciare il server DNS vuoto</p><p>Non selezionare alcuna opzione VPN</p>
	**Spazi di indirizzi della rete virtuale** |<p>Subnet name: immettere un nome per la subnet</p><p>Starting IP:**10.0.0.0**</p><p>CIDR: <b>/24 (256)</b></p>

<h2><a  id="createvm" ></a>Passaggio 2: Creare una VM per l'esecuzione del controller di dominio e dei ruoli del server DNS</h2>


1.  Fare clic su **New** > **Compute** > **Virtual Machine** > **From Gallery**. 2.  Utilizzare i valori nella tabella seguente per completare la
    procedura guidata.
    
    Pagina della procedura guidata | Valori da specificare
    ------------- | ------------- 
	**Sistema operativo** | Selezionare **Windows Server 2012 R2 Datacenter**
	**Configurazione macchina virtuale** | <p>Release date: data di oggi</p><p>Machine name: specificare un valore univoco</p><p>Tier: Standard</p><p>Size: selezionare qualsiasi dimensione</p><p>User name: immettere un nome. Questo account utente sarà membro del gruppo Administrators predefinito. </p><p>Password: deve contenere almeno 8 caratteri e includere 3 dei tipi di carattere seguenti:</p><ul><li>una lettera maiuscola</li><li>una lettera minuscola</li><li>un numero</li><li >un carattere speciale</li></ul> 
	**Servizio cloud** |<p>Cloud service: **Creare un nuovo servizio cloud**</p><p>Cloud service name: Accettare il valore predefinito</p><p>Region/AffinityGroup/VirtualNetwork: Selezionare la rete virtuale creata</p><p>Virtual network subnet: Selezionare la subnet creata. </p><p>Storage account: **Utilizzare un account di archiviazione generato automaticamente**</p><p>Availability set: **Nessuno**</p><p>Endpoints: Accettare i valori predefiniti</p>
	**VM Agent** | Selezionare l'opzione per l\<strong>'installazione dell'agente VM</strong>

3.  L'indirizzo IP dinamico assegnato alla VM per impostazione predefinita è valido per la durata del servizio cloud, ma se la VM viene arrestata verrà modificato. È possibile assegnare un IP statico [eseguendo il cmdlet Set-AzureStaticVNetIP Azure PowerShell][7], per fare in modo che l'indirizzo IP resti invariato anche se la VM viene arrestata.
4.  Collegare un disco aggiuntivo alla VM per l'archiviazione del database di Active Directory, dei log e di SYSVOL.
	1.  Fare clic sulla **VM** > **Attach** > **Attach empty disk**.
	1.  Specificare una dimensione, ad esempio 10 GB e accettare tutti gli altri valori predefiniti.
7.  Accedere alla VM e formattare il disco aggiuntivo.
	1.  Fare clic su **Connect** per accedere alla VM, fare clic su **Open** per creare una sessione RDP, quindi fare di nuovo clic su **Connect**.
	1.  Modificare le credenziali inserendo il nuovo nome utente e la password specificati.
	1. In Server Manager fare clic su **Tools** > **Computer Management**.
	1. Fare clic su **Disk Management**, quindi su **OK** per inizializzare il nuovo disco.
	1. Fare clic con il pulsante destro del mouse sul nome del disco e scegliere **New Simple Volume**. Completare la procedura guidata per formattare il nuovo disco.

<h2><a  id="installad" ></a>Passaggio 3: Installare Windows Server Active Directory</h2>


[Installare Servizi di dominio Active Directory][8] utilizzando la
procedura per l'installazione locale, ovvero utilizzando l'interfaccia utente, un file di risposte o Windows PowerShell. Per installare una nuova foresta è necessario fornire le credenziali di amministratore. Per specificare il percorso del database di Active Directory, dei log e di SYSVOL, modificare il percorso di archiviazione predefinito dall'unità del sistema operativo al disco dati aggiuntivo precedentemente collegato alla VM.

Al termine dell'installazione del controller di dominio, connettersi nuovamente alla VM e accedere al controller di dominio. Ricordare di specificare le credenziali del dominio.


<h2><a  id="dns" ></a>Passaggio 4: Impostare il server DNS per la rete virtuale di Azure</h2>


1.  Fare clic su **Virtual Networks**, fare doppio clic sulla rete virtuale creata, quindi fare clic su **Configure**.
2.  In **DNS servers** digitare il nome e il DIP del controller di dominio e fare clic su **Save**.
3.  Selezionare la VM e fare clic su **Restart** per fare in modo che la VM configuri le impostazioni del resolver DNS con l'indirizzo IP del nuovo server DNS.

<h2><a  id="domainmembers" ></a>Passaggio 5: Creare VM per i membri del dominio e unire il dominio</h2>


Creare ulteriori VM per il provisioning ai membri del dominio. È possibile utilizzare l'interfaccia utente o Azure PowerShell. Se si utilizza l'interfaccia utente, seguire la stessa procedura utilizzata per creare la prima VM. Unire quindi le VM al dominio come si farebbe per un'installazione locale. Se si utilizza Azure PowerShell, è possibile eseguire il provisioning delle VM e unirle al dominio al primo avvio, come nell'esempio seguente. '

	cls

	Set-AzureSubscription -SubscriptionName "Free Trial" -currentstorageaccountname 'constorageaccount'
	Select-AzureSubscription -SubscriptionName "Free Trial"

	#Deploy a new VM and join it to the domain
	#-------------------------------------------
	#Specify my DC's DNS IP (10.0.0.4)
	$myDNS = New-AzureDNS -Name 'DC-1' -IPAddress '10.0.0.4'
	
	# OS Image to Use
	$image = 'a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201310.01-en.us-127GB.vhd'
	$service = 'ConApp1'
	$AG = 'YourAffinityGroup'
	$vnet = 'YourVirtualNetwork'
	$pwd = 'P@ssw0rd'
	$size = 'Small'

	#VM Configuration
	$vmname = 'ConApp1'
	$MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

	New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

Se si esegue nuovamente lo script sarà necessario fornire un valore univoco per $service. È possibile eseguire Test-AzureName -Service *nome servizio*, il cui risultato indica se il nome è già in uso.

## Vedere anche

* [Linee guida per la distribuzione di Active Directory di Windows
  Server in macchine virtuali di Azure][4]

* [Creazione di una rete virtuale in Azure][9]

* [Creazione di una rete virtuale per la connettività cross-premise da
  sito a sito][10]

* [Installazione di un controller di dominio Active Directory di replica
  in una rete virtuale di Azure.][3]

* [Rete virtuale di Azure][1]

* [Come installare e configurare Azure PowerShell][11]

* [Azure PowerShell][12]

* [Cmdlet di gestione di Azure][13]

* [Impostazione dell'indirizzo IP statico per una VM di Azure][14]

* [Installazione di una nuova foresta Active Directory][8]

* [Introduzione alla virtualizzazione di Servizi di dominio Active
  Directory (livello 100)][15]

* [Guida del laboratorio di testing: Configurazione di base di Windows
  Server 2012 R2 in Azure][5]



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/dn133795.aspx
[3]: http://www.windowsazure.com/en-us/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156090.aspx
[5]: http://www.microsoft.com/en-us/download/details.aspx?id=41684
[6]: https://manage.windowsazure.com
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/dn630228.aspx
[8]: http://technet.microsoft.com/library/jj574166.aspx
[9]: http://www.windowsazure.com/en-us/manage/services/networking/create-a-virtual-network/
[10]: http://www.windowsazure.com/en-us/documentation/articles/virtual-networks-create-site-to-site-cross-premises-connectivity/
[11]: http://www.windowsazure.com/en-us/documentation/articles/install-configure-powershell/
[12]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156055.aspx
[13]: http://msdn.microsoft.com/en-us/library/windowsazure/jj152841
[14]: http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address
[15]: http://technet.microsoft.com/en-us/library/hh831734.aspx