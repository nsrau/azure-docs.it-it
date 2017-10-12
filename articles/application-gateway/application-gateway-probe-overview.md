---
title: "Panoramica del monitoraggio dell'integrità per il gateway applicazione di Azure | Documentazione Microsoft"
description: "Informazioni sulle funzionalità di monitoraggio nel gateway applicazione di Azure"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7eeba328-bb2d-4d3e-bdac-7552e7900b7f
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2016
ms.author: davidmu
ms.openlocfilehash: 83a0b1be1aba48146aa1aaedb36ad9d9d23f17d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="application-gateway-health-monitoring-overview"></a>Panoramica del monitoraggio dell'integrità del gateway applicazione

Per impostazione predefinita, il gateway applicazione di Azure monitora l'integrità di tutte le risorse nel pool back-end e rimuove automaticamente dal pool le risorse considerate non integre. Il gateway applicazione continua a monitorare le istanze non integre e le riaggiunge al pool back-end integro, dopo che sono diventate disponibili e rispondono ai probe di integrità. Il gateway applicazione invia i probe di integrità con la stessa porta definita nelle impostazioni HTTP del back-end. Questa configurazione garantisce che il probe testi la stessa porta che verrebbe usata dai clienti per connettersi al back-end.

![Esempio di probe del gateway applicazione][1]

Oltre al monitoraggio del probe di integrità predefinito, è anche possibile personalizzare il probe di integrità in base ai requisiti dell'applicazione. In questo articolo vengono illustrati i probe di integrità predefiniti e personalizzati.

> [!NOTE]
> Se nella subnet del gateway applicazione è presente un gruppo di sicurezza di rete, gli intervalli di porte 65503-65534 devono essere aperti nella subnet del gateway applicazioni per il traffico in ingresso. Tali porte sono necessarie per il corretto funzionamento dell'API di integrità back-end.

## <a name="default-health-probe"></a>Probe di integrità predefinito

Un gateway applicazione configura automaticamente un probe di integrità predefinito quando non si configura nessuna configurazione di probe personalizzato. Il comportamento del monitoraggio prevede l'invio di una richiesta HTTP agli indirizzi IP configurati per il pool back-end. Per i probe predefiniti, se le impostazioni HTTP del back-end vengono configurate per HTTPS, il probe usa HTTPS anche per testare l'integrità dei back-end.

Ad esempio, si configura il gateway applicazione per usare i server back-end A, B e C per ricevere il traffico di rete HTTP sulla porta 80. Il monitoraggio dell'integrità predefinito testa i tre server ogni 30 secondi per ottenere una risposta HTTP integra. Una risposta HTTP integra ha un [codice di stato](https://msdn.microsoft.com/library/aa287675.aspx) compreso tra 200 e 399.

Se il controllo probe predefinito non riesce per il server A, il gateway applicazione lo rimuove dal pool back-end e il flusso del traffico di rete a questo server viene arrestato. Il probe predefinito continua tuttavia a controllare il server A ogni 30 secondi. Quando il server A risponde correttamente a una richiesta di un probe di integrità predefinito, viene aggiunto di nuovo come integro al pool back-end e il flusso del traffico verso il server riprenderà.

### <a name="default-health-probe-settings"></a>Impostazioni del probe di integrità predefinito

| Proprietà probe | Valore | Descrizione |
| --- | --- | --- |
| URL probe |http://127.0.0.1:\<porta\>/ |Percorso URL |
| Interval |30 |Intervallo di probe in secondi |
| Timeout |30 |Timeout di probe in secondi |
| Soglia non integra |3 |Numero di tentativi di probe. Il server back-end viene contrassegnato come inattivo dopo che il numero di errori di probe consecutivi ha raggiunto una soglia non integra. |

> [!NOTE]
> La porta è la stessa delle impostazioni HTTP del back-end.

Il probe predefinito esamina solo http://127.0.0.1:\<porta\> per determinare lo stato di integrità. Se si deve configurare il probe di integrità per passare a un URL personalizzato o modificare altre impostazioni, è necessario usare probe personalizzati come descritto nei passaggi seguenti.

## <a name="custom-health-probe"></a>Probe di integrità personalizzato

I probe personalizzati consentono un controllo più granulare sul monitoraggio dell'integrità. Quando si usano i probe personalizzati, è possibile configurare l'intervallo di probe, l'URL e il percorso da testare e il numero di risposte non riuscite da accettare prima di contrassegnare l'istanza del pool back-end come non integra.

### <a name="custom-health-probe-settings"></a>Impostazioni del probe di integrità personalizzato

La tabella seguente fornisce le definizioni delle proprietà di un probe di integrità personalizzato.

| Proprietà probe | Descrizione |
| --- | --- |
| Nome |Nome del probe. Questo nome viene usato per fare riferimento al probe nelle impostazioni HTTP back-end |
| Protocol |Protocollo usato per inviare il probe. Il probe usa il protocollo definito nelle impostazioni HTTP del back-end. |
| Host |Nome host per inviare il probe. Applicabile solo quando vengono configurati più siti nel gateway applicazione. In caso contrario, usare "127.0.0.1". Questo valore è diverso dal nome host della VM. |
| Path |Percorso relativo del probe. Il percorso valido inizia da "/". |
| Interval |Intervallo di probe in secondi. Questo valore è l'intervallo di tempo tra due probe consecutivi. |
| Timeout |Timeout del probe in secondi. Se non viene ricevuta una risposta valida entro questo periodo di timeout, il probe viene contrassegnato come non riuscito.  |
| Soglia non integra |Numero di tentativi di probe. Il server back-end viene contrassegnato come inattivo dopo che il numero di errori di probe consecutivi ha raggiunto una soglia non integra. |

> [!IMPORTANT]
> Se il gateway applicazione è configurato per un singolo sito, per impostazione predefinita il nome dell'host deve essere specificato come "127.0.0.1", se non diversamente configurato nel probe personalizzato.
> Come riferimento, un probe personalizzato viene inviato a \<protocollo\>://\<host\>:\<porta\>\<percorso\>. La porta usata sarà la stessa definita nelle impostazioni HTTP del back-end.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver acquisito familiarità con il monitoraggio dell'integrità del gateway applicazione, è possibile configurare un [probe di integrità personalizzato](application-gateway-create-probe-portal.md) nel portale di Azure oppure un [probe di integrità personalizzato](application-gateway-create-probe-ps.md) usando PowerShell e il modello di distribuzione Azure Resource Manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
