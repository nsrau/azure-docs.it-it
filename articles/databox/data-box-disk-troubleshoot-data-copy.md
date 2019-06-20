---
title: Problemi di copia di dati sulla risoluzione dei problemi di Azure Data Box Disk | Microsoft Docs
description: Viene descritto come risolvere gli errori riscontrati durante la copia dei dati in Azure Data Box Disk usando i log.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148348"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Risolvere i problemi di copia dei dati in Azure Data Box Disk

Questo articolo si applica a Microsoft Azure Data Box Disk e viene descritto come risolvere eventuali problemi che viene visualizzato quando si copiano i dati per i dischi. L'articolo riguarda anche i problemi quando si usa lo strumento di copia di divisione.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Problemi di copia dei dati quando si usa un sistema Linux

In questa sezione descrive alcuni dei principali problemi riscontrati quando si usa un client Linux per copiare i dati in dischi.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Problema: Unità montata in modalità di sola lettura
 
**Causa** 

La causa probabile è un file system anomalo.

Rimontare un'unità in modalità di lettura/scrittura impedisce il funzionamento con Data Box Disk. Questo scenario non è supportato da unità decrittografate da dislocker. Il dispositivo potrebbe essere stato rimontato con successo tramite il comando seguente:

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

Anche se il rimontaggio avesse esito positivo, i dati non verranno mantenuti.

**Risoluzione**

Nel sistema Linux, procedere come segue:

1. Installare il `ntfsprogs` pacchetto per l'utilità ntfsfix.
2. Smontare i punti di montaggio specificati per l'unità dallo strumento di sblocco. Il numero di punti di montaggio variano per le unità.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Eseguire `ntfsfix` nel percorso corrispondente. Il numero evidenziato deve essere uguale al passaggio 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Eseguire il comando seguente per rimuovere i metadati della modalità di ibernazione che potrebbero causare il problema di montaggio.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Eseguire una pulizia Smonta.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Eseguire una pulizia unlock e montare.
7. Testare il punto di montaggio scrivendo un file.
8. Smontare e rimontare per convalidare il salvataggio permanente dei file.
9. Continuare con la copia dei dati.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Problema: Errore con i dati non persistenti dopo la copia
 
**Causa** 

Se l'unità non dispone di dati dopo essere stata disinstallata (anche se i dati sono stati copiati all'interno della stessa), è possibile che sia stata rimontata in modalità di lettura/scrittura a seguito del montaggio in modalità di sola lettura.

**Risoluzione**
 
In questo caso, vedere la risoluzione per le [unità montate in sola lettura](#issue-drive-getting-mounted-as-read-only).

Se non era applicabile, copiare i log dalla cartella con lo strumento per sbloccare Data Box Disk e [contattare il supporto tecnico Microsoft](data-box-disk-contact-microsoft-support.md).


## <a name="data-box-disk-split-copy-tool-errors"></a>Errori dello strumento di divisione della copia di Data Box Disk

Nella tabella seguente sono riepilogati i problemi visualizzati quando si usa uno strumento di copia di divisione per dividere i dati su più dischi.

|Messaggio/avvisi di errore |Consigli |
|---------|---------|
|[Info] Recupero di password di BitLocker per il volume: m <br>[Errore] Eccezione rilevata durante il recupero delle chiave di BitLocker per volumi m:<br> La sequenza non contiene elementi.|Questo errore viene generato se il Data Box Disk di destinazione è offline. <br> Usare lo strumento `diskmgmt.msc` per mettere i dischi online.|
|Eccezione [Errore] generata: l'operazione WMI non è riuscita:<br> Metodo=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=Il formato della password di ripristino fornita non è valido. <br>Le password di ripristino di BitLocker sono composte da 48 cifre. <br>Verificare che la password di ripristino sia nel formato corretto e ripetere l'operazione.|Usare lo strumento di sblocco del disco di Data Box per sbloccare prima i dischi, poi riprovare il comando. Per altre informazioni, vedere <li> [Sbloccare il Data Box Disk per i client Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Sbloccare il Data Box Disk per i client Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|Eccezione [Errore] generata: esiste un file DriveManifest.xml nell'unità di destinazione. <br> Ciò indica che l'unità di destinazione potrebbe essere stata preparata con un file journal diverso. <br>Per aggiungere altri dati nella stessa unità, usare il file journal precedente. Per eliminare i dati esistenti e riutilizzare le unità di destinazione per un nuovo processo di importazione, eliminare il *DriveManifest.xml* nell'unità. Eseguire di nuovo questo comando con un nuovo file journal.| Questo errore si verifica quando si prova a usare lo stesso set di unità per più sessioni di importazione. <br> Usare un solo set di unità per un'unica sessione di divisione della copia.|
|Eccezione [Errore] generata: CopySessionId importdata-sept-test-1 si riferisce a una sessione di copia precedente e non può essere riusato per una nuova sessione di copia.|Questo errore viene segnalato quando si prova a usare lo stesso nome di un processo precedente già completato per un nuovo processo.<br> Assegnare un nome univoco al nuovo processo.|
|[Info] Nome del file o directory di destinazione supera la lunghezza massima NTFS. |Questo messaggio viene segnalato quando il file di destinazione è stato rinominato a causa di un percorso file troppo lungo.<br> Modificare l'opzione disposition nel file `config.json` per controllare questo comportamento.|
|Eccezione [Errore] generata: la sequenza di escape JSON non è corretta. |Questo messaggio viene segnalato quando il formato del file config.json non è valido. <br> Convalidare il file `config.json` con [JSONlint](https://jsonlint.com/) prima di salvare il file.|


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [risolvere i problemi relativi agli strumenti di convalida](data-box-disk-troubleshoot.md).
