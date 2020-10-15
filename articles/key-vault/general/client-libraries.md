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
ms.openlocfilehash: 2c48917e7205bca03380267c1c97469acc245fa6
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628028"
---
# <a name="client-libraries-for-azure-key-vault"></a>Librerie client per Azure Key Vault

Le librerie client per Azure Key Vault consentono l'accesso a livello di codice alle funzionalità di Key Vault da un'ampia gamma di linguaggi, tra cui .NET, Python, Java e JavaScript.

## <a name="client-libraries-per-language-and-object"></a>Librerie client per linguaggio e oggetto

Ogni SDK dispone di librerie client distinte per l'insieme di credenziali delle chiavi, i segreti, le chiavi e i certificati, in base alla tabella riportata di seguito.

| Linguaggio | Segreti | Chiavi | Certificati | Key Vault (piano di gestione)
|--|--|--|--|
| .NET | - [Informazioni di riferimento sulle API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [Pacchetto NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Avvio rapido](../secrets/quick-create-net.md) | - [Informazioni di riferimento sulle API](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [Pacchetto NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [Informazioni di riferimento sulle API](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [Pacchetto NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) | - [Informazioni di riferimento sulle API](/dotnet/api/microsoft.azure.management.keyvault?view=azure-dotnet)<br>- [Pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [Informazioni di riferimento sulle API](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [Pacchetto PyPi](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Avvio rapido](../secrets/quick-create-python.md) |- [Informazioni di riferimento sulle API](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [Pacchetto PyPi](https://pypi.org/project/azure-keyvault-keys/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Avvio rapido](../keys/quick-create-python.md) | - [Informazioni di riferimento sulle API](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [Pacchetto PyPi](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Avvio rapido](../certificates/quick-create-python.md) | - [Informazioni di riferimento sulle API](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault?view=azure-python)<br> - [Pacchetto PyPi](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [Informazioni di riferimento sulle API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Avvio rapido](../secrets/quick-create-java.md) |- [Informazioni di riferimento sulle API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [Informazioni di riferimento sulle API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |- [Informazioni di riferimento sulle API](/java/api/com.microsoft.azure.management.keyvault?view=azure-java-stable)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [Informazioni di riferimento sulle API](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [Pacchetto npm](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Avvio rapido](../secrets/quick-create-node.md) |- [Informazioni di riferimento sulle API](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [Pacchetto npm](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [Informazioni di riferimento sulle API](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [Pacchetto npm](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |  - [Informazioni di riferimento sulle API](/javascript/api/@azure/arm-keyvault/?view=azure-node-latest)<br>- [Pacchetto npm](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>Passaggi successivi

- Vedere la [Guida per gli sviluppatori per Azure Key Vault](developers-guide.md)
- Altre informazioni su come [Eseguire l'autenticazione a un insieme di credenziali delle chiavi](authentication.md)
- Altre informazioni su come [Proteggere l'accesso a un insieme di credenziali delle chiavi](secure-your-key-vault.md)
