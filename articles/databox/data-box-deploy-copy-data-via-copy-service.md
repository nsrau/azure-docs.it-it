---
title: Copiare dati in Microsoft Azure Data Box tramite SMB | Microsoft Docs
description: Informazioni su come copiare dati in Azure Data Box tramite il servizio di copia dei dati
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: alkohli
ms.openlocfilehash: 9d271642a432d8a149fbe468087a0598c91e7c36
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902380"
---
# <a name="tutorial-use-data-copy-service-to-directly-ingest-data-into-azure-data-box-preview"></a>Esercitazione: Usare il servizio di copia dei dati per inserire direttamente dati in Azure Data Box (anteprima)

Questa esercitazione illustra come inserire i dati con il servizio di copia dei dati, senza che sia necessario un host intermedio. Il servizio di copia dei dati viene eseguito in locale in Data Box, si connette al dispositivo NAS tramite SMB e copia i dati in Data Box.

Usare il servizio di copia dei dati:

- Negli ambienti NAS in cui gli host intermedi potrebbero non essere disponibili.
- Con file di piccole dimensioni che richiedono settimane per l'inserimento e il caricamento dei dati. Questo servizio migliora significativamente i tempi di inserimento e caricamento.

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
> * Prerequisiti
> * Copiare i dati nel Data Box


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. Aver completato l'esercitazione descritta in [Esercitazione: Configurare Azure Data Box](data-box-deploy-set-up.md).
2. Aver ricevuto Data Box e che lo stato dell'ordine nel portale sia **Recapitati**.
3. Avere le credenziali del dispositivo NAS di origine a cui ci si connette per la copia dei dati.
4. Essere connessi a una rete ad alta velocità. È consigliabile avere una connessione di almeno 10 GbE. In assenza di una connessione a 10 GbE, è possibile usare un collegamento dati a 1 GbE, ma la velocità delle operazioni di copia ne risentirà.

## <a name="copy-data-to-data-box"></a>Copiare i dati nel Data Box

Dopo aver stabilito la connessione al dispositivo NAS, il passaggio successivo consiste nel copiare i dati. Prima di procedere alla copia dei dati, tenere conto delle considerazioni seguenti:

- Durante la copia dei dati, assicurarsi che le dimensioni dei dati siano conformi ai limiti di dimensione descritti in [Limiti per l'archiviazione di Azure e per Azure Data Box](data-box-limits.md).
- Se i dati caricati da Data Box vengono caricati contemporaneamente da altre applicazioni all'esterno di Data Box, potrebbero verificarsi errori nel processo di caricamento e il danneggiamento dei dati.
- In caso di varianza dei dati durante la lettura da parte del servizio di copia, potrebbero verificarsi errori o il danneggiamento dei dati.

Per copiare i dati usando il servizio di copia dei dati, è necessario creare un processo. Per creare un processo per copiare i dati, seguire questa procedura.

1. Nell'interfaccia utente Web locale di Data Box passare a **Gestisci > Copia dati**.
2. Nella pagina **Copia dati** fare clic su **Crea**.

    ![Fare clic su **Crea** nella pagina **Copia dati**](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. Nella finestra di dialogo **Configura processo e inizia** specificare gli input seguenti.
    
    |Campo                          |Valore    |
    |-------------------------------|---------|
    |Nome processo                       |Nome univoco contenente meno di 230 caratteri per il processo. Nel nome del processo non sono consentiti i caratteri seguenti: \<, \>, \|, \?, \*, \\, \:, \/ e \\\.         |
    |Percorso di origine                |Specificare il percorso SMB dell'origine dati nel formato `\\<ServerIPAddress>\<ShareName>` o `\\<ServerName>\<ShareName>`.        |
    |Username                       |Nome utente in formato `\\<DomainName><UserName>` per accedere all'origine dati.        |
    |Password                       |Password per accedere all'origine dati.           |
    |Account di archiviazione di destinazione    |Selezionare nell'elenco a discesa l'account di archiviazione di destinazione in cui caricare i dati.         |
    |Tipo di destinazione       |Selezionare il tipo di archivio di destinazione tra BLOB in blocchi, BLOB di pagine o File di Azure.        |
    |Condivisione/Contenitore di destinazione    |Immettere il nome del contenitore o della condivisione per caricare i dati nell'account di archiviazione di destinazione. Il nome può essere un nome di condivisione o un nome di contenitore. Ad esempio, `myshare` o `mycontainer`. È anche possibile immettere il formato `sharename\directory_name` o `containername\virtual_directory_name` nel cloud.        |
    |Criteri di corrispondenza file per la copia    | Immettere i criteri di corrispondenza dei nomi file nei due modi seguenti.<ul><li>**Usare espressioni jolly**. Nelle espressioni jolly sono supportati solo `*` e `?`. L'espressione `*.vhd`, ad esempio, corrisponde a tutti i file con estensione vhd. Analogamente, `*.dl?` corrisponde a tutti i file con estensione `.dl` o `.dll`. `*foo`, infine, corrisponderà a tutti i file i cui nomi terminano con `foo`.<br>È possibile immettere direttamente un'espressione jolly nel campo. Per impostazione predefinita, il valore immesso nel campo viene considerato come un'espressione jolly.</li><li>**Usare espressioni regolari**. Sono supportate espressioni regolari basate su POSIX. L'espressione regolare `.*\.vhd`, ad esempio, corrisponderà a tutti i file con estensione `.vhd`. Per un'espressione regolare specificare il criterio (`<pattern>`) direttamente come `regex(<pattern>)`. <li>Per altre informazioni sulle espressioni regolari, vedere [Linguaggio di espressioni regolari - Riferimento rapido](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |Ottimizzazione file              |Quando questa opzione è abilitata, i file di dimensioni inferiori a 1 MB vengono compressi all'inserimento. Ciò consente di velocizzare la copia dei dati per i file di piccole dimensioni. Il risparmio di tempo è significativo quando il numero di file supera di gran lunga il numero di directory.        |
 
4. Fare clic su **Avvia**. Gli input vengono convalidati. Se la convalida ha esito positivo, verrà quindi avviato un processo. L'avvio del processo potrebbe richiedere alcuni minuti.

    ![Avviare un processo dalla finestra di dialogo Configura processo e inizia](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Verrà creato un processo con le impostazioni specificate. Selezionando la casella di controllo, è quindi possibile sospendere, riprendere, annullare o riavviare un processo.

    ![Gestire un processo tramite la pagina Copia dati](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - È possibile sospendere il processo se influisce sulle risorse NAS durante le ore di punta.

        ![Sospendere un processo](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        È possibile riprendere il processo in un secondo momento nelle ore non di punta.

        ![Riprendere un processo](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - È possibile annullare un processo in qualsiasi momento.

        ![Annullare un processo](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)Quando si annulla un processo, è necessario confermare.

        ![Confermare l'annullamento di un processo](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Se si decide di annullare un processo, i dati già copiati non vengono eliminati. Per eliminare tutti i dati che sono stati copiati in Data Box, reimpostare il dispositivo.

        ![Reimpostare il dispositivo](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Se si annulla o si sospende un processo, i file di grandi dimensioni inclusi nella copia possono restare parzialmente copiati. Questi file verranno caricati nello stesso stato in Azure. Quando si prova ad annullare o sospendere, verificare che i file siano stati copiati correttamente. Per convalidare i file, esaminare le condivisioni SMB o scaricare il file distinta base.

    - È possibile riavviare un processo improvvisamente non riuscito a causa di un errore temporaneo, ad esempio un problema di rete. Non è invece possibile riavviare un processo se ha raggiunto uno stato terminale, come il completamento corretto o il completamento con errori. Gli errori potrebbero essere causati da problemi relativi alle dimensioni o alla denominazione dei file. Questi errori vengono registrati, ma non è possibile riavviare il processo dopo che è stato completato.

        ![Riavviare un processo non riuscito](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Se si verifica un errore e non è possibile riavviare il processo, scaricare i log degli errori ed esaminare l'errore nei file di log. Dopo aver corretto il problema, si può creare un nuovo processo per copiare i file. È anche possibile [copiare i file tramite SMB](data-box-deploy-copy-data.md).
    
    - In questa versione non si può eliminare un processo.
    
    - È possibile creare un numero illimitato di processi, eseguendone tuttavia al massimo 10 alla volta in parallelo.
    - Se è attivata l'ottimizzazione dei file, i file di piccole dimensioni vengono compressi all'inserimento per migliorare le prestazioni di copia. In questi casi verrà visualizzato un file compresso, con il GUID come nome. Non eliminare questo file perché verrà decompresso durante il caricamento.

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
    - Nella colonna **Elaborato** vengono visualizzati il numero e le dimensioni dei file elaborati.
    - Nella colonna **Dettagli** fare clic su **Visualizza** per visualizzare i dettagli del processo.
    - Se durante il processo di copia si sono verificati errori, riportati nella colonna **N. di errori**, per risolvere i problemi scaricare i log degli errori dalla colonna **Log degli errori**.

Attendere il completamento dei processi di copia. Dato che alcuni errori vengono registrati solo nella pagina **Connetti e copia**, prima di procedere con il passaggio successivo assicurarsi che i processi di copia siano terminati senza errori.

![Nessun errore nella pagina **Connetti e copia**](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Per assicurare l'integrità dei dati, il checksum viene calcolato inline durante la copia dei dati. Al termine della copia, verificare lo spazio occupato e lo spazio disponibile nel dispositivo.
    
![Verificare lo spazio occupato e lo spazio disponibile nel dashboard](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

Al termine del processo di copia, si può passare a **Prepara per la spedizione**.

>[!NOTE]
> La preparazione per la spedizione non può essere eseguita mentre sono in corso processi di copia.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Prerequisiti
> * Copiare i dati nel Data Box


Passare all'esercitazione successiva per informazioni su come riconsegnare Data Box a Microsoft.

> [!div class="nextstepaction"]
> [Spedire Azure Data Box a Microsoft](./data-box-deploy-picked-up.md)

