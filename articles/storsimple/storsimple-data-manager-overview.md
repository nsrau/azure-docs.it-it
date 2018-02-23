---
title: Panoramica di Microsoft Azure StorSimple Data Manager | Microsoft Docs
description: L'articolo presenta una panoramica del servizio StorSimple Data Manager
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: vidarmsft
ms.openlocfilehash: 8b0ff2c100878e568e0a4c67e79864006512bd78
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="storsimple-data-manager-solution-overview"></a>Panoramica della soluzione StorSimple Data Manager

## <a name="overview"></a>Panoramica

Microsoft Azure StorSimple usa l'archiviazione cloud come un'estensione della soluzione locale e organizza automaticamente i dati in livelli tra l'archiviazione locale e il cloud. I dati vengono archiviati nel cloud in un formato compresso e non duplicato per ottenere la massima efficienza e ridurre i costi. Poiché vengono archiviati in formato StorSimple, i dati non sono facilmente utilizzabili da altre applicazioni cloud eventualmente necessarie.

StorSimple Data Manager consente di accedere con facilità ai dati in formato StorSimple e usarli nel cloud. A tale scopo, questa soluzione trasforma il formato StorSimple in BLOB e file nativi che è possibile usare con altri servizi, ad esempio Servizi multimediali di Azure, Azure HDInsights e Azure Machine Learning.

Questo articolo presenta una panoramica della soluzione StorSimple Data Manager. Viene inoltre descritto come usare il servizio per scrivere applicazioni che utilizzino dati di StorSimple e altri servizi di Azure nel cloud.

## <a name="how-it-works"></a>Funzionamento

Il servizio StorSimple Data Manager identifica i dati di StorSimple nel cloud da un dispositivo StorSimple serie 8000 locale. Questi dati sono archiviati nel formato StorSimple compresso e non duplicato. Il servizio Data Manager fornisce le API per estrarre i dati in formato StorSimple e trasformarli in altri formati, ad esempio BLOB e File di Azure. I dati così trasformati saranno quindi immediatamente utilizzabili da Azure HDInsight e Servizi multimediali di Azure. La trasformazione dei dati consente pertanto a questi servizi di eseguire operazioni sui dati di StorSimple trasformati dal dispositivo StorSimple serie 8000 locale. Questo flusso è illustrato nel diagramma seguente.

![Diagramma di alto livello](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Casi d'uso di Data Manager

È possibile usare Data Manager con Funzioni di Azure, Automazione di Azure e Azure Data Factory per gestire l'esecuzione di flussi di lavoro sui dati archiviati in StorSimple. Può ad esempio essere utile elaborare contenuti multimediali archiviati in StorSimple con Servizi multimediali di Azure o eseguire un algoritmo di Machine Learning su tali dati oppure attivare un cluster Hadoop per analizzare i dati di StorSimple. Combinando l'ampia gamma di servizi disponibili in Azure con l'archiviazione StorSimple, è possibile sfruttare al meglio tutte le potenzialità offerte dai propri dati.


## <a name="region-availability"></a>Aree di disponibilità

StorSimple Data Manager è disponibile nelle sette aree seguenti:

 - Asia sudorientale
 - Stati Uniti orientali
 - Stati Uniti occidentali
 - Stati Uniti occidentali 2
 - Stati Uniti centro-occidentali
 - Europa settentrionale
 - Europa occidentale

StorSimple Data Manager può tuttavia essere usato per trasformare dati archiviati nelle aree seguenti: 

![Aree disponibili per i dati](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions.png)

Questo set di aree è più ampio perché la distribuzione delle risorse in una delle aree del primo elenco è in grado di attivare il processo di trasformazione sui dati presenti nelle aree incluse nel secondo elenco. È pertanto possibile trasformare i dati tramite questo servizio purché si trovino in una qualsiasi delle 26 aree.


## <a name="choosing-a-region"></a>Scelta di un'area

Tenere presente quanto segue:
 - L'account di archiviazione di origine, ovvero quello associato al dispositivo StorSimple, e l'account di archiviazione di destinazione, in cui i dati verranno archiviati in formato nativo, devono trovarsi nella stessa area di Azure.
 - Data Manager e la definizione del processo devono essere attivati nell'area che contiene l'account di archiviazione StorSimple. Se questo non è possibile, attivare Data Manager nell'area di Azure più vicina e quindi creare la definizione del processo nella stessa area dell'account di archiviazione StorSimple. 

    Se l'account di archiviazione StorSimple non è presente in una delle 26 aree che supportano la creazione di definizioni di processo, è consigliabile non eseguire StorSimple Data Manager poiché si riscontreranno lunghi tempi di latenza e costi del traffico in uscita potenzialmente elevati.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

StorSimple Data Manager richiede la chiave di crittografia dei dati del servizio per eseguire la trasformazione dal formato StorSimple a quello nativo. La chiave di crittografia dei dati del servizio viene generata quando il primo dispositivo esegue la registrazione al servizio StorSimple. Per altre informazioni su questa chiave, vedere [Sicurezza di StorSimple](storsimple-8000-security.md).

La chiave di crittografia dei dati del servizio fornita come input viene archiviata in un insieme di credenziali delle chiavi generato al momento della creazione di un'istanza del servizio Data Manager. L'insieme di credenziali si trova nella stessa area di Azure in cui è presente StorSimple Data Manager. La chiave viene eliminata contemporaneamente all'istanza del servizio Data Manager.

Questa chiave viene usata per eseguire la trasformazione dalle risorse di calcolo, che si trovano nella stessa area di Azure in cui è presente la definizione del processo. L'area può coincidere o meno con quella in cui viene attivato Data Manager.

Se l'area di Data Manager è diversa da quella della definizione del processo, è importante comprendere quali dati o metadati si trovano in ognuna delle aree. Il diagramma seguente illustra l'effetto risultante dalla presenza del servizio Data Manager e della definizione del processo in aree diverse.

![Servizio e definizione del processo in aree diverse](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="next-steps"></a>Passaggi successivi

[Usare l'interfaccia utente di StorSimple Data Manager per la trasformazione dei dati](storsimple-data-manager-ui.md).