### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Preparare un'installazione push in un server Linux

1. Verificare che sia presente la connettività di rete tra il computer Linux e il server di elaborazione.
2. Creare un account utilizzabile dal server di elaborazione per accedere al computer. L'account deve essere un utente **root** nel server Linux di origine. Usare questo account solo per l'installazione push e per gli aggiornamenti.
3. Assicurarsi che il file /etc/hosts nel server Linux di origine contenga le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.
4. Installare i pacchetti openssh, openssh-server, openssl più recenti nel computer da replicare.
5. Assicurarsi che SSH (Secure Shell) sia abilitato e in esecuzione sulla porta 22.
6. Abilitare il sottosistema SFTP e l'autenticazione della password nel file sshd_config:
  1.  Accedere come utente **root**.
  2.  Nel file /etc/ssh/sshd_config trovare la riga che inizia con **PasswordAuthentication**.
  3.  Rimuovere il commento dalla riga e modificare il valore in **yes**.
  4.  Trovare la riga che inizia con **Subsystem** e rimuovere il commento.

     ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)
  5. Riavviare il servizio **sshd**.

7. Aggiungere l'account creato in CSPSConfigtool.
    1.  Accedere al server di configurazione.
    2.  Aprire **cspsconfigtool.exe**. È disponibile come collegamento sul desktop e nella cartella %ProgramData%\home\svsystems\bin.
    3.  Nella scheda **Gestisci account** fare clic su **Aggiungi account**.
    4.  Aggiungere l'account che è stato creato. 
    5.  Immettere le credenziali usate quando si abilita la replica per un computer.
