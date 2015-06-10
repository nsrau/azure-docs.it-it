<properties 
	pageTitle="Introduzione all'automazione di Azure" 
	description="Informazioni sull'importazione e sull'esecuzione di un processo di automazione in Azure." 
	services="automation" 
	documentationCenter="" 
	authors="bwren" 
	manager="stevenka" 
	editor=""/>

<tags 
	ms.service="automation" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article" 
	ms.date="2/20/2015" 
	ms.author="bwren"/>


# Introduzione all'automazione di Azure

## Informazioni su Automazione di Azure

L'automazione di Microsoft Azure offre agli utenti la possibilità di automatizzare le attività manuali, a esecuzione prolungata, soggette a errori e ripetute di frequente comunemente eseguite negli ambienti cloud. È possibile creare, monitorare, gestire e distribuire risorse nell'ambiente Azure tramite runbook, che sono basati su flussi di lavoro di Windows PowerShell. In questa guida verrà illustrata un'esercitazione per eseguire un semplice runbook di esempio. Sono inoltre incluse le risorse per esplorare funzionalità più avanzate del servizio.

## Esercitazione
In questa esercitazione vengono illustrati i passaggi per creare un account di automazione, nonché importare un runbook "Hello World" di esempio in Automazione di Azure, eseguirlo e quindi visualizzarne l'output.

Per completare l'esercitazione, sarà necessaria una sottoscrizione di Azure. Se non si dispone ancora di un account, è possibile <a href="/pricing/member-offers/msdn-benefits-details/" target="_blank">attivare i benefici della sottoscrizione MSDN</a> oppure <a href="/pricing/free-trial/" target="_blank">iscriversi per ottenere una versione di valutazione gratuita</a>.

[AZURE.INCLUDE [automation-note-authentication](../includes/automation-note-authentication.md)]

## <a name="automationaccount"></a>Creare un account di automazione

