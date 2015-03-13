<properties 
	pageTitle="Creazione di una campagna di marketing digitale in Siti Web di Azure" 
	description="In questa Guida vengono fornite informazioni generali di carattere tecnico sull'uso di Siti Web di Azure per la creazione di campagne di marketing digitali. Sono inclusi la distribuzione, l'integrazione di social networking, le strategie di scalabilità e il monitoraggio." 
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

# Campagne di marketing digitali in Siti Web di Azure
[Siti Web di Azure] è la scelta ideale per le campagne di marketing digitali. Le campagne di questo tipo in genere sono di breve durata e hanno la funzione di realizzare obiettivi di marketing a breve termine. È necessario prendere in considerazione due scenari principali. Nel primo scenario una società di marketing di terze parti crea e gestisce la campagna per conto del cliente, per l'intera durata della promozione. Nel secondo scenario la società di marketing crea la campagna di marketing digitale, quindi trasferisce al cliente la proprietà delle risorse corrispondenti. La campagna di marketing digitale verrà quindi eseguita e gestita autonomamente dal cliente. Siti Web di Azure è la soluzione perfetta in entrambi gli scenari. 

> [AZURE.NOTE] Per iniziare a usare Siti Web di Azure prima di iscriversi per ottenere un account, visitare la pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, in cui è possibile creare immediatamente e gratuitamente un sito di base ASP.NET temporaneo in Siti Web di Azure. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Di seguito è riportato un esempio di una campagna di marketing digitale multicanale globale con Siti Web di Azure. L'esempio illustra le attività che è possibile eseguire con investimenti tecnici minimi semplicemente componendo Siti Web di Azure con altri servizi. **Fare clic su un elemento nella topografia per visualizzare altre informazioni su di esso.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>

> [WACOM.NOTE]
> Questa guida presenta alcune delle aree e delle attività più comuni relative all'esecuzione di una campagna di marketing digitale in Siti Web di Azure. Tuttavia, sono disponibili altre soluzioni comuni che è possibile implementare in Siti Web di Azure. Per esaminare queste soluzioni, vedere le altre guide sulla <a href="http://azure.microsoft.com/manage/services/web-sites/global-web-presence-solution-overview/">presenza Web globale</a> e sulle <a href="http://azure.microsoft.com/manage/services/web-sites/business-application-solution-overview">applicazioni aziendali</a>.

### Creare i siti da zero o usare le risorse esistenti

È possibile creare rapidamente nuovi siti a partire da un noto sistema CMS presente nella raccolta oppure trasferire in Siti Web di Azure le risorse Web esistenti create con una vasta gamma di linguaggi e framework diversi.

La raccolta Azure offre modelli provenienti dai più diffusi sistemi di gestione dei contenuti (CMS) per siti Web, tra cui [Orchard], [Umbraco], [Drupal] e [WordPress]. È pertanto possibile creare un sito Web il proprio CMS preferito, nonché scegliere uno dei numerosi back-end di database per soddisfare le proprie esigenze, ad esempio [Database SQL di Azure] e [MySQL].

Siti Web di Azure supporta l'esecuzione di asset Web esistenti scritti in .NET, PHP, Java, Node. js o Python e possono essere spostate in Siti Web di Azure mediante gli strumenti [FTP] che si è già abituati a usare. Se si creano spesso campagne di marketing digitali, è possibile che alcune risorse Web esistenti siano disponibili in un sistema di gestione del controllo del codice sorgente. È possibile effettuare la distribuzione in Siti Web di Azure direttamente dalle opzioni di controllo del codice sorgente più note, ad esempio [Visual Studio], [Visual Studio Online] e [Git] (in locale, GitHub, BitBucket, DropBox, Mercurial e così via).

### Lavorare in modo agile

È possibile lavorare in modo agile pubblicando continuamente i dati direttamente dal controllo del codice sorgente esistente ed eseguire test A/B in Siti Web di Azure. 

Durante la pianificazione, la creazione di un prototipo e le fasi iniziali dello sviluppo di un sito, è possibile esaminare con il cliente versioni effettivamente funzionanti del sito della campagna prima che sia attivato [eseguendo la distribuzione in uno slot di gestione temporanea] del sito Web di Azure. Integrando il controllo del codice sorgente con Siti Web di Azure, si ha la possibilità di [pubblicare continuamente] in uno slot di questo tipo e quindi passare alla fase di produzione senza alcun periodo di inattività quando il sito è pronto. 

Inoltre, quando si pianificano modifiche relative a un sito Web attivo, è possibile [eseguire test A/B] facilmente sugli aggiornamenti proposti tramite la funzionalità di test nell'ambiente di produzione e analizzare il comportamento dell'utente reale per prendere decisioni consapevoli sulla struttura del sito.


### Integrarsi con i social media

La campagna di marketing digitale in Siti Web di Azure può essere integrata con i social media mediante l'autenticazione presso noti provider come Facebook e Twitter. Per un esempio di questo approccio con un'applicazione ASP.NET, vedere [Distribuire un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure]. 

Inoltre, ogni sito di social media offre in genere informazioni su altre modalità di integrazione da .NET e molti altri framework.

### Usare dati multimediali elaborati e raggiungere tutti i dispositivi

È possibile arricchire la propria campagna di marketing digitale con altri servizi di Azure come i seguenti:

