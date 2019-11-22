---
title: Installare un pacchetto in un notebook di Jupyter in Azure
description: Come installare un pacchetto Python, R o F# da un notebook di Jupyter in esecuzione in Azure.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: 5d85c8e936ce7c8bf38ec7bc9c27d9066cc8b155
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277541"
---
# <a name="install-packages-from-within-a-notebook"></a>Installare pacchetti da un notebook

Sebbene sia possibile configurare l'[ambiente per il notebook a livello di progetto](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), è possibile installare i pacchetti direttamente all'interno di un notebook singolo.

I pacchetti installati dal notebook si applicano solo alla sessione del server corrente. Le installazioni di un pacchetto non sono persistenti dopo che il server è stato arrestato.

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

Caricare quindi il generatore Paket:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Aprire libray:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Passaggi successivi

- [Procedura: configurare e gestire i progetti](configure-manage-azure-notebooks-projects.md)
- [Procedura: presentare una presentazione](present-jupyter-notebooks-slideshow.md)
