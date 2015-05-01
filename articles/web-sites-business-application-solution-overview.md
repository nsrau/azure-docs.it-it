<properties 
	pageTitle="Creazione di un'applicazione line-of-business in Siti Web di Azure" 
	description="Questa guida fornisce informazioni tecniche generali sull'uso di Siti Web di Azure per la creazione di applicazioni line-of-business intranet. Sono inclusi le strategie di autenticazione, l'inoltro del bus di servizio e il monitoraggio." 
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



# Creazione di un'applicazione line-of-business in Siti Web di Azure

[Siti Web di Azure] è la scelta ideale per applicazioni line-of-business. Tali applicazioni sono applicazioni Intranet che devono essere protette per uso aziendale interno. Di solito richiedono l'autenticazione, in genere in una directory aziendale, e alcune l'accesso o l'integrazione con servizi e dati locali. 

Lo spostamento delle applicazioni line-of-business in Siti Web di Azure presenta diversi vantaggi, tra cui:

-  Possibilità di implementare la scalabilità verticale e orizzontale con carichi di lavoro dinamici, ad esempio un'applicazione che gestisce le valutazioni delle prestazioni annuali. Durante il periodo di verifica, il traffico raggiunge livelli elevati nelle aziende di grandi dimensioni. Azure offre opzioni che garantiscono all'azienda la scalabilità orizzontale per gestire il carico durante il periodo di verifica con traffico elevato e di risparmiare denaro tornando alla scala iniziale per la parte restante dell'anno. 
-  Possibilità di concentrarsi più sullo sviluppo delle applicazioni e meno sull'acquisizione e sulla gestione dell'infrastruttura.
-  Maggiore supporto per i dipendenti e i partner nell'uso dell'applicazione da qualsiasi postazione. Non è necessario che gli utenti siano connessi alla rete aziendale per poter usare l'applicazione e il gruppo IT evita di dover implementare soluzioni complesse con proxy inverso. Sono disponibili molte opzioni di autenticazione per proteggere l'accesso alle applicazioni aziendali.

Di seguito è riportato un esempio di un'applicazione line-of-business in esecuzione in Siti Web di Azure. L'esempio illustra le attività che è possibile eseguire con investimenti tecnici minimi semplicemente componendo Siti Web di Azure con altri servizi. **Fare clic su un elemento nella topografia per visualizzare altre informazioni su di esso.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>

<div class="dev-callout">
<strong>Nota</strong>
<p>Questa guida presenta alcune delle aree e delle attività più comuni relative alle applicazioni line-of-business. In Siti Web di Azure sono tuttavia disponibili altre funzionalità che è possibile usare nelle implementazioni specifiche. Per informazioni dettagliate su tali funzionalità, vedere anche le altre guide relative alla <a href="http://azure.microsoft.com/manage/services/web-sites/global-web-presence-solution-overview/">presenza Web globale</a> e alle <a href="http://azure.microsoft.com/manage/services/web-sites/digital-marketing-campaign-solution-overview">campagne di marketing digitali</a>.</p>
</div>

### Trasferire le risorse esistenti

È possibile trasferire in Siti Web di Azure le risorse Web esistenti create con una vasta gamma di linguaggi e framework.

Siti Web di Azure supporta l'esecuzione di asset Web esistenti scritti in .NET, PHP, Java, Node. js o Python. Il trasferimento in Siti Web di Azure può essere eseguito mediante gli strumenti [FTP] già noti o il sistema di gestione del controllo del codice sorgente di cui si dispone. Siti Web di Azure supporta la pubblicazione diretta dalle opzioni di controllo del codice sorgente più diffuse, ad esempio [Visual Studio], [Visual Studio Online] e [Git] (locale, GitHub, BitBucket, DropBox, Mercurial e così via.

### Proteggere le risorse

È possibile proteggere le risorse mediante crittografia, autenticare gli utenti aziendali locali o remoti e autorizzarne l'uso delle risorse. 

Proteggere le risorse interne da utenti non autorizzati tramite [HTTPS]. Il nome di dominio **\*.azurewebsites.net** viene già fornito con un certificato SSL e, se si usa il proprio dominio personalizzato, è possibile trasferire il relativo certificato SSL in Siti Web di Azure. A ogni certificato SSL è associata una tariffa mensile (ripartita su base oraria). Per altre informazioni, vedere la pagina relativa ai [dettagli sui prezzi di Siti Web].

[Autenticare gli utenti] nella directory aziendale. Siti Web di Azure è in grado di autenticare gli utenti con i provider di identità locali, ad esempio Active Directory Federation Services (ADFS) o con un tenant di Azure Active Directory sincronizzato con la distribuzione di Active Directory aziendale. Gli utenti possono accedere alle proprietà Web in Siti Web di Azure tramite Single Sign-On quando sono in sede e quando sono fuori sede. I servizi esistenti, come Office 365 o Windows Intune, già usano Azure Active Directory. Tramite [Easy Auth] è molto semplice attivare l'autenticazione con lo stesso tenant di Azure Active Directory per il proprio sito Web. 

[Autorizzare gli utenti] per l'uso delle proprietà Web. Con un'aggiunta minima di codice, è possibile trasferire in Siti Web di Azure lo stesso modello di codifica ASP.NET locale usando, ad esempio, la decoration '[Authorize]'. È possibile controllare l'accesso in modo specifico con la stessa flessibilità con cui si gestiscono le applicazioni in locale.

