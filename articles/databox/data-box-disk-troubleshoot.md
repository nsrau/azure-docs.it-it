---
title: Risoluzione dei problemi di Azure Data Box Disk | Microsoft Docs
description: Descrive come risolvere i problemi riscontrati in Azure Data Box Disk.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/09/2018
ms.author: alkohli
ms.openlocfilehash: 9b2c03c13cf687af7cdebc9c4d2624a6a7c5907f
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069200"
---
# <a name="troubleshoot-issues-in-azure-data-box-disk-preview"></a>Risolvere i problemi in Azure Data Box Disk (anteprima)

Questo articolo si applica a Microsoft Azure Data Box nella versione di anteprima. Questo articolo illustra una parte dei flussi di lavoro e delle attività di gestione complessi che possono essere eseguiti per Data Box e Data Box Disk. 

È possibile gestire Data Box Disk tramite il portale di Azure. Questo articolo è incentrato sulle attività che è possibile eseguire con il portale di Azure. Usare il portale di Azure per gestire gli ordini, gestire i dispositivi e monitorare lo stato dell'ordine man mano che procede verso il completamento.

L'articolo include le esercitazioni seguenti:

- Scaricare i log di diagnostica
- Eseguire query sui log attività


> [!IMPORTANT]
> Data Box è disponibile in anteprima. Esaminare le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di distribuire questa soluzione.

## <a name="download-diagnostic-logs"></a>Scaricare i log di diagnostica

Se si verificano errori durante il processo di copia dei dati, nel portale viene visualizzato il percorso della cartella in cui si trovano i log di diagnostica. 

I log di diagnostica possono essere:
- Log degli errori
- Log dettagliati  

Per spostarsi sul percorso per il log di copia, passare all'account di archiviazione associato all'ordine di Data Box. 

1.  Passare a **Generale > Dettagli ordine** e prendere nota dell'account di archiviazione associato all'ordine.
 

2.  Passare a **Tutte le risorse** e cercare l'account di archiviazione identificato nel passaggio precedente. Selezionare e fare clic sull'account di archiviazione.

    ![Log di copia 1](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs1.png)

