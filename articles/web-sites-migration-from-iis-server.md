<properties 
	pageTitle="Esecuzione della migrazione di siti Web IIS a Siti Web di Azure usando Migration Assistant" 
	description="Illustra come usare Migration Assistant di Siti Web di Azure per eseguire rapidamente la migrazione di siti Web IIS esistenti in Siti Web di Azure" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2014" 
	ms.author="cephalin"/>

# Eseguire la migrazione di siti Web IIS a Siti Web di Azure usando Migration Assistant #
È possibile eseguire facilmente la migrazione a Siti Web di Azure dei siti Web esistenti che vengono eseguiti su Internet Information Service (IIS) 6 o versioni successive. [Migration Assistant di Siti Web di Azure](https://www.movemetothecloud.net/) è in grado di analizzare l'installazione del server IIS, identificare di quali siti è possibile eseguire la migrazione a Siti Web di Azure, evidenziare eventuali elementi dei quali non è possibile eseguire la migrazione o che non sono supportati sulla piattaforma e quindi eseguire la migrazione dei propri siti Web e dei database associati in Azure.

>[AZURE.NOTE] Il supporto per Windows Server 2003 terminerà il 14 luglio 2015. Se i siti Web sono attualmente eseguiti su un server IIS che è Windows Server 2003, Siti Web di Azure è una soluzione a basso rischio, a costo contenuto e ad attrito ridotto per mantenere i propri siti Web online, mentre Migration Assistant può aiutare ad automatizzare il processo di migrazione per conto dell'utente. 

## Elementi verificati durante l'analisi della compatibilità ##
Migration Assistant di Siti Web di Azure crea un report di conformità per identificare le potenziali cause dei problemi di blocco che potrebbero impedire la corretta migrazione da IIS in locale a Siti Web di Azure. Alcuni degli elementi chiavi da considerare sono:

-	Associazioni delle porte: Siti Web di Azure supporta solo la porta 80 per HTTP e la porta 443 per il traffico HTTPS. Diverse configurazioni di porta verranno ignorate e il traffico verrà indirizzato alla porta 80 o 443. 
-	Autenticazione: Siti Web di Azure supporta l'autenticazione anonima per impostazione predefinita e l'autenticazione basata su form laddove specificato da un'applicazione. L'autenticazione di Windows può essere usata eseguendo l'integrazione solo con Azure Active Directory e ADFS. Tutte le altre forme di autenticazione, ad esempio l'autenticazione di base, non sono attualmente supportate. 
-	Global Assembly Cache (GAC): GAC non è supportato in Siti Web di Azure. Se l'applicazione fa riferimento ad assembly che in genere si distribuiscono nella GAC, sarà necessario distribuirli alla cartella bin dell'applicazione in Siti Web di Azure. 
-	Modalità di compatibilità IIS5: non supportata in Siti Web di Azure. 
-	Pool di applicazioni: in Siti Web di Azure, ogni sito e le applicazioni figlio vengono eseguiti nello stesso pool di applicazioni. Se il sito ha più applicazioni figlio che usano più pool di applicazioni, consolidarle in un singolo pool di applicazioni con impostazioni comuni o eseguire la migrazione di ogni applicazione in un sito Web separato.
-	Componenti COM: Siti Web di Azure non consente la registrazione di componenti COM sulla piattaforma. Se le applicazioni o i siti Web si avvalgono di tutti i componenti COM, è necessario riscriverli nel codice gestito e distribuirli con il sito Web o l'applicazione.
-	Filtri ISAPI: Siti Web di Azure può supportare l'utilizzo dei filtri ISAPI. È necessario eseguire le operazioni seguenti:
	-	distribuire le DLL con il sito Web 
	-	registrare le DLL usando [Web.config](http://www.iis.net/configreference/system.webserver/isapifilters)
	-	inserire un file applicationHost.xdt nella radice del sito con il contenuto seguente:

			<?xml version="1.0"?>
			<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
			<configSections>
			    <sectionGroup name="system.webServer">
			      <section name="isapiFilters" xdt:Transform="SetAttributes(overrideModeDefault)" overrideModeDefault="Allow" />
			    </sectionGroup>
			  </configSections>
			</configuration>

		Per altri esempi di utilizzo del formato XML Document Transformations con il proprio sito Web, vedere come [trasformare il proprio sito Web di Microsoft Azure](http://blogs.msdn.com/b/waws/archive/2014/06/17/transform-your-microsoft-azure-web-site.aspx).

-	Non verrà eseguita la migrazione di altri componenti, come SharePoint, le estensioni FPSE, FTP e i certificati SSL.

## Come usare Migration Assistant di Siti Web di Azure ##
Questa sezione prende in esame un esempio di migrazione di alcuni siti Web che usano un server di database SQL in esecuzione su un computer Windows Server 2003 R2 (IIS 6.0) locale:

1.	Nel server IIS o nel computer client passare a [https://www.movemetothecloud.net/](https://www.movemetothecloud.net/) 

	![](./media/web-sites-migration-from-iis-server/migration-tool-homepage.png)

2.	Installare Migration Assistant di Siti Web di Azure facendo clic sul pulsante **Dedicated IIS Server**. Altre opzioni saranno disponibili nel prossimo futuro. 
4.	Fare clic sul pulsante **Install Tool** per installare Migration Assistant di Siti Web di Azure sul computer.

	![](./media/web-sites-migration-from-iis-server/install-page.png)

	>[AZURE.NOTE] È anche possibile fare clic su **Download for offline install** per scaricare un file ZIP per eseguire l'installazione sui server non connessi a Internet. In alternativa, fare clic su **Upload an existing migration readiness report**, un'opzione avanzata che consente di usare un report di conformità della migrazione generato in precedenza (illustrato più avanti in questo articolo).

5.	Nella schermata **Application Install** fare clic su **Install** per eseguire l'installazione sul computer. Verranno installate anche le dipendenze corrispondenti come distribuzione Web, DacFX e IIS, se necessario. 

	![](./media/web-sites-migration-from-iis-server/install-progress.png)

	Una volta installato, Migration Assistant di Siti Web di Azure viene avviato automaticamente.
  
6.	Scegliere **Migrate sites and databases from a remote server to Azure**. Immettere le credenziali amministrative per il server remoto e fare clic su **Continue**. 

	![](./media/web-sites-migration-from-iis-server/migrate-from-remote.png)

	Naturalmente, è possibile scegliere di eseguire la migrazione dal server locale. L'opzione remota è utile quando si vuole eseguire la migrazione di siti Web da un server IIS di produzione.
 
	A questo punto, l'utilità di migrazione analizzerà la configurazione del server IIS, quali siti, applicazioni, pool di applicazioni e dipendenze, allo scopo di identificare i siti Web di candidati alla migrazione. 

8.	Nella schermata seguente sono mostrati tre siti Web: **Default Web Site**, **TimeTracker** e **CommerceNet4**. A tutti e tre è associato un database di cui si vuole eseguire la migrazione. Selezionare tutti i siti che si vogliono valutare, quindi fare clic su **Avanti**.

	![](./media/web-sites-migration-from-iis-server/select-migration-candidates.png)
 
9.	Fare clic su **Upload** per caricare il report di conformità. Se si sceglie **save file locally**, sarà possibile eseguire nuovamente l'utilità di migrazione in un secondo momento per caricare il report di conformità salvato, come indicato in precedenza.

	![](./media/web-sites-migration-from-iis-server/upload-readiness-report.png)
 
	Dopo aver caricato il report di conformità, Azure esegue l'analisi di conformità e vengono illustrati i risultati. Leggere i dettagli di valutazione per ogni sito Web e assicurarsi di comprendere o che siano stati risolti tutti i problemi prima di procedere. 
 
	![](./media/web-sites-migration-from-iis-server/readiness-assessment.png)

12.	Fare clic su **Begin Migration** per avviare la migrazione. Si verrà reindirizzati ad Azure per accedere al proprio account. È importante accedere con un account con una sottoscrizione di Azure attiva. Se non si ha un account Azure, è possibile iscriversi alla versione di valutazione gratuita qui. 

13.	Selezionare l'account tenant, la sottoscrizione di Azure e l'area da usare per i siti Web e i database di Azure migrati, quindi fare clic su **Start Migration**. È possibile selezionare i siti Web di cui eseguire la migrazione in un secondo momento.

	![](./media/web-sites-migration-from-iis-server/choose-tenant-account.png)

14.	Nella schermata successiva è possibile apportare modifiche alle impostazioni di migrazione predefinite, ad esempio:

	- usare un database SQL di Azure esistente o crearne uno nuovo e configurarne le credenziali
	- selezionare i siti Web di cui eseguire la migrazione
	- definire i nomi per i siti Web di Azure e i database SQL collegati
	- personalizzare le impostazioni globali e a livello di sito

	La schermata seguente mostra tutti i siti Web selezionati per la migrazione con le impostazioni predefinite.

	![](./media/web-sites-migration-from-iis-server/migration-settings.png)

	>[AZURE.NOTE] la casella di controllo **Enable Azure Active Directory** nelle impostazioni personalizzate si integra con il sito Web di Azure con [Azure Active Directory](http://azure.microsoft.com/it-it/documentation/articles/active-directory-whatis/) (la **directory predefinita**). Per ulteriori informazioni sulla sincronizzazione di Azure Active Directory con Active Directory locale, vedere [Integrazione di directory](http://msdn.microsoft.com/library/jj573653).

16.	 Dopo aver apportato tutte le modifiche desiderate, fare clic su **Create** per avviare il processo di migrazione. Lo strumento di migrazione creerà il database SQL di Azure e il sito Web di Azure e quindi pubblicherà il contenuto del sito Web e i database. L'avanzamento della migrazione è chiaramente indicato nello strumento di migrazione e sarà visualizzata una schermata di riepilogo alla fine, che include dettagli sui siti di cui è stata eseguita la migrazione, l'esito della migrazione e collegamenti ai siti Web di Azure appena creati. 

	Se si verifica un errore durante la migrazione, lo strumento di migrazione indicherà chiaramente l'errore ed eseguirà il rollback delle modifiche. Sarà anche possibile inviare il report degli errori direttamente al team di progettazione facendo clic su **Send Error Report** con lo stack di chiamate acquisito e il corpo del messaggio di compilazione. 

	![](./media/web-sites-migration-from-iis-server/migration-error-report.png)

	Se la migrazione avviene senza errori, fare clic su **Give Feedback** per fornire direttamente eventuali commenti e suggerimenti. 
 
20.	Fare clic sui collegamenti ai siti Web di Azure per verificare che la migrazione sia riuscita.




<!--HONumber=42-->
