---
title: 'Autenticazione dell''utente finale: Java con Data Lake Store tramite Azure Active Directory | Microsoft Docs'
description: Informazioni su come ottenere l'autenticazione dell'utente finale con Data Lake Store tramite Azure Active Directory con Java
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
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: b1fbf3c1e0e5715ff7effcdac45ff03f2b55f9ee
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="end-user-authentication-with-data-lake-store-using-java"></a>Autenticazione dell'utente finale con Data Lake Store tramite Java
> [!div class="op_single_selector"]
> * [Uso di Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Uso di .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Uso di Python](data-lake-store-end-user-authenticate-python.md)
> * [Uso dell'API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

Questo articolo illustra come usare Java SDK per eseguire l'autenticazione dell'utente finale con Azure Data Lake Store. Per l'autenticazione da servizio a servizio con Data Lake Store tramite Java SDK, vedere [Autenticazione da servizio a servizio con Data Lake Store tramite Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>Prerequisiti
* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Creare un'applicazione "nativa" di Azure Active Directory**. È necessario avere completato i passaggi descritti in [Autenticazione dell'utente finale con Data Lake Store usando Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Questa esercitazione usa Maven per compilare e progettare le dipendenze. Sebbene sia possibile compilare senza ricorrere a un sistema di compilazione come Maven o Gradle, questi sistemi semplificano la gestione delle dipendenze.

* (Facoltativo) E IDE come [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) oppure [Eclipse](https://www.eclipse.org/downloads/) o simili.

## <a name="end-user-authentication"></a>Autenticazione dell'utente finale
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
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. Usare il frammento di codice seguente nell'applicazione Java per ottenere token per l'applicazione nativa di Active Directory creata in precedenza usando `DeviceCodeTokenProvider`. Sostituire **FILL-IN-HERE** (COMPLETARE QUI) con i valori effettivi per l'applicazione nativa di Azure Active Directory.

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

Data Lake Store SDK mette a disposizione metodi pratici che consentono di gestire i token di sicurezza necessari per comunicare con l'account Data Lake Store. Tuttavia, l'SDK non implica l'uso esclusivo di questi metodi. È possibile usare qualsiasi altro modo per ottenere i token, come l'[SDK di Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) o il proprio codice personalizzato.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come usare l'autenticazione dell'utente finale per eseguire l'autenticazione con Azure Data Lake Store usando Java SDK. È ora possibile leggere gli articoli seguenti per informazioni su come usare Java SDK in Azure Data Lake Store.

* [Operazioni dati in Data Lake Store con Java SDK](data-lake-store-get-started-java-sdk.md)


