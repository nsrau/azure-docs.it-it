<properties 
	pageTitle="Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows" 
	description="Informazioni su come ripristinare la connettività Desktop remoto (RDP) alla macchina virtuale di Azure con diagnostica e passaggi per isolare l'origine del problema."
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/27/2015" 
	ms.author="josephd"/>

# Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows

In questo argomento viene descritto un approccio metodico per la correzione e la determinazione della causa radice delle connessioni Desktop remoto a macchine virtuali di Azure basate su Windows.

## Passaggio 1: Eseguire il pacchetto di diagnostica Azure IaaS

Per risolvere molti dei problemi comuni con la creazione di connessioni Desktop remoto, Microsoft ha creato un [pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864).

1.	Fare clic su **Pacchetto di diagnostica Microsoft Azure IaaS (Windows)** in questa pagina per creare una nuova sessione di diagnostica.
2.	Nella pagina **Quali dei seguenti problemi si verificano con la macchina virtuale di Azure in uso?**, selezionare il problema **Connettività RDP a una macchina virtuale di Azure (riavvio richiesto)**. 

Per ulteriori informazioni, vedere l’articolo della Knowledge base [Pacchetto di diagnostica Microsoft Azure IaaS (Windows)](http://support.microsoft.com/kb/2976864).

Se l'esecuzione del pacchetto di diagnostica Azure IaaS non ha consentito di risolvere il problema o non si è riusciti a eseguire il pacchetto di diagnostica, potrebbero essere necessarie informazioni più dettagliate per la risoluzione dei problemi descritte nei passaggi seguenti.

> [AZURE.NOTE]Il pacchetto di diagnostica Azure IaaS (Windows) deve essere eseguito da un computer con Windows 8, Windows 8.1, Windows Server 2012 o Windows Server 2012 R2.

## Passaggio 2: Determinare il messaggio di errore dal client Desktop remoto

Utilizzare queste sezioni in base al messaggio di errore che viene visualizzato.

### Finestra di messaggio di Connessione Desktop remoto: La sessione remota è stata disconnessa perché non sono disponibili server licenze di Desktop remoto per il rilascio della licenza.

Causa: il periodo di prova di 120 delle licenza per il ruolo Server Desktop remoto è scaduto ed è necessario installare le licenze.
 
Come soluzione temporanea, salvare una copia locale del file RDP dal portale di gestione di Azure ed eseguire questo comando al prompt dei comandi di Windows PowerShell per effettuare la connessione.

	mstsc <File name>.RDP /admin

In tal modo verrà disabilitata la licenza solo per quella connessione.

Se in realtà non sono necessarie due connessioni Desktop remoto contemporanee alla macchina virtuale, è possibile utilizzare Gestione server per rimuovere il ruolo Server Desktop remoto.

Vedere anche il post di blog [Errore della macchina virtuale di Azure VM con "Non sono disponibili server licenze Desktop remoto"](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx).

### Finestra di messaggio di Connessione Desktop remoto: Impossibile trovare il computer "nome".

Causa: il client Desktop remoto del computer non è in grado di risolvere il nome del computer nelle impostazioni del file RDP.

Di seguito è illustrato un esempio di file RDP generato dal portale di gestione di Azure.

	full address:s:tailspin-azdatatier.cloudapp.net:55919
	prompt for credentials:i:1

La parte di indirizzo è costituita dal nome di dominio completo del servizio cloud che contiene la macchina virtuale (in questo esempio tailspin-azdatatier.cloudapp.net) e dalla porta TCP esterna dell'endpoint per il traffico di Desktop remoto (55919).

Possibili soluzioni al problema:

- Se si utilizza una rete Intranet aziendale, assicurarsi che il computer abbia accesso al server proxy e sia in grado di inviargli traffico HTTPS.
- Se si utilizza un file RDP archiviato localmente, provare a utilizzarne uno generato dal portale di gestione di Azure per essere certi di disporre del nome corretto per il servizio cloud e la porta endpoint della macchina virtuale.

### Finestra di messaggio di Protezione di Windows: Le credenziali specificate non funzionano.

Causa: impossibile convalidare il nome account e la password inviati dalla macchina virtuale a cui si sta effettuando la connessione.

Un computer basato su Windows può convalidare le credenziali di un account locale o di un account di dominio.

- Per gli account locali, utilizzare la sintassi <computer name><nome account> (esempio: SQL1\\Admin4798). 
- Per gli account di dominio, utilizzare la sintassi <domain name><nome account> (esempio: CONTOSO\\johndoe).

Per i computer innalzati a controller di dominio in una nuova foresta di Active Directory, l'account amministratore locale al quale si è effettuato l’accesso al momento dell'innalzamento di livello viene convertito in un account equivalente con la stessa password nella nuova foresta e nel nuovo dominio. L'account amministratore locale precedente viene eliminato. Ad esempio, se è stato effettuato l'accesso con l'account amministratore locale DC1\\DCAdmin e la macchina virtuale è stata innalzata al livello di controller di dominio in una nuova foresta per il dominio corp.contoso.com, l'account locale DC1\\DCAdmin viene eliminato e viene creato un nuovo account di dominio CORP\\DCAdmin con la stessa password.

Controllare il nome dell'account per essere certi che sia un nome che possa essere verificato come account valido dalla macchina virtuale. Controllare la password per assicurarsi che sia quella corretta.

Se è necessario modificare la password dell'account amministratore locale, vedere [Come reimpostare una password o il servizio Desktop remoto per le macchine virtuali di Windows](virtual-machines-windows-reset-password.md).

### Finestra di messaggio di Connessione Desktop remoto: Il computer non è in grado di connettersi al computer remoto.

Causa: l'account utilizzato per la connessione non dispone dei diritti di accesso Desktop remoto.

Ogni computer Windows dispone di un gruppo locale Utenti Desktop remoto, che contiene gli account e gruppi che hanno il diritto di accedere con una connessione Desktop remoto. Anche i membri del gruppo Administrators locale dispongono dell’accesso, sebbene tali account non siano elencati come membri del gruppo locale Utenti Desktop remoto. Per i computer appartenenti a un dominio, il gruppo Administrators locale contiene anche gli amministratori di dominio per il dominio.

Assicurarsi che l'account che si utilizza per avviare la connessione disponga dei diritti di accesso al desktop remoto. Utilizzare un account amministratore di dominio o amministratore locale come soluzione temporanea per creare una connessione Desktop remoto e aggiungere l'account desiderato al gruppo Utenti Desktop remoto locale utilizzando lo snap-in Gestione Computer (**Strumenti di sistema > Utenti e gruppi locali > Gruppi > Utenti Desktop remoto**).

### Finestra di messaggio di Connessione Desktop remoto:  Impossibile connettersi al computer remoto per uno dei seguenti motivi ...

Causa: il client Desktop remoto non è in grado di raggiungere il servizio Servizi Desktop remoto della macchina virtuale. Questo problema può dipendere da molte cause principali.

Di seguito viene riportato il set di componenti coinvolti.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_0.png)
 
