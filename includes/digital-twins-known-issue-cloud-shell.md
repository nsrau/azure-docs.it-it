---
author: baanders
description: file di inclusione per Gemelli digitali di Azure - cita un problema noto con l'autenticazione di Cloud Shell
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: bd3a192ed493a886da427cb6d89239a4eb8e4d17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89290099"
---
>[!NOTE]
>Esiste attualmente un **problema noto** in Cloud Shell che interessa questi gruppi di comandi: `az dt route`, `az dt model`, `az dt twin`.
>
>Per risolverlo, eseguire `az login` in Cloud Shell prima di eseguire il comando oppure usare l'[interfaccia della riga di comando locale](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) anziché Cloud Shell. Per informazioni dettagliate, vedere [*Risoluzione dei problemi: problemi noti in Gemelli digitali di Azure*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).