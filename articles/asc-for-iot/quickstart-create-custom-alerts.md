---
title: 'Guida introduttiva: Creare avvisi personalizzati per il Centro sicurezza di Azure per IoT'
description: In questo avvio rapido sono disponibili informazioni per creare e assegnare avvisi del dispositivo personalizzati per il Centro sicurezza di Azure per IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: eca5d69efb04cf8210b0b2aa502bcee5cd4f5264
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904130"
---
# <a name="quickstart-create-custom-alerts"></a>Guida introduttiva: Creare avvisi personalizzati


L'uso di gruppi di sicurezza e avvisi personalizzati sfrutta al meglio le informazioni di sicurezza end-to-end e la conoscenza categoriale dei dispositivi per garantire una maggiore sicurezza nell'intera soluzione IoT. 

## <a name="why-use-custom-alerts"></a>Perché usare gli avvisi personalizzati? 

Solo il proprietario conosce bene i propri dispositivi.

Per i clienti che conoscono perfettamente il comportamento previsto dei dispositivi, il Centro di sicurezza di Azure per IoT consente di tradurre questa conoscenza in criteri di comportamento dei dispositivi, avvisando in caso di eventuali deviazioni dalla norma.

## <a name="security-groups"></a>Gruppi di sicurezza

I gruppi di sicurezza consentono di definire gruppi logici di dispositivi e di gestirne lo stato della sicurezza in modo centralizzato.

Questi gruppi possono rappresentare i dispositivi con hardware specifico, i dispositivi distribuiti in un determinato percorso o qualsiasi altro gruppo adatto a esigenze specifiche.

I gruppi di sicurezza sono definiti da una proprietà tag dei dispositivi gemelli denominata **SecurityGroup**. Per impostazione predefinita, ogni soluzione IoT sull'hub IoT ha un gruppo di sicurezza denominato **default**. Per cambiare il gruppo di sicurezza di un dispositivo, cambiare il valore della proprietà **SecurityGroup**.
 
Ad esempio:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  }, 
```

Usare i gruppi di sicurezza per raggruppare i dispositivi in categorie logiche. Dopo aver creato i gruppi, assegnarli agli avvisi personalizzati a scelta, per ottenere la soluzione di sicurezza per IoT end-to-end più efficace. 

## <a name="customize-an-alert"></a>Personalizzare un avviso

1. Aprire l'hub IoT. 
2. Fare clic su **Avvisi personalizzati** nella sezione **Sicurezza**. 
3. Scegliere un gruppo di sicurezza a cui si vuole applicare la personalizzazione. 
4. Fare clic su **Aggiungi un avviso personalizzato**.
5. Selezionare un avviso personalizzato nell'elenco a discesa. 
6. Modificare le proprietà necessarie e fare clic su **OK**.
7. Assicurarsi di fare clic su **SALVA**. Se non si salva il nuovo avviso, l'avviso verrà eliminato la volta successiva che si chiude l'hub IoT.

 
## <a name="alerts-available-for-customization"></a>Avvisi disponibili per la personalizzazione

La tabella seguente fornisce un riepilogo degli avvisi disponibili per la personalizzazione.


| Gravità | NOME | origine dati | DESCRIZIONE | Correzione consigliata|
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
| Basso      | Avviso personalizzato: il numero di connessioni attive non rientra nell'intervallo consentito  | Agente       | Il numero connessioni attive in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito.|  Esaminare i log del dispositivo. Scoprire l'origine della connessione e determinare se è dannosa o meno. Se è dannosa, rimuovere il possibile malware e identificare l'origine. Se non è dannosa, aggiungere l'origine all'elenco di connessioni consentite.  |
| Basso      | Avviso personalizzato: è stata creata una connessione in uscita a un indirizzo IP non consentito                             | Agente       | È stata creata una connessione in uscita a un IP che non rientra nell'elenco di indirizzi IP consentiti. |Esaminare i log del dispositivo. Scoprire l'origine della connessione e determinare se è dannosa o meno. Se è dannosa, rimuovere il possibile malware e identificare l'origine. Se non è dannosa, aggiungere l'origine all'elenco di indirizzi IP consentiti.                        |
| Basso      | Avviso personalizzato: il numero di accessi locali non riusciti non rientra nell'intervallo consentito                               | Agente       | La quantità di accessi locali non riusciti in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito. |   |
| Basso      | Avviso personalizzato: accesso di un utente non incluso nell'elenco di utenti consentiti | Agente       | Un utente locale non incluso nell'elenco di utenti consentiti ha effettuato l'accesso al dispositivo.|  Se si salvano dati non elaborati, passare all'account di Log Analytics e usare i dati per esaminare il dispositivo, identificare l'origine e quindi correggere l'elenco di elementi consentiti/bloccati per tali impostazioni. Se attualmente non si salvano dati non elaborati, passare al dispositivo e correggere l'elenco di elementi consentiti/bloccati per tali impostazioni.|
| Basso      | Avviso personalizzato: è stato eseguito un processo non consentito | Agente       | Nel dispositivo è stato eseguito un processo non consentito. |Se si salvano dati non elaborati, passare all'account di Log Analytics e usare i dati per esaminare il dispositivo, identificare l'origine e quindi correggere l'elenco di elementi consentiti/bloccati per tali impostazioni. Se attualmente non si salvano dati non elaborati, passare al dispositivo e correggere l'elenco di elementi consentiti/bloccati per tali impostazioni.  |
|


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come distribuire un agente di sicurezza, passare all'articolo successivo...

> [!div class="nextstepaction"]
> [Distribuire un agente di sicurezza](how-to-deploy-agent.md)
