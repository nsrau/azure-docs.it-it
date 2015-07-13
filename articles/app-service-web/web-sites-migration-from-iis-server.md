<properties 
	pageTitle="Migrazione di un'app Web aziendale a un servizio app di Azure" 
	description="Illustra come usare il Migration Assistant di App Web per eseguire rapidamente la migrazione dei siti Web IIS esistenti al servizio app per app Web" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Migrazione di un'app Web aziendale a un servizio app di Azure

È possibile eseguire facilmente la migrazione dei siti Web esistenti che vengono eseguiti su Internet Information Service (IIS) 6 o versioni successive al [servizio app per app Web](http://go.microsoft.com/fwlink/?LinkId=529714). [Migration Assistant di App Web](https://www.movemetothecloud.net/) può analizzare l'installazione del server IIS, identificare di quali siti è possibile eseguire la migrazione al servizio app, evidenziare eventuali elementi dei quali non è possibile eseguire la migrazione o che non sono supportati sulla piattaforma e quindi eseguire la migrazione dei propri siti Web e dei database associati in Azure.

>[AZURE.NOTE]Il supporto per Windows Server 2003 terminerà il 14 luglio 2015. Se i siti Web sono attualmente eseguiti su un server IIS che è Windows Server 2003, le app Web rappresentano una soluzione a basso rischio, a costo contenuto e ad attrito ridotto per mantenere i propri siti Web online, mentre Migration Assistant di App Web può aiutare ad automatizzare il processo di migrazione per conto dell'utente.

## Elementi verificati durante l'analisi della compatibilità ## ##
Il Migration Assistant crea un report di conformità per identificare le potenziali cause dei problemi di blocco che potrebbero impedire la corretta migrazione da IIS in locale al servizio app per app Web di Azure. Alcuni degli elementi chiavi da considerare sono:

-	Associazioni delle porte: App Web supporta solo la porta 80 per il traffico HTTP e la porta 443 per quello HTTPS. Diverse configurazioni di porta verranno ignorate e il traffico verrà indirizzato alla porta 80 o 443. 
-	Autenticazione: App Web supporta l'autenticazione anonima per impostazione predefinita e l'autenticazione basata su form laddove specificato da un'applicazione. L'autenticazione di Windows può essere usata eseguendo l'integrazione solo con Azure Active Directory e ADFS. Tutte le altre forme di autenticazione, come ad esempio l'autenticazione di base, al momento non sono supportate. 
-	Global Assembly Cache (GAC) – Una GAC non è supportata in App Web. Se l'applicazione fa riferimento ad assembly che in genere si distribuiscono nella GAC, sarà necessario distribuirli alla cartella bin dell'applicazione nelle app Web. 
-	Modalità di compatibilità IIS5: non supportata in App Web. 
-	Pool di applicazioni: in App Web, ogni sito e le applicazioni figlio vengono eseguiti nello stesso pool di applicazioni. Se il sito ha più applicazioni figlio che usano più pool di applicazioni, consolidarle in un singolo pool di applicazioni con impostazioni comuni o eseguire la migrazione di ogni applicazione in un'app Web separata.
-	Componenti COM: App Web non consente la registrazione di componenti COM sulla piattaforma. Se le applicazioni o i siti Web si avvalgono di tutti i componenti COM, è necessario riscriverli nel codice gestito e distribuirli con il sito Web o l'applicazione.
-	Filtri ISAPI: App Web può supportare l'uso di filtri ISAPI. È necessario eseguire le operazioni seguenti:
	-	distribuire le DLL con l'app Web 
	-	registrare le DLL con [Web.config](http://www.iis.net/configreference/system.webserver/isapifilters)
	-	inserire un file applicationHost.xdt nella radice del sito con il contenuto seguente:

			<?xml version="1.0"?>
			<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
			<configSections>
			    <sectionGroup name="system.webServer">
			      <section name="isapiFilters" xdt:Transform="SetAttributes(overrideModeDefault)" overrideModeDefault="Allow" />
			    </sectionGroup>
			  </configSections>
			</configuration>

		Per altri esempi di utilizzo del formato XML Document Transformations con il proprio sito Web, vedere [Trasformare il proprio sito Web di Microsoft Azure](http://blogs.msdn.com/b/waws/archive/2014/06/17/transform-your-microsoft-azure-web-site.aspx).

-	Non verrà eseguita la migrazione di altri componenti, come SharePoint, le estensioni FPSE, FTP e i certificati SSL.

## Come usare Migration Assistant di App Web ## ##
Questa sezione prende in esame un esempio di migrazione di alcuni siti Web che usano un server di database SQL in esecuzione su un computer Windows Server 2003 R2 (IIS 6.0) locale:

1.	Nel server IIS o nel computer client andare a [https://www.movemetothecloud.net/](https://www.movemetothecloud.net/) 

	![](./media/web-sites-migration-from-iis-server/migration-tool-homepage.png)

2.	Installare il Migration Assistant di App Web facendo clic sul pulsante **Server IIS dedicato**. Altre opzioni saranno disponibili nel prossimo futuro.
4.	Fare clic sul pulsante **Installa strumento** per installare Migration Assistant di App Web nel computer.

	![](./media/web-sites-migration-from-iis-server/install-page.png)

	>[AZURE.NOTE]È anche possibile fare clic su **Download per installazione offline** per scaricare un file ZIP per l'installazione su server non connessi a Internet. In alternativa, fare clic su **Carica un report di conformità di una migrazione esistente**, che è un'opzione avanzata per usare un report di conformità di una migrazione esistente generato in precedenza (illustrato più avanti in questo articolo).

5.	Nella schermata **Installazione applicazione**, fare clic su **Installa** per avviare l'installazione nel computer. Verranno installate anche le dipendenze corrispondenti come distribuzione Web, DacFX e IIS, se necessario.

	![](./media/web-sites-migration-from-iis-server/install-progress.png)

	Una volta installato, Migration Assistant di App Web viene avviato automaticamente.
  
6.	Scegliere **Esegui la migrazione di siti e database da un server remoto ad Azure**. Immettere le credenziali amministrative del server remoto e fare clic su **Continua**.

	![](./media/web-sites-migration-from-iis-server/migrate-from-remote.png)

	Naturalmente, è possibile scegliere di eseguire la migrazione dal server locale. L'opzione remota è utile quando si vuole eseguire la migrazione di siti Web da un server IIS di produzione.
 
	A questo punto, l'utilità di migrazione analizzerà la configurazione del server IIS, quali siti, applicazioni, pool di applicazioni e dipendenze, allo scopo di identificare i siti Web di candidati alla migrazione.

8.	La seguente screenshot mostra tre siti Web: **sito Web predefinito**, **TimeTracker** e **CommerceNet4**. A tutti e tre è associato un database di cui si vuole eseguire la migrazione. Selezionare tutti i siti che si voglio valutare, quindi fare clic su **Avanti**.

	![](./media/web-sites-migration-from-iis-server/select-migration-candidates.png)
 
9.	Fare clic su **Carica** per caricare il report di conformità. Se si sceglie di **salvare il file in locale**, è possibile eseguire lo strumento di migrazione in un secondo momento per caricare il report di conformità salvato, come indicato in precedenza.

	![](./media/web-sites-migration-from-iis-server/upload-readiness-report.png)
 
	Dopo aver caricato il report di conformità, Azure esegue l'analisi di conformità e vengono illustrati i risultati. Leggere i dettagli di valutazione per ogni sito Web e assicurarsi di comprendere o che siano stati risolti tutti i problemi prima di procedere.
 
	![](./media/web-sites-migration-from-iis-server/readiness-assessment.png)

12.	Fare clic su **Inizia la migrazione** per avviare la migrazione. Si verrà reindirizzati ad Azure per accedere al proprio account. È importante accedere con un account con una sottoscrizione di Azure attiva. Se non si ha un account di Azure, è possibile iscriversi alla versione di valutazione gratuita qui.

13.	Selezionare l'account tenant, la sottoscrizione di Azure e l'area da usare per le app Web e i database di Azure migrati, quindi fare clic su **Avvia la migrazione**. È possibile selezionare i siti Web di cui eseguire la migrazione in un secondo momento.

	![](./media/web-sites-migration-from-iis-server/choose-tenant-account.png)

14.	Nella schermata successiva è possibile apportare modifiche alle impostazioni di migrazione predefinite, ad esempio:

	- usare un database SQL di Azure esistente o crearne uno nuovo e configurarne le credenziali
	- selezionare i siti Web di cui eseguire la migrazione
	- definire i nomi per le app Web di Azure e i relativi database SQL collegati
	- personalizzare le impostazioni globali e a livello di sito

	La schermata seguente mostra tutti i siti Web selezionati per la migrazione con le impostazioni predefinite.

	![](./media/web-sites-migration-from-iis-server/migration-settings.png)

	>[AZURE.NOTE]la casella di controllo **Enable Azure Active Directory** nelle impostazioni personalizzate si integra con il sito Web di Azure con [Azure Active Directory](active-directory-whatis.md) (la **directory predefinita**). Per altre informazioni sulla sincronizzazione di Azure Active Directory con l'Active Directory locale, vedere [Integrazione directory](http://msdn.microsoft.com/library/jj573653).

16.	 Dopo aver apportato tutte le modifiche desiderate, fare clic su **Crea** per avviare il processo di migrazione. Lo strumento di migrazione creerà il database SQL di Azure e l'app Web di Azure e quindi pubblicherà il contenuto del sito Web e i database. L'avanzamento della migrazione è chiaramente indicato nello strumento di migrazione e sarà visualizzata una schermata di riepilogo alla fine, che include dettagli sui siti di cui è stata eseguita la migrazione, l'esito della migrazione e collegamenti alle app Web di Azure appena create.

	Se si verifica un errore durante la migrazione, lo strumento di migrazione indicherà chiaramente l'errore ed eseguirà il rollback delle modifiche. Sarà anche possibile inviare il report degli errori direttamente al team di progettazione facendo clic sul pulsante **Invia segnalazione errori**, con lo stack di chiamate acquisito e il corpo del messaggio di compilazione.

	![](./media/web-sites-migration-from-iis-server/migration-error-report.png)

	Se la migrazione avviene senza errori, fare clic su **Invia commenti** e suggerimenti per fornire direttamente eventuali commenti e suggerimenti.
 
20.	Fare clic sui collegamenti alle app Web di Azure per verificare che la migrazione sia riuscita.

21. È ora possibile gestire le app Web migrate nel servizio app di Azure. A tal scopo, accedere al [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

22. Nel portale di Azure, aprire il pannello App Web per visualizzare i siti Web migrati (mostrati come app Web), quindi fare clic su uno di loro per avviare la gestione dell'app Web, ad esempio per configurare la pubblicazione continua, creare backup, avviare la scalabilità automatica e monitorare l'uso o le prestazioni.

	![](./media/web-sites-migration-from-iis-server/TimeTrackerMigrated.png)

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=62-->