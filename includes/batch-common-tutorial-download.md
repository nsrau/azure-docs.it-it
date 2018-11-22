---
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: a8ea55a40f1ee4681b6aec147e02b7bce6f1d7cf
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279699"
---
### <a name="retrieve-output-files"></a>Recuperare i file di output

È possibile usare il portale di Azure per scaricare i file MP3 di output generati dalle attività ffmpeg. 

1. Fare clic su **Tutti i servizi** > **Account di archiviazione** e quindi fare clic sul nome dell'account di archiviazione.
2. Fare clic su **BLOB** > *output*.
3. Fare clic con il pulsante destro del mouse su uno dei file MP3 di output e quindi scegliere **Scarica**. Seguire i prompt nel browser per aprire o salvare il file.

![Scaricare il file di output](./media/batch-common-tutorial-download/download.png)

Anche se non viene mostrato in questo esempio, è anche possibile scaricare i file a livello di codice dai nodi di calcolo o dal contenitore di archiviazione.
