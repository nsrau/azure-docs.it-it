---
title: Connessione a un'origine dati nelle raccolte di aree di lavoro di Power BI | Microsoft Docs
description: Informazioni su come connettersi a un'origine dati nelle raccolte di aree di lavoro di Power BI.
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 2a4caeb3-255d-4215-9554-0ca8e3568c13
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 24600c4343e3bfebe14f25020c5a7ba02d15af64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-data-source"></a>Connettersi a un'origine dati

Le **raccolte di aree di lavoro di Power BI** consentono di incorporare report in un'app. Quando un report di Power BI viene incorporato in un'app, il report si connette ai dati sottostanti tramite l'**importazione** di una copia dei dati o tramite **connessione** diretta all'origine dati con **DirectQuery**.

> [!IMPORTANT]
> Le raccolte di aree di lavoro di Power BI sono deprecate e sono disponibili fino a giugno 2018 o fino alla data specificata nel contratto. È consigliabile pianificare la migrazione a Power BI Embedded per evitare interruzioni nell'applicazione. Per informazioni su come eseguire la migrazione dei dati a Power BI Embedded, vedere [Come eseguire la migrazione del contenuto delle raccolte di aree di lavoro di Power BI a Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Di seguito sono spiegate le differenze tra l'**importazione** e la modalità **DirectQuery**.

| Importa | DirectQuery |
| --- | --- |
| Tabelle, colonne *e dati* vengono importati o copiati nel set di dati del report. Per visualizzare le modifiche apportate ai dati sottostanti, è necessario aggiornare o importare nuovamente un set di dati completo e corrente. |Vengono importate o copiate nel set di dati del report solo *tabelle e colonne* . Vengono visualizzati sempre i dati più recenti. |

Con le raccolte di aree di lavoro di Power BI, è attualmente possibile usare DirectQuery con origini dati cloud ma non con origini dati locali.

> [!NOTE]
> Il gateway dati locale non è al momento supportato con le raccolte di aree di lavoro di Power BI. Non è quindi possibile usare DirectQuery con origini dati locali.

## <a name="supported-data-sources"></a>Origini dati supportate

**DirectQuery**
* Database SQL di Azure
* Azure SQL Data Warehouse

**Import (Importa) (Import (Importa)a)**

È possibile usare per l'importazione tutte le origini dati disponibili in Power BI Desktop. Tali dati **non** potranno essere aggiornati nelle raccolte di aree di lavoro di Power BI. Sarà necessario caricare le modifiche al file PBIX nelle raccolte di aree di lavoro di Power BI. Questo è dovuto al fatto che non è disponibile un gateway. 

## <a name="benefits-of-using-directquery"></a>Vantaggi di DirectQuery

L'uso di **DirectQuery**presenta due vantaggi principali:

* **DirectQuery** consente di creare visualizzazioni su set di dati di grandi dimensioni per cui sarebbe altrimenti impossibile importare prima tutti i dati.
* Le modifiche ai dati sottostanti possono richiedere un aggiornamento dei dati e, per alcuni report, l'esigenza di visualizzare i dati correnti può richiedere trasferimenti di grandi quantità di dati, rendendo la reimportazione dei dati impossibile. I report **DirectQuery** usano invece sempre dati correnti.

## <a name="limitations-of-directquery"></a>Limitazioni di DirectQuery

L'uso di **DirectQuery**presenta alcune limitazioni:

* Tutte le tabelle devono provenire da un database singolo.
* Se la query è troppo complessa, si verifica un errore. Per correggere l'errore è necessario eseguire il refactoring della query per renderla meno complessa. Se la query deve essere complessa, è necessario importare i dati anziché usare **DirectQuery**.
* Il filtro di relazione è limitato a un'unica direzione, piuttosto che ad entrambe le direzioni.
* Non è possibile modificare il tipo di dati di una colonna.
* Per impostazione predefinita, alle espressioni DAX valide per le misure sono imposte limitazioni. Vedere [DirectQuery e misure](#measures).

<a name="measures"/>

## <a name="directquery-and-measures"></a>DirectQuery e misure
Per garantire che le prestazioni delle query inviate all'origine dati sottostante siano accettabili, vengono imposte limitazioni alle misure. In **Power BI Desktop** gli utenti esperti possono scegliere di ignorare questa limitazione selezionando **File > Opzioni e impostazioni > Opzioni**. Nella finestra di dialogo **Opzioni** scegliere **DirectQuery** e selezionare l'opzione **Consenti misure senza limitazioni in modalità DirectQuery**. Se tale opzione viene selezionata, è possibile usare qualsiasi espressione DAX valida per una misura. È tuttavia necessario sapere che alcune espressioni che funzionano bene quando i dati sono importati possono invece rallentare le query all'origine back-end se si usa la modalità **DirectQuery**. 

## <a name="see-also"></a>Vedere anche

* [Introduzione alle raccolte di aree di lavoro di Microsoft Power BI](get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)

