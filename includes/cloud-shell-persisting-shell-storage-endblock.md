---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572279"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Trasferire file locali in Cloud Shell
La directory `clouddrive` viene sincronizzata con il pannello di archiviazione del portale di Azure. Usare questo pannello per trasferire file locali da e verso la condivisione file. L'aggiornamento dei file dall'interno di Cloud Shell si riflette nell'interfaccia utente grafica di archiviazione file quando il pannello viene aggiornato.

### <a name="download-files"></a>Download dei file

![Elenco di file locali](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. Nel portale di Azure passare alla condivisione file montata.
2. Selezionare il file di destinazione.
3. Fare clic sul pulsante **Download**.

### <a name="upload-files"></a>Caricare file

![File locali da caricare](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Passare alla condivisione file montata.
2. Selezionare il pulsante **Carica**.
3. Selezionare uno o più file che si desidera caricare.
4. Confermare il caricamento.

Si dovrebbe a questo punto vedere che è possibile accedere ai file nella directory `clouddrive` in Cloud Shell.