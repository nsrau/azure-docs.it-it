---
title: Connettersi a una VM Linux in Azure Lab Services | Microsoft Docs
description: Informazioni su come usare Desktop remoto per le macchine virtuali Linux in un lab Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d13868477ff2e3378d87d7785789a7498ed17e59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85443418"
---
# <a name="connect-to-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Connettersi a macchine virtuali Linux in un lab di classe Azure Lab Services
Questo articolo spiega in che modo gli studenti possono connettersi a una macchina virtuale (VM) Linux in un lab usando:
- Un terminale SSH (protocollo Secure Shell)
- Un desktop remoto con interfaccia utente grafica (GUI, graphical user interface)

> [!IMPORTANT] 
> Il protocollo SSH viene configurato automaticamente in modo che sia gli studenti che l'insegnante possano accedere tramite SSH alle VM Linux senza alcuna configurazione aggiuntiva. Se invece gli studenti devono connettersi usando un desktop remoto con GUI, è possibile che l'insegnante debba eseguire una configurazione aggiuntiva.  Per informazioni dettagliate, vedere [Abilitare il desktop remoto per macchine virtuali Linux](how-to-enable-remote-desktop-linux.md).

## <a name="connect-to-the-student-vm-using-ssh"></a>Connettersi alla macchina virtuale dello studente tramite SSH

1. Quando uno studente accede al portale Labs direttamente (`https://labs.azure.com`) o usando un collegamento di registrazione (`https://labs.azure.com/register/<registrationCode>`), viene visualizzato un riquadro per ogni lab a cui lo studente può accedere. 
   
1. Impostare il pulsante sul riquadro in modo da avviare la macchina virtuale quando si trova in stato di arresto. 