Prima di entrare nel processo dettagliato di risoluzione dei problemi, è utile esaminare mentalmente cosa è cambiato da quando si era in grado di creare connessioni Desktop remoto e utilizzare tale cambiamento come base per la correzione del problema. ad esempio:

- Se si era in grado di creare connessioni Desktop remoto ed è stato modificato l'indirizzo IP pubblico del servizio cloud contenente la macchina virtuale (noto anche come indirizzo IP virtuale [VIP]), nella cache del client DNS potrebbe essere presente una voce per il nome DNS del servizio cloud e l’*indirizzo IP precedente*. Scaricare la cache del client DNS e riprovare. In alternativa, provare a eseguire la connessione utilizzando il nuovo VIP.
- Se si è passati dall’utilizzo del portale di gestione di Azure o del portale di anteprima di Azure all’uso di un'applicazione per gestire le connessioni Desktop remoto, assicurarsi che la configurazione dell'applicazione includa la porta TCP determinato in modo casuale per il traffico di Desktop remoto. 

Nelle sezioni seguenti si passerà dall’isolamento e dalla definizione delle varie cause principali di questo problema, al fornire soluzioni definitive e temporanee.

## Passaggio 3: Passaggi preliminari per la risoluzione dei problemi dettagliata

Effettuare i seguenti passaggi:

