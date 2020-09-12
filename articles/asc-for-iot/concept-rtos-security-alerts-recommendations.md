---
title: Modulo di sicurezza per Azure RTO incorporato & avvisi e consigli personalizzabili
description: Informazioni sugli avvisi di sicurezza e sulla correzione consigliata con il modulo Azure Internet per la sicurezza-RTO.
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: ebb4edf10433cce981b4718835676d0a5d76ba40
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514882"
---
# <a name="security-module-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Modulo di sicurezza per gli avvisi di sicurezza e le raccomandazioni di Azure RTO (anteprima)

Il modulo Security per Azure RTO analizza continuamente la soluzione Internet delle cose usando analisi avanzate e Intelligence per le minacce per avvertire l'utente di potenziali attività dannose e modifiche sospette del sistema. È anche possibile creare avvisi personalizzati in base alle proprie conoscenze sul comportamento previsto e sulle baseline del dispositivo.

Un modulo di sicurezza per l'avviso di Azure RTO funge da indicatore del potenziale compromesso e deve essere analizzato e aggiornato. Un modulo di sicurezza per la raccomandazione Azure RTO identifica un comportamento di sicurezza debole da correggere e aggiornare. 

In questo articolo è presente un elenco di avvisi e consigli predefiniti che vengono attivati in base agli intervalli predefiniti e personalizzabili con i propri valori, in base al comportamento previsto o di base. 

Per altre informazioni sul funzionamento della personalizzazione degli avvisi nel centro sicurezza di Azure per il servizio Internet, vedere gli [avvisi personalizzabili](concept-customizable-security-alerts.md). Gli avvisi e le raccomandazioni specifici disponibili per la personalizzazione quando si usa il modulo di sicurezza per Azure RTO sono descritti in dettaglio nelle tabelle seguenti. 

## <a name="security-module-for-azure-rtos-supported-security-alerts"></a>Modulo di sicurezza per gli avvisi di sicurezza supportati da Azure RTO

### <a name="device-related-security-alerts"></a>Avvisi di sicurezza relativi al dispositivo

|Attività di avviso di sicurezza correlata al dispositivo  |Nome avviso  |
|---------|---------|
|Indirizzo IP| Comunicazione con un indirizzo IP sospetto rilevato|
|Identificazione personale del certificato del dispositivo X. 509|Identificazione personale del certificato del dispositivo X. 509 non corrispondente|
|Certificato X.509| Certificato X. 509 scaduto|
|Token di firma di accesso condiviso| Token SAS scaduto|
|Token di firma di accesso condiviso| Firma del token SAS non valida|

### <a name="iot-hub-related-security-alerts"></a>Avvisi di sicurezza correlati all'hub Internet

|Attività avviso di sicurezza dell'hub Internet  |Nome avviso  |
|---------|---------|
|Aggiungere un certificato    |  È stato rilevato un tentativo non riuscito di aggiungere un certificato a un hub Internet.       |
|Aggiunta o modifica di un'impostazione di diagnostica    | È stato rilevato un tentativo di aggiungere o modificare un'impostazione di diagnostica di un hub Internet.      |
|Eliminare un certificato    |  È stato rilevato un tentativo non riuscito di eliminare un certificato da un hub Internet       |
|Eliminare un'impostazione di diagnostica    |  Rilevato tentativo di eliminazione di un'impostazione di diagnostica da un hub Internet      |
|Certificato eliminato    | L'eliminazione di un certificato da un hub Internet è stata rilevata        |
|Nuovo certificato     |  È stata rilevata l'aggiunta di un nuovo certificato a un hub Internet.       |

## <a name="security-module-for-azure-rtos-supported-customizable-alerts"></a>Modulo di sicurezza per Azure RTO supporta gli avvisi personalizzabili

### <a name="device-related-customizable-alerts"></a>Avvisi personalizzabili correlati al dispositivo

|Attività correlata al dispositivo |Nome avviso  |
|---------|---------|
|Connessioni attive|Il numero di connessioni attive non è compreso nell'intervallo consentito|
|Messaggi da cloud a dispositivo nel protocollo **MQTT**|Il numero di messaggi da cloud a dispositivo nel protocollo **MQTT** non è compreso nell'intervallo consentito|
|Connessione in uscita| Connessione in uscita a un indirizzo IP non consentito|

### <a name="hub-related-customizable-alerts"></a>Avvisi personalizzabili correlati all'hub 

|Attività correlate all'hub  |Nome avviso  |
|---------|---------|
|Ripulitura della coda di comandi     |  Numero di eliminazioni della coda dei comandi non comprese nell'intervallo consentito       |
|Messaggi da cloud a dispositivo nel protocollo **MQTT**    |  Numero di messaggi da cloud a dispositivo nel protocollo **MQTT** al di fuori dell'intervallo consentito       |
|Messaggi da dispositivo a cloud nel protocollo **MQTT**    | Numero di messaggi da dispositivo a cloud nel protocollo **MQTT** al di fuori dell'intervallo consentito        |
|Invoke di metodi diretti     |  Numero di richiami al metodo diretto non compresi nell'intervallo consentito       |
|Messaggi da cloud a dispositivo rifiutati nel protocollo **MQTT**     |   Numero di messaggi da cloud a dispositivo rifiutati nel protocollo **MQTT** al di fuori dell'intervallo consentito      |
|Aggiornamenti ai moduli gemelli     |  Numero di aggiornamenti ai moduli gemelli al di fuori dell'intervallo consentito       |
|Operazioni non autorizzate    |  Numero di operazioni non autorizzate al di fuori dell'intervallo consentito       |

## <a name="security-module-for-azure-rtos-supported-recommendations"></a>Suggerimenti supportati per il modulo di sicurezza per Azure RTO

### <a name="device-related-recommendations"></a>Suggerimenti relativi ai dispositivi

|Attività correlata al dispositivo  |Nome raccomandazione |
|---------|---------|
|Credenziali di autenticazione    |  Credenziali di autenticazione identiche usate da più dispositivi       |

### <a name="hub-related-recommendations"></a>Raccomandazioni relative all'hub

|Attività correlate all'hub Internet  |Nome raccomandazione |
|---------|---------|
|Criterio filtro IP   |  I criteri di filtro IP predefiniti devono essere impostati su **Nega**  |
|Regola di filtro IP| La regola di filtro IP include un intervallo IP di grandi dimensioni|
|Log di diagnostica|Suggerimento per abilitare i log di diagnostica nell'hub Internet|

### <a name="all-azure-security-center-for-iot-alerts-and-recommendations"></a>Tutto il Centro sicurezza di Azure per gli avvisi e le raccomandazioni

Per un elenco completo del Centro sicurezza di Azure per gli avvisi e le raccomandazioni correlati ai servizi Internet, vedere gli [avvisi di sicurezza](concept-security-alerts.md)per le cose, [consigli](concept-recommendations.md)sulla sicurezza di Internet.

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva: modulo di sicurezza per Azure RTO](quickstart-azure-rtos-security-module.md)
- [Configurare e personalizzare il modulo di sicurezza per Azure RTO](how-to-azure-rtos-security-module.md)
- Vedere il [modulo di sicurezza per l'API RTO di Azure](azure-rtos-security-module-api.md)