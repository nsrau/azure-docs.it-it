---
title: Configurare un dispositivo per eseguire la migrazione di Server valutazione/migrazione di Azure per macchine virtuali VMware | Microsoft Docs
description: Viene descritto come configurare un'appliance per il rilevamento, valutazione e migrazione senza agenti di macchine virtuali VMware con Azure eseguire la migrazione della valutazione/migrazione di Server.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: fe190381df346278e75a3e6fd9876b80c33bd86b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811726"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Configurare un'appliance per le macchine virtuali VMware

Questo articolo descrive come configurare l'appliance Azure Migrate, se si è valutare le macchine virtuali VMware con lo strumento di valutazione di Azure Migrate Server o la migrazione di macchine virtuali VMware in Azure con la migrazione senza agente utilizzando lo strumento di migrazione del Server eseguire la migrazione di Azure.

L'appliance di VM VMware è un'appliance leggera utilizzata per eseguire la migrazione di Server valutazione/migrazione di Azure per eseguire le operazioni seguenti:

- individuare le macchine virtuali VMware locali.
- Inviare i dati dei metadati e delle prestazioni per le macchine virtuali individuate per eseguire la migrazione di Server valutazione/migrazione di Azure.

[Altre informazioni](migrate-appliance.md) sull'appliance Azure Migrate.


## <a name="appliance-deployment-steps"></a>Passaggi di distribuzione delle Appliance

Per configurare l'appliance è:
- Scaricare un file di modello con estensione OVA e importarlo nel Server vCenter.
- Creare l'appliance e verificare che sia possibile connettersi al Server Assessment di eseguire la migrazione di Azure. 
- Configurare l'appliance per la prima volta e registrarlo con il progetto Azure Migrate.

## <a name="download-the-ova-template"></a>Scaricare il modello OVA

1. Nelle **migrazione obiettivi** > **server** > **Azure Migrate: Server Assessment**, fare clic su **Discover**.
2. Nelle **individua macchine virtuali** > **i computer sono virtualizzati?** , fare clic su **Sì con VMWare vSphere hypervisor**.
3. Fare clic su **scaricare** per scaricare la. File di modello con estensione OVA.



### <a name="verify-security"></a>Verificare la sicurezza

Verificare che il file con estensione OVA sia sicuro prima di distribuirlo.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente, per generare l'hash per il con estensione OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Per la versione di appliance 1.0.0.5, il valore hash generato deve corrispondere a queste impostazioni. 

  **Algoritmo** | **Valore hash**
  --- | ---
  MD5 | ddfdf21c64af02a222ed517ce300c977


## <a name="create-the-appliance-vm"></a>Creare l'appliance della macchina virtuale

Importare il file scaricato e creare una macchina virtuale.

1. Nella console di vSphere Client fare clic su **File** > **Deploy OVF Template** (Distribuisci modello OVF).
2. Nella procedura guidata Distribuire il modello OVF > **Origine** specificare il percorso del file con estensione ova.
3. Nelle **Name** e **posizione**, specificare un nome descrittivo per la macchina virtuale. Selezionare l'oggetto di inventario in cui verrà ospitata la macchina virtuale.
5. Nelle **Host/Cluster**, specificare l'host o cluster in cui verrà eseguita la macchina virtuale.
6. Nelle **archiviazione**, specificare la destinazione di archiviazione per la macchina virtuale.
7. In **Disk Format** (Formato disco) specificare il tipo e la dimensione del disco.
8. Nelle **Mapping di rete**, specificare la rete a cui si connetterà la macchina virtuale. La rete richiede la connettività internet per inviare metadati al Server Assessment di eseguire la migrazione di Azure.
9. Rivedere e confermare le impostazioni e quindi fare clic su **Finish** (Fine).


### <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso di appliance in Azure

Assicurarsi che l'appliance della macchina virtuale può connettersi al [URL di Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


## <a name="configure-the-appliance"></a>Configurare l'appliance

Configurare l'appliance per la prima volta.

1. Nella console di vSphere Client fare clic con il pulsante destro del mouse su VM > **Open Console** (Apri console).
2. Specificare la lingua, fuso orario e la password per l'appliance.
3. Aprire un browser su qualsiasi computer in cui è possibile connettersi alla VM e aprire l'URL dell'app web appliance: **https://*indirizzo IP o nome dell'appliance*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop di appliance facendo clic sul collegamento dell'app.
4. Nell'app web > **configurare i prerequisiti**, eseguire le operazioni seguenti:
    - **Licenza**: Accettare le condizioni di licenza e leggere le informazioni di terze parti.
    - **Connettività**: L'app verifica che la VM abbia accesso a internet. Se la VM Usa un proxy:
        - Fare clic su **le impostazioni del Proxy**e specificare l'indirizzo del proxy e la porta in ascolto, nel formato http://ProxyIPAddress o http://ProxyFQDN.
        - Se il proxy richiede l'autenticazione, specificare le credenziali.
        - È supportato solo il proxy HTTP.
    - **Ora sincronizzazione**: Ora viene verificata. L'ora dell'appliance deve essere sincronizzato con l'ora internet per il corretto funzionamento dell'individuazione.
    - **Installare gli aggiornamenti**: Azure Migrate verifica che siano installati gli ultimi aggiornamenti di appliance.
    - **Installare VDDK**: Azure Migrate verifica che sia installato VMWare vSphere del Kit di sviluppo del disco virtuale (VDDK).
        - Migrates Azure Usa il VDDK per replicare le macchine durante la migrazione ad Azure.
        - Scaricare VDDK 6.7 da VMware ed estrarre il contenuto del file zip scaricato nella posizione specificata nell'appliance.

## <a name="register-the-appliance-with-azure-migrate"></a>Registrare il dispositivo con Azure Migrate

1. Fare clic su **Accedi**. Se non viene visualizzata, verificare che è stato disabilitato il blocco popup nel browser.
2. Nella nuova scheda, accedere usando le credenziali di Azure. 
    - Accedere con il nome utente e password.
    - Accedere con un PIN non è supportata.
3. Dopo aver effettuato l'accesso, tornare all'app web.
2. Selezionare la sottoscrizione in cui è stato creato il progetto Azure Migrate. Selezionare quindi il progetto.
3. Specificare un nome per l'appliance. Il nome deve essere alfanumerico con 14 caratteri o meno.
4. Fare clic su **Register**.


## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

A questo punto, connettersi dall'appliance al Server vCenter e avviare l'individuazione della macchina virtuale. 

1. Nelle **specificare i dettagli del Server vCenter**, specificare il nome (FQDN) o l'indirizzo IP del Server vCenter. È possibile lasciare la porta predefinita o specificare una porta personalizzata in cui è in ascolto il Server vCenter.
2. Nelle **nome utente** e **Password**, specificare le credenziali dell'account di sola lettura che il dispositivo utilizzerà per individuare le macchine virtuali nel server vCenter. Assicurarsi che l'account abbia le [autorizzazioni necessarie per l'individuazione](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions).
3. Fare clic su **convalidare la connessione** per assicurarsi che il dispositivo possa connettersi al Server vCenter.
4. Dopo aver stabilita la connessione, fare clic su **salvare e avviare l'individuazione**.


Verrà avviata l'individuazione. Bastano circa 15 minuti per i metadati delle macchine virtuali individuate da visualizzare nel portale. 


## <a name="next-steps"></a>Passaggi successivi

Rivedere le esercitazioni relative [VMware assessment](tutorial-assess-vmware.md) e [migrazione senza agenti](tutorial-migrate-vmware.md).
