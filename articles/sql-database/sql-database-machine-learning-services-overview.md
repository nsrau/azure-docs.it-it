---
title: Machine Learning Services with R (preview)
description: This article describes Azure SQL Database Machine Learning Services (with R) and explains how it works.
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
ms.date: 11/20/2019
ms.openlocfilehash: ca223de2bc0b26e4968d400ea418761a399dacae
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462342"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services with R (preview)

Machine Learning Services è una funzionalità del database SQL di Azure, usata per l'esecuzione di script R nel database. La funzionalità include pacchetti Microsoft R che garantiscono prestazioni elevate per le analisi predittive e l'apprendimento automatico. I dati relazionali possono essere usati negli script R tramite stored procedure, script di T-SQL contenenti istruzioni R o codice R contenente T-SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> The preview is available for single databases and elastic pools using the vCore-based purchasing model in the **general purpose** and **business critical** service tiers. In this initial preview, the **hyperscale** service tier and the **managed instance** deployment option are not supported. R attualmente è l'unico linguaggio supportato. Al momento non è disponibile il supporto per Python.
>
> The preview is currently available in the following regions: West Europe, North Europe, West US 2, East US, South Central US, North Central US, Canada Central, Southeast Asia, India South, and Australia Southeast.

## <a name="what-you-can-do-with-r"></a>Cosa permette di fare R

Sfruttare i vantaggi del linguaggio R per implementare l'analisi avanzata e l'apprendimento automatico nel database. Questa possibilità consente di effettuare i calcoli e l'elaborazione dove si trovano i dati, eliminando la necessità di eseguire il pull dei dati attraverso la rete. Also, you can leverage the power of enterprise R packages to deliver advanced analytics at scale.

Machine Learning Services include una distribuzione di base di R, integrata con i pacchetti R aziendali di Microsoft. Le funzioni e gli algoritmi R di Microsoft sono progettati per essere scalabili e utili e offrire quindi soluzioni di analisi predittiva, modellazione statistica, visualizzazioni dei dati e algoritmi di apprendimento automatico all'avanguardia.

### <a name="r-packages"></a>Pacchetti R

Most common open-source R packages are pre-installed in Machine Learning Services. Sono inclusi anche i pacchetti R di Microsoft seguenti:

| Pacchetto R | Description|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open è la distribuzione avanzata di R offerta da Microsoft. It is a complete open-source platform for statistical analysis and data science. È basata su R, con cui è compatibile al 100%, e include funzionalità aggiuntive per migliorare le prestazioni e la riproducibilità. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR è la libreria primaria per R scalabile. Le funzioni di questa libreria sono tra le più diffuse. In queste librerie sono disponibili trasformazioni e manipolazione, riepilogo statistico, visualizzazione e molte altre forme di analisi e modellazione dei dati. Inoltre, le funzioni in queste librerie distribuiscono automaticamente i carichi di lavoro tra i core disponibili per l'elaborazione parallela, con la possibilità di operare su blocchi di dati coordinati e gestiti dal motore di calcolo. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML aggiunge algoritmi di apprendimento automatico per creare modelli personalizzati per l'analisi del sentiment, delle immagini e del testo. |

In addition to the pre-installed packages, you can [install additional packages](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Iscrizione all'anteprima

> [!IMPORTANT]
> Sign up for Azure SQL Database Machine Learning Services (preview) is currently closed.

Machine Learning Services with R is not recommended for production workload during the preview.

## <a name="next-steps"></a>Passaggi successivi

- See the [key differences from SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- To learn how to use R to query Azure SQL Database Machine Learning Services (preview), see the [Quickstart guide](sql-database-connect-query-r.md).
- To get started with some simple R scripts, see [Create and run simple R scripts in Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md).
