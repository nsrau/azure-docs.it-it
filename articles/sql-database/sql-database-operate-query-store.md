---
title: Uso dell'archivio query nel database SQL di Azure
description: Informazioni su come usare l'archivio query nel database SQL di Azure
services: sql-database
author: bonova
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 11/08/2016
ms.author: bonova
ms.openlocfilehash: f0c3780f6efe87437742af7c1b8f6a3e6d0ee243
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Uso dell'archivio query nel database SQL di Azure
L'archivio query di Azure è una funzionalità di database completamente gestita che raccoglie e presenta informazioni cronologiche dettagliate su tutte le query in modo continuo. L'archivio query può essere concepito come un registratore dei dati di volo di un aeroplano che semplifica notevolmente la risoluzione dei problemi relativi alle prestazioni delle query sia per i clienti del cloud sia per i clienti locali. Questo articolo spiega alcuni aspetti specifici dell'uso dell'archivio query in Azure. Usando questi dati di query pre-raccolti, è possibile diagnosticare e risolvere velocemente i problemi di prestazioni e dedicare così più tempo alla propria attività. 

Archivio query è [disponibile a livello globale](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) nel database SQL di Azure da novembre 2015. Archivio query è la base per le funzionalità di analisi delle prestazioni e ottimizzazione, come [Advisor per database SQL e il dashboard delle prestazioni](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Al momento della pubblicazione di questo articolo, l'archivio query è in esecuzione in più di 200.000 database utente in Azure e raccoglie informazioni relative alle query da molti mesi senza interruzioni.

> [!IMPORTANT]
> Microsoft sta per attivare l'archivio query per tutti i database SQL di Azure esistenti e nuovi. 
> 
> 

## <a name="optimal-query-store-configuration"></a>Configurazione ottimale dell'archivio query
Questa sezione descrive impostazioni di configurazione predefinite ottimali progettate per garantire un funzionamento affidabile dell'archivio query e delle funzionalità dipendenti, come indicato nell'articolo [Advisor per database SQL e dashboard delle prestazioni](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). La configurazione predefinita è ottimizzata per la raccolta di dati continua, ossia per un tempo minimo di OFF/READ_ONLY.

| Configurazione | DESCRIZIONE | Predefinito | Comment |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Specifica il limite per lo spazio dati che Query Store occupa all'interno del database del cliente |100 |Applicato per i nuovi database |
| INTERVAL_LENGTH_MINUTES |Definisce la dimensione dell'intervallo di tempo durante il quale le statistiche di runtime raccolte per i piani di query vengono aggregate e rese persistenti. Tutti i piani di query attivi hanno al massimo una riga per un periodo di tempo definito con questa configurazione |60 |Applicato per i nuovi database |
| STALE_QUERY_THRESHOLD_DAYS |Criterio di pulizia basato sul tempo che controlla il periodo di memorizzazione delle statistiche di runtime persistenti e delle query inattive |30 |Applicato per i nuovi database e i database con un'impostazione predefinita precedente (367) |
| SIZE_BASED_CLEANUP_MODE |Specifica se la pulizia automatica dei dati viene eseguita quando la dimensione dati dell'archivio query si avvicina al limite |AUTO |Applicato per tutti i database |
| QUERY_CAPTURE_MODE |Specifica se vengono monitorate tutte le query o solo un sottoinsieme di esse |AUTO |Applicato per tutti i database |
| FLUSH_INTERVAL_SECONDS |Specifica il periodo massimo durante il quale le statistiche di runtime acquisite vengono mantenute in memoria prima di essere scaricate su disco |900 |Applicato per i nuovi database |
|  | | | |

> [!IMPORTANT]
> I valori predefiniti indicati sopra vengono applicati automaticamente nella fase finale dell'attivazione dell'archivio query in tutti i database SQL di Azure (vedere la precedente nota importante). Dopo questa attivazione, il database SQL di Azure non modificherà i valori di configurazione impostati dai clienti, a meno che non abbiano un impatto negativo sul carico di lavoro primario o sull'affidabilità di funzionamento dell'archivio query.
> 
> 

Se si desidera mantenere le impostazioni personalizzate, usare [ALTER DATABASE con le opzioni dell'archivio query](https://msdn.microsoft.com/library/bb522682.aspx) per riportare la configurazione allo stato precedente. Vedere [Procedure consigliate per l'archivio query](https://msdn.microsoft.com/library/mt604821.aspx) per informazioni su come scegliere i parametri di configurazione ottimali.

## <a name="next-steps"></a>Passaggi successivi
[Informazioni dettagliate sulle prestazioni del database SQL](sql-database-performance.md)

## <a name="additional-resources"></a>Risorse aggiuntive
Per altre informazioni, vedere gli articoli dedicati ai seguenti argomenti:

* [Un registratore dei dati di volo per il database](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 
* [Monitoraggio delle prestazioni mediante l'archivio query](https://msdn.microsoft.com/library/dn817826.aspx)
* [Scenari di utilizzo dell'archivio query](https://msdn.microsoft.com/library/mt614796.aspx)
* [Monitoraggio delle prestazioni mediante l'archivio query](https://msdn.microsoft.com/library/dn817826.aspx) 

