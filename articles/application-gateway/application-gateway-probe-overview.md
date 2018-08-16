---
title: Panoramica del monitoraggio dell'integrità per il gateway applicazione di Azure
description: Informazioni sulle funzionalità di monitoraggio nel gateway applicazione di Azure
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 8/6/2018
ms.author: victorh
ms.openlocfilehash: b34e5317a35d694e8521e73b0846da973661d9df
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39530430"
---
# <a name="application-gateway-health-monitoring-overview"></a>Panoramica del monitoraggio dell'integrità del gateway applicazione

Per impostazione predefinita, il gateway applicazione di Azure monitora l'integrità di tutte le risorse nel pool back-end e rimuove automaticamente dal pool le risorse considerate non integre. Il gateway applicazione continua a monitorare le istanze non integre e le riaggiunge al pool back-end integro, dopo che sono diventate disponibili e rispondono ai probe di integrità. Il gateway applicazione invia i probe di integrità con la stessa porta definita nelle impostazioni HTTP del back-end. Questa configurazione garantisce che il probe testi la stessa porta che verrebbe usata dai clienti per connettersi al back-end.

![Esempio di probe del gateway applicazione][1]

Oltre al monitoraggio del probe di integrità predefinito, è anche possibile personalizzare il probe di integrità in base ai requisiti dell'applicazione. In questo articolo vengono illustrati i probe di integrità predefiniti e personalizzati.

## <a name="default-health-probe"></a>Probe di integrità predefinito

Un gateway applicazione configura automaticamente un probe di integrità predefinito quando non si configura nessuna configurazione di probe personalizzato. Il comportamento del monitoraggio prevede l'invio di una richiesta HTTP agli indirizzi IP configurati per il pool back-end. Per i probe predefiniti, se le impostazioni HTTP del back-end vengono configurate per HTTPS, il probe usa HTTPS anche per testare l'integrità dei back-end.

Ad esempio, si configura il gateway applicazione per usare i server back-end A, B e C per ricevere il traffico di rete HTTP sulla porta 80. Il monitoraggio dell'integrità predefinito testa i tre server ogni 30 secondi per ottenere una risposta HTTP integra. Una risposta HTTP integra ha un [codice di stato](https://msdn.microsoft.com/library/aa287675.aspx) compreso tra 200 e 399.

Se il controllo probe predefinito non riesce per il server A, il gateway applicazione lo rimuove dal pool back-end e il flusso del traffico di rete a questo server viene arrestato. Il probe predefinito continua tuttavia a controllare il server A ogni 30 secondi. Quando il server A risponde correttamente a una richiesta di un probe di integrità predefinito, viene aggiunto di nuovo come integro al pool back-end e il flusso del traffico verso il server riprenderà.

### <a name="probe-matching"></a>Criteri di corrispondenza dei probe

Per impostazione predefinita, una risposta HTTP(S) con codice di stato 200 viene considerata integra. I probe di integrità personalizzati supportano anche due criteri di corrispondenza. Questi criteri possono essere usati per modificare l'interpretazione predefinita di ciò che costituisce una risposta integra.

I criteri di corrispondenza sono i seguenti: 

- **Corrispondenza del codice di stato della risposta HTTP**: il criterio adottato da un probe per accettare il codice di risposta o l'intervallo di codici di risposta HTTP specificato dall'utente. Sono supportati singoli codici di stato della risposta, delimitati da virgole, o un intervallo di codici di stato.
- **Corrispondenza del corpo della risposta HTTP**: il criterio adottato dal probe in base al quale viene esaminato il corpo della risposta HTTP e viene stabilita una corrispondenza con una stringa specificata dall'utente. La corrispondenza verifica solo la presenza della stringa specificata dall'utente nel corpo della risposta e non è una corrispondenza completa basata su espressione regolare.

I criteri di corrispondenza possono essere specificati tramite il cmdlet `New-AzureRmApplicationGatewayProbeHealthResponseMatch`.

Ad esempio: 

