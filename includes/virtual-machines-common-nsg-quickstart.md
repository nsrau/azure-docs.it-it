Aprire una porta o creare un endpoint in Azure tramite la creazione di un filtro di rete che consenta il traffico verso la porta scelta su una subnet o un'interfaccia di rete di macchina virtuale (VM). Questi filtri, che consentono di controllare il traffico in ingresso e in uscita, vengono inseriti in un gruppo di sicurezza di rete e collegati alla risorsa che riceverà il traffico.

Si userà un esempio comune di traffico Web sulla porta 80. Dopo aver ottenuto una macchina virtuale configurata per rispondere alle richieste Web sulla porta TCP 80, ricordando anche di avviare i servizi e aprire tutte le regole del firewall del sistema operativo nella macchina virtuale, si procederà a:

1. Creare un gruppo di sicurezza di rete.
2. Creare una regola in ingresso che consenta il traffico con:
  - intervallo di porte di destinazione impostato su "80"
  - intervallo di porte di origine "*", che consente qualsiasi porta di origine
  - valore di priorità minore di 65.500, per ottenere una priorità più alta rispetto al valore predefinito della regola in ingresso di accesso negato di tipo catch-all
3. Associare il gruppo di sicurezza di rete con l'interfaccia di rete della VM o con la subnet
    
È possibile creare configurazioni di rete molto complesse per proteggere l'ambiente mediante gruppi di sicurezza di rete e le regole, invece che solo una o due regole che consentono il traffico HTTP o la gestione remota. Per altre informazioni, vedere la sezione ["Altre informazioni"](#more-information-on-network-security-groups) seguente o [Che cos'è un gruppo di sicurezza di rete](../articles/virtual-network/virtual-networks-nsg.md).

<!---HONumber=AcomDC_0601_2016-->