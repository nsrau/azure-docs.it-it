---
title: Informazioni sui consigli per la sicurezza nel centro sicurezza di Azure | Microsoft Docs
description: Informazioni sul concetto di consigli per la sicurezza e su come vengono usati nel centro sicurezza di Azure per tutto.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: 6f1efa365b4f3a1ab65cc89c84af57c88325e9d2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597276"
---
# <a name="security-recommendations"></a>Raccomandazioni sulla sicurezza

Il Centro sicurezza di Azure analizza le risorse di Azure e i dispositivi Internet e fornisce consigli sulla sicurezza per ridurre la superficie di attacco. Le raccomandazioni sulla sicurezza sono praticabili e mirano a aiutare i clienti a rispettare le procedure consigliate per la sicurezza.

In questo articolo è disponibile un elenco di raccomandazioni che possono essere attivate nell'hub e/o nei dispositivi Internet delle cose.

## <a name="recommendations-for-iot-devices"></a>Suggerimenti per i dispositivi Internet

Le raccomandazioni per i dispositivi forniscono informazioni dettagliate e suggerimenti per migliorare il comportamento di sicurezza del dispositivo. 

| severity | Name                                                      | origine dati | DESCRIZIONE                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Medi   | Aprire le porte nel dispositivo                                      | Agente       | Un endpoint di ascolto è stato trovato nel dispositivo.                                                                                                                                                        |
| Medi   | Sono stati trovati criteri di firewall permissivi in una delle catene. | Agente       | Sono stati trovati criteri firewall consentiti (INPUT/OUTPUT). Il criterio firewall deve negare tutto il traffico per impostazione predefinita e definire le regole per consentire la comunicazione necessaria al/dal dispositivo.                               |
| Medi   | È stata trovata una regola permissiva del firewall nella catena di input     | Agente       | È stata rilevata una regola nel firewall che contiene un modello permissivo per un'ampia gamma di porte o indirizzi IP.                                                                                    |
| Medi   | È stata trovata una regola permissiva del firewall nella catena di output    | Agente       | È stata rilevata una regola nel firewall che contiene un modello permissivo per un'ampia gamma di porte o indirizzi IP.                                                                                   |
| Medi   | Convalida della linea di base del sistema operativo non riuscita           | Agente       | Il dispositivo non è conforme ai [benchmark Linux di CIS](https://www.cisecurity.org/cis-benchmarks/).                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Raccomandazioni operative per i dispositivi Internet

Le indicazioni operative forniscono informazioni dettagliate e suggerimenti per migliorare la configurazione degli agenti di sicurezza.

| severity | Name                                    | origine dati | DESCRIZIONE                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Basso      | Agent invia messaggi non utilizzati          | Agente       | il 10% o più messaggi di sicurezza sono inferiori a 4 KB durante le ultime 24 ore.  |
| Basso      | Configurazione del gemello di sicurezza non ottimale | Agente       | La configurazione di dispositivi gemelli di sicurezza non è ottimale.                                        |
| Basso      | Conflitto di configurazione della sicurezza dei dispositivi gemelli    | Agente       | Sono stati identificati conflitti nella configurazione dei dispositivi gemelli di sicurezza. |                          |


## <a name="recommendations-for-iot-hub"></a>Suggerimenti per l'hub Internet

Gli avvisi di raccomandazione forniscono informazioni e suggerimenti sulle azioni per migliorare il comportamento di sicurezza dell'ambiente.  

| severity | NOME                                                     | origine dati | DESCRIZIONE                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| High     | Credenziali di autenticazione identiche usate da più dispositivi | Hub IoT     | Le credenziali di autenticazione dell'hub Internet vengono usate da più dispositivi. Questo può indicare un dispositivo illegittimo che rappresenta un dispositivo legittimo. L'uso di credenziali duplicate aumenta il rischio di rappresentazione del dispositivo da parte di un attore malintenzionato. |
| Medi   | Il criterio di filtro IP predefinito deve essere Deny                  | Hub IoT     | Per la configurazione del filtro IP è necessario definire regole per il traffico consentito e, per impostazione predefinita, negare tutto il resto del traffico per impostazione predefinita.                                                                                                     |
| Medi   | La regola di filtro IP include un intervallo IP di grandi dimensioni                   | Hub IoT     | Un intervallo di indirizzi IP di origine della regola di filtro IP è troppo grande. Le regole eccessivamente permissive possono esporre l'hub delle cose a attori malintenzionati.                                                                                       |
| Basso      | Abilitare i log di diagnostica nell'hub IoT                       | Hub IoT     | Abilitare i log e conservarli per un periodo massimo di un anno. Il mantenimento dei log consente di ricreare percorsi attività a scopo di analisi quando si verifica un evento imprevisto di sicurezza o la rete viene compromessa.                                       |
|

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica](overview.md) del Centro sicurezza di Azure per il servizio Internet
- Informazioni su come [accedere ai dati di sicurezza](how-to-security-data-access.md)
- Altre informazioni sull' [analisi di un dispositivo](how-to-investigate-device.md)