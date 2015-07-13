<properties 
	pageTitle="Creare una campagna di marketing digitale in App Web di Azure App Service" 
	description="Questa guida fornisce informazioni generali di carattere tecnico su come usare App Web di Azure App Service per creare campagne di marketing digitali. Sono inclusi la distribuzione, l'integrazione di social media, le strategie di scalabilità e il monitoraggio." 
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

# Creare una campagna di marketing digitale in App Web di Azure App Service
App Web di [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) offre una soluzione ottimale per le campagne di marketing digitali. Le campagne di questo tipo in genere sono di breve durata e hanno la funzione di realizzare obiettivi di marketing a breve termine. È necessario prendere in considerazione due scenari principali. Nel primo scenario una società di marketing di terze parti crea e gestisce la campagna per conto del cliente, per l'intera durata della promozione. Nel secondo scenario la società di marketing crea la campagna marketing digitale, quindi trasferisce al cliente la proprietà delle risorse corrispondenti. La campagna di marketing digitale verrà quindi eseguita e gestita autonomamente dal cliente. Siti Web di Azure è la soluzione perfetta in entrambi gli scenari.

>[AZURE.NOTE]Per iniziare a usare Azure App Service prima di registrarsi per ottenere un account Azure, andare a [Prova App Service](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea in App Service. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Di seguito è riportato un esempio di una campagna di marketing digitale multicanale globale con App Web di App Service. L'esempio illustra le attività che è possibile eseguire con investimenti tecnici minimi semplicemente unendo App Web di App Service con altri servizi. **Fare clic su un elemento nella topografia per visualizzare ulteriori informazioni**.

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]Questa guida presenta alcune delle aree e delle attività più comuni relative all'esecuzione di una campagna di marketing digitale in App Web di Azure App Service. Esistono tuttavia altre soluzioni comuni che è possibile implementare in App Web di Azure App Service. Per informazioni dettagliate su queste soluzioni, vedere le guide relative alla [presenza Web globale](web-sites-global-web-presence-solution-overview.md) e alle [applicazioni business](web-sites-business-application-solution-overview.md).

## Creare da zero o recuperare asset esistenti

È possibile creare nuove app Web rapidamente da un CMS di uso comune nella raccolta o trasferire asset Web esistenti nelle app Web di App Service da linguaggi e framework diversi.

Azure Marketplace fornisce i modelli dei sistemi di gestione dei contenuti (CMS) di siti Web di uso comune, ad esempio [Orchard], [Umbraco], [Drupal] e [WordPress]. È possibile creare un'app Web usando il CMS desiderato. In base alle proprie esigenze è possibile scegliere tra diversi back-end di database, tra cui il [database SQL di Azure] e [MySQL].

Le app Web supportano l'esecuzione di asset Web esistenti scritti in .NET, PHP, Java, Node.js o Python. È possibile spostare gli asset in App Web mediante gli strumenti [FTP] con cui si ha familiarità. Se si creano spesso campagne di marketing digitali, è possibile che alcune risorse Web esistenti siano disponibili in un sistema di gestione del controllo del codice sorgente. In App Web è supportata la distribuzione diretta dalle opzioni di controllo del codice sorgente più diffuse, ad esempio [Visual Studio], [Visual Studio Online] e [Git] (locale, GitHub, BitBucket, DropBox, Mercurial e così via).

## Lavorare in modo agile

È possibile lavorare in modo agile pubblicando continuamente i dati direttamente dal controllo del codice sorgente esistente ed eseguire test A/B in App Web di App Service.

Durante la pianificazione, la creazione di un prototipo e le fasi iniziali dello sviluppo di un'app Web, è possibile esaminare con il cliente versioni effettivamente funzionanti dell'app della campagna prima che sia attivata eseguendo la [distribuzione in uno slot di gestione temporanea] dell'app Web. Integrando il controllo del codice sorgente con App Web di App Service, è possibile eseguire la [pubblicazione continua] in uno slot di questo tipo e quindi passare alla fase di produzione senza alcun periodo di inattività quando l'app è pronta.

Inoltre, durante la pianificazione di modifiche da apportare a un'app Web attiva, è possibile [eseguire test A/B] degli aggiornamenti proposti tramite la funzionalità di testing dell'ambiente di produzione e analizzare il comportamento dell'utente reale per prendere decisioni informate sulla progettazione dell'app.


## Integrarsi con i social media

La campagna di marketing digitale in App Web di App Service può essere integrata con i social media mediante l'autenticazione presso noti provider come Facebook e Twitter. Per un esempio di questo metodo con un'applicazione ASP.NET, vedere la pagina relativa alla [creazione di un'app ASP.NET MVC con autenticazione e database SQL e alla distribuzione in Azure App Service].

Inoltre, ogni sito di social media offre in genere informazioni su altre modalità di integrazione da .NET e molti altri framework.

## Usare contenuti multimediali elaborati e raggiungere tutti i dispositivi

È possibile arricchire la propria campagna di marketing digitale con altri servizi di Azure, ad esempio:

-  Caricare e trasmettere in streaming video a livello globale con [Servizi multimediali di Azure].
-  Inviare e-mail agli utenti con il [servizio SendGrid disponibile in Azure Marketplace].
-  Stabilire la presenza in dispositivi Windows, iOS e Android con [Servizi mobili].
-  Inviare una notifica push a milioni di dispositivi con [Hub di notifica].

## Operare a livello internazionale

È possibile operare in tutto il mondo gestendo siti regionali con Gestione traffico di Azure e fornire contenuti molto rapidamente con la rete CDN di Azure.

Per servire i clienti internazionali nelle rispettive aree geografiche di appartenenza, usare [Gestione traffico di Azure] per instradare i visitatori del sito a un sito regionale in grado di fornire prestazioni ottimali. In alternativa, è possibile distribuire uniformemente il carico del sito su diverse copie dell'app Web ospitate in più aree geografiche.

È possibile inoltre fornire rapidamente contenuti statici agli utenti a livello globale tramite l'[integrazione dell'app Web con la rete CDN di Azure]. La rete CDN di Azure memorizza nella cache il contenuto statico nel [nodo della rete CDN] più vicino all'utente, riducendo al minimo la latenza e le connessioni all'app Web.

