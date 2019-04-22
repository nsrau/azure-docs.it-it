---
title: Endpoint di streaming in Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo descrive le caratteristiche degli endpoint di streaming e come vengono usati da Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 2e715e5280794172451a333624a954340a1a60fe
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58881019"
---
# <a name="streaming-endpoints"></a>Endpoint di streaming

In Servizi multimediali di Azure un'entità [endpoint di streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) rappresenta un servizio di streaming in grado di distribuire contenuti direttamente a un'applicazione di lettore client o a una rete CDN (rete per la distribuzione di contenuti) per la successiva distribuzione. Il flusso in uscita da un servizio **endpoint di streaming** può essere costituito da un flusso live o da una risorsa video on demand associata all'account di Servizi multimediali. Quando si crea un account di Servizi multimediali viene creato un endpoint di streaming **predefinito** nello stato Arrestato. L'endpoint di streaming **predefinito** non può essere eliminato. Nell'account è possibile creare altri endpoint di streaming. 

> [!NOTE]
> Per avviare lo streaming di video, è necessario avviare l **'endpoint di streaming** da cui si vuole trasmettere il video. 
>  
> Il costo verrà addebitato solo quando StreamingEndpoint è in stato di esecuzione.

## <a name="naming-convention"></a>Convenzione di denominazione

Per l'endpoint predefinito: `{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

