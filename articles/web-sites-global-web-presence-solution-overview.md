<properties 
	pageTitle="Creazione di una presenza Web globale su Siti Web di Azure" 
	description="Questa guida fornisce informazioni tecniche generali su come ospitare il sito dell'organizzazione (.COM) in Siti Web di Azure. Sono inclusi la distribuzione, i domini personalizzati, SSL e il monitoraggio." 
	editor="jimbe" 
	manager="wpickett" 
	authors="cephalin" 
	services="web-sites" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2014" 
	ms.author="cephalin"/>


# Creazione di una presenza Web globale su Siti Web di Azure

[Siti Web di Azure] dispone di tutte le funzionalità necessarie per stabilire una presenza Web globale per un sito .COM. Ogni azienda, indipendentemente dalle dimensioni, ha bisogno di una piattaforma solida, sicura e scalabile per migliorare le prestazioni aziendali, la consapevolezza del marchio e le comunicazioni con i clienti. Siti Web di Azure consente di gestire l'identità e il marchio aziendale con la continuità garantita da Microsoft.

> [AZURE.NOTE] Per iniziare a usare Siti Web di Azure prima di iscriversi per ottenere un account, visitare la pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, in cui è possibile creare immediatamente e gratuitamente un sito di base ASP.NET temporaneo in Siti Web di Azure. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Di seguito è riportato un esempio di un sito Web .COM in esecuzione in Siti Web di Azure. L'esempio illustra le attività che è possibile eseguire con investimenti tecnici minimi semplicemente componendo Siti Web di Azure con altri servizi. **Per altre informazioni su un elemento, selezionarlo nella topografia.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [WACOM.NOTE]
> Questa guida illustra alcune delle aree e delle attività più comuni relative all'esecuzione di un sito .COM in Siti Web di Azure. Esistono tuttavia altre soluzioni comuni che è possibile implementare in Siti Web di Azure. Per informazioni dettagliate su tali soluzioni, vedere le guide relative alle <a href="http://azure.microsoft.com/manage/services/web-sites/digital-marketing-campaign-solution-overview">campagne di marketing digitali</a> e alle <a href="http://azure.microsoft.com/manage/services/web-sites/business-application-solution-overview">applicazioni business</a>.

### Creare siti da zero o recuperare asset esistenti

È possibile creare nuovi siti rapidamente da un CMS diffuso nella raccolta o spostare asset Web esistenti in Siti Web di Azure da diversi linguaggi e Framework.

La raccolta di Azure fornisce modelli di noti sistemi di gestione dei contenuti di siti Web (CMS), ad esempio [Orchard], [Umbraco], [Drupal] e [WordPress]. È possibile creare un sito Web usando il CMS desiderato. In base alle esigenze è possibile scegliere tra diversi database back-end tra cui [Database SQL di Azure] e [MySQL].

Siti Web di Azure supporta l'esecuzione di asset Web esistenti scritti in .NET, PHP, Java, Node. js o Python. Il trasferimento in Siti Web di Azure può essere eseguito mediante gli strumenti [FTP] già noti o il sistema di gestione del controllo del codice sorgente di cui si dispone. Siti Web di Azure supporta la pubblicazione diretta dalle opzioni di controllo del codice sorgente più diffuse, ad esempio [Visual Studio], [Visual Studio Online] e [Git] (locale, GitHub, BitBucket, DropBox, Mercurial e così via).

### Pubblicare in modo affidabile

Siti Web consente di pubblicare un sito Web in modo affidabile, mediante la pubblicazione continua direttamente dal sistema di controllo del codice sorgente esistente e la verifica in diretta del contenuto. 

Durante la pianificazione, la creazione di prototipi e le fasi iniziali dello sviluppo di un sito, è possibile visualizzare versioni funzionanti del sito Web prima che venga attivato tramite la [distribuzione in uno slot di gestione temporanea] del sito Web di Azure. Grazie all'integrazione del controllo del codice sorgente con Siti Web di Azure, è possibile [pubblicazione continua] in uno slot di gestione temporanea e, quando lo si desidera, passare all'ambiente di produzione senza tempi di inattività. Se si verifica un errore nel sito di produzione, è inoltre possibile tornare immediatamente a una versione precedente del sito. 

