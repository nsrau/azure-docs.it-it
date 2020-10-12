---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: a4f03223b5067d18f5d6e747b3bb630a86031b8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88689559"
---
Se non si è in grado di usare i bundle di estensione, è possibile usare Azure Functions Core Tools localmente per installare i pacchetti di estensione specifici richiesti dal progetto.

> [!IMPORTANT]
> Non è possibile installare in modo esplicito le estensioni in un'app per le funzioni che usa bundle di estensione. Rimuovere la `extensionBundle` sezione in *host.jsprima di* installare le estensioni in modo esplicito.

Gli elementi seguenti descrivono alcuni motivi per cui potrebbe essere necessario installare le estensioni manualmente:

* È necessario accedere a una versione specifica di un'estensione non disponibile in un bundle.
* È necessario accedere a un'estensione personalizzata non disponibile in un bundle.
* È necessario accedere a una combinazione specifica di estensioni non disponibili in un singolo bundle.

> [!NOTE]
> Per installare manualmente le estensioni usando gli strumenti di base, è necessario che sia installato [.NET Core 2. x SDK](https://dotnet.microsoft.com/download) . Il .NET Core SDK viene usato da Azure Functions Core Tools per installare le estensioni da NuGet. Non è necessario essere a conoscenza di .NET per usare le estensioni di funzioni di Azure.

Quando si installano in modo esplicito le estensioni, un file di progetto .NET denominato Extensions. csproj viene aggiunto alla radice del progetto. Questo file definisce il set di pacchetti NuGet necessari per le funzioni. Sebbene sia possibile usare i [riferimenti ai pacchetti NuGet](/nuget/consume-packages/package-references-in-project-files) in questo file, Core Tools consente di installare le estensioni senza dover modificare manualmente il file.

Esistono diversi modi per usare gli strumenti di base per installare le estensioni necessarie nel progetto locale. 

#### <a name="install-all-extensions"></a>Installare tutte le estensioni 

Usare il comando seguente per aggiungere automaticamente tutti i pacchetti di estensione usati dalle associazioni nel progetto locale:

```dotnetcli
func extensions install
```
Il comando legge il *function.jssu* file per vedere quali pacchetti sono necessari, li installa e ricompila il progetto Extensions (Extensions. csproj). Consente di aggiungere eventuali nuove associazioni alla versione corrente, ma non aggiorna le associazioni esistenti. Usare l'opzione `--force` per aggiornare le associazioni esistenti alla versione più recente quando si installano quelle nuove.

Se l'app per le funzioni USA associazioni non riconosciute dagli strumenti di base, è necessario installare manualmente l'estensione specifica.

#### <a name="install-a-specific-extension"></a>Installare un'estensione specifica

Usare il comando seguente per installare un pacchetto di estensione specifico in una versione specifica, in questo caso l'estensione di archiviazione:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```
