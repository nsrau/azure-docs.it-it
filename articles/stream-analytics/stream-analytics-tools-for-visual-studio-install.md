---
title: Installare gli strumenti di Analisi di flusso di Azure per Visual Studio
description: Questo articolo descrive i requisiti di installazione e come configurare gli strumenti di Analisi di flusso di Azure per Visual Studio.This article describes installation requirements and how to set up the Azure Stream Analytics tools for Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: fa3990d2239da71fb27f4c9a06699f758fcfe7a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354374"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Installare gli strumenti di Analisi di flusso di Azure per Visual Studio

Visual Studio 2019 e Visual Studio 2017 supportano Azure Data Lake e strumenti di Analisi di flusso. Questo articolo descrive come installare e disinstallare gli strumenti.

Per altre informazioni sull'uso degli strumenti, vedere [Guida introduttiva: Creare un processo di Analisi di flusso](stream-analytics-quick-create-vs.md)di Azure usando Visual Studio.

## <a name="install"></a>Installazione di

Gli strumenti sono supportati dalle edizioni Professional e Community di Visual Studio Enterprise (Ultimate/Premium), Professional e Community. Express Edition e Visual Studio per Mac non li supportano.

Si consiglia Visual Studio 2019.

### <a name="install-for-visual-studio-2019-and-2017"></a>Installare per Visual Studio 2019 e 2017<a name="recommended-visual-studio-2019-and-2017"></a>

Gli strumenti di Analisi dei dati di Azure fanno parte dei carichi di lavoro di sviluppo e **archiviazione e elaborazione** dei dati di **Azure.Azure** Data Lake and Stream Analytics Tools are part of the Azure development and Data storage and processing workloads. Abilitare uno di questi due carichi di lavoro durante l'installazione. Se Visual Studio è già installato, selezionare **Strumenti** > **Get Tools and Features** per aggiungere carichi di lavoro.

Scaricare [Visual Studio 2019 (Anteprima 2 o versioni successive) o Visual Studio 2017 (15.3 o versione successiva)](https://www.visualstudio.com/) e seguire le istruzioni per l'installazione.

Selezionare il carico di lavoro **di archiviazione ed elaborazione dei dati** come illustrato di seguito:Select the Data storage and processing workload as shown:

![Il carico di lavoro Elaborazione ed archiviazione dati è selezionato](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Selezionare il carico di lavoro di sviluppo di Azure come illustrato di seguito:Select the **Azure development** workload as shown:

![Il carico di lavoro Sviluppo di Azure è selezionato](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

Dopo aver aggiunto il carico di lavoro, aggiornare gli strumenti. Questa procedura fa riferimento a Visual Studio 2019:This procedure refers to Visual Studio 2019:

1. Selezionare **Estensioni** > **per gestire le estensioni**.

1. In **Gestisci estensioni**selezionare **Aggiornamenti** e scegliere Azure Data Lake e Strumenti **analisi flusso**.

1. Selezionare **Aggiorna** per installare l'estensione più recente.

![Estensioni e aggiornamenti di Visual Studio](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Installare per Visual Studio 2015 e 2013<a name="visual-studio-2015-2013"></a>

Gli strumenti sono supportati dalle edizioni Professional e Community di Visual Studio Enterprise (Ultimate/Premium), Professional e Community. L'edizione Express non li supporta.

* Installare Visual Studio 2015 o Visual Studio 2013 Update 4.
* Installare Microsoft Azure SDK per .NET versione 2.7.1 o successiva usando [Installazione guidata piattaforma Web Microsoft](https://www.microsoft.com/web/downloads/platform.aspx).
* Installare [Microsoft Azure Data Lake e Strumenti analisi di flusso per Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Aggiornamento<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

Per Visual Studio 2019 e Visual Studio 2017, un promemoria di nuova versione viene visualizzato come una notifica di Visual Studio.For Visual Studio 2019 and Visual Studio 2017, a new version reminder shows up as a Visual Studio notification.

Per Visual Studio 2015 e Visual Studio 2013, gli strumenti verificano automaticamente la presenza di nuove versioni. Seguire le istruzioni per installare la versione più recente.

## <a name="uninstall"></a>Disinstallare

È possibile disinstallare Azure Data Lake e gli strumenti di analisi di flusso. Per Visual Studio 2019 o Visual Studio 2017, selezionare **Strumenti** > **Get Tools and Features**. In **Modifica**deselezionare **Azure Data Lake e Strumenti analisi flusso**. Viene visualizzato nel carico di lavoro **di archiviazione ed elaborazione dei dati** o nel carico di lavoro di sviluppo di Azure.It appears under the Data storage and processing workload or the **Azure development** workload.

Per eseguire la disinstallazione da Visual Studio 2015 o Visual Studio 2013, passare a**Programmi e funzionalità del**Pannello di >  **controllo**. Disinstallare **Microsoft Azure Data Lake e Strumenti analisi flusso per Visual Studio**.
