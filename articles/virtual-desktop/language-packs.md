---
title: Installare i Language Pack nelle macchine virtuali Windows 10 in Windows Virtual Desktop - AzureInstall language packs on Windows 10 VMs in Windows Virtual Desktop - Azure
description: Come installare i Language Pack per le macchine virtuali multisessione di Windows 10 in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c40df9d821e069e2cd5ff0c42d5841f6b9041c96
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80634035"
---
# <a name="install-language-packs"></a>Installare i Language Pack

Quando si configurano le distribuzioni di Windows Virtual Desktop a livello internazionale, è consigliabile assicurarsi che la distribuzione supporti più lingue. È possibile installare i Language Pack in un'immagine di macchina virtuale (VM) a più sessioni di Windows 10 Enterprise per supportare tutte le lingue necessarie all'organizzazione. Questo articolo illustra come installare i Language Pack e acquisire immagini che consentono agli utenti di scegliere le proprie lingue di visualizzazione.

Per altre informazioni su come distribuire una macchina virtuale in Azure, vedere Creare una macchina virtuale Windows in una zona di disponibilità con il portale di Azure.Learn more about how to deploy a VM in Azure at [Create a Windows virtual machine in an availability zone with the Azure portal.](../virtual-machines/windows/create-portal-availability-zone.md)

>[!NOTE]
>Questo articolo si applica alle macchine virtuali multisessione di Windows 10 Enterprise.This article applies to Windows 10 Enterprise multi-session VMs.

## <a name="install-a-language-pack"></a>Installare un Language Pack

Per creare un'immagine VM con Language Pack, è innanzitutto necessario installare i Language Pack in un computer e acquisire un'immagine.

Per installare i Language Pack:

1. Accedere come amministratore.
2. Assicurati di aver installato tutti gli ultimi aggiornamenti di Windows e Windows Store.
3. Vai a **Impostazioni** > **Ora & Lingua** > **.**
4. In **Paese o area geografica**selezionare il paese o l'area geografica preferita dal menu a discesa.
    In questo esempio, selezioneremo **Francia**, come mostrato nella schermata seguente:In this example, we're going to select France , as shown in the following screenshot:

    ![Cattura di schermata della pagina Area. La regione attualmente selezionata è la Francia.](media/region-page-france.png)

