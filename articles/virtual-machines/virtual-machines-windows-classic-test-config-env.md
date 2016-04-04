<properties
	pageTitle="Ambiente di test della configurazione di base"
	description="Informazioni su come creare un ambiente di sviluppo e test semplice che simuli una Intranet semplificata in Microsoft Azure."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="josephd"/>

# Ambiente di test della configurazione di base

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-windows-test-config-env.md).

In questo articolo vengono fornite le istruzioni dettagliate per creare l’ambiente di test della configurazione di base in una rete virtuale di Azure.

È possibile utilizzare l'ambiente di test risultante:

- Per lo sviluppo e il testing di applicazioni.
- Per l’[ambiente cloud ibrido simulato](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md).
- Per estenderlo con altre macchine virtuali e servizi di Azure per un ambiente di test di progettazione.

L'ambiente di test della configurazione di base è costituito dalla subnet Corpnet in una rete virtuale solo cloud denominata TestLab che simula una intranet semplificata, privata e connessa a Internet.

![](./media/virtual-machines-windows-classic-test-config-env/BC_TLG04.png)

Contiene quanto segue:

- Una macchina virtuale di Azure su cui è in esecuzione Windows Server 2012 R2 denominato DC1, configurato come controller di dominio intranet e server DNS (Domain Name System).
- Una macchina virtuale di Azure su cui è in esecuzione Windows Server 2012 R2 denominata APP1, configurata come applicazione generica e server Web.
- Una macchina virtuale di Azure su cui è in esecuzione Windows Server 2012 R2 denominato CLIENT1 che verrà utilizzato come client intranet.

Questa configurazione consente a DC1, APP1, CLIENT1 e altri computer della subnet Corpnet di effettuarsi quanto segue:

- Connettersi a Internet per installare gli aggiornamenti, accedere alle risorse Internet in tempo reale e partecipare a tecnologie di cloud pubblico, ad esempio Microsoft Office 365 e altri servizi di Azure.
- Gestione remota mediante connessioni Desktop remoto dal computer connesso a Internet o alla rete dell'organizzazione.

Esistono quattro fasi per l'impostazione dell'ambiente di test della configurazione di base di Windows Server 2012 R2 in Azure.

1.	Creare la rete virtuale.
2.	Configurare DC1.
3.	Configurare APP1.
4.	Configurare CLIENT1.

Se non si dispone di una sottoscrizione Azure, è possibile effettuare l'iscrizione per ottenere una versione di valutazione gratuita in [Versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/). Se si dispone di una sottoscrizione MSDN Platforms, vedere i [vantaggi di Azure per gli abbonati MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/).

> [AZURE.NOTE] Macchine virtuali in Azure comportano un costo monetario quando sono in esecuzione. Il costo viene addebitato sulla base della versione di prova gratuita, della sottoscrizione MSDN Platforms o della sottoscrizione a pagamento. Per ulteriori informazioni sui costi dell'esecuzione di macchine virtuali di Azure, vedere i [dettagli relativi ai prezzi delle macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/) e il [Calcolatore dei costi Azure](https://azure.microsoft.com/pricing/calculator/). Per contenere i costi, vedere [Riduzione dei costi di macchine virtuali in ambiente di test in Azure](#costs).

## Fase 1: creare la rete virtuale

Creare innanzitutto la rete virtuale TestLab che ospiterà la subnet Corpnet della configurazione di base.

