<properties 
	pageTitle="Installazione di una foresta Active Directory in una rete virtuale di Azure" 
	description="Un'esercitazione che illustra come creare una nuova foresta di Active Directory in una macchina virtuale (VM) in una rete virtuale di Azure." 
	services="active-directory, virtual-network" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/12/2014" 
	ms.author="Justinha"/>




#Installazione di una nuova foresta Active Directory in una rete virtuale di Azure

Questo argomento descrive come creare un nuovo ambiente Windows Server Active Directory su una macchina virtuale (VM) in una [rete virtuale di Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx). In questo caso, la rete virtuale di Azure non è connessa a una rete locale. 

Altri argomenti di interesse:

- Facoltativamente, è possibile [configurare una VPN da sito a sito usando la procedura guidata del portale di gestione](http://msdn.microsoft.com/library/windowsazure/dn133795.aspx) e quindi installare una nuova foresta, oppure estendere una foresta locale a una rete virtuale di Azure. Per la procedura, vedere [Installazione di un controller di dominio Active Directory di replica nelle reti virtuali di Azure](http://azure.microsoft.com/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/).
-  Per le linee guida concettuali sull'installazione di Servizi di dominio Active Directory in una rete virtuale di Azure, vedere [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).
-  Per istruzioni dettagliate sulla creazione di un ambiente di laboratorio di test in Azure che include Servizi di dominio Active Directory, vedere [Guida del laboratorio di testing: Configurazione di base di Windows Server 2012 R2 in Azure](http://www.microsoft.com/it-it/download/details.aspx?id=41684).



##Sommario##

* [Differenze rispetto all'installazione locale](#differ)
* [Passaggio 1. Creare una rete virtuale di Azure](#createvnet)
* [Passaggio 2: Creare una VM per l'esecuzione del controller di dominio e dei ruoli del server DNS](#createvm)
* [Passaggio 3: Installare Windows Server Active Directory](#installad)
* [Passaggio 4: Impostare il server DNS per la rete virtuale di Azure](#dns)
* [Passaggio 5: Creare VM per i membri del dominio e unire il dominio](#domainmembers)


<h2><a id="differ"></a>Differenze rispetto all'installazione locale</h2>
Non esistono molte differenze tra l'installazione locale di un controller di dominio e l'installazione in Azure. Le differenze principali sono elencate nella tabella seguente. 

Configurazione  | Locale  | Rete virtuale di Azure	
------------- | -------------  | ------------
**Indirizzo IP per il controller di dominio**  | Assegnare un indirizzo IP statico nelle proprietà della scheda di rete   | Eseguire il cmdlet Set-AzureStaticVNetIP per assegnare un indirizzo IP statico
**Resolver del client DNS**  | Impostare l'indirizzo del server DNS preferito e alternativo nelle proprietà della scheda di rete dei membri del dominio   | Impostare l'indirizzo del server DNS nelle proprietà della rete virtuale
**Archiviazione del database di Active Directory**  | Facoltativamente, modificare il percorso di archiviazione predefinito da C:\  | È necessario modificare il percorso di archiviazione predefinito da C:\



<h2><a id="createvnet"></a>Passaggio 1: Creare una rete virtuale di Azure</h2>
1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Creare una rete virtuale. Fare clic su <b>Reti</b> > <b>Crea rete virtuale</b>. Usare i valori nella tabella seguente per completare la procedura guidata. 

	Pagina della procedura guidata  | Valori da specificare
	------------- | -------------
	**Dettagli della rete virtuale**  | <p>Name: immettere un nome per la rete virtuale</p><p>Region: scegliere l'area più vicina</p>
	**DNS e VPN**  | <p>Lasciare il campo relativo al server DNS vuoto</p><p>Non selezionare alcuna opzione VPN</p>
	**Spazi di indirizzi della rete virtuale**  | <p>Nome subnet: immettere un nome per la subnet</p><p>Starting IP: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



<h2><a id="createvm"></a>Passaggio 2: Creare una VM per l'esecuzione del controller di dominio e dei ruoli del server DNS</h2>
 
1. Fare clic su <b>Nuovo</b> > <b>Calcolo</b> > <b>Macchina virtuale</b> > <b>Da raccolta</b>. 
2. Usare i valori nella tabella seguente per completare la procedura guidata.

	Pagina della procedura guidata  | Valori da specificare
	------------- | -------------
	**Sistema operativo**  | Selezionare **Windows Server 2012 R2 Datacenter**
	**Configurazione macchina virtuale**  | <p>Data di rilascio: data odierna</p><p>Nome computer: specificare un valore univoco</p><p>Livello: Standard</p><p>Dimensione: selezionare una dimensione</p><p>Nome utente: immettere un nome. Questo account utente sarà membro del gruppo Administrators predefinito. </p><p>Password: deve contenere almeno 8 caratteri e includere 3 dei seguenti tipi di caratteri:</p><ul><li>una lettera maiuscola</li><li>una lettera minuscola</li><li>un numero</li><li>un carattere speciale</li></ul>
	**Servizio cloud**  | <p>Servizio cloud: <b>Crea un nuovo servizio cloud</b></p><p>Nome servizio cloud: accettare il valore predefinito</p><p>Regione/Gruppo di affinità/Rete virtuale: selezionare la rete virtuale creata</p><p>Subnet della rete virtuale: selezionare la subnet creata. </p><p>Account di archiviazione: <b>Usa un account di archiviazione generato automaticamente</b></p><p>Set di disponibilità: <b>None</b></p><p>Endpoints: accettare i valori predefiniti</p>
	**Agente di macchine virtuali**  | Selezionare **Installa l'agente di macchine virtuali**

1. L'indirizzo IP dinamico assegnato alla VM per impostazione predefinita è valido per la durata del servizio cloud, ma se la VM viene arrestata verrà modificato. Assegnare un indirizzo IP statico [eseguendo il cmdlet Set-AzureStaticVNetIP Azure PowerShell](http://msdn.microsoft.com/library/windowsazure/dn630228.aspx), in modo che l'indirizzo IP resti invariato anche se la VM viene arrestata. 
2. Collegare un disco aggiuntivo alla VM per l'archiviazione del database di Active Directory, dei log e di SYSVOL. 
  3. Fare clic su <b>VM</b> > <b>Allega</b> > <b>Attach Empty Disk</b>. 
  4. Specificare una dimensione, ad esempio 10 GB e accettare tutti gli altri valori predefiniti.
3. Accedere alla VM e formattare il disco aggiuntivo. 
  4. Fare clic su <b>Connetti</b> per accedere alla VM, fare clic su <b>Apri</b> per creare una sessione RDP e quindi fare di nuovo clic su <b>Connetti</b> .
  4. Modificare le credenziali inserendo il nuovo nome utente e la password specificati.
  5. In Server Manager fare clic su <b>Strumenti</b> > <b>Gestione computer</b>. 
  6. Fare clic su <b>Gestione disco</b> e quindi su <b>OK</b> per inizializzare il nuovo disco. 
  6. Fare clic con il pulsante destro del mouse sul nome del disco e scegliere <b>New Simple Volume</b>. Completare la procedura guidata per formattare il nuovo disco. 

<h2><a id="installad"></a>Passaggio 3: Installare Windows Server Active Directory</h2>
[Installare Servizi di dominio Active Directory](http://technet.microsoft.com/library/jj574166.aspx) usando la procedura per l'installazione locale, ovvero usando l'interfaccia utente, un file di risposte o Windows PowerShell. Per installare una nuova foresta è necessario fornire le credenziali di amministratore. Per specificare il percorso del database di Active Directory, dei log e di SYSVOL, modificare il percorso di archiviazione predefinito dall'unità del sistema operativo al disco dati aggiuntivo precedentemente collegato alla VM. 
<p>Al termine dell'installazione del controller di dominio, connettersi nuovamente alla VM e accedere al controller di dominio. Ricordare di specificare le credenziali del dominio.</p>

<h2><a id="dns"></a>Passaggio 4: Impostare il server DNS per la rete virtuale di Azure</h2>
1. Fare clic su <b>Reti virtuali</b>, fare doppio clic sulla rete virtuale creata e quindi fare clic su <b>Configura</b>. 
2. In <b>Server DNS</b>digitare il nome e il DIP del controller di dominio e fare clic su <b>Save</B>. 
3. Selezionare la VM e fare clic su <b>Riavvio</b> per fare in modo che la VM configuri le impostazioni del resolver DNS con l'indirizzo IP del nuovo server DNS. 


<h2><a id="domainmembers"></a>Passaggio 5: Creare VM per i membri del dominio e unire il dominio</h2>
<p>Creare altre macchine virtuali per il provisioning dei computer membri del dominio. È possibile usare l'interfaccia utente o Azure PowerShell. Se si usa l'interfaccia utente, seguire la stessa procedura usata per creare la prima VM. Unire quindi le VM al dominio come si farebbe per un'installazione locale. Se si usa Azure PowerShell, è possibile eseguire il provisioning delle macchine virtuali e aggiungerle al dominio al primo avvio. </p><p>In questo esempio verrà creata una macchina virtuale aggiunta al dominio e denominata DC2 che esegue Windows Server 2012 R2 Datacenter. Dopo il provisioning, eseguire l'accesso a DC2 come amministratore di dominio e alzare di livello la macchina in modo che diventi un controller di dominio di replica. </p><p>È possibile eseguire Get-AzureVMImage per ottenere nomi di immagine. Ad esempio, per visualizzare un elenco di immagini per Windows Server 2012 R2, eseguire Get-AzureVMImage | where-object {$_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"}.</p>
	'

	cls

	Set-AzureSubscription -SubscriptionName "Free Trial" -currentstorageaccountname 'constorageaccount'
	Select-AzureSubscription -SubscriptionName "Free Trial"

	#Deploy a new VM and join it to the domain
	#-------------------------------------------
	#Specify my DC's DNS IP (10.0.0.4)
	$myDNS = New-AzureDNS -Name 'DC1' -IPAddress '10.0.0.4'
	
	# OS Image to Use
	$image = 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd'
	$service = 'DC2'
	$vnet = 'YourVirtualNetwork'
	$pwd = 'P@ssw0rd'
	$size = 'Small'

	#VM Configuration
	$vmname = 'DC2'
	$MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

	New-AzureVM -ServiceName $service -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

Se si esegue nuovamente lo script sarà necessario fornire un valore univoco per $service. È possibile eseguire Test-AzureName -Service <i>nome servizio</i>, il cui risultato indica se il nome è già in uso. 	

## Vedere anche

-  [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Configurare una rete virtuale solo cloud nel portale di gestione](http://msdn.microsoft.com/library/dn631643.aspx)

-  [Configurare una VPN da sito a sito nel portale di gestione](http://msdn.microsoft.com/library/dn133795.aspx)

-  [Installazione di un controller di dominio Active Directory di replica nelle reti virtuali di Azure](http://azure.microsoft.com/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/)

-  [Microsoft Azure IT Pro IaaS: (01) Dati fondamentali delle macchine virtuali](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Microsoft Azure IT Pro IaaS: (05) Creazione di reti virtuali e connettività cross-premise](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Panoramica di Rete virtuale](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

-  [Come installare e configurare Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)

-  [Azure PowerShell](http://msdn.microsoft.com/library/windowsazure/jj156055.aspx)

-  [Cmdlet di gestione di Azure](http://msdn.microsoft.com/library/windowsazure/jj152841)

-  [Impostazione dell'indirizzo IP statico per una VM di Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)

-  [Come assegnare un IP statico a una macchina virtuale di Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)

-  [Installazione di una nuova foresta Active Directory](http://technet.microsoft.com/library/jj574166.aspx)

-  [Introduzione alla virtualizzazione di Servizi di dominio Active Directory (livello 100)](http://technet.microsoft.com/library/hh831734.aspx)

-  [Guida del laboratorio di testing: Configurazione di base di Windows Server 2012 R2 in Azure](http://www.microsoft.com/it-it/download/details.aspx?id=41684)


<!--HONumber=35.2-->

<!--HONumber=46--> 
