<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use Media Services" authors="" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Come utilizzare Servizi multimediali

In questa guida viene illustrato come iniziare a programmare tramite Servizi multimediali di Azure. Questa guida è costituita da un set di articoli e include una panoramica tecnica di Servizi multimediali, le procedure per la configurazione dell'account Azure per Servizi multimediali, una guida all'installazione per lo sviluppo e gli argomenti che illustrano come eseguire attività di programmazione comuni. Tra gli scenari illustrati sono inclusi: caricamento di asset, crittografia di asset, codifica di asset e distribuzione di asset. Gli esempi sono scritti in C# e prevedono l'utilizzo di Media Services SDK per .NET. Per ulteriori informazioni su Servizi multimediali di Azure, fare riferimento alla sezione [Passaggi successivi][Passaggi successivi].

È inoltre possibile programmare Servizi multimediali tramite le API REST basate su OData. È possibile compilare un'applicazione effettuando chiamate API REST a Servizi multimediali da linguaggi .NET o altri linguaggi di programmazione. Per una serie di documentazione completa sulla programmazione con l'API REST di Servizi multimediali, vedere [Creazione di applicazioni con l'API REST di Servizi multimediali di Azure][Creazione di applicazioni con l'API REST di Servizi multimediali di Azure].

Per iniziare a programmare con l'API REST di Servizi multimediali o Media Services SDK, è innanzitutto necessario abilitare l'account Azure per Servizi multimediali come descritto nella sezione [Configurare un account Azure per Servizi multimediali][Configurare un account Azure per Servizi multimediali].

Fare clic [qui][qui] per accedere alla documentazione più aggiornata relativa a Media Services SDK.

## <a name="what-are"></a><span class="short header">Informazioni su Servizi multimediali</span>

Servizi multimediali di Azure costituisce una piattaforma multimediale estensibile che integra il meglio di Microsoft Media Platform e componenti multimediali di terze parti in Azure. Servizi multimediali offre una pipeline multimediale nel cloud che consente ai partner di settore di estendere o sostituire tecnologie di componenti. Fornitori di software indipendenti e provider di contenuti multimediali possono utilizzare Servizi multimediali per creare soluzioni multimediali end-to-end. In questa panoramica vengono descritti gli scenari di sviluppo comuni e l'architettura generale per Servizi multimediali.

Nel diagramma riportato di seguito viene illustrata l'architettura di Servizi multimediali di base.

![Architettura di Servizi multimediali][Architettura di Servizi multimediali]

### Supporto delle funzionalità di Servizi multimediali

Nella versione corrente di Servizi multimediali viene fornito il set di funzionalità seguenti per lo sviluppo di applicazioni multimediali nel cloud.

-   **Inserimento**. Le operazioni di inserimento consentono di inserire gli asset nel sistema, ad esempio caricandoli e crittografandoli prima che vengano collocati nel servizio di archiviazione di Azure. Servizi multimediali offre l'integrazione con componenti partner per fornire soluzioni di caricamento rapide tramite protocollo UDP (User Diagram Protocol).
-   **Codifica**. Le operazioni di codifica consentono di codificare, trasformare e convertire asset multimediali. È possibile eseguire le attività di codifica nel cloud tramite Azure Media Encoder. Di seguito sono riportate le opzioni di codifica disponibili:
	-   Utilizzo di Azure Media Encoder e di un'ampia gamma di codec e formati standard, inclusi quelli principali, come ad esempio IIS Smooth Streaming, MP4 e conversione in Apple HTTP Live Streaming.
	-   Conversione di intere librerie o singoli file con controllo completo su input e output.
	-   Ampio set di tipi di file, formati e codec supportati (vedere [Tipi di file supportati per Servizi multimediali][Tipi di file supportati per Servizi multimediali]).
	-   Conversioni tra formati supportati. Servizi multimediali consente di convertire file MP4 ISO (con estensione mp4) al formato di file Smooth Streaming (PIFF 1.3) ( con estensione ismv o isma). Consente inoltre di convertire il formato di file Smooth Streaming (PIFF) al formato Apple HTTP Live Streaming (con estensione msu8 o ts).
-   **Protezione**. Per proteggere il contenuto è necessario crittografare il contenuto Live Streaming o su richiesta per il trasporto, l'archiviazione e la distribuzione sicuri. Servizi multimediali fornisce una soluzione DRM indipendente dalla tecnologia per la protezione del contenuto. Le tecnologie DRM attualmente supportate sono Microsoft PlayReady e MPEG Common Encryption. A breve sarà disponibile il supporto per altre tecnologie DRM.
-   **Streaming**. Durante lo streaming il contenuto viene distribuito in diretta o su richiesta ai client oppure vengono scaricati file multimediali specifici dal cloud. Servizi multimediali fornisce una soluzione indipendente dal formato per i contenuti in streaming. Servizi multimediali offre il supporto dell'origine di streaming per i formati Smooth Streaming, Apple HTTP Live Streaming ed MP4. A breve sarà disponibile il supporto per altri formati. È inoltre possibile distribuire contenuti in streaming mediante una rete CDN di terze parti che offre la scalabilità per la distribuzione a milioni di utenti.

