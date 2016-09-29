| | **Velocità effettiva del gateway VPN (1)** | **Tunnel IPsec massimi del gateway VPN (2)** | **Velocità effettiva del gateway di ExpressRoute** | **Coesistenza gateway VPN ed ExpressRoute**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **SKU Basic** | 100 Mbps | 10 | 500 Mbps | No |
| **SKU standard (3)** | 100 Mbps | 10 | 1000 Mbps | Sì |
| **SKU con prestazioni elevate (3)** | 200 Mbps | 30 | 2000 Mbps | Sì |

- (1) La velocità effettiva della VPN è una stima approssimativa basata sulle misurazioni tra reti virtuali nella stessa area di Azure. Non è una velocità effettiva garantita per le connessioni cross-premise tramite Internet. È la misura massima possibile per la velocità effettiva.
- (2) Il numero di tunnel si riferisce alle VPN RouteBased. Una VPN PolicyBased può supportare solo un tunnel VPN da sito a sito.
- (3) Le VPN PolicyBased non sono supportate in questo SKU. Sono supportate solo nello SKU di base.

<!---HONumber=AcomDC_0921_2016-->