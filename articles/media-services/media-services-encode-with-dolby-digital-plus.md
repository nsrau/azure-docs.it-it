<properties 
	pageTitle="Codifica di file multimediali con Dolby Digital Plus" 
	description="Questo argomento illustra come codificare i file multimediali con Dolby Digital Plus." 
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
	ms.date="06/29/2015" 
	ms.author="juliako"/>

#Codifica di file multimediali con Dolby Digital Plus

Azure Media Encoder supporta la codifica **Dolby® Digital Plus** o Enhanced AC-3 (E-AC-3), un codec audio avanzato di tipo surround appositamente progettato per la creazione di file multimediali di alta qualità. Da home theater e PC a telefoni cellulari e streaming online, la tecnologia Dolby Digital Plus è in grado di offrire prestazioni audio ad alta fedeltà e di qualità cinematografica da qualsiasi dispositivo di intrattenimento. Dolby Digital Plus si basa sulle principali tecnologie Dolby Digital, che rappresentano lo standard consolidato per i sistemi audio di tipo surround di cinema, trasmissioni radiotelevisive e home theater e si stanno progressivamente espandendo nell'ambito dei dispositivi per l'intrattenimento mobile. Le nuove tecnologie avanzate del sistema Dolby, orientate al miglioramento delle prestazioni audio, offrono un'ottima qualità sonora e la possibilità di usare una larghezza di banda ridotta. In questo modo, è possibile visualizzare contenuti di qualità con meno interruzioni, anche quando la larghezza di banda è limitata.


##Configurare Azure Media Encoder per la codifica con Dolby Digital Plus

###Ottenere il processore di Azure Media Encoder 

La tecnologia Dolby Digital Plus è supportata da Azure Media Encoder. Per ottenere un riferimento ad **Azure Media Encoder**, vedere l'argomento che illustra come [ottenere i processori di contenuti multimediali](media-services-get-media-processor.md).

###<a id="configure_preset"></a>Configurare le impostazioni di Azure Media Encoder

Quando si configurano le impostazioni di codifica per l'uso con Azure Media Encoder, sono disponibili numerosi set di impostazioni predefiniti rappresentati da stringhe di facile memorizzazione. Il codificatore Dolby Digital Plus offre un'ampia gamma di controlli. Per altre informazioni, vedere [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx). Non sono pertanto disponibili set di impostazioni predefiniti in formato stringa basati su questo codec. È necessario specificare le impostazioni del codificatore desiderate in un file XML e inviare tali dati con l'attività, come illustrato nel seguente esempio di codice:
	
	string configuration = File.ReadAllText(pathToXMLConfigFile));

    ITask task = job.Tasks.AddNew("My Dolby Digital Plus Encode Task",
        processor,
        configuration,
        _clearConfig);

Questo argomento descrive alcuni set di impostazioni XML di esempio per la configurazione delle impostazioni del codificatore. L'elemento usato per configurare la codifica Dolby Digital Plus è [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx) ed è riportato come nodo figlio dell'elemento <AudioProfile> in un set di impostazioni XML di Azure Media Encoder. Questo elemento XML contiene più attributi che controllano vari elementi della codifica.

##Codifica nel formato Dolby Digital Plus 5.1 multicanale

Per eseguire la codifica nel formato Dolby Digital Plus 5.1 multicanale, impostare gli attributi Codec ed EncoderMode su "DolbyDigitalPlus". Per specificare il numero di canali codificati, usare l'attributo AudioCodingMode dell'elemento <DolbyDigitalPlusAudioProfile>. Per la codifica nel formato 5.1 multicanale, impostare AudioCodingMode su "Mode32".

Il seguente set di impostazioni XML include un set di impostazioni XML completo di Azure Media Encoder che genera un file MP4 con video H264 Broadband 1080p e audio Dolby Digital Plus 5.1 multicanale. Questo set impostazioni specifica anche la codifica nel canale LFE (Low Frequency Effects), specificato impostando l'attributo LFEOn su true. Per eventuali attributi non specificati vengono impostati i valori predefiniti.

