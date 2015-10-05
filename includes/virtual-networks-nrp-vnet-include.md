## Rete virtuale
Le reti virtuali e le subnet consentono di definire un limite di sicurezza per i carichi di lavoro in esecuzione in Azure. Una rete virtuale è caratterizzata da una raccolta di spazi di indirizzi, noti anche come blocchi CIDR.

Una subnet è una risorsa figlio di una rete virtuale e consente di definire i segmenti degli spazi di indirizzi all'interno di un blocco CIDR, usando i prefissi degli indirizzi IP. Le NIC possono essere aggiunte alle subnet e connesse alle macchine virtuali, fornendo connettività per diversi carichi di lavoro.

![Scheda di interfaccia di rete in una macchina virtuale singola](./media/resource-groups-networking/Figure4.png)

Le proprietà principali di una risorsa della rete virtuale includono:

- Spazio di indirizzi IP (blocco CIDR) 
- Nome della rete virtuale
- subnet
- Server DNS

Una rete virtuale può essere associata anche alle risorse di rete seguenti:

- Gateway VPN

### Subnet

Le proprietà principali di una subnet includono:

- Prefisso dell'indirizzo IP
- Nome della subnet

Una subnet può essere associata anche alle risorse di rete seguenti:

- NSG

<!---HONumber=Sept15_HO4-->