## Eseguire l'ottimizzazione

Ottimizzare l'applicazione Web scalandola automaticamente mediante Scalabilità automatica, effettuando la memorizzazione nella cache con Cache Redis di Azure, eseguendo le attività in background con Processi Web e mantenendo una disponibilità elevata con Gestione traffico di Azure.

L'implementazione della [scalabilità verticale e orizzontale] offerta da App Web di App Service è ideale per i carichi di lavoro imprevedibili, come nel caso di campagne di marketing digitali. È possibile eseguire la scalabilità orizzontale dell'app Web manualmente [tramite il portale di anteprima di Azure](http://go.microsoft.com/fwlink/?LinkId=529715), a livello di programmazione tramite l'[API di gestione del servizio] o gli [script di PowerShell] oppure automaticamente tramite la funzionalità Scalabilità automatica. Nel livello **Standard**, tale funzionalità consente di implementare la scalabilità orizzontale di un'app Web automaticamente, in base all'uso della CPU. È pertanto possibile operare con la massima agilità e contemporaneamente ridurre al minimo i costi scalando orizzontalmente l'app Web solo quando è necessario secondo l'attività degli utenti. Per le procedure consigliate, vedere l'articolo di [Troy Hunt] relativo ai [10 elementi appresi per implementare rapidamente la scalabilità delle app Web con Azure].

È possibile aumentare la velocità di risposta dell'app Web con [Cache Redis di Azure]. Usare questa funzionalità per memorizzare nella cache i dati dei database back-end e altri elementi come lo [stato della sessione ASP.NET] e la [cache di output].

È possibile mantenere un'elevata disponibilità dell'app Web mediante [Gestione traffico di Azure]. Tramite il metodo **Failover**, Gestione traffico instrada automaticamente il traffico a un sito secondario se si verifica un problema nel sito primario.

## Eseguire il monitoraggio e l'analisi

Tramite Azure o strumenti di terze parti è possibile mantenersi aggiornati sulle prestazioni dell'app Web e ricevere avvisi su eventi critici nell'app Web. Inoltre, con Application Insights o mediante l'analisi dei log Web di HDInsight, è possibile ottenere facilmente informazioni dettagliate sugli utenti.

Il pannello dell'app Web nel portale di Azure offre un [riepilogo rapido] delle metriche delle prestazioni e delle quote delle risorse correnti dell'app Web nel [portale di anteprima di Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Per informazioni complete sulla disponibilità, le prestazioni e l'uso dell'applicazione, usare [Azure Application Insights], che offre potenti e rapidi strumenti per la risoluzione dei problemi, la diagnostica e il recupero delle informazioni sull'uso. In alternativa, usare uno strumento di terze parti come [New Relic] per fornire dati di monitoraggio avanzati per le app Web.

Con il livello **Standard** è possibile monitorare i tempi di risposta dell'app e ricevere notifiche tramite e-mail nel caso in cui l'app Web non risponda. Per altre informazioni, vedere [Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure].

## Altre risorse

- [Documentazione relativa alle app Web del servizio app](/services/app-service/web/)
- [Mappa di formazione per le app Web del servizio app di Azure](websites-learning-map.md)
- [Blog Web di Azure](/blog/topics/web/)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[Azure App Service]: /services/app-service/web/

[Orchard]: web-sites-dotnet-orchard-cms-gallery.md
[Umbraco]: web-sites-gallery-umbraco.md
[Drupal]: web-sites-php-migrate-drupal.md
[WordPress]: web-sites-php-web-site-gallery.md
  
[MySQL]: web-sites-php-mysql-deploy-use-git.md
[database SQL di Azure]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[distribuzione in uno slot di gestione temporanea]: web-sites-staged-publishing.md
[pubblicazione continua]: http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[eseguire test A/B]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[creazione di un'app ASP.NET MVC con autenticazione e database SQL e alla distribuzione in Azure App Service]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md

[Servizi multimediali di Azure]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[servizio SendGrid disponibile in Azure Marketplace]: sendgrid-dotnet-how-to-send-email.md
[Servizi mobili]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md
[Hub di notifica]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md

[Gestione traffico di Azure]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[integrazione dell'app Web con la rete CDN di Azure]: cdn-websites-with-cdn.md
[nodo della rete CDN]: https://msdn.microsoft.com/library/azure/gg680302.aspx

[scalabilità verticale e orizzontale]: /manage/services/web-sites/how-to-scale-websites/
[Azure Management Portal]: http://manage.windowsazure.com/
[API di gestione del servizio]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[script di PowerShell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[10 elementi appresi per implementare rapidamente la scalabilità delle app Web con Azure]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Cache Redis di Azure]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[stato della sessione ASP.NET]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[cache di output]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[riepilogo rapido]: /manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: /develop/net/how-to-guides/new-relic/
[Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

  
  [gitstaging]: http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
 

<!---HONumber=62-->