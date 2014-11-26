<properties linkid="websites-digital-marketing" urlDisplayName="Resources" pageTitle="Create a Digital Marketing Campaign on Azure Websites" metaKeywords="" description="This guide provides a technical overview of how to use Azure Websites to create digital marketing campaigns. This includes deployment, social media integration, scaling strategies, and monitoring." metaCanonical="" services="" documentationCenter="" title="Create a Digital Marketing Campaign on Azure Websites" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth" />

# Creare una campagna marketing digitale in Siti Web di Azure

In questa Guida viene fornita una panoramica tecnica dell'uso di Siti Web di Azure per la creazione di campagne marketing digitali. Una campagna marketing digitale è in genere di breve durata e si propone di ottenere un obiettivo marketing a breve termine. È necessario prendere in considerazione due scenari principali. Nel primo scenario una società di marketing di terze parti crea e gestisce la campagna per conto del cliente, per l'intera durata della promozione. Nel secondo scenario la società di marketing crea la campagna marketing digitale, quindi trasferisce al cliente la proprietà delle risorse corrispondenti. La campagna marketing digitale verrà quindi eseguita e gestita autonomamente dal cliente.

[Siti Web di Azure][Siti Web di Azure] è la soluzione ideale in entrambi gli scenari. Consente infatti una creazione rapida, supporta più framework e linguaggi, assicura la scalabilità in base alla domanda da parte degli utenti e permette di usare diversi sistemi di distribuzione e di controllo del codice sorgente. Se si usa Azure, sarà inoltre possibile accedere ad altri servizi di Azure, ad esempio Servizi multimediali, che consentono di ottimizzare una campagna marketing.

Benché sia possibile usare [Servizi cloud di Azure][Servizi cloud di Azure] o [Macchine virtuali di Azure][Macchine virtuali di Azure] per l'hosting di siti Web, tale opzione è consigliabile per questo scenario solo se è necessaria una caratteristica non offerta da Siti Web di Azure. Per informazioni sulle opzioni disponibili, vedere [Confronto tra Siti Web, Servizi cloud e macchine virtuali di Azure][Confronto tra Siti Web, Servizi cloud e macchine virtuali di Azure].

In questa guida vengono illustrati gli argomenti seguenti:

-   [Distribuzione di siti Web esistenti][Distribuzione di siti Web esistenti]
-   [Integrazione con i social media][Integrazione con i social media]
-   [Scalabilità in base alla domanda da parte degli utenti][Scalabilità in base alla domanda da parte degli utenti]
-   [Integrazione con altri servizi][Integrazione con altri servizi]
-   [Monitoraggio della campagna][Monitoraggio della campagna]

<div class="dev-callout">
<strong>Nota</strong>
<p>In questa guida vengono illustrate alcune delle aree e delle attivit&agrave; pi&ugrave; comuni relative allo sviluppo di un sito .COM pubblico. In Siti Web di Azure sono tuttavia disponibili altre funzionalit&agrave; che &egrave; possibile usare nelle implementazioni specifiche. Per informazioni dettagliate su tali funzionalit&agrave;, vedere le guide relative alla <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/global-web-presence-solution-overview/">presenza Web globale</a> e alle <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/business-application-solution-overview">applicazioni business</a>.</p>
</div>

## <a name="deployexisting"></a>Distribuzione di siti Web esistenti

Nello scenario relativo alla presenza Web globale vengono illustrate diverse opzioni per la creazione e la distribuzione di un nuovo sito Web. Se non si è esperti di Siti Web di Azure, è consigliabile [esaminare le informazioni disponibili][esaminare le informazioni disponibili]. Se si creano spesso campagne marketing digitali, è possibile che siano disponibili asset Web da personalizzare per le diverse promozioni. In questa sezione verranno esaminate in modo dettagliato le opzioni per la distribuzione di diversi tipi di siti Web dal controllo del codice sorgente.

