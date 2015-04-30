<properties 
	pageTitle="Creazione di una presenza Web globale nelle app Web di Servizio app di Azure" 
	description="Questa guida fornisce informazioni tecniche generali su come ospitare il sito dell'organizzazione (.COM) nelle app Web di Servizio app di Azure. Sono inclusi la distribuzione, i domini personalizzati, SSL e il monitoraggio." 
	editor="jimbe" 
	manager="wpickett" 
	authors="cephalin" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="cephalin"/>


# Creazione di una presenza Web globale nelle app Web di Servizio app di Azure

Le app Web di [Servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) dispongono di tutte le funzionalità necessarie per stabilire una presenza Web globale per il proprio sito .COM. Ogni organizzazione, indipendentemente dalle dimensioni, necessita di una piattaforma solida, sicura e scalabile per migliorare le prestazioni aziendali, la consapevolezza del marchio e le comunicazioni con i clienti. Le app Web di Servizio app di Azure consentono di gestire l'identità e il marchio aziendale con la continuità garantita da Microsoft.

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Di seguito è riportato un esempio di un sito Web .COM in esecuzione nelle app Web di Servizio app di Azure. L'esempio illustra le attività che è possibile eseguire con investimenti tecnici minimi semplicemente componendo le app Web con altri servizi. **Fare clic su un elemento nella topografia per visualizzare altre informazioni su di esso.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]
> Questa guida illustra alcune delle aree e delle attività più comuni relative all'esecuzione di un sito .COM pubblico nelle app Web di Servizio app di Azure. Esistono tuttavia altre soluzioni comuni che è possibile implementare nelle app Web di Servizio app di Azure. Per informazioni dettagliate su tali soluzioni, vedere le guide relative alle [campagne di marketing digitali](web-sites-digital-marketing-application-solution-overview.md) e alle [applicazioni aziendali](web-sites-business-application-solution-overview.md).

## Creare da zero o recuperare asset esistenti

È possibile creare rapidamente nuovi siti da un CMS molto usato disponibile nella raccolta o spostare nelle app Web di Servizio app di Azure gli asset Web esistenti da diversi linguaggi e framework.

Azure Marketplace fornisce modelli di noti sistemi di gestione dei contenuti (CMS) di siti Web, ad esempio [Orchard], [Umbraco], [Drupal] e [WordPress]. È possibile creare un'app Web usando il CMS desiderato. In base alle proprie esigenze è possibile scegliere tra diversi back-end di database, tra cui il [database SQL di Azure] e [MySQL].

Le app Web di Servizio app di Azure supportano l'esecuzione di asset Web esistenti scritti in .NET, PHP, Java, Node.js o Python. Il trasferimento nelle app Web può essere effettuato mediante strumenti [FTP] con cui si ha già familiarità o il sistema di gestione del controllo del codice sorgente di cui si dispone. Le app Web supportano la pubblicazione diretta dalle opzioni di controllo del codice sorgente più diffuse, ad esempio [Visual Studio], [Visual Studio Online] e [Git] (locale, GitHub, BitBucket, DropBox, Mercurial e così via).

## Pubblicare in modo affidabile

Le app Web consentono di pubblicare un sito Web in modo affidabile, mediante la pubblicazione continua direttamente dal sistema di controllo del codice sorgente esistente e la verifica in diretta del contenuto. 

Durante la pianificazione, la creazione di prototipi e le fasi iniziali dello sviluppo di un sito, è possibile visualizzare versioni funzionanti del sito Web prima che venga attivato tramite la [distribuzione in uno slot di gestione temporanea] nelle app Web di Servizio app di Azure. Grazie all'integrazione del controllo del codice sorgente con le app Web, è possibile effettuare la [pubblicazione continua] in uno slot di gestione temporanea e, quando lo si desidera, passare all'ambiente di produzione senza tempi di inattività. Se si verifica un errore nel sito di produzione, è anche possibile tornare immediatamente a una versione precedente del sito. 

