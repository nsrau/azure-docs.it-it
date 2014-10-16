<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="How to create a hybrid deployment of RemoteApp" description="Learn how to create a deployment of RemoteApp that connects to your internal network." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo"></tags>

# Come creare una distribuzione ibrida di RemoteApp

Sono disponibili due tipi di distribuzione di RemoteApp:

-   Cloud: risiede completamente in Azure e viene creata con l'opzione **Creazione rapida** nel portale di gestione di Azure.
-   Ambiente ibrido: include una rete virtuale per l'accesso locale e viene creata con l'opzione **Crea con VPN** nel portale di gestione.

In questa esercitazione vengono illustrati i sette passaggi del processo di creazione di una distribuzione ibrida.

1.  Creare un'immagine modello di RemoteApp.
2.  Creare un servizio RemoteApp.
3.  Collegarsi a una rete virtuale.
4.  Collegarsi a un'immagine modello.
5.  Configurare la sincronizzazione della directory. RemoteApp richiede questa configurazione per sincronizzare utenti, gruppi, contatti e password dall'ambiente Active Directory locale al tenant di Azure Active Directory.
6.  Pubblicare i programmi RemoteApp.
7.  Configurare l'accesso utente.

**Prima di iniziare**

Prima di creare il servizio, è necessario eseguire le seguenti operazioni:

-   Installare gli [aggiornamenti obbligatori][] per migliorare le prestazioni di Azure RemoteApp.
-   Effettuare l'iscrizione per l'[anteprima di RemoteApp][].
-   Creare in Active Directory un account utente da usare come account del servizio RemoteApp. Limitare le autorizzazioni per l'account in modo che possa aggiungere computer al dominio.
-   Raccogliere informazioni sulla rete locale, ovvero indirizzi IP e dettagli sui dispositivi VPN.
-   Installare il modulo [Azure PowerShell][].
-   Raccogliere informazioni sugli utenti e sui gruppi a cui concedere l'accesso. Possono essere informazioni sugli account Microsoft o sugli account aziendali Active Directory per utenti o gruppi.

## **Passaggio 1: Creare un'immagine modello**

Azure RemoteApp usa un'immagine modello di Windows Server 2012 R2 per ospitare tutti i programmi da condividere con gli utenti. Per creare un'immagine modello di RemoteApp personalizzata, è possibile iniziare con un'immagine esistente o crearne una nuova. I requisiti per l'immagine che possono essere caricati e usati con l'app Azure RemoteApp sono i seguenti:

-   Deve essere inclusa in un file VHD (i file VHDX non sono attualmente supportati).
-   Il file VHD può essere di dimensioni fisse o a espansione dinamica. È consigliabile un file VHD a espansione dinamica perché i tempi di caricamento di questo file in Azure sono inferiori rispetto a uno di dimensioni disse.
-   Il disco deve essere inizializzato con lo stile di partizione MBR (Master Boot Record). Lo stile di partizione basato sulla tabella di partizione GUID (GPT) non è supportato.
-   Il file VHD deve contenere una singola installazione di Windows Server 2012 R2. Può contenere più volumi, ma solo uno che contiene un'installazione di Windows.
-   È necessario installare il ruolo Host sessione Desktop remoto e la funzionalità Esperienza desktop.
-   EFS (Encrypting File System) deve essere disabilitato.
-   L'immagine deve essere preparata con SYSPREP usando i parametri **/oobe /generalize /shutdown** (NON usare il parametro **/mode:vm**).

Per creare una nuova immagine modello:

1.  Procurarsi un DVD o un'immagine ISO di Windows Server 2012 R2.
2.  Creare un file VHD.
3.  Installare Windows Server 2012 R2.
4.  Installare il ruolo Host sessione Desktop remoto e la funzionalità Esperienza desktop.
5.  Installare funzionalità aggiuntive richieste dalle applicazioni.
6.  Installare e configurare le applicazioni.
7.  Eseguire eventuali configurazioni aggiuntive di Windows richieste dalle applicazioni.
8.  Disabilitare EFS (Encrypting File System).
9.  Preparare l'immagine con SYSPREP.

