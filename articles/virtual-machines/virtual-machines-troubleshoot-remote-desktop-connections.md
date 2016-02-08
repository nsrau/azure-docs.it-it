<properties
	pageTitle="Risolvere i problemi di connessione a Desktop remoto a una VM di Azure | Microsoft Azure"
	description="Risolvere i problemi di errori di connessione a Desktop remoto per una VM Windows. Ottenere rapidamente procedure di prevenzione, istruzioni in base al messaggio di errore e informazioni dettagliate sulla risoluzione dei problemi di rete."
	keywords="Errore di desktop remoto, errore di connessione al desktop remoto, impossibile connettersi alla macchina virtuale, risoluzione dei problemi di desktop remoto"
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
	ms.date="01/25/2016"
	ms.author="dkshir"/>

# Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows

La mancata connessione di desktop remoto (RDP) per la macchina virtuale di Azure basata su Windows può dipendere da vari motivi. Il problema può dipendere dal servizio di desktop remoto nella VM, dalla connessione di rete o dal client di desktop remoto nel computer host. Questo articolo consente di individuarne le cause e correggerle.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Questo articolo si applica a macchine virtuali di Azure che eseguono Windows. Per macchine virtuali di Azure che eseguono Linux, vedere [Risolvere i problemi di connessione SSH a una macchina virtuale di Azure](virtual-machines-troubleshoot-ssh-connections.md).

