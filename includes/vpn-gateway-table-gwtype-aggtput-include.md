| | **Velocità effettiva del gateway VPN (1)** | **Tunnel IPsec massimi del gateway VPN (2)** | **Velocità effettiva del gateway di ExpressRoute** | **Coesistenza gateway VPN ed ExpressRoute**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **SKU Basic** | 100 Mbps | 10 | 500 Mbps | No |
| **SKU standard** | 100 Mbps | 10 | 1000 Mbps | Sì |
| **SKU con prestazioni elevate (3)** | 200 Mbps | 30 | 2000 Mbps | Sì |

- (1) La velocità effettiva della VPN è una stima approssimativa basata sulle misurazioni tra reti virtuali nella stessa area di Azure. Tale dato non costituisce una garanzia di ciò che è possibile ottenere per connessioni cross-premise tramite Internet, ma deve essere considerato come una misurazione massima possibile.
- (2) Il numero di tunnel si riferisce alla VPN basata su route riportata di seguito. Una VPN basata su criteri può supportare solo un tunnel VPN da sito a sito.
- (3) Lo SKU con prestazioni elevate non è supportato per il tipo di VPN basata su criteri.

<!---HONumber=AcomDC_0727_2016-->