---
title: Modelli personalizzati
titleSuffix: Azure Digital Twins
description: Informazioni sul modo in cui i dispositivi gemelli digitali di Azure usano i modelli definiti dall'utente per descrivere le entità nell'ambiente.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: a1bb07ee900622b138a556a33469641e4e8310af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392302"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Comprendere i modelli gemelli nei dispositivi gemelli digitali di Azure

Una caratteristica chiave dei dispositivi gemelli digitali di Azure è la possibilità di definire un vocabolario personalizzato e di creare un grafico a gemelli in termini autonomi dell'azienda. Questa funzionalità viene fornita tramite **modelli**definiti dall'utente. È possibile considerare i modelli come Sostantivi in una descrizione del mondo. 

Un modello è simile a una **classe** in un linguaggio di programmazione orientato a oggetti, definendo una forma dati per un particolare concetto nell'ambiente di lavoro reale. I modelli hanno nomi, ad esempio *room* o *sensore*, e contengono elementi quali proprietà, telemetria/eventi e comandi che descrivono il tipo di entità nell'ambiente. Successivamente, questi modelli vengono usati per creare i dispositivi [**gemelli digitali**](concepts-twins-graph.md) che rappresentano entità specifiche che soddisfano questa descrizione del tipo.

I modelli vengono scritti usando il **linguaggio DTDL (Digital Twin Definition Language)** basato su JSON-LD.  

## <a name="digital-twin-definition-language-dtdl-for-writing-models"></a>Digital Twin Definition Language (DTDL) per la scrittura di modelli

I modelli per i dispositivi gemelli digitali di Azure vengono definiti tramite il linguaggio DTDL (Digital Gemini Definition Language). DTDL è basato su JSON-LD ed è indipendente dal linguaggio di programmazione. DTDL non è esclusivo per i dispositivi gemelli digitali di Azure, ma viene usato anche per rappresentare i dati dei dispositivi in altri servizi Internet, ad esempio [plug and Play](../iot-pnp/overview-iot-plug-and-play.md). I dispositivi gemelli digitali di Azure usano DTDL *versione 2*.

