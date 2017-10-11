### <a name="supportedclientos"></a>Quali sistemi operativi client è possibile usare con la connettività da punto a sito?

Sono supportati i sistemi operativi client seguenti:

* Windows 7 (a 32 e 64 bit)
* Windows Server 2008 R2 (solo a 64 bit)
* Windows 8 (a 32 e 64 bit)
* Windows 8.1 (a 32 e 64 bit)
* Windows Server 2012 (solo a 64 bit)
* Windows Server 2012 R2 (solo a 64 bit)
* Windows 10
* OSX versione 10.11 per Mac (El Capitan)
* macOS versione 10.12 per Mac (Sierra)

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Quanti endpoint client VPN è possibile includere nella configurazione da punto sito?

Sono supportati fino a 128 client VPN da poter connettere contemporaneamente a una rete virtuale.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>È possibile attraversare proxy e firewall con la funzionalità Da punto a sito

Azure supporta due tipi di opzioni VPN da punto a sito:

* SSTP (Secure Socket Tunneling Protocol). SSTP è una soluzione proprietaria Microsoft basata su SSL che può penetrare i firewall perché la porta TCP 443 usata da SSL viene aperta dalla maggior parte dei firewall.

* VPN IKEv2. La VPN IKEv2 è una soluzione VPN IPsec basata sugli standard che usa le porte UDP 500 e 4500 e il protocollo IP numero 50. Non sempre queste porte vengono aperte dai firewall ed esiste quindi la possibilità che la VPN IKEv2 non riesca ad attraversare proxy e firewall.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Se si riavvia un computer client configurato per la funzionalità Da punto a sito, la VPN verrà riconnessa automaticamente?

Per impostazione predefinita, tramite il computer client non verrà ristabilita automaticamente la connessione VPN.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>La funzionalità Da punto a sito supporta la riconnessione automatica e il DNS dinamico nei client VPN?

La riconnessione automatica e il DNS dinamico non sono supportati attualmente nelle VPN da punto a sito.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>È possibile la coesistenza di configurazioni da sito a sito e punto a sito per la stessa rete virtuale?

Sì. Per il modello di distribuzione Resource Manager, è necessario un gateway di tipo VPN RouteBased. Per il modello di distribuzione classica è necessario un gateway dinamico. La configurazione da punto a sito non è supportata per gateway VPN con routing statico o PolicyBased.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>È possibile configurare un client da punto a sito per connettersi contempo a più reti virtuali?

No. Un client da punto a sito può connettersi solo alle risorse nella rete virtuale in cui risiede il gateway di rete virtuale.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Che velocità effettiva è possibile prevedere usando connessioni da sito a sito o da punto a sito?

È difficile mantenere esattamente la velocità effettiva tramite i tunnel VPN. IPSec e SSTP sono protocolli VPN con un elevato livello di crittografia. La velocità effettiva è limitata inoltre dalla latenza e dalla larghezza di banda tra le sedi locali e Internet. Per un gateway VPN che ha solo connessioni VPN da punto a sito IKEv2, la velocità effettiva totale che è possibile prevedere dipende dallo SKU del gateway. Per altre informazioni sulla velocità effettiva, vedere [SKU del gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Per la connettività da punto a sito è possibile usare qualsiasi client VPN software che supporta SSTP e/o IKEv2?

No. È possibile usare solo il client VPN nativo in Windows per SSTP e il client VPN nativo in Mac per IKEv2. Vedere l'elenco dei sistemi operativi client supportati.

### <a name="can-i-access-the-internet-when-i-am-connected-over-p2s-vpn"></a>È possibile accedere a Internet quando si è connessi tramite VPN P2S?

Sì, è possibile accedere a Internet mentre si è connessi a una rete VPN P2S.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>Azure supporta VPN IKEv2 con Windows?

Gli utenti possono connettersi ad Azure utilizzando il client VPN di Windows integrato che supporta IKEv2. Tuttavia, le connessioni IKEv2 da un dispositivo Windows non funzionano nello scenario seguente:

  Se il dispositivo dell'utente contiene un numero elevato di certificati radice attendibili, le dimensioni del payload del messaggio durante lo scambio IKE sono elevate e ciò comporta la frammentazione del livello IP. I frammenti vengono rifiutati alla fine di Azure, che comporta l'errore di connessione. Il numero di certificati esatto in cui si verifica questo problema è difficile da stimare. Di conseguenza, il funzionamento delle connessioni IKEv2 dai dispositivi Windows non è garantito. Quando si configura sia SSTP che IKEv2 in un ambiente misto (costituito da dispositivi Windows e Mac), il profilo VPN Windows tenta sempre prima di accedere al tunnel IKEv2. Se l’esito è negativo a causa del problema descritto qui, viene utilizzato l’SSTP.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>Oltre a Windows e Mac, quali altre piattaforme supporta Azure per VPN P2S?

Per VPN P2S, Azure supporta solo Windows e Mac.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enabled-radius-andor-ikev2-vpn-on-it"></a>Si dispone già di un Gateway VPN di Azure distribuito. È possibile attivare RADIUS e/o VPN IKEv2 su di esso?

Sì, è possibile abilitare queste nuove funzionalità del gateway già distribuito, sia tramite Powershell che il portale di Azure.