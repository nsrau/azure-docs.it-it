<properties 
	pageTitle="Ambiente di prova di Office 365 DirSync | Microsoft Azure"
	description="Informazioni su come configurare un server di sincronizzazione delle directory di Office 365 (DirSync) in un cloud ibrido per IT pro o test di sviluppo."
	services="virtual-network"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/08/2015"
	ms.author="josephd"/>

# Configurazione della sincronizzazione della directory (DirSync) di Office 365 in un cloud ibrido per l'esecuzione di test

I passaggi in questo argomento illustrano la creazione di un ambiente di cloud ibrido per il test della sincronizzazione delle directory di Office 365 (DirSync) con sincronizzazione della password ospitata in Microsoft Azure. Di seguito è riportata la configurazione risultante.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
Questa configurazione simula un server DirSync in ambiente di produzione Azure dal percorso su Internet. È costituita da:

- Una rete locale semplificata (la subnet Corpnet).
- Una rete virtuale cross-premise ospitata in Azure (TestVNET).
- Una connessione VPN da sito a sito.
- Una sottoscrizione di valutazione di Office 365 FastTrack.
- Un server DirSync e un controller di dominio secondario nella rete virtuale TestVNET.

Questa configurazione rappresenta una base e un punto di partenza comune da cui è possibile:

- Sviluppare e testare applicazioni per Office 365 basate sulla sincronizzazione con un dominio di Active Directory locale tramite la sincronizzazione delle password.
- Eseguire il test di questo carico di lavoro IT basato sul cloud.

La configurazione di questo ambiente di test cloud ibrido prevede tre fasi principali:

1.	Configurare l'ambiente cloud ibrido per l'esecuzione di test.
2.	Configurare la versione di valutazione di Office 365 FastTrack.
3.	Configurare il server DirSync (DS1).

Se non si dispone di una sottoscrizione Azure, è possibile effettuare l'iscrizione per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/). Se si dispone di un abbonamento MSDN, vedere [Benefici di Azure per gli abbonati MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Fase 1: Impostare l'ambiente cloud ibrido

Utilizzare le istruzioni nell’argomento [Configurare l’ambiente cloud ibrido per il test](virtual-networks-setup-hybrid-cloud-environment-testing.md). Poiché l'ambiente di test non richiede la presenza del server APP1 nella subnet Corpnet, è possibile arrestarlo per il momento.

Questa è la configurazione corrente.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_1.png)

> [AZURE.NOTE]Per la fase 1, è possibile configurare l'ambiente di test cloud ibrido simulato. Vedere [Impostare un ambiente cloud ibrido simulato per i test](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) per le istruzioni.

## Fase 2: Configurare la versione di prova di Office 365 FastTrack.

Per avviare la versione di valutazione di Office 365 FastTrack, sono necessari il nome di una società fittizia e un account Microsoft. È consigliabile usare una variante del nome dell'azienda Contoso per il nome della società, cioè una società fittizia usata nei contenuti di esempio di Microsoft, ma non è obbligatorio.

Successivamente, iscriversi per ottenere un nuovo account Microsoft. Andare a ****http://outlook.com** e creare un account con un indirizzo di posta elettronica come user123@outlook.com. Verrà eseguita l'iscrizione a una versione di valutazione di Office 365 FastTrack con questo account.

Quindi, iscriversi per ottenere una nuova versione di valutazione di Office 365 FastTrack.

1.	Accedere a CLIENT1 con le credenziali dell'account CORP\\User1.
2.	Aprire Internet Explorer e passare a ****http://fasttrack.office.com**.
3.	Fare clic su **Introduzione a Fast Track**.
4.	Nella pagina introduttiva di FastTrack, sotto **Innanzitutto, iscriviti per ottenere una versione di prova di Office 365**, fare clic su **Per le aziende, cliccare qui per l’iscrizione**.
5.	Nella pagina Passaggio 1, compilare i campi specificando il nuovo account Microsoft in **Indirizzo e-mail aziendale**, quindi fare clic su **Avanti**.
6.	Nella pagina Step 2, digitare il nome di un account Office 365 iniziale nel primo campo, il nome della società fittizia, quindi una password. Annotare l'indirizzo di posta elettronica risultante (ad esempio user123@contoso123.onmicrosoft.com) e la password in un luogo sicuro. Queste informazioni saranno necessarie per completare la configurazione guidata dello strumento di sincronizzazione di Active Directory nella fase 3. Fare clic su **Avanti**.
7.	Nella pagina Passaggio 3, digitare il numero di telefono di un cellulare o di uno smartphone in grado di ricevere SMS e quindi fare clic su **Invia messaggio al mio numero**.
8.	Dopo aver ricevuto l'SMS sul telefono, digitare il codice di verifica e quindi fare clic su **Crea il mio account**. 
9.	Al termine della creazione dell'account Office 365, fare clic su **Sei pronto per iniziare**.
10.	Verrà visualizzata la pagina principale del portale di Office 365. Nella barra multifunzione superiore fare clic su **Admin**, quindi fare clic su **Office 365**. Verrà visualizzata la pagina dell'area di amministrazione di Office 365. Tenere aperta questa pagina in CLIENT1.

