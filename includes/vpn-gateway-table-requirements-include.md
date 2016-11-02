La tabella seguente elenca i requisiti per i gateway VPN PolicyBased e RouteBased. La tabella è valida per entrambi i modelli di distribuzione classica e di Gestione risorse. Per il modello classico, i gateway VPN PolicyBased corrispondono ai gateway statici, mentre i gateway RouteBased corrispondono ai gateway dinamici.


| | **Gateway VPN Basic PolicyBased** | **Gateway VPN Basic RouteBased** | **Gateway VPN Standard RouteBased** | **Gateway VPN High Performance RouteBased** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
| **Connettività Site-to-Site** | Configurazione VPN PolicyBased | Configurazione VPN RouteBased | Configurazione VPN RouteBased | Configurazione VPN RouteBased |
| **Connettività da punto a sito (P2S**) | Non supportate | Supportato (può coesistere con Site-to-Site) | Supportato (può coesistere con Site-to-Site) | Supportato (può coesistere con Site-to-Site) |
| **Metodo di autenticazione** | Chiave condivisa | Chiave precondivisa per la connettività da sito a sito, Certificati per la connettività da punto a sito | Chiave precondivisa per la connettività da sito a sito, Certificati per la connettività da punto a sito | Chiave precondivisa per la connettività da sito a sito, Certificati per la connettività da punto a sito |
| **Numero massimo di connessioni Site-to-Site** | 1 | 10 | 10 | 30 |
| **Numero massimo di connessioni Point-to-Site** | Non supportate | 128 | 128 | 128 |
|**Supporto routing attivo (BGP)** | Non supportate | Non supportate | Supportato | Supportato |
 

<!---HONumber=AcomDC_0921_2016-->