---
title: Anteprima della risoluzione dei problemi di Microsoft Power BI Embedded
description: Anteprima della risoluzione dei problemi di Microsoft Power BI Embedded
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: c8aee652-ed8b-4c66-9c63-f798b7a655b4
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1f4cf4a04e7a921f8e11c94f74d53ac80c231933


---
# <a name="microsoft-power-bi-embedded-preview-troubleshooting"></a>Anteprima della risoluzione dei problemi di Microsoft Power BI Embedded
Questo articolo fornisce alcune risposte per la risoluzione dei problemi di **Power BI Embedded**.

<a name="connection-string"/>

## <a name="setting-sql-server-connection-strings"></a>Impostare le stringhe di connessione di SQL Server
Per impostare una stringa di connessione di SQL Server, è necessario seguire un formato specifico. Di seguito viene specificata una stringa di connessione di esempio per SQL Server.

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

Per altre informazioni sulle stringhe di connessione di SQL Server, vedere gli articoli seguenti:

* [Stringhe di connessione di SQL Server](https://msdn.microsoft.com/library/jj653752.aspx)
* [Proprietà SqlConnection.ConnectionString](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>

## <a name="setting-credentials"></a>Impostazione delle credenziali
Nel caso in cui si abbiano credenziali per un ambiente di sviluppo o di gestione temporanea, ad esempio nome utente e password, potrebbe essere necessario aggiornare le credenziali che corrispondono a una soluzione di produzione.

## <a name="see-also"></a>Vedere anche
* [Esempio introduttivo](power-bi-embedded-get-started-sample.md)
* [Informazioni su Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)




<!--HONumber=Nov16_HO3-->


