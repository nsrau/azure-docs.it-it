<properties
	pageTitle="Come installare e configurare Azure PowerShell"
	description="Informazioni su come installare e configurare Azure PowerShell."
	editor="tysonn"
	manager="stevenka"
	documentationCenter=""
	services=""
	authors="coreyp69"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="coreyp"/>

# Come installare e configurare Azure PowerShell#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Cross-Platform CLI">Interfaccia della riga di comando multipiattaforma</a></div>

È possibile usare Windows PowerShell per eseguire numerose attività in Azure, sia in modo interattivo al prompt dei comandi che in modo automatico tramite script. Azure PowerShell è un modulo che offre i cmdlet per gestire Azure tramite Windows PowerShell. È possibile usare i cmdlet per creare, testare, distribuire e gestire soluzioni e servizi offerti tramite la piattaforma Azure. Nella maggior parte dei casi è possibile usarli per eseguire le stesse attività eseguibili tramite il portale di gestione di Azure. È ad esempio possibile creare e configurare servizi cloud, macchine virtuali, reti virtuali e app Web. 

Il modulo viene distribuito come file scaricabile. Il codice sorgente viene gestito tramite un archivio disponibile pubblicamente. Nelle istruzioni per l'installazione più avanti in questo argomento è incluso un collegamenti ai file scaricabili. Per informazioni sul codice sorgente, vedere l'articolo sul [repository del codice di Azure PowerShell](https://github.com/Azure/azure-powershell).

In questa guida vengono fornite informazioni di base sull'installazione e la configurazione di Azure PowerShell per la gestione della piattaforma Azure.

### <a id="Prereq"></a>Prerequisiti per l'uso di Azure PowerShell

Azure è una piattaforma basata su sottoscrizione. Ciò significa che per poter usare la piattaforma è necessaria una sottoscrizione. Nella maggior parte dei casi significa inoltre che i cmdlet necessitano delle informazioni della sottoscrizione per eseguire le attività correlate alla sottoscrizione. Queste informazioni non sono invece necessarie per alcuni dei cmdlet correlati all'archiviazione. Per fornire tali informazioni, è necessario configurare il computer in modo che si connetta alla sottoscrizione, come descritto più avanti nell'articolo in "Procedura: Eseguire la connessione alla sottoscrizione".

> [AZURE.NOTE] A partire dalla versione 0.8.5, per i moduli Azure PowerShell è necessario disporre di Microsoft .NET Framework 4.5.

Quando si installa il modulo, il programma di installazione verifica se nel sistema è disponibile il software necessario e installa tutte le dipendenze, tra cui la versione corretta di Windows PowerShell e .NET Framework.

<h2> <a id="Install"></a>Procedura: Installare Azure PowerShell</h2>

Per scaricare e installare i moduli Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=320376). Quando richiesto, fare clic su **Esegui**. Con l'Installazione guidata piattaforma Web vengono installati tutti i moduli e tutte le dipendenze di Azure PowerShell. Per completare l'installazione, attenersi alle istruzioni visualizzate.

> [AZURE.NOTE] Se si vuole solo scaricare il programma di installazione di PowerShell, visitare https://github.com/Azure/azure-powershell/releases.
In questo repository è anche disponibile il codice per i cmdlet di PowerShell

