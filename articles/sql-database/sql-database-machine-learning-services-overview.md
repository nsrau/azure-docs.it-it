---
title: Azure SQL Database Machine Learning Services con panoramica di R (anteprima)
description: Questo articolo descrive Azure SQL Database Machine Learning Services (con R) e spiega come funziona.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 186b986fe1931365ee34efab2e04e58908402cc0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427949"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database servizi Machine Learning con R (anteprima)

Machine Learning Services è una funzionalità del database SQL di Azure, usata per l'esecuzione di script R nel database. La funzionalità include pacchetti Microsoft R che garantiscono prestazioni elevate per le analisi predittive e l'apprendimento automatico. I dati relazionali possono essere usati negli script R tramite stored procedure, script di T-SQL contenenti istruzioni R o codice R contenente T-SQL.

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services (con R) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> L'anteprima pubblica è disponibile per i database singoli e pool elastici usando il modello di acquisto basato su vCore nel **generico** e **aziendale critica** livelli di servizio. In questa anteprima pubblica iniziale, il livello di servizio **Hyperscale** e l'opzione di distribuzione **Istanza gestita** non sono supportati. R attualmente è l'unico linguaggio supportato. Al momento non è disponibile il supporto per Python.
>
> L'anteprima è attualmente disponibile nelle aree seguenti: Europa occidentale, Europa settentrionale, Stati Uniti occidentali 2, Stati Uniti orientali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Canada centrale, Asia sud-orientale, India meridionale e Australia sud-orientale.
>
> [Iscriversi per l'anteprima](#signup) più avanti.

## <a name="what-you-can-do-with-r"></a>Cosa permette di fare R

Sfruttare i vantaggi del linguaggio R per implementare l'analisi avanzata e l'apprendimento automatico nel database. Questa possibilità consente di effettuare i calcoli e l'elaborazione dove si trovano i dati, eliminando la necessità di eseguire il pull dei dati attraverso la rete. Inoltre, è possibile sfruttare le potenzialità dei pacchetti R enterprise per offrire analitica avanzata su larga scala.

Machine Learning Services include una distribuzione di base di R, integrata con i pacchetti R aziendali di Microsoft. Le funzioni e gli algoritmi R di Microsoft sono progettati per essere scalabili e utili e offrire quindi soluzioni di analisi predittiva, modellazione statistica, visualizzazioni dei dati e algoritmi di apprendimento automatico all'avanguardia.

### <a name="r-packages"></a>Pacchetti R

Pacchetti R open source più comuni sono preinstallati in servizi di Machine Learning. Sono inclusi anche i pacchetti R di Microsoft seguenti:

| Pacchetto R | Descrizione|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open è la distribuzione avanzata di R offerta da Microsoft. È una piattaforma open source completa per l'analisi scientifica dei dati e analisi statistica. È basata su R, con cui è compatibile al 100%, e include funzionalità aggiuntive per migliorare le prestazioni e la riproducibilità. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR è la libreria primaria per R scalabile. Le funzioni di questa libreria sono tra le più diffuse. In queste librerie sono disponibili trasformazioni e manipolazione, riepilogo statistico, visualizzazione e molte altre forme di analisi e modellazione dei dati. Inoltre, le funzioni in queste librerie distribuiscono automaticamente i carichi di lavoro tra i core disponibili per l'elaborazione parallela, con la possibilità di operare su blocchi di dati coordinati e gestiti dal motore di calcolo. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML aggiunge algoritmi di apprendimento automatico per creare modelli personalizzati per l'analisi del sentiment, delle immagini e del testo. |

Oltre ai pacchetti pre-installati, è possibile [installare pacchetti aggiuntivi](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Iscriversi per l'anteprima

Per iscriversi all'anteprima pubblica, seguire questa procedura:

1. Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/) prima di iniziare.

2. Inviare un messaggio di posta elettronica a Microsoft all'indirizzo [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) per iscriversi all'anteprima pubblica. L'anteprima pubblica di Machine Learning Services (con R) nel database SQL non è abilitata per impostazione predefinita.

Dopo la registrazione dell'utente del programma, Microsoft potrà eseguire l'onboarding è per la versione di anteprima pubblica e abilitare R per quella esistente o nuovo database.

Servizi di Machine Learning con R non è consigliato per il carico di lavoro di produzione durante l'anteprima pubblica.

## <a name="next-steps"></a>Passaggi successivi

- Vedere le [chiave differenze rispetto a SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- Per informazioni su come usare R per eseguire query SQL Database servizi Azure Machine Learning (anteprima), vedere la [Guida introduttiva](sql-database-connect-query-r.md).
- Per iniziare a usare alcuni script R semplici, vedere [creare ed eseguire script R semplici in Azure SQL Database servizi Machine Learning (anteprima)](sql-database-quickstart-r-create-script.md).
