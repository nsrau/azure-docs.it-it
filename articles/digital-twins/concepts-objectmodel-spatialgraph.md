---
title: Informazioni sui modelli a oggetti di Gemelli digitali e sul grafico di intelligenza spaziale | Microsoft Docs
description: Uso di Gemelli digitali di Azure per modellare le relazioni tra persone, luoghi e dispositivi
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 1c2068af510cb3733ce99a6ae7b40487a8c1a015
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323805"
---
# <a name="understanding-digital-twins-object-models-and-spatial-intelligence-graph"></a>Informazioni sui modelli a oggetti di Gemelli digitali e sul grafico di intelligenza spaziale

Gemelli digitali di Azure è un servizio IoT di Azure che permette rappresentazioni virtuali complete di ambienti fisici e dei dispositivi, delle persone e dei sensori associati. Questo servizio migliora lo sviluppo organizzando concetti specifici per un dominio in modelli utili, che si trovano all'interno di un grafico di intelligenza spaziale. Questi concetti modellano fedelmente le relazioni e le interazioni tra persone, spazi e dispositivi.

I _modelli a oggetti di Gemelli digitali_ descrivono concetti, categorie e proprietà specifici per un dominio. I modelli vengono predefiniti dagli utenti che vogliono adattare la soluzione alle proprie esigenze specifiche. Insieme, questi modelli a oggetti di Gemelli digitali costituiscono un'_ontologia_. L'ontologia di un edificio intelligente descrive aree, ambienti, piani, uffici, zone, sale conferenze e sale riunioni. L'ontologia di una rete energetica descrive le diverse centrali elettriche, le sottostazioni, le risorse di energia e i clienti. Questi modelli a oggetti e ontologie di Gemelli digitali permettono la personalizzazione di Gemelli digitali di Azure in base a esigenze e scenari diversificati.

Predisponendo _modelli a oggetti di Gemelli digitali_ e _ontologie_, è possibile popolare un _grafico spaziale_. I grafici spaziali sono rappresentazioni virtuali delle molte relazioni tra spazi, dispositivi e persone rilevanti per una soluzione IoT. Il diagramma seguente mostra un esempio di grafico spaziale che usa l'ontologia di un edificio intelligente.

![Creazione di un grafico spaziale di Gemelli digitali][1]

<a id="model" />

Il grafico spaziale riunisce spazi, dispositivi, sensori e utenti. Ognuno è collegato in modo da modellare il mondo reale: l'ambiente 43 ha quattro piani, ognuno con diverse aree. Gli utenti sono associati alla propria workstation e hanno accesso a parti del grafico.  Ad esempio, un amministratore avrà i diritti per apportare modifiche al grafico spaziale, mentre un visitatore avrà solo i diritti per visualizzare determinati dati dell'edificio.

## <a name="digital-twins-object-models"></a>Modelli a oggetti di Gemelli digitali

I modelli a oggetti di Gemelli digitali supportano queste categorie principali di oggetti:

- Gli **spazi** sono luoghi fisici o virtuali, ad esempio `Tenant`, `Customer`, `Region` o `Venue`.
- I **dispositivi** sono parti virtuali o fisiche di apparecchiature, ad esempio `AwesomeCompany Device` o `Raspberry Pi 3`.
- I **sensori** sono oggetti che rilevano eventi, ad esempio `AwesomeCompany Temperature Sensor` o `AwesomeCompany Presence Sensor`.
- Gli **utenti** identificano gli occupanti e le rispettive caratteristiche.

Altre categorie di oggetti:

- Le **risorse** sono associate a uno spazio e in genere rappresentano risorse di Azure usate dagli oggetti nel grafico spaziale, ad esempio `IoTHub`.
- I **BLOB** sono associati a oggetti, come spazi, dispositivi, sensori e utenti, e usati come file con tipo MIME e metadati, ad esempio `maps`, `pictures` o `manuals`.
- I **tipi estesi** sono enumerazioni estendibili che ottimizzano le entità con caratteristiche specifiche, ad esempio `SpaceType` o `SpaceSubtype`.
- Le **ontologie** rappresentano un set di tipi estesi, ad esempio `Default`, `Building`, `BACnet` o `EnergyGrid`.
- **Chiavi e valori di proprietà** sono caratteristiche personalizzate di spazi, dispositivi, sensori e utenti. Possono essere usati oltre alle caratteristiche predefinite, ad esempio `DeltaProcessingRefreshTime` come chiave e `10` come valore.
- I **ruoli** sono set di autorizzazioni assegnate a utenti e dispositivi nel grafico spaziale, ad esempio `Space Administrator`, `User Administrator` o `Device Administrator`.
- Le **assegnazioni di ruolo** sono l'associazione tra un ruolo e un oggetto nel grafico spaziale, ad esempio la concessione a un utente o a un'entità servizio dell'autorizzazione per gestire uno spazio nel grafico spaziale.
- Gli **archivi chiavi di sicurezza** forniscono le chiavi di sicurezza per tutti i dispositivi nella gerarchia al di sotto di un oggetto dello spazio specifico per permettere al dispositivo di comunicare in tutta sicurezza con il servizio Gemelli digitali.
- Le **funzioni definite dall'utente** o **UDF** permettono l'elaborazione all'interno del grafico spaziale di dati di telemetria dei sensori personalizzabili. Ad esempio, una funzione definita dall'utente può impostare un valore di sensore, eseguire logica personalizzata in base alle letture del sensore e impostare l'output su uno spazio, associare metadati a uno spazio e inviare notifiche quando vengono soddisfatte condizioni predefinite. Attualmente le funzioni definite dall'utente possono essere scritte in JavaScript.
- I **matcher** sono oggetti che determinano quali funzioni definite dall'utente verranno eseguite per un messaggio di telemetria specificato.
- Gli **endpoint** sono le posizioni in cui possono essere instradati i messaggi di telemetria e gli eventi di Gemelli digitali, ad esempio `Event Hub`, `Service Bus` o `Event Grid`.

