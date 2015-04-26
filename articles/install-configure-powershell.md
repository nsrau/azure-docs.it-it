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
	ms.date="11/26/2014" 
	ms.author="coreyp"/>

# Come installare e configurare Azure PowerShell

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/it-it/manage/install-and-configure-cli/" title="CLI per pi&ugrave; piattaforme">CLI per pi&ugrave; piattaforme</a></div>

È possibile utilizzare Windows PowerShell per eseguire numerose attività in Azure, sia in modo interattivo dal prompt dei comandi che in modo automatico tramite script. Azure PowerShell è un modulo che offre i cmdlet per gestire Azure tramite Windows PowerShell. È possibile utilizzare i cmdlet per creare, testare, distribuire e gestire soluzioni e servizi offerti tramite la piattaforma Azure. Nella maggior parte dei casi è possibile usarli per eseguire le stesse attività eseguibili tramite il portale di gestione di Azure. È ad esempio possibile creare e configurare servizi cloud, macchine virtuali, reti virtuali e siti Web.

Il modulo viene distribuito come file scaricabile. Il codice sorgente viene gestito tramite un archivio disponibile pubblicamente. Nelle istruzioni per l'installazione più avanti in questo argomento è incluso un collegamenti ai file scaricabili. Per informazioni sul codice sorgente, vedere l'articolo sull'[archivio del codice di Azure PowerShell][archivio del codice di Azure PowerShell].

In questa guida vengono fornite informazioni di base sull'installazione e la configurazione di Azure PowerShell per la gestione della piattaforma Azure.

## Sommario

-   [Prerequisiti per l'utilizzo di Azure PowerShell][Prerequisiti per l'utilizzo di Azure PowerShell]
-   [Procedura: Installare Azure PowerShell][Procedura: Installare Azure PowerShell]
-   [Procedura: Eseguire la connessione alla sottoscrizione][Procedura: Eseguire la connessione alla sottoscrizione]
-   [Come utilizzare i cmdlet: un esempio][Come utilizzare i cmdlet: un esempio]
-   [Risorse della Guida][Risorse della Guida]
-   [Risorse aggiuntive][Risorse aggiuntive]

### <span id="Prereq"></span></a>Prerequisiti per l'utilizzo di Azure PowerShell

Azure è una piattaforma basata su sottoscrizione. Ciò significa che per poter utilizzare la piattaforma è necessaria una sottoscrizione. Nella maggior parte dei casi significa inoltre che i cmdlet necessitano delle informazioni della sottoscrizione per eseguire le attività correlate alla sottoscrizione. Queste informazioni non sono invece necessarie per alcuni dei cmdlet correlati all'archiviazione. Per fornire tali informazioni, è necessario configurare il computer in modo che si connetta alla sottoscrizione, come descritto più avanti nell'articolo in "Procedura: Eseguire la connessione alla sottoscrizione".

> [WACOM.NOTE] A partire dalla versione 0.8.5, per i moduli Azure PowerShell è necessario disporre di Microsoft .NET Framework 4.5.

Quando si installa il modulo, il programma di installazione verifica se nel sistema è disponibile il software necessario e installa tutte le dipendenze, tra cui la versione corretta di Windows PowerShell e .NET Framework.

## <span id="Install"></span></a>Procedura: Installare Azure PowerShell

Per scaricare e installare i moduli Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft][Installazione guidata piattaforma Web Microsoft]. Quando richiesto, fare clic su **Esegui**. Con l'Installazione guidata piattaforma Web vengono installati tutti i moduli e tutte le dipendenze di Azure PowerShell. Per completare l'installazione, attenersi alle istruzioni visualizzate.

Per ulteriori informazioni sugli strumenti da riga di comando disponibili per Azure, vedere la sezione relativa agli [strumenti da riga di comando][strumenti da riga di comando].

Con il modulo viene installata anche una console personalizzata per Azure PowerShell. È possibile eseguire i cmdlet dalla console standard di Windows PowerShell o da quella di Azure PowerShell.

Il metodo utilizzato per aprire l'una o l'altra console varia a seconda della versione di Windows in esecuzione:

-   In un computer che esegue almeno Windows 8 o Windows Server 2012 è possibile utilizzare la funzionalità di ricerca integrata. Nella schermata Start iniziare a digitare **power**. Viene visualizzato un elenco specifico di app che include Windows PowerShell e Azure PowerShell. Per aprire la console, fare clic su una delle app. Per aggiungere l'app alla schermata Start, fare clic con il pulsante destro del mouse sull'icona.

-   In un computer che esegue una versione di Windows precedente a Windows 8 o Windows Server 2012 è possibile usare il menu Start. Dal menu Start fare clic su **Tutti i programmi**, scegliere **Azure** e quindi **Azure PowerShell**.

## <span id="Connect"></span></a>Procedura: Eseguire la connessione alla sottoscrizione

Per utilizzare Azure, è necessaria una sottoscrizione. Se non si dispone di una sottoscrizione, vedere [Introduzione ad Azure][Introduzione ad Azure].

Per la gestione dei servizi da parte dei cmdlet, è necessario disporre di una sottoscrizione. Per fornire a Windows PowerShell le informazioni relative alla propria sottoscrizione, sono disponibili due metodi. È possibile usare un certificato di gestione contenente tali informazioni oppure accedere a Azure usando l'account Microsoft o un account aziendale. Una volta eseguito l'accesso, Azure Active Directory (Azure AD) esegue l'autenticazione delle credenziali e restituisce un token di accesso che consente ad Azure PowerShell di gestire l'account.

Per scegliere facilmente il metodo di autenticazione più adatto alle proprie esigenze, tenere presente quanto segue:

-   Azure AD è il metodo di autenticazione consigliato in quanto semplifica la gestione dell'accesso a una sottoscrizione. Con l'aggiornamento della versione 0.8.6, rende possibile lo scenario di automazione con l'autenticazione di Azure AD anche se viene usato l'account aziendale e può essere usato anche con l'API di Gestione risorse di Azure.
-   Quando si utilizza il metodo basato sul certificato, le informazioni della sottoscrizione rimangono disponibili finché la sottoscrizione e il certificato sono validi. Con questo metodo, tuttavia, diventa più difficile gestire l'accesso a una sottoscrizione condivisa, ad esempio quando più persone sono autorizzate ad accedere all'account. Inoltre, l'API di Gestione risorse di Azure non accetta l'autenticazione del certificato.

Per ulteriori informazioni su autenticazione e gestione delle sottoscrizioni in Azure, vedere [Gestire account, sottoscrizioni e ruoli amministrativi][Gestire account, sottoscrizioni e ruoli amministrativi].

### Utilizzare il metodo basato su Azure AD

1.  Aprire la console di Azure PowerShell come descritto in [Procedura: Installare Azure PowerShell][Procedura: Installare Azure PowerShell].

2.  Digitare il comando seguente:

    `Add-AzureAccount`

3.  Nella finestra digitare l'indirizzo di posta elettronica e la password associati all'account.

4.  Le informazioni delle credenziali vengono autenticate e salvate in Azure, quindi la finestra viene chiusa.

5.  A partire dalla versione 0.8.6, se si dispone di un account aziendale, è possibile digitare il comando seguente per ignorare la finestra popup. Viene visualizzata la finestra standard delle credenziali di Windows PowerShell che consente di immettere la password e il nome utente dell'account aziendale.

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

6.  Se si usa questo metodo in uno script di automazione e si desidera evitare la visualizzazione di eventuali finestre popup, usare il frammento di codice seguente.

        $userName = "<your organizational account user name>"
        $securePassword = ConvertTo-SecureString -String "<your organizational account password>" -AsPlainText -Force
        $cred = New-Object System.Management.Automation.PSCredential($userName, $securePassword)
        Add-AzureAccount -Credential $cred 

    > [WACOM.NOTE] Questo metodo di accesso non interattivo funziona solo con l'account aziendale. L'account aziendale corrisponde a un utente gestito dall'organizzazione, come definito nel tenant Azure Active Directory aziendale. Se attualmente non si dispone di un account aziendale e si utilizza un account Microsoft per accedere alla sottoscrizione di Azure, è possibile crearne facilmente uno eseguendo la procedura seguente.
    >
    > 1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure] e fare clic su **Active Directory**.
    >
    > 2.  Se non esiste alcuna directory, selezionare **Create your directory** e specificare le informazioni richieste.
    >
    > 3.  Selezionare la directory e aggiungere un nuovo utente. Questo nuovo utente corrisponde a un account aziendale.
    >
    >     Durante la creazione dell'utente, verranno forniti un indirizzo di posta elettronica e una password provvisoria. Salvare queste informazioni perché verranno utilizzate in un altro passaggio.
    >
    > 4.  Nel portale di gestione selezionare **Settings** e quindi **Administrators**. Selezionare **Add** e aggiungere il nuovo utente come co-amministratore. In questo modo l'account aziendale potrà gestire la sottoscrizione di Azure.
    >
    > 5.  Infine, disconnettersi dal portale Azure ed effettuare di nuovo l'accesso con il nuovo account aziendale. La prima volta che si accede con questo account verrà richiesto di cambiare la password.
    >
    > Per ulteriori informazioni sull'account aziendale con Microsoft Azure, vedere [Iscrizione ad Azure come organizzazione][Iscrizione ad Azure come organizzazione].