Per altre informazioni sugli strumenti da riga di comando disponibili per Azure, vedere la sezione relativa agli [strumenti da riga di comando]( http://go.microsoft.com/fwlink/?LinkId=320796).

Con il modulo viene installata anche una console personalizzata per Azure PowerShell. È possibile eseguire i cmdlet dalla console standard di Windows PowerShell o da quella di Azure PowerShell.

Il metodo usato per aprire l'una o l'altra console varia a seconda della versione di Windows in esecuzione:

- In un computer che esegue almeno Windows 8 o Windows Server 2012 è possibile usare la funzionalità di ricerca integrata. Nella schermata Start iniziare a digitare **power**. Viene visualizzato un elenco specifico di app che include Windows PowerShell e Azure PowerShell. Per aprire la console, fare clic su una delle app. Per aggiungere l'app alla schermata Start, fare clic con il pulsante destro del mouse sull'icona.

- In un computer che esegue una versione di Windows precedente a Windows 8 o Windows Server 2012 è possibile usare il menu Start. Dal menu Start fare clic su **Tutti i programmi**, scegliere **Azure** e quindi **Azure PowerShell**.

<h2><a id="Connect"></a>Procedura: Eseguire la connessione alla sottoscrizione</h2>

Per usare Azure, è necessaria una sottoscrizione. Se non si dispone di una sottoscrizione, vedere [Introduzione ad Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

Per la gestione dei servizi da parte dei cmdlet, è necessario disporre di una sottoscrizione. Per fornire a Windows PowerShell le informazioni relative alla propria sottoscrizione, sono disponibili due metodi. È possibile usare un certificato di gestione contenente tali informazioni oppure accedere a Azure usando l'account Microsoft o un account aziendale o dell'istituto di istruzione. Una volta eseguito l'accesso, Azure Active Directory (Azure AD) esegue l'autenticazione delle credenziali e restituisce un token di accesso che consente ad Azure PowerShell di gestire l'account.

Per scegliere facilmente il metodo di autenticazione più adatto alle proprie esigenze, tenere presente quanto segue:

- Azure AD è il metodo di autenticazione consigliato in quanto semplifica la gestione dell'accesso a una sottoscrizione. Con l'aggiornamento della versione 0.8.6, rende possibile lo scenario di automazione con l'autenticazione di Azure AD anche se viene usato un account aziendale o dell'istituto di istruzione e può essere usato anche con l'API di Gestione risorse di Azure.
- Quando si usa il metodo basato sul certificato, le informazioni della sottoscrizione rimangono disponibili finché la sottoscrizione e il certificato sono validi. Con questo metodo, tuttavia, diventa più difficile gestire l'accesso a una sottoscrizione condivisa, ad esempio quando più persone sono autorizzate ad accedere all'account. Inoltre, l'API di Gestione risorse di Azure non accetta l'autenticazione del certificato.

Per altre informazioni su autenticazione e gestione delle sottoscrizioni in Azure, vedere [Gestire account, sottoscrizioni e ruoli amministrativi](http://go.microsoft.com/fwlink/?LinkId=324796).

<h3>Usare il metodo basato su Azure AD</h3>

1. Aprire la console di Azure PowerShell come descritto in [Procedura: Installare Azure PowerShell](#Install).

2. Digitare il seguente comando:

		Add-AzureAccount

3. Nella finestra digitare l'indirizzo di posta elettronica e la password associati all'account.

4. Le informazioni delle credenziali vengono autenticate e salvate in Azure, quindi la finestra viene chiusa.

5. A partire dalla versione 0.8.6, se si accede usando un account aziendale o dell'istituto di istruzione, è possibile digitare il comando seguente per ignorare la finestra popup. Viene visualizzata la finestra standard delle credenziali di Windows PowerShell che consente di immettere la password e il nome utente dell'account aziendale o dell'istituto di istruzione.

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

    > Per altre informazioni sulla sicurezza e sull'uso di credenziali, vedere [Procedure consigliate per la distribuzione di password e altri dati sensibili in ASP.NET e Siti Web di Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).


	> [AZURE.NOTE] Questo metodo di accesso non interattivo funziona solo con un account aziendale o dell'istituto di istruzione.  Un account aziendale o dell'istituto di istruzione è un utente che viene gestito dalla propria azienda o dal proprio istituto di istruzione e che viene definito nell'istanza di Azure Active Directory per tale azienda o istituto di istruzione. Se non si dispone di un account aziendale o dell'istituto di istruzione e si usa un account Microsoft per accedere alla sottoscrizione di Azure, è possibile crearne facilmente uno mediante la procedura seguente.
	>
	> 1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com) e fare clic su **Active Directory**.
	>
	> 2. Se non esistono directory, selezionare **Crea directory** e fornire le informazioni richieste.
	>
	> 3. Selezionare la directory e aggiungere un nuovo utente. Il nuovo utente può accedere usando un account aziendale o dell'istituto di istruzione.
	>
	>     Durante la creazione dell'utente, verranno forniti un indirizzo di posta elettronica e una password provvisoria. Salvare queste informazioni perché verranno usate in un altro passaggio.
	>
	> 4. Dal portale di gestione, selezionare **Impostazioni** e quindi **Amministratori**. Selezionare **Aggiungi** e aggiungere il nuovo utente come co-amministratore. In questo modo l'account aziendale o dell'istituto di istruzione può gestire la sottoscrizione di Azure.
	>
	> 5. Infine, disconnettersi dal portale di Azure e ripetere l'accesso con il nuovo account aziendale o dell'istituto di istruzione. La prima volta che si accede con questo account verrà richiesto di cambiare la password.
	>
	>Per altre informazioni sull'iscrizione a Microsoft Azure con un account aziendale o dell'istituto di istruzione, vedere [Iscrizione ad Azure come organizzazione](sign-up-organization.md).

<h3>Usare il metodo basato sul certificato</h3>

Il modulo Azure include cmdlet che consentono di scaricare e importare il certificato.

> [AZURE.NOTE] I cmdlet del modulo AzureResourceManager richiedono il metodo di Azure AD (Add-AzureAccount). Questi cmdlet non supportano i file di impostazioni di pubblicazione. Per altre informazioni sui cmdlet del modulo AzureResourceManager, vedere i [cmdlet di Gestione risorse di Azure](http://go.microsoft.com/fwlink/?LinkID=394765).


- Il cmdlet **Get-AzurePublishSettingsFile** apre una pagina Web nel
portale di gestione di Azure, dalla quale è possibile scaricare le informazioni sula sottoscrizione. Tali informazioni sono contenute in un file .publishsettings.

- Il cmdlet **Import-AzurePublishSettingsFile** consente invece di importare il file .publishsettings che verrà usato dal modulo. Tale file include un certificato di gestione con le credenziali di sicurezza.

> [AZURE.IMPORTANT] Dopo aver importato le impostazioni, è consigliabile eliminare il profilo di pubblicazione scaricato tramite <b>Get-AzurePublishSettingsFile</b>. Il certificato di gestione include le credenziali di sicurezza, pertanto è consigliabile impedirne l'accesso a utenti non autorizzati. Altre informazioni sulle sottoscrizioni sono disponibili nel [portale di gestione di Azure](http://manage.windowsazure.com/) o nel [Portale per i clienti dei Microsoft Online Services](http://go.microsoft.com/fwlink/p/?LinkId=324875).

1. Accedere al [portale di gestione di Azure Management](http://manage.windowsazure.com) usando le credenziali del proprio account Azure.

2. Aprire la console di Azure PowerShell come descritto in [Procedura: Installare Azure PowerShell](#Install).

3. Digitare il seguente comando:

		Get-AzurePublishSettingsFile

4. Quando richiesto, scaricare e salvare il profilo di pubblicazione e prendere nota del percorso e del nome del file .publishsettings. Queste informazioni sono necessarie quando si esegue il cmdlet **Import-AzurePublishSettingsFile** per importare le impostazioni. Il formato predefinito del percorso e del nome file è:

			C:\\Users<UserProfile>\\Download[*MySubscription*-...]-*downloadDate*-credentials.publishsettings

5. Digitare un comando simile al seguente, sostituendo i segnaposto con il nome del proprio account Windows e il percorso e il nome file:

		Import-AzurePublishSettingsFile C:\Users<UserProfile>\Downloads<SubscriptionName>-credentials.publishsettings

> [AZURE.NOTE] Se si viene aggiunti come coamministratore ad altre sottoscrizioni dopo l'importazione delle impostazioni di pubblicazione, sarà necessario ripetere questa procedura per scaricare un nuovo file delle impostazioni di pubblicazione e quindi importare le impostazioni. Per informazioni sull'aggiunta di coamministratori per la gestione dei servizi per una sottoscrizione, vedere [Aggiungere e rimuovere coamministratori per le sottoscrizioni Azure](http://msdn.microsoft.com/library/windowsazure/gg456328.aspx).

<h3> Visualizzare account e dettagli della sottoscrizione</h3>
Con Azure PowerShell è possibile usare più account e sottoscrizioni. Per aggiungere altri account, è sufficiente eseguire più volte Add-AzureAccount.

Per ottenere gli account Azure disponibili, digitare:

	Get-AzureAccount

Per ottenere le sottoscrizioni Azure, digitare:

	Get-AzureSubscription

## <a id="Ex"></a>Come usare i cmdlet: un esempio ##

Dopo aver installato il modulo e aver configurato il computer per la connessione alla sottoscrizione, è possibile creare un'app Web di Azure. Questo esempio offre informazioni utili per iniziare a usare i cmdlet di Azure.

1. Avviare la console di Azure PowerShell.

2. Scegliere un nome per l'app web. È necessario sceglierne uno conforme alle convenzioni di denominazione DNS. I nomi validi possono contenere solo lettere dalla 'a' alla 'z', numeri da '0' a '9', nonché il segno meno ('-'). 

	Il nome dell'app Web deve essere univoco in Azure. In questo esempio viene usato "mySite", assicurarsi tuttavia di scegliere un nome diverso da questo, ad esempio il nome del proprio account seguito da un numero.  

	Dopo aver selezionato un nome, digitare un comando simile al seguente. Sostituire il nome dell'app web per "mySite".

		New-AzureWebsite mySite

	Il cmdlet crea l'app web e restituisce un oggetto che rappresenta la nuova app Web. Le proprietà dell'oggetto includono informazioni utili sull'app Web.

3. Per ottenere informazioni sull'app Web, digitare questo comando. Vengono restituite alcune informazioni su tutte le app Web nella sottoscrizione, inclusa quella appena creato.

		Get-AzureWebsite

4. Per ottenere altre informazioni sull'app Web, aggiungere il nome dell'app al comando. Assicurarsi di sostituire "mySite" con il nome dell'app Web.

		Get-AzureWebsite -Name mySite

5. Le app Web vengono avviate dopo la creazione. Per arrestare l'app Web, digitare il seguente comando, incluso il nome dell'app Web.

		Stop-AzureWebsite -Name mySite

6. Per verificare che il sito Web si sia 'stopped', eseguire di nuovo il comando Get-AzureWebsite.

		Get-AzureWebsite

7. Per completare questo test, eliminare l'app Web. Digitare:  

		Remove-AzureWebsite -Name mySite

7. Per completare questa attività, confermare l'eliminazione dell'app Web.

		Get-AzureWebsite -Name mySite

##<a id="Help"></a>Risorse della Guida##

Queste risorse consentono di ottenere informazioni per cmdlet specifici:


-   Dall'interno della console è possibile usare la Guida incorporata. Per accedere a tale Guida, usare il cmdlet **Get-Help**. Nella tabella seguente vengono forniti alcuni esempi di comandi che è possibile usare per accedere alla Guida. Per visualizzare altre informazioni dall'interno della console, digitare **help**.

    <table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Comando</b></td>
		<td><b>Risultato</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Get-Help</td>
		<td>Illustra l'utilizzo della Guida. <p><b>Nota</b>: la descrizione include alcune informazioni sui file della Guida che non sono valide per il modulo Azure. In particolare, i file della Guida vengono installati contestualmente al modulo e non sono pertanto scaricabili separatamente.</p>
</td>
    </tr>
    <tr align="left" valign="top">
		<td>Get-Help Azure</td>
		<td>Consente di ottenere tutti i cmdlet nel modulo Azure.</td>
    </tr>
	<tr align="left" valign="top">
		<td>Get-Help &lt;<b>linguaggio</b>&gt;-dev</td>
		<td>Consente di ottenere i cmdlet per lo sviluppo e la gestione di applicazioni in un linguaggio specifico, ad esempio help node-dev, help php-dev o help python-dev.</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Get-Help &lt;<b>cmdlet</b>&gt;</td>
		<td>Consente di ottenere informazioni su un cmdlet di Windows PowerShell. Sostituire <cmdlet> con il nome del cmdlet.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Get-Help &lt;<b>cmdlet</b>&gt; -Parameter *</td>
		<td>Consente di ottenere le descrizioni dei parametri del cmdlet. Il simbolo asterisco (*) indica "tutti".</td>
    </tr>
    <tr align="left" valign="top">
		<td>Get-Help &lt;<b>cmdlet</b>&gt; -Examples</td>
		<td>Consente di ottenere la sintassi ed esempi d'uso di cmdlet.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Get-Help &lt;<b>cmdlet</b>&gt; -Full</td>
		<td>Consente di ottenere tutte le informazioni relative a un cmdlet, inclusi dettagli tecnici.</td>
    </tr>
    </tbody>
    </table>



- Informazioni di riferimento sui cmdlet nei moduli Azure PowerShell sono inoltre disponibili nella raccolta della documentazione di Azure. Per informazioni, vedere [Riferimento ai cmdlet di Azure](http://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Per le informazioni messe a disposizione dalla community, visitare i seguenti forum:

- [Forum Azure su MSDN]( http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)


## <a id="Resources"></a>Risorse aggiuntive##

Di seguito sono elencate alcune risorse disponibili che è possibile usare per imparare a usare Azure e Windows PowerShell.

- Per informazioni su come accedere ai componenti di archiviazione di Azure, vedere [Uso di Azure PowerShell con Archiviazione di Azure](storage-powershell-guide-full.md).

- Per inviare commenti e suggerimenti sui cmdlet, segnalare problemi o accedere al codice sorgente, vedere l'articolo [sul repository del codice di Azure PowerShell](https://github.com/WindowsAzure/azure-sdk-tools).

- Per informazioni sulla riga di comando e l'ambiente di scripting di Windows PowerShell, visitare lo [Script Center di TechNet](http://go.microsoft.com/fwlink/p/?LinkId=320211).

- Per informazioni su come installare, imparare a usare, usare e personalizzare Windows PowerShell, vedere [Scrittura di script con Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320210).

- Per informazioni sugli script e su come eseguirli in Windows PowerShell, vedere [Supporto per l'utilizzo di script](http://go.microsoft.com/fwlink/p/?LinkId=320627). In questo articolo vengono fornite informazioni di base su come creare script e configurare il computer per eseguirli.

- Per informazioni sui cmdlet per Azure AD, vedere [Gestire Azure AD tramite Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320628).





  [Portale per i clienti dei Microsoft Online Services]: https://mocp.microsoftonline.com/site/default.aspx


<!--HONumber=52-->