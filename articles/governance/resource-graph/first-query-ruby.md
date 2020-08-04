---
title: 'Avvio rapido: Eseguire la prima query con Ruby'
description: Questo argomento di avvio rapido illustra la procedura per abilitare la gemma Resource Graph per Ruby ed eseguire la prima query.
ms.date: 07/12/2020
ms.topic: quickstart
ms.openlocfilehash: 9763e2e9ec49d6fb4ea37fac12578ab23b7b5363
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100882"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-ruby"></a>Avvio rapido: Eseguire la prima query di Resource Graph con Ruby

Il primo passaggio per usare Azure Resource Graph consiste nel verificare che le gemme necessarie per Ruby siano installate. Questo argomento di avvio rapido illustra la procedura per aggiungere le gemme all'installazione di Ruby.

Al termine della procedura, le gemme risulteranno aggiunte all'installazione di Ruby e sarà possibile eseguire la prima query di Resource Graph.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Un'entità servizio di Azure, tra cui _clientId_ e _clientSecret_.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-graph-project"></a>Creare il progetto Resource Graph

Per consentire a Ruby di eseguire query di Azure Resource Graph, è necessario aggiungere la gemma a `Gemfile`. Questa gemma funziona ovunque sia possibile usare Ruby, ad esempio con [Azure Cloud Shell](https://shell.azure.com), [bash in Windows 10](/windows/wsl/install-win10) o in locale.

1. Controllare che sia installata l'ultima versione di Ruby (almeno **2.7.1**). Se non è ancora installata, scaricarla dal sito [Ruby-Lang.org](https://www.ruby-lang.org/en/downloads/).

1. Nell'ambiente Ruby preferito, inizializzare un bundle in una nuova cartella di progetto:

   ```bash
   # Initialize a bundle to create a new Gemfile
   bundle init
   ```

1. Aggiornare `Gemfile` con le gemme necessarie per Azure Resource Graph. Il file aggiornato dovrebbe essere simile all'esempio seguente:

   ```file
   # frozen_string_literal: true

   source "https://rubygems.org"

   git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

   # gem "rails"
   gem 'azure_mgmt_resourcegraph', '~> 0.17.2'
   ```

1. Dalla cartella del progetto, eseguire `bundle install`. Verificare che le gemme siano state installate con `bundle list`.

1. Nella stessa cartella di progetto, creare il file `argQuery.rb` con il codice seguente e salvare il file aggiornato:

   ```ruby
   #!/usr/bin/env ruby

   require 'azure_mgmt_resourcegraph'
   ARG = Azure::ResourceGraph::Profiles::Latest::Mgmt

   # Get arguments and set options
   options = {
       tenant_id: ARGV[0],
       client_id: ARGV[1],
       client_secret: ARGV[2],
       subscription_id: ARGV[3]
   }

   # Create Resource Graph client from options
   argClient = ARG::Client.new(options)

   # Create Resource Graph QueryRequest for subscription with query
   query_request = ARGModels::QueryRequest.new.tap do |qr|
       qr.subscriptions = [ARGV[3]]
       qr.query = ARGV[4]
   end

   # Get the resources from Resource Graph
   response = argClient.resources(query_request)

   # Convert data to JSON and output
   puts response.data.to_json
   ```

## <a name="run-your-first-resource-graph-query"></a>Eseguire la prima query di Resource Graph

Con lo script Ruby salvato e pronto per l'uso, è possibile provare una semplice query di Resource Graph. La query restituisce le prime cinque risorse di Azure con il **nome** e il **tipo di risorsa** di ogni risorsa.

In ogni chiamata a `argQuery` vengono usate variabili che è necessario sostituire con i propri valori:

- `{tenantId}`: sostituire con il proprio ID tenant
- `{clientId}`: sostituire con l'ID client dell'entità servizio
- `{clientSecret}`: sostituire con il segreto client dell'entità servizio
- `{subscriptionId}`: sostituire con l'ID sottoscrizione

1. Modificare le directory nella cartella del progetto in cui sono stati creati i file `Gemfile` e `argClient.rb`.

1. Eseguire la prima query di Azure Resource Graph usando le gemme e il metodo `resources`:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Poiché questo esempio di query non fornisce un modificatore di ordine, ad esempio `order by`, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta.

1. Impostare il parametro finale su `argQuery.rb` e cambiare la query per applicare `order by` alla proprietà **Name**:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Come nella prima query, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta. L'ordine dei comandi della query è importante. In questo esempio `order by` segue `limit`. Questo ordine dei comandi prima limita i risultati della query e successivamente li ordina.

1. Impostare il parametro finale su `argQuery.rb` e cambiare la query per applicare prima `order by` alla proprietà **Name** e quindi `limit` ai primi cinque risultati:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Quando la query finale viene eseguita più volte, presupponendo che non vengano apportate modifiche all'ambiente, i risultati restituiti saranno coerenti e ordinati in base alla proprietà **Name**, ma ancora limitati ai primi cinque risultati.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere le gemme installate dall'ambiente Ruby, usare il comando seguente:

```bash
# Remove the installed gems from the Ruby environment
gem uninstall azure_mgmt_resourcegraph
```

> [!NOTE]
> La gemma `azure_mgmt_resourcegraph` presenta dipendenze quali `ms_rest` e `ms_rest_azure` che potrebbero essere state installate a seconda dell'ambiente. È possibile disinstallare queste gemme anche se non sono più necessarie.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono state aggiunte le gemme di Resource Graph all'ambiente Ruby ed è stata eseguita la prima query. Per altre informazioni sul linguaggio di Resource Graph, passare alla pagina dei dettagli del linguaggio di query.

> [!div class="nextstepaction"]
> [Altre informazioni sul linguaggio di query](./concepts/query-language.md)
