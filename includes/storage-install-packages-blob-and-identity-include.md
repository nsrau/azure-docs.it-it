---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806584"
---
## <a name="install-client-library-packages"></a>Installare i pacchetti della libreria clientInstall client library packages

> [!NOTE]
> Gli esempi illustrati di seguito usano la libreria client di Archiviazione di Azure versione 12.The examples shown here use the Azure Storage client library version 12. La libreria client versione 12 fa parte di Azure SDK. Per altre informazioni su Azure SDK, vedere il repository di Azure SDK in [GitHub.](https://github.com/Azure/azure-sdk)

Per installare il pacchetto di archiviazione BLOB, eseguire il comando seguente dalla console di gestione pacchetti NuGet:To install the Blob storage package, run the following command from the NuGet package manager console:

```powershell
Install-Package Azure.Storage.Blobs
```

Gli esempi illustrati di seguito usano anche la versione più recente della [libreria client di Azure Identity per .NET](https://www.nuget.org/packages/Azure.Identity/) per eseguire l'autenticazione con le credenziali di Azure AD. Per installare il pacchetto, eseguire il comando seguente dalla console di gestione pacchetti NuGet:

```powershell
Install-Package Azure.Identity
```
