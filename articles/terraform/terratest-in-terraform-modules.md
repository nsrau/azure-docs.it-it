---
title: Testare i moduli Terraform in Azure con Terratest
description: Informazioni su come usare Terratest per testare i moduli Terraform.
services: terraform
ms.service: terraform
keywords: terraform, devops, account di archiviazione, azure, terratest, unit test, test di integrazione
author: JunyiYi
manager: jeconnoc
ms.author: junyi
ms.topic: tutorial
ms.date: 10/19/2018
ms.openlocfilehash: 7feee063c7b311934f7d157a9dff62d803a041b0
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638712"
---
# <a name="test-terraform-modules-in-azure-using-terratest"></a>Testare i moduli Terraform in Azure con Terratest

I moduli Terraform vengono usati per creare componenti riutilizzabili, componibili e testabili, che incorporano l'incapsulamento nel mondo dell'"infrastruttura come codice".

Come con altri componenti software, il controllo di qualità svolge un ruolo importante nei moduli Terraform. Purtroppo, la documentazione disponibile che spiega come creare unit test e test di integrazione nei moduli Terraform è non è sufficiente. Questa esercitazione introduce un'infrastruttura di test e le procedure consigliate adottate durante la compilazione dei [moduli Terraform per Azure](https://registry.terraform.io/browse?provider=azurerm).