Questa è la configurazione corrente.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_2.png)

## Fase 3: Configurare il server DirSync (DS1)

Innanzitutto, creare una macchina virtuale di Azure per DS1 con questi comandi al prompt dei comandi di Azure PowerShell nel computer locale. Per usare questi comandi, inserire i valori delle variabili e rimuovere i caratteri < and >.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for DS1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DS1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Successivamente, connettersi alla macchina virtuale DS1.

1.	Nella pagina Macchine virtuali del portale di gestione Azure, fare clic su **In esecuzione** nella colonna STATO relativa alla macchina virtuale DS1.
2.	Nella barra delle applicazioni fare clic su **Connetti**. 
3.	Quando viene richiesto di aprire DS1.rdp, fare clic su **Apri**.
4.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto, fare clic su **Connetti**.
5.	Quando vengono richieste le credenziali, usare le seguenti:
	- Nome: **CORP\\User1**
	- Password: [Password dell'account User1]
6.	Quando viene visualizzata una finestra di messaggio di Connessione Desktop remoto che si riferisce ai certificati, fare clic su **Sì**.

Configurare quindi una regola di Windows Firewall per permettere il traffico per il test della connettività di base. Da un prompt dei comandi Windows PowerShell a livello di amministratore in DS1, eseguire questi comandi.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Il comando ping dovrebbe restituire quattro risposte dall'indirizzo IP 10.0.0.1.

Quindi, installare .NET 3.5 in DS1 con questo comando al prompt dei comandi di Windows PowerShell.

	Add-WindowsFeature NET-Framework-Core

Successivamente, installare Directory Sync in DS1.

1.	Eseguire Internet Explorer, digitare ****http://go.microsoft.com/fwlink/?LinkID=278924** nella barra degli indirizzi e quindi premere INVIO. Quando viene richiesto di eseguire dirsync.exe, fare clic sulla freccia accanto a **Salva**, fare clic su **Salva con nome**, quindi fare clic su **Salva** per salvare il file nella cartella download. Per ulteriori informazioni sull'installazione dello strumento, vedere [Installare o aggiornare lo strumento di sincronizzazione della Directory](http://technet.microsoft.com/library/jj151800).
2.	Aprire la cartella **Download**, fare doppio clic sul file **dirsync** e quindi fare clic su **Esegui come amministratore**.
3.	Nella pagina iniziale della procedura guidata di installazione di sincronizzazione di Active Directory, fare clic su **Avanti**. 
4.	Nella pagina Condizioni di licenza, fare clic su **Accetto**, quindi fare clic su **Avanti**.
5.	Nella pagina Selezione cartella installazione, fare clic su **Avanti**. Il completamento dell'installazione potrebbe richiedere alcuni minuti.
6.	Nella pagina finale, deselezionare **Avvia ora configurazione guidata**, quindi fare clic su **Fine**.
7.	Dalla schermata Start, fare clic su **user1**, quindi fare clic su **Disconnetti**.

Successivamente, abilitare la sincronizzazione delle directory per la versione di valutazione di Office 365 FastTrack.

1.	In CLIENT1 sulla pagina **Centro di amministrazione di Office 365** nel riquadro sinistro fare clic su **Utenti**, quindi fare clic su **Utenti attivi**.
2.	Per **Sincronizzazione di Active Directory**, fare clic su **Configura**.
3.	Nella pagina Impostazione e gestione della sincronizzazione di Active Directory, al passaggio 3, fare clic su **Attiva**.
4.	Quando viene visualizzato il messaggio **Attivare la sincronizzazione di Active Directory?**, fare clic su **Attiva**. Al termine, viene visualizzato il messaggio **Sincronizzazione di Active Directory attivata** al passaggio 3.
5.	Lasciare la pagina **Impostazione e gestione della sincronizzazione di Active Directory** aperta in CLIENT1.

Quindi, accedere a DC1 con l'account CORP\\User1 e aprire un prompt dei comandi di Windows PowerShell a livello di amministratore. Eseguire questi comandi uno alla volta per creare una nuova unità organizzativa denominata contoso\_users e aggiungere due nuovi account utente per Marci Kaufman e Lynda Meyer.

	New-ADOrganizationalUnit -Name contoso_users -Path "DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName marcik -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Marci Kaufman" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName lyndam -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Lynda Meyer" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"

Quando si esegue ogni comando di Windows PowerShell, viene richiesta la nuova password dell'utente. Annotare queste password e memorizzarle in una posizione sicura: saranno necessarie più avanti.

Successivamente, configurare Directory Sync in DS1.

1.	Accedere a DS1 con le credenziali dell'account CORP\\User1.
2.	Nella schermata **Start** digitare **Directory Sync**.
3.	Fare clic con il pulsante destro del mouse su **Configurazione sincronizzazione della directory**, quindi fare clic su **Esegui come amministratore**. Verrà avviata la configurazione guidata.
4.	Nella pagina di benvenuto fare clic su **Avanti**.
5.	Nella pagina delle credenziali di Microsoft Azure Active Directory, digitare l'indirizzo di posta elettronica e la password dell'account inizialmente creato durante la configurazione della versione di valutazione di Office 365 FastTrack nella fase 2. Fare clic su Avanti. 
6.	Nella pagina credenziali Active Directory, digitare **CORP\\User1** in **Nome utente** e la password dell'account User1 in **Password**. Fare clic su **Avanti**.
7.	Nella pagina Distribuzione ibrida, selezionare **Abilita distribuzione ibrida**, quindi fare clic su **Avanti**.
8.	Nella pagina di sincronizzazione Password, selezionare **Abilita la sincronizzazione delle Password**, quindi fare clic su **Avanti**.
9.	Viene visualizzata la pagina Configurazione. Al termine della configurazione, fare clic su **Avanti**.
10.	Nella pagina finale fare clic su **Fine**. Quando richiesto, fare clic su **OK**.

Verificare quindi che gli account utente nel dominio CORP siano sincronizzati con Office 365. Si noti che potrebbero essere necessarie alcune ore prima che venga eseguita la sincronizzazione.

In CLIENT1 nella pagina **Impostazione e Gestione sincronizzazione di Active Directory** fare clic sul collegamento **Utenti** nel passaggio 6 di questa pagina. Se la sincronizzazione della directory è stata eseguita correttamente, verrà visualizzato quanto segue.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_4.png)