Inoltre, durante la pianificazione di modifiche da apportare a un sito Web attivo, è possibile [esecuzione dei test A/B] degli aggiornamenti proposti tramite la funzionalità di testing dell'ambiente di produzione e analizzare il comportamento dell'utente reale per prendere decisioni informate sulla struttura del sito.

### Personalizzare e proteggere

È possibile usare gratuitamente un dominio di Siti Web o eseguire il mapping al nome di dominio registrato e proteggere il marchio con il certificato SSL firmato da un'Autorità di certificazione.

Quando si esegue il proprio sito in Siti Web di Azure, il dominio **\*.azurewebsites.net** è gratuito. In alternativa, è possibile eseguire il mapping del sito Web a un [dominio personalizzato] (ad esempio, contoso.com), ottenuto da qualsiasi registro di sistema DNS, ad esempio GoDaddy.

Se si raccolgono informazioni sugli utenti, si pratica l'e-commerce o si gestiscono altri dati di natura sensibile, è possibile proteggere l'immagine del marchio e i propri clienti mediante [HTTPS]. Il nome di dominio **\*.azurewebsites.net** è già dotato di un certificato SSL e, se si usa un dominio personalizzato, è possibile spostare il relativo certificato SSL in Siti Web di Azure. A ogni certificato SSL è associata una tariffa mensile (ripartita su base oraria). Per altre informazioni, vedere la pagina relativa ai [dettagli sui prezzi di Siti Web].

### Espandere i propri orizzonti

È possibile espandere i propri orizzonti usando Gestione traffico di Azure per servire siti regionali e la rete CDN di Azure per fornire rapidamente contenuti.

Per servire i clienti globali nelle rispettive aree, è possibile usare [Gestione traffico di Azure] per indirizzare i visitatori di un sito a un sito regionale che offre prestazioni migliori. In alternativa, è possibile distribuire uniformemente il carico del sito su diverse copie del sito Web ospitate in più aree geografiche.

È possibile inoltre fornire rapidamente contenuti statici agli utenti a livello globale tramite l'[integrazione del sito Web con la rete CDN di Azure]. La rete CDN di Azure memorizza nella cache il contenuto statico nel [nodo della rete CDN] più vicino all'utente, riducendo al minimo la latenza e le connessioni al sito Web.

### Eseguire l'ottimizzazione

È possibile ottimizzare il sito .COM grazie alla scalabilità automatica di Autoscale, la memorizzazione nella cache di Cache Redis di Azure, l'esecuzione di attività in background di WebJobs e il mantenimento della disponibilità elevata di Gestione traffico di Azure.

La funzionalità di [scalabilità verticale e orizzontale] di Siti Web Azure soddisfa le esigenze dei siti .COM, indipendentemente dalle dimensioni del carico di lavoro. È possibile eseguire la scalabilità orizzontale del sito Web manualmente tramite il [portale di gestione di Azure], a livello di codice tramite l'[API di gestione del servizio] o [gli script PowerShell], oppure automaticamente tramite la funzionalità Autoscale. Nel piano di hosting **Standard** Autoscale consente di eseguire la scalabilità orizzontale un sito Web in modo automatico in base all'utilizzo della CPU. Per le procedure consigliate, vedere l'articolo di [Troy Hunt], [10 elementi appresi per scalare rapidamente i siti Web con Azure].

Con [Cache Redis di Azure] è possibile rendere il sito Web più reattivo. Cache Redis è utile per memorizzare i dati da database back-end e altre funzionalità, ad esempio lo [stato della sessione ASP.NET] e la [cache di output].

[Gestione traffico di Azure] consente di mantenere la disponibilità elevata di un sito Web. Tramite il metodo **Failover**, in caso di problemi nel sito primario Gestione traffico indirizza automaticamente il traffico a un sito secondario.

### Monitorare e analizzare

Tramite Azure o strumenti di terze parti è possibile mantenersi aggiornati sulle prestazioni del sito Web e ricevere avvisi su eventi critici nel sito. Inoltre, con Application Insights o mediante l'analisi dei log Web di HDInsight è possibile ottenere facilmente informazioni dettagliate sugli utenti. 

