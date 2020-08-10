---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: 2936d22eacef73daef4433b3fd296dd4757fa410
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031125"
---
Se non si è in grado di usare i bundle di estensione, è possibile usare Azure Functions Core Tools localmente per installare i pacchetti di estensione specifici richiesti dal progetto. 

> [!NOTE]
> Per installare manualmente le estensioni usando gli strumenti di base, è necessario che sia installato .NET Core 2. x SDK.

Quando si installano in modo esplicito le estensioni, un file di progetto .NET denominato Extensions. csproj viene aggiunto alla radice del progetto. Questo file definisce il set di pacchetti NuGet necessari per le funzioni. Sebbene sia possibile usare i [riferimenti ai pacchetti NuGet](/nuget/consume-packages/package-references-in-project-files) in questo file, Core Tools consente di installare le estensioni senza dover modificare manualmente il file.

Esistono diversi modi per usare gli strumenti di base per installare le estensioni necessarie nel progetto locale. 

#### <a name="install-all-extensions"></a>Installare tutte le estensioni 

Usare il comando seguente per aggiungere automaticamente tutti i pacchetti di estensione usati dalle associazioni nel progetto locale:

```dotnetcli
func extensions install
```
Il comando legge il *function.jssu* file per vedere quali pacchetti sono necessari, li installa e ricompila il progetto Extensions (Extensions. csproj). Consente di aggiungere eventuali nuove associazioni alla versione corrente, ma non aggiorna le associazioni esistenti. Usare l'opzione `--force` per aggiornare le associazioni esistenti alla versione più recente quando si installano quelle nuove.

#### <a name="install-a-specific-extension"></a>Installare un'estensione specifica

Usare il comando seguente per installare un pacchetto di estensione specifico in una versione specifica, in questo caso l'estensione di archiviazione:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```