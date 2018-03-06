---
title: Esempi di modelli di Azure Resource Manager - Servizio app | Microsoft Docs
description: Esempi di modelli di Azure Resource Manager - Servizio app
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
editor: ggailey777
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 02/26/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: e28a27b9a00164fcbba6eb5d3e5435548486ffa4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="azure-resource-manager-templates-for-azure-web-apps"></a>Modelli di Azure Resource Manager per app Web di Azure

La tabella seguente include collegamenti ai modelli di Azure Resource Manager. Per consigli su come evitare errori comuni quando si creano modelli di app Web, vedere [Linee guida per la distribuzione di app Web con i modelli di Azure Resource Manager](web-sites-rm-template-guidance.md).

| | |
|-|-|
|**Distribuire un'app Web**||
| [App Web collegata a un repository GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Distribuisce un'app Web di Azure che esegue il pull del codice da GitHub. |
| [App Web con slot di distribuzione personalizzati](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Distribuisce un'app Web con slot/ambienti di distribuzione personalizzati. |
|**Configurare l'app Web**||
| [Certificato dell'app Web dall'insieme di credenziali delle chiavi ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Distribuisce il certificato di un'app Web dal segreto di Key Vault e lo usa per il binding SSL. |
| [App Web con dominio personalizzato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Distribuisce un'app Web di Azure con un nome host personalizzato. |
| [App Web con dominio personalizzato e SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Distribuisce un'app Web di Azure con un nome host personalizzato e ottiene il certificato dell'app Web da Key Vault per il binding SSL. |
| [App Web con l'estensione GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Distribuisce un'app Web di Azure con l'estensione sito Golang, che consente di eseguire applicazioni Web sviluppate in Golang in Azure. |
| [App Web con Java 8 e Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Distribuisce un'app Web di Azure con Java 8 e Tomcat 8 abilitati, per eseguire applicazioni Web Java in Azure. |
|**App Web Linux**||
| [App Web in Linux con MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Distribuisce un'app Web in Linux con Database di Azure per MySQL. |
| [App Web in Linux con PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Distribuisce un'app Web in Linux con Database di Azure per PostgreSQL. |
|**App Web con risorse collegate**||
| [App Web con MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Distribuisce un'app Web in Windows con Database di Azure per MySQL. |
| [App Web con PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Distribuisce un'app Web in Windows con Database di Azure per PostgreSQL. |
| [App Web con un database SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Distribuisce un'app Web di Azure e un database SQL al livello di servizio "Basic". |
| [App Web con connessione all'archivio BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Distribuisce un'app Web con una stringa di connessione all'archivio BLOB, per usare l'archivio BLOB di Azure dall'app Web. |
| [App Web con Cache Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Distribuisce un'app Web di Azure con Cache Redis. |
|**Ambiente del servizio app**||
| [Creare un ambiente del servizio app versione 2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Crea un ambiente del servizio app versione 2 nella rete virtuale. |
| [Creare un ambiente del servizio app versione 2 con un indirizzo di bilanciamento del carico interno](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Crea un ambiente del servizio app versione 2 nella rete virtuale con un indirizzo di bilanciamento del carico interno privato. |
| [Configurare il certificato SSL predefinito per un ambiente del servizio app con bilanciamento del carico interno o un ambiente del servizio app con bilanciamento del carico interno versione 2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Configura il certificato SSL predefinito per un ambiente del servizio app con bilanciamento del carico interno o un ambiente del servizio app con bilanciamento del carico interno versione 2 |
| | |