```
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Una volta specificati, i criteri di corrispondenza possono essere associati alla configurazione del probe tramite un parametro `-Match` in PowerShell.

### <a name="default-health-probe-settings"></a>Impostazioni del probe di integrità predefinito

| Proprietà probe | Valore | DESCRIZIONE |
| --- | --- | --- |
| URL probe |http://127.0.0.1:\<port\>/ |Percorso URL |
| Interval |30 |Il tempo di attesa in secondi prima di inviare il probe di integrità successivo.|
| Timeout |30 |Il tempo in secondi che un gateway applicazione trascorre in attesa di una risposta del probe prima di contrassegnare il probe come non integro. Se un probe viene restituito come integro, il back-end corrispondente viene subito contrassegnato anch'esso come tale.|
| Soglia non integra |3 |Determina quanti probe inviare in caso di errore del probe di integrità normale. Questi probe di integrità aggiuntivi vengono inviati in rapida successione per determinare velocemente del back-end e non attendono l'intervallo. Il server back-end viene contrassegnato come inattivo dopo che il numero di errori di probe consecutivi ha raggiunto una soglia non integra. |

> [!NOTE]
> La porta è la stessa delle impostazioni HTTP del back-end.

Il probe predefinito esamina solo http://127.0.0.1:\<port\> per determinare lo stato di integrità. Se si deve configurare il probe di integrità per passare a un URL personalizzato o modificare altre impostazioni, è necessario usare probe personalizzati.

### <a name="probe-intervals"></a>Intervalli probe

Tutte le istanze del gateway applicazione eseguono il probe del back-end in modo indipendente. La stessa configurazione di probe si applica a ogni istanza del gateway applicazione. Ad esempio, se la configurazione del probe consiste nell'inviare probe di integrità ogni 30 secondi e se il gateway applicazione dispone di due istanze, entrambe le istanze invieranno il probe di integrità ogni 30 secondi.

Se sono presenti più listener, ogni listener esegue il probe nel back-end in modo indipendente. Ad esempio, se sono presenti due listener che puntano allo stesso pool back-end su due porte diverse, ossia configurate da due impostazioni http di back-end, ogni listener eseguirà il probe dello stesso back-end in modo indipendente. In questo caso, esistono due probe da ogni istanza del gateway applicazione per i due listener. Se sono presenti due istanze del gateway applicazione in questo scenario, la macchina virtuale back-end vedrà quattro probe per l'intervallo di probe configurato.

## <a name="custom-health-probe"></a>Probe di integrità personalizzato

I probe personalizzati consentono un controllo più granulare sul monitoraggio dell'integrità. Quando si usano i probe personalizzati, è possibile configurare l'intervallo di probe, l'URL e il percorso da testare e il numero di risposte non riuscite da accettare prima di contrassegnare l'istanza del pool back-end come non integra.

### <a name="custom-health-probe-settings"></a>Impostazioni del probe di integrità personalizzato

La tabella seguente fornisce le definizioni delle proprietà di un probe di integrità personalizzato.

| Proprietà probe | DESCRIZIONE |
| --- | --- |
| NOME |Nome del probe. Questo nome viene usato per fare riferimento al probe nelle impostazioni HTTP back-end |
| Protocollo |Protocollo usato per inviare il probe. Il probe usa il protocollo definito nelle impostazioni HTTP del back-end. |
| Host |Nome host per inviare il probe. Applicabile solo quando vengono configurati più siti nel gateway applicazione. In caso contrario, usare "127.0.0.1". Questo valore è diverso dal nome host della VM. |
| path |Percorso relativo del probe. Il percorso valido inizia da "/". |
| Interval |Intervallo di probe in secondi. Questo valore è l'intervallo di tempo tra due probe consecutivi. |
| Timeout |Timeout del probe in secondi. Se non viene ricevuta una risposta valida entro questo periodo di timeout, il probe viene contrassegnato come non riuscito.  |
| Soglia non integra |Numero di tentativi di probe. Il server back-end viene contrassegnato come inattivo dopo che il numero di errori di probe consecutivi ha raggiunto una soglia non integra. |

> [!IMPORTANT]
> Se il gateway applicazione è configurato per un singolo sito, per impostazione predefinita il nome dell'host deve essere specificato come "127.0.0.1", se non diversamente configurato nel probe personalizzato.
> Come riferimento, un probe personalizzato viene inviato a \<protocollo\>://\<host\>:\<porta\>\<percorso\>. La porta usata sarà la stessa definita nelle impostazioni HTTP del back-end.

## <a name="nsg-considerations"></a>Considerazioni sui gruppi di sicurezza di rete

Se nella subnet del gateway applicazione è presente un gruppo di sicurezza di rete, aprire gli intervalli di porte 65503-65534 nella subnet del gateway applicazione per il traffico in ingresso. Tali porte sono necessarie per il corretto funzionamento dell'API di integrità back-end.

Inoltre, non è possibile bloccare la connettività Internet in uscita ed è necessario autorizzare il traffico dal tag AzureLoadBalancer.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver acquisito familiarità con il monitoraggio dell'integrità del gateway applicazione, è possibile configurare un [probe di integrità personalizzato](application-gateway-create-probe-portal.md) nel portale di Azure oppure un [probe di integrità personalizzato](application-gateway-create-probe-ps.md) usando PowerShell e il modello di distribuzione Azure Resource Manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
