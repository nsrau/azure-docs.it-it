---
title: Usare l'intestazione content-type JSON per le coppie chiave-valore
titleSuffix: Azure App Configuration
description: Informazioni su come usare il tipo di contenuto JSON per i valori chiave
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta
ms.openlocfilehash: 725beb50e55852e35ee4434539ff158f082059df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88122039"
---
# <a name="leverage-content-type-to-store-json-key-values-in-app-configuration"></a>Usare Content-Type per archiviare i valori della chiave JSON nella configurazione dell'app

I dati vengono archiviati nella configurazione dell'app come valori chiave, in cui i valori vengono considerati come tipo stringa per impostazione predefinita. Tuttavia, è possibile specificare un tipo personalizzato sfruttando la proprietà Content-Type associata a ogni valore chiave, in modo che sia possibile mantenere il tipo originale dei dati o fare in modo che l'applicazione si comporti in modo diverso in base al tipo di contenuto.


## <a name="overview"></a>Panoramica

Nella configurazione dell'app, è possibile usare il tipo di supporto JSON come Content-Type dei valori chiave per usufruire dei vantaggi, ad esempio:
- **Gestione dei dati più semplice**: la gestione dei valori di chiave, come le matrici, diventerà molto più semplice nella portale di Azure.
- **Esportazione avanzata dei dati**: i tipi primitivi, le matrici e gli oggetti JSON verranno conservati durante l'esportazione dei dati.
- **Supporto nativo con il provider di configurazione dell'app**: i valori chiave con tipo di contenuto JSON funzioneranno correttamente quando vengono usati dalle librerie del provider di configurazione delle app nelle applicazioni.

#### <a name="valid-json-content-type"></a>Tipo di contenuto JSON valido