1.	Nella barra delle applicazioni del [portale di Azure classico](https://manage.windowsazure.com) fare clic su **Nuovo > Servizi di rete > Rete virtuale > Creazione personalizzata**.
2.	Nella pagina Dettagli della rete virtuale digitare **TestLab** in **Nome**.
3.	In **Percorso** selezionare il percorso appropriato.
4.	Fare clic sulla freccia Avanti.
5.	In Server DNS nella pagina Server DNS e connettività VPN, **Server DNS** digitare **DC1** in **Selezionare o immettere nome**, digitare **10.0.0.4** in **Indirizzo IP** quindi fare clic sulla freccia Avanti.
6.	Nella pagina Spazi di indirizzi della rete virtuale in **Subnet**, fare clic su **Subnet-1** e sostituire il nome con **Corpnet**.
7.	Nella colonna **CIDR (conteggio indirizzi)** per la subnet Corpn,et fare clic su **/24 (256)**.
8.	Fare clic sull'icona Completa. Attendere il completamento della creazione della rete virtuale prima di continuare.

Usare quindi le istruzioni disponibili in [Come installare e configurare Azure PowerShell](../install-configure-powershell.md) per installare Azure PowerShell nel computer locale. Quindi, aprire un prompt dei comandi di Azure PowerShell.

Selezionare innanzitutto la sottoscrizione di Azure corretta con questi comandi. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

È possibile ottenere il nome della sottoscrizione corretto dalla proprietà **SubscriptionName** dell'output del comando **Get-AzureSubscription**.

Successivamente, creare un servizio cloud di Azure. Il servizio cloud funge da limite di sicurezza e un contenitore logico per le macchine virtuali distribuite nella rete virtuale. Offre inoltre un modo per connettersi in remoto e gestire le macchine virtuali nella subnet Corpnet.

È necessario selezionare un nome univoco per il servizio cloud. *Il nome del servizio cloud può contenere solo lettere, numeri e trattini. Il primo e ultimo carattere nel campo deve essere una lettera o un numero.*

È ad esempio possibile nominare il servizio cloud TestLab-*UniqueSequence*, in cui *UniqueSequence* è un'abbreviazione dell'organizzazione. Se ad esempio il nome dell'organizzazione è Tailspin Toys, è possibile assegnare il nome TestLab-Tailspin al servizio cloud.

Per verificare l'univocità del nome, è possibile usare questo comando di Azure PowerShell.

	Test-AzureName -Service <Proposed cloud service name>

Se questo comando restituisce "False", il nome proposto è univoco. Quindi, creare il servizio cloud con questi comandi.

	$loc="<the same location (region) as your TestLab virtual network>"
	New-AzureService -Service <Unique cloud service name> -Location $loc

Registrare il nome effettivo del servizio cloud.

Successivamente, è necessario configurare un account di archiviazione che conterrà i dischi per le macchine virtuali e i dischi dati aggiuntivi. *È necessario selezionare un nome univoco che contenga solo lettere minuscole e numeri.* È possibile verificare l'univocità del nome dell'account di archiviazione con questo comando di Azure PowerShell.

	Test-AzureName -Storage <Proposed storage account name>

Se questo comando restituisce "False", il nome proposto è univoco. Quindi, creare l'account di archiviazione e impostare l’abbonamento da utilizzare con questi comandi.

	$stAccount="<your storage account name>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $loc
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $stAccount

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-classic-test-config-env/BC_TLG01.png)

## Fase 2: configurare DC1.

DC1 è un controller di dominio per il dominio Servizi di dominio Active Directory corp.contoso.com e un server DNS per le macchine virtuali della rete virtuale TestLab.

Innanzitutto, specificare il nome del servizio cloud e utilizzare questi comandi al prompt dei comandi di Azure PowerShell nel computer locale per creare una macchina virtuale di Azure per DC1.

	$serviceName="<your cloud service name>"
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for DC1."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel "AD" -LUN 0 -HostCaching None
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

Successivamente, connettersi alla macchina virtuale DC1.