Inoltre, durante la pianificazione di modifiche da apportare a un sito Web attivo, è possibile effettuare in tutta semplicità l'[esecuzione dei test A/B] degli aggiornamenti proposti tramite la funzionalità di testing dell'ambiente di produzione e analizzare il comportamento dell'utente reale per prendere decisioni informate sulla struttura del sito.

## Personalizzare e proteggere

È possibile usare gratuitamente un dominio delle app Web di Servizio app di Azure o eseguire il mapping al nome di dominio registrato e proteggere il proprio marchio con il certificato SSL firmato da un'Autorità di certificazione.

Quando si esegue il proprio sito Web nelle app Web, il dominio **\*.azurewebsites.net** è gratuito. In alternativa, è possibile eseguire il mapping del sito Web a un [dominio personalizzato] (ad esempio, contoso.com), ottenuto da qualsiasi registro DNS, ad esempio GoDaddy.

Se si raccolgono informazioni sugli utenti, si pratica l'e-commerce o si gestiscono altri dati di natura sensibile, è possibile proteggere l'immagine del marchio e i propri clienti mediante [HTTPS]. Il nome di dominio **\*.azurewebsites.net** è già dotato di un certificato SSL e, se si usa un dominio personalizzato, è possibile spostare il relativo certificato SSL nelle app Web. A ogni certificato SSL è associata una tariffa mensile (ripartita su base oraria). Per altre informazioni, vedere la pagina relativa ai [dettagli sui prezzi di Servizio app di Azure].

## Operare a livello internazionale

È possibile operare in tutto il mondo gestendo siti regionali con Gestione traffico di Azure e fornire contenuti molto rapidamente con la rete CDN di Azure.

Per servire i clienti internazionali nelle rispettive aree geografiche di appartenenza, usare [Gestione traffico di Azure] per instradare i visitatori del sito a un sito regionale in grado di fornire prestazioni ottimali. In alternativa, è possibile distribuire uniformemente il carico del sito in più copie del sito Web ospitate in più aree.

È possibile inoltre fornire rapidamente contenuti statici agli utenti a livello globale tramite l'[integrazione dell'app Web con la rete CDN di Azure]. In questo modo, il contenuto statico viene memorizzato nella cache nel [nodo della rete CDN] più vicino all'utente, riducendo al minimo la latenza e le connessioni al sito Web.

## Eseguire l'ottimizzazione

È possibile ottimizzare il sito .COM grazie al ridimensionamento automatico mediante Scalabilità automatica, la memorizzazione nella cache con Cache Redis di Azure, l'esecuzione di attività in background tramite Processi Web e il mantenimento della disponibilità elevata con Gestione traffico di Azure.

La funzionalità di [scalabilità verticale e orizzontale] delle app Web di Servizio app di Azure soddisfa le esigenze dei siti .COM, indipendentemente dalle dimensioni del carico di lavoro. È possibile scalare in orizzontale il sito Web manualmente tramite il [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715), a livello di codice tramite l'[API di gestione del servizio] o gli [script di PowerShell] oppure automaticamente tramite la funzionalità Scalabilità automatica. Nel piano di hosting **Standard** tale funzionalità consente di scalare in orizzontale un sito Web automaticamente in base all'uso della CPU. Per le procedure consigliate, vedere l'articolo di [Troy Hunt] relativo ai [10 elementi appresi per implementare rapidamente la scalabilità delle app Web con Azure].

Aumentare la velocità di risposta del sito Web con la [Cache Redis di Azure]. Usare questa funzionalità per memorizzare nella cache i dati dei database back-end e altri elementi come lo [stato della sessione ASP.NET] e la [cache di output].

Mantenere un'elevata disponibilità del sito Web mediante [Gestione traffico di Azure]. Tramite il metodo **Failover**, Gestione traffico instrada automaticamente il traffico a un sito secondario se si verifica un problema nel sito primario.

## Eseguire il monitoraggio e l'analisi

