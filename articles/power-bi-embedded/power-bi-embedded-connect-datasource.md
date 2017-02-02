---
title: Microsoft Power BI Embedded - Connessione a un&quot;origine dati
description: Microsoft Power BI Embedded - Connettersi a origini dati
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 2a4caeb3-255d-4215-9554-0ca8e3568c13
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 5c4b5e09ac5e336504be32533ad011aad102af75
ms.openlocfilehash: fdc0d76741d5078978e1bfeadd156e84cac3a48a


---
# <a name="connect-to-a-data-source"></a>Connettersi a un'origine dati
Con **Power BI Embedded**è possibile incorporare report in proprie app. Quando un report di Power BI viene incorporato in un'app, il report si connette ai dati sottostanti tramite l'**importazione** di una copia dei dati o tramite **connessione** diretta all'origine dati con **DirectQuery**.

Di seguito sono spiegate le differenze tra l'**importazione** e **DirectQuery**.

| Importa | DirectQuery |
| --- | --- |
| Tabelle, colonne *e dati* vengono importati o copiati nel set di dati del report. Per visualizzare le modifiche apportate ai dati sottostanti, è necessario aggiornare o importare nuovamente un set di dati completo e corrente. |Vengono importate o copiate nel set di dati del report solo *tabelle e colonne* . Vengono visualizzati sempre i dati più recenti. |

Con Power BI Embedded è attualmente possibile usare DirectQuery con le origini dati cloud ma non su origini dati locali.

> [!NOTE]
> Il gateway dati locale non è al momento supportato con Power BI Embedded. Non è quindi possibile usare DirectQuery con origini dati locali.

## <a name="supported-data-sources"></a>Origini dati supportate

**DirectQuery**
* Database SQL di Azure
* Azure SQL Data Warehouse

**Import (Importa) (Import (Importa)a)**

È possibile usare per l'importazione tutte le origini dati disponibili in Power BI Desktop. Tali dati **non** potranno essere aggiornati in Power BI Embedded. Sarà necessario caricare le modifiche al file PBIX in Power BI Embedded. Questo è dovuto al fatto che non è disponibile un gateway. 

## <a name="benefits-of-using-directquery"></a>Vantaggi di DirectQuery
L'uso di **DirectQuery**presenta due vantaggi principali:

* **DirectQuery** consente di creare visualizzazioni su set di dati molto grandi nei casi in cui sarebbe impossibile importare prima tutti i dati.
* Le modifiche ai dati sottostanti possono richiedere un aggiornamento dei dati. Nel caso di alcuni report, per visualizzare i dati correnti, può essere quindi necessario trasferire molti dati, rendendo la re-importazione dei dati impossibile. I report **DirectQuery** usano invece sempre dati correnti.

## <a name="limitations-of-directquery"></a>Limitazioni di DirectQuery
   L'uso di **DirectQuery**presenta alcune limitazioni:

* Tutte le tabelle devono provenire da un database singolo.
* Se la query è troppo complessa, si verificherà un errore. Per correggere l'errore è necessario eseguire il refactoring della query per renderla meno complessa. Se la query deve essere necessariamente complessa, i dati saranno importati e non sarà possibile usare la modalità **DirectQuery**.
* Il filtro di relazione è limitato a un'unica direzione, piuttosto che ad entrambe le direzioni.
* Non è possibile modificare il tipo di dati di una colonna.
* Per impostazione predefinita, alle espressioni DAX valide per le misure sono imposte limitazioni. Vedere [DirectQuery e misure](#measures).

<a name="measures"/>

## <a name="directquery-and-measures"></a>DirectQuery e misure
Per garantire che le prestazioni delle query inviate all'origine dati sottostante siano accettabili, vengono imposte limitazioni alle misure. In **Power BI Desktop** gli utenti esperti possono scegliere di ignorare questa limitazione selezionando **File > Opzioni e impostazioni > Opzioni**. Nella finestra di dialogo **Opzioni** scegliere **DirectQuery** e selezionare l'opzione **Consenti misure senza limitazioni in modalità DirectQuery**. Se tale opzione viene selezionata, è possibile usare qualsiasi espressione DAX valida per una misura. È tuttavia necessario sapere che alcune espressioni che funzionano molto bene quando i dati sono importati possono invece rallentare considerevolmente le query all'origine back-end se si usa la modalità **DirectQuery** . 

## <a name="see-also"></a>Vedere anche
* [Introduzione a Microsoft Power BI Embedded](power-bi-embedded-get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)




<!--HONumber=Dec16_HO3-->


