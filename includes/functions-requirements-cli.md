---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a84f0a92703d1b626710cfc4dcfa2820bc58bda6
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673149"
---
## <a name="configure-your-local-environment"></a>Configurare l'ambiente locale

Per eseguire le procedure descritte è necessario:

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) versione 2.7.1846 o una versione 2.x successiva.
::: zone-end  
::: zone pivot="programming-language-python"
+ Python 3.6 e 3.7 richiedono [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) versione 2.7.1846 o una versione 2.x successiva. Python 3.8 richiede la [versione 3.x](../articles/azure-functions/functions-run-local.md#v2) di Core Tools.
::: zone-end

+ L'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) versione 2.0.76 o successiva. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), versioni Active LTS e Maintenance LTS (8.11.1 e 10.14.1 consigliate).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64 bit)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64 bit)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64 bit)](https://www.python.org/downloads/release/python-368/), supportati da Funzioni di Azure. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2+](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](https://aka.ms/azure-jdks), versione 8

+ [Apache Maven](https://maven.apache.org), versione 3.0 o successiva.

> [!IMPORTANT]
> Per completare questa guida introduttiva, è necessario impostare la variabile di ambiente JAVA_HOME sul percorso di installazione di JDK.
::: zone-end