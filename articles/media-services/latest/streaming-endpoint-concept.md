---
title: Endpoint di streaming (Origin)
titleSuffix: Azure Media Services
description: Informazioni sugli endpoint di streaming (Origin), un servizio di creazione dinamica dei pacchetti e di streaming che fornisce contenuti direttamente a un'app lettore client o a una rete per la distribuzione di contenuti (CDN).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: c8901dccb67e91c608e999f823cf7d2e757da08b
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186005"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Endpoint di streaming (Origin) in servizi multimediali di Azure

In Servizi multimediali di Microsoft Azure, un [endpoint di streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) rappresenta un servizio di creazione e creazione di pacchetti dinamici (just-in-Time) in grado di fornire contenuti live e on demand direttamente a un'app per giocatori client usando uno dei protocolli di streaming media comuni (HLS o Dash). Inoltre, l' **endpoint di streaming** fornisce la crittografia dinamica (just-in-Time) a DRM leader del settore.

Quando si crea un account di Servizi multimediali viene creato un endpoint di streaming **predefinito** nello stato Arrestato. Non è possibile eliminare l'endpoint di streaming **predefinito** . È possibile creare altri endpoint di streaming con l'account (vedere [quote e limitazioni](limits-quotas-constraints.md)).

> [!NOTE]
> Per avviare lo streaming di video, è necessario avviare l **'endpoint di streaming** da cui si vuole trasmettere il video.
>
> Viene addebitato solo quando l'endpoint di streaming è nello stato in esecuzione.

## <a name="naming-convention"></a>Convenzione di denominazione

Il formato del nome host dell'URL di streaming è: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`, dove `servicename` = il nome dell'endpoint di streaming o il nome dell'evento Live.

Quando si usa l'endpoint di streaming predefinito, `servicename` viene omesso, quindi l'URL è: `{accountname}-{regionname}.streaming.azure.net`.

### <a name="limitations"></a>Limitazioni

