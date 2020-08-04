---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 3f5c7021461b407e90d61ed045c112aab3c1c4e9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87055992"
---
## <a name="configure-your-local-environment"></a>Configurare l'ambiente locale

Per eseguire le procedure descritte è necessario:

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) versione 2.7.1846 o una versione 2.x successiva.
::: zone-end  
::: zone pivot="programming-language-python"
+ La versione di Azure Functions Core Tools che corrisponde alla versione di Python installata:

   | Versione Python | Versione Core Tools |
   | -------------- | ------------------ |
   | Python 3.8     | [versione 3.x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3.6<br/>Python 3.7 | [Versione 2.7.1846 o versioni successive](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ L'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) versione 2.4 o successiva. 
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

    > [!IMPORTANT]
    > + Il supporto delle funzioni per Java 11 è attualmente in anteprima e l'archetipo Maven crea una distribuzione di Java 8 per impostazione predefinita. Se si vuole invece eseguire l'app per le funzioni in Java 11, è necessario aggiornare manualmente il file pom.xml con i valori di Java 11. Per altre informazioni, vedere [Versioni di Java](../articles/azure-functions/functions-reference-java.md#java-versions). 
    > + Per completare questa guida di avvio rapido, è necessario impostare la variabile di ambiente `JAVA_HOME` sul percorso di installazione della versione corretta di JDK.

+ [Apache Maven](https://maven.apache.org), versione 3.0 o successiva.

::: zone-end