La colonna **Stato** indica che l'account è stato ottenuto tramite la sincronizzazione con un dominio di Active Directory.

Quindi, eseguire una dimostrazione della sincronizzazione delle password di Office 365 con l'account di Active Directory di Lynda Meyer.

1.	In CLIENT1 nella pagina **Utenti attivi**, seleziona l’account **Lynda Meyer**.
2.	Nelle proprietà dell'account Lynda Meyer, in **Licenza assegnata**, fare clic su **Modifica**.
3.	Nella scheda **Assegna licenza**, selezionare un percorso in **Impostazione località utente** (ad esempio Stati Uniti).
4.	Selezionare **Microsoft Office 365 Piano E3**, quindi fare clic su **Salva**.
5.	Chiudere Internet Explorer.
6.	Eseguire Internet Explorer e passare a ****http://portal.microsoftonline.com**.
7.	Accedere con le credenziali di Office 365 di Lynda Meyer. Il nome utente sarà lyndam @<*il nome fittizio*>.onmicrosoft. La password è la password dell'account utente di Active Directory di Lynda Meyer.
8.	Dopo aver eseguito l'accesso correttamente, viene visualizzata la pagina principale del portale di Office 365 con **Creiamo una differenza oggi**.

Questa è la configurazione corrente.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
Questo ambiente è ora pronto per eseguire il test delle applicazioni di Office 365 che si basano sulla funzionalità DirSync di Office 365 o il test della funzionalità e delle prestazioni di DirSync da DS1.

## Risorse aggiuntive

[Distribuire la sincronizzazione delle directory di Office 365 (DirSync) in Microsoft Azure](http://technet.microsoft.com/library/dn635310.aspx)

[Soluzioni con Office Server e Cloud](http://technet.microsoft.com/library/dn262744.aspx)

[Configurazione di un ambiente cloud ibrido per l'esecuzione di test](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Configurazione di una farm Intranet di SharePoint in un cloud ibrido per l'esecuzione di test](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Configurazione di un'applicazione LOB basata sul Web in un cloud ibrido per l'esecuzione di test](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Configurazione di un ambiente cloud ibrido simulato per l'esecuzione di test](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Ambienti di test basati su cloud ibrido di Azure](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Linee guida sull'implementazione dei servizi di infrastruttura di Azure](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)


 

<!---HONumber=August15_HO9-->