* Il nome dell'endpoint di streaming ha un valore massimo di 24 caratteri.
* Il nome deve seguire questo modello [Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) : `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Types

Sono disponibili due tipi di **endpoint di streaming** : **standard** (anteprima) e **Premium**. Il tipo è definito in base al numero di unità di scala (`scaleUnits`) allocate per l'endpoint di streaming.

La tabella seguente descrive i tipi:

|digitare|Unità di scala|DESCRIZIONE|
|--------|--------|--------|  
|**Standard**|0|L'endpoint di streaming predefinito è un tipo **standard** , che può essere modificato nel tipo Premium regolando `scaleUnits`.|
|**Premium**|>0|**Premium** Gli endpoint di streaming sono adatti per carichi di lavoro avanzati e offrono una capacità di larghezza di banda dedicata e scalabile. Si passa a un tipo **Premium** regolando `scaleUnits` (unità di streaming). Il valore `scaleUnits` rappresenta la capacità di uscita dedicata acquistabile in incrementi di 200 Mbps. Quando si usa il tipo **Premium** , ogni unità abilitata fornisce ulteriore capacità di larghezza di banda per l'app. |

> [!NOTE]
> Per i clienti che desiderano distribuire contenuti a destinatari Internet di grandi dimensioni, è consigliabile abilitare la rete CDN nell'endpoint di streaming.

Per informazioni sul contratto di contratto, vedere [prezzi e contratto di contratto](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Confronto tra le tipologie di streaming

Funzionalità|Standard|Premium
---|---|---
Velocità effettiva |Fino a 600 Mbps e possono fornire una velocità effettiva molto più elevata quando si usa una rete CDN.|200 Mbps per unità di streaming (SU). Può fornire una velocità effettiva molto più elevata quando viene usata una rete CDN.
RETE CDN|Rete CDN di Azure, rete CDN di terze parti o nessuna rete CDN.|Rete CDN di Azure, rete CDN di terze parti o nessuna rete CDN.
Fatturazione con ripartizione proporzionale| Giornaliera|Giornaliera
Crittografia dinamica|Sì|Sì
Creazione dinamica dei pacchetti|Sì|Sì
Ridimensionare|Scalabilità automatica fino alla velocità effettiva di destinazione.|SUs aggiuntivo
Filtro IP/G20/host personalizzato <sup>1</sup>|Sì|Sì
Download progressivo|Sì|Sì
Uso consigliato |Consigliato per la maggior parte dei casi di streaming.|Uso professionale.

<sup>1</sup> usato direttamente sull'endpoint di streaming quando la rete CDN non è abilitata nell'endpoint.<br/>

## <a name="properties"></a>Proprietà

Questa sezione fornisce informazioni dettagliate su alcune delle proprietà dell'endpoint di streaming. Per esempi di come creare un nuovo endpoint di streaming e per le descrizioni di tutte le proprietà, vedere [Streaming Endpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create) (Endpoint di streaming).

- `accessControl`: usato per configurare le seguenti impostazioni di sicurezza per questo endpoint di streaming: chiavi di autenticazione dell'intestazione della firma Akamai e indirizzi IP autorizzati a connettersi a questo endpoint. Questa proprietà può essere impostata solo quando `cdnEnabled` è impostato su false.

- `cdnEnabled`: indica se l'integrazione della rete CDN di Azure per questo endpoint di streaming è abilitata (disabilitata per impostazione predefinita). Se `cdnEnabled` è impostata su true, vengono disabilitate le configurazioni seguenti: `customHostNames` e `accessControl`.

    Non tutti i data center supportano l'integrazione della rete CDN di Azure. Per verificare se l'integrazione della rete CDN di Azure data center è disponibile, seguire questa procedura:

  - Provare a impostare il `cdnEnabled` su true.
  - Controllare il risultato restituito per un `HTTP Error Code 412` (PreconditionFailed) con un messaggio "Impossibile impostare la proprietà CdnEnabled dell'endpoint di streaming su true perché la funzionalità della rete CDN non è disponibile nell'area corrente".

    Se si riceve questo errore, il data center non lo supporta. Provare con un'altra data center.

- `cdnProfile`: quando `cdnEnabled` è impostato su true, è anche possibile passare i valori di `cdnProfile`. `cdnProfile` è il nome del profilo della rete CDN in cui verrà creato il punto di endpoint della rete CDN. È possibile fornire un cdnProfile esistente o usarne uno nuovo. Se il valore è NULL e `cdnEnabled` è impostata su true, viene usato il valore predefinito "AzureMediaStreamingPlatformCdnProfile". Se il `cdnProfile` fornito è già esistente, nel profilo viene creato un endpoint. Se il profilo non esiste, viene creato automaticamente un nuovo profilo.
- `cdnProvider`: quando è abilitata la rete CDN, è anche possibile passare i valori `cdnProvider`. `cdnProvider` consente di controllare il provider usato. Sono attualmente supportati tre valori: "StandardVerizon", "PremiumVerizon" e "StandardAkamai". Se non viene fornito alcun valore e `cdnEnabled` è true, viene usato "StandardVerizon", ovvero il valore predefinito.
- `crossSiteAccessPolicies`: usato per specificare i criteri di accesso tra siti per diversi client. Per altre informazioni, vedere [Cross-domain policy file specification](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) (Specifica dei file di criteri tra domini) e [Making a Service Available Across Domain Boundaries](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx)(Disponibilità di un servizio tra confini di dominio). Le impostazioni si applicano solo ai Smooth Streaming.
- `customHostNames`: usato per configurare un endpoint di streaming in modo da accettare il traffico indirizzato a un nome host personalizzato. Questa proprietà è valida per gli endpoint di streaming standard e Premium e può essere impostata quando `cdnEnabled`: false.

    La proprietà del nome di dominio deve essere confermata da servizi multimediali. Servizi multimediali verifica la proprietà del nome di dominio richiedendo un record `CName` contenente l'ID dell'account di servizi multimediali come componente da aggiungere al dominio in uso. Ad esempio, per usare "sports.contoso.com" come nome host personalizzato per l'endpoint di streaming, è necessario configurare un record per `<accountId>.contoso.com` che punti a uno dei nomi dell'host di verifica di Servizi multimediali. Il nome dell'host di verifica è composto da verifydns.\<mediaservices-dns-zone >.

    Di seguito sono riportate le zone DNS previste da usare nel record di verifica per diverse aree di Azure.
  
  - America del Nord, Europa, Singapore, Hong Kong SAR, Giappone:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - Cina:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Ad esempio, un record `CName` che esegue il mapping di "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" a "verifydns.media.azure.net" dimostra che l'ID di servizi multimediali 945a4c4e-28ea-45CD-8ccb-a519f6b700ad ha la proprietà del dominio contoso.com, consentendo così l'uso di un nome in contoso.com come nome host personalizzato per un endpoint di streaming con tale account. Per individuare il valore dell'ID di Servizi multimediali, passare al [portale di Azure](https://portal.azure.com/) e selezionare l'account di Servizi multimediali. L' **ID account** viene visualizzato in alto a destra nella pagina.

    Se viene effettuato un tentativo di impostare un nome host personalizzato senza una verifica appropriata del record `CName`, la risposta DNS avrà esito negativo e quindi verrà memorizzata nella cache per un certo periodo di tempo. Una volta definito il record corretto, potrebbe essere necessario del tempo prima che la risposta memorizzata nella cache venga nuovamente convalidata. A seconda del provider DNS per il dominio personalizzato, per riconvalidare il record sono necessari da pochi minuti a un'ora.

    Oltre all'`CName` che esegue il mapping di `<accountId>.<parent domain>` a `verifydns.<mediaservices-dns-zone>`, è necessario creare un altro `CName` che esegue il mapping del nome host personalizzato (ad esempio, `sports.contoso.com`) al nome host dell'endpoint di streaming di servizi multimediali (ad esempio, `amstest-usea.streaming.media.azure.net`).

    > [!NOTE]
    > Gli endpoint di streaming che si trovano nella stessa data center non possono condividere lo stesso nome host personalizzato.

    Attualmente, servizi multimediali non supporta SSL con domini personalizzati.

- `maxCacheAge`: esegue l'override dell'intestazione di controllo della cache HTTP Max-Age predefinita impostata dall'endpoint di streaming nei frammenti del supporto e nei manifesti su richiesta. Il valore è impostato in secondi.
- `resourceState` -

    - Arrestato: stato iniziale di un endpoint di streaming dopo la creazione
    - Avvio: transizione allo stato in esecuzione
    - Running: è in grado di trasmettere il contenuto ai client
    - Ridimensionamento: aumento o riduzione delle unità di scala
    - Arresto in corso: transizione allo stato interrotto
    - Eliminazione di: verrà eliminato

- `scaleUnits`: fornire capacità in uscita dedicata che può essere acquistata con incrementi di 200 Mbps. Se è necessario passare al tipo **Premium**, regolare `scaleUnits`.

## <a name="working-with-cdn"></a>Uso della rete CDN

Nella maggior parte dei casi, la rete per la distribuzione di contenuti è abilitata. Tuttavia, se si prevede una concorrenza massima inferiore a 500 visualizzatori, è consigliabile disabilitare la rete CDN poiché la rete CDN si adatta meglio alla concorrenza.

### <a name="considerations"></a>Considerazioni

* L'endpoint di streaming `hostname` e l'URL di streaming rimangono invariati, indipendentemente dal fatto che la rete CDN sia abilitata.
* Se è necessario testare il contenuto con o senza la rete CDN, creare un altro endpoint di streaming non abilitato per la rete CDN.

### <a name="detailed-explanation-of-how-caching-works"></a>Spiegazione dettagliata del funzionamento della memorizzazione nella cache

Non esiste un valore della larghezza di banda specifico quando si aggiunge la rete CDN perché la quantità di larghezza di banda necessaria per un endpoint di streaming abilitato per la rete CDN varia. Molto dipende dal tipo di contenuto, da quanto è popolare, dalla velocità in bit e dai protocolli. La rete CDN memorizza nella cache solo gli elementi richiesti. Ciò significa che il contenuto popolare verrà servito direttamente dalla rete CDN, purché il frammento video venga memorizzato nella cache. Il contenuto live viene in genere memorizzato nella cache perché molte persone guardano la stessa cosa. Il contenuto su richiesta può essere un po' più complicato perché potrebbe essere presente contenuto popolare e altri non. Se si hanno milioni di asset video in cui nessuno di essi è popolare (solo uno o due visualizzatori alla settimana) ma si hanno migliaia di persone che guardano tutti i video, la rete CDN diventa molto meno efficace. In presenza di questi mancati riscontri nella cache è consigliabile aumentare il carico sull'endpoint di streaming.

È anche necessario valutare il funzionamento del flusso adattivo. Ogni singolo frammento video viene memorizzato nella cache come entità in sé. Si supponga, ad esempio, la prima volta che un determinato video viene guardato. Se il Visualizzatore Ignora solo pochi secondi qui e qui, solo i frammenti video associati a ciò che l'utente ha guardato vengono memorizzati nella cache nella rete CDN. Con il flusso adattivo, si hanno in genere da 5 a 7 bitrate del video diversi. Se una persona sta osservando una velocità in bit e un altro utente sta osservando una velocità in bit diversa, ognuno di essi viene memorizzato nella cache separatamente nella rete CDN. Anche se due persone stanno osservando la stessa velocità in bit, potrebbero trasmettere flussi su protocolli diversi. Ogni protocollo (HLS, MPEG-DASH, Smooth Streaming) viene memorizzato nella cache separatamente. In conclusione, ogni bitrate e ogni protocollo vengono memorizzati nella cache separatamente; inoltre, vengono memorizzati nella cache solo i frammenti video che sono stati richiesti.

### <a name="enable-azure-cdn-integration"></a>Abilitare l'integrazione della rete CDN di Azure

Quando viene eseguito il provisioning di un endpoint di streaming con la rete CDN abilitata, è previsto un tempo di attesa definito in servizi multimediali prima di eseguire l'aggiornamento DNS per eseguire il mapping dell'endpoint di streaming all'endpoint rete CDN.

Se in seguito si desidera disabilitare o abilitare la rete CDN, l'endpoint di streaming deve essere nello stato **interrotto**. L'abilitazione dell'integrazione della rete CDN di Azure e l'attivazione delle modifiche in tutti i POP della rete CDN potrebbero richiedere fino a due ore. Tuttavia, è possibile avviare l'endpoint di streaming e il flusso senza interruzioni dall'endpoint di streaming e, una volta completata l'integrazione, il flusso viene recapitato dalla rete CDN. Durante il periodo di provisioning, l'endpoint di streaming sarà nello stato **iniziale** ed è possibile che si verifichi un calo delle prestazioni.

Quando viene creato l'endpoint di streaming standard, questo viene configurato per impostazione predefinita con Verizon standard. È possibile configurare Premium Verizon o provider Akamai standard usando le API REST.

L'integrazione della rete CDN è abilitata in tutti i data center di Azure eccetto per le aree Cina e Governo federale.

> [!IMPORTANT]
> L'integrazione di Servizi multimediali di Azure con la rete CDN di Azure è implementata nella **rete CDN di Azure da Verizon** per gli endpoint di streaming standard. Gli endpoint di streaming Premium possono essere configurati usando tutti **i provider e i livelli di prezzo della rete CDN di Azure**. Per altre informazioni sulle funzionalità della rete CDN di Azure, vedere la [Panoramica della rete per la distribuzione di contenuti (rete CDN) di Azure](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-was-made"></a>Determinare se è stata apportata la modifica DNS

È possibile determinare se è stata apportata una modifica DNS a un endpoint di streaming (il traffico viene indirizzato alla rete CDN di Azure) usando https://www.digwebinterface.com. Se nei risultati sono presenti nomi di dominio azureedge.net, il traffico viene ora puntato alla rete CDN.

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

L'esempio [in questo repository](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) illustra come avviare l'endpoint di streaming predefinito con .NET.
