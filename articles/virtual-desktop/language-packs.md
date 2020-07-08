---
title: Installare i Language Pack in VM Windows 10 in desktop virtuale di Windows-Azure
description: Come installare i Language Pack per le macchine virtuali con più sessioni di Windows 10 nel desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70592d940e3766597475f4a7b90a3902a53406d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361303"
---
# <a name="install-language-packs"></a>Installare i language pack

Quando si configurano le distribuzioni di desktop virtuali Windows a livello internazionale, è consigliabile assicurarsi che la distribuzione supporti più lingue. È possibile installare i Language Pack in un'immagine di macchina virtuale (VM) di Windows 10 Enterprise per supportare tutti i linguaggi necessari per l'organizzazione. Questo articolo illustra come installare i Language Pack e acquisire immagini che consentono agli utenti di scegliere le proprie lingue di visualizzazione.

Per altre informazioni su come distribuire una VM in Azure, vedere [creare una macchina virtuale Windows in una zona di disponibilità con il portale di Azure](../virtual-machines/windows/create-portal-availability-zone.md).

>[!NOTE]
>Questo articolo si applica alle macchine virtuali con più sessioni di Windows 10 Enterprise.

## <a name="install-a-language-pack"></a>Installare un Language Pack

Per creare un'immagine di macchina virtuale con i Language Pack, è necessario prima installare i Language Pack in un computer e acquisire un'immagine.

Per installare i Language Pack:

1. Accedere come amministratore.
2. Assicurarsi di aver installato tutti gli aggiornamenti più recenti di Windows e Windows Store.
3. Passare a **Impostazioni**  >  **ora &**  >  **area della**lingua.
4. In **paese o area geografica**selezionare il paese o l'area geografica preferita dal menu a discesa.
    In questo esempio si selezionerà **France**, come illustrato nello screenshot seguente:

    > [!div class="mx-imgBorder"]
    > ![Screenshot della pagina dell'area. L'area attualmente selezionata è Francia.](media/region-page-france.png)

