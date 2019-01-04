---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/13/2018
ms.author: alkohli
ms.openlocfilehash: 4efaaa0e3a1439a94a693aed13bf5c60563b4b29
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53549749"
---
Il passaggio finale consiste nel preparare il dispositivo per la spedizione. In questo passaggio tutte le condivisioni del dispositivo vengono portate offline. Una volta avviata la preparazione del dispositivo per la spedizione, le condivisioni non sono più accessibili.
1. Selezionare **Prepara per la spedizione** e fare clic su **Avvia preparazione**. 
   
    ![Preparare per la spedizione 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

2. Per impostazione predefinita, i checksum vengono calcolati inline durante la preparazione per la spedizione. Il calcolo dei checksum potrebbe richiedere alcuni minuti in base alle dimensioni dei dati. Fare clic su **Avvia preparazione**.
    1. Le condivisioni del dispositivo vengono portate offline e il dispositivo viene bloccato durante la preparazione per la spedizione.
        
        ![Preparare per la spedizione 1](media/data-box-prepare-to-ship/prepare-to-ship2.png) 
   
    2. Completata la preparazione, lo stato del dispositivo viene aggiornato a *Pronto per la spedizione*. 
        
        ![Preparare per la spedizione 1](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    3. Scaricare l'elenco dei file (manifesto) copiati in questo processo. In seguito è possibile usare questo elenco per verificare i file caricati in Azure.
        
        ![Preparare per la spedizione 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

3. Arrestare il dispositivo. Passare alla pagina **Arresta o riavvia** e fare clic su **Arresta**. Alla richiesta di conferma fare clic su **OK** per continuare.
4. Rimuovere i cavi. Il passaggio successivo consiste nella spedizione del dispositivo a Microsoft.
