<properties
	pageTitle="Risolvere i problemi di connessione a Desktop remoto a una VM di Azure | Microsoft Azure"
	description="Risolvere i problemi di errori di connessione a Desktop remoto per una VM Windows. Ottenere rapidamente procedure di prevenzione, istruzioni in base al messaggio di errore e informazioni dettagliate sulla risoluzione dei problemi di rete."
	keywords="Errore di desktop remoto, errore di connessione al desktop remoto, impossibile connettersi alla VM, risoluzione dei problemi di desktop remoto, impossibile connettersi alla vm azure, connessione non possibile tra rdp e vm azure"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="support-article"
	ms.date="06/14/2016"
	ms.author="iainfou"/>

# Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows

La mancata connessione Remote Desktop Protocol (RDP) alla macchina virtuale di Azure basata su Windows può essere dovuta a vari motivi. Il problema può dipendere dal servizio di desktop remoto nella VM, dalla connessione di rete o dal client di desktop remoto nel computer host. Questo articolo illustra alcuni dei metodi più comuni per risolvere i problemi di connessione RDP. Se il problema non è elencato qui o se non è ancora possibile connettersi alla VM tramite RDP, leggere i [passaggi e concetti relativi alla risoluzione dettagliata dei problemi RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md).

Per ricevere assistenza in qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.

<a id="quickfixrdp"></a>

## Passaggi rapidi per la risoluzione dei problemi
Dopo ogni passaggio della procedura di risoluzione dei problemi, tentare la riconnessione alla VM:

1. Reimpostare l'accesso remoto tramite il portale di Azure o Azure PowerShell
2. Riavviare la VM
3. Distribuire di nuovo la VM
4. Controllare le regole per l'endpoint di Gruppo di sicurezza / Servizi cloud
5. Esaminare i log della console VM nel portale di Azure o in Azure PowerShell
6. Controllare l'integrità della risorsa VM nel portale di Azure
7. Reimpostare la password della VM

Continuare la lettura se sono necessarie procedure e spiegazioni più dettagliate sia per i modelli di distribuzione classica che di Resource Manager.


<a id="fix-common-remote-desktop-errors"></a>
## Risolvere i problemi delle VM create con il modello di distribuzione Resource Manager

Dopo ogni passaggio della procedura di risoluzione dei problemi, tentare la riconnessione alla VM.

> [AZURE.TIP] Se il pulsante 'Connetti' nel portale è disattivato e non si è connessi ad Azure tramite una connessione [Express Route](../expressroute/expressroute-introduction.md) o [VPN da sito a sito](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), per poter usare il protocollo RDP è necessario creare un indirizzo IP pubblico e assegnarlo alla VM. Altre informazioni sono disponibili in [Indirizzi IP pubblici in Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).

