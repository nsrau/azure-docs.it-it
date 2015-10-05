## Gateway applicazione

Il servizio Gateway applicazione fornisce una soluzione di bilanciamento del carico HTTP gestita da Azure basata sul bilanciamento del carico di livello 7. Il bilanciamento del carico applicazioni consente di usare le regole di routing per il traffico di rete basato su HTTP.

I gateway applicazione contengono le risorse figlio seguenti:

- **Pool di server back-end** - L’elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP/VIP pubblici. 
- **Impostazioni del pool di server back-end** - Ogni pool ha impostazioni quali porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
- **Porta front-end** - Questa porta è la porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
- **Listener:** - il listener ha una porta front-end, un protocollo (Http o Https, con applicazione della distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL). 
- **Regola** - La regola associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico. È attualmente supportata solo la regola di base. La regola di base è una distribuzione del carico di tipo round robin.

<!---HONumber=Sept15_HO4-->