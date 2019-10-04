---
title: Informazioni sui modelli a oggetti gemelli digitali di Azure e sul grafico di intelligence spaziale | Microsoft Docs
description: Gemelli digitali di Azure consente di modellare le relazioni tra persone, luoghi e dispositivi
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 67a4aceb157ee3fe1b1d1553efd587a0f2838d88
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950016"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Informazioni sui modelli a oggetti di Gemelli digitali e sul grafico di intelligenza spaziale

Gemelli digitali di Azure è un servizio IoT di Azure che permette rappresentazioni virtuali complete di ambienti fisici e dei dispositivi, delle persone e dei sensori associati. Migliora lo sviluppo organizzando concetti specifici di dominio in modelli utili. I modelli vengono quindi situati all'interno di un grafico di intelligenza spaziale. Questi concetti modellano fedelmente le relazioni e le interazioni tra persone, spazi e dispositivi.

I modelli a oggetti di Gemelli digitali descrivono concetti, categorie e proprietà specifici per un dominio. I modelli vengono predefiniti dagli utenti che vogliono adattare la soluzione alle proprie esigenze specifiche. Insieme, questi modelli a oggetti di Gemelli digitali costituiscono un'_ontologia_. L'ontologia di un edificio intelligente descrive aree, ambienti, piani, uffici, zone, sale conferenze e sale riunioni. L'ontologia di una rete energetica descrive le diverse centrali elettriche, le sottostazioni, le risorse di energia e i clienti. Con i modelli a oggetti di Gemelli digitali e le ontologie, è possibile personalizzare diversi scenari ed esigenze.

Predisponendo modelli a oggetti di Gemelli digitali e un’ontologia, è possibile popolare un _grafico spaziale_. I grafici spaziali sono rappresentazioni virtuali delle molte relazioni tra spazi, dispositivi e persone rilevanti per una soluzione IoT. Questo diagramma mostra un esempio di grafico spaziale che usa l'ontologia di un edificio intelligente.

