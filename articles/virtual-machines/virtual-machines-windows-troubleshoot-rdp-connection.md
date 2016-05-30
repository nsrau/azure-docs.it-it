<properties
	pageTitle="Risolvere i problemi di connessione a Desktop remoto a una VM di Azure | Microsoft Azure"
	description="Risolvere i problemi di errori di connessione a Desktop remoto per una VM Windows. Ottenere rapidamente procedure di prevenzione, istruzioni in base al messaggio di errore e informazioni dettagliate sulla risoluzione dei problemi di rete."
	keywords="Errore di desktop remoto, errore di connessione al desktop remoto, impossibile connettersi alla macchina virtuale, risoluzione dei problemi di desktop remoto"
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
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows

La mancata connessione Remote Desktop Protocol (RDP) alla macchina virtuale di Azure basata su Windows può essere dovuta a vari motivi. Il problema può dipendere dal servizio di desktop remoto nella VM, dalla connessione di rete o dal client di desktop remoto nel computer host. Questo articolo consente di individuarne le cause e correggerle.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Questo articolo si applica a macchine virtuali di Azure che eseguono Windows. Per le macchine virtuali di Azure che eseguono Linux, vedere [Risolvere i problemi relativi alle connessioni Secure Shell a una macchina virtuale di Azure basata su Linux](virtual-machines-linux-troubleshoot-ssh-connection.md).

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.

<a id="quickfixrdp"></a>

## Correggere gli errori comuni del desktop remoto

Questa sezione elenca le correzioni rapide dei comuni problemi di connessione Desktop remoto.

### Risolvere i problemi delle macchine virtuali create con il modello di distribuzione classica

Questi passaggi aiutano a risolvere la maggior parte degli errori di connessione Desktop remoto nelle macchine virtuali di Azure create con il modello di distribuzione classica. Dopo ogni passaggio, tentare la riconnessione alla VM.

