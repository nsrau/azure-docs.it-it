---
title: "Pianificazione della capacità per i ruoli server di servizio App di Azure nello Stack di Azure | Documenti Microsoft"
description: "Pianificazione della capacità per i ruoli server di servizio App di Azure nello Stack di Azure"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 8926955d5e0260b5971e07b6988bb21df9980847
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Pianificazione della capacità per i ruoli server di servizio App di Azure nello Stack di Azure
*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Per eseguire il provisioning di una distribuzione pronto di produzione del servizio App di Azure nello Stack di Azure, è necessario pianificare la capacità di cui si prevede che il sistema per il supporto.  Sezione vengono fornite indicazioni per il numero minimo di istanze e calcolo SKU è necessario utilizzare per la distribuzione di produzione.

È possibile pianificare la strategia di capacità di servizio App utilizzano queste linee guida. Le versioni future dello Stack di Azure verranno fornite opzioni di disponibilità elevata per il servizio App.

| Ruolo del server di servizio App | Minimo numero consigliato di istanze | Calcolo consigliato SKU|
| --- | --- | --- |
| Controller | 2 | A1 |
| Front-end | 2 | A1 |
| Gestione | 2 | A3 |
| Editore | 2 | A1 |
| Web worker - condiviso | 2 | A1 |
| Web worker - dedicato | 2 per ogni livello | A1 |

## <a name="controller-role"></a>Ruolo controller

**Valore minimo consigliato**: due istanze di A1 standard

Il Controller del servizio App di Azure sperimentato in genere un basso utilizzo della CPU, memoria e risorse di rete. Tuttavia, per la disponibilità elevata, è necessario disporre di due controller. Due controller vengono anche il numero massimo di controller consentiti. È possibile creare direttamente il secondo Controller di siti Web dal programma di installazione durante la distribuzione.

## <a name="front-end-role"></a>Ruolo Front-End

**Valore minimo consigliato**: due istanze di A1 standard

A seconda della disponibilità di Web Worker, Front-End instrada le richieste ai Web worker. Per la disponibilità elevata, è necessario più di un Front-End che è possibile disporre di più di due. Per pianificare la capacità, prendere in considerazione che ciascun core è possibile gestire circa 100 richieste al secondo.

## <a name="management-role"></a>Ruolo di gestione

**Valore minimo consigliato**: due istanze di A3

Il ruolo di gestione di Azure App Service è responsabile della gestione risorse di Azure di servizio App e gli endpoint dell'API, estensioni portale (amministrazione, tenant, il portale di funzioni) e il servizio dati. Il ruolo del Server di gestione richiede in genere solo su 4 GB di RAM in un ambiente di produzione. Tuttavia, è possibile sperimentare livelli di CPU superiori quando vengono eseguite molte attività di gestione (ad esempio la creazione di siti web). Per la disponibilità elevata, è necessario più di un server assegnati a questo ruolo, e almeno due core per server.

## <a name="publisher-role"></a>Ruolo server di pubblicazione

**Valore minimo consigliato**: due istanze di A1

Se molti utenti contemporaneamente, il ruolo server di pubblicazione potrebbe verificarsi un utilizzo elevato della CPU. Per la disponibilità elevata, rendere disponibile più di un ruolo di server di pubblicazione.  Il server di pubblicazione gestisce solo il traffico FTP o FTPS.

## <a name="web-worker-role"></a>Ruolo Web worker

**Valore minimo consigliato**: due istanze di A1

Per la disponibilità elevata, è necessario disporre almeno quattro ruoli Web Worker, due per la modalità del sito web condiviso e due per ogni livello di ruolo di lavoro dedicato si intende offrire. Condiviso e la modalità di calcolo dedicato offrono diversi livelli di servizio ai tenant. Se si dispone di molti clienti, potrebbero essere necessari più Web worker:
 - utilizzo di piani di lavoro in modalità di calcolo dedicato (che utilizza molte risorse)
 - in esecuzione in modalità di calcolo condivisa.

Dopo che un utente ha creato un piano di servizio App per una modalità di calcolo dedicato SKU, il numero di lavori Web specificati nel piano di servizio App non saranno disponibile per gli utenti.

Per fornire funzioni di Azure per gli utenti del modello di piano il consumo, è necessario distribuire condiviso Web worker.

Quando si scelgono di utilizzare il numero di ruoli Web Worker condivisi, è possibile esaminare queste considerazioni:

- **Memoria**: la memoria è la risorsa più critica per un ruolo Web Worker. Memoria insufficiente influisce sulle prestazioni del sito web durante lo scambio e memoria virtuale dal disco. Ogni server richiede circa 1,2 GB di RAM per il sistema operativo. RAM oltre questa soglia può essere utilizzata per eseguire i siti web.
- **Percentuale di siti web attivi**: sono in genere attivo circa il 5% di applicazioni in un servizio App di Azure sulla distribuzione di Azure Stack. Tuttavia, la percentuale di applicazioni che sono attive in qualsiasi momento può essere superiore o inferiore. Con una frequenza di applicazione attivi pari al 5%, il numero massimo di applicazioni da inserire in un servizio App di Azure sulla distribuzione di Azure Stack deve essere inferiore a:
    - 20 volte il numero di siti web attivi (5 x 20 = 100).
- **Footprint di memoria medio**: il footprint di memoria medio per le applicazioni osservati negli ambienti di produzione è di circa 70 MB. Pertanto, la memoria allocata in tutti i computer con ruolo Web Worker o VM può essere calcolata come segue:

    *Numero di applicazioni di provisioning eseguito * 70 MB * 5%: (numero di ruoli Web Worker * 1044 MB)*

   Ad esempio, se sono presenti 5.000 applicazioni nell'ambiente in cui è in esecuzione 10 ruoli Web Worker, ogni ruolo Web Worker VM deve avere 7060 MB di RAM:

   0,05 * 5.000 * 70-(10 * 1044) = 7060 (= circa 7 GB)

   Per informazioni sull'aggiunta di più istanze di lavoro, vedere [aggiunta di più ruoli di lavoro](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Ruolo file server

Per il ruolo File Server, è possibile utilizzare un file server autonomo per lo sviluppo e test, ad esempio durante la distribuzione del servizio App di Azure nel Kit di sviluppo dello Stack di Azure è possibile utilizzare questo modello - https://aka.ms/appsvconmasdkfstemplate. Ai fini della produzione, è necessario utilizzare un File Server Windows preconfigurato o un server di file non Windows preconfigurato.

Negli ambienti di produzione il ruolo File Server vengono sperimentati i/o su disco elevati. Poiché esso vengono ospitati tutti i file di contenuto e dell'applicazioni per siti web dell'utente, è necessario preconfigurare uno dei seguenti valori per questo ruolo:
- un File Server Windows
- Cluster di file Server
- un file server non Windows
- cluster di file server
- Dispositivo NAS (Network Attached Storage) per ulteriori informazioni, vedere [il provisioning di un file server](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Passaggi successivi

[Prima di iniziare con il servizio App nello Stack di Azure](azure-stack-app-service-before-you-get-started.md)
