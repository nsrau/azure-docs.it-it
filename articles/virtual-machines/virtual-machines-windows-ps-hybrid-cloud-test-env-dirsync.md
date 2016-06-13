<properties 
	pageTitle="Ambiente di prova di Office 365 DirSync | Microsoft Azure" 
	description="Informazioni su come configurare un server di sincronizzazione delle directory di Office 365 (DirSync) in un cloud ibrido per IT pro o test di sviluppo." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# Configurazione della sincronizzazione della directory (DirSync) di Office 365 in un cloud ibrido per l'esecuzione di test
 
I passaggi in questo argomento illustrano la creazione di un ambiente di cloud ibrido per il test della sincronizzazione delle directory di Office 365 (DirSync) con sincronizzazione della password ospitata in Microsoft Azure. Di seguito è riportata la configurazione risultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph3.png)
 
Questa configurazione simula un server DirSync in ambiente di produzione Azure dal percorso su Internet. È costituita da:

- Una rete locale semplificata (la subnet Corpnet).
- Una rete virtuale cross-premise ospitata in Azure (TestVNET).
- Una connessione VPN da sito a sito.
- Una sottoscrizione di valutazione di Office 365 FastTrack.
- Un server DirSync che esegue lo strumento Azure AD Connect e un controller di dominio secondario nella rete virtuale TestVNET.

Questa configurazione rappresenta una base e un punto di partenza comune da cui è possibile:

- Sviluppare e testare applicazioni per Office 365 basate sulla sincronizzazione con un dominio di Active Directory locale tramite la sincronizzazione delle password.
- Eseguire il test di questo carico di lavoro IT basato sul cloud.

La configurazione di questo ambiente di test cloud ibrido prevede tre fasi principali:

1.	Configurare l'ambiente cloud ibrido per l'esecuzione di test.
2.	Configurare la versione di valutazione di Office 365 FastTrack.
3.	Configurare il server DirSync (DS1).

Se non si ha già una sottoscrizione di Azure, è possibile iscriversi per ottenere un account gratuito nella pagina [Versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/). Se si ha un abbonamento a MSDN o una sottoscrizione di Visual Studio, vedere [Credito Azure mensile per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Fase 1: Impostare l'ambiente cloud ibrido

Utilizzare le istruzioni nell’argomento [Configurare l’ambiente cloud ibrido per il test](virtual-machines-windows-ps-hybrid-cloud-test-env-base.md). Poiché l'ambiente di test non richiede la presenza del server APP1 nella subnet Corpnet, è possibile arrestarlo per il momento.

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph1.png)

> [AZURE.NOTE] Per la fase 1, è possibile configurare anche l'[ambiente di test del cloud ibrido simulato](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md).

## Fase 2: Configurare la versione di prova di Office 365 FastTrack.

Per avviare la versione di valutazione di Office 365 FastTrack, sono necessari il nome di una società fittizia e un account Microsoft. È consigliabile usare una variante del nome dell'azienda Contoso per il nome della società, cioè una società fittizia usata nei contenuti di esempio di Microsoft, ma non è obbligatorio.

Successivamente, iscriversi per ottenere un nuovo account Microsoft. Andare a **http://outlook.com** e creare un account con un indirizzo di posta elettronica come user123@outlook.com. Verrà eseguita l'iscrizione a una versione di valutazione di Office 365 FastTrack con questo account.

Quindi, iscriversi per ottenere una nuova versione di valutazione di Office 365 Enterprise E3.

1.	Accedere a CLIENT1 con le credenziali dell'account CORP\\User1.
2.	Aprire Internet Explorer e passare a **https://go.microsoft.com/fwlink/p/?LinkID=403802**.
3.	Eseguire i vari passaggi del processo di iscrizione per la versione di valutazione di Office 365 Enterprise E3.

Quando viene richiesto l'**indirizzo di posta elettronica aziendale**, specificare il nuovo account Microsoft.

Quando viene richiesto di creare un ID, digitare il nome di un account Office 365 iniziale, il nome della società fittizia e quindi una password. Annotare l'indirizzo di posta elettronica risultante (ad esempio user123@contoso123.onmicrosoft.com) e la password in un luogo sicuro. Queste informazioni sono necessarie per completare la configurazione di Azure AD Connect nella fase 3.

Al termine, verrà visualizzata la pagina principale del portale di Office 365. Nella barra multifunzione superiore fare clic su **Admin** e quindi fare clic su **Office 365**. Verrà visualizzata la pagina dell'area di amministrazione di Office 365. Tenere aperta questa pagina in CLIENT1.

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph2.png)

## Fase 3: Configurare il server DirSync (DS1)

Dal portale di Azure avviare il computer DC2, se necessario.

Creare quindi una macchina virtuale di Azure per DS1 con questi comandi al prompt dei comandi di Azure PowerShell nel computer locale. Per usare questi comandi, inserire i valori delle variabili e rimuovere i caratteri < and >.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name DS1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DS1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName DS1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DS1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DS1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DS1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DS1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Usare quindi il portale di Azure per connettersi alla macchina virtuale DS1 con le credenziali dell'account amministratore locale.

Configurare quindi una regola di Windows Firewall per permettere il traffico per il test della connettività di base. Da un prompt dei comandi Windows PowerShell a livello di amministratore in DS1, eseguire questi comandi.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc2.corp.contoso.com

Il comando ping restituirà quattro risposte dall'indirizzo IP 192.168.0.4.