- Verificare lo stato della macchina virtuale nel portale di gestione di Azure o nel portale di anteprima di Azure
- [Riavviare la macchina virtuale](https://msdn.microsoft.com/library/azure/dn763934.aspx)
- [Ridimensionare la macchina virtuale](https://msdn.microsoft.com/library/dn168976.aspx)

Dopo questi passaggi, tentare nuovamente la connessione Desktop remoto.

## Passaggio 4: Risoluzione dei problemi dettagliata 

L'impossibilità per il client Desktop remoto di raggiungere Servizi Desktop remoto nella macchina virtuale di Azure può essere dovuta alle seguenti fonti di problemi o di errori di configurazione:

- Computer client Desktop remoto
- Dispositivo periferico dell’Intranet dell’organizzazione
- Endpoint del servizio del cloud ed elenco di controllo di accesso (ACL)
- Gruppi di sicurezza di rete
- Macchina virtuale di Azure basata su Windows

### Origine 1: computer client Desktop remoto

Per eliminare il computer come fonte di problemi o errori di configurazione, verificare che il computer sia in grado di stabilire connessioni Desktop remoto a un altro computer locale basato su Windows.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_1.png)
 
Se non è possibile, effettuare i seguenti controlli sul computer:

- Un'impostazione firewall locale che blocca il traffico di Desktop remoto
- Software proxy client installato localmente che impedisce le connessioni Desktop remoto
- Software di monitoraggio della rete installato localmente che impedisce le connessioni Desktop remoto
- Altri tipi di software di sicurezza che effettuano il monitoraggio del traffico o consentano/non consentano tipi di traffico che impediscano le connessioni Desktop remoto

In tutti questi casi, provare a disabilitare temporaneamente il software e tentare una connessione Desktop remoto a un computer locale per determinare la causa principale. Quindi, contattare l'amministratore di rete per correggere le impostazioni del software in modo da consentire le connessioni Desktop remoto.

### Origine 2: dispositivo periferico dell’Intranet dell’organizzazione

Per eliminare il dispositivo periferico dell’Intranet dell’organizzazione come fonte di problemi o errori di configurazione, verificare che un computer connesso direttamente a Internet sia in grado di effettuare connessioni Desktop remoto alla macchina virtuale di Azure.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_2.png)
 
Se non si dispone di un computer connesso direttamente a Internet, è possibile creare facilmente e utilizzare una nuova macchina virtuale di Azure in un servizio cloud specifico. Per ulteriori informazioni, vedere [Creare una macchina virtuale con Windows in Azure](virtual-machines-windows-tutorial.md). Dopo aver completato i test, eliminare la macchina virtuale e il servizio cloud.

Se è possibile creare una connessione Desktop remoto con un computer collegato direttamente a Internet, controllare se nel dispositivo periferico dell’Intranet dell’organizzazione si verificano le seguenti condizioni:

- Firewall interno che blocca le connessioni HTTPS a Internet
- Server proxy che impedisce le connessioni Desktop remoto
- Software di rilevamento delle intrusioni o software per il monitoraggio della rete in esecuzione sui dispositivi nella rete perimetrale che impedisce le connessioni Desktop remoto

Rivolgersi all'amministratore di rete per correggere le impostazioni del dispositivo periferico dell’Intranet dell’organizzazione per consentire connessioni Desktop remoto a Internet basate su HTTPS.

### Origine 3: endpoint del servizio cloud e ACL

Per eliminare l'endpoint del servizio cloud e l’ACL come fonti di problemi o errori di configurazione, verificare che un'altra macchina virtuale di Azure che si trova nello stesso servizio cloud sia in grado di effettuare connessioni Desktop remoto alla macchina virtuale di Azure.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_3.png)
 
Se non si dispone di un'altra macchina virtuale nello stesso servizio cloud, è possibile crearne facilmente una nuova. Per ulteriori informazioni, vedere [Creare una macchina virtuale con Windows in Azure](virtual-machines-windows-tutorial.md). Dopo aver completato i test, eliminare la macchina virtuale aggiuntiva.

Se è possibile creare una connessione Desktop remoto con una macchina virtuale nello stesso servizio cloud, verificare quanto indicato di seguito:

