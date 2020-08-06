---
title: Ciclo di vita del supporto Azure Storage Explorer | Microsoft Docs
description: Panoramica dei criteri e del ciclo di vita del supporto per Azure Storage Explorer
services: storage
author: MRayermannMSFT
manager: jinglouMSFT
ms.service: storage
ms.topic: article
ms.date: 07/10/2020
ms.author: marayerm
ms.openlocfilehash: 30eaa9b9f8700877607af047e5e64eca65471545
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87759598"
---
# <a name="azure-storage-explorer-support-lifecycle-and-policy"></a>Ciclo di vita del supporto Azure Storage Explorer e criteri

Questo documento illustra il ciclo di vita del supporto e i criteri per Azure Storage Explorer.

## <a name="update-schedule-and-process"></a>Aggiornare la pianificazione e il processo

Azure Storage Explorer viene rilasciato da quattro a sei volte all'anno. Microsoft può anche ignorare questa pianificazione per rilasciare correzioni per i problemi critici.

Storage Explorer controlla la disponibilità di aggiornamenti ogni ora e li scarica quando sono disponibili. Per avviare il processo di installazione, tuttavia, è necessaria l'accettazione dell'utente. Se gli utenti scelgono di eseguire l'aggiornamento in un momento diverso, possono verificare manualmente la presenza di aggiornamenti. In Windows e Linux, gli utenti possono verificare la disponibilità di aggiornamenti selezionando **Controlla aggiornamenti** **dal menu?** . In macOS, il **controllo della disponibilità di aggiornamenti** è disponibile nel **menu dell'app**. Gli utenti possono anche accedere direttamente alla pagina di download [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per scaricare e installare la versione più recente.

Si consiglia di usare sempre le versioni più recenti di Storage Explorer. Se un problema impedisce l'aggiornamento alla versione più recente di Storage Explorer, aprire un problema in [GitHub](https://github.com/microsoft/AzureStorageExplorer).

## <a name="support-lifecycle"></a>Ciclo di vita del supporto

Storage Explorer è disciplinato dal [criterio del ciclo](https://support.microsoft.com/help/30881/modern-lifecycle-policy)di vita moderno. Si prevede che gli utenti mantengano l'installazione dei Storage Explorer aggiornati. Rimanere sempre aggiornati garantisce agli utenti le funzionalità più recenti, i miglioramenti delle prestazioni, la sicurezza e l'affidabilità dei servizi.

A partire dalla versione 1.14.1, qualsiasi versione Storage Explorer successiva a 12 mesi sarà considerata non supportata. Tutte le versioni precedenti a 1.14.1 verranno considerate fuori dal supporto a partire dal 14 luglio 2021. Le versioni non supportate non sono più in grado di funzionare completamente come previsto. Per un elenco di tutte le versioni, la data di rilascio e la data di fine del supporto, vedere la pagina relativa alle [versioni](#releases).

A partire dalla versione 1.13.0, viene visualizzato un avviso in-app quando una versione di circa un mese non è più supportata. L'avviso invita gli utenti a eseguire l'aggiornamento alla versione più recente di Storage Explorer. Quando una versione non è più supportata, l'avviso in-app verrà visualizzato a ogni avvio.

## <a name="releases"></a>Versioni

Questa tabella descrive la data di rilascio e la data di fine del supporto per ogni versione di Azure Storage Explorer.

| Versione Storage Explorer  | Data di rilascio       | Data di fine del supporto |
|:-------------------------:|:------------------:|:-------------------:|
| v 1.14.2                   | 16 luglio 2020      | 16 luglio 2021       |
| v 1.14.1                   | 14 luglio 2020      | 14 luglio 2021       |
| v 1.14.0                   | 24 giugno 2020      | 14 luglio 2021       |
| v 1.13.1                   | 18 maggio 2020       | 14 luglio 2021       |
| v 1.13.0                   | 1 maggio 2020        | 14 luglio 2021       |
| v 1.12.0                   | 16 gennaio 2020   | 14 luglio 2021       |
| v 1.11.2                   | 17 dicembre 2019  | 14 luglio 2021       |
| v 1.11.1                   | 20 novembre 2019  | 14 luglio 2021       |
| v 1.11.0                   | 4 novembre 2019   | 14 luglio 2021       |
| v 1.10.1                   | 19 settembre 2019 | 14 luglio 2021       |
| v 1.10.0                   | 12 settembre 2019 | 14 luglio 2021       |
| v1.9.0                    | 1 ° luglio 2019       | 14 luglio 2021       |
| v 1.8.1                    | 10 maggio 2019       | 14 luglio 2021       |
| v 1.8.0                    | 2 maggio 2019        | 14 luglio 2021       |
| v 1.7.0                    | 5 marzo 2019      | 14 luglio 2021       |
| v 1.6.2                    | 8 gennaio 2019    | 14 luglio 2021       |
| v1.6.1 incorporato                    | 18 dicembre 2018  | 14 luglio 2021       |
| v 1.6.0                    | 4 dicembre 2018   | 14 luglio 2021       |
| versione 1.5.0                    | 29 ottobre 2018   | 14 luglio 2021       |
| v 1.4.4                    | 15 ottobre 2018   | 14 luglio 2021       |
| v 1.4.2                    | 24 settembre 2018 | 14 luglio 2021       |
| v 1.4.1                    | 28 agosto 2018    | 14 luglio 2021       |
| v 1.3.1                    | 11 luglio 2018      | 14 luglio 2021       |
| v 1.2.0                    | 12 giugno 2018      | 14 luglio 2021       |
| v 1.1.0                    | 9 maggio 2018        | 14 luglio 2021       |
| v 1.0.0 (e versioni precedenti)        | 16 aprile 2018     | 14 luglio 2021       |