### Scenari di sviluppo di Servizi multimediali

Servizi multimediali supporta numerosi scenari di sviluppo di contenuti multimediali comuni, come illustrato nella tabella seguente.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">

<thead>

<tr>

<th>
Scenario

</th>

<th>
Descrizione

</th>

</tr>

</thead>

<tbody>

<tr>

<td>
Creazione di flussi di lavoro end-to-end

</td>

<td>
Creazione di flussi di lavoro multimediali completi interamente nel cloud Dal caricamento dei file multimediali alla distribuzione del contenuto, Servizi multimediali offre un'ampia gamma di componenti che possono essere combinati per gestire flussi di lavoro dell'applicazione specifici. Le funzionalità correnti includono caricamento, archiviazione, codifica, conversione di formati, protezione del contenuto e distribuzione di contenuti in streaming su richiesta.

</td>

</tr>

<tr>

<td>
Creazione di flussi di lavoro ibridi

</td>

<td>
È possibile integrare Servizi multimediali con strumenti e processi esistenti. Ad esempio, codificare il contenuto in sede e quindi caricarlo in Servizi multimediali per la transcodifica in più formati e la distribuzione tramite la rete CDN di Azure o una rete CDN di terze parti. È possibile chiamare Servizi multimediali individualmente tramite le API REST standard per l'integrazione con applicazioni e servizi esterni.

</td>

</tr>

<tr>

<td>
Supporto cloud per lettori multimediali

</td>

<td>
È possibile creare, gestire e distribuire contenuti multimediali su più dispositivi (iOS, Android e Windows) e piattaforme.

</td>

</tr>

</tbody>

</table>
</p>
### <a name="media-client"></a>Sviluppo client di Servizi multimediali

È possibile estendere la copertura della soluzione Servizi multimediali utilizzando SDK e Player framework per creare applicazioni client multimediali. Questi client sono concepiti per gli sviluppatori che desiderano creare applicazioni Servizi multimediali che offrono un'esperienza utente accattivante su un'ampia gamma di dispositivi e piattaforme. A seconda dei dispositivi di destinazione, sono disponibili varie opzioni di SDK e Player Framework di Microsoft e partner di terze parti.

Di seguito è riportato un elenco di SDK e Player Framework client disponibili. Per ulteriori informazioni su questi e altri SDK e Player Framework pianificati e le relative funzionalità supportate, vedere gli argomenti relativi allo [sviluppo di client per Servizi multimediali][sviluppo di client per Servizi multimediali] nei forum dedicati a Servizi multimediali.

#### Supporto client Mac e PC

Per i PC e i Mac è possibile creare un'esperienza di streaming utilizzando Microsoft Silverlight o Adobe Open Source Media Framework.

-   [Smooth Streaming Client per Silverlight][Smooth Streaming Client per Silverlight]
-   [Microsoft Media Platform: Player Framework per Silverlight][Microsoft Media Platform: Player Framework per Silverlight]
-   [Plug-in Smooth Streaming per OSMF 2.0][Plug-in Smooth Streaming per OSMF 2.0]. Per informazioni sull'utilizzo di questo plug-in, vedere [Come utilizzare il plug-in Microsoft Smooth Streaming per Adobe Open Source Media Framework][Come utilizzare il plug-in Microsoft Smooth Streaming per Adobe Open Source Media Framework].

#### Applicazioni per Windows 8

Per Windows 8, è possibile creare app di Windows Store utilizzando uno qualsiasi dei costrutti e dei linguaggi di sviluppo supportati come HTML, JavaScript, XAML, C# e C+.

