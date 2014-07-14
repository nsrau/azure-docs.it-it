<properties  linkid="Install-Config-Windows-Azure-PowerShell" urlDisplayName="Azure PowerShell" pageTitle="How to install and configure Azure PowerShell" description="Learn how to install and configure Azure PowerShell." umbracoNaviHide="0" disqusComments="1" editor="tysonn" manager="jeffreyg" documentationCenter="" services="" solutions="" authors="kathydav" title="How to install and configure Azure PowerShell" />

# Come installare e configurare Azure PowerShell

 
<div  class="dev-center-tutorial-selector sublanding"><a href="/en-us/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/en-us/manage/install-and-configure-cli/" title="CLI per più piattaforme">CLI per più piattaforme</a></div>

 È possibile utilizzare Windows PowerShell per eseguire numerose attività in Azure, sia in modo interattivo dal prompt dei comandi che in modo automatico tramite script. Azure PowerShell è un modulo che offre i cmdlet per gestire Azure tramite Windows PowerShell. È possibile utilizzare i cmdlet per creare, testare, distribuire e gestire soluzioni e servizi offerti tramite la piattaforma Azure. Nella maggior parte dei casi è possibile utilizzarli per eseguire le stesse attività eseguibili tramite il portale di gestione di Azure. È ad esempio possibile creare e configurare servizi cloud, macchine virtuali, reti virtuali e siti Web.

Il modulo viene distribuito come file scaricabile. Il codice sorgente viene gestito tramite un archivio disponibile pubblicamente. Nelle istruzioni per l'installazione più avanti in questo argomento è incluso un collegamenti ai file scaricabili. Per informazioni sul codice sorgente, vedere l'articolo sull'[archivio del codice di Azure PowerShell][1].

In questa guida vengono fornite informazioni di base sull'installazione e la configurazione di Azure PowerShell per la gestione della piattaforma Azure.

## Sommario