Se è necessaria ulteriore assistenza in qualsiasi punto in questo articolo, è possibile contattare gli esperti di Azure su [MSDN Azure e i forum di overflow dello stack](https://azure.microsoft.com/support/forums/). In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Andare al [sito di supporto di Azure](https://azure.microsoft.com/support/options/) e fare clic su **Ottenere supporto**.


<a id="quickfixrdp"></a>
## Correggere gli errori comuni del desktop remoto

In questa sezione sono elencati alcuni passaggi per una correzione rapida dei comuni problemi di connessione del desktop remoto.

### Macchine virtuali create con il modello di distribuzione classico

Questi passaggi aiutano a risolvere la maggior parte degli errori di connessione del desktop remoto nelle macchine virtuali di Azure create con il modello di distribuzione classico. Dopo ogni passaggio, tentare la riconnessione alla VM.

- Reimpostare il servizio Desktop remoto dal [portale di Azure](https://portal.azure.com) per risolvere i problemi di avvio con il server RDP.<br> Fare clic su **Sfoglia** > **Macchine virtuali (classico)** > la macchina virtuale Windows > **Reimposta accesso...**.

- Riavviare la macchina virtuale per risolvere altri problemi di avvio.<br> Fare clic su **Sfoglia** > **Macchine virtuali (classico)** > la macchina virtuale Windows > **Riavvia**.

- Ridimensionare la macchina virtuale per risolvere eventuali problemi di host.<br> Fare clic su **Sfoglia** > **Macchine virtuali (classico)** > la macchina virtuale Windows > **Impostazioni** > **Dimensioni**. Per i passaggi dettagliati, vedere l'articolo relativo al [Ridimensionamento della macchina virtuale](https://msdn.microsoft.com/library/dn168976.aspx).

- Esaminare il log o la schermata della console della VM per correggere i problemi di avvio.<br> Fare clic su **Sfoglia** > **Macchine virtuali (classico**) > la macchina virtuale Windows > **Impostazioni** > **Diagnostica di avvio**.

- Controllare l'integrità delle risorse della VM per qualsiasi problema di piattaforma.<br> Fare clic su **Sfoglia** > **Macchine virtuali (classico)** > la macchina virtuale Windows > **Impostazioni** > **Controlla integrità**.

### Macchine virtuali create con il modello di distribuzione di Gestione risorse

Questi passaggi aiutano a risolvere la maggior parte degli errori di connessione del desktop remoto nelle macchine virtuali di Azure create con il modello di distribuzione di Gestione risorse. Dopo ogni passaggio, tentare la riconnessione alla VM.

- _Reimpostare l'accesso remoto_ con Powershell<br> a. Se necessario, [installare Azure PowerShell e connettersi alla sottoscrizione di Azure](../powershell-install-configure.md) con il metodo di Azure AD. Notare che non è necessario passare alla modalità di Gestione risorse nelle nuove versioni di Azure PowerShell 1.0.x.

	b. Reimpostare la connessione RDP utilizzando uno dei due comandi Azure PowerShell seguenti. Sostituire `myRG`, `myVM`, `myVMAccessExtension` e la posizione con i valori pertinenti alla propria configurazione.

	```
	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus
	```
	OPPURE<br>

  ```
  Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus
  ```

- Riavviare la macchina virtuale per risolvere altri problemi di avvio.<br> Fare clic su **Sfoglia** > **Macchine virtuali** > la macchina virtuale Windows > **Riavvia**.

- Ridimensionare la macchina virtuale per risolvere eventuali problemi di host.<br> Fare clic su **Sfoglia** > **Macchine virtuali** > la macchina virtuale Windows > **Impostazioni** > **Dimensioni**.

- Esaminare il log o la schermata della console della VM per correggere i problemi di avvio.<br> Fare clic su **Sfoglia** > **Macchine virtuali** > la macchina virtuale Windows > **Impostazioni** > **Diagnostica di avvio**.


Se i passaggi precedenti non risolvono gli errori di connessione del desktop remoto, andare alla sezione successiva.

## Risoluzione degli errori di connessione specifici di Desktop remoto

Di seguito sono descritti gli errori più comuni che è possibile visualizzare durante i tentativi di connessione desktop remoto alla macchina virtuale di Azure:

1. [Errore di connessione Desktop remoto: La sessione remota è stata disconnessa perché non sono disponibili server licenze di Desktop remoto per il rilascio della licenza](#rdplicense).

2. [Errore di connessione Desktop remoto: Impossibile trovare il computer "nome"](#rdpname).

3. [Errore di connessione Desktop remoto: Si è verificato un errore di autenticazione. Impossibile contattare l'autorità di sicurezza locale](#rdpauth).

4. [Errore di sicurezza di Windows: Le credenziali specificate non funzionano](#wincred).

5. [Errore di connessione Desktop remoto: Il computer non è in grado di connettersi al computer remoto](#rdpconnect).

<a id="rdplicense"></a>
### Errore di connessione Desktop remoto: La sessione remota è stata disconnessa perché non sono disponibili server licenze di Desktop remoto per il rilascio della licenza.

Causa: il periodo di prova di 120 giorni delle licenza per il ruolo Server Desktop remoto è scaduto ed è necessario installare le licenze.

Per risolvere il problema, salvare una copia locale del file RDP dal portale ed eseguire questo comando nel prompt dei comandi di Windows PowerShell per avviare la connessione. In tal modo verrà disabilitata la licenza solo per quella connessione.

		mstsc <File name>.RDP /admin

Se in realtà non sono necessarie più di due connessioni di desktop remoto contemporanee nella VM, è possibile usare Gestione server per rimuovere il ruolo server desktop remoto.

Vedere anche il post di blog [Errore della macchina virtuale di Azure VM con "Non sono disponibili server licenze Desktop remoto"](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx).

<a id="rdpname"></a>
### Errore di connessione Desktop remoto: Impossibile trovare il computer "nome".

Causa: il client Desktop remoto del computer non è stato in grado di risolvere il nome del computer nelle impostazioni del file RDP.

Possibili soluzioni:

- Se si usa una rete Intranet aziendale, assicurarsi che il computer abbia accesso al server proxy e sia in grado di inviare a quest'ultimo traffico HTTPS.
- Se si usa un file RDP archiviato localmente, provare a usare il file generato dal portale. Ciò consente di usare il nome DNS corretto per la macchina virtuale o il servizio cloud e la porta dell'endpoint della macchina virtuale. Di seguito viene riportato un esempio di file RDP generato dal portale:

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

La parte di indirizzo nel file RDP è costituita dal nome di dominio completo del servizio cloud che contiene la macchina virtuale (in questo esempio tailspin-azdatatier.cloudapp.net) e dalla porta TCP esterna dell'endpoint per il traffico di Desktop remoto (55919).

<a id="rdpauth"></a>
### Errore di connessione Desktop remoto: Si è verificato un errore di autenticazione. Impossibile contattare l'autorità di sicurezza locale.

Causa: la macchina virtuale di destinazione non è in grado di individuare l'autorità di sicurezza nella porzione di nome utente delle credenziali.

Quando il nome utente è nel formato *AutoritàSicurezza\NomeUtente* (esempio: CORP\\User1), la parte *AutoritàSicurezza* indica o il nome del computer della macchina virtuale (per l'autorità di protezione locale) o un nome di dominio di Active Directory.

Possibili soluzioni:

- Se l'account utente è locale nella macchina virtuale, verificare che il nome della macchina virtuale sia stato digitato correttamente.
- Se l'account è in un dominio di Active Directory, controllare l'ortografia del nome di dominio.
- Se è un account di dominio di Active Directory e il nome di dominio è stato digitato correttamente, verificare che in tale dominio sia disponibile un controller di dominio. Può trattarsi di un problema comune in una rete virtuale di Azure che contiene controller di dominio, in cui non è stato avviato un computer controller di dominio. Per risolvere il problema, è possibile usare un account amministratore locale anziché un account di dominio.

<a id="wincred"></a>
### Errore di sicurezza di Windows: Le credenziali specificate non funzionano.

Causa: la macchina virtuale di destinazione non ha potuto convalidare il nome e la password dell'account.

Un computer basato su Windows può convalidare le credenziali di un account locale o di un account di dominio.

- Per gli account locali, usare la sintassi *NomeComputer\NomeUtente* (ad esempio: SQL1\\Admin4798).
- Per gli account di dominio, usare la sintassi *NomeDominio\NomeUtente* (ad esempio: CONTOSO\\johndoe).

Se la VM è stata innalzata al livello di controller di dominio in una nuova foresta Active Directory, anche l'account amministratore locale con il quale è stato eseguito l'accesso viene convertito in un account equivalente con la stessa password nella nuova foresta e nel nuovo dominio. L'account locale viene quindi eliminato. Ad esempio, se è stato eseguito l'accesso con l'account locale DC1\\DCAdmin e la macchina virtuale è stata innalzata al livello di controller di dominio in una nuova foresta per il dominio corp.contoso.com, l'account locale DC1\\DCAdmin viene eliminato e viene creato un nuovo account di dominio (CORP\\DCAdmin) con la stessa password.

Assicurarsi che il nome dell'account sia un nome che possa essere verificato come account valido dalla macchina virtuale e che la password sia corretta.

Se è necessario modificare la password dell'account amministratore locale, vedere [Come reimpostare una password o il servizio Desktop remoto per le macchine virtuali Windows](virtual-machines-windows-reset-password.md).

<a id="rdpconnect"></a>
### Errore di connessione Desktop remoto: Il computer non è in grado di connettersi al computer remoto.

Causa: l'account usato per la connessione non dispone dei diritti di accesso Desktop remoto.

Ogni computer Windows dispone di un gruppo locale Utenti Desktop remoto, che contiene gli account e i gruppi che hanno il diritto di accedere in remoto. Anche i membri del gruppo Administrators locale dispongono dell'accesso, sebbene tali account non siano elencati nel gruppo locale Utenti Desktop remoto. Per le macchine appartenenti a un dominio, il gruppo Administrators locale contiene anche gli amministratori di dominio per il dominio.

Assicurarsi che l'account che si usa per la connessione disponga dei diritti di accesso a Desktop remoto. Per risolvere il problema, usare un account amministratore locale o di dominio per connettersi a Desktop remoto e usare lo snap-in Gestione computer (**Strumenti di sistema > Utenti e gruppi locali > Gruppi > Utenti Desktop remoto**) per aggiungere l'account desiderato al gruppo locale Utenti Desktop remoto.

## Risoluzione degli errori di desktop remoto generici

Se nessuno di questi errori si è verificato ed è ancora impossibile connettersi alla VM tramite Desktop remoto, leggere [la guida dettagliata sulla risoluzione dei problemi per Desktop remoto](virtual-machines-rdp-detailed-troubleshoot.md).


## Risorse aggiuntive

[Pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Come reimpostare una password o il servizio Desktop remoto per le macchine virtuali di Windows](virtual-machines-windows-reset-password.md)

[Come installare e configurare Azure PowerShell](../install-configure-powershell.md)

[Risolvere i problemi relativi alle connessioni Secure Shell (SSH) a una macchina virtuale di Azure basata su Linux](virtual-machines-troubleshoot-ssh-connections.md)

[Risoluzione dei problemi di accesso a un'applicazione in esecuzione su una macchina virtuale di Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=AcomDC_0128_2016-->