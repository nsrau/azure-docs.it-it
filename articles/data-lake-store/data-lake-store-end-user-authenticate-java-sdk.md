---
title: "Autenticazione dell'utente finale: Java con Azure Data Lake archiviazione Gen1 usando Azure Active Directory | Microsoft Docs"
description: Informazioni su come ottenere l'autenticazione dell'utente finale con Azure Data Lake Storage Gen1 tramite Java
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: d2c0cc277b2115f50455a1caa825306cc173d94c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57538987"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Autenticazione dell'utente finale con Azure Data Lake Storage Gen1 tramite Java
> [!div class="op_single_selector"]
> * [Uso di Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Uso di .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Uso di Python](data-lake-store-end-user-authenticate-python.md)
> * [Uso dell'API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

Questo articolo illustra come usare Java SDK per eseguire l'autenticazione dell'utente finale con Azure Data Lake Storage Gen1. Per l'autenticazione da servizio a servizio con Data Lake Storage Gen1 tramite Java SDK, vedere [Autenticazione da servizio a servizio con Data Lake Storage Gen1 tramite Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>Prerequisiti
* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Creare un'applicazione "nativa" di Azure Active Directory**. È necessario avere completato i passaggi descritti in [Autenticazione dell'utente finale con Data Lake Storage Gen1 usando Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Questa esercitazione usa Maven per compilare e progettare le dipendenze. Sebbene sia possibile compilare senza ricorrere a un sistema di compilazione come Maven o Gradle, questi sistemi semplificano la gestione delle dipendenze.

* (Facoltativo) E IDE come [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) oppure [Eclipse](https://www.eclipse.org/downloads/) o simili.

## <a name="end-user-authentication"></a>Autenticazione dell'utente finale
1. Creare un progetto Maven usando l'[archetipo mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) dalla riga di comando o con un IDE. Per istruzioni su come creare un progetto Java usando IntelliJ, vedere [qui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Per istruzioni su come creare un progetto Java usando Eclipse, vedere [qui](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Aggiungere le dipendenze seguenti al file **pom.xml** di Maven. Aggiungere il frammento di codice seguente prima del tag **\</project>**:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.2.3</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    La prima dipendenza consiste nell'uso di Data Lake Storage Gen1 SDK (`azure-data-lake-store-sdk`) dal repository maven. La seconda dipendenza serve a specificare il framework di registrazione (`slf4j-nop`) da usare per questa applicazione. Data Lake Storage Gen1 SDK usa l'interfaccia di registrazione [slf4j](https://www.slf4j.org/) che consente di scegliere tra diversi framework di registrazione comuni, ad esempio log4j, registrazione con Java, logback e così via o nessuna registrazione. Per questo esempio la registrazione viene disabilitata, quindi si usa il binding **slf4j-nop**. Per usare altre opzioni di registrazione nell'applicazione, vedere [qui](https://www.slf4j.org/manual.html#projectDep).

3. Aggiungere le istruzioni import seguenti all'applicazione.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. Usare il frammento di codice seguente nell'applicazione Java per ottenere token per l'applicazione nativa di Active Directory creata in precedenza usando `DeviceCodeTokenProvider`. Sostituire **FILL-IN-HERE** (COMPLETARE QUI) con i valori effettivi per l'applicazione nativa di Azure Active Directory.

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

Data Lake Storage Gen1 SDK offre metodi pratici che consentono di gestire i token di sicurezza necessari per comunicare con l'account Data Lake Storage Gen1. Tuttavia, l'SDK non implica l'uso esclusivo di questi metodi. È possibile usare qualsiasi altro modo per ottenere i token, come l'[SDK di Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) o il proprio codice personalizzato.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come usare l'autenticazione dell'utente finale per eseguire l'autenticazione con Azure Data Lake Storage Gen1 usando Java SDK. È ora possibile leggere gli articoli seguenti per informazioni su come usare Java SDK in Azure Data Lake Storage Gen1.

* [Operazioni dati in Data Lake Storage Gen1 con Java SDK](data-lake-store-get-started-java-sdk.md)


