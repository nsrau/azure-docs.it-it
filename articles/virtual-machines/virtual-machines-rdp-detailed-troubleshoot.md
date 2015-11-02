<properties
	pageTitle="Risoluzione dettagliata del Desktop remoto | Microsoft Azure"
	description="Passaggi della risoluzione dettagliata dei problemi per le connessioni RDP a una macchina virtuale di Azure che esegue Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="dkshir"/>

# Risoluzione dettagliata dei problemi relativi a connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

In questo articolo vengono fornite procedure dettagliate sulla risoluzione dei problemi per gli errori di Desktop remoto complessi.

> [AZURE.IMPORTANT]Per eliminare gli errori più comuni di Desktop remoto, assicurarsi di leggere [Risoluzione dei problemi di base per Desktop remoto](virtual-machines-troubleshoot-remote-desktop-connections.md) prima di procedere.

## Contattare il supporto tecnico di Azure

Se è necessaria ulteriore assistenza in qualsiasi punto in questo articolo, è possibile contattare gli esperti di Azure su [MSDN Azure e i forum di overflow dello stack](http://azure.microsoft.com/support/forums/).

In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Andare al [sito di supporto di Azure](http://azure.microsoft.com/support/options/) e fare clic su **Ottieni supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Microsoft Azure](http://azure.microsoft.com/support/faq/).


## Messaggio di errore generico di Desktop remoto

A volte può capitare di vedere questo messaggio di errore nella finestra di messaggio di Connessione Desktop remoto: _Impossibile connettersi al computer remoto per uno dei seguenti motivi..._

Questo errore si presenta quando il client Desktop remoto non è in grado di raggiungere il servizio Desktop remoto della macchina virtuale. Possono esserci varie ragioni per questo errore.

Di seguito viene riportato il set dei componenti coinvolti.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_0.png)

Prima di entrare nel processo dettagliato di risoluzione dei problemi, è utile esaminare mentalmente cosa è cambiato da quando si era in grado di creare connessioni Desktop remoto e utilizzare tale cambiamento come base per la correzione del problema. Ad esempio:

- Se si era in grado di creare connessioni Desktop remoto ed è stato modificato l'indirizzo IP pubblico della macchina virtuale o del servizio cloud contenente la macchina virtuale (noto anche come indirizzo IP virtuale [VIP]), nella cache del client DNS potrebbe essere presente una voce per il nome DNS e l’*indirizzo IP precedente*. Scaricare la cache del client DNS e riprovare. In alternativa, provare a effettuare la connessione utilizzando il nuovo indirizzo VIP.
- Se si è passati dall'utilizzo del portale di Azure o del portale di anteprima di Azure all'uso di un'applicazione per gestire le connessioni Desktop remoto, assicurarsi che la configurazione dell'applicazione includa la porta TCP determinata in modo casuale per il traffico di Desktop remoto.

Nelle sezioni seguenti si passerà dall’isolamento e dalla definizione delle varie cause principali di questo problema, al fornire soluzioni definitive e temporanee.


## Operazioni preliminari

Eseguire questi passaggi prima di procedere alla risoluzione dei problemi dettagliata.

- Verificare lo stato della macchina virtuale nel portale di Azure o nel portale di anteprima di Azure
- Riavviare la macchina virtuale
- [Ridimensionare la macchina virtuale](virtual-machines-size-specs.md)

Dopo questi passaggi, tentare nuovamente la connessione Desktop remoto.


## Risoluzione dettagliata dei problemi

Il client Desktop remoto potrebbe non essere in grado di raggiungere il servizio di Desktop remoto nella macchina virtuale di Azure a causa di problemi o errori di configurazione nelle origini seguenti:

- Computer client Desktop remoto
- Dispositivo periferico dell’Intranet dell’organizzazione
- Endpoint del servizio cloud ed elenco di controllo di accesso (ACL)
- Gruppi di sicurezza di rete
- Macchina virtuale di Azure basata su Windows

### Origine 1: computer client Desktop remoto

Per eliminare il computer come fonte di problemi o errori di configurazione, verificare che il computer sia in grado di stabilire connessioni Desktop remoto a un altro computer locale basato su Windows.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_1.png)

Se non è possibile, effettuare i seguenti controlli sul computer:

- Un'impostazione firewall locale che blocca il traffico di Desktop remoto
- Software proxy client installato localmente che impedisce le connessioni Desktop remoto
- Software di monitoraggio della rete installato localmente che impedisce le connessioni Desktop remoto
- Altri tipi di software di sicurezza che effettuano il monitoraggio del traffico o consentano/non consentano tipi di traffico che impediscano le connessioni Desktop remoto

In tutti questi casi, provare a disabilitare temporaneamente il software e tentare una connessione Desktop remoto a un computer locale per trovare la causa principale. Quindi, contattare l'amministratore di rete per correggere le impostazioni del software in modo da consentire le connessioni Desktop remoto.

### Origine 2: dispositivo periferico dell’Intranet dell’organizzazione

