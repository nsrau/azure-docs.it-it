> [!NOTE]
> * L'indirizzo IP pubblico del gateway VPN cambierà in caso di migrazione da uno SKU precedente a un nuovo SKU.
> * Non è possibile eseguire la migrazione di gateway VPN classici in nuovi SKU. I gateway VPN classici sono compatibili unicamente con gli SKU legacy (precedenti).
> 

Non è possibile ridimensionare i gateway VPN di Azure tra gli SKU precedenti e le nuove famiglie di SKU. Se nel modello di distribuzione di Resource Manager sono presenti gateway VPN che usano la versione precedente degli SKU, è possibile eseguire la migrazione ai nuovi SKU. Per eseguire la migrazione, eliminare il gateway VPN esistente per la rete virtuale, quindi crearne uno nuovo.

Flusso di lavoro della migrazione:

1. Rimuovere eventuali connessioni al gateway di rete virtuale.
2. Eliminare il gateway VPN precedente.
3. Creare il nuovo gateway VPN.
4. Aggiornare i dispositivi VPN locali con il nuovo indirizzo IP del gateway VPN (per connessioni da sito a sito).
5. Aggiornare il valore dell'indirizzo IP del gateway per eventuali gateway di rete locale da rete virtuale a rete virtuale che si connetteranno a questo gateway.
6. Scaricare i nuovi pacchetti di configurazione VPN client per i client P2S che si connettono alla rete virtuale tramite questo gateway VPN.
7. Creare di nuovo eventuali connessioni al gateway di rete virtuale.