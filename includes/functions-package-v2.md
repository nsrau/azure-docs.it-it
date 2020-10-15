---
title: includere file
description: includere file
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 2eb82eb8b4a64cbbfef91539f46ba084aa73f207
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77205724"
---
Per aggiungere il supporto nell'ambiente di sviluppo preferito, usare i metodi seguenti.

| Ambiente di sviluppo  | Tipo di applicazione      | Per aggiungere il supporto |
|--------------------------|-----------------------|----------------|
| Visual Studio            | Libreria di classi C#      | [Installare il pacchetto NuGet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Basato su [Core Tools](../articles/azure-functions/functions-run-local.md) | [Registrare il bundle di estensioni](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>È consigliabile installare l'[estensione Strumenti di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack). |
| Qualsiasi altro editor/IDE     | Basato su [Core Tools](../articles/azure-functions/functions-run-local.md) | [Registrare il bundle di estensioni](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Portale di Azure             | Online solo nel portale | Viene installato quando si aggiunge un'associazione<br /><br /> Per aggiornare le estensioni delle associazioni esistenti senza dover ripubblicare l'app per le funzioni, vedere [Aggiornare le estensioni](../articles/azure-functions/install-update-binding-extensions-manual.md). |
