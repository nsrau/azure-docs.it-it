---
title: Guida alla risoluzione dei problemi-Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce un elenco delle eccezioni di messaggistica di Hub eventi di Azure e le relative azioni consigliate.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: ab3cbdf938409f4eacffa10ae5cb20f8c36b5856
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124670"
---
# <a name="azure-event-hubs---troubleshooting-guide"></a>Hub eventi di Azure-Guida alla risoluzione dei problemi
Questo articolo fornisce suggerimenti e consigli per la risoluzione dei problemi che possono verificarsi durante l'uso di Azure EventHubs.

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemi di connettività, certificato o timeout
I passaggi seguenti possono essere utili per la risoluzione dei problemi di connettività/certificato/timeout per tutti i servizi in *. servicebus.windows.net. 

- Passare a o [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` . Consente di controllare se sono presenti problemi di filtro IP, rete virtuale o catena di certificati (più comuni quando si usa Java SDK).

    Esempio di messaggio riuscito:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Esempio di messaggio di errore di errore:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Eseguire il comando seguente per verificare se una porta è bloccata sul firewall. Le porte usate sono 443 (HTTPS), 5671 (AMQP) e 9093 (Kafka). A seconda della libreria usata, vengono usate anche altre porte. Ecco il comando di esempio che controlla se la porta 5671 è bloccata.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    In Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Quando si verificano problemi di connettività intermittenti, eseguire il comando seguente per verificare se sono presenti pacchetti eliminati. Questo comando tenterà di stabilire 25 connessioni TCP diverse ogni secondo con il servizio. Quindi, è possibile controllare il numero di riuscite/non riuscite e vedere anche latenza di connessione TCP. È possibile scaricare lo `psping` strumento da [qui](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    È possibile utilizzare comandi equivalenti se si utilizzano altri strumenti, ad esempio `tnc` , `ping` e così via. 
- Ottenere una traccia di rete se i passaggi precedenti non sono utili e analizzarli tramite strumenti come [Wireshark](https://www.wireshark.org/). Se necessario, contattare [supporto tecnico Microsoft](https://support.microsoft.com/) . 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problemi che possono verificarsi con gli aggiornamenti o i riavvii del servizio
Gli aggiornamenti del servizio back-end e i riavvii possono provocare le seguenti conseguenze per le applicazioni:

- Le richieste possono essere temporaneamente limitate.
- Potrebbe essere presente un calo nei messaggi/richieste in arrivo.
- Il file di log può contenere messaggi di errore.
- È possibile che le applicazioni siano disconnesse dal servizio per alcuni secondi.

Se il codice dell'applicazione usa l'SDK, i criteri di ripetizione dei tentativi sono già incorporati e attivi. L'applicazione si riconnetterà senza conseguenze significative per l'applicazione o il flusso di lavoro.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Creare un hub eventi](event-hubs-create.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
