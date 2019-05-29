---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/25/2019
ms.author: alkohli
ms.openlocfilehash: 1d52117440028c75b249f469f2b3576c2ab1c5c5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150480"
---
Il passaggio finale consiste nel preparare il dispositivo per la spedizione. In questo passaggio tutte le condivisioni del dispositivo vengono portate offline. Non è possibile accedere alle condivisioni dopo l'avvio del processo.

> [!IMPORTANT]
> La preparazione per la spedizione è necessaria perché contrassegna i dati non conformi alle convenzioni di denominazione di Azure. Ignorare questo passaggio potrebbe generare potenziali errori di caricamento dei dati, a causa di dati non conformi.

1. Selezionare **Prepara per la spedizione** e fare clic su **Avvia preparazione**. Per impostazione predefinita, i checksum vengono calcolati durante la copia dei dati. L'opzione Prepara per la spedizione completa il calcolo dei checksum e crea l'elenco di file (*file BOM*). Il calcolo dei checksum può richiedere da alcune ore a diversi giorni a seconda delle dimensioni dei dati. 
   
    ![Preparare per la spedizione 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Se per qualsiasi motivo si vuole interrompere la preparazione del dispositivo, fare clic su **Arresta preparazione**. È possibile riprendere la preparazione per la spedizione in un secondo momento.
        
    ![Preparare per la spedizione 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. La preparazione per la spedizione viene avviata e le condivisioni del dispositivo passano alla modalità offline. Quando il dispositivo è pronto, verrà visualizzato un promemoria per il download dell'etichetta per la spedizione.

    ![Promemoria per il download dell'etichetta per la spedizione](media/data-box-prepare-to-ship/download-shipping-label-reminder.png)

3. Al termine della preparazione, lo stato del dispositivo viene aggiornato a *Pronto per la spedizione* e il dispositivo viene bloccato.
        
    ![Preparare per la spedizione 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Se si vogliono copiare altri dati nel dispositivo, è possibile sbloccare il dispositivo, copiare altri dati e ripetere la preparazione per la spedizione.

    Se si verificano errori in questo passaggio, sarà necessario scaricare il log degli errori e risolverli. Dopo aver risolto gli errori, eseguire **Prepara per la spedizione**.

4. Al termine della preparazione per la spedizione (senza errori), scaricare l'elenco dei file copiati nel processo, denominato anche manifesto. In seguito è possibile usare questo elenco per verificare i file caricati in Azure. Per altre informazioni, vedere [Esaminare i file BOM durante la preparazione per la spedizione](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![Preparare per la spedizione 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

5. Arrestare il dispositivo. Passare alla pagina **Arresta o riavvia** e fare clic su **Arresta**. Alla richiesta di conferma fare clic su **OK** per continuare.

6. Rimuovere i cavi. Il passaggio successivo consiste nella spedizione del dispositivo a Microsoft.
