---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: f5b60d862be0d71f71f770c47d88ad39f2fc6ac7
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "66419959"
---
Il passaggio finale consiste nel preparare il dispositivo per la spedizione. In questo passaggio tutte le condivisioni del dispositivo vengono portate offline. Non è possibile accedere alle condivisioni dopo l'avvio del processo.

> [!IMPORTANT]
> La preparazione per la spedizione è necessaria perché contrassegna i dati non conformi alle convenzioni di denominazione di Azure. Ignorare questo passaggio potrebbe generare potenziali errori di caricamento dei dati, a causa di dati non conformi.

1. Selezionare **Prepara per la spedizione** e fare clic su **Avvia preparazione**. Per impostazione predefinita, i checksum vengono calcolati durante la copia dei dati. L'opzione Prepara per la spedizione completa il calcolo dei checksum e crea l'elenco di file (*file distinta base* o manifesto). Il calcolo dei checksum può richiedere da alcune ore a diversi giorni a seconda delle dimensioni dei dati.
   
    ![Preparare per la spedizione 1](media/data-box-heavy-prepare-to-ship/prepare-to-ship1.png)

    Se per qualsiasi motivo si vuole interrompere la preparazione del dispositivo, fare clic su **Arresta preparazione**. È possibile riprendere la preparazione per la spedizione in un secondo momento.
        
    ![Preparare per la spedizione 2](media/data-box-heavy-prepare-to-ship/prepare-to-ship2.png)
    
2. La preparazione per la spedizione viene avviata e le condivisioni del dispositivo passano alla modalità offline. Quando il dispositivo è pronto, verrà visualizzato un promemoria per il download dell'etichetta per la spedizione.

    ![Promemoria per il download dell'etichetta per la spedizione](media/data-box-heavy-prepare-to-ship/download-shipping-label-reminder.png)

3. Al termine della preparazione, lo stato del dispositivo viene aggiornato a *Pronto per la spedizione* e il dispositivo viene bloccato.
        
    ![Preparare per la spedizione 3](media/data-box-heavy-prepare-to-ship/prepare-to-ship3.png)

    Se si vogliono copiare altri dati nel dispositivo, è possibile sbloccare il dispositivo, copiare altri dati e ripetere la preparazione per la spedizione.

    Se si verificano errori in questo passaggio, sarà necessario scaricare il log degli errori e risolverli. Dopo aver risolto gli errori, eseguire **Prepara per la spedizione**.

4. Al termine della preparazione per la spedizione (senza errori), scaricare l'elenco dei file copiati nel processo (*file distinta base* o manifesto). 

    ![Scaricare l'elenco di file o file distinta base](media/data-box-heavy-prepare-to-ship/download-list-of-files.png)

   In seguito è possibile usare questo elenco per verificare i file caricati in Azure. Per altre informazioni, vedere [Esaminare i file BOM durante la preparazione per la spedizione](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![File distinta base di esempio](media/data-box-heavy-prepare-to-ship/sample-bom-file.png)

5. Arrestare il dispositivo. Passare alla pagina **Arresta o riavvia** e fare clic su **Arresta**. Alla richiesta di conferma fare clic su **OK** per continuare.

    ![Arrestare il primo nodo del dispositivo](media/data-box-heavy-prepare-to-ship/shut-down-device-node.png)

6. Ripetere tutti i passaggi precedenti per l'altro nodo del dispositivo.
7. Dopo aver completato l'arresto del dispositivo, tutti i LED sul retro dovranno essere spenti. Il passaggio successivo consiste nel rimuovere tutti i cavi e spedire il dispositivo a Microsoft.