<a id="graph" />

## <a name="spatial-intelligence-graph"></a>Grafico di intelligenza spaziale

Il **grafico spaziale** è il grafico gerarchico di spazi, dispositivi e utenti definiti nel **modello a oggetti di Gemelli digitali**. Il grafico spaziale supporta _ereditarietà_, _filtri_, _attraversamento_, _scalabilità_ ed _estendibilità_. Gli utenti possono interagire con il grafico spaziale e gestirlo con una raccolta di API REST (vedere di seguito).

L'utente che distribuisce un servizio Gemelli digitali nella propria sottoscrizione diventa l'amministratore globale del nodo radice, concedendo automaticamente l'accesso completo all'intera struttura. Questo utente può quindi effettuare il provisioning di spazi nel grafico tramite l'API `Space`. È possibile effettuare il provisioning dei dispositivi tramite l'API `Device`, dei sensori tramite l'API `Sensor` e così via. Sono disponibili anche [strumenti open source](https://github.com/Azure-Samples/digital-twins-samples-csharp) per effettuare il provisioning in blocco del grafico.

L'_ereditarietà_ del grafico si applica alle autorizzazioni e alle proprietà che derivano da un nodo padre a tutti i nodi al suo interno. Ad esempio, quando un ruolo viene assegnato a un utente in un determinato nodo, l'utente avrà le autorizzazioni del ruolo per il nodo specificato e tutti i nodi al suo interno. Inoltre, ogni chiave di proprietà e tipo esteso definiti per un determinato nodo verranno ereditati da tutti i nodi all'interno di tale nodo.

L'_applicazione di filtri_ al grafico permette agli utenti di limitare i risultati della richiesta in base a ID, nome, tipi, sottotipi, spazio padre, spazi associati, tipi di dati del sensore, chiavi e valori di proprietà, attraversamento, livello minimo, livello massimo e altri parametri di filtro OData.

L'_attraversamento_ del grafico permette agli utenti di spostarsi nel grafico spaziale in profondità e ampiezza. Per la profondità, il grafico può essere attraversato dall'alto in basso o dal basso in alto tramite i parametri di spostamento `traverse`, `minLevel` e `maxLevel`. Per l'ampiezza, il grafico può essere esplorato per ottenere nodi di pari livello direttamente associati a uno spazio padre o a uno dei suoi discendenti. Quando si esegue una query su un oggetto, è possibile ottenere tutti gli oggetti correlati che presentano relazioni con l'oggetto usando il parametro `includes` delle API GET.

Gemelli digitali di Azure garantisce la _scalabilità_ del grafico, per poter gestire i carichi di lavoro reali. È possibile usare Gemelli digitali per rappresentare grandi portfolio di proprietà immobiliari, infrastrutture, dispositivi, sensori, telemetria e altro ancora.

L'_estendibilità_ del grafico permette agli utenti di personalizzare i modelli a oggetti di Gemelli digitali sottostanti con nuovi tipi e ontologie. I dati di Gemelli digitali possono essere arricchiti anche con proprietà e valori estendibili.

### <a name="spatial-intelligence-graph-management-apis"></a>API di gestione del grafico di intelligenza spaziale

Dopo aver distribuito Gemelli digitali di Azure dal [portale di Azure](https://portal.azure.com), l'URL [Swagger](https://swagger.io/tools/swagger-ui/) delle API di gestione viene generato automaticamente e viene visualizzato nella sezione **Panoramica** con il formato seguente:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Nome attributo personalizzato | Sostituire con |
| --- | --- |
| `yourInstanceName` | Nome dell'istanza di Gemelli digitali di Azure |
| `yourLocation` | Area del server in cui è ospitata l'istanza |

 L'immagine di seguito mostra il formato dell'URL completo in uso:

![API di gestione del portale di Gemelli digitali][2]

Per altre informazioni sull'uso di grafici di intelligenza spaziale, visitare l'anteprima di prova delle API di gestione di Gemelli digitali di Azure.

> [!TIP]
> Viene fornita un'anteprima di prova di Swagger per mostrare il set di funzionalità delle API.
> L'anteprima è ospitata in [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Per altre informazioni, vedere [How to use Swagger](how-to-use-swagger.md) (Come usare Swagger).

Tutte le chiamate API devono essere autenticate tramite [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). Le API seguono le [convenzioni delle linee guida sulle API REST Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). La maggior parte delle API che restituiscono raccolte supporta le opzioni di query di sistema [OData](http://www.odata.org/getting-started/basic-tutorial/#queryData).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla connettività dei dispositivi e su come inviare messaggi di telemetria al servizio Gemelli digitali di Azure, vedere [Azure Digital Twins device connectivity and telemetry ingress](concepts-device-ingress.md) (Connettività dei dispositivi e ingresso della telemetria in Gemelli digitali di Azure).

Per informazioni sulle limitazioni per le API di gestione, vedere [Gestione e limiti delle API di Gemelli digitali di Azure](concepts-service-limits.md).

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
