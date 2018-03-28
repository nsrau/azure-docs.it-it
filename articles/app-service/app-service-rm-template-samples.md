---
title: Esempi di modelli di Azure Resource Manager - Servizio app | Microsoft Docs
description: Esempi di modelli di Azure Resource Manager per la funzionalità App Web del servizio app
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
ms.openlocfilehash: 155b47fc4c664701ec0f21bdc5ae34f3d7f666ff
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="azure-resource-manager-templates-for-web-apps"></a>Modelli di Azure Resource Manager per app Web

La tabella seguente contiene collegamenti ai modelli di Azure Resource Manager per la funzionalità App Web del servizio app di Azure. Per consigli su come evitare errori comuni quando si creano modelli di app Web, vedere [Linee guida per la distribuzione di app Web con i modelli di Azure Resource Manager](web-sites-rm-template-guidance.md).

| | |
|-|-|
|**Distribuzione di un'app Web**||
| [App Web collegata a un repository GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Distribuisce un'app Web di Azure che esegue il pull del codice da GitHub. |
| [App Web con slot di distribuzione personalizzati](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Distribuisce un'app Web con slot/ambienti di distribuzione personalizzati. |
|**Configurazione di un'app Web**||
| [Certificato dell'app Web da Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Distribuisce il certificato di un'app Web da un segreto di Azure Key Vault e lo usa per il binding SSL. |
| [App Web con un dominio personalizzato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Distribuisce un'app Web di Azure con un nome host personalizzato. |
| [App Web con un dominio personalizzato e SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Distribuisce un'app Web di Azure con un nome host personalizzato e ottiene un certificato dell'app Web da Key Vault per il binding SSL. |
| [App Web con un'estensione GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Distribuisce un'app Web di Azure con l'estensione del sito GoLang. È quindi possibile eseguire applicazioni Web sviluppate in GoLang in Azure. |
| [App Web con Java 8 e Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Distribuisce un'app Web di Azure con Java 8 e Tomcat 8 abilitati. È quindi possibile eseguire applicazioni Java in Azure. |
|**App Web Linux**||
| [App Web in Linux con MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Distribuisce un'app Web di Azure in Linux con Database di Azure per MySQL. |
| [App Web in Linux con PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Distribuisce un'app Web di Azure in Linux con Database di Azure per PostgreSQL. |
|**App Web con risorse connesse**||
| [App Web con MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Distribuisce un'app Web di Azure in Windows con Database di Azure per MySQL. |
| [App Web con PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Distribuisce un'app Web di Azure in Windows con Database di Azure per PostgreSQL. |
| [App Web con un database SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Distribuisce un'app Web di Azure e un database SQL al livello di servizio Basic. |
| [App Web con connessione a un archivio BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Distribuisce un'app Web di Azure con una stringa di connessione a un archivio BLOB di Azure. È quindi possibile usare l'archivio BLOB dall'app Web. |
| [App Web con una cache Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Distribuisce un'app Web di Azure con una cache Redis. |
|**Ambiente del servizio app per PowerApps**||
| [Creare un ambiente del servizio app versione 2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Crea un ambiente del servizio app versione 2 nella rete virtuale. |
| [Creare un ambiente del servizio app versione 2 con un indirizzo ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Crea un ambiente del servizio app versione 2 nella rete virtuale con un indirizzo di bilanciamento del carico interno privato. |
| [Configurare il certificato SSL predefinito per un ambiente del servizio app ILB o un ambiente del servizio app ILB versione 2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Configura il certificato SSL predefinito per un ambiente del servizio app ILB o un ambiente del servizio app ILB versione 2. |
| | |
