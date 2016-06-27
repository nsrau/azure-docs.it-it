<properties
	pageTitle="Connettersi al database SQL tramite Java con JDBC in Windows | Microsoft Azure"
	description="Presentazione di un esempio di codice Java che è possibile usare per connettersi al database SQL di Azure. L'esempio usa JDBC e viene eseguito su un computer client Windows."
	services="sql-database"
	documentationCenter=""
	authors="LuisBosquez"
	manager="jhubbard"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="04/27/2016"
	ms.author="lbosq"/>


# Connettersi al database SQL tramite Java con JDBC in Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Questo argomento presenta un esempio di codice Java che è possibile usare per connettersi al database SQL di Azure. L'esempio Java si basa su Java Development Kit (JDK) versione 1.8 e si connette a un database SQL di Azure tramite il driver JDBC.

## Passaggio 1: Configurare l'ambiente di sviluppo

[Configurare l'ambiente di sviluppo Java](https://msdn.microsoft.com/library/mt720658.aspx)

## Passaggio 2: Creare un database SQL

Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database.

## Passaggio 3: Ottenere la stringa di connessione

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] Se si usa il driver JDBC JTDS, sarà necessario aggiungere "ssl = require" all'URL della stringa di connessione ed è necessario impostare l'opzione seguente per JVM "-Djsse.enableCBCProtection=false". Questa opzione JVM disattiva la correzione di una vulnerabilità di sicurezza,quindi assicurarsi di comprendere i rischi coinvolti prima di impostare questa opzione.

## Passaggio 4: Eseguire il codice di esempio

* [Modello di verifica della connessione a SQL tramite Java](https://msdn.microsoft.com/library/mt720656.aspx)

## Passaggi successivi

Per altre informazioni, vedere il [Centro per sviluppatori di Java](/develop/java/).

## Risorse aggiuntive

Per informazioni sugli schemi di architettura dati comuni delle applicazioni di database multi-tenant software come un servizio (SaaS), vedere [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

<!---HONumber=AcomDC_0615_2016-->