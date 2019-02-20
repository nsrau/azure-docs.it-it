---
title: Panoramica di Machine Learning Services (con R) nel database SQL di Azure (anteprima)
description: Questo argomento descrive Machine Learning Services (con R) nel database SQL di Azure e ne illustra il funzionamento.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 02/06/2019
ms.openlocfilehash: b50fd21e4d3325875134d2e2e9caeed9f8db75d0
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875604"
---
# <a name="machine-learning-services-with-r-in-azure-sql-database-preview"></a>Machine Learning Services (con R) nel database SQL di Azure (anteprima)

Machine Learning Services è una funzionalità del database SQL di Azure, usata per l'esecuzione di script R nel database. La funzionalità include pacchetti Microsoft R che garantiscono prestazioni elevate per le analisi predittive e l'apprendimento automatico. I dati relazionali possono essere usati negli script R tramite stored procedure, script di T-SQL contenenti istruzioni R o codice R contenente T-SQL.

> [!IMPORTANT]
> Machine Learning Services (con R) nel database SQL di Azure è attualmente disponibile in anteprima pubblica per singoli database e pool elastici tramite il modello di acquisto basato su vCore dei livelli di servizio **Utilizzo generico** e **Business critical**. In questa anteprima pubblica iniziale, il livello di servizio **Hyperscale** e l'opzione di distribuzione **Istanza gestita** non sono supportati. R attualmente è l'unico linguaggio supportato. Al momento non è disponibile il supporto per Python. 
>
> L'anteprima è disponibile attualmente nelle aree seguenti: Europa occidentale, Europa settentrionale, Stati Uniti occidentali 2, Stati Uniti orientali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Canada centrale, Asia sud-orientale, India meridionale e Australia sud-orientale. 
>
> [Iscriversi per l'anteprima](#signup) più avanti.

## <a name="what-you-can-do-with-r"></a>Cosa permette di fare R

Sfruttare i vantaggi del linguaggio R per implementare l'analisi avanzata e l'apprendimento automatico nel database. Questa possibilità consente di effettuare i calcoli e l'elaborazione dove si trovano i dati, eliminando la necessità di eseguire il pull dei dati attraverso la rete. È anche possibile sfruttare le potenzialità dei pacchetti R aziendali per implementare l'analisi avanzata su larga scala.

Machine Learning Services include una distribuzione di base di R, integrata con i pacchetti R aziendali di Microsoft. Le funzioni e gli algoritmi R di Microsoft sono progettati per essere scalabili e utili e offrire quindi soluzioni di analisi predittiva, modellazione statistica, visualizzazioni dei dati e algoritmi di apprendimento automatico all'avanguardia.

### <a name="r-packages"></a>Pacchetti R

I pacchetti R open source più comuni sono preinstallati in Machine Learning Services. Sono inclusi anche i pacchetti R di Microsoft seguenti:

| Pacchetto R | DESCRIZIONE|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open è la distribuzione avanzata di R offerta da Microsoft. È una piattaforma open source completa per analisi statistica e data science. È basata su R, con cui è compatibile al 100%, e include funzionalità aggiuntive per migliorare le prestazioni e la riproducibilità. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR è la libreria primaria per R scalabile. Le funzioni di questa libreria sono tra le più diffuse. In queste librerie sono disponibili trasformazioni e manipolazione, riepilogo statistico, visualizzazione e molte altre forme di analisi e modellazione dei dati. Inoltre, le funzioni in queste librerie distribuiscono automaticamente i carichi di lavoro tra i core disponibili per l'elaborazione parallela, con la possibilità di operare su blocchi di dati coordinati e gestiti dal motore di calcolo. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML aggiunge algoritmi di apprendimento automatico per creare modelli personalizzati per l'analisi del sentiment, delle immagini e del testo. |

Oltre a quelli preinstallati, è possibile [installare pacchetti aggiuntivi](sql-database-connect-query-r.md#add-package).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Iscriversi per l'anteprima

Per iscriversi all'anteprima pubblica, seguire questa procedura:

1. Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/) prima di iniziare.

2. Inviare un messaggio di posta elettronica a Microsoft all'indirizzo [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) per iscriversi all'anteprima pubblica. L'anteprima pubblica di Machine Learning Services (con R) nel database SQL non è abilitata per impostazione predefinita.

Dopo la registrazione nel programma, Microsoft eseguirà l'onboarding all'anteprima pubblica e abiliterà R per il database nuovo o esistente.

Non usare Machine Learning Services con R per carichi di lavoro di produzione durante l'anteprima pubblica.

## <a name="next-steps"></a>Passaggi successivi

- Vedere le [differenze principali con Machine Learning Services in SQL Server](sql-database-machine-learning-services-differences.md)
- Per informazioni sull'uso di Machine Learning Services (con R) nel database SQL di Azure, vedere la [guida di avvio rapido](sql-database-connect-query-r.md).
- Altre informazioni nelle [esercitazioni sul linguaggio R in SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)
