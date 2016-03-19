Sono disponibili 3 SKU del Gateway VPN:

- Basic
- Standard
- Prestazioni elevate

La tabella seguente illustra i tipi di gateway e la velocità effettiva aggregata stimata. I prezzi variano a seconda dello SKU del gateway. Per informazioni sui prezzi, vedere [Gateway VPN Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway/). La tabella è valida per entrambi i modelli di distribuzione classica e di Gestione risorse.


| | **Velocità effettiva del gateway VPN** | **Tunnel IPsec massimi del gateway VPN** | **Velocità effettiva del gateway di ExpressRoute** | **Coesistenza gateway VPN ed ExpressRoute**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **SKU Basic** | 100 Mbps | 10 | 500 Mbps | No |
| **SKU standard** | 100 Mbps | 10 | 1000 Mbps | Sì |
| **SKU con prestazioni elevate** | 200 Mbps | 30 | 2000 Mbps | Sì |


**Nota:** la velocità effettiva della VPN è una stima approssimativa basata sulle misurazioni tra reti virtuali nella stessa area di Azure. Tale dato non costituisce una garanzia di ciò che è possibile ottenere per connessioni cross-premise tramite Internet, ma deve essere considerato come una misurazione massima possibile.

<!---HONumber=AcomDC_0224_2016-->