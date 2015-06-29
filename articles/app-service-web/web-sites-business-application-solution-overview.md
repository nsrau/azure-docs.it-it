<properties 
	pageTitle="Creazione di un'app Web line-of-business in Servizio app di Azure" 
	description="Questa guida fornisce informazioni generali di carattere tecnico su come usare le app Web di Servizio app di Azure per creare applicazioni line-of-business Intranet. Sono inclusi le strategie di autenticazione, l'inoltro del bus di servizio e il monitoraggio." 
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



# Creazione di un'app Web line-of-business in Servizio app di Azure

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) App Web rappresenta la scelta ideale per le applicazioni line-of-business. Tali applicazioni sono applicazioni Intranet che devono essere protette per uso aziendale interno. Di solito richiedono l'autenticazione, in genere in una directory aziendale, e alcune l'accesso o l'integrazione con servizi e dati locali.

Lo spostamento delle applicazioni line-of-business nelle app Web di Servizio app di Azure presenta diversi vantaggi, tra cui:

-  Possibilità di implementare la scalabilità verticale e orizzontale con carichi di lavoro dinamici, ad esempio un'applicazione che gestisce le valutazioni delle prestazioni annuali. Durante il periodo di verifica, il traffico raggiunge livelli elevati nelle aziende di grandi dimensioni. Azure offre opzioni che garantiscono all'azienda la scalabilità orizzontale per gestire il carico durante il periodo di verifica con traffico elevato e di risparmiare denaro tornando alla scala iniziale per la parte restante dell'anno. 
-  Possibilità di concentrarsi più sullo sviluppo delle applicazioni e meno sull'acquisizione e sulla gestione dell'infrastruttura.
-  Maggiore supporto per i dipendenti e i partner nell'uso dell'applicazione da qualsiasi postazione. Non è necessario che gli utenti siano connessi alla rete aziendale per poter usare l'applicazione e il gruppo IT evita di dover implementare soluzioni complesse con proxy inverso. Sono disponibili molte opzioni di autenticazione per proteggere l'accesso alle applicazioni aziendali.

Di seguito è riportato un esempio di un'applicazione line-of-business in esecuzione nelle app Web di Servizio app di Azure. L'esempio illustra le attività che è possibile eseguire con investimenti tecnici minimi semplicemente componendo le app Web con altri servizi. **Fare clic su un elemento nella topografia per visualizzare ulteriori informazioni**.

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]Questa guida presenta alcune delle aree e delle attività più comuni relative alle applicazioni line-of-business. Nelle app Web di Servizio app di Azure sono tuttavia disponibili altre funzionalità che è possibile usare nelle implementazioni specifiche. Per informazioni dettagliate su tali funzionalità, vedere le guide relative alla [presenza Web globale](web-sites-global-web-presence-solution-overview.md) e alle [campagne di marketing digitali](web-sites-digital-marketing-application-solution-overview.md).

## Trasferire gli asset esistenti

È possibile trasferire nelle app Web di Servizio app di Azure gli asset Web esistenti creati con una vasta gamma di linguaggi e framework.

Le app Web di Servizio app di Azure supportano l'esecuzione di asset Web esistenti scritti in .NET, PHP, Java, Node.js o Python. È possibile trasferirle nelle app Web utilizzando gli [FTP] abituali o il proprio sistema di controllo del codice sorgente. Le app Web supportano la pubblicazione diretta dalle opzioni di controllo del codice sorgente più diffuse, ad esempio [Visual Studio], [Visual Studio Online] e [Git] \(locale, GitHub, BitBucket, DropBox, Mercurial e così via\).

## Proteggere gli asset

È possibile proteggere gli asset mediante crittografia, autenticare gli utenti aziendali locali o remoti e autorizzarli all'uso degli asset.

Proteggere gli asset interni da utenti non autorizzati tramite [HTTPS]. Il nome di dominio **\*.azurewebsites.net** è già dotato di un certificato SSL e, se si usa un dominio personalizzato, è possibile spostare il relativo certificato SSL nelle app Web del servizio app. A ogni certificato SSL è associata una tariffa mensile \(ripartita su base oraria\). Per altre informazioni, vedere la pagina relativa ai [dettagli sui prezzi di Servizio app di Azure].

[Autenticare gli utenti] nella directory aziendale. Le app Web di Servizio app di Azure sono in grado di autenticare gli utenti con i provider di identità locali, ad esempio Active Directory Federation Services \(ADFS\) o con un tenant di Azure Active Directory sincronizzato con la distribuzione di Active Directory aziendale. Gli utenti possono accedere alle proprietà Web nella app Web tramite Single Sign-On quando sono in sede e quando sono fuori sede. I servizi esistenti, come Office 365 o Microsoft Intune, usano già Azure Active Directory. Tramite [Easy Auth] è molto semplice attivare l'autenticazione con lo stesso tenant di Azure Active Directory per la propria app Web.

[Autorizzare gli utenti] per l'uso delle proprietà Web. Con un'aggiunta minima di codice, è possibile trasferire nelle app Web del servizio app di Azure lo stesso modello di codifica ASP.NET locale usando, ad esempio, la decoration `[Authorize]` È possibile controllare l'accesso in modo specifico con la stessa flessibilità con cui si gestiscono le applicazioni in locale.

## Connettersi alle risorse locali ##

