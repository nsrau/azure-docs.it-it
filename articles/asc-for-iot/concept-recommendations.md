---
title: Suggerimenti per la sicurezza
description: Informazioni sul concetto di consigli per la sicurezza e su come vengono usati nel Centro sicurezza di Azure per l'IoT.Learn about the concept of security recommendations and how they are used in Azure Security Center for IoT.
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
ms.openlocfilehash: 213595ac69efc90ec855b2891641e1f00bd1ba92
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311554"
---
# <a name="security-recommendations"></a>Suggerimenti per la sicurezza

Il Centro sicurezza di Azure per IoT analizza le risorse e i dispositivi IoT di Azure e fornisce consigli sulla sicurezza per ridurre la superficie di attacco.
Le raccomandazioni di sicurezza sono attuose e mirano ad aiutare i clienti nel rispetto delle best practice di sicurezza.

In questo articolo troverai un elenco di consigli che possono essere attivati sul tuo hub IoT e/o sui dispositivi IoT.

## <a name="recommendations-for-iot-devices"></a>Raccomandazioni per i dispositivi IoT

I consigli sui dispositivi forniscono informazioni dettagliate e suggerimenti per migliorare la sicurezza dei dispositivi.

| Gravità | Nome                                                      | origine dati | Descrizione                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Media   | Aprire le porte sul dispositivo                                      | Agente       | È stato trovato un endpoint di ascolto sul dispositivo.                                                                                                                                                        |
| Media   | Criteri firewall permissivi disponibili in una delle catene. | Agente       | Sono stati trovati criteri firewall consentiti (INPUT/OUTPUT). I criteri firewall devono negare tutto il traffico per impostazione predefinita e definire le regole per consentire la comunicazione necessaria da/verso il dispositivo.                               |
| Media   | È stata trovata una regola firewall permissiva nella catena di input     | Agente       | È stata trovata una regola nel firewall che contiene un modello permissivo per un'ampia gamma di indirizzi IP o porte.                                                                                    |
| Media   | È stata trovata una regola del firewall permissiva nella catena di output    | Agente       | È stata trovata una regola nel firewall che contiene un modello permissivo per un'ampia gamma di indirizzi IP o porte.                                                                                   |
| Media   | La convalida della linea di base del sistema operativo non è riuscita           | Agente       | Il dispositivo non è conforme [ai benchmark CIS Linux.](https://www.cisecurity.org/cis-benchmarks/)                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Consigli operativi per i dispositivi IoT

Consigli operativi forniscono informazioni dettagliate e suggerimenti per migliorare la configurazione degli agenti di sicurezza.

| Gravità | Nome                                    | origine dati | Descrizione                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Basso      | L'agente invia messaggi non utilizzati          | Agente       | 10% o più dei messaggi di protezione sono stati inferiori a 4 KB nelle ultime 24 ore.  |
| Basso      | Configurazione di Security Twin non ottimale | Agente       | La configurazione di Security Twin non è ottimale.                                        |
| Basso      | Conflitto di configurazione di sicurezza gemelloSecurity twin configuration conflict    | Agente       | Sono stati identificati conflitti nella configurazione di sicurezza gemella. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Raccomandazioni per l'hub IoT

Gli avvisi di raccomandazione forniscono informazioni dettagliate e suggerimenti per le azioni volte a migliorare la sicurezza dell'ambiente.

| Gravità | Nome                                                     | origine dati | Descrizione                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alto     | Credenziali di autenticazione identiche usate da più dispositivi | Hub IoT     | Le credenziali di autenticazione dell'hub IoT vengono usate da più dispositivi. Ciò potrebbe indicare un dispositivo illegittimo che impersona un dispositivo legittimo. L'uso di credenziali duplicate aumenta il rischio di rappresentazione del dispositivo da parte di un attore malintenzionato. |
| Media   | Il criterio di filtro IP predefinito deve essere negato                  | Hub IoT     | La configurazione del filtro IP deve avere regole definite per il traffico consentito e deve per impostazione predefinita negare tutto il traffico per impostazione predefinita.                                                                                                     |
| Media   | La regola di filtro IP include un ampio intervallo IP                   | Hub IoT     | L'intervallo IP di origine della regola di filtro IP consenti è troppo grande. Le regole eccessivamente permissive possono esporre l'hub IoT ad attori malintenzionati.                                                                                       |
| Basso      | Abilitare i log di diagnostica nell'hub IoTEnable diagnostics logs in IoT Hub                       | Hub IoT     | Abilitare i log e conservarli per un periodo massimo di un anno. La conservazione dei registri consente di ricreare i percorsi attività a scopo di indagine quando si verifica un incidente di sicurezza o la rete viene compromessa.                                       |
|

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica](overview.md) del Centro sicurezza di Azure per il servizio IoTAzure Security Center for IoT service Overview
- Scopri come accedere ai dati di [sicurezza](how-to-security-data-access.md)
- Ulteriori informazioni [sull'analisi di un dispositivo](how-to-investigate-device.md)