Un account di automazione è un contenitore per le risorse di Automazione di Azure e consente di separare gli ambienti o di organizzare ulteriormente i flussi di lavoro. Per altre informazioni, vedere [Account di automazione](http://aka.ms/runbookauthor/azure/automationaccounts) nella sezione Automazione di MSDN Library. Se è già stato creato un account di automazione, è possibile ignorare questo passaggio.

1.	Accedere al [portale di gestione di Azure](http://manage.windowsazure.com).

2.	Nel portale di gestione fare clic su **Crea un account di automazione**.

	![Creazione di un account](./media/automation/automation_01_CreateAccount.png)

3.	Nella pagina **Aggiungi un nuovo account di automazione** immettere un nome e selezionare un'area per l'account. L'area indica la località in cui verranno archiviate le risorse di automazione incluse nell'account. Questa operazione non influisce sulla funzionalità dell'account, ma consente di eseguire più velocemente i runbook se l'area dell'account è vicina a quella in cui sono archiviate altre risorse di Azure. Al termine, fare clic sul segno di spunta.

	![Aggiunta di un nuovo account](./media/automation/automation_02_addnewautoacct.png)

## <a name="importrunbook"></a>Importare il runbook dalla raccolta di runbook

La [raccolta di runbook](http://aka.ms/runbookgallery) include runbook di esempio che è possibile importare direttamente in un account di automazione di Azure, per sfruttare quanto già fatto da altri utenti di Automazione di Azure e PowerShell. In questo passaggio la raccolta verrà usata per importare il runbook di esempio "Hello World".

4.	Nella pagina **Automazione** fare clic sul nuovo account appena creato.
 
	![Nuovo account](./media/automation/automation_03_NewAutoAcct.png)

5.	Fare clic su **RUNBOOK**.

	![Scheda Runbook](./media/automation/automation_04_RunbooksTab.png)
  
6.	Fare clic su **Nuovo** > **Runbook** > **Da raccolta**.

	![Raccolta di runbook](./media/automation/automation_05_ImportGallery.png)

7.  Selezionare la categoria **Esercitazione** e quindi **Hello World per Automazione di Azure**. Fare clic sul pulsante con la freccia destra.

	![Importare runbook](./media/automation/automation_06_ImportRunbook.png)

8.  Verificare i contenuti del runbook, quindi fare clic sul pulsante con la freccia destra.

	![Definizione del runbook](./media/automation/automation_07_RunbookDefinition.png)

8.	Verificare i dettagli del runbook, quindi fare clic sul pulsante con il segno di spunta.

	![Dettagli del runbook](./media/automation/automation_08_RunbookDetails.png)

## <a name="publishrunbook"></a>Pubblicare il runbook 

Il runbook viene inizialmente importato in modalità bozza. Questo significa che è possibile continuare a lavorarvi esso prima di autorizzarlo come nuova versione eseguibile. Dal momento che questo runbook di esempio non richiede alcuna configurazione aggiuntiva, verrà pubblicato così com'è. Per altre informazioni, vedere [Pubblicazione di un runbook](http://aka.ms/runbookauthor/azure/publishrunbook).

9.	Al termine dell'importazione del runbook fare clic su **Write-HelloWorld**.

	![Runbook importato](./media/automation/automation_07_ImportedRunbook.png)

9.	Fare clic su **CREA** e quindi su **BOZZA**.

	È possibile modificare il contenuto di un runbook in modalità Bozza. Per questo runbook, non è necessario apportare alcuna modifica.

	![Bozza di autore](./media/automation/automation_08_AuthorDraft.png)
 
10.	Fare clic su **PUBBLICA** per alzare di livello il runbook in modo che sia pronto per l'uso in produzione.

	![Opzione Pubblica](./media/automation/automation_085_Publish.png)
   
11.	Alla richiesta di conferma fare clic su **Sì**.
 
	![Richiesta di salvataggio e pubblicazione](./media/automation/automation_09_SavePubPrompt.png)

## <a name="startrunbook"></a>Avviare il runbook

Dopo aver importato e pubblicato il runbook, è possibile eseguirlo ed esaminare l'output. Per altre informazioni, vedere [Avvio di un runbook](http://aka.ms/runbookauthor/azure/startrunbook) e [Output e messaggi del runbook](http://aka.ms/runbookauthor/azure/runbookoutput).

12.	Con il runbook **Write-HelloWorld** aperto, fare clic su **AVVIO**.

	![Pubblicato](./media/automation/automation_10_PublishStart.png)
 
13.	Nella pagina **Specificare i valori di parametro del runbook** immettere un valore per **Nome**, che verrà usato come parametro di input per lo script Write-HelloWorld.ps1, quindi fare clic sul segno di spunta.

	![Parametri del runbook](./media/automation/automation_11_RunbookParams.png)
  
14.	Fare clic su **PROCESSI** per controllare lo stato del runbook appena avviato e quindi sul timestamp nella colonna **AVVIO PROCESSO** per visualizzare il riepilogo del processo.

	![Stato del runbook](./media/automation/automation_12_RunbookStatus.png)

15.	Nella pagina **RIEPILOGO** è possibile visualizzare il riepilogo, i parametri di input e l'output del processo.
 
	![Riepilogo del runbook](./media/automation/automation_13_RunbookSummary_callouts.png)

Congratulazioni. L'esercitazione è stata completata.

## <a name="nextsteps"></a>Passaggi successivi 
1. Il semplice runbook in questa esercitazione **non gestisce i servizi di Azure**. Nella maggior parte dei runbook a tale scopo vengono usati i [cmdlet di Azure](http://msdn.microsoft.com/library/jj156055.aspx), che richiedono l'autenticazione alla sottoscrizione di Azure. Seguire le istruzioni illustrate in [Configurazione di Azure per la gestione basata su runbook](http://aka.ms/azureautomationauthentication) per configurare la sottoscrizione di Azure per l'uso di questi cmdlet.  
2. Per altre informazioni sulle funzionalità di Automazione di Azure, fare riferimento alla sezione [Risorse](#resources) di seguito.
3. Effettuare la sottoscrizione al [blog di Automazione di Azure](http://azure.microsoft.com/blog/tag/azure-automation) per essere aggiornati sulle ultime novità del team di Automazione di Azure.

## <a name="resources"></a>Risorse

Sono disponibili numerose risorse per approfondire la conoscenza di Automazione di Azure e creare runbook personalizzati.

- La sezione dedicata ad [Automazione di Azure di MSDN Library](http://go.microsoft.com/fwlink/p/?LinkId=392860) include la documentazione completa sulla configurazione e l'amministrazione di Automazione di Azure e sulla creazione di runbook personalizzati. 
- In [Cmdlet di Azure PowerShell](http://msdn.microsoft.com/library/jj156055.aspx) vengono fornite informazioni sulle operazioni di automazione di Azure con Windows PowerShell. Questi cmdlet vengono usati nei runbook per lavorare con le risorse di Azure.
- Nel [blog su Automazione di Azure](http://azure.microsoft.com/blog/tag/azure-automation) vengono fornite informazioni aggiornate su Automazione di Azure rilasciate da Microsoft.
- Il [forum su Automazione](http://go.microsoft.com/fwlink/p/?LinkId=390561) consente di pubblicare domande su Automazione di Azure a cui verrà data risposta da Microsoft e dalla community di Automazione.


## Esempi e runbook di utilità

Microsoft e la community di Automazione di Azure mettono a disposizione runbook di esempio, utili per iniziare a creare soluzioni personalizzate, nonché runbook di utilità, utili come componenti di base per attività di automazione più estese. È possibile scaricare questi runbook da [Script Center](http://go.microsoft.com/fwlink/p/?LinkId=393029) o importarli direttamente in Automazione di Azure tramite la [raccolta di runbook](http://aka.ms/runbookgallery).
  

## Commenti e suggerimenti

<strong>Gli utenti sono invitati a fornire commenti e suggerimenti.</strong> Se si è in cerca di una soluzione runbook o di un modulo di integrazione di Automazione di Azure, inviare una richiesta di script in Script Center. In caso di commenti o suggerimenti oppure di richieste di funzionalità per Automazione di Azure, è possibile pubblicarle nell'apposito [forum](http://feedback.windowsazure.com/forums/34192--general-feedback). Grazie.

<!---HONumber=52-->
