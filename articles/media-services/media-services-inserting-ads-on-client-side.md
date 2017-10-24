---
title: Inserimento di annunci sul lato client | Microsoft Docs
description: Questo argomento illustra come inserire annunci sul lato client.
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.openlocfilehash: 52ba731f88c630830560e3cf8406ba2e9613c8a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="inserting-ads-on-the-client-side"></a>Inserimento di annunci sul lato client
Questo argomento contiene informazioni su come inserire diversi tipi di annunci sul lato client.

Per informazioni sul supporto di sottotitoli codificati e annunci nei video in streaming live, vedere, [Sottotitoli codificati supportati e standard per l'inserimento di annunci](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Azure Media Player attualmente non supporta gli annunci.
> 
> 

## <a id="insert_ads_into_media"></a>Inserimento di annunci nei file multimediali
Servizi multimediali di Azure offre il supporto per l'inserimento di annunci tramite la piattaforma Windows Media Platform, ovvero i player framework. Player Framework con supporto per gli annunci sono disponibili per i dispositivi Windows 8, Silverlight, Windows Phone 8 e iOS. Ogni player framework contiene codice di esempio che illustra come implementare un'applicazione di tipo lettore. È possibile inserire tre tipi diversi di annunci nei file multimediali.

* **Lineari** : annunci con frequenza massima che interrompono il video principale.
* **Non lineari** : annunci sovrapposti visualizzati durante la riproduzione del video principale, in genere un logo o un'altra immagine statica all'interno del lettore.
* **Complementari** : annunci visualizzati all'esterno del lettore.

Gli annunci possono essere inseriti in qualsiasi punto della sequenza temporale del video principale. È necessario indicare al lettore quando riprodurre l'annuncio e quali annunci riprodurre. Questa operazione viene eseguita mediante una serie di file standard basati su XML: Video Ad Service Template (VAST), Digital Video Multiple Ad Playlist (VMAP), Media Abstract Sequencing Template (MAST) e Digital Video Player Ad Interface Definition (VPAID). I file VAST indicano quali annunci visualizzare, mentre i file VMAP specificano quando riprodurre i vari annunci e contengono XML VAST. I file MAST rappresentano invece un altro modo di riprodurre in sequenza annunci contenenti XML VAST. I file VPAID, infine, definiscono un'interfaccia tra il lettore video e l'annuncio o il server di annunci.

Ogni Player Framework ha un funzionamento diverso, che verrà illustrato in un argomento specifico. Questo argomento illustra i meccanismi di base usati per inserire gli annunci. Le applicazioni di tipo lettore video richiedono gli annunci da un server di annunci. Il server di annunci può rispondere in diversi modi:

* Restituzione di un file VAST
* Restituzione di un file VMAP (con VAST incorporato)
* Restituzione di un file MAST (con VAST incorporato)
* Restituzione di un file VAST con annunci VPAID

### <a name="using-a-video-ad-service-template-vast-file"></a>Uso di un file VAST (Video Ad Service Template)
Un file VAST specifica gli annunci da visualizzare. Il codice XML seguente è un esempio di un file VAST per un annuncio lineare:

    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>

L'annuncio lineare viene descritto dall'elemento <**Linear**>. Specifica la durata dell'annuncio, gli eventi di rilevamento, il clickthrough, il monitoraggio dei clic e alcuni elementi **MediaFile**. Gli eventi di rilevamento vengono specificati entro l'elemento <**TrackingEvents**> e permette a un server di annunci di rilevare diversi elementi che si verificano durante la visualizzazione dell'annuncio. In questo caso vengono rilevati gli eventi iniziali, intermedi, di completamento e di espansione. L'evento iniziale si verifica quando l'annuncio viene visualizzato. L'evento intermedio si verifica quando è stato visualizzato almeno il 50% della sequenza temporale dell'annuncio. L'evento di completamento si verifica quando l'esecuzione dell'annuncio è stata completata. L'evento di espansione di verifica quando l'utente espande il lettore video visualizzandolo a schermo intero. I clickthrough vengono specificati con un elemento <**ClickThrough**> entro un elemento <**VideoClicks**> e specifica un URI per una risorsa da visualizzare quando l'utente fa clic sull'annuncio. Il monitoraggio dei clic viene specificato in un elemento <**ClickTracking**>, incluso in un elemento <**VideoClicks**> e specifica una risorsa di rilevamento che il lettore può richiedere quando l'utente fa clic sull'annuncio. Gli elementi <**MediaFile**> specificano informazioni su una codifica specifica di un annuncio. Quando sono presenti più elementi <**MediaFile**>, il lettore video può scegliere la codifica migliore per la piattaforma. 

Gli annunci lineari possono essere visualizzati in un ordine specifico. A tale scopo, aggiungere altri elementi <Ad> al file VAST e specificare l'ordine usando l'attributo di sequenza. L'esempio seguente illustra questi concetti.

    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>

Anche gli annunci non lineari vengono specificati in un elemento <Creative>. L'esempio seguente illustra un elemento <Creative> che descrive un annuncio non lineare.

    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>


L'elemento <**NonLinearAds**> può contenere uno o più elementi <**NonLinear**>, ognuno dei quali può descrivere un annuncio non lineare. L'elemento <**NonLinear**> specifica la risorsa per l'annuncio non lineare. La risorsa può essere di tipo <**StaticResouce**>, <**IFrameResource**> o <**HTMLResouce**>. <**StaticResource**> descrive una risorsa non HTML e definisce un attributo creativeType che specifica la modalità di visualizzazione della risorsa:

Image/gif, image/jpeg, image/png: la risorsa viene visualizzata in un tag HTML <**img**>.

Application/x-javascript: la risorsa viene visualizzata in un tag HTML <**script**>.

Application/x-shockwave-flash: la risorsa viene visualizzata in un lettore Flash.

**IFrameResource** descrive una risorsa HTML che può essere visualizzata in un IFrame. **HTMLResource** descrive una parte di codice HTML che può essere inserita in una pagina Web. **TrackingEvents** specifica gli eventi di rilevamento e l'URI da richiedere quando si verifica un evento. In questo esempio vengono rilevati gli eventi acceptInvitation e collapse. Per altre informazioni sull'elemento **NonLinearAds** e i rispettivi figli, vedere IAB.NET/VAST. Si noti che l'elemento **TrackingEvents** si trova entro l'elemento **NonLinearAds** invece dell'elemento **NonLinear**.

Gli annunci complementari vengono definiti entro un elemento <CompanionAds>. L'elemento <CompanionAds> può contenere uno o più elementi <Companion>. Ogni elemento <Companion> descrive un annuncio complementare e può contenere una risorsa di tipo <StaticResource>, <IFrameResource>, o <HTMLResource>, specificata in modo analogo a un annuncio non lineare. Un file VAST può contenere più annunci complementari e il lettore può scegliere quello più adatto da visualizzare. Per altre informazioni su VAST, vedere [VAST 3.0](http://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Uso di un file VMAP (Video Multiple Ad Playlist) digitale
Un file VMAP permette di specificare quando si verificano le interruzioni pubblicitarie, la durata di ogni interruzione, quanti annunci possono essere visualizzati in ogni interruzione e il tipo di annunci da visualizzare in un'interruzione. L'esempio seguente illustra un file VMAP che definisce una singola interruzione pubblicitaria:

    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[http://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scaleable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>

Un file VMAP inizia con un elemento <VMAP> che include uno o più elementi <AdBreak>, ognuno dei quali definisce un'interruzione pubblicitaria. Ogni interruzione pubblicitaria specifica un tipo di interruzione, un ID di interruzione e un offset temporale. L'attributo breakType specifica il tipo di annuncio che può essere riprodotto durante l'interruzione: lineare, non lineare o di visualizzazione. Gli annunci di visualizzazione sono mappati agli annunci complementari VAST. È possibile specificare più tipi di annuncio in un elenco separato da virgole (senza spazi). breakID è un identificatore facoltativo per l'annuncio. timeOffset specifica quando deve essere visualizzato l'annuncio. Può essere specificato in uno dei modi seguenti:

1. Tempo: con formato hh:mm:ss o hh:mm:ss.mmm, dove .mmm corrisponde a millisecondi. Il valore di questo attributo specifica il tempo dall'inizio della sequenza temporale del video all'inizio dell'interruzione pubblicitaria.
2. Percentuale: con formato n% dove n indica la percentuale della sequenza temporale del video da riprodurre prima della visualizzazione dell'annuncio.
3. Inizio/Fine: specifica che un annuncio deve essere visualizzato prima o dopo la visualizzazione del video.
4. Posizione: specifica l'ordine delle interruzioni pubblicitarie quando la tempistica delle interruzioni pubblicitarie è sconosciuta ad esempio nello streaming live. L'ordine di ogni interruzione è specificato con il formato #n dove n è un valore Integer pari a 1 o superiore. 1 indica che l'annuncio deve essere riprodotto alla prima opportunità, 2 indica che l'annuncio deve essere riprodotto alla seconda opportunità e così via.

Nell'elemento <**AdBreak**> può essere presente un elemento <**AdSource**>. L'elemento <**AdSource**> contiene gli attributi seguenti:

1. ID: specifica un identificatore per l'origine dell'annuncio.
2. allowMultipleAds: valore booleano che specifica se è possibile visualizzare più annunci durante l'interruzione pubblicitaria.
3. followRedirects: valore booleano facoltativo che specifica se il lettore deve rispettare i reindirizzamenti in una risposta annuncio.

L'elemento <**AdSource**> fornisce al lettore una risposta inline all'annuncio o un riferimento a una risposta annuncio. Può contenere uno degli elementi seguenti:

* <VASTAdData> indica che una risposta annuncio VAST è incorporata nel file VMAP.
* <AdTagURI>: un URI che fa riferimento a una risposta annuncio da un altro sistema.
* <CustomAdData>: -una stringa arbitraria che rappresenta una risposta non VAST.

In questo esempio una risposta annuncio inline è specificata con un elemento <VASTAdData> che contiene una risposta annuncio VAST. Per altre informazioni sugli altri elementi, vedere [VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

L'elemento <**AdBreak**> può contenere anche un elemento <**TrackingEvents**>. L'elemento <**TrackingEvents**> permette di rilevare l'inizio o la fine di un'interruzione pubblicitaria o eventuali errori verificatisi durante l'interruzione pubblicitaria. L'elemento <**TrackingEvents**> contiene uno o più elementi <**Tracking**>, ognuno dei quali specifica un evento di rilevamento e un URI di rilevamento. Di seguito sono elencati gli eventi di rilevamento possibili:

1. breakStart: rileva l'inizio di un'interruzione pubblicitaria.
2. breakEnd: rileva il completamento di un'interruzione pubblicitaria.
3. error: rileva un errore verificatosi durante l'interruzione pubblicitaria.

L'esempio seguente mostra un file VMAP che specifica gli eventi di rilevamento.

    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>

Per altre informazioni sull'elemento <**TrackingEvents**> e i rispettivi elementi figlio, vedere http://iab.org/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Uso di un file MAST (Media Abstract Sequencing Template)
Un file MAST permette di specificare i trigger che definiscono il momento in cui è visualizzato un annuncio. Di seguito è riportato un file MAST di esempio che contiene trigger per un annuncio di tipo preroll, midroll e postroll.

    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>



Un file MAST inizia con un elemento **MAST** che contiene un elemento **triggers**. L'elemento <triggers> contiene uno o più elementi **trigger** che definiscono quando deve essere riprodotto un annuncio. 

L'elemento **trigger** contiene un elemento **startConditions** che specifica quando deve iniziare la riproduzione di un annuncio. L'elemento **startConditions** contiene uno o più elementi <condition>. Quando ogni elemento <condition> restituisce true, sarà avviato o revocato un trigger, rispettivamente in base alla presenza di <condition> in un elemento **startConditions** o **endConditions**. Se sono presenti più elementi <condition>, saranno considerati come OR implicito e qualsiasi condizione che restituisce true provocherà l'avvio del trigger. Gli elementi <condition> possono essere annidati. Quando gli elementi <condition> figlio sono preimpostati, sono considerati come AND implicito e per l'avvio del trigger tutte le condizioni devono restituire true. L'elemento <condition> contiene gli attributi seguenti che definiscono la condizione: 

1. **type** - specifica il tipo di condizione, di evento o di proprietà.
2. **name** - nome della proprietà o dell'evento da usare durante la valutazione.
3. **value** – valore in base al quale sarà valutata una proprietà.
4. **operator** : operazione da usare durante la valutazione: EQ (uguale), NEQ (diverso da), GTR (maggiore), GEQ (maggiore o uguale), LT (minore), LEQ (minore o uguale), MOD (modulo).

**endConditions** contengono anche elementi <condition>. Quando una condizione restituisce true, il trigger viene reimpostato. L'elemento <trigger> contiene anche un elemento <sources> che include uno o più elementi <source>. Gli elementi <source> definiscono l'URI per la risposta annuncio e il tipo della risposta annuncio. In questo esempio si assegna un URI a una risposta VAST. 

    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>


### <a name="using-video-player-ad-interface-definition-vpaid"></a>Uso di VPAID (Video Player-Ad Interface Definition)
VPAID è un'API che permette alle unità di annuncio eseguibili di comunicare con un lettore video. Ciò offre esperienze altamente interattive per gli annunci. L'utente può interagire con l'annuncio e l'annuncio può rispondere alle azioni eseguite dall'utente. Ad esempio, un annuncio può mostrare pulsanti che permettono all'utente di visualizzare altre informazioni o una versione più lunga dell'annuncio. Il lettore video deve supportare l'API VPAID e l'annuncio eseguibile la deve implementare. Quando un lettore richiede un annuncio da un ad server, è possibile che il server risponda con una risposta VAST contenente un annuncio VPAID.

Un annuncio eseguibile è creato in codice che deve essere eseguito in un ambiente di runtime, ad esempio Adobe Flash™ o JavaScript eseguibile in un Web browser. Quando un ad server restituisce una risposta VAST contenente un annuncio VPAID, il valore dell'attributo apiFramework nell'elemento <MediaFile> deve essere "VPAID". Questo attributo specifica che l'annuncio incluso è un annuncio eseguibile VPAID. L'attributo type deve essere impostato sul tipo MIME dell'eseguibile, ad esempio "application/x-shockwave-flash" o "application/x-javascript". Il frammento di codice XML seguente mostra l'elemento <MediaFile> da una risposta VAST contenente un annuncio eseguibile VPAID. 

    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>


Un annuncio eseguibile può essere inizializzato mediante l'elemento <AdParameters> negli elementi <Linear> o <NonLinear> in una risposta VAST. Per altre informazioni sull'elemento <AdParameters>, vedere [VAST 3.0](http://www.iab.net/media/file/VASTv3.0.pdf). Per altre informazioni sull'API VPAID, vedere [VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementazione di un lettore Windows o Windows Phone 8 con supporto per gli annunci
Microsoft Media Platform: Player Framework per Windows 8 e Windows Phone 8 contiene una raccolta di applicazioni di esempio che illustrano come implementare un'applicazione per la lettura di video tramite il framework. È possibile scaricare Player Framework e i relativi esempi dalla pagina relativa a [Player Framework per Windows 8 e Windows Phone 8](https://playerframework.codeplex.com).

Quando si apre la soluzione Microsoft.PlayerFramework.Xaml.Samples, verrà visualizzato un numero di cartelle all'interno del progetto. La cartella Advertising contiene il codice di esempio necessario per la creazione di un lettore video con supporto per gli annunci. All'interno della cartella Advertising è presente un numero di file XAML/cs, ognuno dei quali mostra come inserire gli annunci in una specifica modalità. L'elenco seguente descrive i singoli file:

* AdPodPage.xaml - Mostra come visualizzare un podcast annuncio.
* AdSchedulingPage.xaml - Mostra come pianificare annunci.
* FreeWheelPage.xaml - Mostra come usare il plug-in FreeWheel per pianificare annunci
* MastPage.xaml - Mostra come pianificare annunci con un file MAST.
* ProgrammaticAdPage.xaml - Mostra come pianificare la visualizzazione di annunci in un video a livello di codice.
* ScheduleClipPage.xaml - Mostra come pianificare un annuncio senza usare un file VAST.
* VastLinearCompanionPage.xaml - Mostra come inserire annunci lineari e annunci complementari.
* VastNonLinearPage.xaml - Mostra come inserire un annuncio non lineare.
* VmapPage.xaml - Mostra come specificare annunci con un file VMAP.

Ognuno di questi esempi usa la classe MediaPlayer definita da Player Framework. Nella maggior parte degli esempi vengono usati plug-in che aggiungono supporto per vari formati di risposta annuncio. L'esempio ProgrammaticAdPage interagisce a livello di codice con un'istanza MediaPlayer.

### <a name="adpodpage-sample"></a>Esempio AdPodPage
Questo esempio usa AdSchedulerPlugin per definire quando visualizzare un annuncio. In questo esempio viene pianificata la riproduzione di un annuncio midroll dopo 5 secondi. Il podcast annuncio (ossia un gruppo di annunci visualizzati in ordine) è specificato in un file VAST restituito da un ad server. L'URI per il file VAST è specificato nell'elemento <RemoteAdSource>.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>

Per altre informazioni su AdSchedulerPlugin, vedere la pagina relativa all' [inserimento di annunci in Player Framework su Windows 8 e Windows Phone 8](http://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
Questo esempio usa AdSchedulerPlugin. Vengono pianificati tre annunci, un annuncio preroll, un annuncio midroll e un annuncio postroll. L'URI per il VAST per ciascuno di essi è specificato in un elemento <RemoteAdSource>.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>


### <a name="freewheelpage"></a>FreeWheelPage
Questo esempio usa FreeWheelPlugin, che specifica un attributo Source che a sua volta specifica un URI che punta a un file SmartXML in cui è specificato il contenuto dell'annuncio, oltre alle informazioni di pianificazione.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

### <a name="mastpage"></a>MastPage
Questo esempio usa MastSchedulerPlugin, che consente di usare un file MAST. L'attributo Source specifica il percorso del file MAST.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Questo esempio interagisce a livello di codice con MediaPlayer. Il file ProgrammaticAdPage.xaml crea un'istanza di MediaPlayer:

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>

Il file ProgrammaticAdPage.xaml.cs crea un oggetto AdHandlerPlugin, aggiunge un elemento TimelineMarker per specificare quando un annuncio deve essere visualizzato, quindi aggiunge un gestore per l'evento MarkerReached che carica un oggetto RemoteAdSource specificando un URI a un file VAST, quindi riproduce l'annuncio.

    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }

### <a name="scheduleclippage"></a>ScheduleClipPage
Questo esempio usa AdSchedulerPlugin per pianificare un annuncio midroll specificando un file con estensione wmv contenente l'annuncio.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPage
L'esempio illustra come usare AdSchedulerPlugin per pianificare un annuncio lineare midroll con un annuncio complementare. L'elemento <RemoteAdSource> specifica il percorso del file VAST.

    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

### <a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage
Questo esempio usa l'elemento AdSchedulerPlugin per pianificare un annuncio lineare e uno non lineare. Il percorso del file VAST è specificato nell'elemento <RemoteAdSource>.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

### <a name="vmappage"></a>VMAPPage
Questo esempio usa l'elemento VmapSchedulerPlugin per pianificare annunci usando un file VMAP. L'URI al file VMAP è specificato nell'attributo Source dell'elemento <VmapSchedulerPlugin>.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Implementazione di un lettore video iOS con supporto per gli annunci
Microsoft Media Platform: Player Framework per iOS contiene una raccolta di applicazioni di esempio che illustrano come implementare un'applicazione per la lettura di video tramite il framework. È possibile scaricare Player Framework e i relativi esempi dalla pagina relativa a [Media Player Framework di Azure](https://github.com/Azure/azure-media-player-framework). La pagina di GitHub include un collegamento a una pagina Wiki che contiene informazioni aggiuntive su player framework e un'introduzione all'esempio del lettore, ovvero la pagina [Wiki su Media Player di Azure](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Pianificazione di annunci con VMAP
L'esempio seguente illustra come pianificare gli annunci usando un file VMAP.

    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }          
            }

### <a name="scheduling-ads-with-vast"></a>Pianificazione di annunci con VAST
L'esempio seguente illustra come pianificare un annuncio VAST ad associazione tardiva.

    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

   L'esempio seguente illustra come pianificare un annuncio VAST ad associazione anticipata.
//Example:4 Schedule an early binding VAST ad //Download the VAST file if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]]) { [self logFrameworkError]; } else { adLinearTime.startTime = 7; adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }

L'esempio seguente illustra come inserire un annuncio usando Rough Cut Editing (RCE)

    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

L'esempio seguente illustra come pianificare un podcast annuncio.

    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

L'esempio seguente illustra come pianificare un annuncio midroll temporaneo. Un annuncio temporaneo viene riprodotto solo una volta, indipendentemente da qualsiasi ricerca eseguita dal visualizzatore.

    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

L'esempio seguente illustra come pianificare un annuncio midroll permanente. Un annuncio permanente verrà visualizzato ogni volta che viene raggiunto il punto specificato nella sequenza temporale del video.

    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }


L'esempio seguente illustra come pianificare un annuncio postroll.

    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

L'esempio seguente illustra come pianificare un annuncio preroll.

    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

L'esempio seguente illustra come pianificare un annuncio midroll sovrapposto.

    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }



## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Vedere anche
[Sviluppo di applicazioni di lettore video](media-services-develop-video-players.md)

