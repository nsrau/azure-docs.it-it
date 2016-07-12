<properties 
	pageTitle="Plug-in Smooth Streaming per Open Source Media Framework" 
	description="Informazioni su come usare il plug-in Smooth Streaming di Servizi multimediali di Azure per Adobe Open Source Media Framework." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2016"
	ms.author="juliako"/>


# Come usare il plug-in Microsoft Smooth Streaming per Adobe Open Source Media Framework

##Panoramica


Il plug-in Microsoft Smooth Streaming per Open Source Media Framework 2.0 (SS per OSMF) estende le funzionalità predefinite di OSMF e aggiunge la funzionalità di riproduzione del contenuto Smooth Streaming ai lettori OSMF nuovi ed esistenti. Il plug-in aggiunge inoltre funzionalità di riproduzione Smooth Streaming a Strobe Media Playback (SMP).

In Smooth Streaming per OSMF sono incluse due versioni di plug-in:

- Plug-in Static Smooth Streaming per OSMF (con estensione swc)

- Plug-in Dynamic Smooth Streaming per OSMF (con estensione swf)

In questo documento si presuppone che il lettore disponga già di conoscenze tecniche generali relative a OSMF e ai plug-in per OSMF. Per altre informazioni su OSMF, consultare la documentazione sul [sito Web ufficiale di OSMF](http://osmf.org/).

###Plug-in Smooth Streaming per OSMF 2.0

Il plug-in supporta il caricamento e la riproduzione di contenuto Smooth Streaming su richiesta con le funzionalità seguenti:

- Riproduzione Smooth Streaming su richiesta (riproduzione, pausa, ricerca, arresto)
- Riproduzione Smooth Streaming in diretta (riproduzione)
- Funzioni DVR in diretta (pausa, ricerca, riproduzione DVR, modalità attiva)
- Supporto per codec video: H.264
- Supporto per codec audio: AAC
- Commutazione tra più lingue per l'audio grazie alle API incorporate in OSMF
- Selezione della qualità di riproduzione massima grazie alle API incorporate in OSMF
- Sottotitoli collaterali con il plug-in per didascalie OSMF
- Adobe&reg; Flash&reg; Player 11.4 o versione successiva.
- In questa versione è supportato solo OSMF 2.0.

## Funzionalità supportate e problemi noti

Per un elenco completo di funzionalità supportate, funzionalità non supportate e problemi noti, fare riferimento a [questo documento](http://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf).


## Caricamento del plug-in
I plug-in OSMF possono essere caricati in modo statico (in fase di compilazione) o dinamico (in fase di esecuzione). Nel download del plug-in Smooth Streaming per OSMF sono incluse entrambe le versioni statica e dinamica.

- Caricamento statico: per eseguire il caricamento in modo statico, è necessario un file di libreria statica (SWC). I plug-in statici vengono aggiunti come riferimento ai progetti e vengono uniti all'interno del file di output finale in fase di compilazione.

- Caricamento dinamico: per eseguire il caricamento dinamico, è necessario un file precompilato (SWF). I plug-in dinamici vengono caricati in fase di esecuzione e non sono inclusi nell'output del progetto. (Output compilato) I plug-in dinamici possono essere caricati tramite i protocolli HTTP e FILE.

Per altre informazioni sul caricamento statico e dinamico, vedere la [pagina ufficiale del plug-in OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

###Caricamento statico di Smooth Streaming per OSMF
Nel frammento di codice seguente viene illustrato come caricare il plug-in Smooth Streaming per OSMF in modo statico e riprodurre un video di base usando la classe MediaFactory di OSMF. Prima di includere il codice di Smooth Streaming per OSMF, verificare che nei riferimenti del progetto sia incluso il plug-in statico "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc".

```
package 
{
	
	import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;
	
	import flash.display.*;
	import org.osmf.media.*;
	import org.osmf.containers.MediaContainer;
	import org.osmf.events.MediaErrorEvent;
	import org.osmf.events.MediaFactoryEvent;
	import org.osmf.events.MediaPlayerStateChangeEvent;
	import org.osmf.layout.*;
	
	
	
	[SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
	public class TestPlayer extends Sprite
	{        
		public var _container:MediaContainer;
		public var _mediaFactory:DefaultMediaFactory;
		private var _mediaPlayerSprite:MediaPlayerSprite;
		

		public function TestPlayer( )
		{
			stage.quality = StageQuality.HIGH;

			initMediaPlayer();

		}
	
		private function initMediaPlayer():void
		{
		
			// Create the container (sprite) for managing display and layout
			_mediaPlayerSprite = new MediaPlayerSprite();    
			_mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
			_mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
			_mediaPlayerSprite.scaleMode = ScaleMode.NONE;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			//Adds the container to the stage
			addChild(_mediaPlayerSprite);
			
			// Create a mediafactory instance
			_mediaFactory = new DefaultMediaFactory();
			
			// Add the listeners for PLUGIN_LOADING
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
			
			// Load the plugin class 
			loadAdaptiveStreamingPlugin( );  
			
		}
		
		private function loadAdaptiveStreamingPlugin( ):void
		{
			var pluginResource:MediaResourceBase;
			
			pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
			_mediaFactory.loadPlugin( pluginResource ); 
		}
		
		private function onPluginLoaded( event:MediaFactoryEvent ):void
		{
			// The plugin is loaded successfully.
			// Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
		loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
		
		}
		
		private function onPluginLoadFailed( event:MediaFactoryEvent ):void
		{
			// The plugin is failed to load ...
		}
		
		
		private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
		{
			var state:String;
			
			state =  event.state;
			
			switch (state)
			{
				case MediaPlayerState.LOADING: 
					
					// A new source is started to load.
					
					break;
				
				case  MediaPlayerState.READY :   
					// Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
					
					break;
				
				case MediaPlayerState.BUFFERING :
					
					break;
				
				case  MediaPlayerState.PAUSED :
					break;      
				// other states ...          
			}
		}
		
		private function onPlayerFailed(event:MediaErrorEvent) : void
		{
			// Media Player is failed .           
		}
		
		private function loadMediaSource(sourceURL : String):void 
		{
			// Take an URL of SmoothStreamingSource's manifest and add it to the page.
			
			var resource:URLResource= new URLResource( sourceURL );
			
			var element:MediaElement = _mediaFactory.createMediaElement( resource );
			_mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			
			// Add the media element
			_mediaPlayerSprite.media = element;
		}     
		
	}
}
```


###Caricamento dinamico di Smooth Streaming per OSMF

Nel frammento di codice seguente viene illustrato come caricare il plug-in Smooth Streaming per OSMF in modo dinamico e riprodurre un video di base usando la classe MediaFactory di OSMF. Prima di includere il codice di Smooth Streaming per OSMF, copiare il plug-in dinamico "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" nella cartella del progetto se si desidera eseguire il caricamento tramite il protocollo FILE o copiarlo in un server Web per il caricamento tramite HTTP. Non è necessario includere il file "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" nei riferimenti del progetto.

 
pacchetto {
	
	import flash.display.*;
	import org.osmf.media.*;
	import org.osmf.containers.MediaContainer;
	import org.osmf.events.MediaErrorEvent;
	import org.osmf.events.MediaFactoryEvent;
	import org.osmf.events.MediaPlayerStateChangeEvent;
	import org.osmf.layout.*;
	import flash.events.Event;
	import flash.system.Capabilities;

	
	//Sets the size of the SWF
	
	[SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
	public class TestPlayer extends Sprite
	{        
		public var _container:MediaContainer;
		public var _mediaFactory:DefaultMediaFactory;
		private var _mediaPlayerSprite:MediaPlayerSprite;
		
		
		public function TestPlayer( )
		{
			stage.quality = StageQuality.HIGH;
			initMediaPlayer();
		}
		
		private function initMediaPlayer():void
		{
			
			// Create the container (sprite) for managing display and layout
			_mediaPlayerSprite = new MediaPlayerSprite();    
			_mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
			_mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

			//Adds the container to the stage
			addChild(_mediaPlayerSprite);
			
			// Create a mediafactory instance
			_mediaFactory = new DefaultMediaFactory();
			
			// Add the listeners for PLUGIN_LOADING
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
			
			// Load the plugin class 
			loadAdaptiveStreamingPlugin( );  
			
		}
		
		private function loadAdaptiveStreamingPlugin( ):void
		{
			var pluginResource:MediaResourceBase;
			var adaptiveStreamingPluginUrl:String;

			// Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

			adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
			pluginResource = new URLResource(adaptiveStreamingPluginUrl);
			_mediaFactory.loadPlugin( pluginResource ); 

		}
		
		private function onPluginLoaded( event:MediaFactoryEvent ):void
		{
			// The plugin is loaded successfully.

			// Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

	loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
		}
		
		private function onPluginLoadFailed( event:MediaFactoryEvent ):void
		{
			// The plugin is failed to load ...
		}
		
		
		private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
		{
			var state:String;
			
			state =  event.state;
			
			switch (state)
			{
				case MediaPlayerState.LOADING: 
					
					// A new source is started to load.
					
					break;
				
				case  MediaPlayerState.READY :   
					// Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
					
					break;
				
				case MediaPlayerState.BUFFERING :
					
					break;
				
				case  MediaPlayerState.PAUSED :
					break;      
				// other states ...          
			}
		}
		
		private function onPlayerFailed(event:MediaErrorEvent) : void
		{
			// Media Player is failed .           
		}
		
		private function loadMediaSource(sourceURL : String):void 
		{
			// Take an URL of SmoothStreamingSource's manifest and add it to the page.
			
			var resource:URLResource= new URLResource( sourceURL );
			
			var element:MediaElement = _mediaFactory.createMediaElement( resource );
			_mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			// Add the media element
			_mediaPlayerSprite.media = element;
		}     
		
	}
}

##Strobe Media Playback con plug-in dinamico SS per OSMF

Il plug-in dinamico Smooth Streaming per OSMF è compatibile con [Strobe Media Playback (SMP)](http://osmf.org/strobe_mediaplayback.html). È possibile utilizzare il plug-in SS per OSMF per aggiungere la riproduzione di contenuti Smooth Streaming a SMP. A questo scopo, copiare il plug-in "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" in un server Web per il caricamento tramite HTTP attenendosi alla procedura seguente:

1.	Passare alla [pagina di installazione di Strobe Media Playback](http://osmf.org/dev/2.0gm/setup.html).
2.	Impostare l'elemento src su un'origine Smooth Streaming, ad esempio http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest.
3.	Apportare le modifiche di configurazione desiderate e fare clic su Preview and Update.
 
	**Nota** Per il server Web del contenuto deve essere presente un file crossdomain.xml valido.
4.	Copiare e incollare il codice in una pagina HTML semplice usando l'editor di testo desiderato, come illustrato nell'esempio seguente:



		<html>
		<body>
		<object width="920" height="640"> 
		<param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
		<param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
		<param name="allowFullScreen" value="true"></param>
		<param name="allowscriptaccess" value="always"></param>
		<param name="wmode" value="direct"></param>
		<embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
    		type="application/x-shockwave-flash" 
    		allowscriptaccess="always" 
    		allowfullscreen="true" 
    		wmode="direct" 
    		width="920" 
    		height="640" 
    		flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
		</embed>
		</object>
		</body>
		</html>



5. Aggiungere il plug-in Smooth Streaming per OSMF al codice incorporato e salvare.

		<html>
		<object width="920" height="640"> 
		<param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
		<param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
		<param name="allowFullScreen" value="true"></param>
		<param name="allowscriptaccess" value="always"></param>
		<param name="wmode" value="direct"></param>
		<embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
    		type="application/x-shockwave-flash" 
    		allowscriptaccess="always" 
    		allowfullscreen="true" 
    		wmode="direct" 
    		width="920" 
    		height="640" 
    		flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
		</embed>
		</object>
		</html>


6. 	Salvare la pagina HTML e pubblicarla in un server Web. Passare alla pagina Web pubblicata usando un browser Internet compatibile con Flash&reg; Player, ad esempio Internet Explorer, Chrome, Firefox e così via.
7. 	Riprodurre il contenuto Smooth Streaming in Adobe&reg; Flash&reg; Player.

Per ulteriori informazioni sullo sviluppo generale per OSMF, consultare la [pagina ufficiale per lo sviluppo di OSMF](http://osmf.org/resources.html).

##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##Vedere anche

[Plug-in di streaming adattivo Microsoft per aggiornamento OSMF](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/)

<!---HONumber=AcomDC_0629_2016-->