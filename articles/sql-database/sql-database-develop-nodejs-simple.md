<properties
	pageTitle="Connettersi al database SQL tramite Node.js | Microsoft Azure"
	description="Presentazione di un esempio di codice Node.js che è possibile usare per connettersi al database SQL di Azure."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/07/2016"
	ms.author="meetb"/>

# Connettersi al Database SQL tramite Node.js

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

In questo argomento viene illustrato come connettersi a un database SQL di Azure ed eseguire query tramite Node.js. È possibile eseguire questo esempio da piattaforme Windows, Ubuntu Linux o Mac.

## Passaggio 1: Configurare l'ambiente di sviluppo

[Prerequisiti per l'uso del driver Tedious Node.js per SQL Server](https://msdn.microsoft.com/library/mt652094.aspx)

## Passaggio 2: Creare un database SQL

Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database di esempio. È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**.

## Passaggio 3: Ottenere i dettagli di connessione

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Passaggio 4: Eseguire il codice di esempio

[Modello di verifica della connessione a SQL tramite Node.js](https://msdn.microsoft.com/library/mt715784.aspx)

## Risorse aggiuntive

Per informazioni sugli schemi di architettura dati comuni delle applicazioni di database multi-tenant software come un servizio (SaaS), vedere [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

<!---HONumber=AcomDC_0615_2016-->