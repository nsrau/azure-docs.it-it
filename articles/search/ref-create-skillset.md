---
title: Creare un set di competenze (API REST - api-version=2017-11-11-Preview) - Ricerca di Azure | Microsoft Docs
description: Un set di competenze è una raccolta di competenze cognitive che comprendono una pipeline di arricchimento.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a5277d4137ede5fe6dacf993413eefd7c9e46ad4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786910"
---
# <a name="create-skillset-api-version2017-11-11-preview"></a>Creare un set di competenze (api-version=2017-11-11-Preview)

**Si applica a:** api-version-2017-11-11-Preview

Un set di competenze è una raccolta di competenze cognitive usate per l'elaborazione in linguaggio naturale e altre trasformazioni. Le competenze includono, tra le altre cose, estrazione di entità denominate, estrazione di frasi chiave e suddivisione in blocchi del testo in pagine logiche.

Per usare il set di competenze, farvi riferimento in un indicizzatore di Ricerca di Azure e quindi eseguire l'indicizzatore per importare i dati, richiamare le trasformazioni e l'arricchimento ed eseguire il mapping dei campi di output a un indice. Un set di competenze è una risorsa di alto livello, ma funziona solo nell'ambito dell'elaborazione dell'indicizzatore. In quanto risorsa di alto livello, è possibile progettare un set di competenze una sola volta e quindi farvi riferimento in più indicizzatori. 

Un set di competenze è espresso in Ricerca di Azure tramite una richiesta HTTP PUT o POST. Per il metodo PUT, il corpo della richiesta è uno schema JSON che specifica le competenze richiamate. Le competenze sono concatenate tramite associazioni di input/output, in cui l'output di una trasformazione diventa l'input di un'altra.

Un set di competenze deve includere almeno una competenza. Non c'è un limite teorico al numero massimo di competenze, ma una configurazione comune ne contiene da tre a cinque.  


> [!NOTE]
> La ricerca cognitiva è disponibile in anteprima pubblica e l'esecuzione del set di competenze è attualmente offerta gratuitamente. Il prezzo per questa funzionalità verrà annunciato in un momento successivo.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```  

## <a name="request"></a>Richiesta  
 Per tutte le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta di **creazione di un set di competenze** può essere costruita usando un metodo PUT, con il nome del set di competenze incluso nell'URL. Se il set di competenze non esiste, viene creato. Se esiste già, viene aggiornato in base alla nuova definizione. Si noti che è possibile usare il metodo PUT per un solo set di competenze alla volta.  

 Il nome del set di competenze deve soddisfare i requisiti seguenti:

- Essere minuscolo
- Iniziare e terminare con una lettera o un numero
- Non includere barre o punti
- Essere composto da meno di 128 caratteri 

Dopo l'iniziale costituita da una lettera o un numero, il resto del nome del set di competenze può includere lettere, numeri e trattini, purché i trattini non siano consecutivi.  

Il parametro **api-version** è obbligatorio. L'unica versione disponibile è `2017-11-11-Preview`. Per un elenco di tutte le versioni, vedere [Versioni API in Ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=834796). 


### <a name="request-headers"></a>Intestazioni della richiesta  

 La tabella seguente descrive le intestazioni della richiesta obbligatorie e facoltative.  

|Intestazione di richiesta|DESCRIZIONE|  
|--------------------|-----------------|  
|*Content-Type:*|Richiesto. Impostare il valore su `application/json`|  
|*api-key:*|Richiesto. L'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta di **creazione del set di competenze** deve includere un'intestazione `api-key` impostata sulla chiave amministratore e non su una chiave di query.|  

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere sia il nome del servizio sia `api-key` dal dashboard del servizio nel portale di Azure. Per informazioni, vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md) .  

### <a name="request-body-syntax"></a>Sintassi del corpo della richiesta  

Il corpo della richiesta contiene la definizione del set di competenze, costituita da una o più competenze specificate in modo completo, oltre che i parametri di nome e descrizione facoltativi.  

La sintassi per la strutturazione del payload della richiesta è la seguente: Una richiesta di esempio viene fornita più avanti in questo articolo e anche in [Come definire un set di competenze](cognitive-search-defining-skillset.md).  

```
{   
    "name" : "Required for POST, optional for PUT. Friendly name of the skillset",  
    "description" : "Optional. Anything you want, or null",  
    "Skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",  
}  
```

### <a name="request-example"></a>Esempio di richiesta
 L'esempio seguente crea un set di competenze usato per arricchire una raccolta di documenti finanziari.

```http
PUT https://[servicename].search.windows.net/skillsets/financedocenricher?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

Il corpo della richiesta è un documento JSON. Questo set di competenze specifico usa due competenze in modo asincrono, elaborando in modo indipendente la sostanza di `contents` con due diverse trasformazioni. In alternativa, è possibile indirizzare l'output di una trasformazione in modo che diventi l'input di un'altra. Per alte informazioni, vedere [Come definire un set di competenze](cognitive-search-defining-skillset.md).

```json
{
  "name": "financedocenricher",
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
  ]
}
```

## <a name="response"></a>Risposta  

 In caso di richiesta eseguita correttamente, viene visualizzato il codice di stato "201 - Creato".  

 Per impostazione predefinita, il corpo della risposta contiene il codice JSON per la definizione del set di competenze creata. Se tuttavia l'intestazione della richiesta Prefer è impostata su return=minimal, il corpo della risposta sarà vuoto e il codice di stato per l'esito positivo sarà "204 - Nessun contenuto" invece di "201 - Creato". Questo vale indipendentemente dall'uso del metodo PUT o POST per creare il set di competenze.   

## <a name="see-also"></a>Vedere anche 

+ [Cognitive search overview](cognitive-search-concept-intro.md) (Panoramica della ricerca cognitiva)
+ [Guida introduttiva: Provare la ricerca cognitiva](cognitive-search-quickstart-blob.md)
+ [Esercitazione: Indicizzazione arricchita di BLOB di Azure](cognitive-search-tutorial-blob.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Come eseguire il mapping dei campi](cognitive-search-output-field-mapping.md)
+ [How to define a custom interface](cognitive-search-custom-skill-interface.md) (Come definire un'interfaccia personalizzata)
+ [Example: creating a custom skill](cognitive-search-create-custom-skill-example.md) (Esempio: Creazione di una competenza personalizzata)
+ [Competenze predefinite](cognitive-search-predefined-skills.md)