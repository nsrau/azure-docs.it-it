---
ms.openlocfilehash: 1dd9e377a88d8b35cc5ad43f3e8dd1c48dc35d7c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78201032"
---
::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>Registrare le estensioni delle associazioni

Ad eccezione dei trigger HTTP e timer, i binding vengono implementati come pacchetti di estensione. Eseguire il comando [dotnet add package](/dotnet/core/tools/dotnet-add-package) seguente nella finestra del terminale per aggiungere il pacchetto di estensione di archiviazione nel progetto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Ora è possibile aggiungere il binding di output di archiviazione nel progetto.  
::: zone-end  