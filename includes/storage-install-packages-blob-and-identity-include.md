---
title: includere il file
description: includere file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74806584"
---
## <a name="install-client-library-packages"></a>Installare i pacchetti della libreria client

> [!NOTE]
> Gli esempi illustrati di seguito usano la libreria client di archiviazione di Azure versione 12. La libreria client versione 12 fa parte di Azure SDK. Per altre informazioni su Azure SDK, vedere il repository di Azure SDK su [GitHub](https://github.com/Azure/azure-sdk).

Per installare il pacchetto di archiviazione BLOB, eseguire il comando seguente dalla console di gestione pacchetti NuGet:

```powershell
Install-Package Azure.Storage.Blobs
```

Gli esempi illustrati di seguito usano anche la versione più recente della [libreria client di identità di Azure per .NET](https://www.nuget.org/packages/Azure.Identity/) per l'autenticazione con le credenziali Azure ad. Per installare il pacchetto, eseguire il comando seguente dalla console di gestione pacchetti NuGet:

```powershell
Install-Package Azure.Identity
```
