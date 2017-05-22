Ogni computer client che si connette a una rete virtuale usando la soluzione Da punto a sito deve avere un certificato client installato. Il certificato client viene generato dal certificato radice e viene installato in ogni computer client. Se non è stato installato un certificato client valido e il client prova a connettersi alla rete virtuale, l'autenticazione avrà esito negativo.

È possibile generare un certificato univoco per ogni client oppure usare lo stesso certificato per più client. Generare certificati client univoci offre il vantaggio di poter revocare un singolo certificato. In caso contrario, se più client utenti usano lo stesso certificato client e questo deve essere revocato, è necessario generare e installare nuovi certificati per tutti i client che usano tale certificato per l'autenticazione.

È possibile generare i certificati client usando i metodi seguenti:

- **Certificato aziendale:**

  - Se si usa una soluzione aziendale per la creazione di certificati, generare un certificato client con il valore di nome comune nel formato "name@yourdomain.com", invece che nel formato "nome dominio\nome utente".
  - Verificare che il certificato client sia basato sul modello di certificato "Utente" con "Autenticazione client" come primo elemento nell'elenco d'uso, invece di Accesso smart card e così via. È possibile controllare il certificato facendo doppio clic sul certificato client e aprendo *Dettagli > Utilizzo chiavi avanzato*.

- **Certificato radice autofirmato:** se si genera un certificato client da un certificato radice autofirmato usando le istruzioni disponibili nell'articolo [Creare un certificato radice autofirmato per connessioni da punto a sito](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert), il certificato viene installato automaticamente nel computer usato per generarlo. Se si vuole installare un certificato client in un altro computer client, è necessario esportarlo. Seguire le istruzioni disponibili nell'articolo per [esportare il certificato](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).