1.	Nel riquadro sinistro del portale di Azure classico, fare clic su **Macchine virtuali**, quindi fare clic su **Avviato** nella colonna **Stato** della macchina virtuale DC1.  
2.	Nella barra delle applicazioni fare clic su **Connetti**.
3.	Quando viene richiesto di aprire DC1.rdp, fare clic su **Apri**.
4.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto, fare clic su **Connetti**.
5.	Alla richiesta di credenziali, usare le seguenti:
- Nome: **DC1\** [Local administrator account name]
- Password: [Nome dell'account amministratore locale]
6.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto che si riferisce ai certificati, fare clic su **Sì**.

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

Configurare quindi DC1 come controller di dominio e server DNS per il dominio corp.contoso.com. Eseguire questi comandi in un prompt dei comandi di Windows PowerShell a livello di amministratore.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSForest -DomainName corp.contoso.com -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Dopo il riavvio di DC1, riconnettersi alla macchina virtuale DC1.

1.	Nella pagina Macchine virtuali del portale di Azure classico, fare clic su **In esecuzione** nella colonna **Stato** relativa alla macchina virtuale DC1.
2.	Nella barra delle applicazioni fare clic su **Connetti**.
3.	Quando viene richiesto di aprire DC1.rdp, fare clic su **Apri**.
4.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto, fare clic su **Connetti**.
5.	Alla richiesta di credenziali, usare le seguenti:
- Nome: **CORP\**[Nome dell'account amministratore locale]
- Password: [Nome dell'account amministratore locale]
6.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto che si riferisce ai certificati, fare clic su **Sì**.

Successivamente, creare un account utente in Active Directory che verrà utilizzato quando si accede a computer membri del dominio CORP. In DC1 eseguire questi comandi in un prompt dei comandi di Windows PowerShell a livello di amministratore.

	New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

Si noti che non appena viene eseguito il comando, viene visualizzato una finestra in cui viene chiesto di immettere la password dell’account User1. Poiché questo account verrà utilizzato per le connessioni desktop remote per tutti i computer appartenenti al dominio CORP, scegliere una password complessa. Per verificarne il livello di complessità, vedere [Controllo password: utilizzo di password complesse](https://www.microsoft.com/security/pc-security/password-checker.aspx). Registrare la password dell'account User1 e archiviarlo in una posizione protetta.

Riconnettersi alla macchina virtuale DC1 utilizzando l'account CORP\\User1.

Successivamente, per consentire il traffico per lo strumento Ping, eseguire questo comando in un prompt dei comandi di Windows PowerShell a livello di amministratore.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-classic-test-config-env/BC_TLG02.png)

## Fase 3: configurare APP1

APP1 fornisce servizi di condivisione file e Web.

Innanzitutto, specificare il nome del servizio cloud e utilizzare questi comandi al prompt dei comandi di Azure PowerShell nel computer locale per creare una macchina virtuale di Azure per APP1.

	$serviceName="<your cloud service name>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for APP1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name APP1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

Successivamente, connettersi alla macchina virtuale APP1 utilizzando le credenziali CORP\\User1 e aprire un prompt dei comandi di Windows PowerShell a livello di amministratore.

Per controllare la comunicazione di rete e di risoluzione dei nomi tra APP1 e DC1, eseguire il comando **ping dc1.corp.contoso.com** per verificare che vengano restituite quattro risposte.

Quindi, impostare APP1 come server Web con questo comando al prompt dei comandi di Windows PowerShell.

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

Successivamente, creare una cartella condivisa e un file di testo all'interno della cartella in APP1 con questi comandi.

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-classic-test-config-env/BC_TLG03.png)

## Fase 4: configurare CLIENT1.

CLIENT1 agisce come un tipico, Tablet PC, computer desktop o portatile nella intranet di Contoso.

Innanzitutto, specificare il nome del servizio cloud e utilizzare questi comandi al prompt dei comandi di Azure PowerShell nel computer locale per creare una macchina virtuale di Azure per CLIENT1.

	$serviceName="<your cloud service name>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for CLIENT1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name CLIENT1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

Successivamente, connettersi alla macchina virtuale CLIENT1 con le credenziali CORP\\User1.

Per controllare la comunicazione di rete e di risoluzione dei nomi tra CLIENT1 e DC1, eseguire il comando **ping dc1.corp.contoso.com** in un prompt dei comandi Windows PowerShell per verificare che vengano restituite quattro risposte.

Successivamente, verificare che sia possibile accedere al Web e alle risorse di condivisione file in APP1 da CLIENT1.

1.	In Server Manager, nel riquadro dell'albero, fare clic su **Server locale**.
2.	In **Proprietà per CLIENT1**, fare clic su **On** accanto a ** Configurazione sicurezza avanzata IE**.
3.	In ** Configurazione sicurezza avanzata IE**, fare clic su **Off** relativamente ad **Amministratori** e **Utenti**, quindi fare clic su **OK**.
4.	Dalla schermata Start, fare clic su **Internet Explorer**, quindi su **OK**.
5.	Nella barra degli indirizzi digitare ****http://app1.corp.contoso.com/**, quindi premere INVIO. Dovrebbe essere visualizzata la pagina Web di Internet Information Services predefinita per APP1.
6.	Sulla barra delle applicazioni desktop, fare clic sull'icona Esplora File.
7.	Nella barra degli indirizzi digitare **\\\app1\\Files**, quindi premere INVIO.
8.	Dovrebbe essere visualizzata una finestra della cartella con il contenuto della cartella condivisa File.
9.	Nella finestra della cartella condivisa **File**, fare doppio clic sul file **Example.txt**. Viene visualizzato il contenuto del file di Example.txt.
10.	Chiudere il file **Example.txt in Blocco note** e le finestre della cartella condivisa **File**.

Questa sarà la configurazione finale.

![](./media/virtual-machines-windows-classic-test-config-env/BC_TLG04.png)

La configurazione di base in Azure è ora pronta per lo sviluppo di applicazioni e il test o per ambienti di test aggiuntivi.

## Passaggio successivo

- Configurare l'[ambiente cloud ibrido simulato](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) per testare le configurazioni ibride.

## <a id="costs"></a>Riduzione dei costi di macchine virtuali in ambiente di test in Azure

Per ridurre al minimo il costo di esecuzione delle macchine virtuali nell’ambiente di test, è possibile effettuare una delle seguenti operazioni:

- Creare l'ambiente di test ed eseguire il test e la dimostrazione necessari più rapidamente possibile. Al termine, eliminare le macchine virtuali dell’ambiente di test.
- Arrestare le macchine virtuali dell’ambiente di test in stato deallocato.

Per arrestare le macchine virtuali con Azure PowerShell, inserire il nome del servizio cloud ed eseguire questi comandi.

	$serviceName="<your cloud service name>"
	Stop-AzureVM -ServiceName $serviceName -Name "CLIENT1"
	Stop-AzureVM -ServiceName $serviceName -Name "APP1"
	Stop-AzureVM -ServiceName $serviceName -Name "DC1" -Force -StayProvisioned


Per assicurarsi che le macchine virtuali funzionino correttamente per l'avvio di tutti gli elementi dallo stato di arresto (deallocazione), è necessario avviarli nell'ordine seguente:

1.	DC1
2.	APP1
3.	CLIENT1

Per avviare le macchine virtuali in ordine con Azure PowerShell, inserire il nome del servizio cloud ed eseguire questi comandi.

	$serviceName="<your cloud service name>"
	Start-AzureVM -ServiceName $serviceName -Name "DC1"
	Start-AzureVM -ServiceName $serviceName -Name "APP1"
	Start-AzureVM -ServiceName $serviceName -Name "CLIENT1"

<!---HONumber=AcomDC_0323_2016-->