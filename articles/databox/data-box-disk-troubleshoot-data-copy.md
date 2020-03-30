---
title: Disco casella dati di Azure per la risoluzione dei problemi di copia dei dati Documenti Microsoft
description: Descrive come risolvere i problemi riscontrati durante la copia dei dati in Azure Data Box Disk usando i log.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67148348"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Risolvere i problemi di copia dei dati nel disco di Azure Data BoxTroubleshoot data copy issues in Azure Data Box Disk

Questo articolo si applica al disco di Microsoft Azure Data Box e descrive come risolvere eventuali problemi che si verificano durante la copia dei dati su dischi. L'articolo illustra anche i problemi relativi all'utilizzo dello strumento di copia divisa.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Problemi di copia dei dati quando si utilizza un sistema LinuxData copy issues when using a Linux system

Questa sezione descrive in dettaglio alcuni dei principali problemi affrontati quando si utilizza un client Linux per copiare i dati su dischi.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Problema: l'unità che viene montata in sola lettura
 
**Causa** 

La causa probabile è un file system anomalo.

Rimontare un'unità in modalità di lettura/scrittura impedisce il funzionamento con Data Box Disk. Questo scenario non è supportato da unità decrittografate da dislocker. Il dispositivo potrebbe essere stato rimontato con successo tramite il comando seguente:

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

Anche se il rimontaggio avesse esito positivo, i dati non verranno mantenuti.

**Risoluzione**

Attenersi alla seguente procedura sul sistema Linux:

1. Installare `ntfsprogs` il pacchetto per l'utilità ntfsfix.
2. Smontare i punti di montaggio forniti per l'azionamento dallo strumento di sblocco. Il numero di punti di montaggio varia per le unità.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Eseguire `ntfsfix` sul percorso corrispondente. Il numero evidenziato deve essere uguale al passaggio 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Eseguire il comando seguente per rimuovere i metadati di ibernazione che possono causare il problema di montaggio.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Eseguire uno smontaggio pulito.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Fare uno sblocco pulito e montare.
7. Testare il punto di montaggio scrivendo un file.
8. Smontare e rimontare per convalidare la persistenza del file.
9. Continuare con la copia dei dati.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Problema: errore con dati non persistenti dopo la copiaIssue: Error with data not persisting after copy
 
**Causa** 

Se l'unità non dispone di dati dopo essere stata disinstallata (anche se i dati sono stati copiati all'interno della stessa), è possibile che sia stata rimontata in modalità di lettura/scrittura a seguito del montaggio in modalità di sola lettura.

**Risoluzione**
 
In questo caso, vedere la risoluzione per le [unità montate in sola lettura](#issue-drive-getting-mounted-as-read-only).

Se non era applicabile, copiare i log dalla cartella con lo strumento per sbloccare Data Box Disk e [contattare il supporto tecnico Microsoft](data-box-disk-contact-microsoft-support.md).


## <a name="data-box-disk-split-copy-tool-errors"></a>Errori dello strumento di divisione della copia di Data Box Disk

I problemi riscontrati quando si utilizza uno strumento Copia divisa per suddividere i dati su più dischi sono riepilogati nella tabella seguente.

|Messaggio/avvisi di errore |Consigli |
|---------|---------|
|[Info] Recupero della password di BitLocker per il volume: m <br>[Errore] Eccezione rilevata durante il recupero della chiave BitLocker per il volume m:<br> La sequenza non contiene elementi.|Questo errore viene generato se il Data Box Disk di destinazione è offline. <br> Usare lo strumento `diskmgmt.msc` per mettere i dischi online.|
|[Errore] Eccezione generata: Operazione WMI non riuscita:<br> Metodo=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=Il formato della password di ripristino fornita non è valido. <br>Le password di ripristino di BitLocker sono composte da 48 cifre. <br>Verificare che la password di ripristino sia nel formato corretto e ripetere l'operazione.|Usare lo strumento di sblocco del disco di Data Box per sbloccare prima i dischi, poi riprovare il comando. Per altre informazioni, vedere <li> [Sbloccare il Data Box Disk per i client Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Sbloccare il Data Box Disk per i client Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Errore] Eccezione generata: esiste un file DriveManifest.xml nell'unità di destinazione. <br> Ciò indica che l'unità di destinazione potrebbe essere stata preparata con un file journal diverso. <br>Per aggiungere altri dati nella stessa unità, usare il file journal precedente. Per eliminare i dati esistenti e riutilizzare l'unità di destinazione per un nuovo processo di importazione, eliminare il file *DriveManifest.xml* nell'unità. Eseguire di nuovo questo comando con un nuovo file journal.| Questo errore si verifica quando si prova a usare lo stesso set di unità per più sessioni di importazione. <br> Usare un solo set di unità per un'unica sessione di divisione della copia.|
|[Errore] Eccezione generata: CopySessionId importdata-sept-test-1 si riferisce a una sessione di copia precedente e non può essere riutilizzato per una nuova sessione di copia.|Questo errore viene segnalato quando si prova a usare lo stesso nome di un processo precedente già completato per un nuovo processo.<br> Assegnare un nome univoco al nuovo processo.|
|[Info] Nome del file o directory di destinazione supera la lunghezza massima NTFS. |Questo messaggio viene segnalato quando il file di destinazione è stato rinominato a causa di un percorso file troppo lungo.<br> Modificare l'opzione disposition nel file `config.json` per controllare questo comportamento.|
|[Errore] Eccezione generata: sequenza di escape JSON non valida. |Questo messaggio viene segnalato quando il formato del file config.json non è valido. <br> Convalidare il file `config.json` con [JSONlint](https://jsonlint.com/) prima di salvare il file.|


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come risolvere i problemi relativi agli strumenti di [convalida.](data-box-disk-troubleshoot.md)
