Nella tabella seguente sono elencati i requisiti per i gateway VPN basati su criteri e basati su route. La tabella è valida per entrambi i modelli di distribuzione classica e di Gestione risorse. Per il modello classico, i gateway VPN basati su criteri corrispondo ai gateway statici, mentre i gateway basati su route corrispondono ai gateway dinamici.


| | **Gateway VPN di base basato su criteri** | **Gateway VPN di base basato su route** | **Gateway VPN standard basato su route** | **Gateway VPN con prestazioni elevate basato su route** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
| **Connettività Site-to-Site** | Configurazione VPN basata su criteri | Configurazione VPN basata su route | Configurazione VPN basata su route | Configurazione VPN basata su route |
| **Connettività da punto a sito (P2S**) | Non supportato | Supportato (può coesistere con Site-to-Site) | Supportato (può coesistere con Site-to-Site) | Supportato (può coesistere con Site-to-Site) |
| **Metodo di autenticazione** | Chiave condivisa | Chiave precondivisa per la connettività da sito a sito, Certificati per la connettività da punto a sito | Chiave precondivisa per la connettività da sito a sito, Certificati per la connettività da punto a sito | Chiave precondivisa per la connettività da sito a sito, Certificati per la connettività da punto a sito |
| **Numero massimo di connessioni Site-to-Site** | 1 | 10 | 10 | 30 |
| **Numero massimo di connessioni Point-to-Site** | Non supportato | 128 | 128 | 128 |
|**Supporto routing attivo (BGP)** | Non supportato | Non supportato | Non supportato | Non supportato |
 

<!---HONumber=AcomDC_0224_2016-->