Per qualsiasi altro endpoint: `{EndpointName}-{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

## <a name="types"></a>Tipi  

Sono disponibili due tipi di **endpoint di streaming**: Standard e **Premium**. Il tipo è definito in base al numero di unità di scala (`scaleUnits`) allocate per l'endpoint di streaming. 

La tabella seguente descrive i tipi:  

|Type|Unità di scala|DESCRIZIONE|
|--------|--------|--------|  
|**Endpoint di streaming Standard** (scelta consigliata)|0|L'impostazione predefinita l'Endpoint di Streaming è un' **Standard** tipo, ma può essere modificato nel tipo Premium.<br/> Il tipo Standard è l'opzione consigliata per quasi tutti gli scenari di streaming e numero di destinatari. Il tipo **Standard** consente la scalabilità automatica della larghezza di banda in uscita. La velocità effettiva da questo tipo di Endpoint di Streaming è fino a 600 Mbps. I frammenti video memorizzati nella cache nella rete CDN, non usare la larghezza di banda di Endpoint di Streaming.<br/>Per i clienti con requisiti più complessi, Servizi multimediali mette a disposizione endpoint di streaming di tipo **Premium**, scalabili orizzontalmente qualora il numero dei destinatari Internet sia più ampio. Se si prevede che un vasto pubblico e utenti simultanei è elevato, contattaci in amsstreaming al\@microsoft.com, per indicazioni sulla necessità di spostare il **Premium** tipo. |
|**Endpoint di streaming Premium**|>0|Gli endpoint di streaming **Premium** sono ideali per i carichi di lavoro avanzati, in quanto offrono una capacità di larghezza di banda dedicata e scalabile. Per passare al tipo **Premium** è necessario regolare il valore `scaleUnits`. Il valore `scaleUnits` rappresenta la capacità di uscita dedicata acquistabile in incrementi di 200 Mbps. Quando si usa il tipo **Premium**, ogni unità abilitata fornisce all'applicazione una capacità di larghezza di banda aggiuntiva. |
 
## <a name="comparing-streaming-types"></a>Confronto tra le tipologie di streaming

### <a name="features"></a>Funzionalità

Funzionalità|Standard|Premium
---|---|---
Gratis per i primi 15 giorni| Sì |No 
Velocità effettiva |Fino a 600 Mbps quando non si usa la rete CDN di Azure. Scalabilità con la rete CDN.|200 Mbps per unità di streaming (SU). Scalabilità con la rete CDN.
Contratto di servizio | 99,9|99,9 (200 Mbps per SU).
RETE CDN|Rete CDN di Azure, rete CDN di terze parti o nessuna rete CDN.|Rete CDN di Azure, rete CDN di terze parti o nessuna rete CDN.
Fatturazione con ripartizione proporzionale| Giornaliera|Giornaliera
Crittografia dinamica|Sì|Sì
creazione dinamica dei pacchetti|Sì|Sì
Scalabilità|Scalabilità automatica fino alla velocità effettiva di destinazione.|Unità di streaming aggiuntive
Host applicazione di filtri/G20/personalizzato IP <sup>1</sup>|Sì|Sì
Download progressivo|Sì|Sì
Uso consigliato |Consigliato per la maggior parte dei casi di streaming.|Uso professionale.<br/>Per esigenze superiori alle funzionalità offerte dalla tipologia Standard. Se si prevede un numero di destinatari simultanei superiore a 50.000 visualizzatori, contattare Microsoft (amsstreaming@microsoft.com).

<sup>1</sup> usato direttamente nell'Endpoint di Streaming solo quando la rete CDN non è abilitata nell'endpoint.

## <a name="properties"></a>Properties 

Questa sezione fornisce informazioni dettagliate su alcune delle proprietà dell'Endpoint di Streaming. Per esempi di come creare un nuovo endpoint di streaming e per le descrizioni di tutte le proprietà, vedere [Streaming Endpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create) (Endpoint di streaming). 

- `accessControl` -Viene usato per configurare le impostazioni di sicurezza seguenti per questo endpoint di streaming: Chiavi di autenticazione intestazione firma Akamai e indirizzi IP che sono autorizzati a connettersi a questo endpoint.<br />Questa proprietà può essere impostata solo quando `cdnEnabled` è impostata su false.
- `cdnEnabled` -Indica se l'integrazione della rete CDN di Azure per questo endpoint di streaming è abilitata (disabilitato per impostazione predefinita). Se `cdnEnabled` è impostata su true, vengono disabilitate le configurazioni seguenti: `customHostNames` e `accessControl`.
  
    Non tutti i data center supportano l'integrazione della rete CDN di Azure. Per controllare se nel data center con l'integrazione della rete CDN di Azure disponibile, eseguire le operazioni seguenti:
 
  - Tenta di impostare il `cdnEnabled` su true.
  - Controllare il risultato restituito per un `HTTP Error Code 412` (PreconditionFailed) con un messaggio di "Proprietà CdnEnabled dell'endpoint di Streaming non può essere impostato su true, come funzionalità della rete CDN non è disponibile nell'area corrente." 

    Se viene restituito questo errore, il data center non supporta l'integrazione ed è necessario provare con un altro data center.
- `cdnProfile` -Quando `cdnEnabled` è impostata su true, è anche possibile passare `cdnProfile` valori. `cdnProfile` è il nome del profilo della rete CDN in cui verrà creato il punto di endpoint della rete CDN. È possibile fornire un cdnProfile esistente o usarne uno nuovo. Se il valore è NULL e `cdnEnabled` è impostata su true, viene usato il valore predefinito "AzureMediaStreamingPlatformCdnProfile". Se il `cdnProfile` fornito è già esistente, nel profilo viene creato un endpoint. Se il profilo non esiste, viene creato automaticamente un nuovo profilo.
- `cdnProvider` -Quando della rete CDN è abilitata, è anche possibile passare `cdnProvider` valori. `cdnProvider` consente di controllare il provider usato. Attualmente sono supportati tre valori: "StandardVerizon", "PremiumVerizon" and "StandardAkamai". Se viene specificato alcun valore e `cdnEnabled` è true, viene usato "StandardVerizon" (ovvero il valore predefinito).
- `crossSiteAccessPolicies` -Viene usato per specificare i criteri di accesso intersito per vari client. Per altre informazioni, vedere [Cross-domain policy file specification](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) (Specifica dei file di criteri tra domini) e [Making a Service Available Across Domain Boundaries](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx)(Disponibilità di un servizio tra confini di dominio).<br/>Le impostazioni si applicano solo in formato Smooth Streaming.
- `customHostNames` -Viene usato per configurare un Endpoint di Streaming affinché accetti il traffico indirizzato a un nome host personalizzato.  Questa proprietà è valida per gli endpoint di Streaming Premium e Standard e può essere impostata quando `cdnEnabled`: false.
    
    La proprietà del nome di dominio deve essere confermata da servizi multimediali. Servizi multimediali consente di verificare le proprietà del nome di dominio richiedendo un `CName` record che contiene l'ID dell'account servizi multimediali come un componente da aggiungere al dominio in uso. Ad esempio, per usare "sports.contoso.com" come nome host personalizzato per l'endpoint di streaming, è necessario configurare un record per `<accountId>.contoso.com` che punti a uno dei nomi dell'host di verifica di Servizi multimediali. Il nome dell'host di verifica è composto da verifydns.\<mediaservices-dns-zone >. 

    Di seguito sono le zone DNS previste da usare nel record di verifica per diverse aree di Azure.
  
  - America del Nord, Europa, Singapore, Hong Kong, Giappone:
      
    - `media.azure.net`
    - `verifydns.media.azure.net`
      
  - Cina:
        
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    Ad esempio, un `CName` record che viene eseguito il mapping "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" a "verifydns.media.azure.net" dimostra che l'ID di servizi multimediali 945a4c4e-28ea-45cd-8ccb-a519f6b700ad ha la proprietà del dominio contoso.com, pertanto uso di qualsiasi nome in contoso.com da utilizzare come nome host personalizzato per un endpoint di streaming con tale account. Per individuare il valore dell'ID di Servizi multimediali, passare al [portale di Azure](https://portal.azure.com/) e selezionare l'account di Servizi multimediali. Il **Account ID** viene visualizzato nella parte superiore destra della pagina.
        
    se si tenta di impostare un nome host personalizzato senza l'adeguata verifica del record `CName`, la risposta DNS avrà esito negativo e verrà memorizzata nella cache per un certo tempo. Una volta definito il record corretto, potrebbe essere necessario del tempo prima che la risposta memorizzata nella cache venga nuovamente convalidata. A seconda del provider DNS del dominio personalizzato, la nuova convalida potrebbe richiedere da alcuni minuti a un'ora.
        
    Oltre al `CName` che esegue il mapping `<accountId>.<parent domain>` a `verifydns.<mediaservices-dns-zone>`, è necessario creare un altro `CName` che mappa il nome host personalizzato (ad esempio, `sports.contoso.com`) al nome host di servizi Endpoint di Streaming multimediali (ad esempio, `amstest-usea.streaming.media.azure.net`).
 
    > [!NOTE]
    > Che si trova nello stesso data center, gli endpoint di streaming non possono condividere lo stesso nome host personalizzato.

    Attualmente, servizi multimediali non supporta SSL con domini personalizzati. 
    
- `maxCacheAge` -Esegue la cache HTTP max-age predefinita controllo intestazione impostata dall'endpoint di streaming nei frammenti multimediali e i manifesti su richiesta. Il valore è impostato in secondi.
- `resourceState` -

    - Arrestato - lo stato iniziale di un Endpoint di Streaming dopo la creazione
    - A partire - viene effettuata la transizione allo stato di esecuzione
    - È in grado di trasmettere il contenuto ai client in esecuzione:
    - Scalabilità: l'unità sono in corso di scalabilità aumenta o diminuisce
    - -Arresto viene effettuata la transizione allo stato arrestato
    - L'eliminazione - eliminazione
    
- `scaleUnits` -Fornire capacità di uscita dedicata acquistabile in incrementi di 200 Mbps. Se è necessario passare al tipo **Premium**, regolare `scaleUnits`.

## <a name="working-with-cdn"></a>Uso della rete CDN

Nella maggior parte dei casi, la rete per la distribuzione di contenuti è abilitata. Se, tuttavia, si prevede che la concorrenza massima sia inferiore a 500 visualizzatori, è consigliabile disabilitare la rete CDN perché la modalità di concorrenza consente un miglior ridimensionamento della rete.

### <a name="considerations"></a>Considerazioni

* L'endpoint di streaming `hostname` e l'URL di streaming rimangono invariati indipendentemente dal fatto che venga abilitata o meno la rete CDN.
* Se è necessario avere la possibilità di testare il contenuto con o senza la rete CDN, è possibile creare un altro Endpoint di Streaming che non è abilitata.

### <a name="detailed-explanation-of-how-caching-works"></a>Spiegazione dettagliata del funzionamento della memorizzazione nella cache

Quando si aggiunge una rete CDN non è presente alcun valore specifico relativo alla larghezza di banda, perché la larghezza di banda necessaria per un endpoint di streaming abilitato per la rete CDN è variabile e dipende molto dal tipo di contenuto, dalla richiesta, dal bitrate e dai protocolli. La rete CDN memorizza nella cache soltanto ciò che viene richiesto. Ciò significa che il contenuto più diffuso viene gestito direttamente dalla rete CDN, a condizione che il frammento video sia memorizzato nella cache. Il contenuto live viene in genere memorizzato nella cache perché molte persone guardano la stessa cosa. Il contenuto su richiesta può risultare più complesso perché può essere in parte molto richiesto e in parte no. Se si hanno milioni di risorse video con richieste saltuarie, ad esempio con solo 1 o 2 visualizzatori a settimana, a fronte di migliaia di persone che guardano video tutti diversi, la rete CDN perde di efficacia. In presenza di questi mancati riscontri nella cache è consigliabile aumentare il carico sull'endpoint di streaming.
 
È anche necessario valutare il funzionamento del flusso adattivo. Ogni singolo frammento video viene memorizzato nella cache come entità in sé. Ad esempio, nel caso di un video che viene guardato per la prima volta da un utente che ne osserva solo alcuni secondi qua e là, nella rete CDN vengono memorizzati solo i frammenti video associati a ciò che è stato guardato. Con il flusso adattivo, si hanno in genere da 5 a 7 bitrate del video diversi. I video guardati con bitrate diversi da utenti diversi vengono memorizzati separatamente nella cache della rete CDN. Anche se due utenti usano lo stesso bitrate, potrebbero eseguire lo streaming usando protocolli diversi. Ogni protocollo (HLS, MPEG-DASH, Smooth Streaming) viene memorizzato nella cache separatamente. In conclusione, ogni bitrate e ogni protocollo vengono memorizzati nella cache separatamente; inoltre, vengono memorizzati nella cache solo i frammenti video che sono stati richiesti.

### <a name="enable-azure-cdn-integration"></a>Abilitare l'integrazione della rete CDN di Azure

Dopo il provisioning di un Endpoint di Streaming con della rete CDN abilitata vi è un tempo di attesa definita in servizi multimediali prima dell'operazione di aggiornamento del DNS per eseguire il mapping dell'Endpoint di Streaming all'endpoint rete CDN.

Se in seguito si desidera disabilitare o abilitare la rete CDN, l'endpoint di streaming deve essere nello stato **interrotto**. L'abilitazione dell'integrazione della rete CDN di Azure e l'attivazione delle modifiche in tutti i POP della rete CDN potrebbero richiedere fino a due ore. Tuttavia, è possibile avviare l'endpoint di streaming, trasmettere senza interruzioni dall'endpoint di streaming e, dopo aver completato l'integrazione, il flusso viene distribuito dalla rete CDN. Durante il periodo di provisioning l'endpoint di streaming ha stato **avvio in corso** ed è possibile che le prestazioni risultino ridotte.

Quando viene creato l'endpoint di streaming Standard, si è configurato per impostazione predefinita con Verizon Standard. È possibile configurare i provider Premium da Verizon o Akamai Standard usando le API REST. 

L'integrazione della rete CDN è abilitata in tutti i data center di Azure eccetto per le aree Cina e Governo federale.

> [!IMPORTANT]
> L'integrazione di Servizi multimediali di Azure con la rete CDN di Azure è implementata nella **rete CDN di Azure da Verizon** per gli endpoint di streaming standard. Gli endpoint di streaming Premium possono essere configurati usando tutti **i provider e i livelli di prezzo della rete CDN di Azure**. Per altre informazioni sulle funzionalità della rete CDN di Azure, vedere la [Panoramica della rete per la distribuzione di contenuti (rete CDN) di Azure](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-has-been-made"></a>Determinare se sono state apportate modifiche DNS

È possibile determinare se sono state apportate modifiche DNS in un Endpoint di Streaming (il traffico viene indirizzato alla rete CDN di Azure) usando https://www.digwebinterface.com. Se il risultato include i nomi di dominio azureedge.net nei risultati, il traffico a questo punto che puntano alla rete CDN.

## <a name="next-steps"></a>Passaggi successivi

L'esempio [in questo repository](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) illustra come avviare l'endpoint di streaming predefinito con .NET.

