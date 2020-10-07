---
title: 'Avvio rapido: Eseguire la prima query Go'
description: Questo argomento di avvio rapido illustra la procedura per abilitare il pacchetto Resource Graph per Go ed eseguire la prima query.
ms.date: 06/16/2020
ms.topic: quickstart
ms.openlocfilehash: cc15b9fe75913ca47a14001a27e1e3c37d9ea71a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "84899393"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-go"></a>Avvio rapido: Eseguire la prima query di Resource Graph con Go

Il primo passaggio per usare Azure Resource Graph consiste nel verificare che i pacchetti necessari per Go siano installati. Questo argomento di avvio rapido illustra la procedura per aggiungere i pacchetti all'installazione di Go.

Al termine della procedura, i pacchetti risulteranno aggiunti all'installazione di Go e sarà possibile eseguire la prima query di Resource Graph.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="add-the-resource-graph-package"></a>Aggiungere il pacchetto Resource Graph

Per consentire a Go di eseguire query di Azure Resource Graph, è necessario aggiungere il pacchetto. Questo pacchetto funziona ovunque sia possibile usare Go, sia con [Bash in Windows 10](/windows/wsl/install-win10) o con installazione locale.

1. Controllare che sia installata l'ultima versione di Go (almeno **1.14**). Se non è ancora installata, scaricarla dal sito [Golang.org](https://golang.org/dl/).

1. Controllare che sia installata l'interfaccia della riga di comando di Azure più recente o almeno la versione **2.5.1**. Se non è ancora installata, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

   > [!NOTE]
   > L'interfaccia della riga di comando di Azure è necessaria per consentire a Go di usare il metodo `auth.NewAuthorizerFromCLI()` nell'esempio seguente. Per informazioni sulle altre opzioni, vedere [Azure SDK per Go - Altri dettagli sull'autenticazione](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Eseguire l'autenticazione tramite l'interfaccia della riga di comando di Azure.

   ```azurecli
   az login
   ```

1. In un ambiente Go a scelta installare i pacchetti necessari per Azure Resource Graph:

   ```bash
   # Add the Resource Graph package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="run-your-first-resource-graph-query"></a>Eseguire la prima query di Resource Graph

Una volta aggiunti i pacchetti Go all'ambiente scelto, provare a eseguire una semplice query di Resource Graph. La query restituisce le prime cinque risorse di Azure con il **nome** e il **tipo di risorsa** di ogni risorsa.

1. Creare l'applicazione Go e salvare l'origine seguente come `argQuery.go`:

   ```Go
   package main
   
   import (
       "fmt"
       "os"
       "context"
       "strconv"
       arg "github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph"
       "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
      // Get variables from command line arguments
      var query = os.Args[1]
      var subList = os.Args[2:]
   
      // Create and authorize a ResourceGraph client
      argClient := arg.New()
      authorizer, err := auth.NewAuthorizerFromCLI()
      if err == nil {
          argClient.Authorizer = authorizer
      } else {
          fmt.Printf(err.Error())
      }
   
      // Set options
      RequestOptions := arg.QueryRequestOptions {
          ResultFormat: "objectArray",
      }
   
      // Create the query request
      Request := arg.QueryRequest {
          Subscriptions: &subList,
          Query: &query,
          Options: &RequestOptions,
      }
   
      // Run the query and get the results
      var results, queryErr = argClient.Resources(context.Background(), Request)
      if queryErr == nil {
          fmt.Printf("Resources found: " + strconv.FormatInt(*results.TotalRecords, 10) + "\n")
          fmt.Printf("Results: " + fmt.Sprint(results.Data) + "\n")
      } else {
          fmt.Printf(queryErr.Error())
      }
   }
   ```

1. Compilare l'applicazione Go:

   ```bash
   go build argQuery.go
   ```

1. Eseguire la prima query di Azure Resource Graph usando l'applicazione Go compilata. Sostituire `<SubID>` con l'ID sottoscrizione:

   ```bash
   argQuery "Resources | project name, type | limit 5" "<SubID>"
   ```

   > [!NOTE]
   > Poiché questo esempio di query non fornisce un modificatore di ordine, ad esempio `order by`, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta.

1. Impostare il primo parametro su `argQuery` e cambiare la query per applicare `order by` alla proprietà **Name**. Sostituire `<SubID>` con l'ID sottoscrizione:

   ```bash
   argQuery "Resources | project name, type | limit 5 | order by name asc" "<SubID>"
   ```

   > [!NOTE]
   > Come nella prima query, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta. L'ordine dei comandi della query è importante. In questo esempio `order by` segue `limit`. Questo ordine dei comandi prima limita i risultati della query e successivamente li ordina.

1. Impostare il primo parametro su `argQuery` e cambiare la query per applicare prima `order by` alla proprietà **Name** e quindi `limit` ai primi cinque risultati. Sostituire `<SubID>` con l'ID sottoscrizione:

   ```bash
   argQuery "Resources | project name, type | order by name asc | limit 5" "<SubID>"
   ```

Quando la query finale viene eseguita più volte, presupponendo che non vengano apportate modifiche all'ambiente, i risultati restituiti saranno coerenti e ordinati in base alla proprietà **Name**, ma ancora limitati ai primi cinque risultati.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere i pacchetti installati dall'ambiente Go, usare il comando seguente:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati aggiunti i pacchetti di Resource Graph all'ambiente Go ed è stata eseguita la prima query. Per altre informazioni sul linguaggio di Resource Graph, passare alla pagina dei dettagli del linguaggio di query.

> [!div class="nextstepaction"]
> [Altre informazioni sul linguaggio di query](./concepts/query-language.md)