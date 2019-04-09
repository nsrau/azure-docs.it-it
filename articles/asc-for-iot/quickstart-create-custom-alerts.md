---
title: Creare avvisi personalizzati per il Centro sicurezza di Azure per IoT - Anteprima | Microsoft Docs
description: Creare e assegnare avvisi del dispositivo personalizzati per il Centro sicurezza di Azure per IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: d793b105e6d73c98739cd05d6e19a218413d7813
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58861996"
---
# <a name="quickstart-create-custom-alerts"></a>Guida introduttiva: Creare avvisi personalizzati

> [!IMPORTANT]
> Centro sicurezza di Azure per IoT è attualmente in versione di anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L'uso di gruppi di sicurezza e avvisi personalizzati sfrutta al meglio le informazioni di sicurezza end-to-end e la conoscenza categorica del dispositivo per garantire una migliore sicurezza nell'intera soluzione IoT. 

## <a name="why-use-custom-alerts"></a>Perché usare gli avvisi personalizzati? 

Solo il proprietario conosce bene i propri dispositivi.

Per i clienti che comprendono appieno il comportamento del dispositivo previsto, il Centro di sicurezza di Azure per IoT consente di tradurre questa conoscenza in un criterio di comportamento di un dispositivo, avvisando in caso di eventuali deviazioni dal normale comportamento previsto.

## <a name="security-groups"></a>Gruppi di sicurezza

I gruppi di sicurezza consentono di definire gruppi logici di dispositivi e di gestirne lo stato della sicurezza in modo centralizzato.

Questi gruppi possono rappresentare i dispositivi con hardware specifico, i dispositivi distribuiti in un determinato percorso o qualsiasi altro gruppo adatto a esigenze specifiche.

I gruppi di sicurezza sono definiti da una proprietà tag del modulo gemello di sicurezza denominata **SecurityGroup**. Modificare il valore di questa proprietà per modificare il gruppo di sicurezza di un dispositivo.  

Per impostazione predefinita, ogni soluzione IoT sull'hub IoT ha un gruppo di sicurezza denominato **default**.

Usare i gruppi di sicurezza per raggruppare i dispositivi in categorie logiche. Dopo aver creato i gruppi, assegnarli agli avvisi personalizzati di propria scelta, per ottenere la soluzione end-to-end più efficace. 

## <a name="customize-an-alert"></a>Personalizzare un avviso

1. Aprire l'hub IoT. 
2. Selezionare **Sicurezza**, quindi scegliere **Avvisi personalizzati**. 
3. Scegliere i gruppi di sicurezza a cui si vuole applicare la personalizzazione. 
4. Fare clic su **Aggiungi un avviso personalizzato**
5. Immettere un nome per l'avviso (i nomi degli avvisi non possono essere modificati dopo la creazione). 
6. Selezionare un comportamento dell'avviso personalizzato nell'elenco a discesa. 
7. Modificare le proprietà necessarie e fare clic su **OK**.
8. Assicurarsi di fare clic su **SALVA**. Se non si salva il nuovo avviso, l'avviso verrà eliminato la volta successiva che si chiude l'hub IoT.

 
## <a name="alerts-available-for-customization"></a>Avvisi disponibili per la personalizzazione

La tabella seguente fornisce un riepilogo degli avvisi disponibili per la personalizzazione.

