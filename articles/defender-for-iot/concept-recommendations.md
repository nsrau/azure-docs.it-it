---
title: Suggerimenti per la sicurezza
description: Informazioni sul concetto di consigli sulla sicurezza e su come vengono usati in Defender per l'it.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 0eccab6c3d59ad68ddc8f96c3d84c57dc1bbeeca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939522"
---
# <a name="security-recommendations"></a>Suggerimenti per la sicurezza

Defender for Internet esegue l'analisi delle risorse di Azure e dei dispositivi Internet e fornisce consigli sulla sicurezza per ridurre la superficie di attacco.
Le raccomandazioni sulla sicurezza sono praticabili e mirano a aiutare i clienti a rispettare le procedure consigliate per la sicurezza.

In questo articolo è disponibile un elenco di raccomandazioni che possono essere attivate nell'hub e/o nei dispositivi Internet delle cose.

## <a name="recommendations-for-iot-devices"></a>Suggerimenti per i dispositivi Internet

Le raccomandazioni per i dispositivi forniscono informazioni dettagliate e suggerimenti per migliorare il comportamento di sicurezza del dispositivo.

| Gravità | Nome                                                      | origine dati | Descrizione                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Media   | Aprire le porte nel dispositivo                                      | Agente       | Un endpoint di ascolto è stato trovato nel dispositivo.                                                                                                                                                        |
| Media   | Sono stati trovati criteri di firewall permissivi in una delle catene. | Agente       | Sono stati trovati criteri firewall consentiti (INPUT/OUTPUT). Il criterio firewall deve negare tutto il traffico per impostazione predefinita e definire le regole per consentire la comunicazione necessaria al/dal dispositivo.                               |
| Media   | È stata trovata una regola permissiva del firewall nella catena di input     | Agente       | È stata rilevata una regola nel firewall che contiene un modello permissivo per un'ampia gamma di porte o indirizzi IP.                                                                                    |
| Media   | È stata trovata una regola permissiva del firewall nella catena di output    | Agente       | È stata rilevata una regola nel firewall che contiene un modello permissivo per un'ampia gamma di porte o indirizzi IP.                                                                                   |
| Media   | Convalida della linea di base del sistema operativo non riuscita           | Agente       | Il dispositivo non è conforme ai [benchmark Linux di CIS](https://www.cisecurity.org/cis-benchmarks/).                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Raccomandazioni operative per i dispositivi Internet

Le indicazioni operative forniscono informazioni dettagliate e suggerimenti per migliorare la configurazione degli agenti di sicurezza.

| Gravità | Nome                                    | origine dati | Descrizione                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Basso      | Agent invia messaggi non utilizzati          | Agente       | il 10% o più messaggi di sicurezza sono inferiori a 4 KB durante le ultime 24 ore.  |
| Basso      | Configurazione del gemello di sicurezza non ottimale | Agente       | La configurazione di dispositivi gemelli di sicurezza non è ottimale.                                        |
| Basso      | Conflitto di configurazione della sicurezza dei dispositivi gemelli    | Agente       | Sono stati identificati conflitti nella configurazione dei dispositivi gemelli di sicurezza. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Suggerimenti per l'hub Internet

Gli avvisi di raccomandazione forniscono informazioni e suggerimenti sulle azioni per migliorare il comportamento di sicurezza dell'ambiente.

| Gravità | Nome                                                     | origine dati | Descrizione                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alto     | Credenziali di autenticazione identiche usate da più dispositivi | Hub IoT     | Le credenziali di autenticazione dell'hub Internet vengono usate da più dispositivi. Questo può indicare un dispositivo illegittimo che rappresenta un dispositivo legittimo. L'uso di credenziali duplicate aumenta il rischio di rappresentazione del dispositivo da parte di un attore malintenzionato. |
| Media   | Il criterio di filtro IP predefinito deve essere Deny                  | Hub IoT     | Per la configurazione del filtro IP è necessario definire regole per il traffico consentito e, per impostazione predefinita, negare tutto il resto del traffico per impostazione predefinita.                                                                                                     |
| Media   | La regola di filtro IP include un intervallo IP di grandi dimensioni                   | Hub IoT     | Un intervallo di indirizzi IP di origine della regola di filtro IP è troppo grande. Le regole eccessivamente permissive possono esporre l'hub delle cose a attori malintenzionati.                                                                                       |
| Basso      | Abilitare i log di diagnostica nell'hub Internet                       | Hub IoT     | Abilitare i log e conservarli per un periodo massimo di un anno. Il mantenimento dei log consente di ricreare percorsi attività a scopo di analisi quando si verifica un evento imprevisto di sicurezza o la rete viene compromessa.                                       |
|

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica](overview.md) di Defender per il servizio Internet
- Informazioni su come [accedere ai dati di sicurezza](how-to-security-data-access.md)
- Altre informazioni sull' [analisi di un dispositivo](how-to-investigate-device.md)
