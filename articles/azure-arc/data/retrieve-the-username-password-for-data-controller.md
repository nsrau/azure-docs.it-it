---
title: Recuperare il nome utente e la password per la connessione al controller di dati Arc
description: Recuperare il nome utente e la password per la connessione al controller di dati Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 1c33cf21be0b4b1ea39a568d6df9fd90507dd454
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938955"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>Recuperare il nome utente e la password per la connessione al controller di dati Arc

È possibile che si verifichi una situazione in cui è necessario recuperare il nome utente e la password per il controller di dati. Questi sono i comandi necessari quando si esegue. 

```console
azdata login
```

Se si è l'amministratore di Kubernetes per il cluster. Di conseguenza, si dispone dei privilegi necessari per eseguire i comandi da recuperare dal segreto Kubernetes, in cui sono archiviate le informazioni che Azure Arc rende permanente.

> [!NOTE]
>  Se è stato utilizzato un nome diverso per lo spazio dei nomi in cui è stato creato il controller dati, assicurarsi di modificare il `-n arc` parametro nei comandi seguenti per utilizzare il nome dello spazio dei nomi in cui è stato creato il controller dati.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="linux"></a>Linux

Eseguire il comando seguente per recuperare il nome utente:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

Eseguire il comando seguente per recuperare la password:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

Eseguire il comando seguente per recuperare il nome utente:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

Eseguire il comando seguente per recuperare la password:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>Passaggi successivi

Prova altri [scenari](https://github.com/microsoft/Azure-data-services-on-Azure-Arc/tree/master/scenarios)