Questo set di impostazioni XML deve essere passato ad **Azure Media Encoder** per creare un processo di codifica, come descritto in [questo](media-services-dotnet-encode-asset.md) argomento. L'intero set di impostazioni XML viene passato solo in sostituzione di una stringa del set di impostazioni predefinita, come descritto [qui](#configure_preset).


	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode32"
	              LFEOn="True"
	              SamplesPerSecond="48000"
	              BandwidthLimitingLowpassFilter="True"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="512"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="3"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Balanced"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="64"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	                <StreamInfo
	                  Size="1920, 1080">
	                  <Bitrate>
	                    <ConstantBitrate
	                      Bitrate="6000"
	                      IsTwoPass="False"
	                      BufferWindow="00:00:05" />
	                  </Bitrate>
	                </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

##Codifica nel formato Dolby Digital Plus Stereo

Per eseguire la codifica nel formato Dolby Digital Plus Stereo, impostare gli attributi Codec ed EncoderMode su "DolbyDigitalPlus". Il numero di canali codificati viene specificato mediante l'attributo AudioCodingMode. Per la codifica nel formato stereo, impostare AudioCodingMode su "Mode20". Il seguente esempio di set di impostazioni XML mostra l'elemento <DolbyDigitalPlusAudioProfile> usato per la codifica nel formato audio 5.1. Per eventuali attributi non specificati vengono impostati i valori predefiniti.

Questo set di impostazioni XML deve essere passato ad **Azure Media Encoder** per creare un processo di codifica, come descritto in [questo](media-services-dotnet-encode-asset.md) argomento. L'intero set di impostazioni XML viene passato solo in sostituzione di una stringa del set di impostazioni predefinita, come descritto [qui](#configure_preset).

	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode20"
	              LFEOn="False"
	              SamplesPerSecond="48000"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="128"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="1"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Speed"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="32"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	              <StreamInfo
	                Size="852, 480">
	                <Bitrate>
	                  <ConstantBitrate
	                    Bitrate="2200"
	                    IsTwoPass="False"
	                    BufferWindow="00:00:05" />
	                </Bitrate>
	              </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

##Codifica in più file MP4 

È possibile eseguire la codifica in più file MP4 all'interno di un singolo set di impostazioni XML. Per ogni file MP4 da generare, aggiungere un elemento <Preset> nella configurazione. Ogni elemento <Preset> può contenere informazioni di configurazione per video, audio o entrambi.

###Configurazione

La configurazione riportata di seguito genera i seguenti output:

- 8 file MP4 solo video
	- Video a 1080p e 6000 kbps
	- Video a 1080p e 4700 kbps
	- Video a 720p e 3400 kbps
	- Video a 960x540 e 2250 kbps
	- Video a 960x540 e 1500 kbps
	- Video a 640x380 e 1000 kbps
	- Video a 640x380 e 650 kbps
	- Video a 320x180 e 400 kbps

- 5 file MP4 solo audio
	- AAC Audio Stereo a 128 kbps
	- AAC Audio 5.1 a 512 kbps
	- Dolby Digital Plus Stereo a 128 kbps
	- Dolby Digital Plus 5.1 multicanale a 512 kbps
	- AAC Stereo a 56 kbps
- Un manifesto con estensione ism
- Un file XML contenente le proprietà dei file MP4 generati.
		
		<?xml version="1.0" encoding="utf-16"?>
		<!--Created for Azure Media Encoder, May 16 2013 -->
		<Presets>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"   
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <VideoProfile Condition="SourceContainsVideo">
		            <HighH264VideoProfile
		              BFrameCount="3"
		              EntropyMode="Cabac"
		              RDOptimizationMode="Speed"
		              HadamardTransform="False"
		              SubBlockMotionSearchMode="Speed"
		              MultiReferenceMotionSearchMode="Balanced"
		              ReferenceBFrames="False"
		              AdaptiveBFrames="True"
		              SceneChangeDetector="True"
		              FastIntraDecisions="False"
		              FastInterDecisions="False"
		              SubPixelMode="Quarter"
		              SliceCount="0"
		              KeyFrameDistance="00:00:05"
		              InLoopFilter="True"
		              MEPartitionLevel="EightByEight"
		              ReferenceFrames="4"
		              SearchRange="64"
		              AutoFit="True"
		              Force16Pixels="False"
		              FrameRate="0"
		              SeparateFilesPerStream="True"
		              SmoothStreaming="False"
		              NumberOfEncoderThreads="0">
		              <Streams
		                AutoSize="False"
		                FreezeSort="False">
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="6000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="4700"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1280, 720">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="3400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="2250"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1500"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="650"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="320, 180">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		              </Streams>
		            </HighH264VideoProfile>
		          </VideoProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="PropagateSourceTimeStamps">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="6"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="2"
		              AudioCodingMode="Mode20"
		              LFEOn="False"
		              NinetyDegreePhaseShiftSurrounds="False"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="False"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile Condition="SourceContainsAudio">
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="6"
		              AudioCodingMode="Mode32"
		              LFEOn="True"
		              NinetyDegreePhaseShiftSurrounds="True"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="True"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="56"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		</Presets>