Se si riutilizzano asset Web per finalità diverse, è consigliabile prendere in considerazione un sistema di gestione del controllo del codice sorgente, se non se ne utilizza già uno. Tale sistema consente di archiviare i modelli di soluzioni Web comuni, che possono essere sottoposti a branching e personalizzati per clienti specifici. Siti Web consente inoltre di eseguire la sincronizzazione con molti repository diversi di codice sorgente. Nella scheda **Dashboard** selezionare il collegamento **Set up deployment from source control**.

![DigitalMarketingDeploy1][DigitalMarketingDeploy1]

Verrà visualizzata una finestra di dialogo con più opzioni per il controllo del codice sorgente, inclusi sistemi completi per il controllo del codice sorgente, come TFS, oltre a semplici soluzioni di distribuzione, ad esempio Dropbox.

![DigitalMarketingDeploy2][DigitalMarketingDeploy2]

Per gestire il nuovo progetto basato su una linea di base esistente, è possibile usare diverse tecniche per il controllo del codice sorgente. È ad esempio possibile copiare un repository di base, salvato in precedenza, per iniziare a creare il nuovo progetto oppure creare un nuovo branch per tenere traccia delle personalizzazioni per il progetto corrente. Per un esempio dell'utilizzo di branch per la gestione di distribuzioni diverse dallo stesso repository di controllo del codice sorgente, vedere la pagina relativa agli [ambienti multipli con Siti Web di Azure][ambienti multipli con Siti Web di Azure]. In questo post viene illustrato come usare il branching Git per la gestione degli ambienti di gestione temporanea e di produzione.

Dopo la connessione del sito Web al controllo del codice sorgente, sarà possibile configurare le distribuzioni e tenerne traccia dal portale. Per altre informazioni sull'uso del controllo del codice sorgente con Siti Web, vedere [Pubblicazione da controllo del codice sorgente in Siti Web di Azure][Pubblicazione da controllo del codice sorgente in Siti Web di Azure].

Quando si usano asset Web esistenti, è importante mantenere la flessibilità necessaria per l'hosting di tipi diversi di siti Web. Nella scheda **Configura** è possibile selezionare sia il supporto .NET che PHP per il sito Web.

![DigitalMarketingFrameworkVersions][DigitalMarketingFrameworkVersions]

Oltre a queste opzioni di configurazione, Siti Web offre automaticamente supporto per Python 2.7 e Node.js. La versione predefinita di Node.js è 0.10.5.

Siti Web di Azure offre inoltre il vantaggio di poter distribuire in modo rapido i siti di gestione temporanea sul Web. Durante la pianificazione, la creazione dei prototipi e le fasi iniziali dello sviluppo di un sito, l'agenzia e il cliente possono esaminare versioni effettivamente funzionanti del sito della campagna prima che sia attivato. Quando il sito è pronto per la produzione, l'agenzia può gestire la distribuzione in produzione per conto del cliente oppure può fornire al cliente gli asset Web da distribuire e gestire.

## <a name="socialmedia"></a>Integrazione con i social media

La maggior parte delle campagne marketing digitali utilizza siti di social media, ad esempio Facebook o Twitter. Uno degli aspetti base dell'integrazione consiste nell'usare le identità di social media per l'autenticazione. Per un esempio di questo approccio con un'applicazione ASP.NET, vedere [Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure][Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure].