Per eliminare il dispositivo periferico dell’Intranet dell’organizzazione come fonte di problemi o errori di configurazione, verificare che un computer connesso direttamente a Internet sia in grado di effettuare connessioni Desktop remoto alla macchina virtuale di Azure.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_2.png)

Se non si dispone di un computer connesso direttamente a Internet, è possibile creare facilmente e usare una nuova macchina virtuale di Azure nel suo proprio gruppo di risorse o servizio cloud. Per ulteriori informazioni, vedere [Creare una macchina virtuale con Windows in Azure](virtual-machines-windows-tutorial.md). Dopo aver completato i test, eliminare il gruppo di risorse o la macchina virtuale e il servizio cloud.

Se è possibile creare una connessione Desktop remoto con un computer collegato direttamente a Internet, controllare se nel dispositivo periferico dell’Intranet dell’organizzazione si verificano le seguenti condizioni:

- Firewall interno che blocca le connessioni HTTPS a Internet
- Server proxy che impedisce le connessioni Desktop remoto
- Software di rilevamento delle intrusioni o software per il monitoraggio della rete in esecuzione sui dispositivi nella rete perimetrale che impedisce le connessioni Desktop remoto

Rivolgersi all'amministratore di rete per correggere le impostazioni del dispositivo periferico dell’Intranet dell’organizzazione per consentire connessioni Desktop remoto a Internet basate su HTTPS.

### Origine 3: endpoint del servizio cloud e ACL

Per eliminare l'endpoint del servizio cloud e l'elenco di controllo di accesso come fonti di problemi o errori di configurazione per le macchine virtuali create mediante l'API di Gestione dei servizi, verificare che un'altra macchina virtuale di Azure che si trova nello stesso servizio cloud o rete virtuale sia in grado di eseguire connessioni Desktop remoto alla macchina virtuale di Azure.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_3.png)