| Gravità | NOME                                                                                                    | origine dati | DESCRIZIONE                                                                                                                                     |
|----------|---------------------------------------------------------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Basso      | Avviso personalizzato: il numero di messaggi dal cloud al dispositivo nel protocollo AMQP non è compreso nell'intervallo consentito          | Hub IoT     | La quantità di messaggi dal cloud al dispositivo (protocollo AMQP) in un intervallo di tempo non è compreso nell'intervallo consentito configurato                                  |
| Basso      | Avviso personalizzato: il numero di messaggi rifiutati dal cloud al dispositivo nel protocollo AMQP non è compreso nell'intervallo consentito | Hub IoT     | La quantità di messaggi dal cloud al dispositivo (protocollo AMQP) che sono stati rifiutati dal dispositivo in un intervallo di tempo non è compreso nell'intervallo consentito configurato |
| Basso      | Avviso personalizzato: il numero di messaggi dal dispositivo al cloud nel protocollo AMQP non è compreso nell'intervallo consentito          | Hub IoT     | La quantità di messaggi dal dispositivo al cloud (protocollo AMQP) in un intervallo di tempo non è compreso nell'intervallo consentito configurato                                  |
| Basso      | Avviso personalizzato: il numero di chiamate del metodo diretto non è compreso nell'intervallo consentito                              | Hub IoT     | La quantità di chiamate del metodo diretto in un intervallo di tempo non è compreso nell'intervallo consentito configurato                                                     |
| Basso      | Avviso personalizzato: il numero di caricamenti di file non è compreso nell'intervallo consentito                                       | Hub IoT     | La quantità di caricamenti di file in un intervallo di tempo non è compreso nell'intervallo consentito configurato                                                              |
| Basso      | Avviso personalizzato: il numero di messaggi dal cloud al dispositivo nel protocollo HTTP non è compreso nell'intervallo consentito          | Hub IoT     | La quantità di messaggi dal cloud al dispositivo (protocollo HTTP) in un intervallo di tempo non è compreso nell'intervallo consentito configurato                                  |
| Basso      | Avviso personalizzato: il numero di messaggi rifiutati dal cloud al dispositivo nel protocollo HTTP non è compreso nell'intervallo consentito | Hub IoT     | La quantità di messaggi dal cloud al dispositivo (protocollo HTTP) che sono stati rifiutati dal dispositivo in un intervallo di tempo non è compreso nell'intervallo consentito configurato |
| Basso      | Avviso personalizzato: il numero di messaggi dal dispositivo al cloud nel protocollo HTTP non è compreso nell'intervallo consentito          | Hub IoT     | La quantità di messaggi dal dispositivo al cloud (protocollo HTTP) in un intervallo di tempo non è compreso nell'intervallo consentito configurato                                  |
| Basso      | Avviso personalizzato: il numero di messaggi dal cloud al dispositivo nel protocollo MQTT non è compreso nell'intervallo consentito          | Hub IoT     | La quantità di messaggi dal cloud al dispositivo (protocollo MQTT) in un intervallo di tempo non è compreso nell'intervallo consentito configurato                                  |
| Basso      | Avviso personalizzato: il numero di messaggi rifiutati dal cloud al dispositivo nel protocollo MQTT non è compreso nell'intervallo consentito | Hub IoT     | La quantità di messaggi dal cloud al dispositivo (protocollo MQTT) che sono stati rifiutati dal dispositivo in un intervallo di tempo non è compreso nell'intervallo consentito configurato |
| Basso      | Avviso personalizzato: il numero di messaggi dal dispositivo al cloud nel protocollo MQTT non è compreso nell'intervallo consentito          | Hub IoT     | La quantità di messaggi dal dispositivo al cloud (protocollo MQTT) in un intervallo di tempo non è compreso nell'intervallo consentito configurato                                  |
| Basso      | Avviso personalizzato: il numero di pulizie della coda dei comandi non è compreso nell'intervallo consentito                               | Hub IoT     | La quantità di pulizie della coda dei comandi in un intervallo di tempo non è compreso nell'intervallo consentito configurato                                                      |
| Basso      | Avviso personalizzato: il numero di aggiornamenti dei dispositivi gemelli non è compreso nell'intervallo consentito                                       | Hub IoT     | La quantità di aggiornamenti dei dispositivi gemelli in un intervallo di tempo non è compreso nell'intervallo consentito configurato                                                              |
| Basso      | Avviso personalizzato: il numero di operazioni non autorizzate non è compreso nell'intervallo consentito                            | Hub IoT     | La quantità di operazioni non autorizzate in un intervallo di tempo non è compreso nell'intervallo consentito configurato                                                   |
| Basso      | Avviso personalizzato: il numero di connessioni attive non è compreso nell'intervallo consentito                                        | Agente       | La quantità di connessioni attive in un intervallo di tempo non è compreso nell'intervallo consentito configurato                                                        |
| Basso      | Avviso personalizzato: è stata creata una connessione in uscita a un indirizzo IP non consentito                              | Agente       | È stata creata una connessione in uscita a un indirizzo IP non consentito                                                                                  |
| Basso      | Avviso personalizzato: il numero di accessi locali non riusciti non è compreso nell'intervallo consentito                                | Agente       | La quantità di accessi locali non riusciti in un intervallo di tempo non è compreso nell'intervallo consentito configurato                                                       |
| Basso      | Avviso personalizzato: accesso di un utente non consentito                                                      | Agente       | Un utente locale a cui non è consentito l'accesso al dispositivo                                                                                        |
| Basso      | Avviso personalizzato: esecuzione di un processo non consentito                                               | Agente       | È stato eseguito un processo non consentito sul dispositivo |          |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come distribuire un agente di sicurezza, passare all'articolo successivo...

> [!div class="nextstepaction"]
> [Distribuire un agente di sicurezza](how-to-deploy-agent.md)