-  Caricare e trasmettere in streaming video in tutto il mondo con [Servizi multimediali di Azure].
-  Inviare e-mail agli utenti con il servizio [SendGrid disponibile in Azure Marketplace].
-  Stabilire la presenza nei dispositivi Windows, iOS e Android con [Servizi mobili].
-  Inviare una notifica push a milioni di dispositivi con [Hub di notifica].

### Operare a livello internazionale

È possibile operare in tutto il mondo gestendo siti regionali con Gestione traffico di Azure e fornire contenuti molto rapidamente con la rete CDN di Azure.

Per servire i clienti internazionali nelle rispettive aree geografiche di appartenenza, usare [Gestione traffico di Azure] per instradare i visitatori del sito a un sito regionale in grado di fornire prestazioni ottimali. In alternativa, è possibile distribuire uniformemente il carico del sito in più copie del sito Web ospitate in più aree.

È inoltre possibile offrire globalmente contenuti statici agli utenti con la massima velocità [integrando il sito Web con la rete CDN di Azure]. Tale rete memorizza nella cache il contenuto statico nel [nodo della rete CDN] più vicino all'utente, riducendo al minimo la latenza e le connessioni al sito Web.

### Eseguire l'ottimizzazione

Ottimizzare il sito scalandolo automaticamente mediante Scalabilità automatica, effettuando la memorizzazione nella cache con Cache Redis di Azure, eseguendo le attività in background con WebJobs e mantenendo una disponibilità elevata con Gestione traffico di Azure.

L'implementazione della [scalabilità verticale e orizzontale] offerta da Siti Web di Azure è perfetta per i carichi di lavoro imprevedibili, come ad esempio nelle campagne di marketing digitali. È possibile scalare in orizzontale il sito Web manualmente tramite il [portale di gestione di Azure], a livello di codice tramite l'[API di gestione del servizio] o gli [script di PowerShell] oppure automaticamente tramite la funzionalità Scalabilità automatica. Nel piano di hosting **Standard** tale funzionalità consente di scalare in orizzontale un sito Web automaticamente in base all'uso della CPU. È pertanto possibile avere la massima agilità e contemporaneamente minimizzare i costi scalando orizzontalmente il sito Web solo quando è necessario secondo l'attività degli utenti. Per le procedure consigliate, vedere l'articolo di [Troy Hunt] relativo ai [10 elementi appresi per scalare rapidamente i siti Web con Azure].

Aumentare la velocità di risposta del sito Web con la [Cache Redis di Azure]. Usarla per memorizzare nella cache i dati dei database back-end e altri elementi quali lo [stato della sessione ASP.NET] e la [cache di output].

Mantenere un'elevata disponibilità del sito Web mediante [Gestione traffico di Azure]. Tramite il metodo **Failover**, Gestione traffico instrada automaticamente il traffico a un sito secondario se si verifica un problema nel sito primario.

### Eseguire il monitoraggio e l'analisi

Per restare aggiornati sul livello di prestazioni del sito Web, è possibile usare Azure o strumenti di terze parti. È inoltre possibile ricevere avvisi sugli eventi critici relativi al sito Web, nonché ottenere facilmente informazioni sugli utenti con Application Insight o analizzare i log Web con HDInsight. 

Mediante il dashboard di Siti Web di Azure, è anche possibile ottenere un [riepilogo rapido] delle metriche delle prestazioni correnti del sito Web e delle quote relative alle risorse. Per avere informazioni generali complete sull'applicazione in termini di disponibilità, prestazioni e uso, avvalersi di [Application Insights di Azure], che offre potenti e rapide funzioni per la risoluzione dei problemi, la diagnostica e il recupero dei dati sull'uso. In alternativa, usare uno strumento di terze parti come [New Relic] per fornire dati di monitoraggio avanzati per i siti Web.

Nel piano di hosting **Standard**, grazie al monitoraggio della velocità di risposta, è possibile ricevere notifiche via e-mail ogni volta che il sito smette di rispondere. Per altre informazioni, vedere [Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure].

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

[Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure]:/it-it/develop/net/tutorials/web-site-with-sql-database/

[Servizi multimediali di Azure]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Servizio SendGrid in Azure Marketplace]:/it-it/documentation/articles/sendgrid-dotnet-how-to-send-email/
[Servizi mobili]:/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
[Hub di notifica]:/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[Gestione traffico di Azure]:http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[integrando il sito Web con la rete CDN di Azure]:/it-it/documentation/articles/cdn-websites-with-cdn/ 
[Nodo della rete CDN]:https://msdn.microsoft.com/library/azure/gg680302.aspx

[scalabilità verticale e orizzontale]:/it-it/manage/services/web-sites/how-to-scale-websites/
[Portale di gestione di Azure]:http://manage.windowsazure.com/
[API di gestione del servizio]:http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[script di PowerShell]:http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[10 elementi appresi per scalare rapidamente i siti Web con Azure]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Cache Redis di Azure]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[stato della sessione ASP.NET]:https://msdn.microsoft.com/it-it/library/azure/dn690522.aspx
[cache di output]:https://msdn.microsoft.com/it-it/library/azure/dn798898.aspx

[riepilogo rapido]:/it-it/manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:/it-it/develop/net/how-to-guides/new-relic/
[Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

  
  [gitstaging]:http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites  


<!--HONumber=42-->
