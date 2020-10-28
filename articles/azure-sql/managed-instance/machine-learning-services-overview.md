---
title: Machine Learning Services in Istanza gestita SQL di Azure (anteprima)
description: Questo articolo fornisce una panoramica o Machine Learning Services in Istanza gestita SQL di Azure.
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
ms.date: 06/03/2020
ms.openlocfilehash: 2fb584d999d48c7d29420db26daebf592790af2b
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676814"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance-preview"></a>Machine Learning Services in Istanza gestita SQL di Azure (anteprima)

Machine Learning Services è una funzionalità di Istanza gestita SQL di Azure (anteprima) che fornisce l'apprendimento automatico nel database, che supporta gli script Python e R. La funzionalità include i pacchetti Microsoft Python e R per l'analisi predittiva ad alte prestazioni e l'apprendimento automatico. I dati relazionali possono essere usati negli script tramite stored procedure, script T-SQL contenenti istruzioni Python o R oppure codice Python o R contenente T-SQL.

> [!IMPORTANT]
> Machine Learning Services è una funzionalità di Istanza gestita di SQL di Azure attualmente disponibile in anteprima pubblica.
> Questa funzionalità di anteprima è inizialmente disponibile in un numero limitato di aree negli Stati Uniti, in Asia Europa e in Australia con altre aree aggiunte in un secondo momento.
>
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> [Iscriversi per l'anteprima](#signup) più avanti.

## <a name="what-is-machine-learning-services"></a>Che cos'è Machine Learning Services?

Machine Learning Services in Azure SQL Istanza gestita consente di eseguire script Python e R nel database. È possibile usare questa funzionalità per preparare e pulire i dati, eseguire la progettazione delle caratteristiche e il training, la valutazione e la distribuzione di modelli di Machine Learning all'interno di un database. La funzionalità esegue gli script nella posizione in cui i dati risiedono, eliminando la necessità di trasferire i dati in rete in un altro server.

Usare Machine Learning Services con supporto R/Python in Azure SQL Istanza gestita per:

- **Eseguire script r e Python per la preparazione dei dati e l'elaborazione di dati generici** . è ora possibile trasferire gli script r/Python in Azure SQL istanza gestita dove si trovano i dati, anziché spostare i dati in un altro server per eseguire script r e Python. È possibile eliminare la necessità di spostamento dei dati e problemi associati relativi a latenza, sicurezza e conformità.

- **Training di modelli di machine learning nel database** : è possibile eseguire il training dei modelli usando qualsiasi algoritmo open source. È possibile ridimensionare facilmente il training sull'intero set di dati anziché basarsi sui set di dati di esempio estratti dal database.

- **Distribuire i modelli e gli script nell'ambiente di produzione nelle stored procedure** . gli script e i modelli sottoposti a training possono essere operativi semplicemente incorporati nelle stored procedure T-SQL. Le app che si connettono ad Azure SQL Istanza gestita possono trarre vantaggio da stime e intelligence in questi modelli semplicemente chiamando una stored procedure. È anche possibile usare la funzione di stima T-SQL nativa per rendere operativo i modelli per un punteggio rapido in scenari con punteggio in tempo reale altamente simultanei.

Le distribuzioni di base di Python e R sono incluse in Machine Learning Services. È possibile installare e usare pacchetti e framework open source, come PyTorch, TensorFlow e scikit-learn, oltre ai pacchetti Microsoft [revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-revoscalepy) e [microsoftml](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-microsoftml) per Python e [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler), [MicrosoftML](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml), [olapR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-olapr) e [sqlrutils](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-sqlrutils) per R.

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Iscriversi per l'anteprima

Questa versione di anteprima pubblica limitata è soggetta alle condizioni per l' [Anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Se si è interessati a partecipare al programma di anteprima e accettare le presenti condizioni, è possibile richiedere la registrazione creando un ticket di supporto di Azure all'indirizzo [**https://azure.microsoft.com/support/create-ticket/**](https://azure.microsoft.com/support/create-ticket/) . 

1. Nella pagina **Crea un ticket di supporto** fare clic su **Crea un evento imprevisto** .

1. Nella pagina **Guida e supporto** fare clic su **nuova richiesta di supporto** per creare un nuovo ticket.

1. Selezionare le opzioni seguenti:
   - Tipo di problema- **tecnico**
   - Sottoscrizione: *selezionare la sottoscrizione*
   - Servizio- **istanza gestita SQL**
   - Risorsa: *selezionare l'istanza gestita*
   - Riepilogo: *immettere una breve descrizione della richiesta*
   - Tipo di problema- **Machine Learning Services per SQL istanza gestita (anteprima)**
   - Sottotipo di problema: **altro problema o domande "procedura"**

1. Fare clic su **Avanti: soluzioni** .

1. Leggere le informazioni sull'anteprima, quindi fare clic su **Avanti: dettagli** .

1. In questa pagina:
   - Per la domanda **che si sta tentando di iscriversi all'anteprima** , selezionare **Sì** . 
   - Per **Descrizione** , immettere le specifiche della richiesta, inclusi il nome del server logico, l'area e l'ID sottoscrizione che si desidera registrare nell'anteprima. Immettere altri dettagli in base alle esigenze.
   - Selezionare il metodo di contatto preferito. 

1. Al termine, fare clic su **Avanti: rivedere + crea** e quindi fare clic su **Crea** .

Dopo la registrazione nel programma, Microsoft eseguirà l'onboarding all'anteprima pubblica e abiliterà Machine Learning Services per il database nuovo o esistente.

Non usare Machine Learning Services in Istanza gestita di SQL per carichi di lavoro di produzione durante l'anteprima pubblica.

## <a name="next-steps"></a>Passaggi successivi

- Vedere le [differenze principali tra SQL Server Machine Learning Services](machine-learning-services-differences.md).
- Per informazioni su come usare Python in Machine Learning Services, vedere [eseguire script Python](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Per informazioni sull'uso di R in Machine Learning Services, vedere [eseguire script r](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Per ulteriori informazioni su Machine Learning su altre piattaforme SQL, vedere la [documentazione di SQL Machine Learning](https://docs.microsoft.com/sql/machine-learning/).
