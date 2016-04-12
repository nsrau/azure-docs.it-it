<properties
	pageTitle="Risoluzione dettagliata del Desktop remoto | Microsoft Azure"
	description="Passaggi della risoluzione dettagliata dei problemi per le connessioni RDP a una macchina virtuale di Azure che esegue Windows."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="dkshir"/>

# Risoluzione dettagliata dei problemi relativi a connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Questo articolo contiene una procedura dettagliata sulla risoluzione dei problemi per diagnosticare e risolvere errori di Desktop remoto complessi per le macchine virtuali di Azure basate su Windows.

> [AZURE.IMPORTANT] Per eliminare gli errori più comuni di Desktop remoto, verificare di aver letto l'articolo relativo alla [risoluzione dei problemi di base di Desktop remoto](virtual-machines-windows-troubleshoot-rdp-connection.md) prima di procedere.

Se viene visualizzato un messaggio di errore di Desktop remoto che non corrisponde a uno dei messaggi di errore specifici descritti nella [guida alla risoluzione dei problemi di base di Desktop remoto](virtual-machines-windows-troubleshoot-rdp-connection.md), seguire questa procedura e provare a capire perché il client Desktop remoto (o [RDP](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol)) non riesce a connettersi al servizio RDP nella VM di Azure.

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e overflow dello stack relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Andare al [sito di supporto di Azure](https://azure.microsoft.com/support/options/) e fare clic su **Ottenere supporto**. Per informazioni sull'uso del Supporto tecnico di Azure, leggere le [Domande frequenti sul supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).


## Componenti di una connessione Desktop remoto

Questi sono i componenti di una connessione RDP:

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_0.png)

Prima di procedere, può essere utile riflettere su cosa è stato modificato dall'ultima connessione Desktop remoto riuscita alla VM. Ad esempio:

- Se l'indirizzo IP pubblico della VM o il servizio cloud contenente la VM (detto anche indirizzo IP virtuale o [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) è cambiato, l'errore RDP potrebbe essere causato dal fatto che la cache del client DNS ha ancora l'*indirizzo IP precedente* registrato per il nome DNS. Svuotare la cache del client DNS e riprovare a connettersi alla VM. Oppure, provare a connettersi direttamente con il nuovo indirizzo VIP.
- Se si usa un'applicazione di terze parti per gestire le connessioni Desktop remoto, anziché i portali di Azure, verificare che la configurazione dell'applicazione includa la porta TCP corretta per il traffico di Desktop remoto. È possibile controllare questa porta per una macchina virtuale classica nel [portale di Azure](https://portal.azure.com) facendo clic sulle impostazioni della VM > Endpoint.


## Operazioni preliminari

Prima di procedere alla risoluzione dei problemi dettagliata

- Controllare lo stato della macchina virtuale nel portale di Azure classico o nel portale di Azure, per verificare che non siano presenti errori ovvi
- Seguire i [passaggi di correzione rapida per gli errori RDP comuni nella guida alla risoluzione dei problemi di base](virtual-machines-windows-troubleshoot-rdp-connection.md#quickfixrdp)


Provare a riconnettersi alla VM tramite Desktop remoto dopo aver eseguito questi passaggi.


## Risoluzione dettagliata dei problemi

Il client Desktop remoto potrebbe non essere in grado di raggiungere il servizio Desktop remoto nella VM di Azure a causa di problemi nelle origini seguenti:

- Computer client Desktop remoto
- Dispositivo periferico dell’Intranet dell’organizzazione
- Endpoint del servizio cloud ed elenco di controllo di accesso (ACL)
- Gruppi di sicurezza di rete
- Macchina virtuale di Azure basata su Windows

### Origine 1: computer client Desktop remoto

Verificare che il computer sia in grado di stabilire connessioni Desktop remoto a un altro computer locale basato su Windows.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_1.png)

Se non è possibile stabilire connessioni, eseguire i controlli seguenti sul computer:

- Un'impostazione firewall locale che blocca il traffico di Desktop remoto
- Software proxy client installato localmente che impedisce le connessioni Desktop remoto
- Software di monitoraggio della rete installato localmente che impedisce le connessioni Desktop remoto
- Altri tipi di software di sicurezza che effettuano il monitoraggio del traffico o consentano/non consentano tipi di traffico che impediscano le connessioni Desktop remoto

In tutti questi casi, disabilitare temporaneamente il software e provare a connettersi a un computer locale tramite Desktop remoto. Se si riesce a trovare la causa effettiva in questo modo, contattare l'amministratore di rete per correggere le impostazioni del software in modo da consentire le connessioni Desktop remoto.

### Origine 2: dispositivo periferico dell’Intranet dell’organizzazione

Verificare che un computer connesso direttamente a Internet possa eseguire connessioni Desktop remoto alla macchina virtuale di Azure.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_2.png)