3.  Passare a **Servizio BLOB > Esplora BLOB** e cercare il BLOB corrispondente all'account di archiviazione. Passare a **diagnosticslogcontainer > waies**. 

    ![Log di copia 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

    Verranno visualizzati sia i log degli errori che i log dettagliati per la copia dei dati. Selezionare e fare clic su ogni file, quindi scaricare una copia locale.

## <a name="query-activity-logs"></a>Eseguire query sui log attività

È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa. Con i log attività è possibile determinare:

- Le operazioni eseguite sulle risorse nella sottoscrizione.
- Chi ha avviato l'operazione. 
- Quando si è verificata l'operazione.
- Lo stato dell'operazione.
- I valori di altre proprietà che possono essere utili per ricerche sull'operazione.

Il log attività contiene tutte le operazioni di scrittura (ad esempio PUT, POST, DELETE) eseguite sulle risorse, ma non le operazioni di lettura (ad esempio GET). 

I registri attività vengono conservati per 90 giorni. È possibile eseguire query per qualsiasi intervallo di date, purché la data di inizio non risalga a più di 90 giorni prima. È anche possibile filtrare i dati usando una delle query predefinite in Insights. Ad esempio, fare clic su un errore e quindi selezionare e fare clic su errori specifici per comprendere la causa radice.

## <a name="data-box-disk-unlock-tool-errors"></a>Errori dello strumento di sblocco di Data Box Disk


| Messaggio di errore/Comportamento dello strumento      | Consigli                                                                                               |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Nessuna<br><br>Arresto anomalo dello strumento di sblocco di Data Box Disk.                                                                            | BitLocker non è installato. Assicurarsi che BitLocker sia installato nel computer host che esegue lo strumento di sblocco di Data Box Disk.                                                                            |
| La versione corrente di .NET Framework non è supportata. Sono supportate la versione 4.5 e versioni successive.<br><br>Lo strumento viene chiuso con un messaggio.  | .NET 4.5 non è installato. Installare .NET 4.5 o versioni successive nel computer host che esegue lo strumento di sblocco di Data Box Disk.                                                                            |
| Impossibile sbloccare o verificare i volumi. Contattare il supporto Microsoft.  <br><br>Lo strumento non riesce a sbloccare o verificare alcuna unità bloccata. | Lo strumento non è riuscito a sbloccare alcuna unità bloccata con la passkey fornita. Contattare il supporto tecnico Microsoft per i passaggi successivi.                                                |
| I volumi seguenti sono sbloccati e verificati. <br>Lettere di unità dei volumi: E:<br>Impossibile sbloccare volumi con le passkey seguenti: werwerqomnf, qwerwerqwdfda <br><br>Lo strumento sblocca alcune unità ed elenca le lettere di unità per cui lo sblocco è riuscito o non è riuscito.| Operazione completata parzialmente. Impossibile sbloccare alcune delle unità con la passkey fornita. Contattare il supporto tecnico Microsoft per i passaggi successivi. |
| Impossibile trovare volumi bloccati. Verificare che il disco ricevuto da Microsoft sia connesso correttamente e sia in stato bloccato.          | Lo strumento non riesce a trovare unità bloccate. Le unità sono già sbloccate o non vengono rilevate. Assicurarsi che le unità siano collegate e bloccate.                                                           |
| Errore irreversibile: parametro non valido<br>Nome del parametro: invalid_arg<br>SINTASSI:<br>DataBoxDiskUnlock /PassKeys:<elenco_passkey_delimitato_da_punti_e_virgola><br><br>Esempio: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>Esempio: DataBoxDiskUnlock /SystemCheck<br>Esempio: DataBoxDiskUnlock /Help<br><br>/PassKeys:       Ottenere questa passkey dall'ordine di Azure DataBox Disk. La passkey sblocca i dischi.<br>/Help:           Questa opzione offre informazioni sull'utilizzo e gli esempi per il cmdlet.<br>/SystemCheck:    Questa opzione controlla se il sistema soddisfa i requisiti per eseguire lo strumento.<br><br>Premere un tasto qualsiasi per uscire. | Immesso parametro non valido. I soli parametri consentiti sono /SystemCheck /PassKey e /Help.                                                                            |

## <a name="data-box-disk-split-copy-tool-errors"></a>Errori dello strumento di divisione della copia di Data Box Disk

|Messaggio/avvisi di errore  |Consigli |
|---------|---------|
|[Informazioni] Recupero della password di bitlocker per il volume: m <br>[Errore] Eccezione rilevata durante il recupero delle chiave di bitlocker per il volume m:<br> La sequenza non contiene elementi.|Questo errore viene generato se il Data Box Disk di destinazione è offline. <br> Usare lo strumento `diskmgmt.msc` per mettere i dischi online.|
|[Errore] Eccezione generata: Operazione WMI non riuscita:<br> Metodo=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=Il formato della password di ripristino fornita non è valido. <br>Le password di ripristino di BitLocker sono composte da 48 cifre. <br>Verificare che la password di ripristino sia nel formato corretto e ripetere l'operazione.|Usare lo strumento di sblocco del disco di Data Box per sbloccare prima i dischi, poi riprovare il comando. Per altre informazioni, vedere <li> [Sbloccare il Data Box Disk per i client Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Sbloccare il Data Box Disk per i client Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Errore] Eccezione generata: esiste un file DriveManifest.xml nell'unità di destinazione. <br> Ciò indica che l'unità di destinazione potrebbe essere stata preparata con un file journal diverso. <br>Per aggiungere altri dati nella stessa unità, usare il file journal precedente. Per eliminare i dati esistenti e riutilizzare l'unità di destinazione per un nuovo processo di importazione, eliminare il file DriveManifest.xml nell'unità. Eseguire di nuovo questo comando con un nuovo file journal.| Questo errore si verifica quando si prova a usare lo stesso set di unità per più sessioni di importazione. <br> Usare un solo set di unità per un'unica sessione di divisione della copia.|
|[Errore] Eccezione generata: CopySessionId importdata-sept-test-1 si riferisce a una sessione di copia precedente e non può essere riutilizzato per una nuova sessione di copia.|Questo errore viene segnalato quando si prova a usare lo stesso nome di un processo precedente già completato per un nuovo processo.<br> Assegnare un nome univoco al nuovo processo.|
|[Info] Nome del file o directory di destinazione supera la lunghezza massima NTFS. |Questo messaggio viene segnalato quando il file di destinazione è stato rinominato a causa di un percorso file troppo lungo.<br> Modificare l'opzione disposition nel file `config.json` per controllare questo comportamento.|
|[Errore] Eccezione generata: sequenza di escape JSON non valida. |Questo messaggio viene segnalato quando il formato del file config.json non è valido. <br> Convalidare il file `config.json` con [JSONlint](https://jsonlint.com/) prima di salvare il file.|



## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire Data Box Disk tramite il portale di Azure](data-box-portal-ui-admin.md).
