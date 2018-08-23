---
title: Modifiche apportate al progetto ASP.NET Core con la connessione ad Azure Key Vault | Microsoft Docs
description: L'articolo descrive l'impatto della connessione a Key Vault con Servizi connessi di Visual Studio su un progetto ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: b7cbe55fa3a524965e0ebc16c5ff350a60d6e440
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2018
ms.locfileid: "42145443"
---
# <a name="what-happened-to-my-aspnet-core-project-visual-studio-key-vault-connected-service"></a>Modifiche apportate al progetto ASP.NET Core (Servizio connesso Key Vault con Visual Studio)

> [!div class="op_single_selector"]
> - [Per iniziare](vs-key-vault-aspnet-core-get-started.md)
> - [Risultati](vs-key-vault-aspnet-core-what-happened.md)

Questo articolo identifica le modifiche esatte apportate a un progetto ASP.NET quando si aggiunge il [servizio connesso Key Vault con Visual Studio](vs-key-vault-add-connected-service.md).

Per informazioni sull'uso del servizio connesso, vedere l'[introduzione](vs-key-vault-aspnet-core-get-started.md).

## <a name="added-references"></a>Riferimenti aggiunti

Interessa il file di progetto (riferimenti *.NET) e (riferimenti al pacchetto NuGet).

| type | riferimento |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

## <a name="added-files"></a>File aggiunti

- È stato aggiunto il file ConnectedService.json che registra alcune informazioni su provider del servizio connesso, versione e un collegamento alla documentazione.

## <a name="project-file-changes"></a>Modifiche al file di progetto

- Sono stati aggiunti il file ConnectedServices.json e ItemGroup di Servizi connessi.

## <a name="launchsettingsjson-changes"></a>Modifiche a launchsettings.JSON

- Aggiungere le seguenti voci di variabili di ambiente sia al profilo IIS Express sia al profilo che corrisponde al nome del progetto Web:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

## <a name="changes-on-azure"></a>Modifiche in Azure

- È stato creato un gruppo di risorse o ne è stato usato uno esistente.
- È stato creato un insieme di credenziali nel gruppo di risorse specificato.