I tipi di supporto, come definito [qui](https://www.iana.org/assignments/media-types/media-types.xhtml), possono essere assegnati al tipo di contenuto associato a ogni chiave-valore.
Un tipo di supporto è costituito da un tipo e da un sottotipo. Se il tipo è `application` e il sottotipo (o suffisso) è `json` , il tipo di supporto verrà considerato un tipo di contenuto JSON valido.
Alcuni esempi di tipi di contenuto JSON validi sono:

- application/json
- applicazione/attività + JSON
- Application/vnd. foobar + JSON; charset = UTF-8

#### <a name="valid-json-values"></a>Valori JSON validi

Quando un valore chiave ha un tipo di contenuto JSON, il relativo valore deve essere in formato JSON valido per consentire ai client di elaborarlo correttamente. In caso contrario, i client possono avere esito negativo o eseguire il fallback e considerarli come formato stringa.
Di seguito sono riportati alcuni esempi di valori JSON validi:

- "John Doe"
- 723
- false
- Null
- "2020-01-01T12:34:56.789 Z"
- [1, 2, 3, 4]
- {"ObjectSetting": {"targeting": {"default": true, "Level": "Information"}}}

> [!NOTE]
> Nella parte restante di questo articolo, qualsiasi valore chiave nella configurazione dell'app con un tipo di contenuto JSON valido e un valore JSON valido verrà definito **chiave-valore**JSON. 

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare valori chiave JSON nella configurazione dell'app.
> * Importare i valori della chiave JSON da un file JSON.
> * Esportare i valori della chiave JSON in un file JSON.
> * Utilizzare i valori di chiave JSON nelle applicazioni.


## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/).
- Versione più recente dell'interfaccia della riga di comando di Azure (2.10.0 o versione successiva). Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Se si usa l'interfaccia della riga di comando di Azure, è necessario prima accedere usando `az login` . È anche possibile usare Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-an-app-configuration-store"></a>Creare un archivio di Configurazione app

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## <a name="create-json-key-values-in-app-configuration"></a>Creare valori chiave JSON nella configurazione dell'app

I valori della chiave JSON possono essere creati usando portale di Azure, l'interfaccia della riga di comando di Azure o l'importazione da un file JSON. In questa sezione sono disponibili istruzioni sulla creazione degli stessi valori di chiave JSON usando tutti e tre i metodi.

### <a name="create-json-key-values-using-azure-portal"></a>Creare valori chiave JSON usando portale di Azure

Individuare l'archivio di configurazione dell'app e selezionare **Configuration Explorer**  >  **create**  >  **key-value** per aggiungere le coppie chiave-valore seguenti:

| Chiave | Valore | Tipo di contenuto |
|---|---|---|
| Impostazioni: BackgroundColor | Verde | application/json |
| Impostazioni: FontSize | 24 | application/json |
| Impostazioni: UseDefaultRouting | false | application/json |
| Impostazioni: BlockedUsers | Null | application/json |
| Impostazioni: rilasciata | "2020-08-04T12:34:56.789 Z" | application/json |
| Impostazioni: RolloutPercentage | [25, 50, 75100] | application/json |
| Impostazioni: registrazione | {"Test": {"Level": "debug"}, "prod": {"Level": "Warning"}} | application/json |

Lasciare l' **etichetta** vuota e selezionare **applica**.

### <a name="create-json-key-values-using-azure-cli"></a>Creare valori chiave JSON usando l'interfaccia della riga di comando di Azure

I comandi seguenti creeranno i valori di chiave JSON nell'archivio di configurazione dell'app. Sostituire `<appconfig_name>` con il nome dell'archivio di configurazione dell'app.

```azurecli-interactive
appConfigName=<appconfig_name>
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BackgroundColor --value \"Green\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:FontSize --value 24
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:UseDefaultRouting --value false
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BlockedUsers --value null
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:ReleaseDate --value \"2020-08-04T12:34:56.789Z\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:RolloutPercentage --value [25,50,75,100]
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:Logging --value {\"Test\":{\"Level\":\"Debug\"},\"Prod\":{\"Level\":\"Warning\"}}
```

> [!IMPORTANT]
> Se si usa l'interfaccia della riga di comando di Azure o Azure Cloud Shell per creare valori di chiave JSON, il valore specificato deve essere una stringa JSON di escape.

### <a name="import-json-key-values-from-a-file"></a>Importare i valori della chiave JSON da un file

Creare un file JSON denominato `Import.json` con il contenuto seguente e importarlo come chiave-valori nella configurazione dell'app:

```json
{
  "Settings": {
    "BackgroundColor": "Green",
    "BlockedUsers": null,
    "FontSize": 24,
    "Logging": {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}},
    "ReleaseDate": "2020-08-04T12:34:56.789Z",
    "RolloutPercentage": [25,50,75,100],
    "UseDefaultRouting": false
  }
}
```

```azurecli-interactive
az appconfig kv import -s file --format json --path "~/Import.json" --content-type "application/json" --separator : --depth 2
```

> [!Note]
> L' `--depth` argomento viene usato per rendere flat i dati gerarchici da un file in coppie chiave-valore. In questa esercitazione viene specificato Depth per dimostrare che è anche possibile archiviare oggetti JSON come valori nella configurazione dell'app. Se non si specifica Depth, per impostazione predefinita gli oggetti JSON verranno resi bidimensionali al livello più profondo.

I valori della chiave JSON creati dovrebbero essere simili ai seguenti nella configurazione dell'app:

![Archivio di configurazione che contiene i valori chiave JSON](./media/create-json-settings.png)


## <a name="export-json-key-values-to-a-file"></a>Esportare i valori della chiave JSON in un file

Uno dei principali vantaggi dell'uso dei valori chiave JSON è la possibilità di mantenere il tipo di dati originale dei valori durante l'esportazione. Se un valore chiave nella configurazione dell'app non ha un tipo di contenuto JSON, il valore verrà considerato come stringa. 

Prendere in considerazione questi valori chiave senza contenuto JSON:

| Chiave | Valore | Tipo di contenuto |
|---|---|---|
| Impostazioni: FontSize | 24 | |
| Impostazioni: UseDefaultRouting | false | |

Quando si esportano questi valori di chiave in un file JSON, i valori verranno esportati come stringhe:
```json
{
  "Settings": {
    "FontSize": "24",
    "UseDefaultRouting": "false"
  }
}
```

Tuttavia, quando si esportano i valori della chiave JSON in un file, tutti i valori manterranno il tipo di dati originale. Per verificarlo, esportare i valori di chiave dalla configurazione dell'app in un file JSON. Si noterà che il file esportato ha lo stesso contenuto del `Import.json` file importato in precedenza.

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> Se l'archivio di configurazione dell'app include alcuni valori chiave senza JSON Content-Type, verranno esportati anche nello stesso file in formato stringa. Se si vogliono esportare solo i valori di chiave JSON, assegnare un'etichetta o un prefisso univoco ai valori chiave JSON e usare il filtro di etichette o prefissi durante l'esportazione.


## <a name="consuming-json-key-values-in-applications"></a>Utilizzo di valori di chiave JSON nelle applicazioni

Il modo più semplice per usare i valori di chiave JSON nell'applicazione è tramite le librerie del provider di configurazione dell'app. Con le librerie del provider non è necessario implementare una gestione speciale dei valori di chiave JSON nell'applicazione. Sono sempre deserializzati per l'applicazione in modo analogo alle altre librerie del provider di configurazione JSON. 

> [!Important]
> Il supporto nativo per i valori di chiave JSON è disponibile nella versione del provider di configurazione .NET 4.0.0 (o versioni successive). Per ulteriori informazioni, vedere la sezione [*passaggi successivi*](#next-steps) .

Se si usa l'SDK o l'API REST per leggere i valori chiave dalla configurazione dell'app, in base al tipo di contenuto, l'applicazione è responsabile della deserializzazione del valore di una chiave-valore JSON usando un deserializzatore JSON standard.


## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare i valori di chiave JSON nell'archivio di configurazione dell'app, creare un'applicazione per l'utilizzo di questi valori chiave:

* [Avvio rapido per ASP.NET Core](./quickstart-aspnet-core-app.md)
    * Prerequisito: [Microsoft. Azure. AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) Package v 4.0.0 o versione successiva.

* [Avvio rapido per .NET Core](./quickstart-dotnet-core-app.md)
    * Prerequisito: [Microsoft.Extensions.Configuration. Pacchetto AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration) v 4.0.0 o versione successiva.
