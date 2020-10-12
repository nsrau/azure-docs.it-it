---
title: Panoramica del monitoraggio dell'integrità per il gateway applicazione di Azure
description: Il gateway applicazione di Azure monitora l'integrità di tutte le risorse nel pool back-end e rimuove automaticamente dal pool le risorse considerate non integre.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: f0e5a153efe26640e54f386600f07c7b3d4711d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89649068"
---
# <a name="application-gateway-health-monitoring-overview"></a>Panoramica del monitoraggio dell'integrità del gateway applicazione

Per impostazione predefinita, il gateway applicazione di Azure monitora l'integrità di tutte le risorse nel pool back-end e rimuove automaticamente dal pool le risorse considerate non integre. Il gateway applicazione continua a monitorare le istanze non integre e le riaggiunge al pool back-end integro, dopo che sono diventate disponibili e rispondono ai probe di integrità. Per impostazione predefinita, il gateway applicazione invia i probe di integrità con la stessa porta definita nelle impostazioni HTTP del back-end. Una porta Probe personalizzata può essere configurata usando un probe di integrità personalizzato.

Il gateway applicazione dell'indirizzo IP di origine usato per i probe di integrità dipende dal pool back-end:
 
- Se l'indirizzo del server nel pool back-end è un endpoint pubblico, l'indirizzo di origine è l'indirizzo IP pubblico front-end del gateway applicazione.
- Se l'indirizzo del server nel pool back-end è un endpoint privato, l'indirizzo IP di origine è dallo spazio di indirizzi IP privato della subnet del gateway applicazione.

![Esempio di probe del gateway applicazione][1]

Oltre al monitoraggio del probe di integrità predefinito, è anche possibile personalizzare il probe di integrità in base ai requisiti dell'applicazione. In questo articolo vengono illustrati i probe di integrità predefiniti e personalizzati.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Probe di integrità predefinito

Un gateway applicazione configura automaticamente un probe di integrità predefinito quando non si configura nessuna configurazione di probe personalizzato. Il comportamento di monitoraggio funziona eseguendo una richiesta HTTP GET per gli indirizzi IP o il nome di dominio completo configurato nel pool back-end. Per i probe predefiniti se le impostazioni http back-end sono configurate per HTTPS, il probe usa HTTPS per testare l'integrità dei server back-end.

Ad esempio: si configura il gateway applicazione per usare i server back-end A, B e C per ricevere il traffico di rete HTTP sulla porta 80. Il monitoraggio dell'integrità predefinito testa i tre server ogni 30 secondi per una risposta HTTP integro con un timeout di 30 secondi per ogni richiesta. Una risposta HTTP integra ha un [codice di stato](https://msdn.microsoft.com/library/aa287675.aspx) compreso tra 200 e 399. In questo caso, la richiesta HTTP GET per il probe di integrità sarà simile a http://127.0.0.1/ .

Se il controllo Probe predefinito non riesce per il server A, il gateway applicazione interrompe l'invio delle richieste al server. Il probe predefinito continua tuttavia a controllare il server A ogni 30 secondi. Quando il server A risponde correttamente a una richiesta di un probe di integrità predefinito, il gateway applicazione avvia di nuovo l'invio delle richieste al server.

### <a name="default-health-probe-settings"></a>Impostazioni del probe di integrità predefinito

| Proprietà probe | valore | Descrizione |
| --- | --- | --- |
| URL probe |\<protocol\>://127.0.0.1:\<port\>/ |Il protocollo e la porta vengono ereditati dalle impostazioni HTTP back-end a cui è associato il probe |
| Interval |30 |Il tempo di attesa in secondi prima di inviare il probe di integrità successivo.|
| Timeout |30 |Il tempo in secondi che un gateway applicazione trascorre in attesa di una risposta del probe prima di contrassegnare il probe come non integro. Se un probe viene restituito come integro, il back-end corrispondente viene subito contrassegnato anch'esso come tale.|
| Soglia non integra |3 |Determina quanti probe inviare in caso di errore del probe di integrità normale. Nello SKU V1, questi probe di integrità aggiuntivi vengono inviati in rapida successione per determinare rapidamente l'integrità del back-end e non attendere l'intervallo di probe. In caso di SKU versione 2, i probe di integrità attendono l'intervallo. Il server back-end viene contrassegnato come inattivo dopo che il numero di errori di probe consecutivi ha raggiunto una soglia non integra. |

