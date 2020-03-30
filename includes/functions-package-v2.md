---
title: File di inclusione
description: File di inclusione
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 2eb82eb8b4a64cbbfef91539f46ba084aa73f207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205724"
---
Aggiungere il supporto nell'ambiente di sviluppo preferito utilizzando i metodi seguenti.

| Ambiente di sviluppo  | Tipo di applicazione      | Per aggiungere il supporto |
|--------------------------|-----------------------|----------------|
| Visual Studio            | Libreria di classi C      | [Installare il pacchetto NuGetInstall the NuGet package](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Sulla base [degli strumenti principali](../articles/azure-functions/functions-run-local.md) | [Registrare il pacchetto di estensioneRegister the extension bundle](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>È consigliabile installare [l'estensione Strumenti](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) di Azure.Installing the Azure Tools extension is recommended. |
| Qualsiasi altro editor/IDE     | Sulla base [degli strumenti principali](../articles/azure-functions/functions-run-local.md) | [Registrare il pacchetto di estensioneRegister the extension bundle](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Portale di Azure             | Online solo nel portale | Installazioni durante l'aggiunta di un'associazione<br /><br /> Vedere [Aggiornare le estensioni](../articles/azure-functions/install-update-binding-extensions-manual.md) per aggiornare le estensioni di associazione esistenti senza dover ripubblicare l'app per le funzioni. |
