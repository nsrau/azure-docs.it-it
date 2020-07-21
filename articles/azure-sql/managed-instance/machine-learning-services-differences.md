---
title: Differenze principali per Machine Learning Services (anteprima)
description: Questo argomento descrive le differenze principali tra Machine Learning Services in Azure SQL Istanza gestita e SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab, davidph
manager: cgronlun
ms.date: 05/27/2020
ms.openlocfilehash: f267b155fe21e5dee1a7c488c999fe194ed38eb1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86504119"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Differenze principali tra Machine Learning Services in Istanza gestita di SQL di Azure e SQL Server

La funzionalità di [Machine Learning Services in istanza gestita SQL di Azure (anteprima)](machine-learning-services-overview.md) è quasi identica a quella di [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Di seguito sono riportate alcune differenze principali.

> [!IMPORTANT]
> Machine Learning Services in Istanza gestita SQL di Azure è attualmente disponibile in anteprima pubblica. Per iscriversi, vedere [la pagina relativa all'iscrizione per l'anteprima](machine-learning-services-overview.md#signup).

## <a name="preview-limitations"></a>Limiti di anteprima

Nell'anteprima sono presenti le limitazioni seguenti:

- Le connessioni loopback non funzionano (vedere [la connessione loopback a SQL Server da uno script Python o R](/sql/machine-learning/connect/loopback-connection)).
- I pool di risorse esterni non sono supportati.
- Sono supportati solo Python e R. Non è possibile aggiungere linguaggi esterni quali Java.
- Gli scenari che usano l'interfaccia MPI ( [Message Passing Interface](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) ) non sono supportati.

In caso di aggiornamento dell'obiettivo del livello di servizio, eseguire l'aggiornamento e generare un ticket di supporto per riabilitare i limiti delle risorse dedicate per R/Python.

## <a name="language-support"></a>Lingue supportate

Machine Learning Services in SQL Istanza gestita e SQL Server supportano sia Python sia il [Framework di estendibilità](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)R. Le differenze principali sono le seguenti:

- Le versioni iniziali di Python e R sono diverse tra Machine Learning Services in SQL Istanza gestita e SQL Server:

  | System               | Python | R     |
  |----------------------|--------|-------|
  | Istanza gestita di SQL | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- Non è necessario configurare `external scripts enabled` tramite `sp_configure`. Dopo aver [effettuato l'iscrizione](machine-learning-services-overview.md#signup) all'anteprima, Machine Learning è abilitato per Azure SQL istanza gestita.

## <a name="packages"></a>Pacchetti

La gestione dei pacchetti Python e R funziona in modo diverso tra SQL Istanza gestita e SQL Server. Le differenze sono le seguenti:

- I pacchetti non possono eseguire chiamate di rete in uscita. Questa limitazione è simile alle [regole del firewall predefinite per Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) SQL Server, ma non può essere modificata in SQL istanza gestita.
- Non è disponibile il supporto per i pacchetti che dipendono da runtime esterni, ad esempio Java, o richiedono l'accesso alle API del sistema operativo per l'installazione o l'utilizzo.

Per ulteriori informazioni sulla gestione dei pacchetti Python e R, vedere:

- [Ottenere informazioni sui pacchetti Python](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)
- [Ottenere informazioni sui pacchetti R](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)

## <a name="resource-governance"></a>Governance delle risorse

Non è possibile limitare le risorse R attraverso [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) e i pool di risorse esterni.

Durante l'anteprima pubblica, le risorse R sono impostate in modo da avere un massimo del 20% delle risorse di Istanza gestita di SQL e dipendono dal livello di servizio scelto. Per altre informazioni, vedere [Modelli di acquisto del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Errore di memoria insufficiente

Se la memoria disponibile per R non è sufficiente, verrà ricevuto un messaggio di errore. I messaggi di errore comuni sono:

- Non è possibile comunicare con il runtime per lo script ' R ' per l'ID richiesta: * * * * * * *. Verificare i requisiti del runtime di "R"
- Si è verificato un errore di script 'R' durante l'esecuzione di 'sp_execute_external_script' con HRESULT 0x80004004. ... si è verificato un errore dello script esterno: "...could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'" (non è stato possibile allocare la memoria (0 MB) nella funzione C 'R_AllocStringBuffer'")
- Si è verificato un errore di script esterno: errore: Impossibile allocare un vettore di dimensioni.

L'utilizzo della memoria dipende dalla quantità usata negli script R e dal numero di query parallele in esecuzione. Se si ricevono gli errori precedenti, è possibile dimensionare il database a un livello di servizio superiore per risolvere il problema.

## <a name="next-steps"></a>Passaggi successivi

- Vedere Panoramica, [Machine Learning Services in istanza gestita SQL di Azure](machine-learning-services-overview.md).
- Per informazioni su come usare Python in Machine Learning Services, vedere [eseguire script Python](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Per informazioni sull'uso di R in Machine Learning Services, vedere [eseguire script r](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
