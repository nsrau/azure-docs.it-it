[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="is-radius-authentication-supported-on-all-azure-vpn-gateway-skus"></a>L'autenticazione RADIUS è supportata in tutti gli SKU del gateway VPN di Azure?

L'autenticazione RADIUS è supportata per gli SKU VpnGw1, VpnGw2 e VpnGw3. Se si usano SKU legacy, l'autenticazione RADIUS è supportata negli SKU Standard e Prestazioni elevate. Non è supportata nello SKU del gateway Basic. 
 
### <a name="is-radius-authentication-supported-for-the-classic-deployment-model"></a>L'autenticazione RADIUS è supportata per il modello di distribuzione classica?
 
No. L'autenticazione RADIUS non è supportata per il modello di distribuzione classica.
 
### <a name="are-3rd-party-radius-servers-supported"></a>I server RADIUS di terze parti sono supportati?

Sì, i server RADIUS di terze parti sono supportati.
 
### <a name="what-are-the-connectivity-requirements-to-ensure-that-the-azure-gateway-is-able-to-reach-an-on-premises-radius-server"></a>Quali sono i requisiti di connettività per assicurarsi che il gateway di Azure possa raggiungere un server RADIUS locale?

È necessaria una connessione VPN da sito a sito al sito locale, con le route corrette configurate.  
 
### <a name="can-traffic-to-an-on-premises-radius-server-from-the-azure-vpn-gateway-be-routed-over-an-expressroute-connection"></a>Il traffico a un server RADIUS locale (dal gateway VPN di Azure) può essere instradato tramite una connessione ExpressRoute?

No. Può essere instradato solo tramite una connessione da sito a sito.
 
### <a name="is-there-a-change-in-the-number-of-sstp-connections-supported-with-radius-authentication-what-is-the-maximum-number-of-sstp-and-ikev2-connections-supported"></a>Il numero di connessioni SSTP supportate con l'autenticazione RADIUS è stato modificato? Qual il numero massimo di connessioni SSTP e IKEv2 supportate?

Il numero massimo di connessioni SSTP supportate in un gateway con l'autenticazione RADIUS non è stato modificato. È ancora 128. Il numero massimo di connessioni supportate è 128, indipendentemente dal fatto che il gateway sia configurato per SSTP, IKEv2 o entrambi.
 
### <a name="what-is-the-difference-between-doing-certificate-authentication-using-a-radius-server-vs-using-azure-native-certificate-authentication-by-uploading-a-trusted-certificate-to-azure"></a>Qual è la differenza tra eseguire l'autenticazione del certificato usando un server RADIUS o usando l'autenticazione del certificato nativo di Azure (caricando un certificato attendibile in Azure)?

Nell'autenticazione del certificato RADIUS la richiesta di autenticazione viene inoltrata a un server RADIUS che gestisce la convalida effettiva del certificato. Questa opzione è utile per l'integrazione con un'infrastruttura di autenticazione del certificato già disponibile tramite RADIUS.
  
Quando si usa Azure per l'autenticazione del certificato, il gateway VPN di Azure esegue la convalida del certificato. È necessario caricare la chiave pubblica del certificato nel gateway. È anche possibile specificare l'elenco di certificati revocati a cui non deve essere consentito connettersi.

### <a name="does-radius-authentication-work-with-both-ikev2-and-sstp-vpn"></a>L'autenticazione RADIUS funziona sia con IKEv2 che con SSTP VPN?

Sì, l'autenticazione RADIUS funziona sia con IKEv2 che con SSTP VPN.  