---
title: Configurare un dispositivo per eseguire la migrazione di Server valutazione/migrazione di Azure per macchine virtuali Hyper-V | Microsoft Docs
description: Viene descritto come configurare un'appliance per il rilevamento, valutazione e migrazione senza agenti di macchine virtuali Hyper-V con migrazione Server valutazione/migrazione di Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: c531fe49ebff6c021547c2d1c2f382bcd6c9caef
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811765"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Configurare un'appliance per le macchine virtuali Hyper-V

Questo articolo descrive come configurare l'appliance Azure Migrate, se si è valutare le macchine virtuali Hyper-V con lo strumento di valutazione di Azure Migrate Server o la migrazione di macchine virtuali VMware in Azure usando lo strumento di migrazione del Server eseguire la migrazione di Azure.

L'appliance di VM Hyper-V è un'appliance leggera utilizzata per eseguire la migrazione di Server valutazione/migrazione di Azure per eseguire le operazioni seguenti:

- Individuare le macchine virtuali Hyper-V in locale.
- Inviare i dati dei metadati e delle prestazioni per le macchine virtuali individuate per eseguire la migrazione di Server valutazione/migrazione di Azure.

[Altre informazioni](migrate-appliance.md) sull'appliance Azure Migrate.


## <a name="appliance-deployment-steps"></a>Passaggi di distribuzione delle Appliance

Per configurare l'appliance è:
- Scaricare un disco rigido virtuale Hyper-V compresso dal portale di Azure.
- Creare l'appliance e verificare che sia possibile connettersi al Server Assessment di eseguire la migrazione di Azure. 
- Configurare l'appliance per la prima volta e registrarlo con il progetto Azure Migrate.

## <a name="download-the-vhd"></a>Scaricare il disco rigido virtuale

Scaricare il modello VHD compresso per l'appliance.

