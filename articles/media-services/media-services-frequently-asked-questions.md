<properties 
	pageTitle="Domande frequenti" 
	description="Domande frequenti (FAQ)" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/17/2015" 
	ms.author="juliako"/>


#Domande frequenti  

##Panoramica

D: Come scalare l'indicizzazione?

R: Le unità riservate sono le stesse per le attività di codifica e indicizzazione. Seguire le istruzioni in [Come scalare le unità riservate di codifica](media-services-how-to-scale.md). **Tenere presente** che le prestazioni dell'indicizzatore non vengono influenzate dal tipo di unità riservata.

D: Ho caricato, codificato e pubblicato un video. Quale può essere il motivo per cui il video non viene riprodotto quando provo a trasmetterlo in streaming?

R: Uno dei motivi più comuni per questo tipo di problema è che sull'endpoint di streaming da cui si tenta di riprodurre il video non è allocata alcuna unità riservata di streaming. Seguire le istruzioni riportate in [Come scalare unità riservate di streaming](media-services-how-to-scale.md).

D: È possibile eseguire la composizione in un flusso live?

A: La composizione in flussi live non è attualmente disponibile in Servizi multimediali di Azure Media ed è quindi necessario eseguire una composizione preliminare sul proprio computer.

D: È possibile usare la rete CDN di Azure con Live Streaming?

R: Servizi multimediali supporta l'integrazione con la rete CDN di Azure (per altre informazioni, vedere [Come gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-manage-origins.md#enable_cdn)). È quindi possibile usare Live streaming con la rete CDN. Servizi multimediali di Azure fornisce output in formato Smooth Streaming, HLS e MPEG-DASH. Tutti questi formati usano il protocollo HTTP per trasferire dati e ottenere i vantaggi derivanti dalla cache HTTP. In Live Streaming i dati audio/video effettivi vengono divisi in frammenti, ciascuno dei quali viene memorizzato nella rete CDN. L'aggiornamento è necessario solo per i dati manifesto e viene effettuato periodicamente dalla rete CDN.

D: Servizi multimediali di Azure supporta anche l'archiviazione di immagini?

R: Se si desidera archiviare immagini JPEG o PNG, è consigliabile memorizzarle nell'Archiviazione BLOB di Azure. Aggiungerle all'account di Servizi multimediali non comporta infatti alcun vantaggio, a meno che non si desideri tenerle associate ai propri asset audio o video o sia necessario usarle come sovrimpressioni nel codificatore video. Il codificatore di Servizi multimediali supporta infatti la sovrimpressione di immagini nella parte superiore dei video ed è per questo che i formati JPEG e PNG sono elencati tra i formati di input supportati. Per altre informazioni, vedere [Creazione di sovrimpressioni](https://msdn.microsoft.com/library/azure/dn640496.aspx).

D: Come è possibile copiare gli asset da un account di Servizi multimediali a un altro?

R: Per copiare gli asset da un account di Servizi multimediali a un altro, usare il metodo di estensione [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponibile nel repository [Azure Media Services .NET SDK Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions/). Per altre informazioni, vedere [questo](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) thread del forum.

<!---HONumber=July15_HO2-->