I passaggi dettagliati per creare una nuova immagine sono:

1.  Procurarsi un DVD o un'immagine ISO di Windows Server 2012 R2.
2.  Creare un disco VHD usando Gestione disco.

    1.  Avviare Gestione disco (diskmgmt.msc).
    2.  Creare un file VHD a espansione dinamica di dimensioni non inferiori a 40 GB. Stimare la quantità di spazio necessaria per Windows, le applicazioni e le personalizzazioni. Per Windows Server con il ruolo Host sessione Desktop remoto e la funzionalità Esperienza desktop installata sono necessari circa 10 GB di spazio.

        1.  Fare clic su **Azione \> Crea file VHD**.
        2.  Specificare il percorso, le dimensioni e il formato del file VHD. Selezionare **A espansione dinamica**, quindi fare clic su **OK**.

            L'operazione dura alcuni secondi. Una volta completata la creazione del file VHD, dovrebbe essere visibile un nuovo disco senza lettera di unità e nello stato “Non inizializzato" nella console di Gestione disco.

        -   Fare clic con il pulsante destro del mouse sul disco (non lo spazio non allocato), quindi scegliere **Inizializza disco**. Selezionare **MBR** (Master Boot Record) come stile di partizione e fare clic su **OK**.
        -   Creare un nuovo volume. Fare clic con il pulsante destro del mouse sullo spazio non allocato, quindi scegliere **Nuovo volume semplice**. È possibile accettare i valori predefiniti nella procedura guidata, ma assicurarsi di assegnare una lettera di unità per evitare possibili problemi durante il caricamento dell'immagine modello.
        -   Fare clic con il pulsante destro del mouse e scegliere **Scollega file VHD**.

3.  Installare Windows Server 2012 R2:

    1.  Creare una nuova macchina virtuale. Usare la Creazione guidata macchina virtuale in Console di gestione di Hyper-V o Hyper-V client.

        1.  Nella pagina Connessione disco rigido virtuale selezionare **Usa disco rigido virtuale esistente** e passare al file VHD creato nel passaggio precedente.
        2.  Nella pagina Opzioni di installazione selezionare **Installa un sistema operativo da un CD/DVD-ROM di avvio**, quindi selezionare il percorso del supporto di installazione di Windows Server 2012 R2.
        3.  Selezionare altre opzioni della procedura guidata necessarie per installare Windows e le applicazioni. Terminare la procedura guidata.

    2.  Una volta terminata la procedura guidata, modificare le impostazioni della macchina virtuale e apportare eventuali altre modifiche necessarie per installare Windows e i programmi, ad esempio il numero di processori virtuali, quindi fare clic su **OK**.
    3.  Connettersi alla macchina virtuale e installare Windows Server 2012 R2.

4.  Installare il ruolo Host sessione Desktop remoto e la funzionalità Esperienza desktop:

    1.  Avviare Server Manager.
    2.  Fare clic su **Aggiungi ruoli e funzionalità** nella schermata iniziale oppure nel menu **Gestisci**.
    3.  Nella pagina Prima di iniziare fare clic su **Avanti**.
    4.  Selezionare **Installazione basata su ruoli o basata su funzionalità** e fare clic su **Avanti**.
    5.  Selezionare il computer locale nell'elenco e fare clic su **Avanti**.
    6.  Selezionare **Servizi Desktop remoto** e fare clic su **Avanti**.
    7.  Espandere **Interfacce utente e infrastruttura** e selezionare **Esperienza desktop**.
    8.  Fare clic su **Aggiungi funzionalità** e quindi su **Avanti**.
    9.  Nella pagina Servizi Desktop remoto fare clic su **Avanti**.
    10. Fare clic su **Host sessione Desktop remoto**.
    11. Fare clic su **Aggiungi funzionalità** e quindi su **Avanti**.
    12. Nella pagina Conferma selezioni per l'installazione selezionare **Riavvia automaticamente il server di destinazione se necessario**, quindi fare clic su **Sì** nell'avviso di riavvio.
    13. Fare clic su **Installa**. Il computer verrà riavviato.

