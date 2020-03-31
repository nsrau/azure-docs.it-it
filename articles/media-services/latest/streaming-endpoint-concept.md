---
title: Endpoint di streaming (origine)Streaming Endpoints (Origin)
titleSuffix: Azure Media Services
description: Informazioni sugli endpoint di streaming (Origin), un servizio dinamico di creazione di pacchetti e streaming che distribuisce contenuti direttamente a un'app lettore client o a una rete CDN (Content Delivery Network).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: 1a2a370ac92ea3edf925d97af6f5e721c79d0d2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529697"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Streaming Endpoints (Origin) in Azure Media Services

In Servizi multimediali di Microsoft Azure, un endpoint di [streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) rappresenta un servizio di creazione di pacchetti e origini dinamico (just-in-time) in grado di distribuire il contenuto live e su richiesta direttamente a un'app lettore client usando uno dei protocolli multimediali di streaming comuni (HLS o DASH). Inoltre, **l'endpoint** di streaming fornisce la crittografia dinamica (just-in-time) ai DRM leader del settore. 

Quando si crea un account di Servizi multimediali viene creato un endpoint di streaming **predefinito** nello stato Arrestato. Non è possibile eliminare l'endpoint di streaming **predefinito.** È possibile creare altri endpoint di streaming con l'account (vedere [Quote e limitazioni).](limits-quotas-constraints.md)

> [!NOTE]
> Per avviare lo streaming di video, è necessario avviare l **'endpoint di streaming** da cui si vuole trasmettere il video.
>
> La fatturazione viene fatturata solo quando l'endpoint di streaming è in esecuzione.

Assicurarsi di esaminare anche l'argomento [Pacchetto dinamico.](dynamic-packaging-overview.md) 

## <a name="naming-convention"></a>Convenzione di denominazione

Il formato del nome host `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`dell'URL di streaming è: , dove: `servicename` il nome dell'endpoint di streaming o il nome dell'evento live.

Quando si utilizza l'endpoint di streaming predefinito, `servicename` viene omesso in modo che l'URL sia: `{accountname}-{regionname}.streaming.azure.net`.

### <a name="limitations"></a>Limitazioni

* Il nome dell'endpoint di streaming ha un valore massimo di 24 caratteri.
* Il nome deve seguire questo `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`modello di espressione [regolare:](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) .

## <a name="types"></a>Tipi

Esistono due tipi di endpoint di **streaming:** **Standard** (anteprima) e **Premium**. Il tipo è definito in base al numero di unità di scala (`scaleUnits`) allocate per l'endpoint di streaming.

La tabella seguente descrive i tipi:

|Type|Unità di scala|Descrizione|
|--------|--------|--------|  
|**Standard**|0|L'endpoint di streaming predefinito è un tipo **Standard,** che `scaleUnits`può essere modificato nel tipo Premium regolando .|
|**Premium**|>0|**Premium** Gli endpoint di streaming sono adatti per carichi di lavoro avanzati e per fornire una capacità di larghezza di banda dedicata e scalabile. Si passa **Premium** a un tipo `scaleUnits` Premium regolando (unità di streaming). Il valore `scaleUnits` rappresenta la capacità di uscita dedicata acquistabile in incrementi di 200 Mbps. Quando si usa il tipo **Premium,** ogni unità abilitata fornisce ulteriore capacità di larghezza di banda all'app. |

> [!NOTE]
> Per i clienti che desiderano distribuire contenuti a un vasto pubblico Internet, è consigliabile abilitare la rete CDN nell'endpoint di streaming.

