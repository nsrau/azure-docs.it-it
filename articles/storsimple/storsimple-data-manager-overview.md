---
title: Panoramica di Microsoft Azure StorSimple Data Manager | Documentazione Microsoft
description: L'articolo fornisce una panoramica del servizio StorSimple Data Manager (anteprima privata)
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
ms.date: 11/22/2016
ms.author: vidarmsft
ms.translationtype: Human Translation
ms.sourcegitcommit: 32fcca540c1db317c5f11e94b2ee3359cadcc806
ms.openlocfilehash: aedb44610fe57055851538b9dbdb810e66e58d73
ms.contentlocale: it-it
ms.lasthandoff: 11/28/2016

---

# <a name="storsimple-data-manager-overview-private-preview"></a>Panoramica di StorSimple Data Manager (anteprima privata)

## <a name="overview"></a>Overview

Microsoft Azure StorSimple è una soluzione di archiviazione cloud ibrida che risolve le complessità dei dati non strutturati comunemente associate alle condivisioni file. StorSimple usa l'archiviazione cloud come un'estensione della soluzione locale e organizza automaticamente i dati in livelli tra archiviazione locale e archiviazione cloud. La protezione integrata dei dati, con snapshot locali e cloud, elimina la necessità di un'infrastruttura di archiviazione complessa. L'archiviazione e il ripristino di emergenza sono inoltre perfettamente integrati con il cloud che funge da posizione esterna.

Il servizio di trasformazione dati, illustrato nel presente documento, consente di accedere facilmente ai dati di StorSimple nel cloud. Questo servizio fornisce API per estrarre dati da StorSimple e presentarli ad altri servizi di Azure in formati facilmente utilizzabili. I formati supportati nell'anteprima sono BLOB di Azure e asset di Servizi multimediali di Azure. Questa trasformazione consente di associare facilmente servizi quali Servizi multimediali di Azure, Azure HDInsight, Azure Machine Learning e Ricerca di Azure per l'uso dei dati sul dispositivo locale StorSimple serie 8000.

Di seguito viene presentato un diagramma a blocchi di alto livello illustrativo.

![Diagramma di alto livello](./media//storsimple-data-manager-overview/high-level-diagram.png)

Nel documento viene spiegato come iscriversi per un'anteprima privata di questo servizio. Viene inoltre descritto come usare il servizio per scrivere applicazioni che utilizzino dati di StorSimple e altri servizi di Azure nel cloud.

## <a name="sign-up-for-data-manager-preview"></a>Eseguire l'iscrizione per l'anteprima di Data Manager
Prima di eseguire l'iscrizione per il servizio Data Manager, esaminare i prerequisiti seguenti.

### <a name="prerequisites"></a>Prerequisiti

Questo esercizio presuppone che l'utente abbia
* una sottoscrizione di Azure attiva.
* l'accesso a un dispositivo StorSimple serie 8000 registrato
* tutte le chiavi associate con il dispositivo StorSimple serie 8000.

### <a name="sign-up"></a>Iscrizione

StorSimple Data Manager è in modalità di anteprima privata. Per iscriversi a un'anteprima privata di questo servizio, procedere come segue:

1.  Accedere al portale di Azure con l'estensione di StorSimple Data Manager su: [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager). Usare le credenziali di Azure per accedere.

2.  Fare clic sull'icona **+** per creare un servizio. Fare clic su **Archiviazione** e scegliere **Visualizza tutto** nel pannello che si apre.

    ![Ricerca dell'icona di StorSimple Data Manager](./media/storsimple-data-manager-overview/search-data-manager-icon.png)

3. Viene visualizzata l'icona di StorSimple Data Manager.

    ![Selezione dell'icona di StorSimple Data Manager](./media/storsimple-data-manager-overview/select-data-manager-icon.png)

4. Fare clic sull'icona di StorSimple Data Manager, quindi su **Crea**. Selezionare la sottoscrizione che si desidera abilitare per l'anteprima privata e quindi fare clic su **Sign me up!** (Esegui iscrizione)

    ![Esegui iscrizione](./media/storsimple-data-manager-overview/sign-me-up.png)

5. Viene inviata una richiesta di iscrizione, che verrà gestita non appena possibile. Dopo l'abilitazione della sottoscrizione, sarà possibile creare un servizio StorSimple Data Manager.

6. Per accedere facilmente al servizio StorSimple Data Manager, fare clic sull'icona a stella per aggiungerlo ai Preferiti.

    ![Accesso a StorSimple Data Manager](./media/storsimple-data-manager-overview/access-data-managers.png)


## <a name="next-steps"></a>Passaggi successivi

[Usare l'interfaccia utente di StorSimple Data Manager per la trasformazione dei dati](storsimple-data-manager-ui.md).
