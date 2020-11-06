---
title: Integrazione di modelli standard del settore
titleSuffix: Azure Digital Twins
description: Informazioni su come integrare i modelli standard del settore in DTDL per i dispositivi gemelli digitali di Azure, usando ontologie DTDL speciali o convertendo le ontologie esistenti
author: baanders
ms.author: baanders
ms.date: 11/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1866e9b233a1379474c061779ada09fd6d119107
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338395"
---
# <a name="integrate-industry-standard-models-with-dtdl-for-azure-digital-twins"></a>Integra i modelli standard del settore con DTDL per i dispositivi gemelli digitali di Azure

L'uso di modelli basati su standard del settore o la rappresentazione standard di ontologia, ad esempio RDF o OWL, fornisce un punto di partenza completo durante la progettazione dei modelli di dispositivi gemelli digitali di Azure. L'uso di modelli di settore contribuisce anche alla standardizzazione e alla condivisione delle informazioni.

Per l'uso con i dispositivi gemelli digitali di Azure, un modello deve essere rappresentato nel [**linguaggio DTDL (Digital Gemini Definition Language)**](concepts-models.md)basato su JSON-LD. Questo articolo descrive quindi come rappresentare i modelli standard del settore in DTDL, integrando i concetti di settore esistenti con la semantica di DTDL, in modo che i dispositivi gemelli digitali di Azure possano usarli. Il modello DTDL funge quindi da origine della verità per il modello nei dispositivi gemelli digitali di Azure.

Esistono tre possibili percorsi per l'integrazione dei modelli standard del settore con DTDL:
* **Adozione** : è possibile avviare la soluzione con una ontologia DTDL open source basata su standard di settore ampiamente adottati. 
* **Convert** : se si dispone già di modelli esistenti, è necessario convertirli in DTDL.
* **Autore** : è sempre possibile sviluppare modelli DTDL personalizzati da zero, come descritto in [*procedura: gestire i modelli personalizzati*](how-to-manage-model.md).

## <a name="adopt-an-open-source-dtdl-ontology"></a>Adottare un'ontologia DTDL Open Source

Spesso è più semplice iniziare con una ontologia DTDL Open Source anziché iniziare da una pagina vuota. 

Ad esempio, le soluzioni per gli edifici intelligenti possono sfruttare l' [**ontologia RealEstateCore basata su DTDL**](https://github.com/Azure/opendigitaltwins-building)open source, che fornisce un terreno comune per la modellazione di edifici intelligenti sfruttando gli standard di settore per prevenire la riinvenzione. 

Queste ontologie DTDL Open Source forniscono anche le procedure consigliate per utilizzare ed estendere correttamente i modelli. 

## <a name="convert-existing-models-to-dtdl"></a>Convertire i modelli esistenti in DTDL

La maggior parte dei modelli di settore, detti anche **ontologie** , si basa su standard web semantici come [Owl](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)e [FTR](https://www.w3.org/2001/sw/wiki/RDFS). 

Per usare un modello con i dispositivi gemelli digitali di Azure, deve essere in formato DTDL. Questa sezione descrive le linee guida generali per la progettazione sotto forma di **modello di conversione** per la conversione di modelli basati su RDF in DTDL in modo che possano essere usati con i dispositivi gemelli digitali di Azure. 

Contiene anche [ **codice del convertitore di esempio** per un convertitore RDF](#sample-converter-application), che è stato convalidato per lo schema [Brick](https://brickschema.org/ontology/) e può essere esteso per altri schemi nel settore della compilazione.

### <a name="conversion-pattern"></a>Modello di conversione

Sono disponibili diverse librerie di terze parti che possono essere usate per la conversione di modelli basati su RDF in DTDL. Alcune di queste librerie consentono di caricare il file del modello in un grafico. È possibile scorrere in ciclo il grafo cercando specifici costrutti di FTR e OWL e convertirli in DTDL.   

La tabella seguente è un esempio di come è possibile eseguire il mapping dei costrutti di FTR e OWL a DTDL. 

| Concetto di FTR/gufo | Costrutto FTR/OWL | Concetto di DTDL | Costrutto DTDL |
| --- | --- | --- | --- |
| Classi | `owl:Class`<br>Suffisso IRI<br>``rdfs:label``<br>``rdfs:comment`` | Interfaccia | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Sottoclassi | `owl:Class`<br>Suffisso IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interfaccia | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Proprietà DataType | `owl:DatatypeProperty`<br>`rdfs:label` o `INode`<br>`rdfs:label`<br>`rdfs:range` | Proprietà dell'interfaccia | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Proprietà degli oggetti | `owl:ObjectProperty`<br>`rdfs:label` o `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relazione | `type:Relationship`<br>`name`<br>`target` (o omesso se no `rdfs:range` )<br>`comment`<br>`displayName`<br>

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

### <a name="sample-converter-application"></a>Applicazione convertitore di esempio 

È disponibile un'applicazione di esempio che converte un file di modello basato su RDF in [DTDL (versione 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) per l'uso da parte del servizio Azure Digital Twins. È stata convalidata per lo schema [Brick](https://brickschema.org/ontology/) ed è possibile estenderla per altri schemi nel settore della compilazione (ad esempio, la [compilazione della topologia di ontologia (bot)](https://w3c-lbd-cg.github.io/bot/), della rete dei [sensori semantici](https://www.w3.org/TR/vocab-ssn/)o di [buildingSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

L'esempio è un'applicazione della riga di comando di .NET Core denominata **RdfToDtdlConverter**.

È possibile ottenere l'esempio seguente: [**RdfToDtdlConverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Per scaricare il codice nel computer, fare clic sul pulsante *Scarica zip* sotto il titolo nella pagina di destinazione dell'esempio. Verrà scaricato un file *zip* con il nome *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* , che sarà quindi possibile decomprimere ed esplorare.

È possibile usare questo esempio per visualizzare i modelli di conversione nel contesto e per avere come blocco predefinito per le applicazioni che eseguono conversioni di modelli in base alle proprie esigenze specifiche.

## <a name="validate-and-upload-dtdl-models"></a>Convalidare e caricare modelli DTDL

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

Dopo la conversione e la convalida di un modello, è possibile **caricarlo nell'istanza di Azure Digital gemelli**. Per altre informazioni su questo processo, vedere la sezione [*upload Models*](how-to-manage-model.md#upload-models) di *How-to: Manage Custom Models*.

## <a name="next-steps"></a>Passaggi successivi 

Scopri di più sulla progettazione e la gestione di modelli di dispositivi gemelli digitali:
 
* [*Concetti: Modelli personalizzati*](concepts-models.md)
* [*Procedura: Gestire modelli personalizzati*](how-to-manage-model.md)
* [*Procedura: analizzare e convalidare i modelli*](how-to-parse-models.md)
