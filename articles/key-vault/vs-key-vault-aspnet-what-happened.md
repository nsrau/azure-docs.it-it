---
title: Modifiche apportate al progetto ASP.NET con la connessione ad Azure Key Vault | Microsoft Docs
description: L'articolo descrive l'impatto della connessione a Key Vault con Servizi connessi di Visual Studio su un progetto ASP.NET.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: a15f9c41c5af8803bfb230b19cbbf2f1bdbc2686
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44050690"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-key-vault-connected-service"></a>Modifiche apportate al progetto ASP.NET (Servizio connesso Key Vault con Visual Studio)

> [!div class="op_single_selector"]
> - [Per iniziare](vs-key-vault-aspnet-get-started.md)
> - [Risultati](vs-key-vault-aspnet-what-happened.md)

Questo articolo identifica le modifiche esatte apportate a un progetto ASP.NET quando si aggiunge il [servizio connesso Key Vault con Visual Studio](vs-key-vault-add-connected-service.md).

Per informazioni sull'uso del servizio connesso, vedere l'[introduzione](vs-key-vault-aspnet-get-started.md).

## <a name="added-references"></a>Riferimenti aggiunti

Interessa il file di progetto (riferimenti *.NET) e `packages.config` (riferimenti NuGet).

| type | riferimento |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

## <a name="added-files"></a>File aggiunti

- È stato aggiunto il file ConnectedService.json che registra alcune informazioni su provider del servizio connesso, versione e un collegamento alla documentazione.

## <a name="project-file-changes"></a>Modifiche al file di progetto

- Sono stati aggiunti il file ConnectedServices.json e ItemGroup di Servizi connessi.
- Riferimenti agli assembly .NET descritti nella sezione [Riferimenti aggiunti](#added-references).

## <a name="webconfig-or-appconfig-changes"></a>modifiche apportate al file web.config o app.config

- Sono state aggiunte le seguenti voci di configurazione:

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" 
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add 
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral" 
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

## <a name="changes-on-azure"></a>Modifiche in Azure

- È stato creato un gruppo di risorse o ne è stato usato uno esistente.
- È stato creato un insieme di credenziali nel gruppo di risorse specificato.

