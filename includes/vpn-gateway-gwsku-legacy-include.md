Di seguito sono elencati gli SKU del gateway VPN di precedente versione:

* Basic
* Standard
* HighPerformance

Il gateway VPN non usa lo SKU UltraPerformance. Per informazioni sullo SKU UltraPerformance, vedere la documentazione relativa a [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

Quando si usano gli SKU di precedente versione, considerare quanto segue:

* Per usare un tipo di VPN PolicyBased, è necessario usare lo SKU di livello Basic. Le VPN basate su criteri, precedentemente denominate routing statico, non sono supportate negli altri SKU.
* BGP non è supportato nello SKU di livello Basic.
* Le configurazioni con coesistenza di gateway VPN ed ExpressRoute non sono supportate nello SKU di livello Basic.
* Le connessioni del gateway VPN S2S attivo/attivo possono essere configurate solo nello SKU HighPerformance.

