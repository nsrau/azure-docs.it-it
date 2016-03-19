<!--author=SharS last changed: 12/01/15-->

### Passaggio 1: Autorizzare un dispositivo a modificare la chiave DEK del servizio nel portale di Azure classico

In genere, l'amministratore dei dispositivi richiede all'amministratore del servizio di autorizzare un dispositivo a modificare le chiavi DEK del servizio. L'amministratore del servizio autorizza quindi il dispositivo a modificare la chiave.

Questo passaggio viene eseguito nel portale di Azure classico. L'amministratore del servizio può selezionare un dispositivo in un elenco di dispositivi idonei per l'autorizzazione. Il dispositivo viene quindi autorizzato ad avviare il processo di modifica della chiave DEK del servizio.

#### Quali dispositivi possono essere autorizzati a modificare le chiavi DEK del servizio?

Per essere autorizzato ad avviare le modifiche alla chiave DEK del servizio, un dispositivo deve soddisfare i criteri seguenti:

- Il dispositivo deve essere online per essere idoneo per l'autorizzazione di modifica della chiave DEK del servizio.

- È possibile autorizzare di nuovo lo stesso dispositivo dopo 30 minuti se la modifica della chiave non è stata avviata.

- È possibile autorizzare un dispositivo diverso, purché la modifica della chiave non sia stata avviata dal dispositivo autorizzato in precedenza. Dopo che il nuovo dispositivo è stato autorizzato, il dispositivo precedente non può avviare la modifica.

- Non è possibile autorizzare un dispositivo mentre è in corso il rollover della chiave DEK del servizio.

- È possibile autorizzare un dispositivo quando alcuni dei dispositivi registrati con il servizio hanno eseguito il rollover della crittografia mentre altri no. In questi casi, i dispositivi idonei sono quelli che hanno completato la modifica della chiave DEK del servizio.

> [AZURE.NOTE]
Nel portale di Azure classico i dispositivi virtuali StorSimple non vengono visualizzati nell'elenco dei dispositivi che possono essere autorizzati ad avviare la modifica della chiave.

Per selezionare e autorizzare un dispositivo per avviare la modifica della chiave DEK del servizio, seguire questa procedura.

#### Per autorizzare un dispositivo a modificare la chiave

1. Nella pagina del dashboard del servizio fare clic su **Modifica chiave DEK del servizio**.

    ![Modificare la chiave di crittografia del servizio](./media/storsimple-change-data-encryption-key/HCS_ChangeServiceDataEncryptionKey-include.png)

2. Nella finestra di dialogo **Modifica chiave DEK del servizio** selezionare e autorizzare un dispositivo per avviare la modifica della chiave DEK del servizio. L'elenco a discesa contiene tutti i dispositivi idonei che possono essere autorizzati.

3. Fare clic sull'icona del segno di spunta ![icona del segno di spunta](./media/storsimple-change-data-encryption-key/HCS_CheckIcon-include.png).

### Passaggio 2: Usare Windows PowerShell per StorSimple per avviare la modifica della chiave DEK del servizio

Questo passaggio viene eseguito nell'interfaccia di Windows PowerShell per StorSimple nel dispositivo StorSimple autorizzato.

> [AZURE.NOTE] Non è possibile eseguire operazioni nel portale di Azure classico del servizio StorSimple Manager fino a quando il rollover della chiave non viene completato.

Se si usa la console seriale del dispositivo per la connessione all'interfaccia di Windows PowerShell,seguire questa procedura.

#### Per avviare la modifica della chiave DEK del servizio

1. Selezionare l'opzione 1 per eseguire l'accesso completo.

2. Al prompt dei comandi digitare:

     `Invoke-HcsmServiceDataEncryptionKeyChange`

3. Dopo che il cmdlet è stato completato, si otterrà una nuova chiave DEK del servizio. Copiare e salvare la chiave per l'uso nel passaggio 3 di questo processo. Questa chiave verrà usata per aggiornare tutti i dispositivi rimanenti registrati con il servizio StorSimple Manager.

    > [AZURE.NOTE] Questo processo deve essere avviato entro quattro ore dall'autorizzazione di un dispositivo StorSimple.

   La nuova chiave viene quindi inviata al servizio affinché ne venga effettuato il push a tutti i dispositivi registrati con il servizio. Nel dashboard del servizio verrà visualizzato un avviso. Il servizio disabiliterà tutte le operazioni nei dispositivi registrati e l'amministratore dei dispositivi dovrà quindi aggiornare la chiave DEK del servizio negli altri dispositivi. Tuttavia, i flussi di I/O (invio di dati dagli host al cloud) non verranno interrotti.

   Se nel servizio è registrato un unico dispositivo, il processo di rollover è completo ed è possibile ignorare il passaggio successivo. Se nel servizio sono registrati più dispositivi, andare al passaggio 3.

### Passaggio 3: Aggiornare la chiave DEK del servizio in altri dispositivi StorSimple

Questa procedura deve essere eseguita nell'interfaccia di Windows PowerShell del dispositivo StorSimple se vi sono più dispositivi registrati per il servizio StorSimple Manager. La chiave ottenuta nel Passaggio 2: Usare Windows PowerShell per StorSimple per avviare la modifica della chiave DEK del servizio deve essere usata per aggiornare tutti i rimanenti dispositivi StorSimple registrati con il servizio StorSimple Manager.

Eseguire i passaggi seguenti per aggiornare la chiave DEK del servizio nel dispositivo.

#### Per aggiornare la chiave DEK del servizio

1. Usare Windows PowerShell per StorSimple per connettersi alla console. Selezionare l'opzione 1 per eseguire l'accesso completo.

2. Al prompt dei comandi digitare:

    `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`

3. Specificare la chiave DEK ottenuta nel [Passaggio 2: Usare Windows PowerShell per StorSimple per avviare la modifica della chiave DEK del servizio](#to-initiate-the-service-data-encryption-key-change).

<!---HONumber=AcomDC_0128_2016-->