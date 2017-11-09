---
title: Autenticazione in Data Lake Store usando Azure Active Directory | Microsoft Docs
description: Informazioni su come eseguire l'autenticazione con Data Lake Store usando Azure Active Directory
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
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 6cbdda98c079d25c9cce1342c79dea6e66aec3ad
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2017
---
# <a name="authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticazione con Data Lake Store usando Azure Active Directory

Azure Data Lake Store usa Azure Active Directory per l'autenticazione. Prima di creare un'applicazione che funzioni con Azure Data Lake Store, è necessario stabilire come autenticare l'applicazione con Azure Active Directory (Azure AD). 

## <a name="authentication-options"></a>Opzioni di autenticazione

* **Autenticazione dell'utente finale**: le credenziali Azure dell'utente finale vengono usate per l'autenticazione con Data Lake Store. L'applicazione creata per l'utilizzo di Data Lake Store richiede queste credenziali utente. Di conseguenza questo meccanismo di autenticazione è *interattivo* e l'applicazione viene eseguita nel contesto dell'utente connesso. Per altre informazioni e istruzioni, vedere [Autenticazione dell'utente finale per Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Autenticazione da servizio a servizio**: usare questa opzione per fare in modo che un'applicazione esegua automaticamente l'autenticazione con Data Lake Store. In questi casi si crea un'applicazione Azure Active Directory (AD) e si usa la chiave fornita dall'applicazione Azure AD per l'autenticazione con Data Lake Store. Di conseguenza questo meccanismo di autenticazione *non è interattivo*. Per altre informazioni e istruzioni, vedere [Autenticazione da servizio a servizio per Data Lake Store](data-lake-store-service-to-service-authenticate-using-active-directory.md).

La tabella seguente illustra come vengono supportati i meccanismi di autenticazione dell'utente finale e da servizio a servizio per Data Lake Store. Di seguito sono riportate alcune indicazioni per interpretare correttamente la tabella.

* Il simbolo ✔* indica che l'opzione di autenticazione è supportata e contiene il collegamento a un articolo che illustra come usare l'opzione di autenticazione. 
* Il simbolo ✔ indica che l'opzione di autenticazione è supportata. 
* Le celle vuote indicano che l'opzione di autenticazione non è supportata.


|Usare questa opzione di autenticazione con...                   |.NET         |Java     |PowerShell |Interfaccia della riga di comando 2.0 | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Utente finale (senza autenticazione a più fattori**)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(deprecata)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Utente finale (con autenticazione a più fattori**)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Da servizio a servizio (usando la chiave client)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Da servizio a servizio (usando il certificato client) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Fare clic sul simbolo <b>✔\* </b>. È un collegamento.</i><br>
<i>** MFA sta per autenticazione a più fattori</i>

Per altre informazioni su come usare Azure Active Directory per l'autenticazione, vedere [Scenari di autenticazione per Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).

## <a name="next-steps"></a>Passaggi successivi

* [Autenticazione dell'utente finale](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Autenticazione da servizio a servizio](data-lake-store-service-to-service-authenticate-using-active-directory.md)


