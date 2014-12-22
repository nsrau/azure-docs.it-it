<properties urlDisplayName="Resources" pageTitle="Creare una campagna marketing digitale in Siti Web di Azure" metaKeywords="" description="This guide provides a technical overview of how to use Azure Websites to create digital marketing campaigns. This includes deployment, social media integration, scaling strategies, and monitoring." metaCanonical="" services="" documentationCenter="" title="Create a Digital Marketing Campaign on Azure Websites" authors="jroth" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth" />

# Creare una campagna marketing digitale in Siti Web di Azure
In questa Guida viene fornita una panoramica tecnica dell'uso di Siti Web di Azure per la creazione di campagne marketing digitali. Una campagna marketing digitale è in genere di breve durata e si propone di ottenere un obiettivo marketing a breve termine. È necessario prendere in considerazione due scenari principali. Nel primo scenario una società di marketing di terze parti crea e gestisce la campagna per conto del cliente, per l'intera durata della promozione. Nel secondo scenario la società di marketing crea la campagna marketing digitale, quindi trasferisce al cliente la proprietà delle risorse corrispondenti. La campagna marketing digitale verrà quindi eseguita e gestita autonomamente dal cliente. 

[Siti Web di Azure][websitesoverview] è la soluzione ideale in entrambi gli scenari. Consente infatti una creazione rapida, supporta più framework e linguaggi, assicura la scalabilità in base alla domanda da parte degli utenti e permette di usare diversi sistemi di distribuzione e di controllo del codice sorgente. Se si usa Azure, sarà inoltre possibile accedere ad altri servizi di Azure, ad esempio Servizi multimediali, che consentono di ottimizzare una campagna marketing.

Benché sia possibile usare [Servizi cloud di Azure][csoverview] o [Macchine virtuali di Azure][vmoverview] per l'hosting di siti Web, tale opzione è consigliabile per questo scenario solo se è necessaria una caratteristica non offerta da Siti Web di Azure. Per informazioni sulle opzioni disponibili, vedere [Confronto tra Siti Web, Servizi cloud e Macchine virtuali di Azure][chooseservice].

In questa guida vengono illustrati gli argomenti seguenti:

- [Distribuzione di siti Web esistenti](#deployexisting)
- [Integrazione con i social media](#socialmedia)
- [Scalabilità in base alla domanda da parte degli utenti](#scale)
- [Integrazione con altri servizi](#integrate)
- [Monitoraggio della campagna](#monitor)

<div class="dev-callout">
<strong>Nota</strong>
<p>In questa guida vengono illustrate alcune delle aree e delle attività più comuni relative allo sviluppo di un sito .COM pubblico. In Siti Web di Azure sono tuttavia disponibili altre funzionalità che è possibile usare nelle implementazioni specifiche. Per informazioni dettagliate su queste funzionalità, vedere le guide relative alla <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/global-web-presence-solution-overview/">presenza Web globale</a> e alle <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/business-application-solution-overview">applicazioni aziendali</a>.</p>
</div>

##<a name="deployexisting"></a>Distribuzione di siti Web esistenti
Nello scenario relativo alla presenza Web globale vengono illustrate diverse opzioni per la creazione e la distribuzione di un nuovo sito Web. Se non si è esperti di Siti Web di Azure, è consigliabile [esaminare le informazioni disponibili][scenarioglobalweb]. Se si creano spesso campagne marketing digitali, è possibile che siano disponibili asset Web da personalizzare per le diverse promozioni. In questa sezione verranno esaminate in modo dettagliato le opzioni per la distribuzione di diversi tipi di siti Web dal controllo del codice sorgente.

Se si riutilizzano asset Web per finalità diverse, è consigliabile prendere in considerazione un sistema di gestione del controllo del codice sorgente, se non se ne usa già uno. Tale sistema consente di archiviare i modelli di soluzioni Web comuni, che possono essere sottoposti a branching e personalizzati per clienti specifici. Siti Web consente inoltre di eseguire la sincronizzazione con molti repository diversi di codice sorgente. Nella scheda **Dashboard** selezionare il collegamento **Imposta distribuzione dal controllo del codice sorgente**.

![DigitalMarketingDeploy1][DigitalMarketingDeploy1]

Verrà visualizzata una finestra di dialogo con più opzioni per il controllo del codice sorgente, inclusi sistemi completi per il controllo del codice sorgente, come TFS, oltre a semplici soluzioni di distribuzione, ad esempio Dropbox.

![DigitalMarketingDeploy2][DigitalMarketingDeploy2]

Per gestire il nuovo progetto basato su una linea di base esistente, è possibile usare diverse tecniche per il controllo del codice sorgente. È ad esempio possibile copiare un repository di base, salvato in precedenza, per iniziare a creare il nuovo progetto oppure creare un nuovo branch per tenere traccia delle personalizzazioni per il progetto corrente. Per un esempio dell'uso di branch per la gestione di distribuzioni diverse dallo stesso repository di controllo del codice sorgente, vedere la pagina relativa agli [ambienti multipli con Siti Web di Azure][gitstaging]. In questo post viene illustrato come usare il branching Git per la gestione degli ambienti di gestione temporanea e di produzione.

Dopo la connessione del sito Web al controllo del codice sorgente, sarà possibile configurare le distribuzioni e tenerne traccia dal portale. Per altre informazioni sull'uso del controllo del codice sorgente con Siti Web, vedere [Pubblicazione da controllo del codice sorgente in Siti Web di Azure][publishingwithgit].

Quando si usano asset Web esistenti, è importante mantenere la flessibilità necessaria per l'hosting di tipi diversi di siti Web. Nella scheda **Configura** è possibile selezionare sia il supporto .NET che PHP per il sito Web. 

![DigitalMarketingFrameworkVersions][DigitalMarketingFrameworkVersions]

Oltre a queste opzioni di configurazione, Siti Web offre automaticamente supporto per Python 2.7 e Node.js. La versione predefinita di Node.js è 0.10.5.

Siti Web di Azure offre inoltre il vantaggio di poter distribuire in modo rapido i siti di gestione temporanea sul Web. Durante la pianificazione, la creazione dei prototipi e le fasi iniziali dello sviluppo di un sito, l'agenzia e il cliente possono esaminare versioni effettivamente funzionanti del sito della campagna prima che sia attivato. Quando il sito è pronto per la produzione, l'agenzia può gestire la distribuzione in produzione per conto del cliente oppure può fornire al cliente gli asset Web da distribuire e gestire.

##<a name="socialmedia"></a>Integrazione con i social media
La maggior parte delle campagne marketing digitali usa siti di social media, ad esempio Facebook o Twitter. Uno degli aspetti base dell'integrazione consiste nell'usare le identità di social media per l'autenticazione. Per un esempio di questo approccio con un'applicazione ASP.NET, vedere [Distribuire un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure][deploysecurewebsite].

Molte campagne marketing digitali, tuttavia, usano l'integrazione con i social media non solo per l'autenticazione ma anche come elemento chiave della strategia adottata. I siti di social media includono in genere una sezione per sviluppatori, che illustra i diversi modi in cui è possibile integrare le applicazioni con i servizi offerti da tali siti. I servizi che offrono un'API REST possono essere usati da quasi tutti i framework Web. Sono tuttavia spesso presenti informazioni specifiche per il linguaggio usato. Ad esempio, Twitter offre un [elenco di librerie disponibili che supportano l'API Twitter][twitter], incluse le librerie per .NET, Node.js, e PHP. È consigliabile esaminare le indicazioni per lo sviluppo specifiche direttamente in ogni sito di social media a cui si desidera fare riferimento nella campagna.

Se si è sviluppatori ASP.NET e si desidera fare riferimento a Facebook, in Visual Studio è disponibile un modello per un'applicazione MVC 4 specifica per Facebook.  

![DigitalMarketingFacebook][DigitalMarketingFacebook]

Per una procedura dettagliata relativa all'uso di questo modello con Siti Web, vedere il blog relativo alla [creazione di un'app Facebook usando i modelli Facebook ASP.NET MVC e al relativo hosting gratuito in Siti Web di Azure][fbtutorial]. Per un'esercitazione più dettagliata e un'applicazione di esempio, vedere le pagine relative all'[app Facebook ASP.NET MVC per i compleanni][fbbirthdayapp] e al [nuovo modello per l'applicazione Facebook e la libreria ASP.NET MVC][fbvstemplate].

È importante che gli sviluppatori ASP.NET comprendano che l'integrazione con i social media non è limitata ai modelli offerti da Visual Studio. Un modello consente di semplificare il processo, ma, come indicato in precedenza, ogni sito di social media offre in genere informazioni relative a modalità alternative per la connessione da .NET e da molti altri linguaggi e framework.

##<a name="scale"></a>Scalabilità in base alla domanda da parte degli utenti
Il cloud computing risulta utile per i carichi di lavoro non prevedibili e le campagne marketing digitali rientrano in questa categoria. È infatti difficile prevedere la popolarità di un sito di marketing di durata relativamente breve, poiché molto dipende dalla capacità di catturare l'interesse degli utenti e dalle interazioni di social media correlate che contribuiscono all'incremento di traffico verso il sito. Azure offre diverse opzioni per la scalabilità di siti Web e servizi cloud.

- Scalabilità manuale tramite il [portale di gestione di Azure][managementportal].
- Scalabilità a livello di codice tramite l'[API di gestione del servizio][servicemanagementapi] o gli [script PowerShell][powershell].
- Scalabilità automatica tramite la funzionalità Autoscale (Preview).

Nel portale di gestione passare alla scheda **Scalabilità** per il sito Web. Sono disponibili diverse opzioni per la scalabilità. La prima opzione definisce la modalità del sito Web, che può essere impostata su **Gratuito**, **Condiviso** o **Standard**. 

![DigitalMarketingScale][DigitalMarketingScale]

Le funzionalità e le opzioni di scalabilità aumentano in corrispondenza di ogni livello. Ad esempio, i siti in modalità **Gratuito** non possono essere scalati orizzontalmente in istanze multiple, ma i siti in modalità **Condiviso** possono essere scalati orizzontalmente in 6 istanze. È anche possibile aumentare il numero di istanze selezionando la modalità **Standard**. Tale modalità consente di eseguire il sito in macchine virtuali dedicate e offre opzioni specifiche per macchine di dimensioni piccole, medie e grandi. Dopo avere definito la dimensione della macchina virtuale, è inoltre possibile avvalersi della scalabilità orizzontale in istanze multiple. La modalità **Standard** offre la scalabilità orizzontale fino a 10 istanze. Un elenco completo delle differenze tra le varie modalità è disponibile nelle [indicazioni per la determinazione dei prezzi per i siti Web][pricing].

Quando si seleziona la modalità **Standard**, è possibile anche abilitare la funzionalità Scalabilità automatica (anteprima), che consente di applicare la scalabilità in base alla CPU. La percentuale **CPU destinazione** indica un intervallo di uso del processore a cui Azure fa riferimento come obiettivo per le istanze del sito Web. Se l'uso medio della CPU è inferiore a tale obiettivo, Azure riduce il conteggio di istanze, poiché lo stesso carico su un numero inferiore di istanze consentirà di ottenere un incremento dell'utilizzo nelle istanze rimanenti. Non può tuttavia ridurre il numero di istanze al di sotto del valore minimo previsto per **Conteggio istanze**. Analogamente, se l'uso medio della CPU è superiore al valore specificato per **CPU destinazione**, Azure aumenterà il numero di istanze. La distribuzione dello stesso carico su macchine aggiuntive consentirà di ridurre l'uso della CPU in ogni macchina. Il numero di istanze aggiunte è limitato dal valore massimo specificato per **Conteggio istanze**.

![DigitalMarketingAutoScale][DigitalMarketingAutoScale]

La scalabilità automatica consente un utilizzo più efficiente delle risorse. È ad esempio possibile che una campagna marketing digitale sia più attiva durante la notte e nei giorni festivi. Questa soluzione permette inoltre di gestire in modo efficace uno scenario caratterizzato da un incremento imprevisto della popolarità di una campagna. La scalabilità automatica consente di gestire in modo efficiente un incremento del carico, quindi di ridurre il numero di istanze e il relativo costo quando il carico diminuisce. 

Per altre informazioni sulla scalabilità dei siti Web, vedere [Come scalare siti Web][scalewebsite]. Questo argomento è strettamente associato al monitoraggio. Per altre informazioni, vedere la sezione relativa al [monitoraggio della campagna] in questa guida(#monitor).

<div class="dev-callout">
<strong>Nota</strong>
<p>Nel caso delle applicazioni Web che scelgono di usare i servizi cloud e i ruoli Web, è disponibile un'opzione aggiuntiva per la scalabilità basata sulla lunghezza degli elementi in un coda. In un servizio cloud i ruoli che elaborano le code back-end costituiscono un modello di architettura molto diffuso. Per altre informazioni sulla scalabilità dei servizi cloud, vedere <a href="http://www.windowsazure.com/it-it/manage/services/cloud-services/how-to-scale-a-cloud-service/">Come scalare un servizio cloud</a>.</p>
</div>

##<a name="integrate"></a>Integrazione con altri servizi
Un sito per il marketing digitale incorpora spesso elementi multimediali avanzati, ad esempio lo streaming di video. L'hosting di tali siti in Azure offre un'integrazione avanzata con i servizi di Azure correlati. È possibile, ad esempio, usare Servizi multimediali di Azure per la codifica e lo streaming di video dal sito Web. Per altre informazioni su Servizi multimediali, vedere [Introduzione a concetti e scenari di Servizi multimediali di Azure][mediaservices]

È possibile usare altri servizi di Azure per creare un'applicazione più affidabile. Ad esempio, Siti Web è in grado di usare la cache distribuita offerta dal nuovo [Servizio cache di Azure (anteprima)][caching]. In alternativa, è possibile usare i servizi di archiviazione di Azure per l'archiviazione di dati e risorse dell'applicazione. Ad esempio, è possibile archiviare in modo permanente nei BLOB elementi grafici, video e altri file di grandi dimensioni. Sono inoltre disponibili servizi database, quali Database SQL di Azure e MySQL, per soddisfare le esigenze dei dati relazionali.

##<a name="monitor"></a>Monitoraggio della campagna
Nella scheda **Monitoraggio** sono disponibili metriche che semplificano la valutazione dell'esito e delle prestazioni del sito di marketing digitale.

![DigitalMarketingMonitor][DigitalMarketingMonitor]

È ad esempio possibile esaminare i modelli e i livelli di utilizzo mediante metriche quali **Tempo CPU**, **Richieste** e **Dati in uscita**. I valori corrispondenti a tali metriche crescono insieme all'aumento di popolarità della campagna marketing. Le informazioni sull'utilizzo possono consentire di decidere quando applicare la scalabilità orizzontale o verticale. Per altre informazioni, vedere [Come monitorare Siti Web][monitoring].

Se si usano le modalità **Gratuito** e **Condiviso**, è consigliabile esaminare anche le quote di risorse. Nella scheda **Dashboard** del portale vengono mostrate le statistiche di utilizzo correnti relative a diverse quote e viene indicato quando tali quote verranno reimpostate. Le statistiche di utilizzo visualizzate dipendono dalla modalità selezionata. Nella schermata seguente vengono mostrate le statistiche visualizzate per la modalità **Gratuito**. Se si usa la modalità **Condiviso**, la quota per **Dati in uscita** non verrà visualizzata. Nella modalità **Standard**vengono visualizzate solo le quote per **Archiviazione file system** e **Dimensione**.

![DigitalMarketingUsageOverview][DigitalMarketingUsageOverview]

Se tali quote sono quasi esaurite, è consigliabile valutare il passaggio da **Gratuito** a **Condiviso** oppure da **Condiviso** a **Standard** La modalità **Standard** offre risorse dedicate su una o più macchine virtuali di piccole, medie o grandi dimensioni. 

Oltre a usare il portale di gestione per questo tipo di informazioni, è possibile usare alcuni strumenti di terze parti che forniscono dati di monitoraggio avanzati per i siti Web. È ad esempio possibile installare un componente aggiuntivo New Relic da Azure Store. Per una procedura dettagliata relativa all'utilizzo di New Relic per il monitoraggio, vedere [Gestione delle prestazioni delle applicazioni con New Relic in Siti Web di Azure][newrelic]. 

È infine possibile abilitare il monitoraggio e gli avvisi relativi agli endpoint nei siti Web in modalità Standard. Il monitoraggio degli endpoint esegue regolarmente il tentativo di raggiungere il sito Web e segnala quindi il tempo di risposta rilevato. Gli avvisi forniscono notifiche tramite posta elettronica nel caso in cui il tempo di risposta superi una soglia specificata. Per altre informazioni, vedere la sezione relativa al monitoraggio dello scenario basato sulla [presenza Web globale][scenarioglobalweb] e l'argomento [Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure][receivealerts].

##<a name="summary"></a>Riepilogo
Siti Web di Azure costituisce una soluzione ideale per il contenuto Web riutilizzabile, personalizzato per singole campagne marketing. Offre supporto per molti dei linguaggi, framework e sistemi di controllo di gestione del codice sorgente più diffusi, semplificando la migrazione delle risorse e dei flussi di lavoro nel cloud. Il modello dell'applicazione Facebook ASP.NET semplifica la creazione di applicazioni Facebook, ma è possibile usare praticamente qualsiasi strumento di terze parti per l'integrazione con i social media che supporti le interfacce Web. Servizi multimediali di Azure e altri servizi correlati offerti da Azure offrono strumenti aggiuntivi per la creazione di un sito progettato in modo adeguato per una campagna. Le diverse opzioni per la scalabilità manuale e automatica risultano inoltre utili per la gestione della domanda da parte degli utenti, che può essere difficile da prevedere. Per altre informazioni, vedere gli articoli tecnici seguenti.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Area</th>
   <th align="left" valign="top">Risorse</th>
</tr>
<tr>
   <td valign="middle"><strong>Pianificazione</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/choose-web-app-service">Confronto tra Siti Web, Servizi Cloud e Macchine virtuali di Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Creazione</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-create-websites/">Come creare e distribuire un sito Web</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Distribuire</strong></td>
   <td valign="top">- <a href="http://azure.microsoft.com/it-it/documentation/articles/web-sites-deploy/">Come distribuire un sito Web di Azure</a><br/>- <a href="http://www.windowsazure.com/it-it/develop/net/common-tasks/publishing-with-git/">Pubblicazione da controllo del codice sorgente in Siti Web di Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Social media</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/it-it/develop/net/tutorials/web-site-with-sql-database/">Distribuire un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL</a><br/>- <a href="http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx">Creare un'app Facebook usando i modelli Facebook ASP.NET MVC</a><br/>- <a href="http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx">Modello per un'applicazione Facebook e libreria per ASP.NET MVC</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Dimensionamento</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-scale-websites/">Come scalare siti Web</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Dati multimediali elaborati</strong></td>
   <td valign="top">- <a href="http://msdn.microsoft.com/it-it/library/windowsazure/dn223282.aspx">Introduzione a concetti e scenari di Servizi multimediali di Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Monitoraggio</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-monitor-websites/">Come monitorare i siti Web</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure</a></td>
</tr>
</table>

  [websitesoverview]:/it-it/documentation/services/web-sites/
  [csoverview]:/it-it/documentation/services/cloud-services/
  [vmoverview]:/it-it/documentation/services/virtual-machines/
  [chooseservice]:/it-it/manage/services/web-sites/choose-web-app-service
  [scenarioglobalweb]:/it-it/manage/services/web-sites/global-web-presence-solution-overview/
  
  
  [publishingwithgit]:/it-it/develop/net/common-tasks/publishing-with-git/
  [gitstaging]:http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
  [deploysecurewebsite]:/it-it/develop/net/tutorials/web-site-with-sql-database/
  [twitter]:https://dev.twitter.com/docs/twitter-libraries#dotnet
  [fbtutorial]:http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx
  [fbbirthdayapp]:http://www.asp.net/mvc/tutorials/mvc-4/aspnet-mvc-facebook-birthday-app
  [fbvstemplate]:http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx
  [managementportal]:http://manage.windowsazure.com/
  [servicemanagementapi]:http://msdn.microsoft.com/it-it/library/windowsazure/ee460799.aspx
  [powershell]:http://msdn.microsoft.com/it-it/library/windowsazure/jj152841.aspx
  [pricing]:https://www.windowsazure.com/it-it/pricing/details/web-sites/
  [scalewebsite]:/it-it/manage/services/web-sites/how-to-scale-websites/
  
  [mediaservices]:http://msdn.microsoft.com/it-it/library/windowsazure/dn223282.aspx
  [caching]:http://msdn.microsoft.com/it-it/library/windowsazure/dn386094.aspx
  [monitoring]:/it-it/manage/services/web-sites/how-to-monitor-websites/
  [newrelic]:/it-it/develop/net/how-to-guides/new-relic/
  [receivealerts]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx
  
  
  
   [DigitalMarketingDeploy1]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy1.png
  [DigitalMarketingDeploy2]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy2.png
  [DigitalMarketingFrameworkVersions]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_FrameworkVersions.png
  [DigitalMarketingFacebook]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Facebook.png
  [DigitalMarketingScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Scale.png
  [DigitalMarketingAutoScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_AutoScale.png
  [DigitalMarketingMonitor]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Monitor.png
  [DigitalMarketingUsageOverview]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_UsageOverview.png
  
  
  
  
  
