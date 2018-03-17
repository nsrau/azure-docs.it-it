---
title: Scaricare ed estrarre il Kit di sviluppo dello Stack di Azure (ASDK) | Documenti Microsoft
description: Viene descritto come scaricare ed estrarre il Kit di sviluppo dello Stack di Azure (ASDK).
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 0cf389c9443a9cff477b884c277d72de27769afc
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="download-and-extract-the-azure-stack-development-kit-asdk"></a>Scaricare ed estrarre il Kit di sviluppo dello Stack di Azure (ASDK)
Dopo aver verificato che il computer host di kit sviluppo soddisfi i requisiti di base per l'installazione di ASDK, il passaggio successivo consiste a scaricare ed estrarre il pacchetto di distribuzione ASDK per ottenere il Cloudbuilder.vhdx.

## <a name="download-the-asdk"></a>Scaricare il ASDK
1. Prima di iniziare il download, assicurarsi che il computer soddisfi i prerequisiti seguenti:

  - Il computer deve disporre almeno 60 GB di spazio su disco disponibile in quattro separati, identiche unità disco rigido locali inoltre per il disco del sistema operativo.
  - [.NET framework 4.6 (o versione successiva)](https://aka.ms/r6mkiy) deve essere installato.

2. [Passare alla pagina di introduzione](https://azure.microsoft.com/overview/azure-stack/try/?v=try) in cui può scaricare il Kit di sviluppo dello Stack di Azure, fornire i dettagli e quindi fare clic su **Invia**.
3. Scaricare ed eseguire il [controllo distribuzione per il Kit di sviluppo di Azure Stack](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) script del controllo prerequisiti. Questo script autonomo viene sottoposto a controlli dei prerequisiti eseguiti dal programma di installazione per il Kit di sviluppo dello Stack di Azure. Fornisce un modo per verificare che vengano soddisfatti i requisiti hardware e software, prima di scaricare il pacchetto più grande per il Kit di sviluppo dello Stack di Azure.
4. In **scaricare il software**, fare clic su **Kit di sviluppo di Azure Stack**.

  > [!NOTE]
  > Il download ASDK (AzureStackDevelopmentKit.exe) è di circa 10GB.

## <a name="extract-the-asdk"></a>Estrarre il ASDK
1. Al termine del download, fare clic su **eseguire** per avviare il Self-extractor ASDK (AzureStackDevelopmentKit.exe).
2. Leggere e accettare il contratto di licenza visualizzati il **contratto di licenza** pagina della procedura guidata di Self-Extractor e quindi fare clic su **Avanti**.
3. Esaminare le informazioni di istruzione sulla privacy visualizzate nella **nota importante** pagina della procedura guidata di Self-Extractor e quindi fare clic su **Avanti**.
4. Selezionare il percorso di file di installazione di Azure Stack da estrarre attivato il **Seleziona posizione di destinazione** pagina della procedura guidata di Self-Extractor e quindi fare clic su **Avanti**. Il percorso predefinito è *cartella corrente*\Azure Kit di sviluppo dello Stack. 
5. Esaminare il riepilogo sul percorso di destinazione di **pronto per l'estrazione** pagina della procedura guidata di Self-Extractor e quindi fare clic su **estrarre** per estrarre CloudBuilder.vhdx (circa 25 GB) e File ThirdPartyLicenses.rtf. Questo processo richiede parecchio tempo.
6. Copiare o spostare il file CloudBuilder.vhdx alla radice dell'unità C:\ (C:\CloudBuilder.vhdx) nel computer host ASDK.

> [!NOTE]
> Dopo aver estratto i file, è possibile eliminare il. File EXE e. File della cartella BIN per recuperare spazio su disco rigido. In alternativa, è possibile eseguire il backup di questi file in modo che non è necessario scaricare di nuovo i file se è necessario ridistribuire il ASDK.


## <a name="next-steps"></a>Passaggi successivi
[Preparare il computer host ASDK](asdk-prepare-host.md)