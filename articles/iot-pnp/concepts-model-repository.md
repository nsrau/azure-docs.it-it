---
title: Informazioni sui concetti del repository del modello di dispositivo | Microsoft Docs
description: In qualità di sviluppatore di soluzioni o professionisti IT, informazioni sui concetti di base del repository del modello di dispositivo.
author: rido-min
ms.author: rmpablos
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 95c9b6dee402bc0c2dd2cab8ef3200cfd9213d61
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126825"
---
# <a name="device-model-repository"></a>Repository del modello di dispositivo

Il repository del modello di dispositivo (ricevitore) consente ai generatori di dispositivi di gestire e condividere la Plug and Play dei modelli di dispositivo. I modelli di dispositivo sono documenti JSON LD definiti con il [linguaggio di modellazione digitale gemello (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

RICEVITORE definisce un modello per archiviare le interfacce DTDL in una struttura di cartelle basata sull'identificatore del modello del dispositivo gemello (DTMI). È possibile individuare un'interfaccia in ricevitore convertendo DTMI in un percorso relativo. Il DTMI, ad esempio, viene `dtmi:com:example:Thermostat;1` convertito in `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-model-repository"></a>Repository del modello di dispositivo pubblico

Microsoft ospita una ricevitore pubblica con queste caratteristiche:

- Modelli curati. Microsoft esamina e approva tutte le interfacce disponibili usando un flusso di lavoro di convalida della richiesta pull di GitHub aperto.
- Immutabilità.  Dopo la pubblicazione, non è possibile aggiornare un'interfaccia.
- Hyper-scale. Microsoft fornisce tutte le infrastrutture necessarie per creare un endpoint sicuro ed estremamente scalabile.

## <a name="custom-device-model-repository"></a>Repository del modello di dispositivo personalizzato

Per creare un ricevitore personalizzato, è possibile usare lo stesso modello ricevitore in qualsiasi supporto di archiviazione, ad esempio file system locali o server Web HTTP personalizzati. È possibile recuperare i modelli dal ricevitore personalizzato in modo analogo al ricevitore pubblico semplicemente modificando l'URL di base usato per accedere a ricevitore.

> [!NOTE]
> Gli strumenti usati per convalidare i modelli nel ricevitore pubblico possono essere riutilizzati in repository personalizzati.

## <a name="public-models"></a>Modelli pubblici

I modelli di dispositivi gemelli digitali pubblici archiviati nel repository dei modelli sono disponibili per l'utilizzo e l'integrazione delle applicazioni da parte degli utenti. I modelli pubblici consentono a un ecosistema aperto per i compilatori di dispositivi e gli sviluppatori di soluzioni di condividere e riutilizzare i propri Plug and Play i modelli di dispositivo.

Vedere la sezione [pubblicare un modello](#publish-a-model) per istruzioni su come pubblicare un modello nel repository del modello per renderlo pubblico.

Gli utenti possono sfogliare, cercare e visualizzare le interfacce pubbliche dal [repository GitHub](https://github.com/Azure/iot-plugandplay-models)ufficiale.

Tutte le interfacce nelle `dtmi` cartelle sono disponibili anche dall'endpoint pubblico [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Risolvere i modelli

Per accedere a livello di codice a queste interfacce, è necessario convertire un dtmi in un percorso relativo che è possibile usare per eseguire una query sull'endpoint pubblico. Nell'esempio di codice seguente viene illustrato come eseguire questa operazione:

Per convertire DTMI in un percorso assoluto, viene usata la `DtmiToPath` funzione con `IsValidDtmi` :

```cs
static string DtmiToPath(string dtmi)
{
    if (!IsValidDtmi(dtmi))
    {
        return null;
    }
    // dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
    return $"/{dtmi.ToLowerInvariant().Replace(":", "/").Replace(";", "-")}.json";
}

static bool IsValidDtmi(string dtmi)
{
    // Regex defined at https://github.com/Azure/digital-twin-model-identifier#validation-regular-expressions
    Regex rx = new Regex(@"^dtmi:[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?(?::[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?)*;[1-9][0-9]{0,8}$");
    return rx.IsMatch(dtmi);
}
```

Con il percorso risultante e l'URL di base per il repository è possibile ottenere l'interfaccia:

```cs
const string _repositoryEndpoint = "https://devicemodels.azure.com";

string dtmiPath = DtmiToPath(dtmi.ToString());
string fullyQualifiedPath = $"{_repositoryEndpoint}{dtmiPath}";
string modelContent = await _httpClient.GetStringAsync(fullyQualifiedPath);
```

## <a name="publish-a-model"></a>Pubblicare un modello

> [!Important]
> È necessario avere un account GitHub per poter inviare i modelli alla ricevitore pubblica.

1. Creare un fork del repository GitHub pubblico: [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) .
1. Clonare il repository con fork. Se lo si desidera, è possibile creare un nuovo ramo per evitare che le modifiche siano isolate dal `main` ramo.
1. Aggiungere le nuove interfacce alla `dtmi` cartella usando la convenzione cartella/nome file. Vedere lo strumento [Aggiungi modello](#add-model) .
1. Convalidare i modelli localmente utilizzando la sezione [script per convalidare le modifiche](#validate-files) .
1. Eseguire il commit delle modifiche in locale ed effettuare il push nel fork.
1. Dal fork creare una richiesta pull destinata al `main` ramo. Vedere la documentazione relativa alla [creazione di un problema o di una richiesta pull](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) .
1. Esaminare i [requisiti della richiesta](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)pull.

La richiesta pull attiva una serie di azioni di GitHub che convalideranno le nuove interfacce inviate e assicurarsi che la richiesta pull soddisfi tutti i controlli.

Microsoft risponderà a una richiesta pull con tutti i controlli in tre giorni lavorativi.

### <a name="add-model"></a>Aggiungi modello

Nei passaggi seguenti viene illustrato il modo in cui lo script di add-model.js consente di aggiungere una nuova interfaccia. Per eseguire questo script è necessario Node.js:

1. Al prompt dei comandi passare al repository git locale
1. Eseguire `npm install`
1. Eseguire `npm run add-model <path-to-file-to-add>`

Controllare l'output della console per eventuali messaggi di errore.

### <a name="local-validation"></a>Convalida locale

È possibile eseguire gli stessi controlli di convalida in locale prima di inviare la richiesta pull per facilitare la diagnosi dei problemi in anticipo.

#### <a name="validate-files"></a>Validate-files

`npm run validate-files <file1.json> <file2.json>` Verifica che il percorso del file corrisponda alla cartella e ai nomi di file previsti.

#### <a name="validate-ids"></a>Validate-ID

`npm run validate-ids <file1.json> <file2.json>` Verifica che tutti gli ID definiti nel documento usino la stessa radice dell'ID principale.

#### <a name="validate-deps"></a>Validate-Deps

`npm run validate-deps <file1.json> <file2.json>` Verifica che tutte le dipendenze siano disponibili nella `dtmi` cartella.

#### <a name="validate-models"></a>Validate-modelli

È possibile eseguire l' [esempio di convalida DTDL](https://github.com/Azure-Samples/DTDL-Validator) per convalidare i modelli localmente.

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo suggerito consiste nel rivedere l' [architettura plug and Play](concepts-architecture.md).
