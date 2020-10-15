---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/19/2020
ms.author: alkohli
ms.openlocfilehash: 97491000c63c966c3d8159b9361fcb60e062aa49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86208799"
---
Il passaggio finale consiste nel preparare il dispositivo per la spedizione. In questo passaggio tutte le condivisioni del dispositivo vengono portate offline. Non è possibile accedere alle condivisioni dopo l'avvio del processo. È anche possibile scegliere di pulire il dispositivo, per cui i dati locali al suo interno vengono cancellati in modo permanente. Al termine del passaggio, sul display E-ink verrà visualizzata l'etichetta indirizzo.

> [!IMPORTANT]
> - È possibile scegliere di cancellare i dati del dispositivo in modo permanente in questo passaggio e pulire il dispositivo. I dati rimarranno nell'account di archiviazione di Azure e accumuleranno addebiti. È consigliabile eliminare questi dati solo dopo aver verificato che ne sia stata completata la copia in un server locale.

1. Passare a **Prepara per la spedizione** e selezionare **Avvia preparazione**. 
   
    ![Preparare per la spedizione 1](media/data-box-export-prepare-to-ship/prepare-to-ship1.png)

 
2. La preparazione per la spedizione viene avviata e le condivisioni del dispositivo passano alla modalità offline. Per impostazione predefinita, viene eseguita la pulizia del dispositivo e Data Box cancella i dati nei dischi. 


    ![Preparare per la spedizione 2](media/data-box-export-prepare-to-ship/prepare-to-ship2.png)

    È possibile rifiutare esplicitamente la procedura di pulizia deselezionando la casella combinata. In questo caso, i dati del dispositivo verranno cancellati in un secondo momento nel data center.

    ![Preparare per la spedizione 3](media/data-box-export-prepare-to-ship/prepare-to-ship3.png)


3. Una volta avviata la procedura **Prepara per la spedizione**, verrà visualizzata una notifica di stato con un indicatore di stato.

    ![Preparare per la spedizione 4](media/data-box-export-prepare-to-ship/prepare-to-ship4.png)

4. Una volta completata la preparazione per la spedizione e la pulizia del dispositivo (se selezionata), sul display E-ink verrà visualizzata anche l'etichetta indirizzo. 

    Verrà anche visualizzato un numero di riferimento sul completamento, che viene usato per diversi motivi, a seconda del paese in cui si risiede. È anche possibile visualizzare le istruzioni per la spedizione di reso. A seconda del tipo di spedizione scelto, ossia gestita da Microsoft o autonomamente, le istruzioni possono variare. 
        
    ![Preparare per la spedizione 5](media/data-box-export-prepare-to-ship/prepare-to-ship5.png)


5. Arrestare il dispositivo. Individuare il pulsante di accensione sul pannello operativo anteriore del dispositivo. Spegnere il dispositivo.

    ![Pulsante di accensione del Data Box](media/data-box-export-prepare-to-ship/data-box-powered-door-open.png)

6. Rimuovere i cavi. Il passaggio successivo consiste nella spedizione del dispositivo a Microsoft.
