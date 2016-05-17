### BGP è supportato in tutti gli SKU del gateway VPN di Azure?

No, BGP è supportato nei gateway VPN **Standard** e **HighPerformance** di Azure. Lo SKU **Basic** NON è supportato.

### È possibile usare il protocollo BGP con i gateway VPN di Azure basati su criteri?

No, BGP è supportato unicamente nei gateway VPN basati su route.

### È possibile usare un numero sistema autonomo (ASN) privato?

Sì, è possibile usare il proprio ASN privato o ASN pubblici sia per le reti locali che per le reti virtuali di Azure.

### È possibile usare lo stesso ASN sia per le reti VPN locali che per le reti virtuali di Azure?

No, è necessario assegnare ASN diversi alle reti locali e alle reti virtuali di Azure, se vengono connesse insieme tramite BGP. Ai gateway VPN di Azure è assegnato un ASN predefinito di 65515, sia che BGP sia abilitato o meno per la connettività cross-premise. È possibile eseguire l'override di questo valore predefinito assegnando un ASN diverso durante la creazione del gateway VPN. In alternativa è possibile modificare l'ASN dopo aver creato il gateway. Sarà necessario assegnare gli ASN locali ai gateway di rete locali di Azure corrispondenti.



### Quali prefissi di indirizzo vengono segnalati all'utente dai gateway VPN di Azure?

Il gateway VPN di Azure segnala le route seguenti ai dispositivi BGP locali:

- Prefissi di indirizzo di rete virtuale.
- Prefissi di indirizzo per ogni gateway di rete locale connesso al gateway VPN di Azure.
- Route ottenute da altre sessioni di peering BGP connesse al gateway VPN di Azure, **ad eccezione delle route predefinite sovrapposte a qualsiasi prefisso di rete virtuale**.

### È possibile usare BGP con le connessioni tra reti virtuali?

Sì, è possibile usare BGP sia per connessioni cross-premise che per connessioni tra reti virtuali.

### È possibile combinare connessioni BGP con connessioni non BGP per i gateway VPN di Azure?

Sì, è possibile combinare connessioni BGP e connessioni non BGP per lo stesso gateway VPN di Azure.

### Il gateway VPN di Azure supporta il routing di transito BGP?

Sì, il routing di transito BGP è supportato, con l'eccezione che i gateway VPN di Azure **NON** segnalano le route predefinite agli altri peer BGP. Per abilitare il routing di transito tra più gateway VPN di Azure, è necessario abilitare BGP su tutte le connessioni tra reti virtuali intermedie.

### È possibile stabilire più di un tunnel tra il gateway VPN di Azure e la rete locale?

Sì, è possibile stabilire più di un tunnel VPN S2S tra un gateway VPN di Azure e la rete locale. Si noti che tutti i tunnel verranno conteggiati rispetto al numero totale di tunnel per i gateway VPN di Azure. Ad esempio, se sono presenti due tunnel ridondanti tra il gateway VPN di Azure e una delle reti locali, vengono utilizzati 2 tunnel della quota totale per il gateway VPN di Azure, ovvero 10 per il livello Standard e 30 per il livello HighPerformance.

### È possibile stabilire più tunnel tra due reti virtuali di Azure con BGP?

No, non sono supportati tunnel ridondanti tra una coppia di reti virtuali.

### È possibile usare BGP per VPN S2S in una configurazione di coesistenza tra VPN ExpressRoute/S2S?

Attualmente non è possibile.

### Quale indirizzo viene usato dal gateway VPN di Azure per l'indirizzo IP del peer BGP?

Il gateway VPN di Azure alloca un unico indirizzo IP incluso nell'intervallo GatewaySubnet definito per la rete virtuale. Per impostazione predefinita, si tratta del penultimo indirizzo dell'intervallo. Ad esempio, se il valore di GatewaySubnet è 10.12.255.0.0/27, compreso tra 10.42.255.0.0 e 10.42.255.31, l'indirizzo IP del peer BGP nel gateway VPN di Azure sarà 10.12.255.30. Queste informazioni sono reperibili quando si elencano le informazioni sul gateway VPN di Azure.

### Quali sono i requisiti per gli indirizzi IP dei peer BGP nel dispositivo VPN?

L'indirizzo del peer BGP locale **NON DEVE** essere uguale all'indirizzo IP pubblico del dispositivo VPN. Usare un indirizzo IP diverso nel dispositivo VPN per l'indirizzo IP del peer BGP. Può trattarsi di un indirizzo assegnato all'interfaccia di loopback nel dispositivo. Specificare questo indirizzo nel gateway di rete locale corrispondente che rappresenta la posizione.

### Cosa occorre specificare come prefissi di indirizzo per il gateway di rete locale quando si usa BGP?

Il gateway di rete locale di Azure specifica i prefissi di indirizzo iniziali per la rete locale. Con BGP è necessario allocare il prefisso dell'host, ovvero il prefisso /32, dell'indirizzo IP del peer BGP come spazio di indirizzi per la rete locale. Se l'indirizzo IP del peer BGP è 10.52.255.254, specificare "10.52.255.254/32" come localNetworkAddressSpace per il gateway di rete locale che rappresenta la rete locale. Questo permette al gateway VPN di Azure di stabilire la sessione BGP attraverso il tunnel VPN S2S.

### Cosa è necessario aggiungere al dispositivo VPN locale per la sessione di peering BGP?

È necessario aggiungere una route host dell'indirizzo IP del peer BGP di Azure nel dispositivo VPN che punta al tunnel VPN S2S IPsec. Ad esempio, se l'indirizzo IP del peer VPN di Azure è "10.12.255.30", è necessario aggiungere una route host per "10.12.255.30" con un'interfaccia nexthop dell'interfaccia del tunnel IPsec corrispondente nel dispositivo VPN.