1. Nelle **migrazione obiettivi** > **server** > **Azure Migrate: Server Assessment**, fare clic su **Discover**.
2. Nelle **individua macchine virtuali** > **i computer sono virtualizzati?** , fare clic su **Sì con Hyper-V**.
3. Fare clic su **scaricare** per scaricare il file di disco rigido virtuale.

    ![Download della macchina virtuale](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verificare la sicurezza

Verificare che il file compresso sia sicuro prima di distribuirlo.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare l'hash per il disco rigido virtuale
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Per la versione di appliance 1.19.05.10, il valore hash generato deve corrispondere a queste impostazioni.

  **Algoritmo** | **Valore hash**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79


  
## <a name="create-the-appliance-vm"></a>Creare l'appliance della macchina virtuale

Importare il file scaricato e creare la macchina virtuale.

1. Estrarre il file VHD compresso in una cartella nell'host Hyper-V che ospita la macchina virtuale appliance. Tre cartelle vengono estratti.
2. Aprire Gestione di Hyper-V. Nelle **azioni**, fare clic su **Importa macchina virtuale**.

    ![Distribuire file VHD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Nell'Importazione guidata macchina virtuale > **prima di iniziare**, fare clic su **successivo**.
3. Nelle **individua cartella**, specificare la cartella che contiene il disco rigido virtuale estratto. Scegliere quindi **Avanti**.
1. Nelle **Seleziona macchine virtuali**, fare clic su **successivo**.
2. Nelle **scegliere il tipo di importazione**, fare clic su **copia macchina virtuale (Crea un nuovo ID univoco)** . Scegliere quindi **Avanti**.
3. Nelle **Scegli destinazione**, lasciare l'impostazione predefinita. Fare clic su **Avanti**.
4. Nelle **cartelle di archiviazione**, lasciare l'impostazione predefinita. Fare clic su **Avanti**.
5. Nelle **scegliere rete**, specificare il commutatore virtuale che verrà usata dalla macchina virtuale. L'opzione richiede la connettività internet per inviare dati ad Azure.
6. Nelle **riepilogo**, rivedere le impostazioni. Fare clic su **Fine**.
7. Gestione di Hyper-V > **macchine virtuali**, avviare la macchina virtuale.


### <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso di appliance in Azure

Assicurarsi che l'appliance della macchina virtuale può connettersi al [URL di Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

## <a name="configure-the-appliance"></a>Configurare l'appliance

Configurare l'appliance per la prima volta.

1. Gestione di Hyper-V > **macchine virtuali**, fare doppio clic su macchina virtuale > **Connect**.
2. Specificare la lingua, fuso orario e la password per l'appliance.
3. Aprire un browser su qualsiasi computer in cui è possibile connettersi alla VM e aprire l'URL dell'app web appliance: **https://*indirizzo IP o nome dell'appliance*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop di appliance facendo clic sul collegamento dell'app.
1. Nell'app web > **configurare i prerequisiti**, eseguire le operazioni seguenti:
    - **Licenza**: Accettare le condizioni di licenza e leggere le informazioni di terze parti.
    - **Connettività**: L'app verifica che la VM abbia accesso a internet. Se la VM Usa un proxy:
        - Fare clic su **le impostazioni del Proxy**e specificare l'indirizzo del proxy e la porta in ascolto, nel formato http://ProxyIPAddress o http://ProxyFQDN.
        - Se il proxy richiede l'autenticazione, specificare le credenziali.
        - È supportato solo il proxy HTTP.
    - **Ora sincronizzazione**: Ora viene verificata. L'ora dell'appliance deve essere sincronizzato con l'ora internet per il corretto funzionamento dell'individuazione della macchina virtuale.
    - **Installare gli aggiornamenti**: Azure eseguire la migrazione di Server Assessment controlla che il dispositivo è gli ultimi aggiornamenti installati.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrare il dispositivo con Azure Migrate

1. Fare clic su **Accedi**. Se non viene visualizzata, verificare che è stato disabilitato il blocco popup nel browser.
2. Nella nuova scheda, accedere usando le credenziali di Azure. 
    - Accedere con il nome utente e password.
    - Accedi con un PIN non sono supportata.
3. Dopo aver effettuato l'accesso, tornare all'app web.
4. Selezionare la sottoscrizione in cui è stato creato il progetto Azure Migrate. Selezionare quindi il progetto.
5. Specificare un nome per l'appliance. Il nome deve essere alfanumerico con 14 caratteri o meno.
6. Fare clic su **Register**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegare le credenziali per i dischi rigidi virtuali SMB

Se si eseguono i dischi rigidi virtuali su SMB, è necessario abilitare la delega delle credenziali dall'appliance per gli host Hyper-V. Per eseguire questa operazione dall'appliance:

1. Nell'appliance di macchina virtuale, eseguire questo comando. HyperVHost1/HyperVHost2 sono i nomi host di esempio.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. In alternativa, eseguire questa operazione nell'Editor criteri di gruppo locali nell'appliance:
    - Nelle **criteri del Computer locale** > **configurazione del Computer**, fare clic su **modelli amministrativi** > **sistema**  >  **Delega delle credenziali**.
    - Fare doppio clic su **Consenti delega credenziali nuove**e selezionare **abilitato**.
    - Nelle **le opzioni**, fare clic su **mostrano**, e aggiungere ogni host Hyper-V da individuare all'elenco, con **wsman /** come prefisso.
    - Nelle **la delega delle credenziali**, fare doppio clic su **Consenti delega credenziali nuove con l'autenticazione server NTLM sola**. Anche in questo caso, aggiungere ogni host Hyper-V da individuare all'elenco, con **wsman /** come prefisso.

## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

Connettersi dall'appliance a host o cluster Hyper-V e avviare l'individuazione della macchina virtuale.

1. Nelle **nome utente** e **Password**, specificare le credenziali dell'account che il dispositivo utilizzerà per individuare le macchine virtuali. Specificare un nome descrittivo per le credenziali e fare clic su **salvare i dettagli**.
2. Fare clic su **Aggiungi host**e specificare i dettagli di host/cluster Hyper-V.
3. Fare clic su **Convalida**. Dopo la convalida, viene visualizzato il numero di macchine virtuali che possono essere individuati in ogni host/cluster.
    - Se la convalida non riesce per un host, esaminare l'errore, passare il mouse sull'icona nella **stato** colonna. Risolvere i problemi e convalidare di nuovo.
    - Per rimuovere gli host o cluster, selezionare > **Elimina**.
    - È possibile rimuovere un host specifico da un cluster. È possibile rimuovere solo l'intero cluster.
    - È possibile aggiungere un cluster, anche se si verificano problemi con i cluster host specifico.
4. Dopo la convalida, fare clic su **salvare e avviare l'individuazione** per avviare il processo di individuazione.

Verrà avviata l'individuazione. Bastano circa 15 minuti per i metadati delle macchine virtuali individuate da visualizzare nel portale di Azure. 

## <a name="verify-vms-in-the-portal"></a>Verificare le macchine virtuali nel portale

Al termine dell'individuazione, è possibile verificare che le VM vengano visualizzate nel portale.

1. Aprire il dashboard di Azure Migrate.
2. Nelle **Azure Migrate - server** > **Azure Migrate: Server Assessment** pagina, scegliere l'icona che consente di visualizzare il numero per **individuati server**. 


## <a name="next-steps"></a>Passaggi successivi

Provare [valutazione di Hyper-V](tutorial-assess-hyper-v.md) con valutazione Server eseguire la migrazione di Azure.