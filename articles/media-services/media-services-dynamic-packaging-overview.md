<properties
	pageTitle="Panoramica della creazione dinamica dei pacchetti"
	description="Questo argomento fornisce una panoramica della creazione dinamica dei pacchetti."
	authors="Juliako"
	manager="dwrede"
	editor=""
	services="media-services"
	documentationCenter=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/07/2015"
	ms.author="juliako"/>


#Creazione dinamica dei pacchetti

##Panoramica

Servizi multimediali di Microsoft Azure può essere usato per distribuire molti formati di file di origine multimediali, formati di streaming multimediali e formati di protezione del contenuto in un'ampia gamma di tecnologie client, ad esempio iOS, Xbox, Silverlight e Windows 8. Questi client supportano tuttavia protocolli diversi. iOS, ad esempio, richiede un formato HTTP Live Streaming (HLS) V4, mentre Silverlight e Xbox richiedono Smooth Streaming. Se è presente un set di file MP4 a velocità in bit adattiva, ovvero più velocità in bit, (ISO Base Media 14496-12) o di un set di file Smooth Streaming a velocità in bit adattiva e si vuole renderli disponibili per i client che supportano contenuto MPEG DASH, HLS o Smooth Streaming, è possibile usare la funzionalità di creazione dinamica dei pacchetti di Servizi multimediali.

Con la funzionalità di creazione dinamica dei pacchetti, è sufficiente creare un asset che contenga un set di file MP4 o Smooth Streaming a velocità in bit adattiva. In base al formato specificato nella richiesta del manifesto o del frammento, il server di streaming on demand garantirà che il flusso sia ricevuto nel protocollo scelto. Di conseguenza, si archiviano e si pagano solo i file in un singolo formato di archiviazione e il servizio Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client.

Il diagramma seguente mostra il flusso di lavoro tradizionale di codifica e creazione statica dei pacchetti.

![Codifica statica](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Il diagramma seguente mostra il flusso di lavoro di creazione dinamica dei pacchetti.

![Codifica dinamica](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]Per avvalersi della creazione dinamica dei pacchetti, è necessario ottenere prima almeno un'unità di streaming on demand per l'endpoint di streaming da cui si intende distribuire il contenuto. Per altre informazioni, vedere la sezione relativa al [ridimensionamento di Servizi multimediali](media-services-manage-origins.md#scale_streaming_endpoints).

##Scenario comune

1. Caricare un file di input (detto file in formato intermedio). Ad esempio, H.264, MP4 o WMV (per l'elenco dei formati supportati, vedere [Formati supportati dal codificatore di Servizi multimediali](media-services-azure-media-encoder-formats)).

1. Codificare il file in formato intermedio in set MP4 a velocità in bit adattiva H.264.

1. Pubblicare l'asset contenente il set MP4 a velocità in bit adattiva creando il localizzatore su richiesta.

1. Creare gli URL di streaming per accedere e al contenuto e trasmetterlo in streaming.

>[AZURE.NOTE]Non tutti i formati di file MP4 sono supportati per la creazione dinamica dei pacchetti. Per altre informazioni, vedere [Formati non supportati per la creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md#unsupported_formats).

##Preparazione di asset per lo streaming dinamico

Per preparare l'asset per lo streaming dinamico sono disponibili due opzioni:

- Caricare un file master e produrre set di file MP4 a velocità in bit adattiva H.264 usando Codificatore multimediale di Azure.
- Caricare set a velocità in bit adattiva esistenti e convalidarli tramite Media Packager.

###Caricare un file master e produrre set di file MP4 a velocità in bit adattiva H.264 usando Codificatore multimediale di Azure.

Per informazioni su come caricare e codificare asset, vedere gli articoli seguenti:


Caricare i file usando il **portale di gestione di Azure**, **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

Codificare con **Azure Media Encoder** tramite il **portale di gestione di Azure**, **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]


###Caricare set a velocità in bit adattiva esistenti e convalidarli tramite Media Packager

In genere è consigliabile eseguire questa attività se si carica un set di file MP4 a velocità in bit adattiva che non sono stati codificati con Codificatore di Servizi multimediali. L'argomento [Convalida di MP4 a velocità in bit adattiva codificati con codificatori esterni](https://msdn.microsoft.com/library/azure/dn750842.aspx) illustra come eseguire questa attività.

##Streaming dei contenuti ai client

Dopo aver creato i set a velocità in bit adattiva, è possibile pubblicare l'asset creando un localizzatore su richiesta e comporre gli URL di streaming per Smooth Streaming, MPEG DASH, HLS e HDS (solo per possessori di licenza Adobe PrimeTime/Access).

Per informazioni su come creare i localizzatori e usare la creazione dinamica dei pacchetti per trasmettere in streaming il contenuto, vedere gli argomenti seguenti:

[Panoramica della distribuzione di contenuti ai clienti](media-services-deliver-content-overview.md).

Configurare i criteri di distribuzione degli asset usando **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Pubblicare asset, mediante la creazione di localizzatori, usando il **portale di gestione di Azure** o **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


##<a id="unsupported_formats"></a>Formati non supportati dalla creazione dinamica dei pacchetti

I formati di file di origine seguenti non sono supportati dalla creazione dinamica dei pacchetti.

- File Dolby Digital Plus MP4.
- File Dolby Digital Plus Smooth.

##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=Nov15_HO3-->