> [!TIP] 
> Non tutti i servizi che usano DTDL implementano esattamente le stesse funzionalità di DTDL. Ad esempio, il Plug and Play Internet delle cose non usa le funzionalità di DTDL per i grafici, mentre i dispositivi gemelli digitali di Azure attualmente non implementano i comandi di DTDL. Per altre informazioni sulle funzionalità di DTDL specifiche per i dispositivi gemelli digitali di Azure, vedere la sezione più avanti in questo articolo sulle specifiche di implementazione di DTDL per i dispositivi [digitali gemelli di Azure](#azure-digital-twins-dtdl-implementation-specifics).

Per ulteriori informazioni su DTDL in generale, vedere la relativa documentazione specifica in GitHub: [Digital Gemini Definition Language (DTDL)-versione 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

## <a name="elements-of-a-model"></a>Elementi di un modello

All'interno di una definizione del modello, l'elemento di codice di primo livello è un' **interfaccia**. Questo incapsula l'intero modello e il resto del modello viene definito all'interno dell'interfaccia. 

Un'interfaccia del modello DTDL può contenere zero, uno o molti dei seguenti campi:
* **Proprietà** -le proprietà sono campi dati che rappresentano lo stato di un'entità (ad esempio le proprietà in molti linguaggi di programmazione orientati a oggetti). Diversamente dalla telemetria, che è un evento di dati con scadenza, le proprietà hanno l'archiviazione di backup e possono essere lette in qualsiasi momento.
* I campi di **telemetria: i** campi di telemetria rappresentano misurazioni o eventi e vengono spesso usati per descrivere le letture dei sensori del dispositivo. La telemetria non è archiviata in un dispositivo gemello digitale; è più simile a un flusso di eventi dati pronti per l'invio in un punto qualsiasi. 
* **Component** -Components consente di compilare l'interfaccia del modello come assembly di altre interfacce, se lo si desidera. Un esempio di componente è costituito da un'interfaccia *frontCamera* (e da un'altra *backcamera*di interfaccia componente) utilizzata per la definizione di un modello per un *telefono*. È necessario innanzitutto definire un'interfaccia per *frontCamera* come se fosse un modello proprio, quindi è possibile farvi riferimento durante la definizione del *telefono*.

    Usare un componente per descrivere una parte integrante della soluzione, ma non è necessaria un'identità separata e non è necessario crearla, eliminarla o riordinarla in modo indipendente. Se si vuole che le entità dispongano di esistenza indipendente nel grafo gemello, rappresentarle come gemelli digitali distinti di modelli diversi, connessi da *relazioni* (vedere il punto successivo).
    
    >[!TIP] 
    >I componenti possono inoltre essere utilizzati per l'organizzazione per raggruppare set di proprietà correlate all'interno di un'interfaccia del modello. In questa situazione è possibile considerare ogni componente come uno spazio dei nomi o una "cartella" all'interno dell'interfaccia.
* **Relazioni: le** relazioni consentono di rappresentare il modo in cui un dispositivo gemello digitale può essere associato ad altri dispositivi gemelli digitali. Le relazioni possono rappresentare significati semantici diversi, ad esempio *Contains* ("Floor Contains room"), *Cools* ("HVAC Cools room"), *isBilledTo* ("commediator is fatturated to User") e così via. Le relazioni consentono alla soluzione di fornire un grafico delle entità correlate.

> [!NOTE]
> La specifica per DTDL definisce anche i **comandi**, che sono metodi che possono essere eseguiti su un dispositivo gemello digitale, ad esempio un comando reset, o un comando per attivare o disattivare un ventilatore. Tuttavia, *i comandi non sono attualmente supportati nei dispositivi gemelli digitali di Azure.*

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Specifiche di implementazione DTDL di dispositivi digitali gemelli di Azure

Per garantire la compatibilità di un modello DTDL con i dispositivi gemelli digitali di Azure, è necessario che soddisfi questi requisiti.

* Tutti gli elementi DTDL di primo livello in un modello devono essere di tipo *Interface*. Questo perché le API del modello di dispositivi gemelli digitali di Azure possono ricevere oggetti JSON che rappresentano un'interfaccia o una matrice di interfacce. Di conseguenza, non sono consentiti altri tipi di elementi DTDL al livello principale.
* DTDL per i dispositivi gemelli digitali di Azure non deve definire alcun *comando*.
* I dispositivi gemelli digitali di Azure consentono solo un singolo livello di annidamento dei componenti. Ciò significa che un'interfaccia usata come componente non può avere alcun componente. 
* Le interfacce non possono essere definite inline all'interno di altre interfacce DTDL. devono essere definite come entità di primo livello separate con i rispettivi ID. Quindi, quando un'altra interfaccia vuole includere tale interfaccia come componente o tramite ereditarietà, può fare riferimento al relativo ID.

## <a name="example-model-code"></a>Esempio di codice del modello

I modelli di tipo gemello possono essere scritti in qualsiasi editor di testo. Il linguaggio DTDL segue la sintassi JSON, quindi è necessario archiviare i modelli con estensione *JSON*. L'uso dell'estensione JSON consentirà a molti editor di testo di programmazione di fornire il controllo della sintassi di base ed evidenziare i documenti DTDL. Per [Visual Studio Code](https://code.visualstudio.com/)è disponibile anche un' [estensione DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) .

Di seguito è riportato un esempio di un modello tipico, scritto come un'interfaccia DTDL. Il modello descrive i pianeti, ognuno con un nome, una massa e una temperatura. Il pianeta può avere Moons come satelliti e può contenere crateri.

```json
[
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "Temperature",
        "schema": "double"
      },
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

I campi del modello sono:

| Campo | Descrizione |
| --- | --- |
| `@id` | Identificatore per il modello. Deve essere nel formato `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | Identifica il tipo di informazioni da descrivere. Per un'interfaccia, il tipo è *Interface*. |
| `@context` | Imposta il [contesto](https://niem.github.io/json/reference/json-ld/context/) per il documento JSON. I modelli devono usare `dtmi:dtdl:context;2` . |
| `displayName` | opzionale Consente di assegnare al modello un nome descrittivo, se lo si desidera. |
| `contents` | Tutti i dati dell'interfaccia rimanenti vengono inseriti qui come una matrice di definizioni di attributo. Ogni attributo deve fornire un `@type` (*Proprietà*, *telemetria*, *comando*, *relazione*o *componente*) per identificare l'ordinamento delle informazioni di interfaccia descritte, quindi un set di proprietà che definiscono l'attributo effettivo (ad esempio, `name` e `schema` per definire una *Proprietà*). |

> [!NOTE]
> Si noti che l'interfaccia componente (*cratere* in questo esempio) è definita nella stessa matrice dell'interfaccia che la utilizza (*Planet*). I componenti devono essere definiti in questo modo nelle chiamate API per poter trovare l'interfaccia.

### <a name="possible-schemas"></a>Schemi possibili

Come per DTDL, lo schema per gli attributi di *Proprietà* e *telemetria* può essere di tipi primitivi standard `integer` , ovvero,, `double` `string` e `Boolean` altri tipi quali `DateTime` e `Duration` . 

Oltre ai tipi primitivi, i campi di *Proprietà* e *telemetria* possono avere questi tipi complessi:
* `Object`
* `Map`
* `Enum`

I campi di *telemetria* supportano anche `Array` .

### <a name="model-inheritance"></a>Ereditarietà del modello

In alcuni casi può essere necessario specializzare ulteriormente un modello. Ad esempio, potrebbe essere utile disporre di una *stanza*del modello generica e di varianti specializzate *conferenceroom* e *Gym*. Per esprimere la specializzazione, DTDL supporta l'ereditarietà: le interfacce possono ereditare da una o più interfacce. 

Nell'esempio seguente viene nuovamente immaginato il modello *Planet* dell'esempio precedente DTDL come sottotipo di un modello *CelestialBody* più grande. Il modello "Parent" viene definito per primo e quindi il modello "Child" si basa su di esso tramite il campo `extends` .

```json
[
  {
    "@id": "dtmi:com:contoso:CelestialBody;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Celestial body",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "extends": "dtmi:com:contoso:CelestialBody;1",
    "contents": [
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

In questo esempio, *CelestialBody* contribuisce con un nome, una massa e una temperatura al *pianeta*. La `extends` sezione è un nome di interfaccia o una matrice di nomi di interfaccia (che consente all'interfaccia di estensione di ereditare da più modelli padre, se lo si desidera).

Una volta applicata l'ereditarietà, l'interfaccia di estensione espone tutte le proprietà dell'intera catena di ereditarietà.

L'interfaccia di estensione non può modificare alcuna definizione delle interfacce padre. può solo aggiungerli. Non è inoltre in grado di ridefinire una funzionalità già definita in una delle interfacce padre (anche se le funzionalità sono definite come uguali). Se, ad esempio, un'interfaccia padre definisce `double` una *massa*di proprietà, l'interfaccia di estensione non può contenere una dichiarazione di *massa*, anche se è anche un oggetto `double` .

## <a name="validating-models"></a>Convalida di modelli

È disponibile un esempio per la convalida dei documenti del modello per verificare che DTDL sia valido. Si basa sulla libreria del parser DTDL ed è indipendente dal linguaggio. Trovarlo qui: [DTDL validator Sample](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

In alternativa, per altre informazioni sulla libreria del parser, incluso un esempio di come usarlo direttamente, vedere [procedura: analizzare e convalidare i modelli](how-to-use-parser.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere come gestire i modelli con le API DigitalTwinsModels:
* [Procedura: gestire modelli personalizzati](how-to-manage-model.md)

In alternativa, informazioni su come vengono creati i dispositivi digitali gemelli in base ai modelli:
* [Concetti: i dispositivi gemelli digitali e i grafici gemelli](concepts-twins-graph.md)

