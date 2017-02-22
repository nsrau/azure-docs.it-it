1. Connettersi alla macchina virtuale del server di elaborazione tramite Connessione Desktop remoto.
2. Dopo aver eseguito l'accesso, lo strumento di configurazione del server di elaborazione viene avviato automaticamente e richiede di immettere quanto segue:
  * Nome completo (FQDN) o indirizzo IP del server di configurazione.
  * Porta su cui il server di configurazione è in ascolto. Il valore dovrebbe essere 443.
  * Passphrase di connessione al server di configurazione.
  * Porta per il trasferimento dati da configurare per questo server di elaborazione. Lasciare invariato il valore predefinito, a meno che non sia stato specificato un numero di porta diverso nell'ambiente corrente.

    ![Registrare il server di elaborazione](./media/site-recovery-vmware-register-process-server/register-ps.png)
3. Fare clic sul pulsante Salva per salvare la configurazione.
4. Al termine della registrazione, il server di elaborazione viene visualizzato sotto al server di configurazione ed è possibile usarlo per il failback.

> [!TIP]
> Per avviare l'utilità di configurazione del server di elaborazione, è possibile fare doppio clic sul collegamento **cspsconfigtool** disponibile sul desktop della macchina virtuale.


<!--HONumber=Feb17_HO1-->


