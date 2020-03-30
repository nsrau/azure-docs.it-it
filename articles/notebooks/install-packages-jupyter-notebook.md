---
title: Installare pacchetti nei blocchi appunti di Jupyter - Azure Notebook PreviewInstall packages in Jupyter notebooks - Azure Notebooks Preview
description: Informazioni su come installare i pacchetti Python, R e F all'interno di un blocco appunti di Jupyter in esecuzione in Azure.Learn how to install Python, R, and F ' packages from within a Jupyter notebook running on Azure.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: f6359b4c010834ffaee00b6208d309997339f36e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646229"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Installare pacchetti da Azure NotebookS Preview

Sebbene sia possibile configurare l'[ambiente per il notebook a livello di progetto](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), è possibile installare i pacchetti direttamente all'interno di un notebook singolo.

I pacchetti installati dal notebook si applicano solo alla sessione del server corrente. Le installazioni di un pacchetto non sono persistenti dopo che il server è stato arrestato.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="python"></a>Python

I pacchetti in Python possono essere installati tramite pip o conda usando i comandi all'interno delle celle di codice:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Se l'output del comando indica che il requisito è già soddisfatto, Azure Notebooks può includere il pacchetto per impostazione predefinita. È possibile installare il pacchetto anche tramite un [passaggio di installazione dell'ambiente del progetto](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

I pacchetti in R possono essere installati da CRAN o GitHub tramite la funzione `install.packages` in una cella di codice:

```r
install.packages("package_name")
```

È anche possibile installare altri pacchetti di sviluppo e versioni non definitive da GitHub tramite la raccolta devtools:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

I pacchetti in F# possono essere installati da [nuget.org](https://www.nuget.org) chiamando il manager delle dipendenze Paket dall'interno delle celle di codice. In primo luogo, caricare il manager Paket:

```fsharp
#load "Paket.fsx"
```

Installare quindi i pacchetti:

```fsharp
Paket.Package
  [ "MathNet.Numerics"
    "MathNet.Numerics.FSharp"
  ]
```

Quindi caricare il generatore Paket:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Aprire la libreria:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Passaggi successivi

- [Procedura: Configurare e gestire i progetti](configure-manage-azure-notebooks-projects.md)
- [Procedura: presentare una presentazione](present-jupyter-notebooks-slideshow.md)