-   [Smooth Streaming Client SDK per Windows 8][Smooth Streaming Client SDK per Windows 8]. Per ulteriori informazioni su come creare un'app di Windows Store utilizzando questo SDK, vedere [Come creare un'applicazione Windows Store Smooth Streaming][Come creare un'applicazione Windows Store Smooth Streaming]. Per informazioni su come creare un lettore Smooth Streaming in HTML5, vedere la [procedura dettagliata relativa alla creazione del primo lettore Smooth Streaming in HTML5][procedura dettagliata relativa alla creazione del primo lettore Smooth Streaming in HTML5].

-   [Microsoft Media Platform: Player Framework per app di Windows Store per Windows 8][Microsoft Media Platform: Player Framework per app di Windows Store per Windows 8]

#### XBox

Xbox supporta le applicazioni Xbox LIVE che possono utilizzare contenuto Smooth Streaming. L'ADK (Application Development Kit) di Xbox LIVE include:

-   Smooth Streaming Client per Xbox LIVE ADK
-   Microsoft Media Platform: Player Framework per Xbox LIVE ADK

#### Servizi incorporati o dedicati

Dispositivi quali TV connessi, set-top box, lettori Blu-Ray, OTT TV box e dispositivi mobili che dispongono di un framework di sviluppo applicazioni personalizzato e di una pipeline multimediale personalizzata. Microsoft fornisce i kit per il porting seguenti che possono essere concessi in licenza e che consentono ai partner di eseguire il porting della riproduzione Smooth Streaming per la piattaforma.

-   [Microsoft Smooth Streaming Client Porting Kit][Microsoft Smooth Streaming Client Porting Kit]
-   [Microsoft PlayReady Device Porting Kit][Microsoft PlayReady Device Porting Kit]

#### Windows Phone

Microsoft fornisce un SDK che può essere utilizzato per creare applicazioni video premium per Windows Phone.

-   [Smooth Streaming Client per Silverlight][Smooth Streaming Client per Silverlight]
-   [Microsoft Media Platform: Player Framework per Silverlight][Microsoft Media Platform: Player Framework per Silverlight]

#### Dispositivi iOS

Per i dispositivi iOS, ad esempio iPhone, iPod e iPad, Microsoft fornisce un SDK che consente di creare applicazioni per queste piattaforme e distribuire contenuti video premium: Smooth Streaming SDK per dispositivi iOS con PlayReady. L'SDK è disponibile solo per i titolari di una licenza. Per richiedere ulteriori informazioni [inviare un messaggio di posta elettronica a Microsoft][inviare un messaggio di posta elettronica a Microsoft]. Per informazioni sullo sviluppo per iOS, visitare il [sito dedicato agli sviluppatori per iOS][sito dedicato agli sviluppatori per iOS].

#### Dispositivi Android

Numerosi partner Microsoft forniscono SDK per la piattaforma Android che consentono di aggiungere la funzionalità di riproduzione dei contenuti Smooth Streaming nei dispositivi Android. Per informazioni dettagliate sui partner, [inviare un messaggio di posta elettronica a Microsoft][1].

## Passaggi successivi

Dopo avere acquisito una panoramica su Servizi multimediali, è possibile passare all'argomento [Configurazione del computer per Servizi multimediali][Configurazione del computer per Servizi multimediali].

  [Passaggi successivi]: #next-steps
  [Creazione di applicazioni con l'API REST di Servizi multimediali di Azure]: http://go.microsoft.com/fwlink/?linkid=252967
  [Configurare un account Azure per Servizi multimediali]: #setup-account
  [qui]: http://msdn.microsoft.com/it-it/library/hh973613.aspx
  [Architettura di Servizi multimediali]: ./media/media-services-dotnet-how-to-use/wams-01.png
  [Tipi di file supportati per Servizi multimediali]: http://msdn.microsoft.com/it-it/library/hh973634
  [sviluppo di client per Servizi multimediali]: http://social.msdn.microsoft.com/Forums/it-it/MediaServices/thread/e9092ec6-2dfc-44cb-adce-1dc935309d2a
  [Smooth Streaming Client per Silverlight]: http://www.microsoft.com/it-it/download/details.aspx?id=29940
  [Microsoft Media Platform: Player Framework per Silverlight]: http://smf.codeplex.com/documentation
  [Plug-in Smooth Streaming per OSMF 2.0]: http://go.microsoft.com/fwlink/?LinkId=275022
  [Come utilizzare il plug-in Microsoft Smooth Streaming per Adobe Open Source Media Framework]: http://go.microsoft.com/fwlink/?LinkId=275034
  [Smooth Streaming Client SDK per Windows 8]: http://go.microsoft.com/fwlink/?LinkID=246146
  [Come creare un'applicazione Windows Store Smooth Streaming]: http://go.microsoft.com/fwlink/?LinkId=271647
  [procedura dettagliata relativa alla creazione del primo lettore Smooth Streaming in HTML5]: http://msdn.microsoft.com/it-it/library/jj573656(v=vs.90).aspx
  [Microsoft Media Platform: Player Framework per app di Windows Store per Windows 8]: http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home
  [Microsoft Smooth Streaming Client Porting Kit]: http://www.microsoft.com/it-it/mediaplatform/sspk.aspx
  [Microsoft PlayReady Device Porting Kit]: http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx
  [inviare un messaggio di posta elettronica a Microsoft]: mailto:askdrm@microsoft.com
  [sito dedicato agli sviluppatori per iOS]: https://developer.apple.com/devcenter/ios/index.action
  [1]: mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices
  [Configurazione del computer per Servizi multimediali]: http://go.microsoft.com/fwlink/?LinkId=301751
