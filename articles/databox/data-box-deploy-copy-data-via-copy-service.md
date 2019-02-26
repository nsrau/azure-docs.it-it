---
title: 'Esercitazione: Copiare i dati nel dispositivo Microsoft Azure Data Box tramite il servizio di copia dati | Microsoft Docs'
description: In questa esercitazione si apprende come copiare i dati nel dispositivo Azure Data Box tramite il servizio di copia dei dati
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: alkohli
ms.openlocfilehash: 3f76721129906b57a05e597aade9f2febb609968
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343528"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-preview"></a>Esercitazione: Usare il servizio di copia dei dati per copiare i dati in Azure Data Box (anteprima)

Questa esercitazione illustra come inserire i dati con il servizio di copia dei dati senza un host intermedio. Il servizio di copia dei dati viene eseguito in locale in Microsoft Azure Data Box, si connette al dispositivo NAS tramite SMB e copia i dati in Data Box.

Usare il servizio di copia dei dati:

- Negli ambienti NAS in cui gli host intermedi potrebbero non essere disponibili.
- Con file di piccole dimensioni che richiedono settimane per l'inserimento e il caricamento dei dati. Il servizio di copia dei dati migliora significativamente i tempi di inserimento e caricamento di file di piccole dimensioni.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Copiare i dati nel Data Box

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare è necessario:

1. Aver completato questa esercitazione: [Configurare Azure Data Box](data-box-deploy-set-up.md).
2. Aver ricevuto il dispositivo Data Box e che lo stato dell'ordine nel portale sia **Recapitati**.
3. Avere le credenziali del dispositivo NAS di origine a cui ci si connetterà per la copia dei dati.
4. Essere connessi a una rete ad alta velocità. È consigliabile avere una connessione di almeno 10 GbE. In assenza di una connessione a 10 GbE, è possibile usare un collegamento dati a 1 GbE, ma la velocità delle operazioni di copia ne risentirà.

## <a name="copy-data-to-data-box"></a>Copiare i dati nel Data Box

Dopo aver stabilito la connessione al dispositivo NAS, il passaggio successivo consiste nel copiare i dati. Prima di procedere alla copia dei dati, tenere conto delle considerazioni seguenti:

- Durante la copia dei dati, assicurarsi che le dimensioni dei dati siano conformi ai limiti di dimensione descritti nell'articolo [Limiti per l'archiviazione di Azure e per Azure Data Box](data-box-limits.md).
- Se i dati caricati da Data Box vengono caricati contemporaneamente da altre applicazioni all'esterno di Data Box, potrebbero verificarsi errori nel processo di caricamento e il danneggiamento dei dati.
- Se i dati vengono modificati durante la lettura da parte del servizio di copia, potrebbero verificarsi errori o il danneggiamento dei dati.

Per copiare i dati usando il servizio di copia dei dati è necessario creare un processo:

