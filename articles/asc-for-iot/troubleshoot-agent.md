---
title: Guida alla risoluzione dei problemi del Centro sicurezza di Azure per l'agente di sicurezza Linux Microsoft Docs
description: Risolvere i problemi di utilizzo del Centro sicurezza di Azure per gli agenti di sicurezza per Linux.
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
ms.openlocfilehash: 7f3bd4be3ef927f73643146a457bc551ef86a450
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600566"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Guida alla risoluzione dei problemi dell'agente sicurezza (Linux)

Questo articolo illustra come risolvere i potenziali problemi nel processo di avvio dell'agente di sicurezza.

Il Centro sicurezza di Azure per l'agente Internet viene avviato automaticamente dopo l'installazione. Il processo di avvio dell'agente include la lettura della configurazione locale, la connessione all'hub di Azure e il recupero della configurazione del dispositivo gemello remoto. Un errore in uno di questi passaggi potrebbe causare un errore dell'agente di sicurezza.

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
    **C#agente**
    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```
2. Se il comando restituisce una riga vuota, l'agente protezione non è stato in grado di avviarsi correttamente.    

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
2. Il comando Get Security Agent error recupera tutti i log creati dal centro sicurezza di Azure per l'agente. Usare la tabella seguente per comprendere gli errori ed eseguire i passaggi corretti per la correzione. 

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
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```
| Codice di errore | Codice secondario errore | Dettagli errore | Correggi C | CorreggereC# |
|:-----------|:---------------|:--------|:------------|:------------|
| Configurazione locale | Configurazione mancante | Una configurazione non è presente nel file di configurazione locale. Il messaggio di errore dovrebbe indicare la chiave mancante. | Aggiungere la chiave mancante al file/var/LocalConfiguration.json. per informazioni dettagliate, vedere [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) .| Aggiungere la chiave mancante al file General. config. per informazioni dettagliate, vedere [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) . |
| Configurazione locale | Configurazione dell'analisi cant | Non è possibile analizzare un valore di configurazione. Il messaggio di errore deve indicare la chiave che non può essere analizzata. Non è possibile analizzare un valore di configurazione perché il valore non è nel tipo previsto oppure il valore non è compreso nell'intervallo. | Correggere il valore della chiave nel file/var/LocalConfiguration.json in modo che corrisponda allo schema LocalConfiguration. per informazioni dettagliate, vedere [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) . |  Correggere il valore della chiave nel file General. config in modo che corrisponda allo schema. per informazioni dettagliate, vedere [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) .|
| Configurazione locale | Formato file | Non è stato possibile analizzare il file di configurazione. | Il file di configurazione è danneggiato, scaricare l'agente e reinstallare. | |
| Configurazione remota | Timeout | L'agente non è riuscito a recuperare il modulo azureiotsecurity gemello entro il periodo di timeout. | Verificare che la configurazione dell'autenticazione sia corretta e riprovare. | L'agente non è riuscito a recuperare il modulo gemello azureiotsecurity nel periodo di timeout. | Verificare che la configurazione dell'autenticazione sia corretta e riprovare. |
| Authentication | Il file non esiste | Il file nel percorso specificato non esiste. | Verificare che il file esista nel percorso specificato o passare al file **LocalConfiguration. JSON** e modificare la configurazione FilePath. | Verificare che il file esista nel percorso specificato o andare al file **Authentication. config** e modificare la configurazione FilePath.|
| Authentication | Autorizzazione file | L'agente non dispone di autorizzazioni sufficienti per aprire il file. | Concedere all'utente **asciotagent** le autorizzazioni di lettura per il file nel percorso specificato. | Verificare che il file sia accessibile. |
| Authentication | Formato file | Il formato del file specificato non è corretto. | Verificare che il formato del file sia corretto. I tipi di file supportati sono PFX e PEM. | Verificare che il file sia un file di certificato valido. |
| Authentication | Non autorizzato | L'agente non è stato in grado di eseguire l'autenticazione nell'hub Internet con le credenziali specificate. | Convalidare la configurazione di autenticazione nel file LocalConfiguration, passare attraverso la configurazione di autenticazione e verificare che tutti i dettagli siano corretti, verificare che il segreto nel file corrisponda all'identità autenticata. | Convalidare la configurazione dell'autenticazione in Authentication. config, passare attraverso la configurazione di autenticazione e verificare che tutti i dettagli siano corretti, quindi verificare che il segreto nel file corrisponda all'identità autenticata.
| Authentication | Non trovato | Il dispositivo o il modulo è stato trovato. | Convalidare la configurazione dell'autenticazione: assicurarsi che il nome host sia corretto, che il dispositivo esista nell'hub Internet e che disponga di un modulo azureiotsecurity Twin. |  Convalidare la configurazione dell'autenticazione: assicurarsi che il nome host sia corretto, che il dispositivo esista nell'hub Internet e che disponga di un modulo azureiotsecurity Twin. |
| Authentication | Configurazione mancante | Una configurazione non è presente nel file *Authentication. config* . Il messaggio di errore dovrebbe indicare la chiave mancante. | Aggiungere la chiave mancante al file *LocalConfiguration. JSON* .| Aggiungere la chiave mancante al file *Authentication. config.* per informazioni dettagliate, vedere [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) . |
| Authentication | Configurazione dell'analisi cant | Non è possibile analizzare un valore di configurazione. Il messaggio di errore deve indicare la chiave che non può essere analizzata. Non è possibile analizzare un valore di configurazione perché il valore non è del tipo previsto oppure il valore non è compreso nell'intervallo. |Correggere il valore della chiave nel file **LocalConfiguration. JSON** . |Correggere il valore della chiave nel file **Authentication. config** in modo che corrisponda allo schema. per informazioni dettagliate, vedere [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) .|
|

## <a name="restart-the-agent"></a>Riavviare l'agente
1. Dopo l'individuazione e la correzione di un errore dell'agente di sicurezza, riavviare l'agente eseguendo il comando seguente:

    ```bash
    systemctl restart ASCIoTAgent.service
    ```
2. Se necessario, ripetere i processi precedenti per forzare l'arresto dell'agente e recuperare gli errori se l'agente continua a non riuscire a eseguire il processo di avvio. 

## <a name="next-steps"></a>Passaggi successivi
- Leggi il Centro sicurezza di Azure per [informazioni generali](overview.md) sul servizio Internet
- Scopri di più sul centro sicurezza di Azure per l' [architettura dell'it](architecture.md)
- Abilitare il Centro sicurezza di Azure per il [servizio](quickstart-onboard-iot-hub.md) Internet delle cose
- Leggi le [domande frequenti](resources-frequently-asked-questions.md) sul servizio del Centro sicurezza di Azure per le cose
- Informazioni su come accedere a [dati non elaborati sulla sicurezza](how-to-security-data-access.md)
- Informazioni sulle [raccomandazioni](concept-recommendations.md)
- Informazioni sugli [avvisi](concept-security-alerts.md) di sicurezza
