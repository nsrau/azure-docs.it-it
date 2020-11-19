---
title: Informazioni sui concetti del repository del modello di dispositivo | Microsoft Docs
description: In qualità di sviluppatore di soluzioni o professionisti IT, informazioni sui concetti di base del repository del modello di dispositivo.
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b567efe2541bb33c905def73bb78398799b4ed69
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920543"
---
# <a name="device-model-repository"></a>Repository del modello di dispositivo

Il repository del modello di dispositivo (ricevitore) consente ai generatori di dispositivi di gestire e condividere la Plug and Play dei modelli di dispositivo. I modelli di dispositivo sono documenti JSON LD definiti con il [linguaggio di modellazione digitale gemello (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

RICEVITORE definisce un modello per archiviare le interfacce DTDL in una struttura di cartelle basata sull'identificatore del modello del dispositivo gemello (DTMI). È possibile individuare un'interfaccia in ricevitore convertendo DTMI in un percorso relativo. Il DTMI, ad esempio, viene `dtmi:com:example:Thermostat;1` convertito in `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-model-repository"></a>Repository del modello di dispositivo pubblico

Microsoft ospita una ricevitore pubblica con queste caratteristiche:

- Modelli curati. Microsoft esamina e approva tutte le interfacce disponibili usando un flusso di lavoro di convalida della richiesta pull di GitHub.
- Immutabilità.  Dopo la pubblicazione, non è possibile aggiornare un'interfaccia.
- Hyper-scale. Microsoft fornisce l'infrastruttura necessaria per creare un endpoint sicuro e scalabile in cui è possibile pubblicare e utilizzare i modelli di dispositivo.

## <a name="custom-device-model-repository"></a>Repository del modello di dispositivo personalizzato

Usare lo stesso modello ricevitore per creare un ricevitore personalizzato in qualsiasi supporto di archiviazione, ad esempio file system locali o server Web HTTP personalizzati. È possibile recuperare i modelli di dispositivo dal ricevitore personalizzato in modo analogo a quello del ricevitore pubblico modificando l'URL di base usato per accedere a ricevitore.

> [!NOTE]
> Microsoft fornisce gli strumenti per convalidare i modelli di dispositivo nella ricevitore pubblica. È possibile riutilizzare questi strumenti in repository personalizzati.

## <a name="public-models"></a>Modelli pubblici

I modelli di dispositivo pubblico archiviati nel repository del modello sono disponibili per l'utilizzo e l'integrazione da parte di tutti gli utenti nelle proprie applicazioni. I modelli di dispositivo pubblico consentono a un eco-sistema aperto per i generatori di dispositivi e gli sviluppatori di soluzioni di condividere e riutilizzare i propri dispositivi Plug and Play modelli di dispositivo.

Vedere la sezione [pubblicare un modello](#publish-a-model) per istruzioni su come pubblicare un modello nel repository del modello per renderlo pubblico.

Gli utenti possono sfogliare, cercare e visualizzare le interfacce pubbliche dal [repository GitHub](https://github.com/Azure/iot-plugandplay-models)ufficiale.

Tutte le interfacce nelle `dtmi` cartelle sono disponibili anche dall'endpoint pubblico [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Risolvere i modelli

Per accedere a livello di codice a queste interfacce, è necessario convertire un DTMI in un percorso relativo che è possibile usare per eseguire una query sull'endpoint pubblico.

Per convertire un DTMI in un percorso assoluto, usare la `DtmiToPath` funzione con `IsValidDtmi` :

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
1. Aggiungere le nuove interfacce alla `dtmi` cartella usando la convenzione cartella/nome file. Per altre informazioni, vedere [importare un modello nella `dtmi/` cartella](#import-a-model-to-the-dtmi-folder).
1. Convalidare i modelli localmente utilizzando lo `dmr-client` strumento. Per altre informazioni, vedere [convalidare i modelli](#validate-models).
1. Eseguire il commit delle modifiche in locale ed effettuare il push nel fork.
1. Dal fork creare una richiesta pull destinata al `main` ramo. Vedere la documentazione relativa alla [creazione di un problema o di una richiesta pull](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) .
1. Esaminare i [requisiti della richiesta pull](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md).

La richiesta pull attiva un set di azioni GitHub che convalidano le interfacce inviate e assicura che la richiesta pull soddisfi tutti i requisiti.

Microsoft risponderà a una richiesta pull con tutti i controlli in tre giorni lavorativi.

### <a name="dmr-client-tools"></a>`dmr-client` strumenti

Gli strumenti usati per convalidare i modelli durante i controlli della richiesta pull possono essere usati anche per aggiungere e convalidare le interfacce DTDL localmente.

> [!NOTE]
> Questo strumento richiede [.NET SDK](https://dotnet.microsoft.com/download) versione 3,1 o successiva.

### <a name="install-dmr-client"></a>Installare `dmr-client`

```bash
curl -L https://aka.ms/install-dmr-client-linux | bash
```

```powershell
iwr https://aka.ms/install-dmr-client-windows -UseBasicParsing | iex
```

### <a name="import-a-model-to-the-dtmi-folder"></a>Importare un modello nella `dtmi/` cartella

Se il modello è già archiviato in file JSON, è possibile usare il `dmr-client import` comando per aggiungerli alla `dtmi/` cartella con i nomi file corretti:

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> È possibile usare l' `--local-repo` argomento per specificare la cartella radice del repository locale.

### <a name="validate-models"></a>Convalida modelli

È possibile convalidare i modelli con il `dmr-client validate` comando:

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> La convalida usa la versione più recente del parser DTDL per assicurarsi che tutte le interfacce siano compatibili con la specifica del linguaggio DTDL.

Per convalidare le dipendenze esterne, devono esistere nel repository locale. Per convalidare i modelli, utilizzare l' `--repo` opzione per specificare una `local` `remote` cartella o per risolvere le dipendenze:

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>Convalida rigorosa

Il ricevitore include [requisiti](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)aggiuntivi, usare il `stict` flag per convalidare il modello:

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

Controllare l'output della console per eventuali messaggi di errore.

### <a name="export-models"></a>Esportare i modelli

I modelli possono essere esportati da un repository specifico (locale o remoto) a un singolo file usando una matrice JSON:

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo suggerito consiste nel rivedere l' [architettura plug and Play](concepts-architecture.md).
