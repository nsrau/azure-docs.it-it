---
title: Machine Learning Services con R (anteprima)
description: Questo articolo descrive il database SQL di Azure Machine Learning Services (con R) e ne illustra il funzionamento.
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
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Machine Learning Services di database SQL di Azure con R (anteprima)

Machine Learning Services è una funzionalità del database SQL di Azure, usata per l'esecuzione di script R nel database. La funzionalità include pacchetti Microsoft R che garantiscono prestazioni elevate per le analisi predittive e l'apprendimento automatico. I dati relazionali possono essere usati negli script R tramite stored procedure, script di T-SQL contenenti istruzioni R o codice R contenente T-SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> L'anteprima è disponibile per database singoli e pool elastici usando il modello di acquisto basato su vCore nei livelli di servizio per utilizzo **generico** e **business critical** . In questa anteprima iniziale, il livello di servizio con **iperscalabilità** e l'opzione di distribuzione **istanza gestita** non sono supportati. R attualmente è l'unico linguaggio supportato. Al momento non è disponibile il supporto per Python.
>
> L'anteprima è attualmente disponibile nelle aree geografiche seguenti: Europa occidentale, Europa settentrionale, Stati Uniti occidentali 2, Stati Uniti orientali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Canada centrale, Asia sudorientale, India meridionale e Australia sudorientale.

## <a name="what-you-can-do-with-r"></a>Cosa permette di fare R

Sfruttare i vantaggi del linguaggio R per implementare l'analisi avanzata e l'apprendimento automatico nel database. Questa possibilità consente di effettuare i calcoli e l'elaborazione dove si trovano i dati, eliminando la necessità di eseguire il pull dei dati attraverso la rete. Puoi anche sfruttare la potenza dei pacchetti R aziendali per offrire analisi avanzate su larga scala.

Machine Learning Services include una distribuzione di base di R, integrata con i pacchetti R aziendali di Microsoft. Le funzioni e gli algoritmi R di Microsoft sono progettati per essere scalabili e utili e offrire quindi soluzioni di analisi predittiva, modellazione statistica, visualizzazioni dei dati e algoritmi di apprendimento automatico all'avanguardia.

### <a name="r-packages"></a>Pacchetti R

I pacchetti R open source più comuni sono pre-installati in Machine Learning Services. Sono inclusi anche i pacchetti R di Microsoft seguenti:

| Pacchetto R | DESCRIZIONE|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open è la distribuzione avanzata di R offerta da Microsoft. Si tratta di una piattaforma open source completa per l'analisi statistica e data science. È basata su R, con cui è compatibile al 100%, e include funzionalità aggiuntive per migliorare le prestazioni e la riproducibilità. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR è la libreria primaria per R scalabile. Le funzioni di questa libreria sono tra le più diffuse. In queste librerie sono disponibili trasformazioni e manipolazione, riepilogo statistico, visualizzazione e molte altre forme di analisi e modellazione dei dati. Inoltre, le funzioni in queste librerie distribuiscono automaticamente i carichi di lavoro tra i core disponibili per l'elaborazione parallela, con la possibilità di operare su blocchi di dati coordinati e gestiti dal motore di calcolo. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML aggiunge algoritmi di apprendimento automatico per creare modelli personalizzati per l'analisi del sentiment, delle immagini e del testo. |

Oltre ai pacchetti preinstallati, è possibile [installare pacchetti aggiuntivi](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Iscriversi per l'anteprima

> [!IMPORTANT]
> Iscriversi al database SQL di Azure Machine Learning Services (anteprima) è attualmente chiuso.

Machine Learning Services con R non è consigliato per il carico di lavoro di produzione durante l'anteprima.

## <a name="next-steps"></a>Passaggi successivi

- Vedere le [differenze principali tra SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- Per informazioni su come usare R per eseguire query sul database SQL di Azure Machine Learning Services (anteprima), vedere la [Guida introduttiva](sql-database-connect-query-r.md).
- Per iniziare a usare alcuni semplici script R, vedere [creare ed eseguire script r semplici nel database SQL di Azure Machine Learning Services (anteprima)](sql-database-quickstart-r-create-script.md).