5.  Installare funzionalità aggiuntive richieste dalle applicazioni, ad esempio .NET Framework 3.5. Per installare le funzionalità, eseguire l'Aggiunta guidata ruoli e funzionalità.
6.  Installare e configurare i programmi e le applicazioni da pubblicare tramite RemoteApp.

    **Importante:** Microsoft consiglia di installare il ruolo Host sessione Desktop remoto prima di installare le applicazioni in modo da garantire l'individuazione di problemi di compatibilità prima del caricamento dell'immagine in RemoteApp.

7.  Eseguire eventuali configurazioni aggiuntive di Windows richieste dalle applicazioni.
8.  Disabilitare EFS (Encrypting File System). Eseguire il comando seguente a un prompt dei comandi con privilegi elevati:

        Fsutil behavior set disableencryption 1

    In alternativa, è possibile impostare o aggiungere il seguente valore DWORD nel Registro di sistema:

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1

9.  Se si intende creare l'immagine all'interno di una **macchina virtuale di Azure**, è necessario eliminare o rinominare il file **\\Windows\\Panther\\Unattend.xml** in quanto questo impedisce l'esecuzione dello script di caricamento usato in seguito.
10. Preparare l'immagine con SYSPREP. Eseguire il comando seguente a un prompt dei comandi con privilegi elevati:

    **C:\\Windows\\System32\\sysprep\\sysprep.exe /generalize /oobe /shutdown**

    **Nota:** non usare l'opzione **/mode:vm** del comando SYSPREP anche se questa è una macchina virtuale.

## **Passaggio 2: Creare un servizio RemoteApp**

1.  Nel [portale di gestione di Azure][] passare alla pagina RemoteApp.
2.  Fare clic su **Nuovo \> Crea con VPN**.
3.  Immettere un nome per il servizio e fare clic su **Crea servizio RemoteApp**.

Dopo la creazione del servizio RemoteApp passare alla pagina **Avvio rapido** per continuare con la procedura di configurazione.

## **Passaggio 3: Collegarsi a una rete virtuale**

Una rete virtuale consente agli utenti di accedere ai dati nella rete locale mediante le risorse remote di RemoteApp. Sono quattro i passaggi per configurare il collegamento alla rete virtuale:

1.  Nella pagina Avvio rapido fare clic su **Collega una rete virtuale RemoteApp**.
2.  Scegliere se creare una nuova rete virtuale o usarne una esistente. Per questa esercitazione verrà creata una nuova rete.
3.  Specificare le informazioni seguenti per la nuova rete:

    -   Nome
    -   Spazio degli indirizzi della rete virtuale
    -   Spazio degli indirizzi locale
    -   Indirizzo IP del server DNS
    -   Indirizzo IP VPN

    Per altre informazioni, vedere [Configurare una VPN da sito a sito nel portale di gestione][].

4.  In seguito, di nuovo nella pagina Avvio rapido, fare clic su **Ottieni script** per scaricare uno script per configurare il dispositivo VPN per la connessione alla rete virtuale appena creata. È necessario disporre delle seguenti informazioni relative al dispositivo VPN:

    -   Fornitore
    -   Piattaforma
    -   Sistema operativo

    Salvare lo script ed eseguirlo nel dispositivo VPN.

    **Nota:** dopo aver eseguito lo script, la rete virtuale passerà allo stato Pronto. L'operazione può richiedere 5-7 minuti. Finche la rete non è pronta non è possibile usarla.

5.  Infine, di nuovo nella pagina Avvio rapido, fare clic su **Aggiungi dominio locale**. Aggiungere l'account del servizio RemoteApp al dominio di Active Directory locale. È necessario disporre di nome di dominio, unità organizzativa, nome utente e password dell'account del servizio.

## **Passaggio 4: Collegarsi a un'immagine modello di RemoteApp**

