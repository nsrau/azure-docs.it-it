---
title: Avvisi di sicurezza personalizzabili
description: Informazioni sugli avvisi di sicurezza personalizzabili e sulla correzione consigliata usando il Centro sicurezza di Azure per le funzionalità e i servizi di Internet.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: f676c4129b79499eb9ed524821a336b3859dbb3c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004852"
---
# <a name="azure-security-center-for-iot-customizable-security-alerts"></a>Centro sicurezza di Azure per gli avvisi di sicurezza personalizzabili

Il Centro sicurezza di Azure consente di analizzare continuamente la soluzione Internet delle cose usando analisi avanzate e Intelligence per le minacce per segnalare attività dannose.

Si consiglia di creare avvisi personalizzati in base alla propria conoscenza del comportamento previsto del dispositivo per assicurarsi che gli avvisi fungano da indicatori più efficienti di potenziali compromessi per la distribuzione e il paesaggio aziendali univoci.

Il seguente elenco di Centro sicurezza di Azure per gli avvisi di Internet è definibile dall'utente in base all'hub e/o al comportamento del dispositivo. Per altri dettagli su come personalizzare ogni avviso, vedere [creare avvisi personalizzati](quickstart-create-custom-alerts.md).

## <a name="iot-hub-alerts-available-for-customization"></a>Avvisi dell'hub per le cose disponibili per la personalizzazione

| Gravità | Nome avviso | Origine dati | Descrizione | Correzione consigliata|
|---|---|---|---|---|
| Basso      | Avviso personalizzato: il numero di messaggi da cloud a dispositivo nel protocollo AMQP non rientra nell'intervallo consentito          | Hub IoT     | Il numero di messaggi da cloud a dispositivo (protocollo AMQP) in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito.||
| Basso      | Avviso personalizzato: il numero di messaggi rifiutati da cloud a dispositivo nel protocollo AMQP non rientra nell'intervallo consentito | Hub IoT     | Il numero di messaggi da cloud a dispositivo (protocollo AMQP) rifiutati dal dispositivo in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito.||
| Basso      | Avviso personalizzato: il numero di messaggi da dispositivo a cloud nel protocollo AMQP non rientra nell'intervallo consentito      | Hub IoT     | La quantità di messaggi da dispositivo a cloud (protocollo AMQP) in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito.|   |
| Basso      | Avviso personalizzato: il numero di chiamate del metodo diretto non rientra nell'intervallo consentito | Hub IoT     | La quantità di richiami al metodo diretto in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito.||
| Basso      | Avviso personalizzato: il numero di caricamenti di file non rientra nell'intervallo consentito | Hub IoT     | La quantità di caricamenti di file in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito.| |
| Basso      | Avviso personalizzato: il numero di messaggi da cloud a dispositivo nel protocollo HTTP non rientra nell'intervallo consentito | Hub IoT     | La quantità di messaggi dal cloud al dispositivo (protocollo HTTP) in un intervallo di tempo non è compreso nell'intervallo consentito configurato                                  |
| Basso      | Avviso personalizzato: il numero di messaggi rifiutati da cloud a dispositivo nel protocollo HTTP non rientra nell'intervallo consentito | Hub IoT     | La quantità di messaggi da cloud a dispositivo (protocollo HTTP) in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito. |
| Basso      | Avviso personalizzato: il numero di messaggi da dispositivo a cloud nel protocollo HTTP non rientra nell'intervallo consentito | Hub IoT| La quantità di messaggi da dispositivo a cloud (protocollo HTTP) in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito.|    |
| Basso      | Avviso personalizzato: il numero di messaggi da cloud a dispositivo nel protocollo MQTT non rientra nell'intervallo consentito | Hub IoT     | La quantità di messaggi da cloud a dispositivo (protocollo MQTT) in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito.|   |
| Basso      | Avviso personalizzato: il numero di messaggi rifiutati da cloud a dispositivo nel protocollo MQTT non rientra nell'intervallo consentito | Hub IoT     | La quantità di messaggi da cloud a dispositivo (protocollo MQTT) rifiutati dal dispositivo in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito. |
| Basso      | Avviso personalizzato: il numero di messaggi da dispositivo a cloud nel protocollo MQTT non rientra nell'intervallo consentito          | Hub IoT     | La quantità di messaggi da dispositivo a cloud (protocollo MQTT) in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito.|
| Basso      | Avviso personalizzato: il numero di pulizie della coda dei comandi non rientra nell'intervallo consentito                               | Hub IoT     | La quantità di pulizie della coda di comando in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito.||
| Basso      | Avviso personalizzato: il numero di aggiornamenti dei moduli gemelli non rientra nell'intervallo consentito                                       | Hub IoT     | La quantità di aggiornamenti di moduli gemelli in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito.|
| Basso      | Avviso personalizzato: il numero di operazioni non autorizzate non rientra nell'intervallo consentito  | Hub IoT     | La quantità di operazioni non autorizzate in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito.|
|

