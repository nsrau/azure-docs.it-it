---
title: Risolvere i problemi di avvio dell'agente di sicurezza (Linux)Troubleshoot security agent start-up (Linux)
description: Risolvere i problemi di utilizzo del Centro sicurezza di Azure per gli agenti di sicurezza IoT per Linux.Troubleshoot working with Azure Security Center for IoT security agents for Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 935a99dd34b0a4e3d4970e8d91f9332d2bc1489a
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310561"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Guida alla risoluzione dei problemi dell'agente di sicurezza (Linux)

In questo articolo viene illustrato come risolvere potenziali problemi nel processo di avvio dell'agente di sicurezza.

Il Centro sicurezza di Azure per l'agente IoT viene avviato automaticamente immediatamente dopo l'installazione. Il processo di avvio dell'agente include la lettura della configurazione locale, la connessione all'hub IoT di Azure e il recupero della configurazione duplice remota. Un errore in uno di questi passaggi può causare l'errore dell'agente di sicurezza.

In questa guida alla risoluzione dei problemi imparerai a:

> [!div class="checklist"]
> * Verificare se l'agente di sicurezza è in esecuzione
> * Errori relativi ai security agentGet security agent errors
> * Comprendere e correggere gli errori dei security agentUnderstand and remediate security agent errors

## <a name="validate-if-the-security-agent-is-running"></a>Verificare se l'agente di sicurezza è in esecuzione

1. Per la convalida è che l'agente di sicurezza è in esecuzione, attendere alcuni minuti dopo l'installazione dell'agente ed eseguire il comando seguente.
     <br>

    **Agente C**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **Agente di C**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. Se il comando restituisce una riga vuota, l'agente di sicurezza non è stato in grado di avviare correttamente.

## <a name="force-stop-the-security-agent"></a>Arrestare forzatamente l'agente di sicurezza

Nei casi in cui l'agente di sicurezza non è in grado di avviare, arrestare l'agente con il comando seguente, quindi passare alla tabella di errore riportata di seguito:

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>Errori relativi ai security agentGet security agent errors

1. Recuperare gli errori del security agent eseguendo il comando seguente:

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. Il comando get security agent error recupera tutti i log creati dal Centro sicurezza di Azure per l'agente IoT.The get security agent error command retrieves all logs created by the Azure Security Center for IoT agent. Utilizzare la tabella seguente per comprendere gli errori ed eseguire la procedura corretta per la correzione.

> [!Note]
> I log degli errori vengono visualizzati in ordine cronologico. Assicurarsi di prendere nota del timestamp di ogni errore per facilitare la correzione.

## <a name="restart-the-agent"></a>Riavviare l'agente

1. Dopo aver individuato e risolto un errore del Security Agent, provare a riavviare l'agente eseguendo il comando seguente.

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. Ripetere il processo precedente per recuperare gli errori di arresto e recuperare se l'agente continua a non riuscire il processo di avvio.

## <a name="understand-security-agent-errors"></a>Comprendere gli errori dei security agentUnderstand security agent errors

La maggior parte degli errori del Security Agent vengono visualizzati nel seguente formato:

```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| Codice di errore | Sottocodice di errore | Dettagli errore | Correzione C | Correzione C # |
|:-----------|:---------------|:--------|:------------|:------------|
| Configurazione locale | Configurazione mancante | Manca una configurazione nel file di configurazione locale. Il messaggio di errore deve indicare quale chiave è mancante. | Aggiungere la chiave mancante al file /var/LocalConfiguration.json, vedere il [riferimento cs-localconfig](azure-iot-security-local-configuration-c.md) per i dettagli.| Aggiungere la chiave mancante per il file General.config, vedere il riferimento di [c-localconfig](azure-iot-security-local-configuration-csharp.md) per i dettagli. |
| Configurazione locale | Cant Parse Configuration | Non è possibile analizzare un valore di configurazione. Il messaggio di errore deve indicare quale chiave non può essere analizzata. Un valore di configurazione non può essere analizzato perché il valore non è nel tipo previsto o il valore non è compreso nell'intervallo. | Correggere il valore della chiave nel file /var/LocalConfiguration.json in modo che corrisponda allo schema LocalConfiguration, vedere il riferimento di [configurazione locale di c'è](azure-iot-security-local-configuration-csharp.md) per i dettagli. |  Correggere il valore della chiave nel file General.config in modo che corrisponda allo schema, vedere il [cs-localconfig-reference](azure-iot-security-local-configuration-c.md) per i dettagli.|
| Configurazione locale | Formato file | Impossibile analizzare il file di configurazione. | Il file di configurazione è danneggiato, scaricare l'agente e reinstallarlo. | |
| Configurazione remota | Timeout | L'agente non è riuscito a recuperare il modulo gemello di azureiotsecurity entro il periodo di timeout. | Assicurarsi che la configurazione dell'autenticazione sia corretta e riprovare. | L'agente non è riuscito a recuperare il modulo di azureiotsecurity gemello entro il periodo di timeout. | Assicurarsi che la configurazione dell'autenticazione sia corretta e riprovare. |
| Authentication | File non esistente | Il file nel percorso specificato non esiste. | Assicurarsi che il file esista nel percorso specificato o passare al file **LocalConfiguration.json** e modificare la configurazione di **FilePath.** | Assicurarsi che il file esista nel percorso specificato o passare al file **Authentication.config** e modificare la configurazione di **filePath.**|
| Authentication | Autorizzazione file | L'agente non dispone di autorizzazioni sufficienti per aprire il file. | Assegnare all'utente **dell'asciotagent** le autorizzazioni di lettura per il file nel percorso specificato. | Assicurarsi che il file sia accessibile. |
| Authentication | Formato file | Il file specificato non è nel formato corretto. | Assicurarsi che il file sia nel formato corretto. I tipi di file supportati sono .pfx e .pem. | Assicurarsi che il file sia un file di certificato valido. |
| Authentication | Non autorizzata | L'agente non è stato in grado di eseguire l'autenticazione nell'hub IoT con le credenziali specificate. | Convalidare la configurazione dell'autenticazione nel file LocalConfiguration, passare attraverso la configurazione di autenticazione e assicurarsi che tutti i dettagli siano corretti, verificare che il segreto nel file corrisponda all'identità autenticata. | Convalidare la configurazione dell'autenticazione in Authentication.config, passare attraverso la configurazione di autenticazione e assicurarsi che tutti i dettagli siano corretti, quindi verificare che il segreto nel file corrisponda all'identità autenticata.
| Authentication | Non trovato | Il dispositivo / modulo è stato trovato. | Convalidare la configurazione dell'autenticazione: assicurarsi che il nome host sia corretto, che il dispositivo esista nell'hub IoT e disponga di un modulo gemello azureiotsecurity.Validate authentication configuration - make sure the hostname is correct, the device exists in IoT Hub and has an azureiotsecurity twin module. |  Convalidare la configurazione dell'autenticazione: assicurarsi che il nome host sia corretto, che il dispositivo esista nell'hub IoT e disponga di un modulo gemello azureiotsecurity.Validate authentication configuration - make sure the hostname is correct, the device exists in IoT Hub and has an azureiotsecurity twin module. |
| Authentication | Configurazione mancante | Manca una configurazione nel file *Authentication.config.* Il messaggio di errore deve indicare quale chiave è mancante. | Aggiungere la chiave mancante al file *LocalConfiguration.json.*| Aggiungere la chiave mancante al file *Authentication.config,* vedere il riferimento di [c-localconfig](azure-iot-security-local-configuration-csharp.md) per i dettagli. |
| Authentication | Cant Parse Configuration | Non è possibile analizzare un valore di configurazione. Il messaggio di errore deve indicare quale chiave non può essere analizzata. Un valore di configurazione non può essere analizzato perché il valore non è del tipo previsto o il valore non è compreso nell'intervallo. |Correggere il valore della chiave nel file **LocalConfiguration.json.** |Correggere il valore della chiave nel file **Authentication.config** in modo che corrisponda allo schema, vedere [cs-localconfig-reference](azure-iot-security-local-configuration-c.md) per i dettagli.|
|

## <a name="restart-the-agent"></a>Riavviare l'agente

1. Dopo aver individuato e risolto un errore del Security Agent, riavviare l'agente eseguendo il comando seguente:

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. Se necessario, ripetere i processi precedenti per forzare l'arresto dell'agente e recuperare gli errori se l'agente continua a non riuscire il processo di avvio.

## <a name="next-steps"></a>Passaggi successivi

- Leggi la [panoramica](overview.md) del Centro sicurezza di Azure per il servizio IoT
- Altre informazioni sul Centro sicurezza di Azure per l'architettura IoTLearn more about Azure Security Center for IoT [Architecture](architecture.md)
- Abilitare il Centro sicurezza di Azure per il servizio IoTEnable the Azure Security Center for IoT [service](quickstart-onboard-iot-hub.md)
- Leggere le [domande frequenti](resources-frequently-asked-questions.md) sul Centro sicurezza di Azure per il servizio IoTRead the Azure Security Center for IoT service FAQ
- Informazioni su come accedere a [dati non elaborati sulla sicurezza](how-to-security-data-access.md)
- Informazioni sulle [raccomandazioni](concept-recommendations.md)
- Comprendere gli avvisi di sicurezzaUnderstand security [alerts](concept-security-alerts.md)