Tramite Azure o strumenti di terze parti è possibile mantenersi aggiornati sulle prestazioni del sito Web e ricevere avvisi su eventi critici nel sito. Inoltre, con Application Insights o mediante l'analisi dei log Web di HDInsight, è possibile ottenere facilmente informazioni dettagliate sugli utenti. 

Mediante il pannello dell'app Web nel [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715), è anche possibile ottenere un [riepilogo rapido] delle metriche delle prestazioni correnti del sito Web e delle quote relative alle risorse. Per informazioni complete sulla disponibilità, le prestazioni e l'uso dell'applicazione, usare [Azure Application Insights], che offre potenti e rapidi strumenti per la risoluzione dei problemi, la diagnostica e il recupero delle informazioni sull'uso. In alternativa, usare uno strumento di terze parti come [New Relic] per fornire dati di monitoraggio avanzati per i siti Web.

Nel piano di hosting **Standard**, grazie al monitoraggio della velocità di risposta, è possibile ricevere notifiche via e-mail ogni volta che il sito smette di rispondere. Per altre informazioni, vedere [Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure].

## Usare contenuti multimediali elaborati e raggiungere tutti i dispositivi

È possibile rendere più interessante il sito .COM con contenuti multimediali elaborati, ad esempio:

-  Caricare e trasmettere in streaming video a livello globale con [Servizi multimediali di Azure].
-  Inviare e-mail agli utenti con il [servizio SendGrid disponibile in Azure Marketplace].

## Altre risorse

- [Documentazione relativa alle app Web di Servizio app di Azure](/services/app-service/web/)
- [Mappa di formazione per app Web di Servizio app di Azure](websites-learning-map.md)
- [Blog Web di Azure](/blog/topics/web/)

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere: [Servizio app di Azure e relativo impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida sul passaggio dal vecchio al nuovo portale, vedere: [Informazioni di riferimento per l'esplorazione del portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

[Servizio app di Azure]: /services/app-service/web/

[Orchard]:web-sites-dotnet-orchard-cms-gallery.md
[Umbraco]:web-sites-gallery-umbraco.md
[Drupal]:web-sites-php-migrate-drupal.md
[WordPress]:web-sites-php-web-site-gallery.md
[MySQL]:web-sites-php-mysql-deploy-use-git.md
[database SQL di Azure]:web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[FTP]:web-sites-deploy.md#ftp
[Visual Studio]:web-sites-dotnet-get-started.md
[Visual Studio Online]:cloud-services-continuous-delivery-use-vso.md
[Git]:web-sites-publish-source-control.md

[distribuzione in uno slot di gestione temporanea]:web-sites-staged-publishing.md 
[pubblicazione continua]:http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[esecuzione dei test A/B]:http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[dominio personalizzato]:web-sites-custom-domain-name.md
[HTTPS]:web-sites-configure-ssl-certificate.md
[dettagli sui prezzi di Servizio app di Azure]: /pricing/details/app-service/#ssl-connections

[Gestione traffico di Azure]:http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[integrazione dell'app Web con la rete CDN di Azure]:cdn-websites-with-cdn.md 
[nodo della rete CDN]:https://msdn.microsoft.com/library/azure/gg680302.aspx

[scalabilità verticale e orizzontale]:web-sites-scale.md
[Portale di gestione di Azure]:http://manage.windowsazure.com/
[API di gestione del servizio]:https://msdn.microsoft.com/library/azure/ee460799.aspx
[script di PowerShell]:https://msdn.microsoft.com/library/azure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[10 elementi appresi per implementare rapidamente la scalabilità delle app Web con Azure]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Cache Redis di Azure]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[stato della sessione ASP.NET]:https://msdn.microsoft.com/library/azure/dn690522.aspx
[cache di output]:https://msdn.microsoft.com/library/azure/dn798898.aspx

[riepilogo rapido]:web-sites-monitor.md
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:store-new-relic-cloud-services-dotnet-application-performance-management.md
[Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

[Servizi multimediali di Azure]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[servizio SendGrid disponibile in Azure Marketplace]:sendgrid-dotnet-how-to-send-email.md


<!--HONumber=52-->