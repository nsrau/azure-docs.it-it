Questo articolo presuppone quanto segue:

1. È già stata stabilita una connessione **VPN da sito a sito** o **ExpressRoute** tra la rete locale e la rete virtuale di Azure.
2. L'account utente ha le autorizzazioni necessarie per creare una nuova macchina virtuale nella sottoscrizione di Azure in cui è stato eseguito il failover delle macchine virtuali.
3. La sottoscrizione ha un minimo di quattro core disponibili per attivare una nuova macchina virtuale del server di elaborazione.
4. È disponibile la **passphrase del server di configurazione**.

> [!TIP]
> Assicurarsi che sia possibile connettere la porta 443 del server di configurazione, in esecuzione in locale, dalla rete virtuale di Azure in cui è stato eseguito il failover delle macchine virtuali.


<!--HONumber=Feb17_HO1-->