2. Selezionare **Connetti**. Sono disponibili due opzioni per connettersi alla macchina virtuale: **SSH** e **RDP**.

    ![Macchina virtuale dello studente: opzioni di connessione](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

3. Se si seleziona l'opzione **SSH**, viene visualizzata la finestra di dialogo **Connetti alla macchina virtuale**:  

    ![Stringa di connessione SSH](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Fare clic sul pulsante **Copia** accanto alla casella di testo per copiare le informazioni di connessione SSH negli Appunti. 

5. Salvare le informazioni di connessione SSH, ad esempio nel riquadro di testo, per poterle usare nel passaggio successivo.

6. Da un terminale SSH (ad esempio [Putty](https://www.putty.org/)) connettersi alla macchina virtuale.

## <a name="connect-to-the-student-vm-using-gui-remote-desktop"></a>Connettersi alla macchina virtuale dello studente tramite il desktop remoto con GUI
L'insegnante può scegliere di configurare le macchine virtuali in modo che gli studenti possano connettersi anche usando un desktop remoto con GUI.  In questo caso, l’insegnante deve informare gli studenti se connettersi alle proprie macchine virtuali usando il **Desktop remoto Microsoft (RDP)** o l’applicazione client **X2Go**.  Entrambe queste applicazioni consentono a uno studente di connettersi in modalità remota alla macchina virtuale e visualizzare il desktop con interfaccia grafica Linux nel computer locale.

### <a name="connect-to-the-student-vm-using-microsoft-remote-desktop-rdp"></a>Connettersi alla macchina virtuale dello studente usando Desktop remoto Microsoft (RDP)
Gli studenti possono usare Desktop remoto Microsoft (RDP) per connettersi alle proprie macchine virtuali Linux dopo che l'insegnante ha configurato il lab con i pacchetti RDP e GUI per un ambiente desktop con interfaccia grafica Linux, ad esempio MATE, XFCE e così via. Ecco la procedura per eseguire la connessione: 

1. Nel riquadro della VM verificare che la macchina virtuale sia in esecuzione e fare clic su **Connetti**. Sono disponibili due opzioni per connettersi alla macchina virtuale: **SSH** e **RDP**.

    ![Macchina virtuale dello studente: opzioni di connessione](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)
2. Selezionare l'opzione **RDP**.  Quando il file RDP viene scaricato nel computer, salvarlo nella macchina virtuale.

3. Se ci si connette da un computer Windows, in genere il client Desktop remoto Microsoft (RDP) è già installato e configurato.  Di conseguenza, è sufficiente fare clic sul file RDP per aprirlo e avviare la sessione remota.

    Se invece ci si connette da un Mac o un Chromebook, fare riferimento alla procedura seguente:
   - [Connettersi a una macchina virtuale usando RDP in un Mac](connect-virtual-machine-mac-remote-desktop.md)
   - [Connettersi a una macchina virtuale usando RDP in un Chromebook](connect-virtual-machine-chromebook-remote-desktop.md)  

### <a name="connect-to-the-student-vm-using-x2go"></a>Connettersi alla macchina virtuale dello studente tramite X2Go
Gli studenti possono usare X2Go per connettersi alle proprie macchine virtuali Linux dopo che l'insegnante ha configurato il lab con i pacchetti X2Go e GUI per un ambiente desktop con interfaccia grafica Linux, ad esempio MATE, XFCE e così via.

L’insegnante deve indicare agli studenti quale ambiente con interfaccia grafica Linux ha installato.  Queste informazioni sono necessarie nei passaggi successivi per eseguire la connessione usando il client X2Go.

1. Installare il [client X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) nel computer locale.

1. Seguire le istruzioni riportate nella [prima sezione](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh) per copiare le informazioni di connessione SSH per la VM.  Queste informazioni sono necessarie per eseguire la connessione tramite il client X2Go.

1. Dopo aver ottenuto tali informazioni, aprire il client X2Go e selezionare **Session** (Sessione)  > **New Session** (Nuova sessione).
   ![Creazione di una nuova sessione in X2Go](./media/how-to-use-classroom-lab/x2go-new-session.png)

1. Immettere i valori nel riquadro **Session Preferences** (Preferenze sessione) in base alle informazioni di connessione SSH.  Ad esempio, le informazioni di connessione sono simili alle seguenti:

    ```bash
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
    ```

    In base a questo esempio, vengono immessi i valori seguenti:

   - **Session name** (Nome sessione): specificare un nome, ad esempio il nome della propria VM.
   - **Host**: specificare l’ID della VM, ad esempio **`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`** .
   - **Login** (Accesso): specificare il nome utente per la VM, ad esempio **studente**.
   - **SSH port** (Porta SSH): specificare la porta univoca assegnata alla VM, ad esempio **12345**.
   - **Session type** (Tipo di sessione): selezionare l'ambiente desktop con interfaccia grafica Linux che l'insegnante ha configurato per la VM.  È necessario ottenere queste informazioni dall'insegnante.

    Infine, fare clic su **OK** per creare la sessione.

    ![Preferenze di sessione X2Go](./media/how-to-use-classroom-lab/x2go-session-preferences.png)

1.  Fare clic sulla sessione nel riquadro di destra.

    ![Avviare nuova sessione in X2Go](./media/how-to-use-classroom-lab/x2go-start-session.png)

    > [!NOTE] 
    > Se viene visualizzato un messaggio simile a questo, selezionare **yes** (sì) per continuare a immettere la password: **The authenticity of host '[`00000000-0000-0000-0000-000000000000.eastus2.cloudapp.eastus.cloudapp.azure.com`]:12345' can't be established (Impossibile stabilire l’autenticità dell’host).  ECDSA key fingerprint is SHA256:00000000000000000000000000000000000000000000. Are you sure you want to continue connecting (yes/no)?** (L’importa digitale della chiave ECDSA è SHA256:00000000000000000000000000000000000000000000. Continuare a eseguire la connessione (sì/no)?)

2. Quando viene richiesto, immettere la password e fare clic su **OK**.  A questo punto si sarà connessi in remoto all'ambiente desktop GUI della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi
Per istruzioni su come abilitare la funzionalità di connessione Desktop remoto in macchine virtuali Linux, vedere [Abilitare Desktop remoto per le macchine virtuali Linux](how-to-enable-remote-desktop-linux.md). 