### Utilizzare il metodo basato sul certificato

Il modulo Azure include cmdlet che consentono di scaricare e importare il certificato.

-   Con il cmdlet **Get-AzurePublishSettingsFile** viene aperta nel
    portale di gestione di Azure una pagina Web da cui
    scaricare le informazioni della sottoscrizione. Tali informazioni sono contenute in un file .publishsettings.

-   Il cmdlet **Import-AzurePublishSettingsFile** consente invece di importare il file .publishsettings che verrà utilizzato dal modulo. Tale file include un certificato di gestione con le credenziali di sicurezza.

<div class="dev-callout"> 
<b>Nota</b>
<p>Per i cmdlet del modulo AzureResourceManager &egrave; necessario il metodo Azure AD (Add-AzureAccount). Questi cmdlet non supportano la pubblicazione dei file di impostazioni. Per altre informazioni sui cmdlet del modulo AzureResourceManager, vedere i <a href="http://go.microsoft.com/fwlink/?LinkID=394765">cmdlet di Gestione risorse di Azure</a>.</p> 
</div>

<div class="dev-callout"> 
<b>Importante</b> 
<p>&Egrave; consigliabile eliminare il profilo di pubblicazione
scaricato usando <b>Get-AzurePublishSettingsFile</b> dopo aver importato
le impostazioni. Poich&eacute; nel certificato di gestione sono incluse le credenziali di sicurezza,
gli utenti non autorizzati non possono accedervi. Per informazioni
sulle sottoscrizioni, accedere al <a href="http://manage.windowsazure.com/">portale di gestione di Azure</a> o al <a href="http://go.microsoft.com/fwlink/p/?LinkId=324875">Portale per i clienti dei Microsoft Online Services</a>.</p> 
</div>

1.  Accedere al [portale di gestione di Azure][2] utilizzando le credenziali del proprio account Azure.

2.  Aprire la console di Azure PowerShell come descritto in [Procedura: Installare Azure PowerShell][Procedura: Installare Azure PowerShell].

3.  Digitare il comando seguente:

    `Get-AzurePublishSettingsFile`

4.  Quando richiesto, scaricare e salvare il profilo di pubblicazione e prendere nota del percorso
    e del nome del file .publishsettings. Queste informazioni sono necessarie quando si esegue il cmdlet
    **Import-AzurePublishSettingsFile** per importare le impostazioni. Il formato
    predefinito del percorso e del nome file è:

    `C:\UsersC:\\Users\<UserProfile>\\Download\\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings`lt;UserProfile\>\\Download\\[\*MySubscription\*-...]-\*downloadDate\*-credentials.publishsettings</code>

5.  Digitare un comando simile al seguente, sostituendo i segnaposto con il nome del proprio account Windows e il percorso e il nome file:

    `Import-AzurePublishSettingsFile C:\UsersImport-AzurePublishSettingsFile C:\Users\<UserProfile>\Downloads\<SubscriptionName>-credentials.publishsettings`lt;UserProfile\>\\Downloads`Import-AzurePublishSettingsFile C:\Users\<UserProfile>\Downloads\<SubscriptionName>-credentials.publishsettings`lt;SubscriptionName\>-credentials.publishsettings</code>

