Quando si crea un gateway di rete virtuale è necessario specificare il codice SKU del gateway da usare. Selezionare gli SKU che soddisfano i requisiti inerenti ai tipi di carichi di lavoro, alle velocità effettive, alle funzionalità e ai contratti di servizio.

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

###  <a name="workloads"></a>Carichi di lavoro di produzione *e* di sviluppo e test

A causa delle differenze in termini di contratti di servizio e set di funzionalità, per i carichi di produzione *e* di sviluppo e test è consigliabile usare SKU diversi, come descritto di seguito:

| **Carico di lavoro**                       | **SKU**               |
| ---                                | ---                    |
| **Carichi di lavoro critici, di produzione** | VpnGw1, VpnGw2, VpnGw3 |
| **Sviluppo e test o modello di verifica**   | Basic                  |
|                                    |                        |

Se si usano gli SKU di versione precedente, per la produzione sono consigliati gli SKU Standard e HighPerformance. Per informazioni sugli SKU precedenti, vedere [SKU del gateway (legacy)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).

###  <a name="feature"></a>Set di funzionalità degli SKU del gateway

I nuovi SKU del gateway semplificano i set di funzionalità offerti nei gateway:

| **SKU**| **Funzionalità**|
| ---    | ---         |
|**Basic**   | **VPN basata su route**: 10 tunnel con P2S (da punto a sito); assenza di autenticazione RADIUS per P2S; assenza di IKEv2 per P2S<br>**VPN basata su criteri (IKEv1)**: 1 tunnel, nessuna connessione P2S|
| **VpnGw1, VpnGw2 e VpnGw3** | **VPN basata su route**: fino a 30 tunnel ( * ), P2S, BGP, attivo-attivo, IPsec personalizzato/criterio IKE, coesistenza ExpressRoute/VPN |
|        |             |

( * ) È possibile configurare "PolicyBasedTrafficSelectors" per la connessione di un gateway VPN basato su route (VpnGw1, VpnGw2, VpnGw3) a più dispositivi firewall locali basati su criteri. Per informazioni dettagliate, vedere [Connect VPN gateways to multiple on-premises policy-based VPN devices using PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (Connettere gateway VPN a più dispositivi VPN basati su criteri tramite PowerShell).

###  <a name="resize"></a>Ridimensionamento degli SKU di gateway

1. È possibile eseguire il ridimensionamento tra gli SKU VpnGw1, VpnGw2 e VpnGw3.
2. Quando si usano SKU del gateway di versione precedente, è possibile eseguire il ridimensionamento tra gli SKU Basic, Standard e HighPerformance.
2. **Non** è possibile invece eseguire il ridimensionamento dagli SKU Basic/Standard/HighPerformance ai nuovi SKU VpnGw1/VpnGw2/VpnGw3. È necessario [eseguire la migrazione](#migrate) ai nuovi SKU.

###  <a name="migrate"></a>Migrazione dagli SKU di versione precedente ai nuovi SKU

[!INCLUDE [Migrate SKU](./vpn-gateway-migrate-legacy-sku-include.md)]
