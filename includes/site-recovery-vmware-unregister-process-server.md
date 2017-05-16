La procedura per annullare la registrazione di un server di elaborazione varia a seconda dello stato di connessione con il server di configurazione.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Annullare la registrazione di un server di elaborazione in stato connesso

1. Accedere in remoto al server di elaborazione come amministratore.
2. Avviare il **Pannello di controllo** e aprire **Programmi > Disinstalla un programma**.
3. Disinstallare il **server di configurazione/elaborazione di Microsoft Azure Site Recovery**.
4. Dopo aver completato il passaggio 3, è possibile disinstallare le **dipendenze del server di configurazione/elaborazione di Microsoft Azure Site Recovery**.

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Annullare la registrazione di un server di elaborazione in stato disconnesso

> [!WARNING]
> Se non è possibile ripristinare la macchina virtuale in cui è stato installato il server di elaborazione, attenersi alla procedura seguente.

1. Accedere al server di configurazione come amministratore.
2. Aprire un prompt dei comandi amministrativo e passare alla directory `%ProgramData%\ASR\home\svsystems\bin`.
3. Eseguire il comando.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. I dettagli del server di elaborazione verranno eliminati dal sistema.