Molte campagne marketing digitali, tuttavia, utilizzano l'integrazione con i social media non solo per l'autenticazione ma anche come elemento chiave della strategia adottata. I siti di social media includono in genere una sezione per sviluppatori, che illustra i diversi modi in cui è possibile integrare le applicazioni con i servizi offerti da tali siti. I servizi che offrono un'API REST possono essere utilizzati da quasi tutti i framework Web. Sono tuttavia spesso presenti informazioni specifiche per il linguaggio utilizzato. Ad esempio, Twitter offre [un elenco di librerie disponibili che supportano l'API Twitter][un elenco di librerie disponibili che supportano l'API Twitter], incluse le librerie per .NET, Node.js, e PHP. È consigliabile esaminare le indicazioni per lo sviluppo specifiche direttamente in ogni sito di social media a cui si desidera fare riferimento nella campagna.

Se si è sviluppatori ASP.NET e si desidera fare riferimento a Facebook, in Visual Studio è disponibile un modello per un'applicazione MVC 4 specifica per Facebook.

![DigitalMarketingFacebook][DigitalMarketingFacebook]

Per una procedura dettagliata relativa all'uso di questo modello con Siti Web, vedere il blog relativo alla [creazione di un'app Facebook usando i modelli Facebook MVC ASP.NET e al relativo hosting gratuito in Siti Web di Azure][creazione di un'app Facebook usando i modelli Facebook MVC ASP.NET e al relativo hosting gratuito in Siti Web di Azure]. Per un'esercitazione più dettagliata e un'applicazione di esempio, vedere le pagine relative all'[app Facebook MVC ASP.NET per i compleanni][app Facebook MVC ASP.NET per i compleanni] e al [nuovo modello per l'applicazione Facebook e la libreria MVC ASP.NET][nuovo modello per l'applicazione Facebook e la libreria MVC ASP.NET].

È importante che gli sviluppatori ASP.NET comprendano che l'integrazione con i social media non è limitata ai modelli offerti da Visual Studio. Un modello consente di semplificare il processo, ma, come indicato in precedenza, ogni sito di social media offre in genere informazioni relative a modalità alternative per la connessione da .NET e da molti altri linguaggi e framework.

## <a name="scale"></a>Scalabilità in base alla domanda da parte degli utenti

Il cloud computing risulta utile per i carichi di lavoro non prevedibili e le campagne marketing digitali rientrano in questa categoria. È infatti difficile prevedere la popolarità di un sito di marketing di durata relativamente breve, poiché molto dipende dalla capacità di catturare l'interesse degli utenti e dalle interazioni di social media correlate che contribuiscono all'incremento di traffico verso il sito. Azure offre diverse opzioni per la scalabilità di siti Web e servizi cloud.

-   Scalabilità manuale tramite il [portale di gestione di Azure][portale di gestione di Azure].
-   Scalabilità a livello di codice tramite l'[API di gestione del servizio][API di gestione del servizio] o lo [scripting PowerShell][scripting PowerShell].
-   Scalabilità automatica tramite la funzionalità Autoscale (Preview).

Nel portale di gestione passare alla scheda **Scala** per il sito Web. Sono disponibili diverse opzioni per la scalabilità. La prima opzione consente di determinare la modalità del sito Web, impostata su **Gratuito**, **Condiviso** o **Standard**.

![DigitalMarketingScale][DigitalMarketingScale]

Le funzionalità e le opzioni di scalabilità aumentano in corrispondenza di ogni livello. Ad esempio, i siti in modalità **Free** non possono essere scalati orizzontalmente in istanze multiple, ma i siti in modalità **Shared** possono essere scalati orizzontalmente in 6 istanze. È inoltre possibile ottenere la scalabilità verticale selezionando la modalità **Standard**. Tale modalità consente di eseguire il sito in macchine virtuali dedicate e offre opzioni specifiche per macchine di dimensioni piccole, medie e grandi. Dopo avere definito la dimensione della macchina virtuale, è inoltre possibile avvalersi della scalabilità orizzontale in istanze multiple. La modalità **Standard** offre la scalabilità orizzontale fino a 10 istanze. Un elenco completo di differenze tra le modalità è disponibile nelle [indicazioni per la determinazione dei prezzi per i siti Web][indicazioni per la determinazione dei prezzi per i siti Web].

Quando si seleziona la modalità **Standard**, è inoltre possibile abilitare la funzionalità Autoscale (Preview), che consente di applicare la scalabilità in base alla CPU. La percentuale **CPU destinazione** indica un intervallo di uso del processore a cui Azure fa riferimento come obiettivo per le istanze del sito Web. Se l'uso medio della CPU è inferiore a tale obiettivo, Azure riduce il conteggio di istanze, poiché lo stesso carico su un numero inferiore di istanze consentirà di ottenere un incremento dell'utilizzo nelle istanze rimanenti. Non è tuttavia possibile ridurre il conteggio di istanze fino a un valore inferiore al minimo previsto per **Instance Count**. Analogamente, se l'uso medio della CPU è superiore al valore specificato per **Target CPU**, Azure aumenterà il numero di istanze. La distribuzione dello stesso carico su macchine aggiuntive consentirà di ridurre l'uso della CPU in ogni macchina. Il numero di istanze aggiunte è limitato dal valore massimo specificato per **Instance Count**.

![DigitalMarketingAutoScale][DigitalMarketingAutoScale]

La scalabilità automatica consente un utilizzo più efficiente delle risorse. È ad esempio possibile che una campagna marketing digitale sia più attiva durante la notte e nei giorni festivi. Questa soluzione permette inoltre di gestire in modo efficace uno scenario caratterizzato da un incremento imprevisto della popolarità di una campagna. La scalabilità automatica consente di gestire in modo efficiente un incremento del carico, quindi di ridurre il numero di istanze e il relativo costo quando il carico diminuisce.

Per altre informazioni sulla scalabilità dei siti Web, vedere [Come aggiungere risorse a un sito Web][Come aggiungere risorse a un sito Web]. Questo argomento è strettamente associato al monitoraggio. Per altre informazioni, vedere la sezione relativa al [monitoraggio della campagna][Monitoraggio della campagna] in questa guida.

<div class="dev-callout">
<strong>Nota</strong>
<p>Nel caso delle applicazioni Web che scelgono di usare i servizi cloud e i ruoli Web, &egrave; disponibile un'opzione aggiuntiva per la scalabilit&agrave; basata sulla lunghezza degli elementi in un coda. In un servizio cloud i ruoli che elaborano le code back-end costituiscono un modello di architettura molto diffuso. Per altre informazioni sulla scalabilit&agrave; dei servizi cloud, vedere <a href="http://www.windowsazure.com/it-it/manage/services/cloud-services/how-to-scale-a-cloud-service/">Come applicare la scalabilit&agrave; a un servizio cloud</a>.</p>
</div>

## <a name="integrate"></a>Integrazione con altri servizi

Un sito per il marketing digitale incorpora spesso elementi multimediali avanzati, ad esempio lo streaming di video. L'hosting di tali siti in Azure offre un'integrazione avanzata con i servizi di Azure correlati. È possibile, ad esempio, usare Servizi multimediali di Azure per la codifica e lo streaming di video dal sito Web. Per altre informazioni su Servizi multimediali, vedere [Introduzione a concetti e scenari di Servizi multimediali di Azure][Introduzione a concetti e scenari di Servizi multimediali di Azure].

È possibile usare altri servizi di Azure per creare un'applicazione più affidabile. Ad esempio, Siti Web è in grado di usare la cache distribuita offerta dal nuovo [Servizio cache di Azure (anteprima)][Servizio cache di Azure (anteprima)]. In alternativa, è possibile usare i servizi di archiviazione di Azure per l'archiviazione di dati e risorse dell'applicazione. Ad esempio, è possibile archiviare in modo permanente nei BLOB elementi grafici, video e altri file di grandi dimensioni. Sono inoltre disponibili servizi database, quali Database SQL di Azure e MySQL, per soddisfare le esigenze dei dati relazionali.

## <a name="monitor"></a>Monitoraggio della campagna

Nella scheda **Monitor** sono disponibili metriche che semplificano la valutazione dell'esito e delle prestazioni del sito di marketing digitale.

![DigitalMarketingMonitor][DigitalMarketingMonitor]

È ad esempio possibile esaminare i modelli e i livelli di utilizzo mediante metriche quali **CPU Time**, **Requests** e **Data Out**. I valori corrispondenti a tali metriche crescono insieme all'aumento di popolarità della campagna marketing. Le informazioni sull'utilizzo possono consentire di decidere quando applicare la scalabilità orizzontale o verticale. Per altre informazioni, vedere [Come monitorare i siti Web][Come monitorare i siti Web].

Se si usano le modalità **Free** e **Shared**, è consigliabile esaminare anche le quote di risorse. Nella scheda **Dashboard** del portale vengono mostrate le statistiche di utilizzo correnti relative a diverse quote e viene indicato quando tali quote verranno reimpostate. Le statistiche di utilizzo visualizzate dipendono dalla modalità selezionata. Nella schermata seguente vengono mostrate le statistiche visualizzate per la modalità **Free**. Se si usa la modalità **Shared**, la quota per **Data Out** non verrà visualizzata. Nella modalità **Standard** vengono visualizzate solo le quote per **File System Storage** e **Size**.

![DigitalMarketingUsageOverview][DigitalMarketingUsageOverview]

Se tali quote sono quasi esaurite, è consigliabile valutare il passaggio dalla modalità **Free** alla modalità **Shared** oppure dalla modalità **Shared** alla modalità **Standard**. La modalità **Standard** offre risorse dedicate su una o più macchine virtuali di piccole, medie o grandi dimensioni.

Oltre a usare il portale di gestione per questo tipo di informazioni, è possibile usare alcuni strumenti di terze parti che forniscono dati di monitoraggio avanzati per i siti Web. È ad esempio possibile installare un componente aggiuntivo New Relic da Azure Store. Per una procedura dettagliata relativa all'utilizzo di New Relic per il monitoraggio, vedere [Gestione delle prestazioni delle applicazioni con New Relic in Siti Web di Azure][Gestione delle prestazioni delle applicazioni con New Relic in Siti Web di Azure].

È infine possibile abilitare il monitoraggio e gli avvisi relativi agli endpoint nei siti Web in modalità Standard. Il monitoraggio degli endpoint esegue regolarmente il tentativo di raggiungere il sito Web e segnala quindi il tempo di risposta rilevato. Gli avvisi forniscono notifiche tramite posta elettronica nel caso in cui il tempo di risposta superi una soglia specificata. Per altre informazioni, vedere la sezione relativa al monitoraggio dello scenario basato sulla [presenza Web globale][esaminare le informazioni disponibili] e l'argomento [Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure][Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure].

## <a name="summary"></a>Riepilogo

Siti Web di Azure costituisce una soluzione ideale per il contenuto Web riutilizzabile, personalizzato per singole campagne marketing. Offre supporto per molti dei linguaggi, framework e sistemi di controllo di gestione del codice sorgente più diffusi, semplificando la migrazione delle risorse e dei flussi di lavoro nel cloud. Il modello dell'applicazione Facebook ASP.NET semplifica la creazione di applicazioni Facebook, ma è possibile usare praticamente qualsiasi strumento di terze parti per l'integrazione con i social media che supporti le interfacce Web. Servizi multimediali di Azure e altri servizi correlati offerti da Azure offrono strumenti aggiuntivi per la creazione di un sito progettato in modo adeguato per una campagna. Le diverse opzioni per la scalabilità manuale e automatica risultano inoltre utili per la gestione della domanda da parte degli utenti, che può essere difficile da prevedere. Per altre informazioni, vedere gli articoli tecnici seguenti.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Area</th>
   <th align="left" valign="top">Risorse</th>
</tr>
<tr>
   <td valign="middle"><strong>Pianificazione</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/choose-web-app-service">Confronto tra Siti Web, Servizi cloud e Macchine virtuali di Azure</a></td>
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
   <td valign="top">- <a href="http://www.windowsazure.com/it-it/develop/net/tutorials/web-site-with-sql-database/">Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Windows Azure</a><br/>- <a href="http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx">Creazione di un'app relativa a Facebook usando i modelli Facebook MVC ASP.NET</a><br/>- <a href="http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx">Modello per un'applicazione Facebook e libreria per MVC ASP.NET</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Scala</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-scale-websites/">Come scalare siti Web</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Dati multimediali elaborati</strong></td>
   <td valign="top">- <a href="http://msdn.microsoft.com/it-it/library/windowsazure/dn223282.aspx">Introduzione a concetti e scenari di Servizi multimediali di Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Monitor</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-monitor-websites/">Come monitorare i siti Web</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure</a></td>
</tr>
</table>

  [Siti Web di Azure]: /it-it/documentation/services/web-sites/
  [Servizi cloud di Azure]: /it-it/documentation/services/cloud-services/
  [Macchine virtuali di Azure]: /it-it/documentation/services/virtual-machines/
  [Confronto tra Siti Web, Servizi cloud e macchine virtuali di Azure]: /it-it/manage/services/web-sites/choose-web-app-service
  [Distribuzione di siti Web esistenti]: #deployexisting
  [Integrazione con i social media]: #socialmedia
  [Scalabilità in base alla domanda da parte degli utenti]: #scale
  [Integrazione con altri servizi]: #integrate
  [Monitoraggio della campagna]: #monitor
  [presenza Web globale]: http://www.windowsazure.com/it-it/manage/services/web-sites/global-web-presence-solution-overview/
  [esaminare le informazioni disponibili]: /it-it/manage/services/web-sites/global-web-presence-solution-overview/
  [DigitalMarketingDeploy1]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy1.png
  [DigitalMarketingDeploy2]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy2.png
  [ambienti multipli con Siti Web di Azure]: http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
  [Pubblicazione da controllo del codice sorgente in Siti Web di Azure]: /it-it/develop/net/common-tasks/publishing-with-git/
  [DigitalMarketingFrameworkVersions]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_FrameworkVersions.png
  [Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure]: /it-it/develop/net/tutorials/web-site-with-sql-database/
  [un elenco di librerie disponibili che supportano l'API Twitter]: https://dev.twitter.com/docs/twitter-libraries#dotnet
  [DigitalMarketingFacebook]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Facebook.png
  [creazione di un'app Facebook usando i modelli Facebook MVC ASP.NET e al relativo hosting gratuito in Siti Web di Azure]: http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx
  [app Facebook MVC ASP.NET per i compleanni]: http://www.asp.net/mvc/tutorials/mvc-4/aspnet-mvc-facebook-birthday-app
  [nuovo modello per l'applicazione Facebook e la libreria MVC ASP.NET]: http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx
  [portale di gestione di Azure]: http://manage.windowsazure.com/
  [API di gestione del servizio]: http://msdn.microsoft.com/it-it/library/windowsazure/ee460799.aspx
  [scripting PowerShell]: http://msdn.microsoft.com/it-it/library/windowsazure/jj152841.aspx
  [DigitalMarketingScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Scale.png
  [indicazioni per la determinazione dei prezzi per i siti Web]: https://www.windowsazure.com/it-it/pricing/details/web-sites/
  [DigitalMarketingAutoScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_AutoScale.png
  [Come aggiungere risorse a un sito Web]: /it-it/manage/services/web-sites/how-to-scale-websites/
  [Introduzione a concetti e scenari di Servizi multimediali di Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/dn223282.aspx
  [Servizio cache di Azure (anteprima)]: http://msdn.microsoft.com/it-it/library/windowsazure/dn386094.aspx
  [DigitalMarketingMonitor]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Monitor.png
  [Come monitorare i siti Web]: /it-it/manage/services/web-sites/how-to-monitor-websites/
  [DigitalMarketingUsageOverview]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_UsageOverview.png
  [Gestione delle prestazioni delle applicazioni con New Relic in Siti Web di Azure]: /it-it/develop/net/how-to-guides/new-relic/
  [Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx
