Se si verificano problemi di connessione a una macchina virtuale tramite la connessione VPN, controllare gli elementi seguenti:

- Verificare che la connessione VPN sia attiva.
- Verificare che venga effettuata la connessione all'indirizzo IP privato per la VM.
- Se è possibile connettersi alla VM usando l'indirizzo IP privato, ma non il nome del computer, verificare di avere configurato correttamente il valore per DNS. Per altre informazioni sul funzionamento della risoluzione dei nomi per le macchine virtuali, vedere [Risoluzione dei nomi per le macchine virtuali](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Quando ci si connette tramite connessione da punto a sito, controllare gli elementi seguenti:

- Usare "ipconfig" per controllare l'indirizzo IPv4 assegnato alla scheda Ethernet nel computer da cui viene effettuata la connessione. Se l'indirizzo IP è compreso nell'intervallo di indirizzi della rete virtuale a cui ci si connette o nell'intervallo di indirizzi del pool di indirizzi del client VPN, si verifica la cosiddetta sovrapposizione dello spazio indirizzi. Con questo tipo di sovrapposizione, il traffico di rete non raggiunge Azure e rimane nella rete locale.
- Verificare che il pacchetto di configurazione del client VPN sia stato generato dopo che sono stati specificati gli indirizzi IP del server DNS per la rete virtuale. Se gli indirizzi IP del server DNS sono stati aggiornati, generare e installare un nuovo pacchetto di configurazione del client VPN.

Per altre informazioni sulla risoluzione dei problemi di una connessione RDP, vedere [Risolvere i problemi delle connessioni Desktop remoto a una macchina virtuale](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).