- Reimpostare il servizio Desktop remoto dal [portale di Azure](https://portal.azure.com) per risolvere i problemi di avvio con il server RDP. Selezionare **Sfoglia** > **Macchine virtuali (classico)** > *macchina virtuale Windows* > **Reimposta accesso...**.

- Riavviare la macchina virtuale per risolvere altri problemi di avvio. Selezionare **Sfoglia** > **Macchine virtuali (classico)** > *macchina virtuale Windows* > **Riavvia**.

- Ridistribuire la macchina virtuale su un nuovo nodo di Azure. Vedere [Ridistribuzione della macchina virtuale su un nuovo nodo di Azure](virtual-machines-windows-redeploy-to-new-node.md).

	Si noti che al termine di questa operazione i dati temporanei del disco andranno persi e gli indirizzi IP dinamici associati alla macchina virtuale saranno aggiornati.

- Esaminare il log o la cattura di schermata della console della macchina virtuale per correggere i problemi di avvio. Selezionare **Sfoglia** > **Macchine virtuali (classico)** > *macchina virtuale Windows* > **Impostazioni** > **Diagnostica di avvio**.


- Controllare l'integrità delle risorse della VM per eventuali problemi di piattaforma. Selezionare **Sfoglia** > **Macchine virtuali (classico)** > *macchina virtuale Windows* > **Impostazioni** > **Controlla integrità**.


### Risolvere i problemi delle macchine virtuali create con il modello di distribuzione Resource Manager

Questi passaggi aiutano a risolvere la maggior parte degli errori di connessione Desktop remoto nelle macchine virtuali di Azure create con il modello di distribuzione Resource Manager. Dopo ogni passaggio, tentare la riconnessione alla VM.

> [AZURE.TIP] Se il pulsante "Connetti" nel portale è disattivato e non si è connessi ad Azure tramite una connessione [ExpressRoute](../expressroute/expressroute-introduction.md) o [VPN da sito a sito](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), per poter usare il protocollo RDP è necessario creare un indirizzo IP pubblico e assegnarlo alla VM. Altre informazioni sono disponibili in [Indirizzi IP pubblici in Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).

- Reimpostare l'accesso remoto tramite PowerShell.
	- Se necessario, [installare PowerShell e connettersi alla sottoscrizione di Azure](../powershell-install-configure.md) con il metodo di Azure Active Directory. Si noti che non è necessario passare alla modalità Resource Manager nelle versioni di PowerShell 1.0.x.

	- Reimpostare la connessione RDP usando uno dei due comandi PowerShell seguenti. Sostituire `myRG`, `myVM`, `myVMAccessExtension` e la posizione con i valori pertinenti alla propria configurazione.

	```
	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus
	```
	OPPURE

  	```
	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus
	```

	> [AZURE.NOTE] Se è la prima volta che si reimposta la connessione RDP, probabilmente il VMAccessAgent non esiste ancora. Negli esempi precedenti, `myVMAccessExtension` o `MyVMAccess` è un nome specificato per la nuova estensione che verrà installata come parte del processo. Spesso questo valore è semplicemente impostato sul nome della macchina virtuale. Se in precedenza è stato usato il VMAccessAgent, è possibile ottenere il nome dell'estensione esistente tramite `Get-AzureRmVM -ResourceGroupName "myRG" -Name "myVM"` per controllare le proprietà della macchina virtuale. Esaminare quindi la sezione dell’output relativa alle estensioni. Poiché in una macchina virtuale può esistere un solo VMAccessAgent, è necessario aggiungere anche il parametro `-ForceReRun` quando si usa `Set-AzureRmVMExtension.`. In questo modo si forza la ripetizione della registrazione dell’agente.

- Riavviare la macchina virtuale per risolvere altri problemi di avvio. Selezionare **Sfoglia** > **Macchine virtuali** > *macchina virtuale Windows* > **Riavvia**.

- Ridimensionare la macchina virtuale per risolvere eventuali problemi di host. Selezionare **Sfoglia** > **Macchine virtuali** > *macchina virtuale Windows* > **Impostazioni** > **Dimensioni**.

- Esaminare il log o la schermata della console della VM per correggere i problemi di avvio. Selezionare **Sfoglia** > **Macchine virtuali** > *macchina virtuale Windows* > **Impostazioni** > **Diagnostica di avvio**.


Se i passaggi precedenti non hanno risolto gli errori di connessione Desktop remoto, andare alla sezione successiva.

## Risoluzione degli errori di connessione specifici di Desktop remoto

Di seguito sono descritti gli errori più comuni visualizzati quando si tenta di usare Desktop remoto per la connessione alla macchina virtuale di Azure:

- [Errore di connessione Desktop remoto: La sessione remota è stata disconnessa perché non sono disponibili server licenze di Desktop remoto per il rilascio della licenza](#rdplicense).

- [Errore di connessione Desktop remoto: Impossibile trovare il computer "nome"](#rdpname).

- [Errore di connessione Desktop remoto: Si è verificato un errore di autenticazione. Impossibile contattare l'autorità di sicurezza locale](#rdpauth).

- [Errore di sicurezza di Windows: Le credenziali specificate non funzionano](#wincred).

- [Errore di connessione Desktop remoto: Il computer non è in grado di connettersi al computer remoto](#rdpconnect).

<a id="rdplicense"></a>
### Errore di connessione Desktop remoto: La sessione remota è stata disconnessa perché non sono disponibili server licenze di Desktop remoto per il rilascio della licenza.

Causa: il periodo di prova di 120 giorni delle licenza per il ruolo Server Desktop remoto è scaduto ed è necessario installare le licenze.

Per risolvere il problema, salvare una copia locale del file RDP dal portale e al prompt dei comandi di PowerShell eseguire questo comando per avviare la connessione. In tal modo verrà disabilitata la licenza solo per quella connessione:

		mstsc <File name>.RDP /admin

Se in realtà non sono necessarie più di due connessioni di desktop remoto contemporanee nella VM, è possibile usare Gestione server per rimuovere il ruolo server desktop remoto.

Per altre informazioni, vedere il post di blog relativo all’[errore della macchina virtuale di Azure con "Non sono disponibili server licenze di Desktop remoto"](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx).

<a id="rdpname"></a>
### Errore di connessione Desktop remoto: Impossibile trovare il computer "nome".

Causa: il client Desktop remoto del computer non è in grado di risolvere il nome del computer nelle impostazioni del file RDP.

Possibili soluzioni:

- Se si usa una rete Intranet aziendale, assicurarsi che il computer abbia accesso al server proxy e sia in grado di inviare a quest'ultimo traffico HTTPS.

- Se si usa un file RDP archiviato localmente, provare a usare il file generato dal portale. Ciò consente di usare il nome DNS corretto per la macchina virtuale o il servizio cloud e la porta dell'endpoint della macchina virtuale. Di seguito viene riportato un esempio di file RDP generato dal portale:

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

La parte dell'indirizzo del file RDP contiene:
- Il nome di dominio completo del servizio cloud contenente la macchina virtuale ("tailspin-azdatatier.cloudapp.net" in questo esempio).

- La porta TCP esterna dell'endpoint per il traffico di Desktop remoto (55919).

<a id="rdpauth"></a>
### Errore di connessione Desktop remoto: Si è verificato un errore di autenticazione. Impossibile contattare l'autorità di sicurezza locale.

Causa: la macchina virtuale di destinazione non è in grado di individuare l'autorità di sicurezza nella porzione di nome utente delle credenziali.

Quando il nome utente è nel formato *AutoritàSicurezza*\*NomeUtente* (esempio: CORP\\User1), la parte *AutoritàSicurezza* indica o il nome del computer della macchina virtuale (per l'autorità di protezione locale) o un nome di dominio di Active Directory.

Possibili soluzioni:

- Se l'account è locale nella macchina virtuale, verificare che il nome della macchina virtuale sia stato digitato correttamente.

- Se l'account è in un dominio di Active Directory, controllare l'ortografia del nome di dominio.

- Se è un account di dominio di Active Directory e il nome di dominio è stato digitato correttamente, verificare che in tale dominio sia disponibile un controller di dominio. Nelle reti virtuali di Azure che contengono controller di dominio, è un problema comune che un controller di dominio non sia disponibile perché non è stato avviato. Per risolvere il problema, è possibile usare un account amministratore locale anziché un account di dominio.

<a id="wincred"></a>
### Errore di sicurezza di Windows: Le credenziali specificate non funzionano.

Causa: la macchina virtuale di destinazione non può convalidare il nome e la password dell'account.

Un computer basato su Windows può convalidare le credenziali di un account locale o di un account di dominio.

- Per gli account locali, usare la sintassi *NomeComputer*\*NomeUtente* (ad esempio: SQL1\\Admin4798).
- Per gli account di dominio, usare la sintassi *NomeDominio*\*NomeUtente* (ad esempio: CONTOSO\\peterodman).

Se la VM è stata innalzata al livello di controller di dominio in una nuova foresta Active Directory, l'account amministratore locale con il quale è stato eseguito l'accesso viene convertito in un account equivalente con la stessa password nella nuova foresta e nel nuovo dominio. L'account locale viene quindi eliminato.

Ad esempio, se è stato eseguito l'accesso con l'account locale DC1\\DCAdmin e la macchina virtuale è stata innalzata al livello di controller di dominio in una nuova foresta per il dominio corp.contoso.com, l'account locale DC1\\DCAdmin viene eliminato e viene creato un nuovo account di dominio (CORP\\DCAdmin) con la stessa password.

Assicurarsi che il nome dell'account sia un nome che possa essere verificato come account valido dalla macchina virtuale e che la password sia corretta.

Se è necessario modificare la password dell'account amministratore locale, vedere [Come reimpostare una password o il servizio Desktop remoto per le macchine virtuali Windows](virtual-machines-windows-reset-rdp.md).

<a id="rdpconnect"></a>
### Errore di connessione Desktop remoto: Il computer non è in grado di connettersi al computer remoto.

Causa: l'account usato per la connessione non dispone dei diritti di accesso Desktop remoto.

Ogni computer Windows dispone di un gruppo locale Utenti desktop remoto, che contiene gli account e i gruppi che hanno il diritto di accedere in remoto. Anche i membri del gruppo Administrators locale dispongono dell'accesso, sebbene tali account non siano elencati nel gruppo locale Utenti Desktop remoto. Per le macchine appartenenti a un dominio, il gruppo Administrators locale contiene anche gli amministratori di dominio per il dominio.

Assicurarsi che l'account che si usa per la connessione disponga dei diritti di accesso a Desktop remoto. Come soluzione alternativa, usare un account amministratore locale o di dominio per connettersi tramite Desktop remoto. Usare quindi lo snap-in Microsoft Management Console (**Utilità di sistema > Utenti e gruppi locali > Gruppi > Utenti desktop remoto**) per aggiungere l'account desiderato al gruppo locale Utenti desktop remoto.

## Risolvere errori di Desktop remoto generici

Se non si è verificato nessuno di questi errori ed è ancora impossibile connettersi alla VM tramite Desktop remoto, leggere [la guida dettagliata alla risoluzione dei problemi per Desktop remoto](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## Risorse aggiuntive

[Pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Come reimpostare una password o il servizio Desktop remoto per le macchine virtuali di Windows](virtual-machines-windows-reset-rdp.md)

[Come installare e configurare Azure PowerShell](../powershell-install-configure.md)

[Risolvere i problemi relativi alle connessioni Secure Shell a una macchina virtuale di Azure basata su Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Risoluzione dei problemi di accesso a un'applicazione in esecuzione su una macchina virtuale di Azure](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0518_2016-->