È possibile effettuare la connessione ai dati o alle risorse della propria app Web, sia che si trovi nel cloud per avere migliori prestazioni sia che si trovi in locale per motivi di conformità. Per altre informazioni sulla conservazione dei dati in Azure, vedere [Centro protezione Microsoft Azure].

È possibile scegliere tra vari back-end di database in Azure per soddisfare le esigenze dell'app Web, inclusi [database SQL di Azure] e [MySQL]. L'archiviazione sicura in Azure fa sì che i dati siano geograficamente vicini all'app Web e ne ottimizza le prestazioni.

L'azienda può tuttavia richiedere che i dati vengano mantenuti in locale. Le app Web del servizio app di Azure consentono di configurare facilmente una [connessione ibrida] alla risorsa locale, ad esempio un back-end di database. Se si desidera una gestione unificata delle connessioni locali, è possibile integrare molte app Web con una [Rete virtuale di Azure] che dispone di una VPN da sito a sito. È quindi possibile accedere alle risorse locali come se le app Web fossero in locale.

## Eseguire l'ottimizzazione

Ottimizzare l'applicazione line-of-business scalandola automaticamente mediante Scalabilità automatica, effettuando la memorizzazione nella cache con Cache Redis di Azure, eseguendo le attività in background con Processi Web e mantenendo una disponibilità elevata con Gestione traffico di Azure.

L'implementazione della [scalabilità verticale e orizzontale] offerta dalle app Web del servizio app di Azure risponde alle esigenze dell'applicazione line-of-business, indipendentemente dalla dimensione del carico di lavoro. È possibile eseguire la scalabilità orizzontale dell'app Web manualmente tramite il [portale di gestione di Azure], a livello di codice tramite l'[API di gestione del servizio] o gli [script di PowerShell] oppure automaticamente tramite la funzionalità Scalabilità automatica. Nel livello **Standard**, tale funzionalità consente di implementare la scalabilità orizzontale di un'app Web automaticamente, in base all'uso della CPU. Per le procedure consigliate, vedere l'articolo di [Troy Hunt] relativo ai [10 elementi appresi per implementare rapidamente la scalabilità delle app Web con Azure].

È possibile aumentare la velocità di risposta dell'app Web con [Cache Redis di Azure]. Usare questa funzionalità per memorizzare nella cache i dati dei database back-end e altri elementi come lo [stato della sessione ASP.NET] e la [cache di output].

È possibile mantenere un'elevata disponibilità dell'app Web mediante \[Gestione traffico di Azure\]. Tramite il metodo **Failover**, Gestione traffico instrada automaticamente il traffico a un sito secondario se si verifica un problema nel sito primario.

## Eseguire il monitoraggio e l'analisi

Tramite Azure o strumenti di terze parti è possibile mantenersi aggiornati sulle prestazioni dell'app Web e ricevere avvisi su eventi critici nell'app Web. Inoltre, con Application Insights o mediante l'analisi dei log Web di HDInsight, è possibile ottenere facilmente informazioni dettagliate sugli utenti.

Il pannello dell'app Web nel portale di Azure offre un [riepilogo rapido] delle metriche delle prestazioni e delle quote delle risorse correnti dell'app Web nel [portale di anteprima di Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Per informazioni complete sulla disponibilità, le prestazioni e l'uso dell'applicazione, usare [Azure Application Insights], che offre potenti e rapidi strumenti per la risoluzione dei problemi, la diagnostica e il recupero delle informazioni sull'uso. In alternativa, usare uno strumento di terze parti come [New Relic] per fornire dati di monitoraggio avanzati per le app Web.

Con il livello **Standard** è possibile monitorare i tempi di risposta dell'app e ricevere notifiche tramite e-mail nel caso in cui l'app non risponda. Per altre informazioni, vedere [Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure].

## Altre risorse

- [Documentazione relativa alle app Web del servizio app](/services/app-service/web/)
- [Mappa di formazione per le app Web del servizio app di Azure](websites-learning-map.md)
- [Blog Web di Azure](/blog/topics/web/)

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]



[Azure App Service]: /services/app-service/web/

[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[HTTPS]: web-sites-configure-ssl-certificate.md
[dettagli sui prezzi di Servizio app di Azure]: /pricing/details/app-service/#ssl-connections
[Autenticare gli utenti]: web-sites-authentication-authorization.md
[Easy Auth]: /blog/2014/11/13/azure-websites-authentication-authorization/
[Autorizzare gli utenti]: web-sites-authentication-authorization.md

[Centro protezione Microsoft Azure]: /support/trust-center/
[MySQL]: web-sites-php-mysql-deploy-use-git.md
[database SQL di Azure]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[connessione ibrida]: web-sites-hybrid-connection-get-started.md
[Rete virtuale di Azure]: web-sites-integrate-with-vnet.md

[scalabilità verticale e orizzontale]: web-sites-scale.md
[portale di gestione di Azure]: http://manage.windowsazure.com/
[API di gestione del servizio]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[script di PowerShell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[10 elementi appresi per implementare rapidamente la scalabilità delle app Web con Azure]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Cache Redis di Azure]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[stato della sessione ASP.NET]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[cache di output]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[riepilogo rapido]: web-sites-monitor.md
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: ../store-new-relic-cloud-services-dotnet-application-performance-management.md
[Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

 

<!---HONumber=58_postMigration-->