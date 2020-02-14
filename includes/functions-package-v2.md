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
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77205724"
---
Aggiungere il supporto in un ambiente di sviluppo preferito usando i metodi seguenti.

| Ambiente di sviluppo  | Tipo di applicazione      | Per aggiungere il supporto |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C#libreria di classi      | [Installare il pacchetto NuGet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Basato sugli [strumenti principali](../articles/azure-functions/functions-run-local.md) | [Registrare il bundle dell'estensione](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Si consiglia di installare l' [estensione degli strumenti di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) . |
| Qualsiasi altro editor/IDE     | Basato sugli [strumenti principali](../articles/azure-functions/functions-run-local.md) | [Registrare il bundle dell'estensione](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Portale di Azure             | Online solo nel portale | Viene installato quando si aggiunge un'associazione<br /><br /> Vedere [aggiornare le estensioni](../articles/azure-functions/install-update-binding-extensions-manual.md) per aggiornare le estensioni di binding esistenti senza dover ripubblicare l'app per le funzioni. |
