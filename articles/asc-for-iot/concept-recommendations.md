---
title: Informazioni sul Centro sicurezza di AZURE di raccomandazioni di sicurezza IoT Preview | Microsoft Docs
description: Scopri il concetto di raccomandazioni sulla sicurezza e su come vengono usati nel Centro sicurezza di AZURE per IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 1e4582d93d1e3380ecdabdb241f27839d4da4565
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541858"
---
# <a name="security-recommendations"></a>Suggerimenti per la sicurezza

> [!IMPORTANT]
> Centro sicurezza di AZURE per IoT è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Basato sull'analisi della soluzione in corso, Centro sicurezza di AZURE per IoT fornisce le indicazioni seguenti quando è necessario per migliorare e proteggere i dispositivi, lo stato operativo e l'ambiente globale dell'IoT Hub. 


## <a name="device-recommendations"></a>Suggerimenti di dispositivi

Suggerimenti di dispositivi forniscono informazioni e suggerimenti per migliorare il comportamento e la sicurezza del dispositivo. 

| Gravità | NOME                                                      | origine dati | DESCRIZIONE                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Media   | Porte aperte nel dispositivo                                      | Agente       | È stato rilevato un endpoint in ascolto sul dispositivo                                                                                                                                                          |
| Media   | Sono stati trovati in una delle catene di criteri di firewall permissivi. | Agente       | Criteri firewall trovati (INPUT/OUTPUT) è consentito. Criteri firewall devono rifiutare il traffico per impostazione predefinita e definiscono delle regole per consentire la comunicazione necessaria da e verso il dispositivo.                               |
| Media   | Regola di firewall permissivi nella catena di input è stata trovata     | Agente       | È stata trovata una regola del firewall che contiene un modello permissivo per un'ampia gamma di porte o indirizzi IP.                                                                                    |
| Media   | Regola di firewall permissivi nella catena di output è stata trovata    | Agente       | È stata trovata una regola del firewall che contiene un modello permissivo per un'ampia gamma di porte o indirizzi IP.                                                                                   |
| Media   | Operazione di convalida della linea di base del sistema non è riuscita           | Agente       | Dispositivo non è conforme con [benchmark CIS Linux](https://www.cisecurity.org/cis-benchmarks/)                                                                                                         |

### <a name="operational-recommendation"></a>Raccomandazioni operative

Raccomandazioni operative forniscono informazioni e suggerimenti per migliorare la configurazione dell'agente.

| Gravità | NOME                                    | origine dati | DESCRIZIONE                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Basso      | Agent invierà messaggi inutilizzati          | Agente       | 10% o più dei messaggi di sicurezza sono stati inferiori a 4kb nelle ultime 24 ore.  |
| Basso      | Configurazione della sicurezza dei dispositivi gemelli non ottimale | Agente       | Configurazione della sicurezza dei dispositivi gemelli non è ottimale.                                        |
| Basso      | Conflitto di configurazione della sicurezza dei dispositivi gemelli    | Agente       | La configurazione della sicurezza dei dispositivi gemelli sono stati identificati i conflitti.                           |


## <a name="iot-hub-recommendations"></a>Raccomandazioni dell'IoT Hub

Gli avvisi di raccomandazione forniscono informazioni dettagliate e i suggerimenti per le azioni aumentare la sicurezza dell'ambiente.  

| Gravità | NOME                                                     | origine dati | DESCRIZIONE                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alto     | Credenziali di autenticazione identici utilizzate da più dispositivi | Hub IoT     | Le credenziali di autenticazione di IoT Hub vengono usate da più dispositivi. Questo può indicare un dispositivo illegittimo rappresentando un dispositivo legittimo. Uso delle credenziali duplicate aumenta il rischio di rappresentazione del dispositivo da un attore malintenzionato. |
| Media   | Dovrebbe essere negare i criteri di filtro IP predefiniti                  | Hub IoT     | Configurazione del filtro IP deve disporre di regole definite per il traffico consentito ed è necessario per impostazione predefinita, negare il traffico di tutti gli altri per impostazione predefinita.                                                                                                     |
| Media   | Regola del filtro IP include ampio intervallo IP                   | Hub IoT     | Un intervallo IP Consenti IP filtro regola origine è troppo grande. Le regole eccessivamente permissive possono esporre l'hub IoT per attori dannosi.                                                                                       |
| Basso      | Abilitare i log di diagnostica nell'hub IoT                       | Hub IoT     | Abilitare i log e conservarli per un periodo massimo di un anno. Conservazione dei log consente di ricreare gli itinerari di attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o di rete è compromessa.                                       |
|