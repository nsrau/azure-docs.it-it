## Gruppo di sicurezza di rete
Una risorsa del gruppo di sicurezza di rete consente di creare un limite di sicurezza per i carichi di lavoro, implementando le regole di accesso consentito e negato. Queste regole possono essere applicate a livello della scheda di interfaccia di rete (livello dell'istanza della macchina virtuale) oppure a livello della subnet (gruppo di macchine virtuali).

Le proprietà principali di una risorsa del gruppo di sicurezza di rete includono:

- **Regola di sicurezza**: in un gruppo di sicurezza di rete possono essere definite più regole di sicurezza. Ogni regola può consentire o negare diversi tipi di traffico.

### Regola di sicurezza
Una regola di sicurezza è una risorsa figlio di un gruppo di sicurezza di rete.

Le proprietà principali di una regola di sicurezza includono:

- **Protocollo**: protocollo di rete a cui si applica questa regola.
- **Intervallo di porte di origine**: porta di origine o intervallo di porte compreso tra 0 e 65535. È possibile usare un carattere jolly per includere tutte le porte. 
- **Intervallo di porte di destinazione**: porta di destinazione o intervallo di porte compreso tra 0 e 65535. È possibile usare un carattere jolly per includere tutte le porte.
- **Prefisso dell'indirizzo di origine**: intervallo di indirizzi IP di origine. 
- **Prefisso dell'indirizzo di destinazione**: intervallo di indirizzi IP di destinazione.
- **Accesso**: opzioni *Consenti* o *Nega* per il traffico.
- **Priorità**: valore compreso tra 100 e 4096. Il numero di priorità deve essere univoco per ogni regola nella raccolta delle regole di sicurezza. Più basso è il numero di priorità, maggiore sarà la priorità della regola.
- **Direzione**: specifica se la direzione della regola applicata al traffico è *In ingresso* o *In uscita*. 

<!---HONumber=Sept15_HO4-->