> [WACOM.NOTE] Se si viene aggiunti come co-amministratore ad altre sottoscrizioni dopo l'importazione delle impostazioni di pubblicazione, è necessario
> ripetere questa procedura per scaricare un nuovo file .publishsettings
> e quindi importare le impostazioni. Per informazioni sull'aggiunta di co-amministratori per la gestione
> dei servizi per una sottoscrizione, vedere [Aggiungere e rimuovere co-amministratori per le sottoscrizioni Azure][Aggiungere e rimuovere co-amministratori per le sottoscrizioni Azure].

### Visualizzare account e dettagli della sottoscrizione

Con Azure PowerShell è possibile utilizzare più account e sottoscrizioni. Per aggiungere altri account, è sufficiente eseguire più volte Add-AzureAccount.

Per ottenere gli account Azure disponibili, digitare:

    Get-AzureAccount

Per ottenere le sottoscrizioni Azure, digitare:

    Get-AzureSubscription

## <span id="Ex"></span></a>Come utilizzare i cmdlet: un esempio

Dopo aver installato il modulo e aver configurato il computer per la connessione alla sottoscrizione, è possibile creare un sito Web di Azure. Questo esempio offre informazioni utili per iniziare a usare i cmdlet di Azure.

1.  Avviare la console di Azure PowerShell.

2.  Scegliere un nome per il sito Web. È necessario sceglierne uno conforme alle convenzioni di denominazione DNS. I nomi validi possono contenere solo lettere dalla 'a' alla 'z', numeri da '0' a '9', nonché il segno meno ('-').

    Il nome del sito Web deve essere univoco in Azure. In questo esempio viene usato "mySite", assicurarsi tuttavia di scegliere un nome diverso da questo, ad esempio il nome del proprio account seguito da un numero.

    Dopo aver selezionato un nome, digitare un comando simile al seguente. Sostituire "mySite" con il nome del sito Web.

    `New-AzureWebsite mySite`

    Il cmdlet crea il sito Web e restituisce un oggetto che rappresenta il nuovo sito Web. Le proprietà dell'oggetto includono informazioni utili sul sito Web.

3.  Per ottenere informazioni sul sito Web, digitare questo comando. Vengono restituite alcune informazioni su tutti i siti Web nella sottoscrizione, incluso quello appena creato.

    `Get-AzureWebsite`

4.  Per ottenere più informazioni sul proprio sito Web, aggiungere al comando il nome del sito Web. Assicurarsi di sostituire "mySite" con il nome del proprio sito Web.

    `Get-AzureWebsite -Name mySite`

5.  I siti Web vengono avviati dopo la creazione. Per arrestare il sito Web, digitare questo comando incluso il nome del sito Web.

    `Stop-AzureWebsite -Name mySite`

6.  Per verificare che il sito Web si sia arrestato, eseguire di nuovo il comando Get-AzureWebsite.

    `Get-AzureWebsite`

7.  Per completare questo test, eliminare il sito Web. Digitare:

    `Remove-AzureWebsite -Name mySite`

8.  Per completare questa attività, confermare l'eliminazione del sito Web.

    `Get-AzureWebsite -Name mySite`

## <span id="Help"></span></a>Risorse della Guida

Queste risorse consentono di ottenere informazioni per cmdlet specifici:

-   Dall'interno della console è possibile utilizzare la Guida incorporata. Per accedere a tale Guida, utilizzare il cmdlet **Get-Help**. Nella tabella seguente vengono forniti alcuni esempi di comandi che è possibile utilizzare per accedere alla Guida. Per visualizzare ulteriori informazioni dall'interno della console, digitare **help**.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Comando</strong></td>
    <td align="left"><strong>Risultato</strong></td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help</td>
    <td align="left">Illustra l'utilizzo della Guida.
    <p><strong>Nota</strong>: la descrizione include alcune informazioni sui file della Guida che non sono valide per il modulo Azure. In particolare, i file della Guida vengono installati contestualmente al modulo e non sono pertanto scaricabili separatamente.</p></td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help Azure</td>
    <td align="left">Consente di ottenere tutti i cmdlet nel modulo Azure.</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>linguaggio</strong>&gt;-dev</td>
    <td align="left">Consente di ottenere i cmdlet per lo sviluppo e la gestione di applicazioni in un linguaggio specifico, ad esempio help node-dev, help php-dev o help python-dev.</td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt;</td>
    <td align="left">Consente di ottenere informazioni su un cmdlet di Windows PowerShell. Sostituire <cmdlet> con il nome del cmdlet.</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Parameter *</td>
    <td align="left">Consente di ottenere le descrizioni dei parametri del cmdlet. Il simbolo asterisco (*) indica &quot;tutti&quot;.</td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Examples</td>
    <td align="left">Consente di ottenere la sintassi ed esempi d'uso di cmdlet.</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Full</td>
    <td align="left">Consente di ottenere tutte le informazioni relative a un cmdlet, inclusi dettagli tecnici.</td>
    </tr>
    </tbody>
    </table>