> [AZURE.NOTE]Per le macchine virtuali create in Gestione risorse, andare su [Origine 4: gruppi di sicurezza di rete](#nsgs).

Se non si dispone di un'altra macchina virtuale nello stesso servizio cloud o rete virtuale, è possibile crearne facilmente una nuova. Per ulteriori informazioni, vedere [Creare una macchina virtuale con Windows in Azure](virtual-machines-windows-tutorial.md). Dopo aver completato i test, eliminare la macchina virtuale aggiuntiva.

Se è possibile creare una connessione Desktop remoto con una macchina virtuale nello stesso servizio cloud o rete virtuale, verificare quanto indicato di seguito:

- La configurazione dell'endpoint per il traffico Desktop remoto nella macchina virtuale di destinazione. La porta TCP privata dell'endpoint deve corrispondere alla porta TCP su cui è in ascolto il servizio Servizi Desktop remoto nella macchina virtuale, che per impostazione predefinita è 3389.
- L’ACL per l'endpoint di traffico Desktop remoto sulla macchina virtuale di destinazione. Gli ACL consentono di specificare il traffico in ingresso da Internet consentito o negato in base al relativo indirizzo IP di origine. ACL configurati in modo errato possono impedire il traffico di Desktop remoto in ingresso all'endpoint. Esaminare gli ACL per assicurarsi che il traffico in ingresso dagli indirizzi IP pubblici del proxy o da altri server periferici sia consentito. Per altre informazioni, vedere [Che cos'è un elenco di controllo di accesso di rete (ACL)?](../virtual-network/virtual-networks-acl.md)

Per eliminare l'endpoint come fonte del problema, rimuovere l'endpoint corrente e creare un nuovo endpoint, scegliendo una porta casuale nell'intervallo tra 49152 e 65535 per il numero di porta esterna. Per altre informazioni, vedere la pagina [Come configurare gli endpoint a una macchina virtuale](virtual-machines-set-up-endpoints.md).

### <a id="nsgs"></a>Origine 4: gruppi di sicurezza di rete

I gruppi di sicurezza di rete consentono un controllo più granulare del traffico in entrata e in uscita consentito. È possibile creare regole che si estendono alle subnet e ai servizi cloud in una rete virtuale di Azure. Esaminare le regole del gruppo di sicurezza di rete per garantire che sia consentito il traffico di Desktop remoto da Internet.

Per altre informazioni, vedere [Che cos’è un Gruppo di sicurezza di rete (NSG)?](../virtual-network/virtual-networks-nsg.md).

### Origine 5: macchina virtuale di Azure basata su Windows

L’ultima serie di problemi possibili si verifica sulla macchina virtuale di Azure stessa.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_5.png)

L’articolo [risoluzione dei problemi di base di Desktop remoto](virtual-machines-troubleshoot-remote-desktop-connections.md) descrive come utilizzare il [pacchetto diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864). Se il pacchetto di diagnostica non è riuscito a risolvere il problema **connettività RDP a una macchina virtuale di Azure (riavvio necessario)**, seguire le istruzioni in [questo articolo](virtual-machines-windows-reset-password.md) per reimpostare il servizio Servizi Desktop remoto nella macchina virtuale. In tal modo, si verificheranno i seguenti eventi:

- Verrà abilitata la regola predefinita "Desktop remoto" di Windows Firewall (porta TCP 3389).
- Verranno abilitate le connessioni Desktop remoto impostando il valore del Registro di sistema HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections su 0.

Provare nuovamente la connessione dal computer. Se non riesce, alcuni dei problemi possibili sono:

- Il servizio Servizi Desktop remoto non è in esecuzione nella macchina virtuale di destinazione.
- Il servizio Servizi Desktop remoto non è in ascolto sulla porta TCP 3389.
- Windows Firewall o un altro firewall locale dispone di una regola in uscita che impedisce il traffico di Desktop remoto.
- Il software di rilevamento delle intrusioni o il software per il monitoraggio della rete in esecuzione nella macchina virtuale di Azure impedisce le connessioni Desktop remoto.

Per risolvere questi problemi per le macchine virtuali create mediante l'API di Gestione dei servizi, è possibile usare una sessione remota di Azure PowerShell nella macchina virtuale di Azure. In primo luogo, sarà necessario installare un certificato per il servizio cloud di hosting della macchina virtuale. Andare a [Configurare l’accesso remoto PowerShell protetto alle macchine virtuali di Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) e scaricare il file di script **InstallWinRMCertAzureVM.ps1** in una cartella nel computer locale.

Successivamente, installare Azure PowerShell, se non è stato già installato. Vedere [Come installare e configurare Azure PowerShell](../install-configure-powershell.md).

Successivamente, aprire un prompt dei comandi di Azure PowerShell e modificare la cartella corrente nel percorso del file di script **InstallWinRMCertAzureVM.ps1**. Per eseguire uno script di Azure PowerShell, è necessario impostare i criteri di esecuzione corretti. Eseguire il comando **Get-ExecutionPolicy** per determinare il livello di criterio corrente. Per informazioni sull'impostazione del livello appropriato, vedere [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Successivamente, immettere il nome della sottoscrizione di Azure, il nome del servizio cloud e il nome della macchina virtuale (rimuovendo i caratteri < and >), quindi eseguire questi comandi.

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

È possibile ottenere il nome della sottoscrizione corretto dalla proprietà _SubscriptionName_ della visualizzazione del comando **Get-AzureSubscription**. È possibile ottenere il nome del servizio cloud per la macchina virtuale dalla colonna _ServiceName_ della visualizzazione del comando **Get-AzureVM**.

Per dimostrare che si dispone di questo nuovo certificato, aprire uno snap-in Certificati incentrato sull'utente corrente e cercare nella cartella **Autorità di certificazione radice disponibile nell'elenco locale\\Certificati**. Verrà visualizzato un certificato con il nome DNS del servizio cloud nella colonna Rilasciato a (esempio: cloudservice4testing.cloudapp.net).

Successivamente, avviare una sessione remota di Azure PowerShell utilizzando questi comandi.

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

Dopo aver immesso le credenziali di amministratore valide, verrà visualizzato un prompt di Azure PowerShell simile al seguente:

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

La prima parte del prompt indica che vengono emessi i comandi Azure PowerShell per il servizio cloud che contiene la macchina virtuale di destinazione. Il nome del servizio cloud sarà diverso da "cloudservice4testing.cloudapp.net".

È ora possibile inviare comandi Azure PowerShell per analizzare i problemi aggiuntivi menzionati in precedenza e apportare correzioni alla configurazione.

### Per correggere manualmente Servizi Desktop remoto in ascolto sulla porta TCP

Se non è stato possibile eseguire il [pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) per il problema **Connettività RDP a una macchina virtuale di Azure (riavvio necessario)**, eseguire questo comando nel prompt della sessione remota di Azure PowerShell.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

La proprietà PortNumber indica il numero di porta corrente. Se necessario, modificare il numero di porta Desktop remoto nuovamente al valore predefinito (3389) utilizzando questo comando.

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Verificare che la porta sia stata modificata in 3389 utilizzando questo comando.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Chiudere la sessione remota di Azure PowerShell utilizzando questo comando.

	Exit-PSSession

Verificare che anche l'endpoint Desktop remoto per la macchina virtuale di Azure utilizzi la porta TCP 3398 come porta interna. Quindi, riavviare la macchina virtuale di Azure e provare nuovamente la connessione Desktop remoto.


## Risorse aggiuntive

[Pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Come reimpostare una password o il servizio Desktop remoto per le macchine virtuali di Windows](virtual-machines-windows-reset-password.md)

[Come installare e configurare Azure PowerShell](../install-configure-powershell.md)

[Risolvere i problemi relativi alle connessioni Secure Shell (SSH) a una macchina virtuale di Azure basata su Linux](virtual-machines-troubleshoot-ssh-connections.md)

[Risoluzione dei problemi di accesso a un'applicazione in esecuzione su una macchina virtuale di Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Oct15_HO4-->