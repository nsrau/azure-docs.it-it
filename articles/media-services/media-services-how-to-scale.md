<properties 
	pageTitle="Come scalare un servizio multimediale" 
	description="Informazioni su come scalare servizi multimediali specificando il numero di unità riservate di streaming on demand e di unità riservate di codifica di cui eseguire il provisioning nell'account in uso." 
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
	ms.date="02/20/2015" 
	ms.author="juliako"/>


# Come scalare un servizio multimediale  

## Informazioni generali

È possibile ridimensionare **Servizi multimediali** specificando il numero di **unità riservate di streaming** e **unità riservate di codifica** di cui si vuole effettuare il provisioning nell'account in uso. 

È anche possibile ridimensionare l'account di Servizi multimediali aggiungendo account di archiviazione. Per ogni account di archiviazione è previsto un limite di 500 TB. Per espandere lo spazio di archiviazione oltre i limiti predefiniti, è possibile scegliere di collegare più account di archiviazione a un singolo account di Servizi Multimediali.

Questo argomento include collegamenti agli argomenti rilevanti.

## <a id="streaming_endpoins"></a>Unità riservate di streaming

Per altre informazioni, vedere [Ridimensionare l'endpoint di streaming](media-services-manage-origins.md#scale_streaming_endpoints).

## <a id="encoding_reserved_units"></a>Unità riservate di codifica

Per informazioni sulla scalabilità delle unità di codifica, vedere i seguenti argomenti del **portale** e di **.NET**.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

Si noti che le unità riservate sono le stesse per le attività di codifica e indicizzazione.

## <a id="storage"></a>Scalabilità delle risorse di archiviazione

Per altre informazioni, vedere [Gestione di asset di Servizi multimediali su più account di archiviazione](https://msdn.microsoft.com/library/azure/dn271889.aspx) e [Uso dell'archiviazione di Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx).





<!--HONumber=52--> 