-   Informazioni di riferimento sui cmdlet nei moduli Azure PowerShell sono inoltre disponibili nella raccolta della documentazione di Azure. Per informazioni, vedere [Riferimento ai cmdlet di Azure][Riferimento ai cmdlet di Azure].

Per le informazioni messe a disposizione dalla community, visitare i seguenti forum:

-   [Forum di Azure su MSDN][Forum di Azure su MSDN]
-   [Stackoverflow][Stackoverflow]

## <span id="Resources"></span></a>Risorse aggiuntive

Di seguito sono elencate alcune risorse disponibili che è possibile utilizzare per imparare a utilizzare Azure e Windows PowerShell.

-   Per inviare commenti e suggerimenti sui cmdlet, segnalare problemi o accedere al codice sorgente, vedere l'articolo sull'[archivio del codice di Azure PowerShell][archivio del codice di Azure PowerShell].

-   Per informazioni sulla riga di comando e l'ambiente di scripting di Windows PowerShell, visitare lo [Script Center di TechNet][Script Center di TechNet].

-   Per informazioni su come installare, imparare a utilizzare, utilizzare e personalizzare Windows PowerShell, vedere l'articolo sull'[uso degli script con Windows PowerShell][uso degli script con Windows PowerShell].

-   Per informazioni sugli script e su come eseguirli in Windows PowerShell, vedere l'articolo sull'[esecuzione di script][esecuzione di script]. In questo articolo vengono fornite informazioni di base su come creare script e configurare il computer per eseguirli.

-   Per informazioni sui cmdlet per Azure AD, vedere l'articolo sulla [gestione di Azure AD con Windows PowerShell][gestione di Azure AD con Windows PowerShell].

  [archivio del codice di Azure PowerShell]: https://github.com/WindowsAzure/azure-sdk-tools
  [Prerequisiti per l'utilizzo di Azure PowerShell]: #Prereq
  [Procedura: Installare Azure PowerShell]: #Install
  [Procedura: Eseguire la connessione alla sottoscrizione]: #Connect
  [Come utilizzare i cmdlet: un esempio]: #Ex
  [Risorse della Guida]: #Help
  [Risorse aggiuntive]: #Resources
  [Installazione guidata piattaforma Web Microsoft]: http://go.microsoft.com/fwlink/p/?LinkId=320376
  [strumenti da riga di comando]: http://go.microsoft.com/fwlink/?LinkId=320796
  [Introduzione ad Azure]: http://go.microsoft.com/fwlink/p/?LinkId=320795
  [Gestire account, sottoscrizioni e ruoli amministrativi]: http://go.microsoft.com/fwlink/?LinkId=324796
  [portale di gestione di Azure]: https://manage.windowsazure.com
  [Iscrizione ad Azure come organizzazione]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
  [2]: http://manage.windowsazure.com
  [Aggiungere e rimuovere co-amministratori per le sottoscrizioni Azure]: http://msdn.microsoft.com/library/windowsazure/gg456328.aspx
  [Riferimento ai cmdlet di Azure]: http://msdn.microsoft.com/library/windowsazure/jj554330.aspx
  [Forum di Azure su MSDN]: http://go.microsoft.com/fwlink/p/?LinkId=320212
  [Stackoverflow]: http://go.microsoft.com/fwlink/?LinkId=320213
  [Script Center di TechNet]: http://go.microsoft.com/fwlink/p/?LinkId=320211
  [uso degli script con Windows PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320210
  [esecuzione di script]: http://go.microsoft.com/fwlink/p/?LinkId=320627
  [gestione di Azure AD con Windows PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320628

<!--HONumber=46--> 

<!--HONumber=46--> 