1. Nell'interfaccia utente Web locale del dispositivo Data Box passare a **Gestisci** > **Copia dati**.
2. Nella pagina **Copia dati** selezionare **Crea**.

    ![Selezionare Crea nella pagina "Copia dati"](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. Nella finestra di dialogo **Configura processo e inizia** compilare i campi seguenti:
    
    |Campo                          |Valore    |
    |-------------------------------|---------|
    |**Nome processo**                       |Nome univoco contenente meno di 230 caratteri per il processo. Nel nome del processo non sono consentiti i caratteri seguenti: \<, \>, \|, \?, \*, \\, \:, \/ e \\\.         |
    |**Percorso di origine**                |Specificare il percorso SMB dell'origine dati nel formato `\\<ServerIPAddress>\<ShareName>` o `\\<ServerName>\<ShareName>`.        |
    |**Nome utente**                       |Nome utente in formato `\\<DomainName><UserName>` per accedere all'origine dati.        |
    |**Password**                       |Password per accedere all'origine dati.           |
    |**Account di archiviazione di destinazione**    |Selezionare nell'elenco l'account di archiviazione di destinazione in cui caricare i dati.         |
    |**Tipo di destinazione**       |Selezionare il tipo di archiviazione di destinazione dall'elenco: **BLOB in blocchi**, **BLOB di pagine** o **File di Azure**.        |
    |**Condivisione/Contenitore di destinazione**    |Immettere il nome del contenitore o della condivisione in cui caricare i dati nell'account di archiviazione di destinazione. Il nome può essere un nome di condivisione o un nome di contenitore. Ad esempio, usare `myshare` o `mycontainer`. È anche possibile immettere il nome nel formato `sharename\directory_name` o `containername\virtual_directory_name`.        |
    |**Modello di corrispondenza file per la copia**    | È possibile immettere il modello di corrispondenza dei nomi file nei due modi seguenti:<ul><li>**Usare espressioni jolly:** nelle espressioni jolly sono supportati solo `*` e `?`. L'espressione `*.vhd`, ad esempio, corrisponde a tutti i file con estensione `.vhd`. Allo stesso modo, `*.dl?` corrisponde a tutti i file con estensione `.dl` o che iniziano con `.dl`, ad esempio `.dll`. `*foo` corrisponde a tutti i file il cui nome termina con `foo`.<br>È possibile immettere direttamente l'espressione jolly nel campo. Per impostazione predefinita, il valore immesso nel campo è considerato un'espressione jolly.</li><li>**Usare espressioni regolari:** sono supportate espressioni regolari basate su POSIX. L'espressione regolare `.*\.vhd`, ad esempio, corrisponderà a tutti i file con estensione `.vhd`. Per le espressioni regolari specificare `<pattern>` direttamente come `regex(<pattern>)`. Per altre informazioni sulle espressioni regolari, vedere [Linguaggio di espressioni regolari - Riferimento rapido](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |**Ottimizzazione dei file**              |Quando questa funzione è abilitata, i file di dimensioni inferiori a 1 MB vengono compressi durante l'inserimento. Questa compressione consente di velocizzare la copia dei dati per i file di piccole dimensioni. Consente anche un notevole risparmio di tempo quando il numero di file supera di gran lunga il numero di directory.        |
 
4. Selezionare **Avvia**. Gli input verranno convalidati. Se la convalida ha esito positivo, il processo verrà avviato. L'avvio del processo potrebbe richiedere alcuni minuti.

    ![Avviare un processo dalla finestra di dialogo "Configura processo e inizia"](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Verrà creato un processo con le impostazioni specificate. È possibile sospendere, riprendere, annullare o riavviare un processo. Selezionare la casella di controllo accanto al nome del processo, quindi selezionare il pulsante appropriato.

    ![Gestire un processo nella pagina "Copia dati"](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - È possibile sospendere un processo se influisce sulle risorse del dispositivo NAS durante le ore di punta:

        ![Sospendere un processo nella pagina "Copia dati"](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        È possibile riprendere il processo in un secondo momento nelle ore non di punta:

        ![Riprendere un processo nella pagina "Copia dati"](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - È possibile annullare un processo in qualsiasi momento:

        ![Annullare un processo nella pagina "Copia dati"](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Quando si annulla un processo è richiesta una conferma:

        ![Confermare l'annullamento di un processo](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Se si decide di annullare un processo, i dati già copiati non vengono eliminati. Per eliminare tutti i dati che sono stati copiati nel dispositivo Data Box, reimpostare il dispositivo.

        ![Reimpostare un dispositivo](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Se si annulla o si sospende un processo, i file di grandi dimensioni potrebbero essere copiati solo parzialmente. Questi file parzialmente copiati vengono caricati nello stesso stato in Azure. Quando si annulla o si sospende un processo, verificare che i file siano stati copiati correttamente. Per convalidare i file, esaminare le condivisioni SMB o scaricare il file distinta base.

    - È possibile riavviare un processo non riuscito a causa di un errore temporaneo, ad esempio un problema di rete. Non è invece possibile riavviare un processo se ha raggiunto uno stato terminale, come **Operazione completata** o **Operazione completata con errori**. Gli errori del processo possono essere causati da problemi di denominazione dei file o di dimensioni dei file. Questi errori vengono registrati, ma non è possibile riavviare il processo dopo che è stato completato.

        ![Riavviare un processo non riuscito](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Se si verifica un errore e non è possibile riavviare il processo, scaricare i log degli errori per esaminare l'errore stesso. Dopo aver corretto il problema, creare un nuovo processo per copiare i file. È anche possibile [copiare i file tramite SMB](data-box-deploy-copy-data.md).
    
    - In questa versione non si può eliminare un processo.
    
    - È possibile creare un numero illimitato di processi, eseguendone tuttavia al massimo 10 alla volta in parallelo.
    - Se è attivata l'**ottimizzazione dei file**, i file di piccole dimensioni vengono compressi al momento dell'inserimento per migliorare le prestazioni di copia. In questi casi si vedrà un file compresso avente un GUID come nome file. Non eliminare questo file. Il file verrà decompresso durante il caricamento.

6. Mentre il processo è in corso, nella pagina **Copia dati**:

    - Nella colonna **Stato** viene visualizzato lo stato del processo di copia. Lo stato può essere:
        - **Running**
        - **Non riuscito**
        - **Completato**
        - **Sospensione in corso**
        - **Sospeso**
        - **Annullamento in corso**
        - **Canceled**
        - **Completato con errori**
    - Nella colonna **File** vengono visualizzati il numero e le dimensioni totali dei file di cui viene eseguita la copia.
    - Nella colonna **Elaborato** vengono visualizzati il numero e le dimensioni totali dei file elaborati.
    - Nella colonna **Dettagli processo** selezionare **Visualizza** per visualizzare i dettagli del processo.
    - Se durante il processo di copia si verificano errori, riportati nella colonna **N. di errori**, passare alla colonna **Elenco errori** e scaricare i log degli errori per risolvere i problemi.

Attendere il completamento del processo di copia. Dato che alcuni errori vengono registrati solo nella pagina **Connetti e copia**, prima di procedere con il passaggio successivo assicurarsi che il processo di copia sia terminato senza errori.

![Nessun errore nella pagina "Connetti e copia"](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Per garantire l'integrità dei dati, un checksum viene calcolato inline durante la copia dei dati. Al termine della copia, selezionare **Visualizza dashboard** per verificare lo spazio utilizzato e lo spazio disponibile nel dispositivo.
    
![Verificare lo spazio occupato e lo spazio disponibile nel dashboard](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

Al termine del processo di copia è possibile selezionare **Prepara per la spedizione**.

>[!NOTE]
> La **preparazione per la spedizione** non può essere eseguita mentre sono in corso processi di copia.

## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione successiva per informazioni su come riconsegnare il dispositivo Data Box a Microsoft.

> [!div class="nextstepaction"]
> [Spedire il dispositivo Azure Data Box a Microsoft](./data-box-deploy-picked-up.md)

