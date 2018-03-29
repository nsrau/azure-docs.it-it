---
title: Panoramica di Gestione costi di Azure | Microsoft Docs
description: Gestione costi di Azure è una soluzione di gestione dei costi multi-cloud che consente di usare Azure e altre risorse cloud.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: overview
ms.service: cost-management
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 3e5caff5ff1af79154baddf39bf465ddea5aadae
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="what-is-azure-cost-management"></a>Che cos'è la gestione dei costi di Azure?

Gestione costi di Azure è concesso in licenza da Cloudyn, un'affiliata Microsoft, e consente di tenere traccia dell'uso del cloud e delle spese per le risorse di Azure e altri provider cloud, tra cui AWS e Google. I dashboard report di facile comprensione agevolano l'allocazione dei costi e gli showback/chargeback. La gestione dei costi consente di ottimizzare la spesa per il cloud individuando le risorse scarsamente usate che è quindi possibile gestire e adattare.

Per un video introduttivo, vedere [Introduzione a Gestione costi di Azure](https://azure.microsoft.com/en-us/resources/videos/azure-cost-management-overview-and-demo).

## <a name="monitor-usage-and-spending"></a>Monitorare l'uso e le spese

Il monitoraggio dell'uso e delle spese è particolarmente importante per le infrastrutture cloud perché le organizzazioni pagano per le risorse che consumano nel corso del tempo. Quando l'uso supera le soglie di contratto, possono essere registrate rapidamente eccedenze dei costi impreviste. Alcuni fattori importanti possono complicare il monitoraggio ad hoc. Innanzitutto, la proiezione dei costi in base all'uso medio presuppone che il consumo rimanga coerente per un determinato periodo di fatturazione. In secondo luogo, quando i costi si avvicinano al budget o lo superano, è importante ricevere notifiche in modo proattivo per modificare le spese. Inoltre, i provider di servizi cloud potrebbero non offrire la proiezione dei costi in base alle soglie o a report di confronto per periodi.

I report consentono di monitorare le spese per analizzare e tenere traccia dell'uso del cloud, dei costi e dell'andamento. Tramite l'uso dei report riferiti a un periodo di tempo, è possibile rilevare le anomalie che differiscono dall'andamento normale. Le inefficienze nella distribuzione del cloud sono visibili nei report sull'ottimizzazione. È inoltre possibile notare inefficienze nei report di analisi dei costi.

![Report sui costi nel tempo](media\overview\cost-over-time-rpt.png)


## <a name="manage-costs"></a>Gestire i costi

I dati cronologici possono aiutare a gestire i costi durante l'analisi dell'uso e dei costi nel tempo per individuare le tendenze. Le tendenze vengono quindi usate per prevedere la spesa futura. La gestione dei costi comprende anche utili report sui costi previsti.

L'allocazione dei costi gestisce i costi analizzandoli in base ai criteri di tag dell'organizzazione. È possibile usare i tag per gli account, le risorse e le entità personalizzate per perfezionare l'allocazione dei costi. La gestione delle categorie organizza i tag per contribuire a offrire una governance aggiuntiva. Inoltre, l'allocazione dei costi può essere usata per lo showback/chargeback per visualizzare l'uso delle risorse e i costi associati per influenzare i comportamenti di consumo o addebitare i costi ai clienti tenant.

Il controllo degli accessi consente di gestire i costi assicurando che gli utenti e i team accedano unicamente ai dati di gestione dei costi necessari. Per assegnare gli accessi vengono utilizzati la struttura delle entità, la gestione degli utenti e i report programmati con elenchi di destinatari.

Gli avvisi contribuiscono a gestire i costi inviando una notifica automatica quando si verifica una spesa insolita o una spesa eccessiva. Gli avvisi possono inviare una notifica automaticamente anche ad altre parti interessate per le anomalie di spesa e i rischi di spesa eccessiva. Vari report supportano gli avvisi basati su soglie di budget e di costi. Tuttavia gli avvisi non sono attualmente supportati per le sottoscrizioni o gli account dei partner CSP.

## <a name="improve-efficiency"></a>Migliorare l'efficienza

È possibile stabilire l'uso ottimale delle macchine virtuali e identificare le macchine virtuali inattive o rimuoverle, oltre ai dischi non collegati, con la gestione dei costi. Usando le informazioni contenute nei report di ottimizzazione del ridimensionamento e dell'inefficienza, è possibile creare un piano per ridimensionare o rimuovere le macchine virtuali inattive. Tuttavia i report di ottimizzazione non sono attualmente supportati per le sottoscrizioni o gli account dei partner CSP.

![consigli sul ridimensionamento](.\media\overview\sizing.png)

Se è stato eseguito il provisioning delle istanze riservate di AWS, è possibile migliorare l'uso delle istanze riservate con i report di ottimizzazione, in cui è possibile visualizzare i consigli per gli acquisti, modificare le prenotazioni inutilizzate e pianificare il provisioning.

## <a name="next-steps"></a>Passaggi successivi

Una volta acquisita familiarità con la gestione dei costi, il passaggio successivo consiste nel registrare l'ambiente cloud e iniziare a esplorare i dati.

- [Registrare una singola sottoscrizione di Azure](quick-register-azure-sub.md)
