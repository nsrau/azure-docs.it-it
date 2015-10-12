<properties
	pageTitle="Risolvere i problemi di connessione a Desktop remoto a una VM Windows | Microsoft Azure"
	description="Risolvere i problemi di connessioni Desktop remoto o RDP a una macchina virtuale di Azure che esegue Windows."
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

# Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Questo articolo è dedicato alla risoluzione dei problemi su una macchina virtuale creata con il modello di distribuzione di gestione risorse o il modello di distribuzione classica.

Si possono verificare vari casi di connessioni Desktop remoto (RDP) non riuscite alla macchina virtuale di Azure che esegue Windows. Questo articolo consente di individuarne le cause e correggerle.

> [AZURE.NOTE]Questo articolo si applica solo a macchine virtuali di Azure che eseguono Windows. Per la risoluzione dei problemi di connessioni a macchine virtuali di Azure che eseguono Linux, vedere [questo articolo](virtual-machines-troubleshoot-ssh-connections.md).

## Contattare il supporto tecnico di Azure

Se è necessaria ulteriore assistenza in qualsiasi punto in questo articolo, è possibile contattare gli esperti di Azure su [MSDN Azure e i forum di overflow dello stack](http://azure.microsoft.com/support/forums/).

In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Andare al [sito di supporto di Azure](http://azure.microsoft.com/support/options/) e fare clic su **Ottieni supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Microsoft Azure](http://azure.microsoft.com/support/faq/).


## Passaggi di base

I passaggi di base possono consentire di risolvere la maggior parte degli errori di connessione Desktop remoto:

- Reimpostare il servizio Desktop remoto dal [portale di Azure](https://portal.azure.com). Fare clic su **Esplora tutto** > **Macchine virtuali (classiche)** > macchina virtuale Windows > **Reimposta accesso remoto**.

![Reimpostare l'accesso remoto](./media/virtual-machines-troubleshoot-remote-desktop-connections/Portal-RDP-Reset-Windows.png)

- [Riavviare la macchina virtuale](https://msdn.microsoft.com/library/azure/dn763934.aspx).

- [Ridimensionare la macchina virtuale](https://msdn.microsoft.com/library/dn168976.aspx).


## Eseguire il pacchetto di diagnostica Azure IaaS su Windows

Se si esegue la risoluzione dei problemi da un computer che esegue Windows 8, Windows 8.1, Windows Server 2012 o Windows Server 2012 R2, è possibile provare a eseguire il [pacchetto di diagnostica Azure IaaS (Windows)](http://support.microsoft.com/kb/2976864). Questo pacchetto può risolvere molti dei problemi comuni con Desktop remoto.

1.	Fare clic sul **pacchetto di diagnostica Microsoft Azure IaaS (Windows)** nella [pagina di supporto della diagnostica](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864). Fare clic su **Crea** per una nuova sessione di diagnostica. È possibile **condividere** questa sessione con un computer di destinazione diverso o **scaricarla** nel computer locale.
2.	**Eseguire** la sessione, **accettare** il contratto di licenza Microsoft e **avviare** lo strumento di diagnostica.
3.	Autenticare la sottoscrizione di Azure nella finestra popup e seguire le istruzioni.
4.	Nella pagina **Quali dei seguenti problemi si verificano con la macchina virtuale di Azure in uso?**, selezionare il problema **Connettività RDP a una macchina virtuale di Azure (riavvio richiesto)**.

Se non è stato possibile eseguire il pacchetto di diagnostica Azure IaaS o quest'ultimo non è stato utile, passare alla sezione successiva per risolvere il problema in base all'errore ottenuto dal client Desktop remoto.


## Errori di RDP comuni

Di seguito sono descritti gli errori più comuni che è possibile riscontrare durante i tentativi di connessione Desktop remoto alla macchina virtuale di Azure:

1. [Errore di connessione Desktop remoto: La sessione remota è stata disconnessa perché non sono disponibili server licenze di Desktop remoto per il rilascio della licenza](#rdplicense).

2. [Errore di connessione Desktop remoto: Impossibile trovare il computer "nome"](#rdpname).

3. [Errore di connessione Desktop remoto: Si è verificato un errore di autenticazione. Impossibile contattare l'autorità di sicurezza locale](#rdpauth).

4. [Errore di sicurezza di Windows: Le credenziali specificate non funzionano](#wincred).

5. [Errore di connessione Desktop remoto: Il computer non è in grado di connettersi al computer remoto](#rdpconnect).

<a id="rdplicense"></a>
### Errore di connessione Desktop remoto: La sessione remota è stata disconnessa perché non sono disponibili server licenze di Desktop remoto per il rilascio della licenza.

Causa: il periodo di prova di 120 giorni delle licenza per il ruolo Server Desktop remoto è scaduto ed è necessario installare le licenze.

Per risolvere il problema, salvare una copia locale del file RDP dal portale di Azure ed eseguire questo comando nel prompt dei comandi di Windows PowerShell per avviare la connessione.

		mstsc <File name>.RDP /admin

In tal modo verrà disabilitata la licenza solo per quella connessione.

Se in realtà non sono necessarie due connessioni Desktop remoto contemporanee alla macchina virtuale, è possibile usare Gestione server per rimuovere il ruolo Server Desktop remoto.

Vedere anche il post di blog [Errore della macchina virtuale di Azure VM con "Non sono disponibili server licenze Desktop remoto"](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx).

<a id="rdpname"></a>
### Errore di connessione Desktop remoto: Impossibile trovare il computer "nome".

Causa: il client Desktop remoto del computer non è stato in grado di risolvere il nome del computer nelle impostazioni del file RDP.

Possibili soluzioni:

- Se si usa una rete Intranet aziendale, assicurarsi che il computer abbia accesso al server proxy e sia in grado di inviare a quest'ultimo traffico HTTPS.
- Se si usa un file RDP archiviato localmente, provare a usare il file generato dal portale di Azure. Ciò consente di usare il nome DNS corretto per la macchina virtuale o il servizio cloud e la porta dell'endpoint della macchina virtuale. Di seguito viene riportato un esempio di file RDP generato dal portale di Azure:

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

La parte di indirizzo nel file RDP è costituita dal nome di dominio completo del servizio cloud che contiene la macchina virtuale (in questo esempio tailspin-azdatatier.cloudapp.net) e dalla porta TCP esterna dell'endpoint per il traffico di Desktop remoto (55919).

<a id="rdpauth"></a>
### Errore di connessione Desktop remoto: Si è verificato un errore di autenticazione. Impossibile contattare l'autorità di sicurezza locale.

Causa: la macchina virtuale di destinazione non è in grado di individuare l'autorità di sicurezza nella porzione di nome utente delle credenziali.

Quando il nome utente è nel formato *AutoritàSicurezza*\*NomeUtente* (esempio: CORP\\User1), la parte *AutoritàSicurezza* indica o il nome del computer della macchina virtuale (per l'autorità di protezione locale) o un nome di dominio di Active Directory.

Possibili soluzioni:

- Se l'account utente è locale nella macchina virtuale, verificare che il nome della macchina virtuale sia stato digitato correttamente.
- Se l'account è in un dominio di Active Directory, controllare l'ortografia del nome di dominio.
- Se è un account di dominio di Active Directory e il nome di dominio è stato digitato correttamente, verificare che in tale dominio sia disponibile un controller di dominio. Può trattarsi di un problema comune in una rete virtuale di Azure che contiene controller di dominio, in cui non è stato avviato un computer controller di dominio. Per risolvere il problema, è possibile usare un account amministratore locale anziché un account di dominio.

<a id="wincred"></a>
### Errore di sicurezza di Windows: Le credenziali specificate non funzionano.

Causa: la macchina virtuale di destinazione non ha potuto convalidare il nome e la password dell'account.

Un computer basato su Windows può convalidare le credenziali di un account locale o di un account di dominio.

- Per gli account locali, usare la sintassi *NomeComputer*\*NomeUtente* (ad esempio: SQL1\\Admin4798).
- Per gli account di dominio, usare la sintassi *NomeDominio*\*NomeUtente* (ad esempio: CONTOSO\\johndoe).

Se la macchina virtuale è stata innalzata al livello di controller di dominio in una nuova foresta di Active Directory, l'account amministratore locale al quale è stato eseguito l'accesso viene convertito in un account equivalente con la stessa password nella nuova foresta e nel nuovo dominio. L'account amministratore locale viene eliminato. Ad esempio, se è stato eseguito l'accesso con l'account amministratore locale DC1\\DCAdmin e la macchina virtuale è stata innalzata al livello di controller di dominio in una nuova foresta per il dominio corp.contoso.com, l'account locale DC1\\DCAdmin viene eliminato e viene creato un nuovo account di dominio (CORP\\DCAdmin) con la stessa password.

Assicurarsi che il nome dell'account sia un nome che possa essere verificato come account valido dalla macchina virtuale e che la password sia corretta.

Se è necessario modificare la password dell'account amministratore locale, vedere [Come reimpostare una password o il servizio Desktop remoto per le macchine virtuali Windows](virtual-machines-windows-reset-password.md).

<a id="rdpconnect"></a>
### Errore di connessione Desktop remoto: Il computer non è in grado di connettersi al computer remoto.

Causa: l'account usato per la connessione non dispone dei diritti di accesso Desktop remoto.

Ogni computer Windows dispone di un gruppo locale Utenti Desktop remoto, che contiene gli account e i gruppi che hanno il diritto di accedere in remoto. Anche i membri del gruppo Administrators locale dispongono dell'accesso, sebbene tali account non siano elencati nel gruppo locale Utenti Desktop remoto. Per le macchine appartenenti a un dominio, il gruppo Administrators locale contiene anche gli amministratori di dominio per il dominio.

Assicurarsi che l'account che si usa per la connessione disponga dei diritti di accesso a Desktop remoto. Per risolvere il problema, usare un account amministratore locale o di dominio per connettersi a Desktop remoto e usare lo snap-in Gestione computer (**Strumenti di sistema > Utenti e gruppi locali > Gruppi > Utenti Desktop remoto**) per aggiungere l'account desiderato al gruppo locale Utenti Desktop remoto.


## Risoluzione dettagliata dei problemi

Se nessuno di questi errori si è verificato ed è ancora impossibile connettersi alla VM tramite Desktop remoto, leggere [questo articolo](virtual-machines-rdp-detailed-troubleshoot.md) per individuare altre cause.


## Risorse aggiuntive

[Pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Come reimpostare una password o il servizio Desktop remoto per le macchine virtuali di Windows](virtual-machines-windows-reset-password.md)

[Come installare e configurare Azure PowerShell](../install-configure-powershell.md)

[Risolvere i problemi relativi alle connessioni Secure Shell (SSH) a una macchina virtuale di Azure basata su Linux](virtual-machines-troubleshoot-ssh-connections.md)

[Risoluzione dei problemi di accesso a un'applicazione in esecuzione su una macchina virtuale di Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Oct15_HO1-->