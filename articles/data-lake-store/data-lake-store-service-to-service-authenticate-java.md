---
title: 'Autenticazione da servizio a servizio: Java con Data Lake Store tramite Azure Active Directory | Microsoft Docs'
description: Informazioni su come ottenere l'autenticazione da servizio a servizio con Data Lake Store tramite Azure Active Directory con Java
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/29/2017
ms.author: nitinme
ms.openlocfilehash: f68239b2f4f7a2ba0617023d9397184c483a4d99
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-java"></a>Autenticazione da servizio a servizio con Data Lake Store tramite Java
> [!div class="op_single_selector"]
> * [Uso di Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Uso di .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Uso di Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Uso dell'API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Questo articolo illustra come usare Java SDK per eseguire l'autenticazione da servizio a servizio con Azure Data Lake Store. L'autenticazione dell'utente finale con Data Lake Store tramite Java SDK non è supportata.

## <a name="prerequisites"></a>Prerequisiti
* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Creare un'applicazione "Web" di Azure Active Directory**. È necessario avere completato i passaggi descritti in [Autenticazione da servizio a servizio con Data Lake Store tramite Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Questa esercitazione usa Maven per compilare e progettare le dipendenze. Sebbene sia possibile compilare senza ricorrere a un sistema di compilazione come Maven o Gradle, questi sistemi semplificano la gestione delle dipendenze.

* (Facoltativo) E IDE come [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) oppure [Eclipse](https://www.eclipse.org/downloads/) o simili.

## <a name="service-to-service-authentication"></a>Autenticazione da servizio a servizio
1. Creare un progetto Maven usando l'[archetipo mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) dalla riga di comando o con un IDE. Per istruzioni su come creare un progetto Java usando IntelliJ, vedere [qui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Per istruzioni su come creare un progetto Java usando Eclipse, vedere [qui](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

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
   
    La prima dipendenza serve a usare l'SDK di Data Lake Store (`azure-data-lake-store-sdk`) dal repository di Maven. La seconda dipendenza serve a specificare il framework di registrazione (`slf4j-nop`) da usare per questa applicazione. L'SDK di Data Lake Store usa la façade di registrazione [SLF4J](http://www.slf4j.org/), che consente di scegliere tra un numero di framework di registrazione comuni, come SLF4J, registrazione Java, logback e così via, o nessuna registrazione. Per questo esempio la registrazione viene disabilitata, quindi si usa il binding **slf4j-nop**. Per usare altre opzioni di registrazione nell'applicazione, vedere [qui](http://www.slf4j.org/manual.html#projectDep).

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

Data Lake Store SDK mette a disposizione metodi pratici che consentono di gestire i token di sicurezza necessari per comunicare con l'account Data Lake Store. Tuttavia, l'SDK non implica l'uso esclusivo di questi metodi. È possibile usare qualsiasi altro modo per ottenere i token, come l'[SDK di Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) o il proprio codice personalizzato.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come usare l'autenticazione dell'utente finale per eseguire l'autenticazione con Azure Data Lake Store usando Java SDK. È ora possibile leggere gli articoli seguenti per informazioni su come usare Java SDK in Azure Data Lake Store.

* [Operazioni dati in Data Lake Store con Java SDK](data-lake-store-get-started-java-sdk.md)


