---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/01/2020
ms.author: alkohli
ms.openlocfilehash: d099e33e7b35381f5404c9f8964d3ea90d4f3908
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959477"
---
Il passaggio finale consiste nel preparare il dispositivo per la spedizione. In questo passaggio tutte le condivisioni del dispositivo vengono portate offline. Non è possibile accedere alle condivisioni dopo l'avvio del processo.

> [!IMPORTANT]
> La preparazione per la spedizione è necessaria perché contrassegna i dati non conformi alle convenzioni di denominazione di Azure. L'esecuzione di questo passaggio impedisce che si verifichino potenziali errori di caricamento dei dati a causa di dati non conformi.

1. Selezionare **Prepara per la spedizione** e fare clic su **Avvia preparazione**. Per impostazione predefinita, i checksum vengono calcolati durante la copia dei dati. L'opzione Prepara per la spedizione completa il calcolo dei checksum e crea l'elenco di file (*file BOM*). Il calcolo dei checksum può richiedere da alcune ore a diversi giorni a seconda delle dimensioni dei dati. 
   
    ![Preparare per la spedizione 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Se per qualsiasi motivo si vuole interrompere la preparazione del dispositivo, fare clic su **Arresta preparazione**. È possibile riprendere la preparazione per la spedizione in un secondo momento.
        
    ![Preparare per la spedizione 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. La preparazione per la spedizione viene avviata e le condivisioni del dispositivo passano alla modalità offline. <!--You see a reminder to download the shipping label once the device is ready.--> Al termine della preparazione, lo stato del dispositivo viene aggiornato a *Pronto per la spedizione* e il dispositivo viene bloccato.
        
    ![Preparare per la spedizione 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Se si vogliono copiare altri dati nel dispositivo, è possibile sbloccare il dispositivo, copiare altri dati e ripetere la preparazione per la spedizione.

    In caso di errori in questo passaggio, lo stato viene aggiornato in *Analisi completata con errori*. Sbloccare il dispositivo e passare alla pagina **Connetti e copia**, scaricare l'elenco dei problemi e risolvere gli errori.

    ![Preparare per la spedizione 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

    Dopo aver risolto gli errori, eseguire **Prepara per la spedizione**.

4. Dopo aver completato la preparazione per la spedizione (senza errori), seguire questa procedura:

    1. Prendere nota del numero di riferimento del completamento. A seconda del paese di residenza, questo numero potrebbe essere necessario per diverse operazioni.
    2. Scaricare l'elenco dei file (noto anche come manifesto) copiati in questo processo. In seguito è possibile usare questo elenco per verificare i file caricati in Azure. Per altre informazioni, vedere [Esaminare i file BOM durante la preparazione per la spedizione](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
        ![Preparare per la spedizione 1](media/data-box-prepare-to-ship/prepare-to-ship5.png)
    3. Selezionare e scaricare le istruzioni per la spedizione del dispositivo. Le istruzioni per la spedizione variano a seconda del paese di residenza.
    4. Se il display E-ink non visualizza l'etichetta indirizzo, è possibile scaricare qui l'etichetta per la spedizione inversa. 

5. Arrestare il dispositivo. Passare alla pagina **Arresta o riavvia** e fare clic su **Arresta**. Alla richiesta di conferma fare clic su **OK** per continuare.

6. Rimuovere i cavi. Il passaggio successivo consiste nella spedizione del dispositivo a Microsoft.
