---
title: Risolvere i problemi di avvio dell'agente di sicurezza (Linux)
description: Risolvere i problemi relativi all'uso di Azure Defender per gli agenti di sicurezza per Linux.
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
ms.openlocfilehash: e9904e9157a560e2a4853a1a9cd37977defe73ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937387"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Guida alla risoluzione dei problemi dell'agente di sicurezza (Linux)

Questo articolo illustra come risolvere i potenziali problemi nel processo di avvio dell'agente di sicurezza.

Azure Defender per l'agente di gestione delle cose inizia immediatamente dopo l'installazione. Il processo di avvio dell'agente include la lettura della configurazione locale, la connessione all'hub di Azure e il recupero della configurazione del dispositivo gemello remoto. Un errore in uno di questi passaggi potrebbe causare un errore dell'agente di sicurezza.

In questa guida alla risoluzione dei problemi si apprenderà come:

> [!div class="checklist"]
> * Verificare se l'agente di sicurezza è in esecuzione
> * Ottenere gli errori dell'agente di sicurezza
> * Comprendere e correggere gli errori dell'agente di sicurezza

## <a name="validate-if-the-security-agent-is-running"></a>Verificare se l'agente di sicurezza è in esecuzione

1. Per convalidare l'agente di sicurezza in esecuzione, attendere alcuni minuti dopo l'installazione dell'agente ed eseguire il comando seguente.
     <br>

    **Agente C**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **Agente C#**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. Se il comando restituisce una riga vuota, l'agente protezione non è stato in grado di avviarsi correttamente.

## <a name="force-stop-the-security-agent"></a>Forzare l'arresto dell'agente di sicurezza

Nei casi in cui non è possibile avviare l'agente di sicurezza, arrestare l'agente con il comando seguente, quindi continuare con la tabella degli errori riportata di seguito:

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>Ottenere gli errori dell'agente di sicurezza

1. Recuperare gli errori dell'agente di sicurezza eseguendo il comando seguente:

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. Il comando Get Security Agent error recupera tutti i log creati dal Defender per l'agente. Usare la tabella seguente per comprendere gli errori ed eseguire i passaggi corretti per la correzione.

> [!Note]
> I log degli errori vengono visualizzati in ordine cronologico. Assicurarsi di prendere nota del timestamp di ogni errore per facilitare la correzione.

## <a name="restart-the-agent"></a>Riavviare l'agente

1. Dopo l'individuazione e la correzione di un errore dell'agente di sicurezza, provare a riavviare l'agente eseguendo il comando seguente.

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. Ripetere il processo precedente per recuperare l'arresto e recuperare gli errori se l'agente continua a non riuscire a eseguire il processo di avvio.

## <a name="understand-security-agent-errors"></a>Informazioni sugli errori dell'agente di sicurezza

La maggior parte degli errori dell'agente di sicurezza viene visualizzata nel formato seguente:

