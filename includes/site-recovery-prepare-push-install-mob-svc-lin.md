### <a name="prepare-for-push-install-on-linux-servers"></a>Preparare l'installazione push in server Linux

1. Verificare che sia presente la connettività di rete tra il computer Linux e il server di elaborazione.
2. Creare un account utilizzabile dal server di elaborazione per accedere al computer. L'account deve essere un utente **ROOT** nel server Linux di origine. Questo account viene usato solo per gli aggiornamenti e l'installazione push.
3. Verificare che il file `/etc/hosts` nel server Linux di origine contenga le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.
4. Installare i pacchetti openssh, openssh-server, openssl più recenti nel computer da replicare.
5. Assicurarsi che SSH sia abilitato e in esecuzione sulla porta 22.
6. Abilitare il sottosistema SFTP e l'autenticazione della password nel file sshd_config, come segue:
  - Accedere come utente **ROOT**.
  - Nel file /etc/ssh/sshd_config trovare la riga che inizia con **PasswordAuthentication**.
  - Rimuovere il commento dalla riga e modificare il valore da **no** a **yes**.
   6.4 Trovare la riga che inizia con **Subsystem** e rimuovere il commento.

     ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

7. Aggiungere l'account creato in CSPSConfigtool.

    - Accedere al server di configurazione.
    - Aprire **cspsconfigtool.exe**. È disponibile come collegamento sul desktop e nella cartella %ProgramData%\home\svsystems\bin.
    - Nella scheda **Gestisci account** fare clic su **Aggiungi account**.
    - Aggiungere l'account che è stato creato. Dopo aver aggiunto l'account, per abilitare la replica per un computer sarà necessario fornire le credenziali.


<!--HONumber=Jan17_HO3-->


