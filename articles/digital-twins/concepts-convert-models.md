---
title: Conversione di modelli standard del settore
titleSuffix: Azure Digital Twins
description: Informazioni sul modello per la conversione dei modelli standard del settore (RDF/OWL) in DTDL
author: baanders
ms.author: baanders
ms.date: 9/28/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 76d1fd91053216103ef6ace0e56979c57eca569f
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002636"
---
# <a name="convert-industry-standard-models-to-dtdl-for-azure-digital-twins"></a>Convertire i modelli standard del settore in DTDL per i dispositivi gemelli digitali di Azure

I modelli nei dispositivi gemelli digitali di Azure sono rappresentati nel [**DTDL (Digital Gemini Definition Language)**](concepts-models.md)basato su JSON-LD. Se si hanno modelli esistenti all'esterno dei dispositivi gemelli digitali di Azure basati su uno standard del settore, ad esempio RDF o OWL, sarà necessario **convertirli in DTDL** per usarli con i dispositivi gemelli digitali di Azure. La versione di DTDL diventerà quindi l'origine della verità per il modello nei dispositivi gemelli digitali di Azure.

Questo articolo descrive un modello per la conversione di un settore basato su RDF o di modelli personalizzati in DTDL. Sono inclusi:
* **Indicazioni a livello di strategia** che è possibile applicare a una varietà di standard e tipi di modelli
* [**Codice di esempio** per un convertitore specifico di RDF](#sample-converter-application)

## <a name="industry-models"></a>Modelli di settore  

L'uso di modelli di settore offre un punto di partenza completo quando si progetta il modello di Azure Digital Twins. L'uso di modelli di settore contribuisce anche alla standardizzazione e alla condivisione delle informazioni. 

Alcuni modelli di settore comuni includono:  

| Settore verticale | Modello |
| --- | --- | 
| Gestione compilazione/struttura | [RealEstateCore](https://www.realestatecore.io/)<br>[Schema BRICK](https://brickschema.org/ontology/1.1/)<br>[Creazione di una ontologia di topologia (BOT)](https://w3c-lbd-cg.github.io/bot/)<br>[Rete sensori semantici](https://www.w3.org/TR/vocab-ssn/)<br>[Classi di buildingSmart Industry Foundation (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/) |
| Città intelligenti | [ETSI NGSI-LD](https://www.etsi.org/deliver/etsi_gr/CIM/001_099/008/01.01.01_60/gr_CIM008v010101p.pdf)<br>[Guida di riferimento alle applicazioni intelligenti (SAREF)](https://saref.etsi.org/) |
| Griglia di energia | [CIM](https://cimug.ucaiug.org/) / [IEC 61968](https://en.wikipedia.org/wiki/IEC_61968) | 

A seconda delle esigenze, è anche possibile usare DTDL per personalizzare o estendere i modelli di settore o sviluppare un modello personalizzato da zero. 

## <a name="create-and-edit-models"></a>Creazione e modifica di modelli

Il primo passaggio della modellazione consiste nel creare i modelli. È possibile creare e completare la modifica dei modelli standard del settore prima di convertirli in DTDL. in alternativa, è possibile convertirli in DTDL in anticipo e continuare a modificarli come documenti DTDL.

### <a name="with-industry-standards"></a>Con gli standard del settore

La maggior parte dei modelli di settore, detti anche **ontologie**, si basa su standard web semantici come [Owl](https://www.w3.org/OWL/[), [RDF](https://www.w3.org/2001/sw/wiki/RDF)e [FTR](https://www.w3.org/2001/sw/wiki/RDFS). 

In alcuni casi, potrebbe essere necessario creare o modificare un modello utilizzando gli strumenti del modello basati su OWL. Per progettare un modello basato su OWL, è possibile utilizzare un numero qualsiasi di strumenti di creazione di modelli, tra cui quelli riportati di seguito.
* [Webprotetto](https://protege.stanford.edu/products.php#web-protege) e [protetto desktop](https://protege.stanford.edu/products.php#desktop-protege) sono esempi diffusi. È possibile importare modelli di settore in più formati, modificare o estendere un modello ed esportare il modello. 
* [WebVOWL](http://www.visualdataweb.de/webvowl/) è un altro strumento comune per la visualizzazione dei modelli. 

Se si crea un modello usando uno standard del settore che non è DTDL, sarà quindi necessario convertirlo in DTDL e caricarlo in dispositivi gemelli digitali di Azure. 

#### <a name="common-model-file-formats"></a>Formati di file di modello comuni 

RDF, OWL e FTR sono i blocchi predefiniti di base del web semantico. 

**RDF** fornisce una struttura concettuale per la descrizione di elementi, sotto forma di **tripli**. Triple è costituito da tre parti: **Subject**, **predicate**e **Object**. Gli oggetti possono essere costituiti da URI. 

Di seguito sono riportati alcuni esempi di triple RDF:

```
<LogicalDevice> <hasCapabiity> <Temperature>
<Chiller> <locatedIn> <Level1>
<Asset> <isPartOf> <Asset>  
```

Questi esempi sono tutti RDF validi, ma l'ultima istruzione non è semanticamente valida. Questa situazione è la posizione in cui la specifica OWL entra nell'immagine. **Owl** definisce ciò che è possibile scrivere con RDF per avere una ontologia valida.

Di seguito è riportato un esempio di utilizzo di OWL: 

```
<Asset> <isPartOf> <Building>
<TemperatureSensor> <isType> <Sensor>
```

**FTR** fornisce una semantica lessicale aggiuntiva che consente di definire e descrivere le classi. Ad esempio, una classe di questo tipo è `rdfs:subClassOf` :

```
<Equipment> <subClassOf> <Asset>
```

I modelli possono essere salvati, importati ed esportati in molti formati di file, tra cui:  
* RDF/XML 
* Tartaruga (TTL) 
* GUFO/XML 

### <a name="with-dtdl"></a>Con DTDL

I dispositivi gemelli digitali di Azure usano il **linguaggio DTDL (Digital Twin Definition Language)** basato su JSON-LD per la modellazione. Qualsiasi modello che verrà usato con i dispositivi gemelli digitali di Azure dovrà essere originariamente scritto o convertito in DTDL.

Quando si utilizzano i modelli in DTDL, è possibile utilizzare l' [**estensione DTDL**](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)   disponibile per [Visual Studio Code](https://code.visualstudio.com/), che fornisce la convalida della sintassi e altre funzionalità per semplificare la scrittura di modelli DTDL.

Per altre informazioni sui modelli di dispositivi gemelli digitali di Azure e sui relativi componenti, vedere [*concetti: modelli personalizzati*](concepts-models.md) e [*procedura: gestire i modelli personalizzati*](how-to-manage-model.md).

## <a name="convert-models-to-dtdl"></a>Convertire i modelli in DTDL

Per usare un modello con i dispositivi gemelli digitali di Azure, deve essere in formato DTDL. Questa sezione illustra come convertire i modelli basati su RDF in DTDL in modo che possano essere usati con i dispositivi gemelli digitali di Azure.

Sono disponibili diverse librerie di terze parti che possono essere usate per la conversione di modelli basati su RDF in DTDL. Alcune di queste librerie consentono di caricare il file del modello in un grafico. È possibile scorrere in ciclo il grafo cercando specifici costrutti di FTR e OWL e convertirli in DTDL.   

La tabella seguente è un esempio di come è possibile eseguire il mapping dei costrutti di FTR e OWL a DTDL. 

| Concetto di FTR/gufo | Costrutto FTR/OWL | Concetto di DTDL | Costrutto DTDL |
| --- | --- | --- | --- |
| Classi | `owl:Class`<br>Suffisso IRI<br>``rdfs:label``<br>``rdfs:comment`` | Interfaccia | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Sottoclassi | `owl:Class`<br>Suffisso IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interfaccia | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Proprietà DataType | `owl:DatatypeProperty`<br>`rdfs:label` o `INode`<br>`rdfs:label`<br>`rdfs:range` | Proprietà dell'interfaccia | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Proprietà degli oggetti | `owl:ObjectProperty`<br>`rdfs:label` o `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relationship | `type:Relationship`<br>`name`<br>`target` (o omesso se no `rdfs:range` )<br>`comment`<br>`displayName`<br>

Il frammento di codice C# seguente illustra come caricare un file di modello RDF in un grafico e convertirlo in DTDL usando la libreria [**dotNetRDF**](https://www.dotnetrdf.org/) . 

```csharp
using VDS.RDF.Ontology; 
using VDS.RDF.Parsing; 
using Microsoft.Azure.DigitalTwins.Parser; 

//...

Console.WriteLine("Reading file..."); 

FileLoader.Load(_ontologyGraph, rdfFile.FullName); 

// Start looping through for each owl:Class 
foreach (OntologyClass owlClass in _ontologyGraph.OwlClasses) 
{ 

    // Generate a DTMI for the owl:Class 
    string Id = GenerateDTMI(owlClass); 

    if (!String.IsNullOrEmpty(Id)) 
    { 

        Console.WriteLine($"{owlClass.ToString()} -> {Id}"); 

        // Create Interface
        DtdlInterface dtdlInterface = new DtdlInterface 
        { 
            Id = Id, 
            Type = "Interface", 
            DisplayName = GetInterfaceDisplayName(owlClass), 
            Comment = GetInterfaceComment(owlClass), 
            Contents = new List<DtdlContents>() 
        }; 

        // Use DTDL 'extends' for super classes 
        IEnumerable<OntologyClass> foundSuperClasses = owlClass.DirectSuperClasses; 

        //... 
     }

     // Add interface to the list of interfaces 
     _interfaceList.Add(dtdlInterface); 
} 

// Serialize to JSON 
var json = JsonConvert.SerializeObject(_interfaceList); 

//...
``` 

## <a name="validate-and-upload-dtdl-models"></a>Convalidare e caricare modelli DTDL

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

Dopo la conversione e la convalida di un modello, è possibile **caricarlo nell'istanza di Azure Digital gemelli**. Per altre informazioni su questo processo, vedere la sezione [*upload Models*](how-to-manage-model.md#upload-models) di *How-to: Manage Custom Models*.

## <a name="sample-converter-application"></a>Applicazione convertitore di esempio 

È disponibile un'applicazione di esempio che converte un file di modello basato su RDF in [DTDL (versione 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) per l'uso da parte del servizio Azure Digital Twins. L'esempio è un'applicazione della riga di comando di .NET Core denominata **RdfToDtdlConverter**.

È possibile ottenere l'esempio seguente: [**RdfToDtdlConverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Per scaricare il codice nel computer, fare clic sul pulsante *Scarica zip* sotto il titolo nella pagina di destinazione dell'esempio. Verrà scaricato un file *zip* con il nome *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*, che sarà quindi possibile decomprimere ed esplorare.

È possibile usare questo esempio per visualizzare i modelli di conversione nel contesto e per avere come blocco predefinito per le applicazioni che eseguono conversioni di modelli in base alle proprie esigenze specifiche.

## <a name="next-steps"></a>Passaggi successivi 

Scopri di più sulla progettazione e la gestione di modelli di dispositivi gemelli digitali:
 
* [*Concetti: Modelli personalizzati*](concepts-models.md)
* [*Procedura: Gestire modelli personalizzati*](how-to-manage-model.md)
* [*Procedura: analizzare e convalidare i modelli*](how-to-parse-models.md)