[![Digital gemelli-creazione di grafici spaziali](media/concepts/digital-twins-spatial-graph-building.png)](media/concepts/digital-twins-spatial-graph-building.png#lightbox)

Il grafico spaziale riunisce spazi, dispositivi, sensori e utenti. Ogni elemento è collegato agli altri in modo da modellare il mondo reale. In questo esempio, la sede 43 presenta quattro piani, ciascuno con molte aree differenti. Gli utenti sono associati alla propria workstation e hanno accesso a parti del grafico. Un amministratore dispone dei diritti per apportare modifiche al grafico spaziale, mentre un visitatore ha solo i diritti per visualizzare determinati dati dell'edificio.

## <a name="digital-twins-object-models"></a>Modelli a oggetti di Gemelli digitali

I modelli a oggetti di Gemelli digitali supportano queste categorie principali di oggetti:

- Gli **spazi** sono luoghi fisici o virtuali, ad esempio `Tenant`, `Customer`, `Region` e `Venue`.
- I **dispositivi** sono parti virtuali o fisiche di apparecchiature, ad esempio `AwesomeCompany Device` e `Raspberry Pi 3`.
- I **sensori** sono oggetti che rilevano eventi, ad esempio `AwesomeCompany Temperature Sensor` e `AwesomeCompany Presence Sensor`.
- Gli **utenti** identificano gli occupanti e le rispettive caratteristiche.

Altre categorie di oggetti:

- Le **risorse** sono associate a uno spazio e in genere rappresentano risorse di Azure usate dagli oggetti nel grafico spaziale, ad esempio `IoTHub`.
- I **BLOB** sono associati agli oggetti (quali spazi, dispositivi, sensori e utenti). Sono utilizzati come file con tipo MIME e metadati, ad esempio, `maps`, `pictures` e `manuals`.
- I **tipi estesi** sono enumerazioni estendibili che ottimizzano le entità con caratteristiche specifiche, ad esempio `SpaceType` e `SpaceSubtype`.
- Le **ontologie** rappresentano un set di tipi estesi, ad esempio `Default`, `Building`, `BACnet` e `EnergyGrid`.
- **Chiavi e valori di proprietà** sono caratteristiche personalizzate di spazi, dispositivi, sensori e utenti. Possono essere usati oltre alle caratteristiche predefinite, ad esempio `DeltaProcessingRefreshTime` come chiave e `10` come valore.
- I **ruoli** sono set di autorizzazioni assegnate a utenti e dispositivi nel grafico spaziale, ad esempio `Space Administrator`, `User Administrator` e `Device Administrator`.
- Le **assegnazioni di ruolo** sono l'associazione tra un ruolo e un oggetto nel grafico spaziale. Ad esempio, a un utente o a un'entità servizio può essere concessa l'autorizzazione per gestire uno spazio del grafico spaziale.
- Gli **archivi chiavi di sicurezza** forniscono le chiavi di sicurezza per tutti i dispositivi nella gerarchia al di sotto di un oggetto dello spazio specifico per permettere al dispositivo di comunicare in tutta sicurezza con Gemelli digitali.
- Le **funzioni definite dall'utente** (UDF) permettono l'elaborazione all'interno del grafico spaziale di dati di telemetria dei sensori personalizzabili. Ad esempio, una funzione definita dall'utente può:
  - Impostare un valore del sensore.
  - Eseguire la logica personalizzata basata su letture di sensori e impostare l'output su uno spazio.
  - Collegare i metadati a uno spazio.
  - Inviare notifiche quando vengono soddisfatte le condizioni predefinite. Attualmente le funzioni definite dall'utente possono essere scritte in JavaScript.
- I **matcher** sono oggetti che determinano quali funzioni definite dall'utente vengono eseguite per un messaggio di telemetria specificato.
- Gli **endpoint** sono le posizioni in cui possono essere instradati i messaggi di telemetria e gli eventi di Gemelli digitali, ad esempio `Event Hub`, `Service Bus` e `Event Grid`.

## <a name="spatial-intelligence-graph"></a>Grafico di intelligenza spaziale

Il grafico spaziale è il grafico gerarchico di spazi, dispositivi e utenti definiti nel modello a oggetti di Gemelli digitali. Il grafico spaziale supporta ereditarietà, filtri, attraversamento, scalabilità ed estendibilità. È possibile interagire con il grafico spaziale e gestirlo con una raccolta di API REST.

L’utente che distribuisce un servizio di Gemelli digitali nella sottoscrizione diventa amministratore globale del nodo radice. Dispone pertanto dell’accesso completo all’intera struttura. Può effettuare il provisioning di spazi nel grafico tramite l'API Space. Può effettuare il provisioning dei servizi tramite l'API del dispositivo e dei sensori tramite l'API Sensor. Sono disponibili anche [strumenti open source](https://github.com/Azure-Samples/digital-twins-samples-csharp) per effettuare il provisioning in blocco del grafico.

**Ereditarietà del grafico**. L'ereditarietà si applica alle autorizzazioni e alle proprietà che derivano da un nodo padre a tutti i nodi al suo interno. Ad esempio, quando un ruolo viene assegnato a un utente in un determinato nodo, l'utente ha le autorizzazioni del ruolo per il nodo specificato e tutti i nodi al suo interno. Ogni chiave di proprietà e tipo esteso definiti per un determinato nodo vengono ereditati da tutti i nodi all'interno di tale nodo.

**Filtri per il grafico**. I filtri consentono di restringere i risultati della richiesta. È possibile filtrare per ID, nome, tipi, sottotipi, spazio padre e spazi associati. È anche possibile filtrare per tipi di dati dei sensori, chiavi e valori di proprietà, *attraversamento*, *livello minimo*, *livello massimo*e altri parametri di filtro OData.

**Attraversamento del grafico**. È possibile attraversare il grafico spaziale lunga la profondità e l’ampiezza. Per la profondità, il grafico può essere attraversato dall'alto in basso o dal basso in alto tramite i parametri *traverse*, *minLevel* e *maxLevel*. Per l'ampiezza, il grafico può essere attraversato per ottenere nodi di pari livello direttamente associati a uno spazio padre o a uno dei suoi discendenti. Quando si esegue una query su un oggetto, è possibile ottenere tutti gli oggetti correlati che presentano relazioni con l'oggetto usando il parametro *includes* delle API GET.

**Scalabilità dei grafici**. Gemelli digitali garantisce la scalabilità del grafico, per poter gestire i carichi di lavoro reali. È possibile usare Gemelli digitali per rappresentare grandi portfolio di proprietà immobiliari, infrastrutture, dispositivi, sensori, telemetria e altro ancora.

**Estendibilità del grafico**. L'estendibilità del grafico permette di personalizzare i modelli a oggetti di Gemelli digitali sottostanti con nuovi tipi e ontologie. I dati di Gemelli digitali possono essere arricchiti anche con proprietà e valori estendibili.

### <a name="spatial-intelligence-graph-management-apis"></a>API di gestione del grafico di intelligenza spaziale

Dopo aver distribuito Gemelli digitali dal [portale di Azure](https://portal.azure.com), l’URL [Swagger](https://swagger.io/tools/swagger-ui/) delle API di gestione viene generato automaticamente. Viene visualizzato nel portale di Azure, nella sezione **Panoramica**, con il formato seguente.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Attività | Sostituire con |
| --- | --- |
| NOME_ISTANZA_UTENTE | Nome dell'istanza di Gemelli digitali dell'utente |
| POSIZIONE_UTENTE | Area del server in cui è ospitata l'istanza |

 Il formato dell'URL completo viene visualizzato in questa immagine.

(![API di gestione del portale di Gemelli digitali](media/concepts/digital-twins-spatial-graph-management-api-url.png)] (media/Concepts/Digital-Twins-Spatial-Graph-Management-API-URL. png # Lightbox)

Per altre informazioni sull'uso dei grafici di intelligenza spaziale, visitare l'anteprima di prova delle API di gestione di Gemelli digitali di Azure.

> [!TIP]
> Viene fornita un'anteprima di prova di Swagger per mostrare il set di funzionalità delle API.
> L'anteprima è ospitata in [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Per altre informazioni, vedere [How to use Swagger](how-to-use-swagger.md) (Come usare Swagger).

Tutte le chiamate API devono essere autenticate tramite [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). Le API seguono le [convenzioni delle linee guida sulle API REST Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). La maggior parte delle API che restituiscono raccolte supporta le opzioni di query di sistema [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla connettività dei dispositivi e su come inviare messaggi di telemetria a Gemelli digitali, vedere [Connettività dei dispositivi e ingresso della telemetria in Gemelli digitali di Azure](concepts-device-ingress.md).

- Per informazioni sulle limitazioni per le API di gestione, vedere [Gestione e limiti delle API di Gemelli digitali di Azure](concepts-service-limits.md).
