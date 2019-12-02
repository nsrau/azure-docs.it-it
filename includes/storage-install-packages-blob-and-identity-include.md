---
title: file di inclusione
description: file di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 0adf1280fa50e9ee594f3025dff70786f5ba2199
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666164"
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

Per altre informazioni su come eseguire l'autenticazione con la libreria client di identità di Azure da archiviazione di Azure, vedere la sezione **eseguire l'autenticazione con la libreria di identità di Azure** in [autorizzare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure](/azure/storage/common/storage-auth-aad-msi?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).