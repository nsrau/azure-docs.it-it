---
title: Filtri connessioni IP dell'hub IoT di Azure | Microsoft Docs
description: "Come usare i filtri IP per bloccare le connessioni da indirizzi IP specifici all'hub IoT di Azure. È possibile bloccare le connessioni da singoli indirizzi IP o da intervalli di indirizzi IP."
services: iot-hub
documentationcenter: 
author: BeatriceOltean
manager: timlt
editor: 
ms.assetid: f833eac3-5b5f-46a7-a47b-f4f6fc927f3f
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: boltean
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 85f5f044faddd5180f0c19d3f2c235b20f6373d5
ms.contentlocale: it-it
ms.lasthandoff: 05/23/2017

---

# <a name="use-ip-filters"></a>Usare i filtri IP

La sicurezza è un aspetto importante di qualsiasi soluzione IoT basata su un hub IoT di Azure. Talvolta è necessario specificare in modo esplicito gli indirizzi IP da cui possono connettersi i dispositivi come parte della configurazione di sicurezza. La funzionalità _Filtro IP_ consente di configurare regole per rifiutare o accettare il traffico da specifici indirizzi IPv4.

## <a name="when-to-use"></a>Quando usare le autorizzazioni

Esistono due casi d'uso specifici in cui è utile bloccare gli endpoint dell'hub IoT per determinati indirizzi IP:

- L'hub IoT deve ricevere il traffico solo da un intervallo di indirizzi IP specificato e rifiutare tutto il resto. Si usa ad esempio l'hub IoT con [Azure ExpressRoute] per creare connessioni private tra un hub IoT e l'infrastruttura locale.
- È necessario rifiutare il traffico proveniente da indirizzi IP identificati come sospetti dall'amministratore dell'hub IoT.

## <a name="how-filter-rules-are-applied"></a>Come vengono applicate le regole di filtro

Le regole del filtro IP vengono applicate a livello del servizio dell'hub IoT. Le regole del filtro IP vengono quindi applicate a tutte le connessioni provenienti dai dispositivi e dalle app back-end con qualsiasi protocollo supportato.

Qualsiasi tentativo di connessione da un indirizzo IP corrispondente a una regola di rifiuto nell'hub IoT riceve un codice di stato 401 - Non autorizzato e la descrizione. Il messaggio di risposta non indica la regola IP.

## <a name="default-setting"></a>Impostazione predefinita

Per impostazione predefinita, la griglia **Filtro IP** nel portale di un hub IoT è vuota. Questa impostazione predefinita indica che l'hub accetta connessioni da qualsiasi indirizzo IP. Questa impostazione predefinita equivale a una regola che accetta l'intervallo di indirizzi IP 0.0.0.0/0.

![Impostazioni predefinite del filtro IP dell'hub IoT][img-ip-filter-default]

## <a name="add-or-edit-an-ip-filter-rule"></a>Aggiungere o modificare una regola del filtro IP

Quando si aggiunge una regola del filtro IP, vengono richiesti i valori seguenti:

- Un **nome della regola del filtro IP** che deve essere una stringa univoca, senza distinzione tra maiuscole e minuscole e alfanumerica con un massimo di 128 caratteri. Sono ammessi solo caratteri alfanumerici ASCII a 7 bit più `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.
- Selezionare il **rifiuto** o l'**accettazione** come **azione** per la regola del filtro IP.
- Specificare un singolo indirizzo IPv4 o un blocco di indirizzi IP in notazione CIDR. In notazione CIDR, ad esempio, 192.168.100.0/22 rappresenta gli indirizzi IPv4 1024 da 192.168.100.0 a 192.168.103.255.

![Aggiungere una regola di filtro IP a un hub IoT][img-ip-filter-add-rule]

Dopo aver salvato la regola viene visualizzato un avviso che informa che l'aggiornamento è in corso.

![Notifica sul salvataggio di una regola di filtro IP][img-ip-filter-save-new-rule]

L'opzione **Aggiungi** è disabilitata quando si raggiunge il numero massimo di dieci regole del filtro IP.

È possibile modificare una regola esistente facendo doppio clic sulla riga corrispondente.

> [!NOTE]
> Il rifiuto di indirizzi IP può impedire l'interazione di altri servizi di Azure (ad esempio Analisi di flusso di Azure, Macchine virtuali di Azure o Device Explorer nel portale) con l'hub IoT.

> [!WARNING]
> Se si usa Analisi di flusso di Azure (ASA) per leggere i messaggi da un hub IoT con i filtri IP abilitati, usare il nome e l'endpoint compatibile con l'hub eventi dell'hub IoT nella stringa di connessione ASA.

## <a name="delete-an-ip-filter-rule"></a>Eliminare una regola del filtro IP

Selezionare una o più regole del filtro IP nella griglia e fare clic su **Elimina**.

![Eliminare una regola del filtro IP dell'hub IoT][img-ip-filter-delete-rule]

## <a name="ip-filter-rule-evaluation"></a>Valutazione delle regole del filtro IP

Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

Se ad esempio si vogliono accettare gli indirizzi nell'intervallo 192.168.100.0/22 e rifiutare tutti gli altri, la prima regola nella griglia dovrà accettare l'intervallo di indirizzi 192.168.100.0/22. La regola successiva dovrà rifiutare tutti gli indirizzi usando l'intervallo 0.0.0.0/0.

È possibile modificare l'ordine delle regole del filtro IP nella griglia facendo clic sui tre punti verticali all'inizio di una riga e trascinando la selezione.

Per salvare il nuovo ordine delle regole del filtro IP, fare clic su **Salva**.

![Modificare l'ordine delle regole del filtro IP dell'hub IoT][img-ip-filter-rule-order]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

- [Monitoraggio delle operazioni][lnk-monitor]
- [Metriche di Hub IoT][lnk-metrics]

<!-- Images -->
[img-ip-filter-default]: ./media/iot-hub-ip-filtering/ip-filter-default.png
[img-ip-filter-add-rule]: ./media/iot-hub-ip-filtering/ip-filter-add-rule.png
[img-ip-filter-save-new-rule]: ./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png
[img-ip-filter-delete-rule]: ./media/iot-hub-ip-filtering/ip-filter-delete-rule.png
[img-ip-filter-rule-order]: ./media/iot-hub-ip-filtering/ip-filter-rule-order.png


<!-- Links -->

[IoT Hub developer guide]: iot-hub-devguide.md
[Azure ExpressRoute]:  https://azure.microsoft.com/en-us/documentation/articles/expressroute-faqs/#supported-services

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-metrics]: iot-hub-metrics.md