Per informazioni sul servizio di sla, vedere [Prezzi e sLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Confronto tra le tipologie di streaming

Funzionalità|Standard|Premium
---|---|---
Velocità effettiva |Fino a 600 Mbps e può fornire una velocità effettiva molto più elevata quando viene usata una rete CDN.|200 Mbps per unità di streaming (SU). Può fornire una velocità effettiva molto più elevata quando viene usata una rete CDN.
RETE CDN|Rete CDN di Azure, rete CDN di terze parti o nessuna rete CDN.|Rete CDN di Azure, rete CDN di terze parti o nessuna rete CDN.
Fatturazione con ripartizione proporzionale| Giornaliera|Giornaliera
Crittografia dinamica|Sì|Sì
creazione dinamica dei pacchetti|Sì|Sì
Scalabilità|Scalabilità automatica fino alla velocità effettiva di destinazione.|SUs aggiuntivi
Filtro IP/G20/Host personalizzato <sup>1</sup>|Sì|Sì
Download progressivo|Sì|Sì
Uso consigliato |Consigliato per la maggior parte dei casi di streaming.|Uso professionale.

<sup>1</sup> Utilizzato direttamente nell'endpoint di streaming solo quando la rete CDN non è abilitata nell'endpoint.<br/>

## <a name="streaming-endpoint-properties"></a>Proprietà degli endpoint di streaming

Questa sezione fornisce informazioni dettagliate su alcune delle proprietà dell'endpoint di streaming. Per esempi di come creare un nuovo endpoint di streaming e per le descrizioni di tutte le proprietà, vedere [Streaming Endpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create) (Endpoint di streaming).

- `accessControl`: utilizzato per configurare le seguenti impostazioni di sicurezza per questo endpoint di streaming: chiavi di autenticazione dell'intestazione della firma Akamai e indirizzi IP autorizzati a connettersi a questo endpoint. Questa proprietà può essere `cdnEnabled` impostata solo quando è impostata su false.

- `cdnEnabled`: indica se l'integrazione della rete CDN di Azure per questo endpoint di streaming è abilitata (disabilitata per impostazione predefinita). Se `cdnEnabled` è impostata su true, vengono disabilitate le configurazioni seguenti: `customHostNames` e `accessControl`.

    Non tutti i data center supportano l'integrazione della rete CDN di Azure. Per verificare se nel data center è disponibile l'integrazione della rete CDN di Azure, eseguire la procedura seguente:To check if your data center has the Azure CDN integration available, do the following steps:

  - Provare a impostare `cdnEnabled` su true.
  - Controllare il risultato `HTTP Error Code 412` restituito per un (PreconditionFailed) con un messaggio di "Endpoint di streaming CdnEnabled proprietà non può essere impostata su true come funzionalità CDN non è disponibile nell'area corrente".

    Se viene visualizzato questo errore, il data center non lo supporta. Provare con un altro data center.

- `cdnProfile`: `cdnEnabled` quando è impostato su true, è anche possibile passare `cdnProfile` valori. `cdnProfile` è il nome del profilo della rete CDN in cui verrà creato il punto di endpoint della rete CDN. È possibile fornire un cdnProfile esistente o usarne uno nuovo. Se il valore è NULL e `cdnEnabled` è impostata su true, viene usato il valore predefinito "AzureMediaStreamingPlatformCdnProfile". Se il `cdnProfile` fornito è già esistente, nel profilo viene creato un endpoint. Se il profilo non esiste, viene creato automaticamente un nuovo profilo.
- `cdnProvider`: quando la rete CDN `cdnProvider` è abilitata, è anche possibile passare valori. `cdnProvider` consente di controllare il provider usato. Sono attualmente supportati tre valori: "StandardVerizon", "PremiumVerizon" e "StandardAkamai". Se non viene `cdnEnabled` fornito alcun valore ed è true, viene utilizzato "StandardVerizon" (questo è il valore predefinito).
- `crossSiteAccessPolicies`: utilizzato per specificare i criteri di accesso tra siti per i vari client. Per altre informazioni, vedere [Cross-domain policy file specification](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) (Specifica dei file di criteri tra domini) e [Making a Service Available Across Domain Boundaries](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx)(Disponibilità di un servizio tra confini di dominio). Le impostazioni si applicano solo a Smooth Streaming.
- `customHostNames`: consente di configurare un endpoint di streaming per accettare il traffico diretto a un nome host personalizzato. Questa proprietà è valida per gli endpoint di `cdnEnabled`streaming Standard e Premium e può essere impostata quando: false.

    La proprietà del nome di dominio deve essere confermata da Servizi multimediali. Servizi multimediali verifica la proprietà del `CName` nome di dominio richiedendo un record contenente l'ID account di Servizi multimediali come componente da aggiungere al dominio in uso. Ad esempio, per usare "sports.contoso.com" come nome host personalizzato per l'endpoint di streaming, è necessario configurare un record per `<accountId>.contoso.com` che punti a uno dei nomi dell'host di verifica di Servizi multimediali. Il nome dell'host di verifica è composto da verifydns.\<mediaservices-dns-zone >.

    Di seguito sono riportate le zone DNS previste da usare nel record di verifica per aree di Azure diverse.
  
  - Nord America, Europa, Singapore, Hong Kong SAR, Giappone:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - Cina:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Ad esempio, `CName` un record che esegue il mapping di "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" a "verifydns.media.azure.net" dimostra che l'ID servizi multimediali 945a4c4e-28ea-45cd-8ccb-a519f6b700ad ha la proprietà del dominio contoso.com, consentendo in tal modo l'utilizzo di qualsiasi nome contoso.com come nome host personalizzato per un endpoint di streaming con tale account. Per individuare il valore dell'ID di Servizi multimediali, passare al [portale di Azure](https://portal.azure.com/) e selezionare l'account di Servizi multimediali. **L'ID account** viene visualizzato in alto a destra nella pagina.

    Se si verifica un tentativo di impostare un nome `CName` host personalizzato senza una verifica appropriata del record, la risposta DNS avrà esito negativo e quindi verrà memorizzata nella cache per un certo periodo di tempo. Una volta definito il record corretto, potrebbe essere necessario del tempo prima che la risposta memorizzata nella cache venga nuovamente convalidata. A seconda del provider DNS per il dominio personalizzato, la riconvalida del record richiede da pochi minuti a un'ora.

    Oltre a `CName` `<accountId>.<parent domain>` `verifydns.<mediaservices-dns-zone>`tale elemento, è necessario `CName` crearne un altro che `sports.contoso.com`esetrae il mapping del nome host `amstest-usea.streaming.media.azure.net`personalizzato (ad esempio, ) al nome host dell'endpoint di streaming di Servizi multimediali, ad esempio .

    > [!NOTE]
    > Gli endpoint di streaming che si trovano nello stesso data center non possono condividere lo stesso nome host personalizzato.

    Attualmente Servizi multimediali non supporta SSL con domini personalizzati.

- `maxCacheAge`- Esegue l'override dell'intestazione di controllo della cache HTTP max-age impostata dall'endpoint di streaming su frammenti multimediali e manifesti su richiesta.- overrides the default max-age HTTP cache control header set by the streaming endpoint on media fragments and on-demand manifests. Il valore è impostato in secondi.
- `resourceState` -

    - Arrestato: lo stato iniziale di un endpoint di streaming dopo la creazione
    - Avvio: sta passando allo stato di esecuzioneStarting: is transitioning to the running state
    - In esecuzione: è in grado di trasmettere contenuti ai clientRunning: is able to stream content to clients
    - Ridimensionamento: le unità di scala vengono aumentate o diminuite
    - Arresto: sta passando allo stato di arresto
    - Eliminazione: è in corso l'eliminazione

- `scaleUnits`: fornire una capacità di uscita dedicata che può essere acquistata in incrementi di 200 Mbps. Se è necessario passare al tipo **Premium**, regolare `scaleUnits`.

## <a name="why-use-multiple-streaming-endpoints"></a>Perché usare più endpoint di streaming?

Un singolo endpoint di streaming può trasmettere video live e on demand e la maggior parte dei clienti utilizza un solo endpoint di streaming. In questa sezione vengono forniti alcuni esempi del motivo per cui potrebbe essere necessario usare più endpoint di streaming.

* Ogni unità riservata consente 200 Mbps di larghezza di banda. Se sono necessari più di 2.000 Mbps (2 Gbps) di larghezza di banda, è possibile usare il secondo endpoint di streaming e il bilanciamento del carico per offrire larghezza di banda aggiuntiva.

    Tuttavia, la rete CDN è il modo migliore per ottenere la scalabilità orizzontale per i contenuti in streaming, ma se si distribuiscono così tanti contenuti che la rete CDN sta estraendo più di 2 Gbps, è possibile aggiungere ulteriori endpoint di streaming (origini). In questo caso è necessario distribuire URL di contenuto bilanciati tra i due endpoint di streaming. Questo approccio offre una migliore memorizzazione nella cache rispetto al tentativo di inviare richieste a ogni origine in modo casuale (ad esempio, tramite un gestore del traffico). 
    
    > [!TIP]
    > Di solito se la rete CDN sta tirando più di 2 Gbps allora qualcosa potrebbe essere configurato in modo errato (ad esempio, nessuna schermatura di origine).
    
* Bilanciamento del carico di provider CDN diversi. Ad esempio, è possibile configurare l'endpoint di streaming predefinito per l'utilizzo della rete CDN Verizon e crearne un secondo per l'utilizzo di Akamai. Aggiungere quindi un bilanciamento del carico tra i due per ottenere il bilanciamento di più cdn. 

    Tuttavia, i clienti spesso eseguino il bilanciamento del carico tra più provider CDN usando una singola origine.
* Streaming di contenuti misti: Live e Video su richiesta. 

    I modelli di accesso per i contenuti live e on-demand sono molto diversi. Il contenuto live tende a ottenere un sacco di domanda per lo stesso contenuto tutto in una volta. Il contenuto video on-demand (ad esempio il contenuto dell'archivio a coda lunga) ha un utilizzo ridotto sullo stesso contenuto. Così la memorizzazione nella cache funziona molto bene sul contenuto dal vivo, ma non così bene sul contenuto lunga coda.

    Si consideri uno scenario in cui i clienti guardano principalmente contenuti in tempo reale, ma solo occasionalmente guardano contenuti on-demand e vengono serviti dallo stesso endpoint di streaming. Il basso utilizzo del contenuto su richiesta occuperebbe spazio nella cache che sarebbe meglio salvato per il contenuto in tempo reale. In questo scenario, è consigliabile servire il contenuto live da un endpoint di streaming e il contenuto con coda lunga da un altro endpoint di streaming. Ciò migliorerà le prestazioni del contenuto dell'evento dal vivo.
    
## <a name="scaling-streaming-with-cdn"></a>Scalabilità dello streaming con CDNScaling streaming with CDN

Vedere gli articoli seguenti:

- [Panoramica della rete CDN](../../cdn/cdn-overview.md)
- [Scalabilità dello streaming con CDNScaling streaming with CDN](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>Poni domande e ricevi aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="see-also"></a>Vedere anche

[creazione dinamica dei pacchetti](dynamic-packaging-overview.md)

## <a name="next-steps"></a>Passaggi successivi

[Gestire gli endpoint di streaming](manage-streaming-endpoints-howto.md)