1. Reimpostare l'accesso remoto tramite PowerShell.
	- Se non è già stato fatto, [installare e configurare la versione più recente di Azure PowerShell](../powershell-install-configure.md).

	- Reimpostare la connessione RDP usando uno dei due comandi PowerShell seguenti. Sostituire `myRG`, `myVM`, `myVMAccessExtension` e la posizione con i valori pertinenti per la propria configurazione.

	```
	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
		-Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" `
		-Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" `
		-Location Westus
	```
	OPPURE

  	```
	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" `
		-VMName "myVM" -Name "myVMAccess" -Location Westus
	```

	> [AZURE.NOTE] Negli esempi precedenti `myVMAccessExtension` o `MyVMAccess` è un nome specificato per la nuova estensione da installare come parte del processo. Questo valore viene spesso impostato sul nome della VM. Se in precedenza è stato usato il VMAccessAgent, è possibile ottenere il nome dell'estensione esistente usando `Get-AzureRmVM -ResourceGroupName "myRG" -Name "myVM"` per controllare le proprietà della VM. Esaminare quindi la sezione dell’output relativa alle estensioni. Poiché in una VM può esistere un solo VMAccessAgent, è necessario aggiungere anche il parametro `-ForceReRun` quando si usa `Set-AzureRmVMExtension` per registrare di nuovo l'agente.

2. Riavviare la VM per risolvere altri problemi di avvio. Selezionare **Sfoglia** > **Macchine virtuali** > *VM personale* > **Riavvia**.

3. [Ridistribuire la VM su un nuovo nodo di Azure](virtual-machines-windows-redeploy-to-new-node.md).

	Al termine di questa operazione i dati temporanei del disco vanno persi e gli indirizzi IP dinamici associati alla macchina virtuale saranno aggiornati.
	
4. Verificare che le [regole del gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) consentano il traffico RDP (porta TCP 3389).

5. Esaminare il log o lo screenshot della console della VM per correggere i problemi di avvio. Selezionare **Sfoglia** > **Macchine virtuali** > *macchina virtuale Windows* > **Supporto e risoluzione dei problemi** > **Diagnostica di avvio**.

6. [Reimpostare la password della VM](virtual-machines-windows-reset-rdp.md).

Se continuano a verificarsi errori RDP, è possibile [aprire una richiesta di supporto](https://azure.microsoft.com/support/options/) o leggere i [passaggi e concetti relativi alla risoluzione dettagliata dei problemi RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## Risolvere i problemi delle VM create con il modello di distribuzione classica

Dopo ogni passaggio della procedura di risoluzione dei problemi, tentare la riconnessione alla VM.

1. Reimpostare il servizio Desktop remoto dal [portale di Azure](https://portal.azure.com). Selezionare **Sfoglia** > **Macchine virtuali (classico)** > *VM personale* > **Reimposta accesso remoto**.

2. Riavviare la VM per risolvere altri problemi di avvio. Selezionare **Sfoglia** > **Macchine virtuali (classico)** > *VM personale* > **Riavvia**.

3. [Ridistribuire la VM su un nuovo nodo di Azure](virtual-machines-windows-redeploy-to-new-node.md).

	Al termine di questa operazione i dati temporanei del disco andranno persi e gli indirizzi IP dinamici associati alla macchina virtuale saranno aggiornati.
	
4. Verificare che l'[endpoint di Servizi cloud consenta il traffico RDP](../cloud-services/cloud-services-role-enable-remote-desktop.md).

5. Esaminare il log o lo screenshot della console della VM per correggere i problemi di avvio. Selezionare **Sfoglia** > **Macchine virtuali (classico**) > *VM personale* > **Impostazioni** > **Diagnostica di avvio**.

6. Controllare l'integrità delle risorse della VM per eventuali problemi di piattaforma. Selezionare **Sfoglia** > **Macchine virtuali (classico)** > *VM personale* > **Impostazioni** > **Controlla integrità**.

7. [Reimpostare la password della VM](virtual-machines-windows-reset-rdp.md).

Se continuano a verificarsi errori RDP, è possibile [aprire una richiesta di supporto](https://azure.microsoft.com/support/options/) o leggere i [passaggi e concetti relativi alla risoluzione dettagliata dei problemi RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## Risoluzione degli errori di connessione specifici di Desktop remoto

Quando si tenta di connettersi alla VM tramite RDP, potrebbe essere visualizzato un errore specifico. Di seguito sono riportati i messaggi di errore più comuni:

- [La sessione remota è stata disconnessa perché non sono disponibili server licenze di Desktop remoto per il rilascio della licenza.](#rdplicense).

- [Desktop remoto: impossibile rilevare il "nome" del computer](#rdpname).

- [Si è verificato un errore di autenticazione. Impossibile contattare l'autorità di sicurezza locale](#rdpauth).

- [Errore di sicurezza di Windows: Le credenziali specificate non funzionano](#wincred).

- [Il computer non è in grado di connettersi al computer remoto](#rdpconnect).

<a id="rdplicense"></a>
### La sessione remota è stata disconnessa perché non sono disponibili server licenze di Desktop remoto per il rilascio della licenza.

Causa: il periodo di prova di 120 giorni delle licenza per il ruolo Server Desktop remoto è scaduto ed è necessario installare le licenze.

Per risolvere il problema, salvare una copia locale del file RDP dal portale e al prompt dei comandi di PowerShell eseguire questo comando per avviare la connessione. Viene così disabilitata la licenza solo per quella connessione:

		mstsc <File name>.RDP /admin

Se in realtà non sono necessarie più di due connessioni di desktop remoto contemporanee nella VM, è possibile usare Gestione server per rimuovere il ruolo server desktop remoto.

Per altre informazioni, vedere il post di blog relativo all'[errore della VM di Azure "Non sono disponibili server licenze di Desktop remoto"](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx).

<a id="rdpname"></a>
### Desktop remoto: impossibile rilevare il "nome" del computer.

Causa: il client Desktop remoto del computer non è in grado di risolvere il nome del computer nelle impostazioni del file RDP.

Possibili soluzioni:

- Se si usa una rete Intranet aziendale, assicurarsi che il computer abbia accesso al server proxy e sia in grado di inviare a quest'ultimo traffico HTTPS.

- Se si usa un file RDP archiviato localmente, provare a usare il file generato dal portale. Ciò consente di essere certi di usare il nome DNS corretto per la macchina virtuale o il servizio cloud e la porta dell'endpoint della VM. Di seguito viene riportato un esempio di file RDP generato dal portale:

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

La parte dell'indirizzo del file RDP contiene:
- Il nome di dominio completo del servizio cloud contenente la macchina virtuale ("tailspin-azdatatier.cloudapp.net" in questo esempio).

- La porta TCP esterna dell'endpoint per il traffico di Desktop remoto (55919).

<a id="rdpauth"></a>
### Si è verificato un errore di autenticazione. Impossibile contattare l'autorità di sicurezza locale.

Causa: la macchina virtuale di destinazione non è in grado di individuare l'autorità di sicurezza nella porzione di nome utente delle credenziali.

Quando il nome utente è nel formato *AutoritàSicurezza*\*NomeUtente* (esempio: CORP\\Utente1), la parte *AutoritàSicurezza* indica o il nome del computer della VM (per l'autorità di protezione locale) o un nome di dominio di Active Directory.

Possibili soluzioni:

- Se l'account è locale nella macchina virtuale, verificare che il nome della macchina virtuale sia stato digitato correttamente.

- Se l'account è in un dominio di Active Directory, controllare l'ortografia del nome di dominio.

- Se è un account di dominio di Active Directory e il nome di dominio è stato digitato correttamente, verificare che in tale dominio sia disponibile un controller di dominio. Nelle reti virtuali di Azure che contengono controller di dominio, è un problema comune che un controller di dominio non sia disponibile perché non è stato avviato. Per risolvere il problema, è possibile usare un account amministratore locale anziché un account di dominio.

<a id="wincred"></a>
### Errore di sicurezza di Windows: Le credenziali specificate non funzionano.

Causa: la macchina virtuale di destinazione non può convalidare il nome e la password dell'account.

Un computer basato su Windows può convalidare le credenziali di un account locale o di un account di dominio.

- Per gli account locali, usare la sintassi *NomeComputer*\*NomeUtente* (ad esempio: SQL1\\Admin4798).
- Per gli account di dominio usare la sintassi *NomeDominio*\*NomeUtente* (ad esempio: CONTOSO\\peterodman).

Se la VM è stata innalzata al livello di controller di dominio in una nuova foresta Active Directory, l'account amministratore locale con il quale è stato eseguito l'accesso viene convertito in un account equivalente con la stessa password nella nuova foresta e nel nuovo dominio. L'account locale viene quindi eliminato.

Ad esempio, se è stato eseguito l'accesso con l'account locale DC1\\DCAdmin e la macchina virtuale è stata innalzata al livello di controller di dominio in una nuova foresta per il dominio corp.contoso.com, l'account locale DC1\\DCAdmin viene eliminato e viene creato un nuovo account di dominio (CORP\\DCAdmin) con la stessa password.

Assicurarsi che il nome dell'account sia un nome che possa essere verificato come account valido dalla macchina virtuale e che la password sia corretta.

Se è necessario modificare la password dell'account amministratore locale, vedere [Come reimpostare una password o il servizio Desktop remoto per le macchine virtuali Windows](virtual-machines-windows-reset-rdp.md).

<a id="rdpconnect"></a>
### Il computer non è in grado di connettersi al computer remoto.

Causa: l'account usato per la connessione non dispone dei diritti di accesso Desktop remoto.

Ogni computer Windows dispone di un gruppo locale Utenti desktop remoto, che contiene gli account e i gruppi che hanno il diritto di accedere in remoto. Anche i membri del gruppo Administrators locale dispongono dell'accesso, sebbene tali account non siano elencati nel gruppo locale Utenti Desktop remoto. Per le macchine appartenenti a un dominio, il gruppo Administrators locale contiene anche gli amministratori di dominio per il dominio.

Assicurarsi che l'account che si usa per la connessione disponga dei diritti di accesso a Desktop remoto. Come soluzione alternativa, usare un account amministratore locale o di dominio per connettersi tramite Desktop remoto. Usare quindi lo snap-in Microsoft Management Console (**Utilità di sistema > Utenti e gruppi locali > Gruppi > Utenti desktop remoto**) per aggiungere l'account desiderato al gruppo locale Utenti desktop remoto.

## Risolvere errori di Desktop remoto generici

Se non si è verificato alcuno di questi errori e non è ancora possibile connettersi alla VM tramite Desktop remoto, leggere [la guida dettagliata alla risoluzione dei problemi per Desktop remoto](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## Risorse aggiuntive

[Pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Come reimpostare una password o il servizio Desktop remoto per le macchine virtuali di Windows](virtual-machines-windows-reset-rdp.md)

[Come installare e configurare Azure PowerShell](../powershell-install-configure.md)

[Risolvere i problemi relativi alle connessioni Secure Shell a una macchina virtuale di Azure basata su Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Risoluzione dei problemi di accesso a un'applicazione in esecuzione su una macchina virtuale di Azure](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0810_2016-->