```
Defender for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| Codice di errore | Codice secondario errore | Dettagli errore | Correggi C | Correggi C # |
|:-----------|:---------------|:--------|:------------|:------------|
| Configurazione locale | Configurazione mancante | Una configurazione non è presente nel file di configurazione locale. Il messaggio di errore dovrebbe indicare la chiave mancante. | Aggiungere la chiave mancante al LocalConfiguration.js/var/su file. per informazioni dettagliate, vedere [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) .| Aggiungere la chiave mancante al file di General.config. per informazioni dettagliate, vedere [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) . |
| Configurazione locale | Configurazione dell'analisi cant | Non è possibile analizzare un valore di configurazione. Il messaggio di errore deve indicare la chiave che non può essere analizzata. Non è possibile analizzare un valore di configurazione perché il valore non è nel tipo previsto oppure il valore non è compreso nell'intervallo. | Correggere il valore della chiave in/var/LocalConfiguration.jsnel file in modo che corrisponda allo schema LocalConfiguration, vedere [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) per i dettagli. |  Correggere il valore della chiave nel file General.config in modo che corrisponda allo schema. per informazioni dettagliate, vedere [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) .|
| Configurazione locale | Formato file | Non è stato possibile analizzare il file di configurazione. | Il file di configurazione è danneggiato, scaricare l'agente e reinstallare. | |
| Configurazione remota | Timeout | L'agente non è riuscito a recuperare il modulo azureiotsecurity gemello entro il periodo di timeout. | Verificare che la configurazione dell'autenticazione sia corretta e riprovare. | L'agente non è riuscito a recuperare il modulo gemello azureiotsecurity nel periodo di timeout. | Verificare che la configurazione dell'autenticazione sia corretta e riprovare. |
| Authentication | Il file non esiste | Il file nel percorso specificato non esiste. | Verificare che il file esista nel percorso specificato o passare al **LocalConfiguration.jsnel** file e modificare la configurazione **filePath** . | Verificare che il file esista nel percorso specificato o andare al file **Authentication.config** e modificare la configurazione del **filePath** .|
| Authentication | Autorizzazione file | L'agente non dispone di autorizzazioni sufficienti per aprire il file. | Concedere all'utente **asciotagent** le autorizzazioni di lettura per il file nel percorso specificato. | Verificare che il file sia accessibile. |
| Authentication | Formato file | Il formato del file specificato non è corretto. | Verificare che il formato del file sia corretto. I tipi di file supportati sono PFX e PEM. | Verificare che il file sia un file di certificato valido. |
| Authentication | Non autorizzata | L'agente non è stato in grado di eseguire l'autenticazione nell'hub Internet con le credenziali specificate. | Convalidare la configurazione di autenticazione nel file LocalConfiguration, passare attraverso la configurazione di autenticazione e verificare che tutti i dettagli siano corretti, verificare che il segreto nel file corrisponda all'identità autenticata. | Convalidare la configurazione dell'autenticazione in Authentication.config, passare attraverso la configurazione di autenticazione e verificare che tutti i dettagli siano corretti, quindi verificare che il segreto nel file corrisponda all'identità autenticata.
| Authentication | Non trovato | Il dispositivo o il modulo è stato trovato. | Convalidare la configurazione dell'autenticazione: assicurarsi che il nome host sia corretto, che il dispositivo esista nell'hub Internet e che disponga di un modulo azureiotsecurity Twin. |  Convalidare la configurazione dell'autenticazione: assicurarsi che il nome host sia corretto, che il dispositivo esista nell'hub Internet e che disponga di un modulo azureiotsecurity Twin. |
| Authentication | Configurazione mancante | Una configurazione non è presente nel file di *Authentication.config* . Il messaggio di errore dovrebbe indicare la chiave mancante. | Aggiungere la chiave mancante al *LocalConfiguration.jssul* file.| Aggiungere la chiave mancante al file di *Authentication.config* . per informazioni dettagliate, vedere [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) . |
| Authentication | Configurazione dell'analisi cant | Non è possibile analizzare un valore di configurazione. Il messaggio di errore deve indicare la chiave che non può essere analizzata. Non è possibile analizzare un valore di configurazione perché il valore non è del tipo previsto oppure il valore non è compreso nell'intervallo. |Correggere il valore della chiave nella **LocalConfiguration.jssu** file. |Per informazioni dettagliate, è possibile correggere il valore della chiave nel file **Authentication.config** per trovare [la corrispondenza](azure-iot-security-local-configuration-c.md) con lo schema.|
|

## <a name="next-steps"></a>Passaggi successivi

- Leggi la [Panoramica](overview.md) del servizio Defender for Internet
- Scopri di più su Defender per l' [architettura dell'it](architecture.md)
- Abilita il Defender per il [servizio](quickstart-onboard-iot-hub.md) Internet
- Leggi le [domande frequenti](resources-frequently-asked-questions.md) sul servizio Defender for Internet
- Informazioni su come accedere a [dati non elaborati sulla sicurezza](how-to-security-data-access.md)
- Informazioni sulle [raccomandazioni](concept-recommendations.md)
- Informazioni sugli [avvisi](concept-security-alerts.md) di sicurezza
