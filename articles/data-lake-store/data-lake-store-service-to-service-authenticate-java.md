---
title: 'Autenticazione da servizio a: Java con Azure Data Lake archiviazione Gen1 usando Azure Active Directory | Microsoft Docs'
description: Informazioni su come ottenere l'autenticazione da servizio a servizio con Azure Data Lake Storage Gen1 usando Azure Active Directory con Java
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c32eada2acca73e089c2296ce8e59c529d7af665
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60194995"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Autenticazione da servizio a servizio con Azure Data Lake Storage Gen1 tramite Java
> [!div class="op_single_selector"]
> * [Uso di Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Uso di .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Uso di Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Uso dell'API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Questo articolo illustra come usare Java SDK per eseguire l'autenticazione da servizio a servizio con Azure Data Lake Storage Gen1. L'autenticazione dell'utente finale con Data Lake Storage Gen1 tramite Java SDK non è supportata.

## <a name="prerequisites"></a>Prerequisiti
* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Creare un'applicazione "Web" di Azure Active Directory**. È necessario avere completato i passaggi descritti in [Autenticazione da servizio a servizio con Data Lake Storage Gen1 tramite Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Questa esercitazione usa Maven per compilare e progettare le dipendenze. Sebbene sia possibile compilare senza ricorrere a un sistema di compilazione come Maven o Gradle, questi sistemi semplificano la gestione delle dipendenze.

* (Facoltativo) Un IDE come [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) oppure [Eclipse](https://www.eclipse.org/downloads/) o simili.

## <a name="service-to-service-authentication"></a>Autenticazione da servizio a servizio
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
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. Usare il frammento di codice seguente nell'applicazione Java per ottenere il token per l'applicazione Web di Active Directory creata in precedenza usando una delle sottoclassi di `AccessTokenProvider`. L'esempio seguente usa `ClientCredsTokenProvider`. Il provider di token memorizza nella cache le credenziali usate per ottenere il token in memoria e rinnova automaticamente il token se sta per scadere. È possibile creare altre sottoclassi di `AccessTokenProvider` in modo che i token vengano ottenuti con il codice cliente. Per il momento verrà usata quella fornita con il SDK.

    Sostituire **FILL-IN-HERE** (COMPLETARE QUI) con i valori effettivi per l'applicazione Web di Azure Active Directory.

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

Data Lake Storage Gen1 SDK offre metodi pratici che consentono di gestire i token di sicurezza necessari per comunicare con l'account Data Lake Storage Gen1. Tuttavia, l'SDK non implica l'uso esclusivo di questi metodi. È possibile usare qualsiasi altro modo per ottenere i token, come l'[SDK di Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) o il proprio codice personalizzato.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come usare l'autenticazione dell'utente finale per eseguire l'autenticazione con Data Lake Storage Gen1 usando Java SDK. È ora possibile leggere gli articoli seguenti per informazioni su come usare Java SDK in Data Lake Storage Gen1.

* [Operazioni dati in Data Lake Storage Gen1 con Java SDK](data-lake-store-get-started-java-sdk.md)