##Creazione di servizi di codifica commerciali

Alcuni clienti possono avere l'esigenza di creare un servizio di codifica commerciale basato su Servizi multimediali di Azure. Se si prevede di creare un servizio integrato di questo tipo, è importante che tutti i parametri di codifica Dolby Digital Plus siano disponibili. Assicurarsi che tutti i parametri inclusi nel tag <DolbyDigitalPlusAudioProfile> siano esposti e configurabili da parte dell'utente. Per istruzioni su come rendere disponibili tali parametri, contattare prolicensingsupport@dolby.com.

##Uso del supporto DPLM (Dolby Professional Loudness Metering)

Azure Media Encoder può usare DPLM SDK per misurare la sonorità dei dialoghi nell'audio di input e impostare il valore corretto di DialogNormalization. Questa funzionalità è abilitata solo se l'audio viene codificato in Dolby Digital Plus. DPLM è configurato in un file di configurazione di set di impostazioni mediante <LoudnessMetering>, un elemento figlio di <DolbyDigitalPlusAudioProfile>. Il seguente set di impostazioni di esempio mostra come configurare DPLM:
	
	<?xml version="1.0" encoding="utf-16"?>
	<Preset
	  Version="5.0">
	  <Job />
	  <MediaFile>
	    <OutputFormat>
	      <MP4OutputFormat
	        StreamCompatibility="Standard">
	    <AudioProfile>
	             <DolbyDigitalPlusAudioProfile
	               Codec="DolbyDigitalPlus"
	               EncoderMode="DolbyDigitalPlus"
	               DolbySurroundMode="NotIndicated"
	               StereoDownmixPreference="NotIndicated"
	               SamplesPerSecond="48000"
	               AudioCodingMode="Mode20"
	               Channels="2"
	               BitsPerSample="24">
	               <LoudnessMetering
	                 Mode= "ITU_R_BS_1770_2_DI"
	                 SpeechThreshold="20"
	                 TruePeakEmphasis="false"
	                 TruePeakDCBlock="false" />
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="320"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	     </DolbyDigitalPlusAudioProfile>
	    </AudioProfile>
	      </MP4OutputFormat>
	    </OutputFormat>
	  </MediaFile>
	</Preset>

L'elemento <LoudnessMetering> può essere specificato solo all'interno di un elemento <DolbyDigitalPlusAudioProfile>. Se si usa l'elemento <LoudnessMetering>, non è possibile usare l'attributo DialogNormalization. Se l'elemento <LoudnessMetering> e l'attributo DialogNormalization vengono usati contemporaneamente, il codificatore genera un errore. Tutti gli attributi di LoudnessMetering sono facoltativi e per impostazione predefinita vengono configurati i valori consigliati da Dolby Laboratories, Inc.

Ogni attributo è descritto nelle seguenti sezioni.

###Attributo Mode

Questo attributo determina la modalità di misurazione della sonorità. I valori consentiti sono i seguenti:

 
**ITU_R_BS_1770_2_DI** (valore predefinito) - indica ITU-R BS.1770-2 più Dialogue Intelligence

**ITU_R_BS_1770_1_DI** - indica ITU-R BS.1770-1 più Dialogue Intelligence

**ITU_R_BS_1770_2** - indica ITU-R BS.1770-2