- La configurazione dell'endpoint per il traffico Desktop remoto nella macchina virtuale di destinazione. La porta TCP privata dell'endpoint deve corrispondere alla porta TCP su cui è in ascolto il servizio Servizi Desktop remoto nella macchina virtuale, che per impostazione predefinita è 3389. 
- L’ACL per l'endpoint di traffico Desktop remoto sulla macchina virtuale di destinazione. Gli ACL consentono di specificare il traffico in ingresso da Internet consentito o negato in base al relativo indirizzo IP di origine. ACL configurati in modo errato possono impedire il traffico di Desktop remoto in ingresso all'endpoint. Esaminare gli ACL per assicurarsi che il traffico in ingresso dagli indirizzi IP pubblici del proxy o da altri server periferici sia consentito. Per ulteriori informazioni, vedere [Informazioni sugli elenchi di controllo di accesso di rete (ACL)](https://msdn.microsoft.com/library/azure/dn376541.aspx).

Per eliminare l'endpoint come fonte del problema, rimuovere l'endpoint corrente e creare un nuovo endpoint, scegliendo una porta casuale nell'intervallo tra 49152 e 65535 per il numero di porta esterna. Per ulteriori informazioni, vedere [Configurare endpoint in una macchina virtuale in Azure](virtual-machines-set-up-endpoints.md).

### Origine 4: gruppi di sicurezza di rete

I gruppi di sicurezza di rete consentono di avere un controllo più granulare del traffico in entrata e in uscita consentito. È possibile creare regole che si estendono alle subnet e ai servizi cloud in una rete virtuale di Azure. Esaminare le regole del gruppo di sicurezza di rete per garantire che sia consentito il traffico di Desktop remoto da Internet.

Per altre informazioni, vedere l'articolo relativo ai [Gruppi di sicurezza di rete](https://msdn.microsoft.com/library/azure/dn848316.aspx).

### Origine 5: macchina virtuale di Azure basata su Windows

L’ultima serie di problemi possibili si verifica sulla macchina virtuale di Azure stessa.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_5.png)
 
Innanzitutto, se non è stato possibile eseguire il [pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) per il problema **Connettività RDP a una macchina virtuale di Azure (riavvio necessario)**, seguire le istruzioni in [Come reimpostare una password o il servizio Desktop remoto per le macchine virtuali di Windows](virtual-machines-windows-reset-password.md) per reimpostare il servizio Servizi Desktop remoto nella macchina virtuale. In tal modo, si verificheranno i seguenti eventi:

- Verrà abilitata la regola predefinita "Desktop remoto" di Windows Firewall (porta TCP 3389).
- Verranno abilitate le connessioni Desktop remoto impostando il valore del Registro di sistema HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections su 0.

Provare nuovamente la connessione dal computer. Se non riesce, alcuni dei problemi possibili sono:

- Il servizio Servizi Desktop remoto non è in esecuzione nella macchina virtuale di destinazione.
- Il servizio Servizi Desktop remoto non è in ascolto sulla porta TCP 3389.
- Windows Firewall o altri firewall locali dispongono di una regola in uscita che impedisce il traffico di Desktop remoto.
- Il software di rilevamento delle intrusioni o il software per il monitoraggio della rete in esecuzione nella macchina virtuale di Azure impedisce le connessioni Desktop remoto.

Per risolvere questi problemi, è possibile utilizzare una sessione di PowerShell nella macchina virtuale di Azure. In primo luogo, è necessario installare un certificato per il servizio cloud di hosting della macchina virtuale. Andare a [Configurare l’accesso remoto PowerShell protetto alle macchine virtuali di Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) e scaricare il file di script **InstallWinRMCertAzureVM.ps1** in una cartella nel computer locale.

Successivamente, installare Azure PowerShell, se non è stato già installato. Vedere [Come installare e configurare Azure PowerShell](install-configure-powershell.md).