5. Successivamente, selezionare **lingua**, quindi selezionare **Aggiungi una lingua**. Scegliere la lingua da installare dall'elenco e quindi fare clic su **Avanti**.
6. Quando viene visualizzata la finestra **installa le funzionalità del linguaggio** , selezionare la casella **di controllo installa Language Pack e imposta come lingua di visualizzazione di Windows**.
7. Selezionare **Installa**.
8. Per aggiungere più lingue contemporaneamente, selezionare **Aggiungi una lingua**, quindi ripetere il processo per aggiungere una lingua nei passaggi 5 e 6. Ripetere questo processo per ogni lingua che si vuole installare. Tuttavia, è possibile impostare una sola lingua come lingua di visualizzazione alla volta.

    Viene ora eseguita una dimostrazione visiva rapida. Le immagini seguenti illustrano come installare i Language Pack in francese e olandese, quindi impostare il francese come lingua di visualizzazione.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della pagina del linguaggio all'inizio del processo. La lingua di visualizzazione di Windows selezionata è l'inglese.](media/language-page-default.png)

    > [!div class="mx-imgBorder"]
    > ![Screenshot della finestra di selezione della lingua. L'utente ha immesso "French" nella barra di ricerca per trovare i pacchetti della lingua francese.](media/select-language-french.png)

    > [!div class="mx-imgBorder"]
    > ![Screenshot della pagina di installazione delle funzionalità del linguaggio. Il francese è selezionato come lingua preferita. Le opzioni selezionate sono "Imposta lingua di visualizzazione", "installa Language Pack", "riconoscimento vocale" e "grafia".](media/install-language-features.png)

    Dopo aver installato i Language Pack, si noterà che i nomi dei Language Pack vengono visualizzati nell'elenco delle lingue.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della pagina della lingua con i nuovi Language Pack installati. I Language Pack in francese e olandese sono elencati in "lingue preferite".](media/language-page-complete.png)

9. Se viene visualizzata una finestra in cui viene chiesto di disconnettersi dalla sessione. Disconnettersi, quindi eseguire di nuovo l'accesso. La lingua di visualizzazione è ora la lingua selezionata.

10.  Passare a clock del **Pannello di controllo**  >  **e**area geografica  >  **Region**.

11.  Quando viene visualizzata la finestra **area** , selezionare la scheda **Amministrazione** , quindi selezionare **Copia impostazioni**.

12.  Selezionare le caselle di controllo denominate **schermata iniziale e account di sistema** e **nuovi account utente**.

13.  Selezionare **OK**.

14.  Viene visualizzata una finestra che indica di riavviare la sessione. Selezionare **Riavvia adesso**.

15.  Dopo aver eseguito l'accesso, tornare al clock del **Pannello di controllo**  >  **e**all'  >  **area**geografica.

16.  Selezionare la scheda **Amministrazione** .

17.  Selezionare **Modifica impostazioni locali del sistema**.

18. Nel menu a discesa sotto **impostazioni locali di sistema correnti**selezionare la lingua delle impostazioni locali da usare. Quindi selezionare **OK**.

19. Selezionare **Riavvia ora** per riavviare la sessione.

Sono stati installati i Language Pack.

Prima di continuare, verificare che nel sistema siano installate le versioni più recenti di Windows e Windows Store.

## <a name="sysprep"></a>Sysprep

Successivamente, è necessario eseguire Sysprep nel computer per prepararlo per il processo di acquisizione dell'immagine.

Per eseguire Sysprep nel computer:

1. Aprire PowerShell come amministratore.
2. Eseguire il cmdlet seguente per passare alla directory corretta:

    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Eseguire quindi il cmdlet seguente:

    ```powershell
    .\sysprep.exe
    ```

4. Quando viene visualizzata la finestra Utilità preparazione sistema, selezionare la casella di controllo **generalizza**, quindi passare a **Opzioni di arresto** e selezionare **Arresta** dal menu a discesa.

>[!NOTE]
>Il completamento del processo del syprep di tempo può richiedere alcuni minuti. Quando la macchina virtuale viene arrestata, la sessione remota si disconnette.

### <a name="resolve-sysprep-errors"></a>Risolvere gli errori di Sysprep

Se viene visualizzato un messaggio di errore durante il processo Sysprep, è necessario eseguire le operazioni seguenti:

1. Aprire l' **unità C** e passare a **Windows**  >  **system32 Sysprep**  >  **Panther**, quindi aprire il file **Setuperr** .

   Il testo nel file di errore indica che è necessario disinstallare un pacchetto di linguaggio specifico, come illustrato nella figura seguente. Copiare il nome del pacchetto di lingua per il passaggio successivo.

   > [!div class="mx-imgBorder"]
   > ![Screenshot del file Setuperr. Il testo con il nome del pacchetto viene evidenziato in blu scuro.](media/setuperr-package-name.png)

2. Aprire una nuova finestra di PowerShell ed eseguire il cmdlet seguente con il nome del pacchetto copiato nel passaggio 2 per rimuovere il pacchetto di lingua:

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Assicurarsi di aver rimosso il pacchetto eseguendo di `Remove-AppxPackage` nuovo il cmdlet. Se il pacchetto è stato rimosso correttamente, verrà visualizzato un messaggio che indica che il pacchetto che si sta tentando di rimuovere non esiste.

4. Eseguire di `sysprep.exe` nuovo il cmdlet.

## <a name="capture-the-image"></a>Acquisire l'immagine

Ora che il sistema è pronto, è possibile acquisire un'immagine in modo che gli altri utenti possano iniziare a usare le macchine virtuali in base al sistema senza dover ripetere il processo di configurazione.

Per acquisire un'immagine:

1. Passare alla portale di Azure e selezionare il nome del computer configurato in [installare un Language Pack](#install-a-language-pack) e [Sysprep](#sysprep).

2. Selezionare **Acquisisci**.

3. Immettere un nome per l'immagine nel campo **nome** e assegnarlo al gruppo di risorse usando il menu a discesa **gruppo di risorse** , come illustrato nella figura seguente.

   > [!div class="mx-imgBorder"]
   > ![Screenshot della finestra Crea immagine. Il nome che l'utente ha assegnato a questa immagine di test è "vmwvd-image-fr" ed è stato assegnato al gruppo di risorse "testwvdimagerg".](media/create-image.png)

4. Selezionare **Crea**.

5. Attendere alcuni minuti per il completamento del processo di acquisizione. Quando l'immagine è pronta, nel centro notifiche verrà visualizzato un messaggio che informa che l'immagine è stata acquisita.

È ora possibile distribuire una VM usando la nuova immagine. Quando si distribuisce la macchina virtuale, assicurarsi di seguire le istruzioni in [creare una macchina virtuale Windows in una zona di disponibilità con il portale di Azure](../virtual-machines/windows/create-portal-availability-zone.md).

### <a name="how-to-change-display-language-for-standard-users"></a>Come modificare la lingua di visualizzazione per gli utenti standard

Gli utenti standard possono modificare la lingua di visualizzazione nelle proprie macchine virtuali.

Per modificare la lingua di visualizzazione:

1. Passare a **impostazioni della lingua**. Se non si conosce la posizione in cui si trova, è possibile immettere la **lingua** nella barra di ricerca del menu Start.

2. Nel menu a discesa lingua di visualizzazione di Windows selezionare la lingua che si desidera utilizzare come lingua di visualizzazione.

3. Disconnettersi dalla sessione, quindi eseguire di nuovo l'accesso. La lingua di visualizzazione dovrebbe ora essere quella selezionata nel passaggio 2.