Il dashboard di Siti Web di Azure offre un [riepilogo rapido] delle metriche delle prestazioni e delle quote di risorse correnti del sito Web. Per una visualizzazione a 360° della disponibilità, delle prestazioni e dell'uso dell'applicazione, usare [Azure Application Insights], che fornisce potenti e rapidi strumenti di risoluzione dei problemi, diagnostica e informazioni sull'utilizzo. In alternativa, è possibile usare strumenti di terze parti come [New Relic], che forniscono dati di monitoraggio avanzati per i siti Web.

Con il piano di hosting **Standard** è possibile monitorare i tempi di risposta del sito e ricevere notifiche tramite posta elettronica nel caso in cui il sito non risponda. Per altre informazioni, vedere [Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure].

### Usare contenuti multimediali e raggiungere tutti i dispositivi

È possibile rendere più interessante il sito .COM con contenuti multimediali, ad esempio:

-  Caricare ed eseguire lo streaming di video a livello globale con i [servizi multimediali di Azure]
-  Inviare messaggi di posta elettronica agli utenti mediante il [servizio SendGrid in Azure Marketplace]

## Altre risorse

- [Documentazione di Siti Web di Azure](/it-it/documentation/services/websites/)
- [Mappa di formazione per Siti Web di Azure](/it-it/documentation/articles/websites-learning-map/)
- [Blog Web di Azure](/blog/topics/web/)



[Siti Web di Azure]:/it-it/services/websites/

[Orchard]:/it-it/documentation/articles/web-sites-dotnet-orchard-cms-gallery/
[Umbraco]:/it-it/documentation/articles/web-sites-gallery-umbraco/
[Drupal]:/it-it/documentation/articles/web-sites-php-migrate-drupal/
[WordPress]:/it-it/documentation/articles/web-sites-php-web-site-gallery/
[MySQL]:/it-it/documentation/articles/web-sites-php-mysql-deploy-use-git/
[Database SQL di Azure]:/it-it/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
[FTP]:/it-it/documentation/articles/web-sites-deploy/#ftp
[Visual Studio]:/it-it/documentation/articles/web-sites-dotnet-get-started/
[Visual Studio Online]:/it-it/documentation/articles/cloud-services-continuous-delivery-use-vso/
[Git]:/it-it/documentation/articles/web-sites-publish-source-control/

[distribuzione in uno slot di gestione temporanea]:/it-it/documentation/articles/web-sites-staged-publishing/ 
[pubblicazione continua]:http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[esecuzione dei test A/B]:http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[dominio personalizzato]:/it-it/documentation/articles/web-sites-custom-domain-name/
[HTTPS]:/it-it/documentation/articles/web-sites-configure-ssl-certificate/
[Dettagli sui prezzi di Siti Web]:/it-it/pricing/details/web-sites/#service-ssl

[Gestione traffico di Azure]:http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[integrazione del sito Web con la rete CDN di Azure]:/it-it/documentation/articles/cdn-websites-with-cdn/ 
[Nodo della rete CDN]:https://msdn.microsoft.com/library/azure/gg680302.aspx

[scalabilità verticale e orizzontale]:/it-it/manage/services/web-sites/how-to-scale-websites/
[Portale di gestione di Azure]:http://manage.windowsazure.com/
[API di gestione del servizio]:http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[Script di PowerShell]:http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[Le 10 cose che ho imparato sulla scalabilità rapida dei siti Web con Azure]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Cache Redis di Azure]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[Stato della sessione ASP.NET]:https://msdn.microsoft.com/it-it/library/azure/dn690522.aspx
[cache di output]:https://msdn.microsoft.com/it-it/library/azure/dn798898.aspx

[riepilogo rapido]:/it-it/manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:/it-it/develop/net/how-to-guides/new-relic/
[Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

[Servizi multimediali di Azure]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Servizio SendGrid in Azure Marketplace]:/it-it/documentation/articles/sendgrid-dotnet-how-to-send-email/



<!--HONumber=42-->