### Connettersi alle risorse locali ###

È possibile effettuare la connessione ai dati o alle risorse del proprio sito Web, sia che si trovi nel cloud per avere migliori prestazioni sia che si trovi in locale per motivi di conformità. Per altre informazioni sulla conservazione dei dati in Azure, vedere [Centro protezione Microsoft Azure]. 

È possibile scegliere tra vari back-end di database in Azure per soddisfare le esigenze del sito Web, inclusi [database SQL di Azure] e [MySQL]. L'archiviazione sicura in Azure fa sì che i dati siano geograficamente vicini al sito Web e ne ottimizza le prestazioni.

L'azienda può tuttavia richiedere che i dati vengano mantenuti in locale. Siti Web di Azure consente di configurare facilmente una [connessione ibrida] alla risorsa locale, ad esempio un back-end di database. Se si desidera una gestione unificata delle connessioni locali, è possibile integrare molti siti Web di Azure con una [Rete virtuale di Azure] che dispone di una VPN da sito a sito. È quindi possibile accedere alle risorse locali come se i siti Web di Azure fossero in locale. [Enterprise Pizza - Connessione di siti Web all'ambiente locale tramite bus di servizio][enterprisepizza]

### Eseguire l'ottimizzazione

Ottimizzare l'applicazione line-of-business scalandola automaticamente mediante Scalabilità automatica, effettuando la memorizzazione nella cache con Cache Redis di Azure, eseguendo le attività in background con Processi Web e mantenendo una disponibilità elevata con Gestione traffico di Azure.

L'implementazione della [scalabilità verticale e orizzontale] offerta da Siti Web di Azure risponde alle esigenze dell'applicazione line-of-business, indipendentemente dalla dimensione del carico di lavoro. È possibile scalare in orizzontale il sito Web manualmente tramite il [portale di gestione di Azure], a livello di codice tramite l'[API di gestione del servizio] o gli script di [PowerShell] oppure automaticamente tramite la funzionalità Scalabilità automatica. Nel piano di hosting **Standard** tale funzionalità consente di scalare in orizzontale un sito Web automaticamente in base all'uso della CPU. Per le procedure consigliate, vedere l'articolo di [Troy Hunt] relativo ai [10 elementi appresi per scalare rapidamente i siti Web con Azure].

Aumentare la velocità di risposta del sito Web con la [Cache Redis di Azure]. Usarla per memorizzare nella cache i dati dei database back-end e altri elementi quali lo [stato della sessione ASP.NET] e la [cache di output].

Mantenere un'elevata disponibilità del sito Web mediante [Gestione traffico di Azure]. Tramite il metodo **Failover**, Gestione traffico instrada automaticamente il traffico a un sito secondario se si verifica un problema nel sito primario.

### Eseguire il monitoraggio e l'analisi

Per restare aggiornati sul livello di prestazioni del sito Web, è possibile usare Azure o strumenti di terze parti. È inoltre possibile ricevere avvisi sugli eventi critici relativi al sito Web, nonché ottenere facilmente informazioni sugli utenti con Application Insight o analizzare i log Web con HDInsight. 

Mediante il dashboard di Siti Web di Azure, è anche possibile ottenere un [riepilogo rapido] delle metriche delle prestazioni correnti del sito Web e delle quote relative alle risorse. Per avere informazioni generali complete sull'applicazione in termini di disponibilità, prestazioni e uso, avvalersi di [Azure Application Insights], che offre potenti e rapide funzioni per la risoluzione dei problemi, la diagnostica e il recupero dei dati sull'uso. In alternativa, usare uno strumento di terze parti come [New Relic] per fornire dati di monitoraggio avanzati per i siti Web.

Nel piano di hosting **Standard**, grazie al monitoraggio della velocità di risposta, è possibile ricevere notifiche via e-mail ogni volta che il sito smette di rispondere. Per altre informazioni, vedere [Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure].

## Altre risorse

- [Documentazione di Siti Web di Azure](/it-it/documentation/services/websites/)
- [Mappa di formazione per Siti Web di Azure](websites-learning-map.md)
- [Blog Web di Azure](/blog/topics/web/)



[Siti Web di Azure]:/it-it/services/websites/

[FTP]:/it-it/documentation/articles/web-sites-deploy/#ftp
[Visual Studio]:/it-it/documentation/articles/web-sites-dotnet-get-started/
[Visual Studio Online]:/it-it/documentation/articles/cloud-services-continuous-delivery-use-vso/
[Git]:/it-it/documentation/articles/web-sites-publish-source-control/

[HTTPS]:/it-it/documentation/articles/web-sites-configure-ssl-certificate/
[Dettagli sui prezzi di Siti Web]:/it-it/pricing/details/web-sites/#service-ssl
[Autenticare gli utenti]:/it-it/documentation/articles/web-sites-authentication-authorization/
[Easy Auth]:/blog/2014/11/13/azure-websites-authentication-authorization/
[Autorizzare gli utenti]:/it-it/documentation/articles/web-sites-authentication-authorization/

[Centro protezione di Azure]:/it-it/support/trust-center/
[MySQL]:/it-it/documentation/articles/web-sites-php-mysql-deploy-use-git/
[Database SQL di Azure]:/it-it/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
[connessione ibrida]:/it-it/documentation/articles/web-sites-hybrid-connection-get-started/
[Rete virtuale di Azure]:/it-it/documentation/articles/web-sites-integrate-with-vnet/

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




<!--HONumber=42-->