Quindi, aprire un prompt dei comandi di Azure PowerShell e modificare la cartella corrente nel percorso del file di script **InstallWinRMCertAzureVM.ps1**. Per eseguire uno script PowerShell, è necessario impostare i criteri di esecuzione corretti. Eseguire il comando **Get-ExecutionPolicy** per determinare il livello di criterio corrente. Per informazioni sull'impostazione del livello appropriato, vedere [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Successivamente, inserire il nome della sottoscrizione di Azure, il nome del servizio cloud e il nome della macchina virtuale (rimuovendo i caratteri < and >), quindi eseguire questi comandi.

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

È possibile ottenere il nome della sottoscrizione corretto dalla proprietà SubscriptionName della visualizzazione del comando **Get-AzureSubscription**. È possibile ottenere il nome del servizio cloud per la macchina virtuale dalla colonna ServiceName della visualizzazione del comando **Get-AzureVM**.

Per dimostrare che si dispone di questo nuovo certificato, aprire uno snap-in Certificati incentrato sull'utente corrente e cercare nella cartella **Autorità di certificazione radice disponibile nell'elenco locale\\Certificati**. Verrà visualizzato un certificato con il nome DNS del servizio cloud nella colonna Rilasciato a (esempio: cloudservice4testing.cloudapp.net).

Successivamente, avviare una sessione remota di PowerShell con questi comandi.

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

Dopo aver immesso le credenziali di amministratore valide, verrà visualizzato un prompt di Azure PowerShell simile al seguente:

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

La prima parte del prompt indica che vengono emessi i comandi PowerShell per il servizio cloud che contiene la macchina virtuale di destinazione. Il nome del servizio cloud sarà un valore diverso da "cloudservice4testing.cloudapp.net".

È ora possibile inviare comandi PowerShell per analizzare i problemi aggiuntivi citati in precedenza e apportare correzioni alla configurazione.

### Per correggere manualmente il servizio Servizi Desktop remoto in ascolto sulla porta TCP

Se non è stato possibile eseguire il [pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) per il problema **Connettività RDP a una macchina virtuale di Azure (riavvio necessario)**, al prompt della sessione remota di PowerShell eseguire questo comando.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

La proprietà PortNumber indica il numero di porta corrente. Se necessario, modificare il numero di porta Desktop remoto nuovamente nel valore predefinito (3389) con questo comando.

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Verificare che la porta sia stata modificata in 3389 con questo comando.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Chiudere la sessione remota di PowerShell con questo comando.

	Exit-PSSession

Verificare che anche l'endpoint Desktop remoto per la macchina virtuale di Azure utilizzi la porta TCP 3398 come porta interna. Quindi, riavviare la macchina virtuale di Azure e provare nuovamente la connessione desktop remoto.

## Passaggio 5: Inviare il problema ai forum di supporto tecnico di Azure

Per ottenere assistenza dagli esperti di Azure di tutto il mondo, è possibile inviare il problema ai forum MSDN Azure oppure Overflow dello stack. Per ulteriori informazioni vedere [Forum di Microsoft Azure](http://azure.microsoft.com/support/forums/).

## Passaggio 6: Archiviare un incidente del supporto tecnico di Azure

Se è stato eseguito il [pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) per il problema **Connettività RDP a una macchina virtuale di Azure (riavvio necessario)** o sono stati effettuati i passaggi da 2 a 5 in questo articolo e il problema è stato inviato ai forum di supporto di Azure ma non è comunque possibile creare una connessione Desktop remoto, un'alternativa da considerare è la possibilità di ricreare la macchina virtuale.

Se non è possibile ricreare la macchina virtuale, potrebbe essere il momento per archiviare un incidente del supporto tecnico di Azure.

Per archiviare un incidente, andare al [sito del supporto di Azure](http://azure.microsoft.com/support/options/) e fare clic su **Ottenere supporto**.

Per informazioni sull'utilizzo del supporto tecnico di Azure, vedere [Domande frequenti sul supporto tecnico di Microsoft Azure](http://azure.microsoft.com/support/faq/).

## Risorse aggiuntive

[Pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Come reimpostare una password o il servizio Desktop remoto per le macchine virtuali di Windows](virtual-machines-windows-reset-password.md)

[Come installare e configurare Azure PowerShell](install-configure-powershell.md)

[Macchine virtuali - Documentazione](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Domande frequenti sulle macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/dn683781.aspx)


<!--HONumber=54-->