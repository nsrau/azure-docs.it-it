---
title: Differenze principali per Machine Learning Services (anteprima)
description: Questo articolo descrive le differenze principali tra Machine Learning Services in Azure SQL Istanza gestita e SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 10/26/2020
ms.openlocfilehash: c806c0a13f9f5f13588b780054d1f285beb44802
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324534"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Differenze principali tra Machine Learning Services in Istanza gestita di SQL di Azure e SQL Server

La funzionalità di [Machine Learning Services in istanza gestita SQL di Azure (anteprima)](machine-learning-services-overview.md) è quasi identica a quella di [SQL Server Machine Learning Services](/sql/advanced-analytics/what-is-sql-server-machine-learning). Di seguito sono riportate alcune differenze principali.

> [!IMPORTANT]
> Machine Learning Services in Istanza gestita SQL di Azure è attualmente disponibile in anteprima pubblica. Per iscriversi, vedere [la pagina relativa all'iscrizione per l'anteprima](machine-learning-services-overview.md#signup).

## <a name="preview-limitations"></a>Limiti di anteprima

Nell'anteprima sono presenti le limitazioni seguenti:

- Le connessioni loopback non funzionano (vedere [la connessione loopback a SQL Server da uno script Python o R](/sql/machine-learning/connect/loopback-connection)).
- I pool di risorse esterni non sono supportati.
- Sono supportati solo Python e R. Non è possibile aggiungere linguaggi esterni quali Java.
- Gli scenari che usano l'interfaccia MPI ( [Message Passing Interface](/message-passing-interface/microsoft-mpi) ) non sono supportati.

Nel caso di un aggiornamento dell'obiettivo del livello di servizio (SLO), aggiornare SLO e generare un ticket di supporto per riabilitare i limiti delle risorse dedicate per R/Python.

## <a name="language-support"></a>Lingue supportate

Machine Learning Services in SQL Istanza gestita e SQL Server supportano sia Python sia il [Framework di estendibilità](/sql/advanced-analytics/concepts/extensibility-framework)R. Le differenze principali sono le seguenti:

- Le versioni iniziali di Python e R sono diverse tra Machine Learning Services in SQL Istanza gestita e SQL Server:

  | Sistema               | Python | R     |
  |----------------------|--------|-------|
  | Istanza gestita di SQL | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- Non è necessario configurare `external scripts enabled` tramite `sp_configure`. Dopo aver [effettuato l'iscrizione](machine-learning-services-overview.md#signup) all'anteprima, Machine Learning è abilitato per Azure SQL istanza gestita.

## <a name="packages"></a>Pacchetti

La gestione dei pacchetti Python e R funziona in modo diverso tra SQL Istanza gestita e SQL Server. Le differenze sono le seguenti:

- Non è disponibile il supporto per i pacchetti che dipendono da runtime esterni, ad esempio Java, o richiedono l'accesso alle API del sistema operativo per l'installazione o l'utilizzo.
- I pacchetti possono eseguire chiamate di rete in uscita (modifiche rispetto a precedenti nell'anteprima). È possibile impostare le regole di sicurezza in uscita appropriate al livello del [gruppo di sicurezza di rete](../../virtual-network/network-security-groups-overview.md) per abilitare le chiamate di rete in uscita.

Per ulteriori informazioni sulla gestione dei pacchetti Python e R, vedere:

- [Ottenere informazioni sui pacchetti Python](/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [Ottenere informazioni sui pacchetti R](/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Governance delle risorse

Non è possibile limitare le risorse R attraverso [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) e i pool di risorse esterni.

Durante l'anteprima pubblica, le risorse R sono impostate in modo da avere un massimo del 20% delle risorse di Istanza gestita di SQL e dipendono dal livello di servizio scelto. Per altre informazioni, vedere [Modelli di acquisto del database SQL di Azure](../database/purchasing-models.md).

### <a name="insufficient-memory-error"></a>Errore di memoria insufficiente

Se la memoria disponibile per R non è sufficiente, verrà ricevuto un messaggio di errore. I messaggi di errore comuni sono:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

L'utilizzo della memoria dipende dalla quantità usata negli script R e dal numero di query parallele in esecuzione. Se si ricevono gli errori precedenti, è possibile dimensionare il database a un livello di servizio superiore per risolvere il problema.

## <a name="next-steps"></a>Passaggi successivi

- Vedere Panoramica, [Machine Learning Services in istanza gestita SQL di Azure](machine-learning-services-overview.md).
- Per informazioni su come usare Python in Machine Learning Services, vedere [eseguire script Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Per informazioni sull'uso di R in Machine Learning Services, vedere [eseguire script r](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).