Aggiungere la macchina virtuale DS1 al dominio CORP di Active Directory con questi comandi al prompt di Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Usare l'account CORP\\User1 quando viene richiesto di specificare le credenziali dell'account di dominio per il comando **Add-Computer**.

Dopo il riavvio, usare il portale di Azure per connettersi a DS1 con l'account CORP\\User1 e la password.

Quindi, installare .NET 3.5 in DS1 con questo comando al prompt dei comandi di Windows PowerShell a livello di amministratore.

	Add-WindowsFeature NET-Framework-Core

Successivamente, abilitare la sincronizzazione delle directory per la versione di valutazione di Office 365 FastTrack.

1.	In CLIENT1 nella pagina **Centro di amministrazione di Office 365** nel riquadro sinistro fare clic su **Utenti** e quindi fare clic su **Utenti attivi**.
2.	Per la **sincronizzazione di Active Directory** fare clic su **Configura**.
3.	Nella pagina Impostazione e gestione della sincronizzazione di Active Directory, al passaggio 3 fare clic su **Attiva**.
4.	Quando viene visualizzato il messaggio **Attivare la sincronizzazione di Active Directory?**, fare clic su **Attiva**. Al termine, viene visualizzato il messaggio **Sincronizzazione di Active Directory attivata** al passaggio 3.
5.	Lasciare la pagina **Impostazione e gestione della sincronizzazione di Active Directory** aperta in CLIENT1.

Al prompt di Windows PowerShell in DC1 eseguire questi comandi **uno alla volta** per creare una nuova unità organizzativa denominata contoso\_users e aggiungere due nuovi account utente per Marci Kaufman e Lynda Meyer.

	New-ADOrganizationalUnit -Name contoso_users -Path "DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName marcik -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Marci Kaufman" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName lyndam -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Lynda Meyer" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"

Quando si esegue ogni comando di Windows PowerShell **New-ADUser**, viene richiesta la nuova password dell'utente. Annotare queste password e memorizzarle in una posizione sicura: saranno necessarie più avanti.

Installare e configurare quindi lo strumento Azure AD Connect su DS1.

1.	Eseguire Internet Explorer, digitare **https://www.microsoft.com/download/details.aspx?id=47594** nella barra degli **indirizzi** e quindi premere INVIO.
2.	Eseguire il programma di installazione di Microsoft Azure AD Connect.
3.	Dal desktop fare doppio clic su **Azure AD Connect**.
4.	Nella pagina **iniziale** selezionare **Accetto le condizioni di licenza e l'informativa sulla privacy** e quindi fare clic su **Continua**.
5.	Nella pagina **Impostazioni rapide** fare clic su **Usa impostazioni rapide**.
6.	Nella pagina **Connessione ad Azure AD** digitare l'indirizzo di posta elettronica e la password dell'account inizialmente creato durante la configurazione della versione di valutazione di Office 365 FastTrack nella fase 2. Fare clic su **Avanti**.
7.	Nella pagina **Connessione ad AD DS** digitare **CORP\\User1** in **Nome utente** e la password dell'account User1 in **Password**. Fare clic su Avanti.
8.	Nella pagina **Pronte per la configurazione** rivedere le impostazioni e quindi fare clic su **Installa**.
9.	Nella pagina **Configurazione completata** fare clic su **Esci**.

Verificare quindi che gli account utente nel dominio CORP siano sincronizzati con Office 365. Si noti che potrebbero essere necessari alcuni minuti prima che venga eseguita la sincronizzazione.

In CLIENT1 nella pagina **Impostazione e gestione della sincronizzazione di Active Directory** fare clic sul collegamento **Utenti** nel passaggio 6 di questa pagina. Se la sincronizzazione della directory è stata eseguita correttamente, verrà visualizzato quanto segue.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-example.png)

La colonna **Stato** indica che l'account è stato ottenuto tramite la sincronizzazione con un dominio di Active Directory.

Quindi, eseguire una dimostrazione della sincronizzazione delle password di Office 365 con l'account di Active Directory di Lynda Meyer.

1.	In CLIENT1 nella pagina **Utenti attivi** selezionare l'account **Lynda Meyer**.
2.	Nelle proprietà dell'account Lynda Meyer, in **Licenza assegnata**, fare clic su **Modifica**.
3.	Nella scheda **Assegna licenza**, selezionare un percorso in **Impostazione località utente** (ad esempio Stati Uniti).
4.	Selezionare **Microsoft Office 365 Piano E3**, quindi fare clic su **Salva**.
5.	Chiudere Internet Explorer.
6.	Eseguire Internet Explorer e passare a **http://portal.microsoftonline.com**.
7.	Accedere con le credenziali di Office 365 di Lynda Meyer. Il nome utente sarà lyndam@<*il nome fittizio*>.onmicrosoft. La password è la password dell'account utente di Active Directory di Lynda Meyer.
8.	Dopo aver eseguito l'accesso correttamente, viene visualizzata la pagina principale del portale di Office 365 con **Creiamo una differenza oggi**.

Questa è la configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph3.png)
 
Questo ambiente è ora pronto per eseguire il test delle applicazioni di Office 365 che si basano sulla funzionalità DirSync di Office 365 o il test della funzionalità e delle prestazioni di DirSync da DS1.

## Passaggio successivo

- Distribuire il carico di lavoro [nell'ambiente di produzione](http://technet.microsoft.com/library/dn635310.aspx).

<!---HONumber=AcomDC_0601_2016-->