## <a name="agent-alerts-available-for-customization"></a>Avvisi agente disponibili per la personalizzazione

| Gravità | Nome avviso | Origine dati | Descrizione | Correzione consigliata|
|---|---|---|---|---|
| Basso      | Avviso personalizzato: il numero di connessioni attive non rientra nell'intervallo consentito  | Agente       | Il numero connessioni attive in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito.|  Esaminare i log del dispositivo. Scoprire l'origine della connessione e determinare se è dannosa o meno. Se è dannosa, rimuovere il possibile malware e identificare l'origine. Se non è dannosa, aggiungere l'origine all'elenco di connessioni consentite.  |
| Basso      | Avviso personalizzato: è stata creata una connessione in uscita a un indirizzo IP non consentito                             | Agente       | È stata creata una connessione in uscita a un IP che non rientra nell'elenco di indirizzi IP consentiti. |Esaminare i log del dispositivo. Scoprire l'origine della connessione e determinare se è dannosa o meno. Se è dannosa, rimuovere il possibile malware e identificare l'origine. Se non è dannosa, aggiungere l'origine all'elenco di indirizzi IP consentiti.                        |
| Basso      | Avviso personalizzato: il numero di accessi locali non riusciti non rientra nell'intervallo consentito                               | Agente       | La quantità di accessi locali non riusciti in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito. |   |
| Basso      | Avviso personalizzato: accesso di un utente non incluso nell'elenco di utenti consentiti | Agente       | Un utente locale non incluso nell'elenco di utenti consentiti ha effettuato l'accesso al dispositivo.|  Se si salvano dati non elaborati, passare all'account di Log Analytics e usare i dati per esaminare il dispositivo, identificare l'origine e quindi correggere l'elenco di elementi consentiti/bloccati per tali impostazioni. Se attualmente non si salvano dati non elaborati, passare al dispositivo e correggere l'elenco di elementi consentiti/bloccati per tali impostazioni.|
| Basso      | Avviso personalizzato: è stato eseguito un processo non consentito | Agente       | Nel dispositivo è stato eseguito un processo non consentito. |Se si salvano dati non elaborati, passare all'account di Log Analytics e usare i dati per esaminare il dispositivo, identificare l'origine e quindi correggere l'elenco di elementi consentiti/bloccati per tali impostazioni. Se attualmente non si salvano dati non elaborati, passare al dispositivo e correggere l'elenco di elementi consentiti/bloccati per tali impostazioni.  |
|

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [personalizzare un avviso](quickstart-create-custom-alerts.md)
- [Panoramica](overview.md) del Centro sicurezza di Azure per il servizio Internet
- Informazioni su come [accedere ai dati di sicurezza](how-to-security-data-access.md)
- Altre informazioni sull' [analisi di un dispositivo](how-to-investigate-device.md)