Se non si ha un computer connesso direttamente a Internet, creare una nuova macchina virtuale di Azure in un gruppo di risorse o servizio cloud per eseguire un test. Per ulteriori informazioni, vedere [Creare una macchina virtuale con Windows in Azure](virtual-machines-windows-hero-tutorial.md). Dopo aver completato il test, eliminare la macchina virtuale e il gruppo di risorse o il servizio cloud.

Se è possibile creare una connessione Desktop remoto con un computer collegato direttamente a Internet, controllare se nel dispositivo periferico dell’Intranet dell’organizzazione si verificano le seguenti condizioni:

- Firewall interno che blocca le connessioni HTTPS a Internet
- Server proxy che impedisce le connessioni Desktop remoto
- Software di rilevamento delle intrusioni o software per il monitoraggio della rete in esecuzione sui dispositivi nella rete perimetrale che impedisce le connessioni Desktop remoto

Rivolgersi all'amministratore di rete per correggere le impostazioni del dispositivo periferico dell’Intranet dell’organizzazione per consentire connessioni Desktop remoto a Internet basate su HTTPS.

### Origine 3: endpoint del servizio cloud e ACL

Per le macchine virtuali create mediante il modello di distribuzione classico, verificare che un'altra macchina virtuale di Azure che si trova nello stesso servizio cloud o rete virtuale sia in grado di stabilire connessioni Desktop remoto alla macchina virtuale di Azure.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_3.png)