**LEQA_DI** - indica Leq(A) più Dialogue Intelligence

**Nota:**

la modalità ** EBU R128** può essere ottenuta con **ITU_R_BS_1770_2_DI**

**Leq(A)** è incluso solo per motivi di compatibilità con le versioni precedenti e deve essere usato esclusivamente in specifici flussi di lavoro legacy.

**ITU** ha recentemente rilasciato un aggiornamento denominato BS.1770-3, che equivale a BS.1770-2 con TruePeakDCBlock e TruePeakEmphasis entrambi impostati su False.

###Attributo SpeechThreshold

Specifica una soglia per i dialoghi usati da DPLM per produrre un risultato di sonorità integrato, ad esempio scegliendo un'opzione tra il controllo del discorso, il controllo del livello e nessun controllo. L'intervallo dell'impostazione della soglia del dialogo va da 0% a 100%, con incrementi dell'1%. Questo parametro ha effetto solo quando DPLM è configurato in una modalità che usa Dialogue Intelligence, ossia che può essere specificato solo se l'attributo Mode è impostato su ITU_R_BS_1770_2_DI o ITU_R_BS_1770_1_DI. Il valore predefinito, quando Mode è ITU_R_BS_1770_2_DI o ITU_R_BS_1770_1_DI, è pari al 20%. I valori di questo attributo devono essere compresi nell'intervallo da 0 a 100 (con incrementi di 1).

###Attributo TruePeakDCBlock

Questo parametro di input specifica se il blocco DC all'interno della misurazione del true-peak è abilitato (True) o disabilitato (False). Per altre informazioni sul blocco DC, fare riferimento a ITU‐R BS.1770‐2. Il valore predefinito è False.

###Attributo TruePeakEmphasis

Specifica se il filtro di enfasi all'interno della misurazione del true-peak è abilitato (True) o disabilitato (False). Per altre informazioni sul filtro di enfasi, fare riferimento a ITU‐R BS.1770‐2. Il valore predefinito è False.


###Risultati DPLM

Quando un'attività di codifica specifica l'uso di DPLM, i risultati della misurazione della sonorità vengono inclusi nel file XML dei metadati nell'asset di output. Di seguito è fornito un esempio.
	
	<LoudnessMeteringResultParameters 
	     DPLMVersionInformation="Dolby Professional Loudness Metering Development Kit Version 1.0"
	     DialogNormalization="-15" 
	     IntegratedLoudness="-14.8487606" 
	     IntegratedLoudnessGatingMethod="2" 
	     IntegratedLoudnessSpeechPercentage="11.673481" 
	     SamplePeak="-0.7028221" 
	     TruePeak="0.705999851" />

Di seguito sono descritti i singoli attributi.

**DPLMVersionInformation** - Stringa che rappresenta la versione di DPLM SDK in uso.

**DialogNormalization** - Valore di DialNorm, espresso in decibel, misurato a partire dall'audio di input, che verrà incorporato nel flusso DD+ di output, nell'intervallo {-31, -30, …, -1} dB.

**IntegratedLoudness** - Sonorità integrata misurata da DPLM, nell'intervallo da -70 a +10 LKFS/dBFS (dove dBFS viene usato solo quando Mode è impostato su LEQA_DI).

**IntegratedLoudnessGatingMethod** - I valori validi sono: 0 – None/Ungated; 1 – Speech Gated; 2 – Level Gated.

**IntegratedLoudnessSpeechPercentage** - Questo risultato contiene la percentuale della sequenza temporale dei dati multimediali di input in cui viene rilevato il parlato. I valori sono compresi tra 0% e 100%.

**SamplePeak** - Questo risultato contiene il valore massimo di campionamento assoluto in un canale dall'ultimo ripristino della misurazione. I valori validi sono compresi tra -70 e +10 dBFS.

**TruePeak** - Questo risultato contiene il valore massimo di true‐peak assoluto in un canale dall'ultimo ripristino della misurazione. Per una descrizione del true-peak, fare riferimento a ITU‐R BS.1770‐2. I valori possono essere compresi tra -70 e 12,04 dBTP.
 

<!---HONumber=July15_HO4-->