---
title: Autenticazione in Azure Data Lake Storage Gen1 tramite Azure Active Directory | Microsoft Docs
description: Informazioni su come eseguire l'autenticazione con Azure Data Lake Storage Gen1 tramite Azure Active Directory
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
ms.openlocfilehash: f83cf183bee930dd07c707b0eb49125cecd70b84
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193595"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Autenticazione con Azure Data Lake Storage Gen1 tramite Azure Active Directory

Per eseguire l'autenticazione, Azure Data Lake Storage Gen1 usa Azure Active Directory. Prima di creare un'applicazione che funzioni con Data Lake Storage Gen1, è necessario stabilire la modalità di autenticazione dell'applicazione stessa con Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Opzioni di autenticazione

* **Autenticazione dell'utente finale**: per l'autenticazione con Data Lake Storage Gen1 vengono usate le credenziali di Azure dell'utente finale. L'applicazione creata per l'utilizzo di Data Lake Storage Gen1 richiede queste credenziali utente. Di conseguenza questo meccanismo di autenticazione è *interattivo* e l'applicazione viene eseguita nel contesto dell'utente connesso. Per altre informazioni e istruzioni, vedere [Autenticazione dell'utente finale con Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Autenticazione da servizio a servizio**: usare questa opzione per fare in modo che un'applicazione esegua automaticamente l'autenticazione con Data Lake Storage Gen1. In questi casi, per eseguire l'autenticazione con Data Lake Storage Gen1 si crea un'applicazione Azure Active Directory (AD) e si usa la chiave specificata dall'applicazione Azure AD. Di conseguenza questo meccanismo di autenticazione *non è interattivo*. Per altre informazioni e istruzioni, vedere [Autenticazione da servizio a servizio per Data Lake Storage Gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md).

La tabella seguente illustra come vengono supportati i meccanismi di autenticazione dell'utente finale e da servizio a servizio per Data Lake Storage Gen1. Di seguito sono riportate alcune indicazioni per interpretare correttamente la tabella.

* Il simbolo ✔* indica che l'opzione di autenticazione è supportata e si collega a un articolo che illustra come usare l'opzione di autenticazione. 
* Il simbolo ✔ indica che l'opzione di autenticazione è supportata. 
* Le celle vuote indicano che l'opzione di autenticazione non è supportata.


|Usare questa opzione di autenticazione con...                   |.NET         |Java     |PowerShell |Interfaccia della riga di comando di Azure | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Utente finale (senza autenticazione a più fattori**)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)** (deprecato)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Utente finale (con autenticazione a più fattori**)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Da servizio a servizio (usando la chiave client)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Da servizio a servizio (usando il certificato client) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Fare clic sul simbolo <b>✔\*</b>. È un collegamento.</i><br>
<i>** MFA sta per autenticazione a più fattori</i>

Per altre informazioni su come usare Azure Active Directory per l'autenticazione, vedere [Scenari di autenticazione per Azure AD](../active-directory/develop/authentication-scenarios.md).

## <a name="next-steps"></a>Passaggi successivi

* [Autenticazione dell'utente finale](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Autenticazione da servizio a servizio](data-lake-store-service-to-service-authenticate-using-active-directory.md)