5. Dopo di che, selezionare **Lingua**, quindi **Selezionare Aggiungi una lingua**. Scegliere la lingua che si desidera installare dall'elenco, quindi selezionare **Avanti**.
6. Quando viene visualizzata la finestra **Installa funzionalità del linguaggio,** selezionare la casella di controllo **Installa Language pack e impostata come lingua di visualizzazione**di Windows .
7. Selezionare **Installa**.
8. Per aggiungere più lingue contemporaneamente, selezionare **Aggiungi una lingua**, quindi ripetere il processo per aggiungere una lingua nei passaggi 5 e 6. Ripetere questo processo per ogni lingua che si desidera installare. Tuttavia, è possibile impostare una sola lingua alla volta come lingua di visualizzazione.

    Esaminiamo una rapida dimostrazione visiva. Le immagini seguenti mostrano come installare i Language Pack per il francese e l'olandese, quindi impostare il francese come lingua di visualizzazione.

    ![Schermata della pagina Lingua all'inizio del processo. La lingua di visualizzazione di Windows selezionata è l'inglese.](media/language-page-default.png)

    ![Schermata della finestra di selezione della lingua. L'utente ha inserito "francese" nella barra di ricerca per trovare i pacchetti in lingua francese.](media/select-language-french.png)

    ![Schermata della pagina Installa funzionalità del linguaggio. Il francese viene selezionato come lingua preferita. Le opzioni selezionate sono "Imposta la lingua di visualizzazione", "Installa Language Pack", "Riconoscimento vocale" e "Scrittura a mano".](media/install-language-features.png)

    Dopo l'installazione dei Language Pack, i nomi dei Language Pack dovrebbero essere visualizzati nell'elenco delle lingue.

    ![Screenshot della pagina della lingua in cui sono installati i nuovi Language Pack. I Language Pack per il francese e l'Olanda sono elencati in "Lingue preferite".](media/language-page-complete.png)

9. Se viene visualizzata una finestra in cui viene chiesto di disconnettersi dalla sessione. Esci, quindi accedi di nuovo. La lingua di visualizzazione dovrebbe essere la lingua selezionata.

10.  Passare a**Orologio e area geografica del** > **Region**Pannello **di controllo** > .

11.  Quando si apre la finestra **Regione,** selezionare la scheda **Amministrazione,** quindi selezionare **Copia impostazioni**.

12.  Selezionare le caselle di controllo **Schermata iniziale e Account** di sistema e **Nuovi account utente**.

13.  Selezionare **OK**.

14.  Si aprirà una finestra che ti dirà di riavviare la sessione. Selezionare **Riavvia ora**.

15.  Dopo aver eseguito nuovamente l'accesso, tornare a Orologio del **Pannello di controllo** > **e regione .** > **Region**

16.  Selezionare la scheda **Amministrazione.**

17.  Selezionare **Modifica impostazioni locali del sistema**.

18. Nel menu a discesa **in Impostazioni internazionali correnti**del sistema selezionare la lingua delle impostazioni locali che si desidera utilizzare. Dopo di che, selezionare **OK**.

19. Selezionare **Riavvia ora** per riavviare la sessione.

Congratulazioni, hai installato i tuoi Language Pack!

Prima di continuare, assicurati che nel sistema siano installate le versioni più recenti di Windows e Windows Store.

## <a name="sysprep"></a>Sysprep

Successivamente, è necessario sysprep il computer per prepararlo per il processo di acquisizione dell'immagine.

Per sysprep il computer:

1. Aprire PowerShell come amministratore.
2. Eseguire il cmdlet seguente per passare alla directory corretta:
   
    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Successivamente, eseguire il seguente cmdlet:
    
    ```powershell
    .\sysprep.exe
    ```

4. Quando si apre la finestra Utilità preparazione sistema, selezionare la casella di controllo **Generalize (Generalizzare),** quindi andare a **Opzioni di arresto (Shutdown Options)** e selezionare **Shut down (Arresto e ribasso)** dal menu a discesa.

>[!NOTE]
>Il processo di syprep richiederà alcuni minuti per essere completato. Quando la macchina virtuale viene arrestata, la sessione remota si disconnetterà.

### <a name="resolve-sysprep-errors"></a>Risolvere gli errori di sysprep

Se viene visualizzato un messaggio di errore durante il processo sysprep, ecco cosa si dovrebbe fare:

1. Aprire **l'unità C** e passare a **Windows** > **System32 Sysprep** > **Panther**, quindi aprire il file **setuperr.**

   Il testo nel file di errore indicherà che è necessario disinstallare un pacchetto di lingua specifico, come illustrato nell'immagine seguente. Copiare il nome del pacchetto della lingua per il passaggio successivo.

   ![Schermata del file setuperr. Il testo con il nome del pacchetto è evidenziato in blu scuro.](media/setuperr-package-name.png)

2. Aprire una nuova finestra di PowerShell ed eseguire il cmdlet seguente con il nome del pacchetto copiato nel passaggio 2 per rimuovere il pacchetto della lingua:

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Verificare di aver rimosso il pacchetto `Remove-AppxPackage` eseguendo nuovamente il cmdlet. Se il pacchetto è stato rimosso correttamente, verrà visualizzato un messaggio che informa che il pacchetto che si sta tentando di rimuovere non è presente.

4. Eseguire `sysprep.exe` nuovamente il cmdlet.

## <a name="capture-the-image"></a>Acquisire l'immagine

Ora che il sistema è pronto, è possibile acquisire un'immagine in modo che altri utenti possano iniziare a usare le macchine virtuali in base al sistema senza dover ripetere il processo di configurazione.

Per acquisire un'immagine:

1. Passare al portale di Azure e selezionare il nome del computer configurato in [Installare un Language Pack](#install-a-language-pack) e [sysprep](#sysprep).

2. Selezionare **Acquisisci**.

3. Immettere un nome per l'immagine nel campo **Nome** e assegnarlo al gruppo di risorse usando il menu a discesa **Gruppo di** risorse, come illustrato nell'immagine seguente.

   ![Schermata della finestra Crea immagine. Il nome assegnato dall'utente a questa immagine di test è "vmwvd-image-fr" e lo ha assegnato al gruppo di risorse "testwvdimagerg".](media/create-image.png)

4. Selezionare **Create** (Crea).

5. Attendere alcuni minuti per il completamento del processo di acquisizione. Quando l'immagine è pronta, verrà visualizzato un messaggio nel Centro notifiche che informa che l'immagine è stata acquisita.

È ora possibile distribuire una macchina virtuale usando la nuova immagine. Quando si distribuisce la macchina virtuale, assicurarsi di seguire le istruzioni in [Creare una macchina virtuale Windows in una zona di disponibilità con il portale di Azure.When](../virtual-machines/windows/create-portal-availability-zone.md)you deploy the VM, make sure to follow the instructions in Create a Windows virtual machine in an availability zone with the Azure portal .

### <a name="how-to-change-display-language-for-standard-users"></a>Come modificare la lingua di visualizzazione per gli utenti standard

Gli utenti standard possono modificare la lingua di visualizzazione nelle macchine virtuali.

Per cambiare la lingua di visualizzazione:

1. Vai a **Impostazioni lingua**. Se non sai dove si trova, puoi inserire **Lingua** nella barra di ricerca nel menu Start.

2. Nel menu a discesa Lingua di visualizzazione di Windows, selezionare la lingua che si desidera utilizzare come lingua di visualizzazione.

3. Esci dalla sessione, quindi accedi di nuovo. La lingua di visualizzazione dovrebbe ora essere quella selezionata nel passaggio 2.
