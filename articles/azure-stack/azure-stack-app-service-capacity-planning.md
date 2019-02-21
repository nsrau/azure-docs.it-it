---
title: Pianificazione della capacità per i ruoli server di servizio App di Azure in Azure Stack | Microsoft Docs
description: Pianificazione della capacità per i ruoli server di servizio App di Azure in Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 20b79b3c2581db94627746f52ed6837aa80b6be5
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447741"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Pianificazione della capacità per i ruoli server di servizio App di Azure in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Per configurare una distribuzione pronta di produzione del servizio App di Azure in Azure Stack, è necessario pianificare la capacità che previsto il supporto del sistema.  

Questo articolo offre materiale sussidiario per il numero minimo di istanze di calcolo e di calcolo SKU è consigliabile usare per qualsiasi distribuzione di produzione.

È possibile pianificare la strategia di capacità del servizio App usando le linee guida.

| Ruolo del server del servizio App | Minimo numero consigliato di istanze | Calcolo consigliato SKU|
| --- | --- | --- |
| Controller | 2 | A1 |
| Front-end | 2 | A1 |
| Gestione | 2 | A3 |
| Editore | 2 | A1 |
| Web worker - condiviso | 2 | A1 |
| Web worker - dedicato | 2 per ogni livello | A1 |

## <a name="controller-role"></a>Ruolo controller

**Quantità minima consigliata**: Due istanze di Standard A1

Il controller di servizio App di Azure sperimentato in genere un basso utilizzo della CPU, memoria e risorse di rete. Tuttavia, per la disponibilità elevata, è necessario disporre di due controller. Due controller sono anche il numero massimo di controller consentiti. È possibile creare direttamente dal programma di installazione il secondo controller di siti web durante la distribuzione.

## <a name="front-end-role"></a>Ruolo front-end

**Quantità minima consigliata**: Due istanze di Standard A1

Il front-end instrada le richieste ai web worker, a seconda della disponibilità di web worker. Per la disponibilità elevata, è necessario disporre di più front-end e si può avere più di due. Per scopi di pianificazione della capacità, prendere in considerazione che ciascun core è possibile gestire circa 100 richieste al secondo.

## <a name="management-role"></a>Ruolo di gestione

**Quantità minima consigliata**: Due istanze di A3 Standard

Il ruolo di gestione del servizio App di Azure è responsabile per l'App del servizio Azure Resource Manager e gli endpoint API, estensioni del portale (amministrazione, tenant, il portale di funzioni) e il servizio dati. Il ruolo di server di gestione richiede in genere solo su 4 GB di RAM in un ambiente di produzione. Livelli elevati di CPU, tuttavia, verifichi quando vengono eseguite molte attività di gestione (ad esempio la creazione del sito web). Per la disponibilità elevata, è necessario che più di un server assegnato a questo ruolo, e almeno due core per ogni server.

## <a name="publisher-role"></a>Ruolo server di pubblicazione

**Quantità minima consigliata**: Due istanze di Standard A1

Se molti utenti pubblicano contemporaneamente, il ruolo del server di pubblicazione potrebbe verificarsi un utilizzo intenso della CPU. Per la disponibilità elevata, assicurarsi che sia disponibile più di un ruolo del server di pubblicazione. Il server di pubblicazione gestisce solo il traffico FTP/FTPS.

## <a name="web-worker-role"></a>Ruolo Web worker

**Quantità minima consigliata**: Due istanze di Standard A1

Per la disponibilità elevata, è necessario disporre di almeno quattro ruoli web worker, due per la modalità sito web condiviso e due per ogni livello di ruolo di lavoro dedicato che si decida di offrire. Il condivisi e dedicati di calcolo le modalità offrono diversi livelli di servizio ai tenant. Potrebbero essere necessarie più web worker per molti dei clienti sono:

- Utilizzo di piani di lavoro in modalità di calcolo dedicati (che sono a elevato utilizzo di risorse).
- In esecuzione in modalità di calcolo condivisa.

Dopo che un utente ha creato un piano di servizio App per una modalità di calcolo dedicati SKU, il numero di lavori web specificati nel piano di servizio App non è più disponibile per gli utenti.

Per fornire funzioni di Azure per gli utenti nel modello di piano di consumo, è necessario distribuire i ruoli di lavoro web condiviso.

Quando si decide il numero di ruoli di lavoro web condiviso da usare, esaminare queste considerazioni:

- **Memoria**: La memoria è la risorsa più critica per un ruolo web worker. Memoria insufficiente influisce sulle prestazioni del sito web quando viene scambiata memoria virtuale dal disco. Ogni server richiede circa 1,2 GB di RAM per il sistema operativo. RAM superiore a questa soglia è utilizzabile per eseguire i siti web.
- **Percentuale di siti web attivi**: In genere, circa il 5% delle applicazioni nel servizio App di Azure nella distribuzione di Azure Stack sono attivo. Tuttavia, la percentuale di applicazioni che sono attivi in qualsiasi momento può essere superiore o inferiore. Con una frequenza dell'applicazione attiva del 5%, il numero massimo di applicazioni da inserire in un servizio App di Azure nella distribuzione di Azure Stack deve essere inferiore a 20 volte il numero di siti web attivi (5 x 20 = 100).
- **Footprint di memoria medio**: Il footprint di memoria medio per le applicazioni osservati negli ambienti di produzione è di circa 70 MB. Usando questa copertura, la memoria allocata in tutti i computer con ruolo web worker o VM può essere calcolata come segue:

   `Number of provisioned applications * 70 MB * 5% - (number of web worker roles * 1044 MB)`

   Ad esempio, se sono presenti 5.000 applicazioni nell'ambiente in cui è in esecuzione 10 ruoli web worker, ogni ruolo web worker della macchina virtuale deve avere 7060 MB di RAM:

   `5,000 * 70 * 0.05 – (10 * 1044) = 7060 (= about 7 GB)`

   Per informazioni sull'aggiunta di altre istanze del ruolo di lavoro, vedere [aggiunta di altri ruoli di lavoro](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Ruolo file server

Per il ruolo file server, è possibile usare un file server autonomo per lo sviluppo e il testing. ad esempio, quando si distribuisce il servizio App di Azure in Azure Stack Development Kit (ASDK) è possibile usare questo modello: https://aka.ms/appsvconmasdkfstemplate. Per scopi di produzione, è consigliabile usare file server Windows preconfigurato o un server di file non Windows preconfigurato.

Negli ambienti di produzione, il ruolo file server si verifica nel disco con utilizzo intensivo dei / o. Poiché esso vengono ospitati tutti i file di contenuto e dell'applicazioni per siti web dell'utente, è necessario preconfigurare una delle seguenti risorse per questo ruolo:

- File server di Windows
- Cluster di Windows file server
- Server di file non-Windows
- Cluster di file non-Windows server
- Dispositivo NAS (Network Attached Storage)

Per altre informazioni, vedere [effettuare il provisioning di un file server](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo seguente per altre informazioni:

[Prima di iniziare con il servizio App in Azure Stack](azure-stack-app-service-before-you-get-started.md)
