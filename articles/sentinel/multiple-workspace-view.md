---
title: Lavorare con gli eventi imprevisti di Sentinel di Azure in molte aree di lavoro contemporaneamente | Microsoft Docs
description: Come visualizzare gli eventi imprevisti in più aree di lavoro contemporaneamente in Sentinel di Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2020
ms.author: yelevin
ms.openlocfilehash: 448998328ff15b74b0aa0b17e2435a7ff55c54a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83124178"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>Lavorare con gli eventi imprevisti in molte aree di lavoro contemporaneamente 

 Per sfruttare appieno le funzionalità di Sentinel di Azure, Microsoft consiglia di usare un ambiente con una sola area di lavoro. Tuttavia, esistono alcuni casi d'uso che richiedono la presenza di diverse aree di lavoro, in alcuni casi, ad esempio quella di un [provider di servizi di sicurezza gestito (MSSP)](./multiple-tenants-service-providers.md) e dei relativi clienti, in più tenant. La **visualizzazione di più aree** di lavoro consente di visualizzare e gestire gli eventi imprevisti della sicurezza in diverse aree di lavoro contemporaneamente, anche attraverso i tenant, consentendo di mantenere la visibilità e il controllo completi della velocità di risposta di sicurezza dell'organizzazione.

## <a name="entering-multiple-workspace-view"></a>Immissione di più visualizzazioni dell'area di lavoro

Quando si apre Azure Sentinel, viene visualizzato un elenco di tutte le aree di lavoro a cui si dispone dei diritti di accesso, in tutti i tenant e le sottoscrizioni selezionate. A sinistra di ogni nome dell'area di lavoro è presente una casella di controllo. Facendo clic sul nome di una singola area di lavoro, si passerà all'area di lavoro. Per scegliere più aree di lavoro, fare clic su tutte le caselle di controllo corrispondenti, quindi fare clic sul pulsante di **visualizzazione di più aree di lavoro** nella parte superiore della pagina.

> [!IMPORTANT]
> Una visualizzazione dell'area di lavoro multipla supporta attualmente un massimo di 10 aree di lavoro visualizzate simultaneamente. 
> 
> Se si selezionano più di 10 aree di lavoro, verrà visualizzato un messaggio di avviso.

Si noti che nell'elenco delle aree di lavoro è possibile visualizzare la directory, la sottoscrizione, la località e il gruppo di risorse associato a ogni area di lavoro. La directory corrisponde al tenant.

   ![Scegliere più aree di lavoro](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>Gestione degli eventi imprevisti

In una **visualizzazione di più aree di lavoro**è disponibile per ora solo la schermata **eventi imprevisti** . Sembra e funziona nella maggior parte dei casi, come nella schermata **eventi imprevisti** regolari. Tuttavia, esistono alcune importanti differenze:

   ![Visualizzare gli eventi imprevisti in più aree di lavoro](./media/multiple-workspace-view/incidents.png)

- I contatori nella parte superiore della pagina: *eventi imprevisti aperti*, *nuovi eventi imprevisti*, *in corso*e così via, mostrano i numeri per tutte le aree di lavoro selezionate collettivamente.

- Gli eventi imprevisti da tutte le aree di lavoro e le directory selezionate (tenant) verranno visualizzati in un unico elenco unificato. È possibile filtrare l'elenco in base all'area di lavoro e alla directory, oltre ai filtri della schermata **eventi imprevisti** regolari.

- È necessario disporre delle autorizzazioni di lettura e scrittura per tutte le aree di lavoro da cui sono stati selezionati gli eventi imprevisti. Se si dispone solo delle autorizzazioni di lettura per alcune aree di lavoro, verranno visualizzati messaggi di avviso se si selezionano eventi imprevisti in tali aree di lavoro. Non sarà possibile modificare questi eventi imprevisti o altri utenti selezionati insieme a tali eventi (anche se si dispone delle autorizzazioni per gli altri).

- Se si sceglie un singolo evento imprevisto e si fa clic su **Visualizza dettagli completi** o **esamina**, il contesto dei dati dell'area di lavoro dell'evento imprevisto e altri utenti non saranno più disponibili.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come visualizzare e gestire gli eventi imprevisti in più aree di lavoro di Azure Sentinel simultaneamente. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

