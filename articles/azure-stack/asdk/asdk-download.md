---
title: Scaricare ed estrarre Azure Stack Development Kit (ASDK) | Microsoft Docs
description: Viene descritto come scaricare ed estrarre Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 08/10/2018
ms.openlocfilehash: f48c3c9691170df6d460bfe9a6da0e02e4be32c3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58077701"
---
# <a name="download-and-extract-the-azure-stack-development-kit-asdk"></a>Scaricare ed estrarre Azure Stack Development Kit (ASDK)
Dopo aver verificato che il computer host kit di sviluppo soddisfi i requisiti di base per l'installazione di ASDK, il passaggio successivo è per scaricare ed estrarre il pacchetto di distribuzione ASDK per ottenere il Cloudbuilder.vhdx.

## <a name="download-the-asdk"></a>Scaricare il ASDK
1. Prima di avviare il download, assicurarsi che il computer soddisfi i prerequisiti seguenti:

   - Il computer deve avere almeno 60 GB di spazio su disco disponibile in quattro separata, identica unità disco rigido inoltre per il disco del sistema operativo.
   - [.NET framework 4.6 (o versione successiva)](https://dotnet.microsoft.com/download/dotnet-framework-runtime/net46) deve essere installato.

2. [Andare alla pagina di iniziare](https://azure.microsoft.com/overview/azure-stack/try/?v=try) in cui è possibile Scarica Azure Stack Development Kit, fornire i dettagli e quindi fare clic su **Submit**.
3. Scaricare ed eseguire la [controllo di distribuzione per Azure Stack Development Kit](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) script di controllo dei prerequisiti. Questo script autonomo sono illustrati i controlli di pre-requisiti eseguiti dal programma di installazione per Azure Stack Development Kit. Fornisce un modo per verificare che vengano soddisfatti i requisiti hardware e software, prima di scaricare il pacchetto di dimensioni maggiori per Azure Stack Development Kit.
4. Sotto **Download del software**, fare clic su **Azure Stack Development Kit**.

   > [!NOTE]
   > Il download ASDK (AzureStackDevelopmentKit.exe) è di circa 10GB.

## <a name="extract-the-asdk"></a>Estrarre il ASDK
1. Al termine del download, fare clic su **eseguiti** per avviare il Self-extractor ASDK (AzureStackDevelopmentKit.exe).
2. Leggere e accettare il contratto di licenza visualizzati il **contratto di licenza** pagina della procedura guidata di Self-Extractor e quindi fare clic su **successivo**.
3. Esaminare le informazioni di informativa sulla privacy visualizzate nella **avviso importante** pagina della procedura guidata di Self-Extractor e quindi fare clic su **successivo**.
4. Selezionare il percorso di file di installazione di Azure Stack da estrarre on la **Seleziona posizione di destinazione** pagina della procedura guidata di Self-Extractor e quindi fare clic su **successivo**. Il percorso predefinito è *cartella corrente*\Azure Stack Development Kit. 
5. Esaminare il percorso di destinazione riepilogo il **pronto per l'estrazione** pagina della procedura guidata di Self-Extractor e quindi fare clic su **estrarre** estrarre CloudBuilder.vhdx (circa 28 GB) e File ThirdPartyLicenses.rtf. Questo processo richiede alcuni minuti.
6. Copiare o spostare il file CloudBuilder.vhdx alla radice dell'unità C:\ (C:\CloudBuilder.vhdx) nel computer host ASDK.

> [!NOTE]
> Dopo aver estratto i file, è possibile eliminare il. File EXE e. File della cartella BIN per recuperare spazio su disco rigido. In alternativa, è possibile eseguire il backup di questi file in modo che non è necessario scaricare di nuovo i file se si vuole ridistribuire il ASDK.


## <a name="next-steps"></a>Passaggi successivi
[Preparare il computer host ASDK](asdk-prepare-host.md)