Il probe predefinito Cerca solo \<protocol\> : \/ /127.0.0.1: \<port\> per determinare lo stato di integrità. Se si deve configurare il probe di integrità per passare a un URL personalizzato o modificare altre impostazioni, è necessario usare probe personalizzati. Per altre informazioni sui Probe HTTPS, vedere [Panoramica della terminazione TLS e del TLS end-to-end con il gateway applicazione](ssl-overview.md#for-probe-traffic).

### <a name="probe-intervals"></a>Intervalli probe

Tutte le istanze del gateway applicazione eseguono il probe del back-end in modo indipendente. La stessa configurazione di probe si applica a ogni istanza del gateway applicazione. Ad esempio, se la configurazione del probe consiste nell'inviare probe di integrità ogni 30 secondi e se il gateway applicazione dispone di due istanze, entrambe le istanze invieranno il probe di integrità ogni 30 secondi.

Se sono presenti più listener, ogni listener esegue il probe nel back-end in modo indipendente. Ad esempio, se sono presenti due listener che puntano allo stesso pool back-end su due porte diverse, ossia configurate da due impostazioni http di back-end, ogni listener eseguirà il probe dello stesso back-end in modo indipendente. In questo caso, esistono due probe da ogni istanza del gateway applicazione per i due listener. Se sono presenti due istanze del gateway applicazione in questo scenario, la macchina virtuale back-end vedrà quattro probe per l'intervallo di probe configurato.

## <a name="custom-health-probe"></a>Probe di integrità personalizzato

I probe personalizzati consentono un controllo più granulare sul monitoraggio dell'integrità. Quando si usano Probe personalizzati, è possibile configurare un nome host personalizzato, un percorso URL, un intervallo di probe e il numero di risposte non riuscite da accettare prima di contrassegnare l'istanza del pool back-end come non integro e così via.

### <a name="custom-health-probe-settings"></a>Impostazioni del probe di integrità personalizzato

La tabella seguente fornisce le definizioni delle proprietà di un probe di integrità personalizzato.

| Proprietà probe | Descrizione |
| --- | --- |
| Nome |Nome del probe. Questo nome viene usato per identificare e fare riferimento al Probe nelle impostazioni HTTP del back-end. |
| Protocollo |Protocollo usato per inviare il probe. Deve corrispondere al protocollo definito nelle impostazioni HTTP del back-end a cui è associato|
| Host |Nome host con cui inviare il probe. Nello SKU V1 questo valore verrà usato solo per l'intestazione host della richiesta del probe. Nello SKU V2 verrà usato sia come intestazione host che come SNI |
| Path |Percorso relativo del probe. Un percorso valido inizia con "/". |
| Porta |Se definito, viene usato come porta di destinazione. In caso contrario, viene utilizzata la stessa porta delle impostazioni HTTP a cui è associata. Questa proprietà è disponibile solo nello SKU V2
| Interval |Intervallo di probe in secondi. Questo valore è l'intervallo di tempo tra due probe consecutivi |
| Timeout |Timeout del probe in secondi. Se non viene ricevuta una risposta valida entro questo periodo di timeout, il probe viene contrassegnato come non superato  |
| Soglia non integra |Numero di tentativi di probe. Il server back-end è contrassegnato come inattivo dopo che il numero di errori di probe consecutivi raggiunge la soglia di non integrità |

### <a name="probe-matching"></a>Corrispondenza Probe

Per impostazione predefinita, una risposta HTTP(S) con codice di stato compreso tra 200 e 399 viene considerata integra. I probe di integrità personalizzati supportano anche due criteri di corrispondenza. Questi criteri possono essere usati per modificare l'interpretazione predefinita di ciò che costituisce una risposta integra.

I criteri di corrispondenza sono i seguenti: 

- **Corrispondenza del codice di stato della risposta HTTP**: il criterio adottato da un probe per accettare il codice di risposta o l'intervallo di codici di risposta HTTP specificato dall'utente. Sono supportati singoli codici di stato della risposta, delimitati da virgole, o un intervallo di codici di stato.
- **Corrispondenza del corpo della risposta HTTP**: il criterio adottato dal probe in base al quale viene esaminato il corpo della risposta HTTP e viene stabilita una corrispondenza con una stringa specificata dall'utente. La corrispondenza verifica solo la presenza della stringa specificata dall'utente nel corpo della risposta e non è una corrispondenza completa basata su espressione regolare.

I criteri di corrispondenza possono essere specificati tramite il cmdlet `New-AzApplicationGatewayProbeHealthResponseMatch`.

Ad esempio:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Una volta specificati, i criteri di corrispondenza possono essere associati alla configurazione del probe tramite un parametro `-Match` in PowerShell.

## <a name="nsg-considerations"></a>Considerazioni sui gruppi di sicurezza di rete

È necessario consentire il traffico Internet in ingresso sulle porte TCP 65503-65534 per lo SKU del gateway applicazione v1 e sulle porte TCP 65200-65535 per lo SKU v2 con la subnet di destinazione impostata su **Qualsiasi** e l'origine impostata sul tag di servizio **GatewayManager**. Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure.

Non è possibile neanche bloccare la connettività Internet in uscita ed è necessario autorizzare il traffico in ingresso proveniente dal tag **AzureLoadBalancer**.

Per altre informazioni, vedere [Panoramica della configurazione del gateway applicazione](configuration-infrastructure.md#network-security-groups).

## <a name="next-steps"></a>Passaggi successivi
Dopo aver acquisito familiarità con il monitoraggio dell'integrità del gateway applicazione, è possibile configurare un [probe di integrità personalizzato](application-gateway-create-probe-portal.md) nel portale di Azure oppure un [probe di integrità personalizzato](application-gateway-create-probe-ps.md) usando PowerShell e il modello di distribuzione Azure Resource Manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
