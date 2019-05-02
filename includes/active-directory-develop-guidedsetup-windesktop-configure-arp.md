---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 5c2bce5635dfe488c1725efdd2954ecd81cf8e79
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947397"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Aggiungere le informazioni di registrazione dell'applicazione all'app

In questo passaggio è necessario aggiungere l'ID applicazione al progetto.

1. Aprire `App.xaml.cs` e sostituire la riga contenente il `ClientId` con:

```csharp
private static string ClientId = "[Enter the application Id here]";
```
