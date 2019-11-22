---
title: Esercitazione - Testare i moduli Terraform in Azure con Terratest
description: Informazioni su come usare Terratest per testare i moduli Terraform.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: bdb76fe2f87806c02a861ea84361b61a3e94b554
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969210"
---
# <a name="tutorial-test-terraform-modules-in-azure-using-terratest"></a>Esercitazione: Testare i moduli Terraform in Azure con Terratest

> [!NOTE]
> Il codice di esempio di questo articolo non funziona con la versione 0.12 (e successive).

È possibile usare i moduli Terraform per Azure per creare componenti riutilizzabili, componibili e testabili. I moduli Terraform incorporano l'incapsulamento, utile nell'implementazione di processi di infrastruttura come codice.

Quando si creano moduli Terraform, è importante implementare il controllo di qualità. Sfortunatamente è disponibile solo poca documentazione che spiega come creare unit test e test di integrazione nei moduli Terraform. Questa esercitazione presenta un'infrastruttura di test e le procedure consigliate adottate durante la creazione dei [moduli Terraform per Azure](https://registry.terraform.io/browse?provider=azurerm).

Sono state analizzate tutte le infrastrutture di test più diffuse ed è stato scelto [Terratest](https://github.com/gruntwork-io/terratest) per testare i moduli Terraform. Terratest viene implementato come file di libreria Go, Terratest fornisce una raccolta di funzioni helper e modelli per le comuni attività di test delle infrastrutture, ad esempio per effettuare richieste HTTP e per usare SSH per accedere a macchina virtuale specifica. L'elenco seguente illustra alcuni dei principali vantaggi dell'uso di Terratest:

- **Fornisce comodi componenti helper per il controllo dell'infrastruttura**. Questa funzionalità è utile quando si vuole verificare l'infrastruttura reale nell'ambiente reale.
- **La struttura di cartelle è organizzata in modo chiaro**. I test case sono organizzati in modo chiaro e seguono la [struttura di cartelle dei moduli Terraform standard](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Tutti i test case sono scritti in Go**. Gli sviluppatori che usano Terraform sono in gran parte sviluppatori Go. Gli sviluppatori Go non devono apprendere un altro linguaggio di programmazione per usare Terratest. Le uniche dipendenze necessarie per eseguire test case in Terratest, inoltre, sono Go e Terraform.
- **L'infrastruttura è altamente estendibile**. È possibile estendere funzioni aggiuntive basate su Terratest, incluse funzionalità specifiche di Azure.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo pratico è indipendente dalla piattaforma. È possibile eseguire gli esempi di codice usati in questo articolo in Windows, Linux o MacOS. 

Prima di iniziare, installare il software seguente:

- **Linguaggio di programmazione Go**: i test case Terraform sono scritti in [Go](https://golang.org/dl/).
- **dep**: [dep](https://github.com/golang/dep#installation) è uno strumento di gestione dipendenze per Go.
- **Interfaccia della riga di comando di Azure**: l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) è uno strumento da riga di comando che è possibile usare per gestire le risorse di Azure. Terraform supporta l'autenticazione in Azure tramite un'entità servizio oppure [con l'interfaccia della riga di comando di Azure](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).
- **mage**: viene usato il [file eseguibile mage](https://github.com/magefile/mage/releases) per illustrare come semplificare l'esecuzione dei test case Terratest. 

## <a name="create-a-static-webpage-module"></a>Creare un modulo di pagina Web statica

In questa esercitazione si crea un modulo Terraform che effettua il provisioning di una pagina Web statica caricando un singolo file HTML in un BLOB del servizio di archiviazione di Azure. Questo modulo offre agli utenti di tutto il mondo l'accesso alla pagina Web tramite un URL restituito dal modulo.

> [!NOTE]
> Creare tutti i file descritti in questa sezione nella posizione in cui si trova [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

Creare prima di tutto una nuova cartella denominata `staticwebpage` nella cartella `src` GoPath. La struttura di cartelle complessiva di questa esercitazione è illustrata nell'esempio seguente. I file contrassegnati con un asterisco `(*)` sono i più importanti in questa sezione.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

Il modulo della pagina Web statica accetta tre input. Gli input sono dichiarati in `./variables.tf`:

```hcl
variable "location" {
  description = "The Azure region in which to create all resources."
}

variable "website_name" {
  description = "The website name to use to create related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static home page HTML in your local file system."
  default     = "index.html"
}
```

Come accennato in precedenza nell'articolo, questo modulo restituisce anche un URL dichiarato in `./outputs.tf`:

```hcl
output "homepage_url" {
  value = azurerm_storage_blob.homepage.url
}
```

La logica principale del modulo effettua il provisioning di quattro risorse:
- **Gruppo di risorse**: il nome del gruppo di risorse è l'input `website_name` seguito da `-staging-rg`.
- **Account di archiviazione**: il nome dell'account di archiviazione è l'input `website_name` seguito da `data001`. Per rispettare i limiti relativi al nome dell'account di archiviazione, il modulo rimuove tutti i caratteri speciali e usa lettere minuscole nell'intero nome dell'account di archiviazione.
- **Contenitore con nome fisso**: il contenitore è denominato `wwwroot` e creato nell'account di archiviazione.
- **Singolo file HTML**: il file HTML viene letto dall'input `html_path` e caricato in `wwwroot/index.html`.

La logica del modulo della pagina Web statica viene implementata in `./main.tf`:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = var.location
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = azurerm_resource_group.main.name
  storage_account_name  = azurerm_storage_account.main.name
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = azurerm_resource_group.main.name
  storage_account_name   = azurerm_storage_account.main.name
  storage_container_name = azurerm_storage_container.main.name
  source                 = var.html_path
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Unit test

Terratest è progettato per i test di integrazione. A tale scopo, Terratest effettua il provisioning di risorse reali in un ambiente reale. Talvolta, i processi dei test di integrazione possono diventare eccezionalmente grandi, in particolare in presenza di un elevato numero di risorse di cui effettuare il provisioning. La logica che converte i nomi degli account di archiviazione a cui si fa riferimento nella sezione precedente rappresenta un buon esempio. 

In realtà, non è necessario effettuare il provisioning delle risorse. Si vuole solo verificare che la logica di conversione dei nomi sia corretta. Grazie alla flessibilità di Terratest, è possibile usare gli unit test. Gli unit test sono test case in esecuzione in locale (anche se è necessario l'accesso a Internet). I test case di tipo unit test eseguono i comandi `terraform init` e `terraform plan` per analizzare l'output di `terraform plan` e cercare i valori degli attributi da confrontare.

La parte rimanente di questa sezione descrive come usare Terratest per implementare uno unit test per assicurarsi che la logica usata per convertire i nomi degli account di archiviazione sia corretta. Vengono considerati solo i file contrassegnati con un asterisco `(*)`.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Per prima cosa, viene usato un file HTML vuoto denominato `./test/fixtures/storage-account-name/empty.html` come segnaposto.

Il file `./test/fixtures/storage-account-name/main.tf` costituisce la struttura del test case. Accetta un input `website_name`, che è anche l'input degli unit test. La logica è illustrata di seguito:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = var.website_name
  html_path    = "empty.html"
}
```

Il componente principale è l'implementazione degli unit test in `./test/storage_account_name_unit_test.go`.

Gli sviluppatori Go noteranno probabilmente che lo unit test corrisponde alla firma di una funzione di test Go classica che accetta un argomento di tipo `*testing.T`.

Nel corpo dello unit test sono presenti in totale cinque test case definiti nella variabile `testCases` (`key` come input e `value` come output previsto). Per ogni test case di tipo unit test viene eseguito prima di tutto `terraform init` usando come destinazione la cartella della fixture di test (`./test/fixtures/storage-account-name/`). 

Successivamente, un comando `terraform plan` che usa l'input del test case specifico (esaminare la definizione di `website_name` in `tfOptions`) salva il risultato in `./test/fixtures/storage-account-name/terraform.tfplan` (non indicato nella struttura di cartelle complessiva).

Il file del risultato viene analizzato in una struttura leggibile dal codice usando il parser di piano Terraform ufficiale.

Si cercheranno ora gli attributi di interesse (in questo caso, l'oggetto `name` di `azurerm_storage_account`) e si confronteranno i risultati con l'output previsto:

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify the test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

Per eseguire gli unit test, completare i passaggi seguenti nella riga di comando:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

Il risultato del test Go tradizionale viene restituito dopo circa un minuto.

### <a name="integration-test"></a>Test di integrazione

A differenza degli unit test, i test di integrazione devono effettuare il provisioning delle risorse in un ambiente reale per una prospettiva end-to-end. Terratest è molto utile per questo tipo di attività. 

Le procedure consigliate per i moduli Terraform includono l'installazione della cartella `examples`. La cartella `examples` contiene alcuni esempi end-to-end. Per evitare di usare dati reali, è possibile testare tali esempi come test di integrazione. In questa sezione vengono esaminati i tre file contrassegnati con un asterisco `(*)` nella struttura di cartelle seguente:

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Si inizierà prima di tutto con gli esempi. Viene creata una nuova cartella di esempio denominata `hello-world/` nella cartella `./examples/`. Di seguito viene fornita una semplice pagina HTML da caricare: `./examples/hello-world/index.html`.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demonstrate Terratest.</p>
</body>
</html>
```

L'esempio di Terraform `./examples/hello-world/main.tf` è simile a quello illustrato nello unit test. C'è una differenza significativa: l'esempio restituisce anche l'URL del codice HTML caricato come pagina Web denominata `homepage`.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = var.website_name
}

output "homepage" {
  value = module.staticwebpage.homepage_url
}
```

Vengono di nuovo usati Terratest e le funzioni di test Go classiche nel file del test di integrazione `./test/hello_world_example_test.go`.

A differenza degli unit test, i test di integrazione creano risorse effettive in Azure. Per questo motivo, è necessario fare attenzione a evitare conflitti di nomi. Fare particolare attenzione ad alcuni nomi univoci a livello globale, ad esempio i nomi degli account di archiviazione. Di conseguenza, il primo passaggio della logica di test consiste nel generare un oggetto `websiteName` casuale usando la funzione `UniqueId()` fornita da Terratest. Questa funzione genera un nome casuale contenente lettere minuscole, lettere maiuscole o numeri. `tfOptions` crea tutti i comandi Terraform che hanno come destinazione la cartella `./examples/hello-world/`. Fa anche in modo che `website_name` sia impostato sul valore casuale `websiteName`.

Quindi, vengono eseguiti `terraform init`, `terraform apply` e `terraform output`, uno alla volta. Viene usata un'altra funzione helper, `HttpGetWithCustomValidation()`, fornita da Terratest. La funzione helper viene usata per assicurarsi che il codice HTML venga caricato nell'URL `homepage` di output restituito da `terraform output`. Il codice di stato HTTP GET viene confrontato con `200` e viene eseguita la ricerca di alcune parole chiave nel contenuto HTML. Infine, si "promette" l'esecuzione di `terraform destroy` sfruttando la funzionalità `defer` di Go.

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent a naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify the test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output, and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demonstrate Terratest.")
    })
}
```

Per eseguire i test di integrazione, completare i passaggi seguenti nella riga di comando:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

Il risultato del test Go tradizionale viene restituito dopo circa due minuti. È anche possibile eseguire sia gli unit test che i test di integrazione eseguendo questi comandi:

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

I test di integrazione richiedono tempi molto più lunghi rispetto agli unit test (due minuti per un test case di integrazione rispetto a un minuto per cinque test case di tipo unit test). Spetta allo sviluppatore scegliere se usare unit test o test di integrazione in uno scenario. In genere, è preferibile usare gli unit test per la logica complessa con le funzioni HCL di Terraform. I test di integrazione sono preferibili per la prospettiva end-to-end di un utente.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Usare mage per semplificare l'esecuzione dei casi Terratest 
L'esecuzione dei test case in Azure Cloud Shell richiede l'esecuzione di diversi comandi in varie directory. Per rendere più efficiente questo processo, nel progetto viene introdotto il sistema di compilazione. In questa sezione si usa mage, un sistema di compilazione Go, per il processo.

L'unico elemento richiesto da mage è `magefile.go` nella directory radice del progetto (contrassegnato con `(+)` nell'esempio seguente):

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Ecco un esempio di `./magefile.go`. In questo script di compilazione, scritto in Go, vengono implementati cinque passaggi di compilazione:
- `Clean`: questo passaggio rimuove tutti i file temporanei generati durante le esecuzioni dei test.
- `Format`: questo passaggio esegue `terraform fmt` e `go fmt` per formattare la base di codice.
- `Unit`: questo passaggio esegue tutti gli unit test (usando la convenzione `TestUT_*` per i nomi di funzione) nella cartella `./test/`.
- `Integration`: questo passaggio è analogo a `Unit`, ma invece degli unit test, esegue i test di integrazione (`TestIT_*`).
- `Full`: questo passaggio esegue `Clean`, `Format`, `Unit` e `Integration` in sequenza.

```go
// +build mage

// Build a script to format and run tests of a Terraform module project
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// The default target when the command executes `mage` in Cloud Shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build and test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

È possibile usare i comandi seguenti per eseguire un gruppo di test completo. Il codice è simile ai passaggi usati in una sezione precedente. 

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only required when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage$ mage
```

È possibile sostituire l'ultima riga di comando con passaggi mage aggiuntivi. Ad esempio, è possibile usare `mage unit` o `mage clean`. È consigliabile incorporare i comandi `dep` e `az login` in magefile. In questo articolo non viene illustrato il codice necessario a tale scopo. 

Con mage, è anche possibile condividere i passaggi usando il sistema di pacchetti Go. In tal caso, è possibile semplificare gli oggetti magefile in tutti i moduli facendo riferimento solo a un'implementazione comune e dichiarando le dipendenze (`mg.Deps()`).

**Facoltativo: impostare variabili di ambiente di entità servizio per eseguire i test di accettazione**
 
Invece di eseguire `az login` prima dei test, è possibile completare l'autenticazione di Azure impostando variabili di ambiente di entità servizio. Terraform pubblica un [elenco di nomi di variabile di ambiente](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (di cui solo le prime quattro sono necessarie). Terraform pubblica anche istruzioni dettagliate che spiegano come [ottenere il valore di queste variabili di ambiente](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Vedere la pagina su Terratest in GitHub](https://github.com/gruntwork-io/terratest).