---
title: Esempi di modelli di Azure Resource Manager
description: Esempi del modello di Azure Resource Manager relativi ad alcuni degli scenari comuni di Servizio app. Informazioni su come automatizzare le attività di gestione o distribuzione di Servizio app.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 58871035eac89931dce0c1cd289cf3fab97c0c3e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688410"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Modelli di Azure Resource Manager per il servizio app

La tabella seguente include collegamenti ai modelli di Azure Resource Manager per il servizio app di Azure. Per consigli su come evitare errori comuni quando si creano modelli di app, vedere [Linee guida per la distribuzione di app con i modelli di Azure Resource Manager](deploy-resource-manager-template.md).

Per informazioni sulla sintassi e le proprietà JSON per le risorse di Servizi app, vedere [Tipi di risorse Microsoft.Web](/azure/templates/microsoft.web/allversions).

| | |
|-|-|
|**Distribuzione di un'app**||
| [Piano di servizio app e app Linux di base](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Distribuisce un'app del servizio app configurata per Linux. |
| [Piano di servizio app e app Windows di base](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Distribuisce un'app del servizio app configurata per Windows. |
| [App collegata a un repository GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Distribuisce un'app del servizio app che esegue il pull del codice da GitHub. |
| [App con slot di distribuzione personalizzati](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Distribuisce un'app del servizio app con slot/ambienti di distribuzione personalizzati. |
|**Configurazione di un'app**||
| [Certificato dell'app da Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Distribuisce il certificato di un'app del servizio app da un segreto di Azure Key Vault e lo usa per il binding SSL. |
| [App con un dominio personalizzato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Distribuisce un'app del servizio app con un nome host personalizzato. |
| [App con un dominio personalizzato e SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Distribuisce un'app del servizio app con un nome host personalizzato e ottiene un certificato dell'app da Key Vault per il binding SSL. |
| [App con un'estensione GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Distribuisce un'app del servizio app con l'estensione del sito Golang. È quindi possibile eseguire applicazioni Web sviluppate in Golang in Azure. |
| [App con Java 8 e Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Distribuisce un'app del servizio app con Java 8 e Tomcat 8 abilitati. È quindi possibile eseguire applicazioni Java in Azure. |
|**App Linux con risorse connesse**||
| [App in Linux con MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Distribuisce un'app del servizio app in Linux con Database di Azure per MySQL. |
| [App in Linux con PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Distribuisce un'app del servizio app in Linux con Database di Azure per PostgreSQL. |
|**App con risorse connesse**||
| [App con MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Distribuisce un'app del servizio app in Windows con Database di Azure per MySQL. |
| [App con PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Distribuisce un'app del servizio app in Windows con Database di Azure per PostgreSQL. |
| [App con un database SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Distribuisce un'app del servizio app e un database SQL al livello di servizio Basic. |
| [App con connessione a un archivio BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Distribuisce un'app del servizio app con una stringa di connessione a un archivio BLOB di Azure. È quindi possibile usare l'archivio BLOB dall'app. |
| [App con un'istanza di Cache Redis di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Distribuisce un'app del servizio app con un'istanza di Cache Redis di Azure. |
|**Ambiente del servizio app per PowerApps**||
| [Creare un ambiente del servizio app versione 2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Crea un ambiente del servizio app versione 2 nella rete virtuale. |
| [Creare un ambiente del servizio app versione 2 con un indirizzo ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Crea un ambiente del servizio app versione 2 nella rete virtuale con un indirizzo di bilanciamento del carico interno privato. |
| [Configurare il certificato SSL predefinito per un ambiente del servizio app ILB o un ambiente del servizio app ILB versione 2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Configura il certificato SSL predefinito per un ambiente del servizio app ILB o un ambiente del servizio app ILB versione 2. |
| | |
