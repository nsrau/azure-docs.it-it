---
title: Panoramica di Cloudyn in Azure
description: Cloudyn è una soluzione di gestione dei costi multi-cloud che consente di usare Azure e altre risorse cloud.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: overview
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 9e1acee631316ec961af364769daa1f2a3001e72
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80065869"
---
# <a name="what-is-the-cloudyn-service"></a>Che cos'è il servizio Cloudyn?

Cloudyn, un'affiliata Microsoft, consente di tenere traccia dell'uso del cloud e delle spese per le risorse di Azure e altri provider cloud, tra cui AWS e Google. I dashboard report di facile comprensione agevolano l'allocazione dei costi e gli showback/chargeback. Cloudyn consente di ottimizzare la spesa per il cloud individuando le risorse scarsamente usate che è quindi possibile gestire e adattare.

Per un video introduttivo, vedere [Introduzione a Cloudyn di Azure](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo/).
 
Gestione costi di Azure offre funzionalità simili a quelle di Cloudyn. Gestione costi di Azure è una soluzione di gestione costi nativa di Azure. È un modo per analizzare i costi, creare e gestire budget, esportare i dati, esaminare e implementare gli elementi consigliati per l'ottimizzazione e di conseguenza risparmiare. Per altre informazioni, vedere [Gestione costi di Azure](../cost-management-billing-overview.md).
 
[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

Guardare il [video Azure Cost Management and Cloudyn](https://www.youtube.com/watch?v=15DzKPMBRxM) (Gestione costi di Azure e Cloudyn) per ottenere consigli su quando usare Gestione costi di Azure o Cloudyn in base alle proprie esigenze aziendali.
 
>[!VIDEO https://www.youtube.com/embed/15DzKPMBRxM]

## <a name="monitor-usage-and-spending"></a>Monitorare l'uso e le spese

Il monitoraggio dell'uso e delle spese è particolarmente importante per le infrastrutture cloud perché le organizzazioni pagano per le risorse che consumano nel corso del tempo. Quando l'uso supera le soglie di contratto, possono essere registrate rapidamente eccedenze dei costi impreviste. Alcuni fattori importanti possono complicare il monitoraggio ad hoc. Innanzitutto, la proiezione dei costi in base all'uso medio presuppone che il consumo rimanga coerente per un determinato periodo di fatturazione. In secondo luogo, quando i costi si avvicinano al budget o lo superano, è importante ricevere notifiche in modo proattivo per modificare le spese. Inoltre, i provider di servizi cloud potrebbero non offrire la proiezione dei costi in base alle soglie o a report di confronto per periodi.

I report consentono di monitorare le spese per analizzare e tenere traccia dell'uso del cloud, dei costi e dell'andamento. Tramite l'uso dei report riferiti a un periodo di tempo, è possibile rilevare le anomalie che differiscono dall'andamento normale. Le inefficienze nella distribuzione del cloud sono visibili nei report sull'ottimizzazione. È inoltre possibile notare inefficienze nei report di analisi dei costi.

## <a name="manage-costs"></a>Gestire i costi

I dati cronologici possono aiutare a gestire i costi durante l'analisi dell'uso e dei costi nel tempo per individuare le tendenze. Le tendenze vengono quindi usate per prevedere la spesa futura. Cloudyn comprende anche utili report sui costi previsti.

L'allocazione dei costi gestisce i costi analizzandoli in base ai criteri di tag dell'organizzazione. È possibile usare i tag per gli account, le risorse e le entità personalizzate per perfezionare l'allocazione dei costi. La gestione delle categorie organizza i tag per contribuire a offrire una governance aggiuntiva. Inoltre, l'allocazione dei costi può essere usata per lo showback/chargeback per visualizzare l'uso delle risorse e i costi associati per influenzare i comportamenti di consumo o addebitare i costi ai clienti tenant.

Il controllo degli accessi consente di gestire i costi assicurando che gli utenti e i team accedano unicamente ai dati di gestione dei costi necessari. Per assegnare gli accessi vengono utilizzati la struttura delle entità, la gestione degli utenti e i report programmati con elenchi di destinatari.

Gli avvisi contribuiscono a gestire i costi inviando una notifica automatica quando si verifica una spesa insolita o una spesa eccessiva. Gli avvisi possono inviare una notifica automaticamente anche ad altre parti interessate per le anomalie di spesa e i rischi di spesa eccessiva. Vari report supportano gli avvisi basati su soglie di budget e di costi. Tuttavia gli avvisi non sono attualmente supportati per le sottoscrizioni o gli account dei partner CSP.

## <a name="improve-efficiency"></a>Migliorare l'efficienza

Con Cloudyn, è possibile stabilire l'uso ottimale delle macchine virtuali e identificare le macchine virtuali inattive o rimuoverle, oltre ai dischi non collegati. Usando le informazioni contenute nei report di ottimizzazione del ridimensionamento e dell'inefficienza, è possibile creare un piano per ridimensionare o rimuovere le macchine virtuali inattive. Tuttavia i report di ottimizzazione non sono attualmente supportati per le sottoscrizioni o gli account dei partner CSP.

Se è stato eseguito il provisioning delle istanze riservate di AWS, è possibile migliorare l'uso delle istanze riservate con i report di ottimizzazione, in cui è possibile visualizzare i consigli per gli acquisti, modificare le prenotazioni inutilizzate e pianificare il provisioning.


## <a name="next-steps"></a>Passaggi successivi

Una volta acquisita familiarità con Cloudyn, il passaggio successivo consiste nel registrare l'ambiente cloud e iniziare a esplorare i dati.

- [Eseguire la registrazione al programma per i partner CSP e visualizzare i dati sui costi](quick-register-csp.md)
