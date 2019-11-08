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
ms.date: 03/01/2019
ms.openlocfilehash: a2af1fdd1ee461e3b3db613ff4a575649da2dfdc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827440"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Machine Learning Services di database SQL di Azure con R (anteprima)

Machine Learning Services è una funzionalità del database SQL di Azure, usata per l'esecuzione di script R nel database. La funzionalità include pacchetti Microsoft R che garantiscono prestazioni elevate per le analisi predittive e l'apprendimento automatico. I dati relazionali possono essere usati negli script R tramite stored procedure, script di T-SQL contenenti istruzioni R o codice R contenente T-SQL.

> [!IMPORTANT]
> Il database SQL di Azure Machine Learning Services (con R) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> L'anteprima pubblica è disponibile per database singoli e pool elastici usando il modello di acquisto basato su vCore nei livelli di servizio per utilizzo **generico** e **business critical** . In questa anteprima pubblica iniziale, il livello di servizio **Hyperscale** e l'opzione di distribuzione **Istanza gestita** non sono supportati. R attualmente è l'unico linguaggio supportato. Al momento non è disponibile il supporto per Python.
>
> L'anteprima è attualmente disponibile nelle aree geografiche seguenti: Europa occidentale, Europa settentrionale, Stati Uniti occidentali 2, Stati Uniti orientali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Canada centrale, Asia sudorientale, India meridionale e Australia sudorientale.
>
> [Iscriversi per l'anteprima](#signup) più avanti.

## <a name="what-you-can-do-with-r"></a>Cosa permette di fare R

Sfruttare i vantaggi del linguaggio R per implementare l'analisi avanzata e l'apprendimento automatico nel database. Questa possibilità consente di effettuare i calcoli e l'elaborazione dove si trovano i dati, eliminando la necessità di eseguire il pull dei dati attraverso la rete. Puoi anche sfruttare la potenza dei pacchetti R aziendali per offrire analisi avanzate su larga scala.

Machine Learning Services include una distribuzione di base di R, integrata con i pacchetti R aziendali di Microsoft. Le funzioni e gli algoritmi R di Microsoft sono progettati per essere scalabili e utili e offrire quindi soluzioni di analisi predittiva, modellazione statistica, visualizzazioni dei dati e algoritmi di apprendimento automatico all'avanguardia.

### <a name="r-packages"></a>Pacchetti R

I pacchetti R open source più comuni sono pre-installati in Machine Learning Services. Sono inclusi anche i pacchetti R di Microsoft seguenti:

| Pacchetto R | Descrizione|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open è la distribuzione avanzata di R offerta da Microsoft. Si tratta di una piattaforma open source completa per l'analisi statistica e data science. È basata su R, con cui è compatibile al 100%, e include funzionalità aggiuntive per migliorare le prestazioni e la riproducibilità. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR è la libreria primaria per R scalabile. Le funzioni di questa libreria sono tra le più diffuse. In queste librerie sono disponibili trasformazioni e manipolazione, riepilogo statistico, visualizzazione e molte altre forme di analisi e modellazione dei dati. Inoltre, le funzioni in queste librerie distribuiscono automaticamente i carichi di lavoro tra i core disponibili per l'elaborazione parallela, con la possibilità di operare su blocchi di dati coordinati e gestiti dal motore di calcolo. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML aggiunge algoritmi di apprendimento automatico per creare modelli personalizzati per l'analisi del sentiment, delle immagini e del testo. |

Oltre ai pacchetti preinstallati, è possibile [installare pacchetti aggiuntivi](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Iscriversi per l'anteprima

Per iscriversi all'anteprima pubblica, seguire questa procedura:

1. Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/) prima di iniziare.

2. Inviare un messaggio di posta elettronica a Microsoft all'indirizzo [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) per iscriversi all'anteprima pubblica. L'anteprima pubblica di Machine Learning Services (con R) nel database SQL non è abilitata per impostazione predefinita.

Una volta iscritti al programma, Microsoft effettuerà l'onboarding all'anteprima pubblica e Abilita R per il database esistente o nuovo.

Machine Learning Services con R non è consigliato per il carico di lavoro di produzione durante l'anteprima pubblica.

## <a name="next-steps"></a>Passaggi successivi

- Vedere le [differenze principali tra SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- Per informazioni su come usare R per eseguire query sul database SQL di Azure Machine Learning Services (anteprima), vedere la [Guida introduttiva](sql-database-connect-query-r.md).
- Per iniziare a usare alcuni semplici script R, vedere [creare ed eseguire script r semplici nel database SQL di Azure Machine Learning Services (anteprima)](sql-database-quickstart-r-create-script.md).