* [Prerequisiti per l'utilizzo di Azure PowerShell](#Prereq)
* [Procedura: Installare Azure PowerShell](#Install)
* [Procedura: Eseguire la connessione alla sottoscrizione](#Connect)
* [Come utilizzare i cmdlet: un esempio](#Ex)
* [Risorse della Guida](#Help)
* [Risorse aggiuntive](#Resources)

### <a id="Prereq" ></a>Prerequisiti per l'utilizzo di Azure PowerShell

Azure è una piattaforma basata su sottoscrizione. Ciò significa che per poter utilizzare la piattaforma è necessaria una sottoscrizione. Nella maggior parte dei casi significa inoltre che i cmdlet necessitano delle informazioni della sottoscrizione per eseguire le attività correlate alla sottoscrizione. Queste informazioni non sono invece necessarie per alcuni dei cmdlet correlati all'archiviazione. Per fornire tali informazioni, è necessario configurare il computer in modo che si connetta alla sottoscrizione, come descritto più avanti nell'articolo in "Procedura: Eseguire la connessione alla sottoscrizione".

> [WACOM.NOTE] Sono disponibili numerose opzioni di sottoscrizione.
> Per informazioni, vedere [Introduzione ad Azure][2].

Quando si installa il modulo, il programma di installazione verifica se nel sistema è disponibile il software necessario e installa tutte le dipendenze, tra cui la versione corretta di Windows PowerShell e .NET Framework.

<h2> <a id="Install" ></a>Procedura: Installare Azure PowerShell</h2>


Per scaricare e installare il modulo Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft][3]. Quando richiesto, fare clic su **Esegui**. L'Installazione guidata piattaforma Web Microsoft verrà caricata e includerà il modulo **Azure PowerShell** disponibile per l'installazione. Con l'Installazione guidata piattaforma Web vengono installate tutte le dipendenze per i cmdlet di Azure PowerShell. Per completare l'installazione, attenersi alle istruzioni visualizzate.

Per ulteriori informazioni sugli strumenti da riga di comando disponibili per Azure, vedere la sezione relativa agli [strumenti da riga di comando][4].

Con il modulo viene installata anche una console personalizzata per Azure PowerShell. È possibile eseguire i cmdlet dalla console standard di Windows PowerShell o da quella di Azure PowerShell.

Il metodo utilizzato per aprire l'una o l'altra console varia a seconda della versione di Windows in esecuzione:

* In un computer che esegue almeno Windows 8 o Windows Server 2012 è possibile utilizzare la funzionalità di ricerca integrata. Nella schermata Start iniziare a digitare **power**. Verrà visualizzato un elenco specifico di app che include Windows PowerShell e Azure PowerShell. Fare clic su una delle app per aprire la finestra della console. Per aggiungere l'app alla schermata Start, fare clic con il pulsante destro del mouse sull'icona.

* In un computer che esegue una versione di Windows precedente a Windows 8 o Windows Server 2012 è possibile utilizzare il menu Start. Dal menu Start fare clic su **Tutti i programmi**, scegliere **Azure** e quindi
  **Azure PowerShell**.

<h2><a id="Connect" ></a>Procedura: Eseguire la connessione alla sottoscrizione</h2>


Per utilizzare Azure, è necessaria una sottoscrizione. Se non si dispone di una sottoscrizione, vedere [Introduzione ad Azure][2].

Per poter gestire i servizi tramite i cmdlet, è necessario specificare le informazioni della sottoscrizione. A partire dalla versione .0.7 del modulo, è possibile fornire queste informazioni in due diversi modi. È possibile scaricare e utilizzare un certificato di gestione contenente le informazioni oppure accedere ad Azure con l'account Microsoft o un ID organizzazione. Quando si effettua l'accesso, le credenziali vengono autenticate in Azure Active Directory (Azure AD).

Per scegliere facilmente il metodo di autenticazione più adatto alle proprie esigenze, tenere presente quanto segue:

* Il metodo basato su Azure AD consente di gestire più facilmente l'accesso a una sottoscrizione, ma può danneggiare l'automazione. Le credenziali sono disponibili per Azure PowerShell per 12 ore. Dopo tale periodo scadono ed è necessario effettuare di nuovo l'accesso.
* Quando si utilizza il metodo basato sul certificato, le informazioni della sottoscrizione rimangono disponibili finché la sottoscrizione e il certificato sono validi. Con questo metodo diventa più facile utilizzare l'automazione per attività di lunga durata. Dopo aver scaricato e importato le informazioni, non sarà necessario fornirle nuovamente. Con questo metodo, tuttavia, diventa più difficile gestire l'accesso a una sottoscrizione condivisa, ad esempio quando più persone sono autorizzate ad accedere all'account.

Per ulteriori informazioni su autenticazione e gestione delle sottoscrizioni in Azure, vedere [Gestire account, sottoscrizioni e ruoli amministrativi][5].

<h3>Utilizzare il metodo basato su Azure AD</h3>


1.  Aprire la console di Azure PowerShell come descritto in [Procedura: Installare Azure PowerShell](#Install).

2.  Digitare il comando seguente:
    
    `Add-AzureAccount`

3.  Nella finestra digitare l'indirizzo di posta elettronica e la password associati all'account.

4.  Le informazioni delle credenziali vengono autenticate e salvate in Azure, quindi la finestra viene chiusa.

<h3>Utilizzare il metodo basato sul certificato</h3>


Il modulo Azure PowerShell include cmdlet che consentono di scaricare e importare il certificato.

* Con il cmdlet **Get-AzurePublishSettingsFile** viene aperta nel portale di gestione di Azure una pagina Web da cui scaricare le informazioni della sottoscrizione. Tali informazioni sono contenute in un file .publishsettings.

* Il cmdlet **Import-AzurePublishSettingsFile** consente invece di importare il file .publishsettings che verrà utilizzato dal modulo. Tale file include un certificato di gestione con le credenziali di sicurezza.

 
<div  class="dev-callout"> 
<b>Importante</b> 
<p>Dopo aver importato le impostazioni, è consigliabile eliminare il profilo di pubblicazione scaricato tramite <b>Get-AzurePublishSettingsFile</b>. Il certificato di gestione include le credenziali di sicurezza, pertanto è consigliabile impedirne l'accesso a utenti non autorizzati. Ulteriori informazioni sulle sottoscrizioni sono disponibili nel <a href="http://manage.windowsazure.com/">portale di gestione di Azure</a> o nel <a href="http://go.microsoft.com/fwlink/p/?LinkId=324875">Portale per i clienti dei Microsoft Online Services</a>.</p> 
</div>

 1.  Accedere al [portale di gestione di Azure][6] utilizzando le credenziali del proprio account Azure.

2.  Aprire la console di Azure PowerShell come descritto in [Procedura: Installare Azure PowerShell](#Install).

3.  Digitare il comando seguente:
    
    `Get-AzurePublishSettingsFile`

4.  Quando richiesto, scaricare e salvare il profilo di pubblicazione e prendere nota del percorso e del nome del file .publishsettings. Queste informazioni sono necessarie quando si esegue il cmdlet **Import-AzurePublishSettingsFile** per importare le impostazioni. Il formato predefinito del percorso e del nome file è:
    
    C:\\Users\\<ProfiloUtente>\\Download\\[*Sottoscrizione*-...]-*dataDownload*-credentials.publishsettings

5.  Digitare un comando simile al seguente, sostituendo i segnaposto con il nome del proprio account Windows e il percorso e il nome file:
    
    Import-AzurePublishSettingsFile
    C:\\Users\\<ProfiloUtente>\\Downloads\\<NomeSottoscrizione>-credentials.publishsettings

> [WACOM.NOTE] Se si viene aggiunti come coamministratore ad altre
> sottoscrizioni dopo l'importazione delle impostazioni di
> pubblicazione, sarà necessario ripetere questa procedura per scaricare
> un nuovo file .publishsettings e quindi importare le impostazioni. Per
> informazioni sull'aggiunta di coamministratori per la gestione dei
> servizi per una sottoscrizione, vedere [Aggiungere e rimuovere
> coamministratori per le sottoscrizioni Azure][7].

<h3> Visualizzare account e dettagli della sottoscrizione</h3>


Con Azure PowerShell è possibile utilizzare più account e sottoscrizioni. Per aggiungere altri account, è sufficiente eseguire più volte Add-AzureAccount.

Per visualizzare gli account disponibili, digitare:

    `Get-AzureAccount`

Per visualizzare le informazioni della sottoscrizione, digitare:

    `Get-AzureSubscription`

## <a id="Ex" ></a>Come utilizzare i cmdlet: un esempio

Dopo aver installato il modulo e aver configurato il computer per la connessione alla sottoscrizione, è possibile creare un sito Web di esempio per illustrare l'utilizzo dei cmdlet.

1.  Aprire la shell di Azure PowerShell, a meno che non sia già aperta.

2.  Stabilire un nome da assegnare al sito. È necessario utilizzarne uno conforme alle convenzioni di denominazione DNS. I nomi validi possono contenere solo lettere dalla 'a' alla 'z', numeri da '0' a '9', nonché il segno meno ('-'). Il nome deve inoltre essere univoco in Azure.
    
    Dopo aver selezionato un nome, digitare un comando simile al seguente. Ad esempio, per creare un sito Web utilizzando l'account e un numero in modo da poter riutilizzare questa convenzione, digitare quanto segue specificando il proprio nome account:
    
    `New-AzureWebsite my-site-name-1`
    
    Il cmdlet consente di creare il sito Web e quindi di visualizzare informazioni sul nuovo sito.

3.  Per controllare lo stato del sito Web, digitare:
    
    `Get-AzureWebsite`
    
    Il cmdlet consente di visualizzare il nome e lo stato, nonché il nome host del nuovo sito Web.


   
    > [WACOM.NOTE] Se eseguito come illustrato in precedenza, questo
    > comando consente di visualizzare informazioni su tutti i siti Web
    > associati alla sottoscrizione corrente.

4.  I siti Web vengono avviati dopo la creazione. Per arrestare il sito Web, digitare:
    
    `Stop-AzureWebsite -Name nome-account-1`

5.  Eseguire di nuovo il comando Get-AzureWebsite per verificare che lo stato del sito sia 'stopped'.

6.  Per completare questo test, è possibile eliminare il sito Web.
    Digitare:
    
    `Remove-AzureWebsite -Name nome-account-1`
    
    Per completare l'attività, confermare la rimozione.

## <a id="Help" ></a>Risorse della Guida

Queste risorse consentono di ottenere informazioni per cmdlet specifici:

* Dall'interno della console è possibile utilizzare la Guida incorporata. Per accedere a tale Guida, utilizzare il cmdlet **Get-Help**. Nella tabella seguente vengono forniti alcuni esempi di comandi che è possibile utilizzare per accedere alla Guida. Per visualizzare ulteriori informazioni dall'interno della console, digitare **help**.
  
  <table  border="1" cellspacing="4" cellpadding="4">
  <tbody>
  <tr  align="left" valign="top">
      <td><b>Comando</b>
  </td>
  
      <td><b>Risultato</b>
  </td>
  
  </tr>
  
  <tr  align="left" valign="top">
      <td>help</td>
  
      <td>Illustra l'utilizzo della Guida. <p><b>Nota</b>
  : la descrizione include alcune informazioni sui file della Guida che non sono valide per il modulo Azure. In particolare, i file della Guida vengono installati contestualmente al modulo e non sono pertanto scaricabili separatamente.</p>
  
  </td> </tr> <tr  align="left"
valign="top"> <td >help azure</td>
<td >Visualizza l'elenco di tutti i cmdlet del
modulo Azure PowerShell.</td> </tr> <tr
 align="left" valign="top"> <td
>help <<b>linguaggio</b>>-dev</td> <td
>Visualizza l'elenco dei cmdlet per sviluppare e
gestire applicazioni in un linguaggio specifico, ad esempio help node-dev, help php-dev o help python-dev.</td> </tr> <tr
 align="left" valign="top"> <td
>help <<b>cmdlet</b>></td> <td
>Visualizza la Guida relativa a un cmdlet di
Windows PowerShell.</td> </tr> <tr  align="left" valign="top"> <td >help
<<b>cmdlet</b>> -parameter \*</td>
<td >Visualizza le definizioni dei parametri di
un cmdlet, ad esempio help get-azuresubscription -parameter
*</td> </tr> <tr  align="left"
valign="top"> <td >help <<b>cmdlet</b>> -examples</td> <td
>Visualizza la sintassi e la descrizione dei
comandi di esempio di un cmdlet.</td> </tr> <tr
 align="left" valign="top"> <td
>help <<b>cmdlet</b>>
-full</td> <td >Visualizza i requisiti
tecnici di un cmdlet.</td> </tr> </tbody>
</table>

* Informazioni di riferimento sui cmdlet nel modulo Azure PowerShell sono inoltre disponibili nella raccolta della documentazione di Azure. Per informazioni, vedere [Riferimento ai cmdlet di Azure][8].

Per le informazioni messe a disposizione dalla community, visitare i seguenti forum:

* [Forum di Azure su MSDN][9]
* [Stackoverflow][10]

## <a id="Resources" ></a>Risorse aggiuntive

Di seguito sono elencate alcune risorse disponibili che è possibile utilizzare per imparare a utilizzare Azure e Windows PowerShell.

* Per inviare commenti e suggerimenti sui cmdlet, segnalare problemi o accedere al codice sorgente, vedere l'articolo sull'[archivio del codice di Azure PowerShell][1].

* Per informazioni sulla riga di comando e l'ambiente di scripting di Windows PowerShell, visitare lo [Script Center di TechNet][11].

* Per informazioni su come installare, imparare a utilizzare, utilizzare e personalizzare Windows PowerShell, vedere l'articolo sull'[uso degli script con Windows PowerShell][12].

* Per informazioni sugli script e su come eseguirli in Windows PowerShell, vedere l'articolo sull'[esecuzione di script][13]. In questo articolo vengono fornite informazioni di base su come creare script e configurare il computer per eseguirli.

* Per informazioni sui cmdlet per Azure AD, vedere l'articolo sulla [gestione di Azure AD con Windows PowerShell][14].



[1]: https://github.com/WindowsAzure/azure-sdk-tools
[2]: http://go.microsoft.com/fwlink/p/?LinkId=320795
[3]: http://go.microsoft.com/fwlink/p/?LinkId=320376
[4]: http://go.microsoft.com/fwlink/?LinkId=320796
[5]: http://go.microsoft.com/fwlink/?LinkId=324796
[6]: http://manage.windowsazure.com
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/gg456328.aspx
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554330.aspx
[9]: http://go.microsoft.com/fwlink/p/?LinkId=320212
[10]: http://go.microsoft.com/fwlink/?LinkId=320213
[11]: http://go.microsoft.com/fwlink/p/?LinkId=320211
[12]: http://go.microsoft.com/fwlink/p/?LinkId=320210
[13]: http://go.microsoft.com/fwlink/p/?LinkId=320627
[14]: http://go.microsoft.com/fwlink/p/?LinkId=320628