---
title: Librerie client per Azure Key Vault | Microsoft Docs
description: Librerie client per Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 9cc8c1e9406f6b92a445cd52a4ad26e71d83ef5a
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263784"
---
# <a name="client-libraries-for-azure-key-vault"></a>Librerie client per Azure Key Vault

Le librerie client per Azure Key Vault consentono l'accesso a livello di codice alle funzionalità di Key Vault da un'ampia gamma di linguaggi, tra cui .NET, Python, Java e JavaScript.

## <a name="client-libraries-per-language-and-object"></a>Librerie client per linguaggio e oggetto

Ogni SDK dispone di librerie client distinte per i segreti, le chiavi e i certificati, in base alla tabella riportata di seguito.

| Linguaggio | Segreti | Chiavi | Certificati |
|--|--|--|--|
| .NET | - [Informazioni di riferimento sulle API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [Pacchetto NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Avvio rapido](../secrets/quick-create-net.md) | - [Informazioni di riferimento sulle API](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [Pacchetto NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [Informazioni di riferimento sulle API](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [Pacchetto NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) |
| Python| - [Informazioni di riferimento sulle API](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [Pacchetto PyPi](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Avvio rapido](../secrets/quick-create-python.md) |- [Informazioni di riferimento sulle API](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [Pacchetto PyPi](https://pypi.org/project/azure-keyvault-keys/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Avvio rapido](../keys/quick-create-python.md) | - [Informazioni di riferimento sulle API](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [Pacchetto PyPi](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Avvio rapido](../certificates/quick-create-python.md) |
| Java | - [Informazioni di riferimento sulle API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Avvio rapido](../secrets/quick-create-java.md) |- [Informazioni di riferimento sulle API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [Informazioni di riferimento sulle API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |
| Node.js | - [Informazioni di riferimento sulle API](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [Pacchetto npm](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Avvio rapido](../secrets/quick-create-node.md) |- [Informazioni di riferimento sulle API](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [Pacchetto npm](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [Informazioni di riferimento sulle API](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [Pacchetto npm](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |

## <a name="next-steps"></a>Passaggi successivi

- Vedere la [Guida per gli sviluppatori per Azure Key Vault](developers-guide.md)
- Altre informazioni sull'[identità gestita per Azure Key Vault](managed-identity.md)