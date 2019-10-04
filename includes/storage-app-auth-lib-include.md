---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5e605e4ad987db16b98649b32dc96fa1620b1564
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011989"
---
Il Microsoft Azure libreria client di [autenticazione app](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) per .NET (anteprima) semplifica il processo di acquisizione e rinnovo di un token dal codice. La libreria client di autenticazione app gestisce automaticamente l'autenticazione. La libreria usa le credenziali dello sviluppatore per l'autenticazione durante lo sviluppo locale. L'utilizzo delle credenziali per lo sviluppatore durante lo sviluppo locale è più sicuro perché non è necessario creare credenziali di Azure AD o condividere le credenziali tra gli sviluppatori. Quando la soluzione viene distribuita in un secondo momento in Azure, la libreria passa automaticamente a usando le credenziali dell'applicazione.

Per usare la libreria di autenticazione app in un'applicazione di archiviazione di Azure, installare il pacchetto di anteprima più recente da [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication), nonché la versione più recente della [libreria client comune di archiviazione di Azure per .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) e la [libreria client di archiviazione BLOB di Azure. per .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). Aggiungere le istruzioni **using** seguenti al codice:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```
