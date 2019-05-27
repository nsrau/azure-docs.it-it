---
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: lahugh
ms.openlocfilehash: bd51eabcff412de4928c682683b2a69b3e82e601
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127364"
---
### <a name="retrieve-output-files"></a>Recuperare i file di output

È possibile usare il portale di Azure per scaricare i file MP3 di output generati dalle attività ffmpeg. 

1. Fare clic su **Tutti i servizi** > **Account di archiviazione** e quindi fare clic sul nome dell'account di archiviazione.
2. Fare clic su **BLOB** > *output*.
3. Fare clic con il pulsante destro del mouse su uno dei file MP3 di output e quindi scegliere **Scarica**. Seguire i prompt nel browser per aprire o salvare il file.

![Scaricare il file di output](./media/batch-common-tutorial-download/download.png)

Anche se non viene mostrato in questo esempio, è anche possibile scaricare i file a livello di codice dai nodi di calcolo o dal contenitore di archiviazione.