Un'immagine modello di RemoteApp contiene i programmi da condividere con gli utenti. È possibile caricare la nuova immagine modello creata al passaggio 1 o creare un collegamento a un'immagine esistente già caricata in Azure.

Se si carica la nuova immagine, è necessario immettere il nome e scegliere il percorso dell'immagine. Nella pagina successiva della procedura guidata verrà visualizzato un insieme di cmdlet di PowerShell. Copiarli ed eseguirli da un prompt di Azure PowerShell con privilegi elevati per caricare l'immagine specificata.

Se si crea un collegamento a un'immagine modello esistente, è sufficiente specificare il nome, il percorso e la sottoscrizione di Azure associata all'immagine.

## **Passaggio 5: Configurare la sincronizzazione della directory**

RemoteApp richiede la sincronizzazione della directory tra Azure Active Directory e l'istanza locale di Active Directory in modo da sincronizzare utenti, gruppi, contatti e password nel tenant di Azure Active Directory. Per informazioni sulla pianificazione e le procedure dettagliate, vedere [Roadmap sulla sincronizzazione della directory][].

## **Passaggio 6: Pubblicare i programmi RemoteApp**

Un programma RemoteApp è l'app o il programma da fornire agli utenti e si trova nell'immagine modello caricata per il servizio. Quando un utente accede a un programma RemoteApp, questo sembra essere eseguito nell'ambiente locale, ma in realtà viene eseguito in Azure.

Prima che gli utenti possano accedere ai programmi RemoteApp, è necessario pubblicarli nel feed degli utenti finali, ovvero un elenco di programmi disponibili cui gli utenti accedono tramite il portale di Azure.

È possibile pubblicare più programmi nel servizio RemoteApp. Nella pagina dei programmi RemoteApp fare clic su **Pubblica** per aggiungere un programma. È possibile pubblicare dal menu Start dell'immagine modello oppure specificando il percorso nell'immagine modello per il programma. Se si sceglie di aggiungere il programma dal menu Start, scegliere quello da pubblicare. Se si sceglie di specificare il percorso del programma, fornire il nome del programma e il percorso in cui è installato il programma nell'immagine modello.

## **Passaggio 7: Configurare l'accesso utente**

A questo punto, dopo aver creato il servizio RemoteApp, è necessario aggiungere gli utenti e i gruppi che potranno usare le risorse remote. Gli utenti o i gruppi a cui viene fornito l'accesso devono esistere nel tenant di Active Directory associato alla sottoscrizione usata per creare il servizio RemoteApp.

1.  Nella pagina Avvio rapido fare clic su **Configura accesso utente**.
2.  Immettere l'account aziendale o il nome del gruppo di Active Directory oppure l'account Microsoft per cui concedere l'accesso.

    Per gli utenti assicurarsi di usare il formato “[utente@dominio.com][]”. Per i gruppi immettere il nome del gruppo.

3.  Dopo aver convalidato utenti o gruppi, fare clic su **Salva**.

## Passaggi successivi

La procedura è stata completata e la distribuzione ibrida RemoteApp è stata creata e distribuita. Il passaggio successivo consiste nel chiedere agli utenti di scaricare e installare il client Desktop remoto. L'URL del client è indicato nella pagina Avvio rapido di RemoteApp. Chiedere quindi agli utenti di effettuare l'accesso ad Azure e di accedere ai programmi RemoteApp pubblicati.

  [aggiornamenti obbligatori]: http://support.microsoft.com/kb/2977219
  [anteprima di RemoteApp]: http://azure.microsoft.com/en-us/services/remoteapp/
  [Azure PowerShell]: http://azure.microsoft.com/it-it/documentation/articles/install-configure-powershell/
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Configurare una VPN da sito a sito nel portale di gestione]: http://msdn.microsoft.com/library/azure/dn133795.aspx
  [Roadmap sulla sincronizzazione della directory]: http://msdn.microsoft.com//library/azure/hh967642.aspx
  [utente@dominio.com]: mailto:user@domain.com