Dopo aver considerato tutte le infrastrutture di test più diffuse, abbiamo scelto di usare [Terratest](https://github.com/gruntwork-io/terratest). Terratest viene implementato come file di libreria Go, che fornisce una raccolta degli schemi e delle funzioni helper per le comuni attività di test di infrastruttura, ad esempio effettuare le richieste HTTP e SSH a una determinata macchina virtuale. Alcuni dei vantaggi principali del Terratest sono:

- **Fornisce helper pratici per controllare l'infrastruttura.** Questa funzionalità è utile quando si vuole verificare l'infrastruttura reale nell'ambiente reale.
- **La struttura di cartelle è organizzata in modo chiaro.** I test case saranno organizzati in modo chiaro e seguiranno la [struttura di cartelle del modulo Terraform standard](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Tutti i test case sono scritti in Go.** Dal momento che la maggior parte degli sviluppatori di Terraform sono già sviluppatori Go, l'uso di Terratest elimina la necessità di apprendere un altro linguaggio di programmazione. In più, le uniche dipendenze necessarie per eseguire test case in Terratest sono Go e Terraform.
- **Questa infrastruttura è altamente estendibile.** Non è difficile estendere le funzioni aggiuntive su Terratest, ad esempio le funzionalità specifiche di Azure.

## <a name="prerequisites"></a>Prerequisiti

Questa guida pratica è indipendente dalla piattaforma ed è eseguibile su Windows, Linux o MacOS. Prima di procedere, installare il software seguente:

- **Linguaggio di programmazione Go**: i test case Terraform sono scritti in [Go](https://golang.org/dl/).
- **dep**: [dep](https://github.com/golang/dep#installation) è uno strumento di gestione dipendenze per Go.
- **Interfaccia della riga di comando di Azure**: l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) è uno strumento da riga di comando per la gestione delle risorse di Azure. Terraform supporta l'autenticazione in Azure attraverso un'entità servizio oppure [con l'interfaccia della riga di comando di Azure](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).
- **mage**: verrà usato il [file eseguibile mage](https://github.com/magefile/mage/releases) per scoprire come semplificare l'esecuzione dei test case Terratest. 

## <a name="create-a-static-webpage-module"></a>Creare un modulo di pagina Web statica

In questa esercitazione si creerà un modulo Terraform che eseguirà il provisioning di una pagina Web statica caricando un unico file HTML nel BLOB del servizio di archiviazione di Azure. Questo modulo consentirà agli utenti in tutto il mondo di accedere a questa pagina Web attraverso un URL restituito dal modulo.

> [!NOTE]
> Tutti i file descritti in questa sezione devono essere creati nel proprio ambiente [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

Prima di tutto, creare una nuova cartella denominata `staticwebpage` nella cartella `src` dell'ambiente GoPath. La struttura di cartelle complessiva di questa esercitazione viene illustrata di seguito (i file contrassegnati con un asterisco `(*)` sono l'obiettivo principale di questa sezione).

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

Il modulo della pagina Web statica accetta tre input, che vengono dichiarati in `./variables.tf`:

```hcl
variable "location" {
  description = "The Azure region in which all resources will be created."
}

variable "website_name" {
  description = "The website name which will be used to create a bunch of related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static homepage HTML in your local filesystem."
  default     = "index.html"
}
```

Come accennato in precedenza, questo modulo restituisce anche un URL dichiarato in `./outputs.tf`:

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

Questo introduce la logica principale di questo modulo. In totale, verrà eseguito il provisioning di quattro risorse:
- Un gruppo di risorse il cui nome è l'input `website_name` con l'aggiunta di `-staging-rg`.
- Un account di archiviazione il cui nome è l'input `website_name` con l'aggiunta di `data001`. Tuttavia, per rispettare i limiti del nome dell'account di archiviazione, il modulo rimuoverà tutti i caratteri speciali dall'intero nome, che sarà scritto in caratteri minuscoli.
- Un contenitore denominato fisso `wwwroot` creato nell'account di archiviazione precedente.
- Un singolo file HTML letto dall'input `html_path` e caricato in `wwwroot/index.html`.

La logica del modulo della pagina Web statica viene implementata in `./main.tf`:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = "${var.location}"
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = "${azurerm_resource_group.main.name}"
  location                 = "${azurerm_resource_group.main.location}"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = "${azurerm_resource_group.main.name}"
  storage_account_name  = "${azurerm_storage_account.main.name}"
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = "${azurerm_resource_group.main.name}"
  storage_account_name   = "${azurerm_storage_account.main.name}"
  storage_container_name = "${azurerm_storage_container.main.name}"
  source                 = "${var.html_path}"
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Unit test

Terratest è tradizionalmente uno strumento progettato per i test di integrazione, quindi esegue il provisioning di risorse reali in un ambiente reale. A volte questi processi possono diventare eccezionalmente grandi, specialmente se si hanno numerose risorse di cui eseguire il provisioning. La logica di conversione di denominazione dell'account di archiviazione descritta nella sezione precedente è un buon esempio: non è effettivamente necessario eseguire il provisioning di alcuna risorsa, ma solo assicurarsi che la logica di conversione di denominazione sia corretta.

Grazie alla flessibilità di Terratest, questo avviene facilmente con l'uso di unit test. Gli unit test sono test case eseguiti in locale (anche se l'accesso a internet è comunque necessario) usando semplici comandi `terraform init` e `terraform plan`; gli unit test case analizzano l'output di `terraform plan` e cercano i valori di attributo da confrontare.

La parte restante di questa sezione descrive come usare Terratest per implementare un unit test in modo da assicurarsi che la logica di conversione di denominazione dell'account di archiviazione sia corretta. In questo caso verranno considerati solo i file contrassegnati con un asterisco `(*)`.

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

Per prima cosa, un file HTML vuoto `./test/fixtures/storage-account-name/empty.html` è solo un segnaposto.

Il file `./test/fixtures/storage-account-name/main.tf` è la struttura del test case. Accetta un solo input `website_name`, che è anche l'input degli unit test. La relativa logica è visualizzata di seguito:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = "${var.website_name}"
  html_path    = "empty.html"
}
```

Infine, il componente principale è l'implementazione degli unit test: `./test/storage_account_name_unit_test.go`

Gli sviluppatori Go riconosceranno che corrisponde alla firma di una classica funzione di test Go quando si accetta un argomento di tipo `*testing.T`.

Nel corpo dell'unit test, è presente un totale di cinque test case definiti nella variabile `testCases` (chiave come input, valore come output previsto). Per ogni unit test case, verrà eseguito prima di tutto `terraform init` con destinazione la cartella della fixture di test (`./test/fixtures/storage-account-name/`). 

In seguito, un comando `terraform plan` con l'input di test case specifico (esaminare la definizione di `website_name` in `tfOptions`) salverà il risultato in `./test/fixtures/storage-account-name/terraform.tfplan` (non elencato nella struttura di cartelle complessiva).

Successivamente, verrà analizzato il file di risultati in una struttura leggibile dal codice usando il parser di piano Terraform ufficiale.

A questo punto si cercheranno gli attributi a cui si è interessati (in questo caso, il `name` dell'`azurerm_storage_account`) per confrontarli con l'output previsto.

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
        // Specify test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions.Vars, "plan", "-out="+tfPlanOutput)...)

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

Per eseguire gli unit test, è necessario completare i passaggi seguenti nella riga di comando.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

Verrà visualizzato il risultato del test Go tradizionale dopo circa un minuto.

### <a name="integration-test"></a>Test di integrazione

A differenza degli unit test, i test di integrazione sono necessari per effettuare il provisioning delle risorse in un ambiente reale dal punto di vista end-to-end. Terratest è davvero utile a tale scopo. Dal momento che la procedura consigliata del modulo Terraform consiglia anche la cartella `examples` contenente alcuni esempi end-to-end, perché non testare quegli esempi come test di integrazione? In questa sezione, ci concentreremo su tre file, ciascuno contrassegnato con un asterisco `(*)`.

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

Si inizierà prima di tutto con gli esempi. Verrà creata una nuova cartella di esempio denominata `hello-world/` nella cartella `./examples/`. Di seguito è riportata una semplice pagina HTML da caricare `./examples/hello-world/index.html`:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demostrate Terratest.</p>
</body>
</html>
```

L'esempio di Terraform `./examples/hello-world/main.tf` è simile a quello illustrato nello unit test, con una sola grande differenza: stampa anche l'URL del codice HTML caricato denominato come `homepage`.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = "${var.website_name}"
}

output "homepage" {
  value = "${module.staticwebpage.homepage_url}"
}
```

Terratest e la classica funzione di test Go vengono di nuovo visualizzati nel file di test di integrazione `./test/hello_world_example_test.go`.

A differenza degli unit test, i test di integrazione creeranno risorse effettive in Azure, ed è per questo motivo he è necessario prestare attenzione a evitare conflitti di nome. Prestare particolare attenzione ad alcuni nomi univoci a livello globale, ad esempio il nome dell'account di archiviazione. Di conseguenza, il primo passaggio della logica di test consiste nel generare un `websiteName` casuale usando la funzione `UniqueId()` fornita da Terratest. Questa funzione genera un nome casuale contenente lettere minuscole, lettere maiuscole o numeri. `tfOptions` permette di indirizzare tutti i comandi di Terraform alla cartella `./examples/hello-world/` e garantisce che il `website_name` sia impostato su `websiteName` casuale.

Quindi, vengono eseguiti `terraform init`, `terraform apply` e `terraform output`, uno alla volta. È stata usata un'altra funzione helper `HttpGetWithCustomValidation()` fornita da Terratest per assicurarsi che l'HTML venga caricato nell'URL `homepage` di output restituito da `terraform output` confrontando il codice di stato Get HTTP con `200` e cercando alcune parole chiave nel contenuto HTML. Infine, si "promette" l'esecuzione di `terraform destroy` sfruttando la funzionalità `defer` di Go.

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

    // Generate a random website name to prevent naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demostrate Terratest.")
    })
}
```

Per eseguire i test di integrazione, è necessario completare i passaggi seguenti nella riga di comando.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

Verrà visualizzato il risultato del test Go tradizionale dopo circa due minuti. Naturalmente, è anche possibile eseguire sia gli unit test che i test di integrazione eseguendo:

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

Come si può vedere, i test di integrazione richiedono tempi molto più lunghi rispetto agli unit test (due minuti per un caso di integrazione mentre un minuto per cinque casi unit). La decisione su quando usare gli unit test e quando sfruttare i test di integrazione spetta comunque all'utente. In genere, è preferibile usare gli unit test per la logica complessa con le funzioni HCL di Terraform e i test di integrazione dal punto di vista end-to-end di un utente.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Usare mage per semplificare l'esecuzione dei casi Terratest 

Come si è visto, l'esecuzione di test case nella shell non è un compito facile perché è necessario passare a diverse directory ed eseguire diversi comandi. È per questo motivo che si introdurrà il sistema di compilazione nel progetto. In questa sezione si userà un mage del sistema di compilazione Go per eseguire il processo.

L'unica cosa necessaria per mage è un `magefile.go` nella directory radice del progetto (contrassegnato con `(+)` nella figura seguente).

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

Di seguito è fornito un esempio di `./magefile.go`. In questo script di compilazione, scritto in Go, sono stati implementati cinque passaggi di compilazione:
- `Clean`: questo passaggio rimuoverà tutti i file temporanei/generati durante le esecuzioni di test.
- `Format`: questo passaggio eseguirà `terraform fmt` e `go fmt` per formattare la codebase.
- `Unit`: questo passaggio eseguirà tutti gli unit test (usando la convenzione `TestUT_*` del nome della funzione) nella cartella `./test/`.
- `Integration`: analogo a `Unit`, ma invece degli unit test, esegue i test di integrazione (`TestIT_*`).
- `Full`: questo passaggio esegue `Clean`, `Format`, `Unit', and `Integration`, in sequenza.

```go
// +build mage

// Build script to format and run tests of a Terraform module project.
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// Default target when execute `mage` in shell
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

// A build step that removes temporary build/test files
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

Analogamente all'esecuzione dei passaggi precedenti, è possibile usare i comandi seguenti per eseguire un gruppo di test completo:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only requied when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage$ mage
```

È possibile sostituire l'ultima riga di comando con tutti i passaggi mage, ad esempio `mage unit` o `mage clean`. A questo punto si potrebbe pensare che sono ancora presenti molte righe di comando ed è una buona idea incorporare comandi `dep` nonché `az login` nel magefile. In questa fase, però, non verrà illustrato il codice. Un ulteriore passaggio dell'uso di mage è che la procedura potrebbe essere condivisa usando il sistema a pacchetto di Go. Di conseguenza, i magefile in tutti i moduli potrebbero essere semplificati semplicemente facendo riferimento a un'implementazione comune e dichiarando le dipendenze (`mg.Deps()`).

> [!NOTE]
> **Opzione: impostare variabili di ambiente dell'entità servizio per eseguire i test di accettazione**
> 
> Anziché eseguire `az login` prima dei test, è possibile eseguire l'autenticazione di Azure impostando le variabili di ambiente dell'entità servizio. Terraform pubblica [un elenco di nomi di variabile di ambiente](https://www.terraform.io/docs/providers/azurerm/index.html#testing) (di cui solo le prime quattro sono necessarie). Terraform pubblica anche istruzioni dettagliate che spiegano come [ottenere il valore di queste variabili di ambiente](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Terratest, vedere la [relativa pagina di GitHub](https://github.com/gruntwork-io/terratest). Informazioni utili su mage sono reperibili nella [pagina di GitHub](https://github.com/magefile/mage) e nella [home page](https://magefile.org/) relative.