> [AZURE.NOTE] Per le macchine virtuali create in Gestione risorse, andare su [Origine 4: gruppi di sicurezza di rete](#nsgs).

Se non si ha un'altra macchina virtuale nello stesso servizio cloud o rete virtuale, crearne una nuova usando la procedura descritta in [Creare una macchina virtuale che esegue Windows in Azure](virtual-machines-windows-hero-tutorial.md). Eliminare la macchina virtuale aggiuntiva al completamento del test.

Se è possibile connettersi tramite Desktop remoto a una macchina virtuale nello stesso servizio cloud o rete virtuale, verificare quanto segue:

- La configurazione dell'endpoint per il traffico di Desktop remoto nella VM di destinazione: la porta TCP privata dell'endpoint deve corrispondere alla porta TCP in cui è in ascolto il servizio Desktop remoto della macchina virtuale (l'impostazione predefinita è 3389).
- L'ACL per l'endpoint del traffico di Desktop remoto sulla VM di destinazione: gli ACL consentono di specificare il traffico in ingresso da Internet consentito o negato in base al relativo indirizzo IP di origine. ACL configurati in modo errato possono impedire il traffico di Desktop remoto in ingresso all'endpoint. Verificare gli ACL per assicurarsi che il traffico in ingresso dagli indirizzi IP pubblici del proxy o da altri server periferici sia consentito. Per altre informazioni, vedere [Che cos'è un elenco di controllo di accesso di rete (ACL)?](../virtual-network/virtual-networks-acl.md)

Per controllare se l'endpoint è l'origine del problema, rimuovere l'endpoint corrente e creare un nuovo endpoint, scegliendo una porta casuale nell'intervallo tra 49152 e 65535 per il numero di porta esterna. Per altre informazioni, vedere la pagina [Come configurare gli endpoint a una macchina virtuale](virtual-machines-windows-classic-setup-endpoints.md).

### <a id="nsgs"></a>Origine 4: gruppi di sicurezza di rete

I gruppi di sicurezza di rete consentono un controllo più granulare del traffico in entrata e in uscita consentito. È possibile creare regole che si estendono alle subnet e ai servizi cloud in una rete virtuale di Azure. Verificare le regole del gruppo di sicurezza di rete per garantire che sia consentito il traffico di Desktop remoto da Internet.

Per altre informazioni, vedere [Che cos'è un Gruppo di sicurezza di rete (NSG)?](../virtual-network/virtual-networks-nsg.md).

### Origine 5: macchina virtuale di Azure basata su Windows

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_5.png)

Usare il [pacchetto di diagnostica di IaaS di Azure (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) per verificare se l'errore è dovuto alla macchina virtuale di Azure stessa. Se il pacchetto di diagnostica non riesce a risolvere il problema di **connettività RDP a una VM di Azure (riavvio necessario)**, seguire le istruzioni in [questo articolo](virtual-machines-windows-reset-rdp.md) per reimpostare il servizio Desktop remoto nella macchina virtuale. In tal modo, si verificheranno i seguenti eventi:

- Verrà abilitata la regola predefinita "Desktop remoto" di Windows Firewall (porta TCP 3389).
- Verranno abilitate le connessioni Desktop remoto impostando il valore del Registro di sistema HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections su 0.

Provare nuovamente la connessione dal computer. Se non si è ancora in grado di connettersi tramite Desktop remoto, verificare se sono presenti i problemi seguenti:

- Il servizio Desktop remoto non è in esecuzione nella VM di destinazione.
- Il servizio Desktop remoto non è in ascolto sulla porta TCP 3389.
- Windows Firewall o un altro firewall locale dispone di una regola in uscita che impedisce il traffico di Desktop remoto.
- Il software di rilevamento delle intrusioni o il software per il monitoraggio della rete in esecuzione nella macchina virtuale di Azure impedisce le connessioni Desktop remoto.

Per le VM create usando il modello di distribuzione classico, usare una sessione remota di Azure PowerShell per connettersi alla macchina virtuale di Azure. In primo luogo, sarà necessario installare un certificato per il servizio cloud di hosting della macchina virtuale. Passare alla pagina dello script per [configurare l'accesso remoto PowerShell protetto alle VM di Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) e scaricare il file di script **InstallWinRMCertAzureVM.ps1** nel computer locale.

Successivamente, installare Azure PowerShell, se non è stato già installato. Vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Successivamente, aprire un prompt dei comandi di Azure PowerShell e modificare la cartella corrente nel percorso del file di script **InstallWinRMCertAzureVM.ps1**. Per eseguire uno script di Azure PowerShell, è necessario impostare i criteri di esecuzione corretti. Eseguire il comando **Get-ExecutionPolicy** per determinare il livello di criterio corrente. Per informazioni sull'impostazione del livello appropriato, vedere [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Successivamente, immettere il nome della sottoscrizione di Azure, il nome del servizio cloud e il nome della macchina virtuale (rimuovendo i caratteri < and >), quindi eseguire questi comandi.

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

È possibile ottenere il nome della sottoscrizione corretto dalla proprietà _SubscriptionName_ della visualizzazione del comando **Get-AzureSubscription**. È possibile ottenere il nome del servizio cloud per la macchina virtuale dalla colonna _ServiceName_ della visualizzazione del comando **Get-AzureVM**.

Verificare di avere il nuovo certificato, aprire uno snap-in Certificati per l'utente corrente e cercare nella cartella **Autorità di certificazione radice attendibili\\Certificati**. Verrà visualizzato un certificato con il nome DNS del servizio cloud nella colonna Rilasciato a (esempio: cloudservice4testing.cloudapp.net).

Successivamente, avviare una sessione remota di Azure PowerShell utilizzando questi comandi.

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

Dopo aver immesso le credenziali di amministratore valide, verrà visualizzato un prompt di Azure PowerShell simile al seguente:

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

La prima parte di questo prompt è il nome del servizio cloud che contiene la VM di destinazione, che può essere diverso da "cloudservice4testing.cloudapp.net". È ora possibile inviare comandi di Azure PowerShell per questo servizio cloud, per analizzare i problemi aggiuntivi menzionati in precedenza e apportare correzioni alla configurazione.

### Per correggere manualmente Servizi Desktop remoto in ascolto sulla porta TCP

Se non è stato possibile eseguire il [pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) per il problema **Connettività RDP a una macchina virtuale di Azure (riavvio necessario)**, eseguire questo comando nel prompt della sessione remota di Azure PowerShell.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

La proprietà PortNumber indica il numero di porta corrente. Se necessario, modificare il numero di porta Desktop remoto nuovamente al valore predefinito (3389) utilizzando questo comando.

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Verificare che la porta sia stata modificata in 3389 utilizzando questo comando.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Chiudere la sessione remota di Azure PowerShell utilizzando questo comando.

	Exit-PSSession

Verificare che anche l'endpoint Desktop remoto per la VM di Azure usi la porta TCP 3398 come porta interna. Riavviare la VM di Azure e tentare nuovamente la connessione Desktop remoto.


## Risorse aggiuntive

[Pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Come reimpostare una password o il servizio Desktop remoto per le macchine virtuali di Windows](virtual-machines-windows-reset-rdp.md)

[Come installare e configurare Azure PowerShell](../powershell-install-configure.md)

[Risolvere i problemi relativi alle connessioni Secure Shell (SSH) a una macchina virtuale di Azure basata su Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Risoluzione dei problemi di accesso a un'applicazione in esecuzione su una macchina virtuale di Azure](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0323_2016-->