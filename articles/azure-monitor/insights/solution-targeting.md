---
title: Targeting delle soluzioni di monitoraggio in Monitoraggio di Azure | Microsoft Docs
description: Il targeting delle soluzioni di monitoraggio consente di limitare le soluzioni di monitoraggio a un set specifico di agenti.  Questo articolo descrive come creare una configurazione di ambito e applicarla a una soluzione.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: 4082847e1871fc03713471b0c043dddb80f91b0d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769234"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Targeting delle soluzioni di monitoraggio in Monitoraggio di Azure (Anteprima)
Quando viene aggiunta una soluzione di monitoraggio alla sottoscrizione, la soluzione viene automaticamente distribuita per impostazione predefinita a tutti gli agenti di Windows e Linux connessi all'area di lavoro di Log Analytics.  Si consiglia di gestire i costi e limitare la quantità di dati raccolti per una soluzione limitandola a un determinato set di agenti.  Questo articolo descrive come usare il **targeting della soluzione**, una funzionalità che consente di applicare un ambito alle soluzioni.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Come definire un ambito per una soluzione
Definire l'ambito di una soluzione è una procedura di tre passaggi, come descritto nelle sezioni seguenti. 


### <a name="1-create-a-computer-group"></a>1. Creare un gruppo di computer
Specificare i computer da includere in un ambito creando un [gruppo di computer](../platform/computer-groups.md) in Monitoraggio di Azure.  Il gruppo di computer si può basare su una query di log o può essere importato da altre origini, ad esempio gruppi di Active Directory o WSUS. Come [descritto di seguito](#solutions-and-agents-that-cant-be-targeted), solo i computer direttamente collegati a Monitoraggio di Azure verranno inclusi nell'ambito.

Dopo avere creato il gruppo di computer nell'area di lavoro è necessario includerlo in una configurazione di ambito che può essere applicata a una o più soluzioni.
 
 
### <a name="2-create-a-scope-configuration"></a>2. Creare una configurazione ambito
 Una **configurazione ambito** include uno o più gruppi di computer e può essere applicata a una o più soluzioni. 
 
 Creare una configurazione ambito con la seguente procedura.  

 1. Nel portale di Azure passare ad **Aree di lavoro di Log Analytics** e selezionare l'area di lavoro personale.
 2. Nelle proprietà dell'area di lavoro in **Origini dati dell'area di lavoro** selezionare **Configurazioni ambito**.
 3. Fare clic su **Aggiungi** per creare una nuova configurazione ambito.
 4. Digitare un **Nome** per la configurazione ambito.
 5. Fare clic su **Selezionare i gruppi di computer**.
 6. Selezionare il gruppo di computer che è stato creato e, facoltativamente, tutti gli altri gruppi da aggiungere alla configurazione.  Fare clic su **Seleziona**.  
 6. Fare clic su **OK** per creare la configurazione ambito. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Applicare la configurazione ambito a una soluzione.
Dopo aver creato una configurazione ambito, è possibile applicarla a una o più soluzioni.  Si noti che è possibile usare una sola configurazione ambito con più soluzioni e che ogni soluzione può usare solo una configurazione ambito.

Applicare una configurazione ambito con la seguente procedura.  

 1. Nel portale di Azure passare ad **Aree di lavoro di Log Analytics** e selezionare l'area di lavoro personale.
 2. Nelle proprietà dell'area di lavoro selezionare **Soluzioni**.
 3. Fare clic sulla soluzione a cui applicare l'ambito.
 4. Nelle proprietà della soluzione in **Origini dati dell'area di lavoro** selezionare **Targeting della soluzione**.  Se l'opzione non è disponibile significa che [non è possibile eseguire il targeting di questa soluzione](#solutions-and-agents-that-cant-be-targeted).
 5. Fare clic su **Aggiungi configurazione dell'ambito**.  Se a questa soluzione è già applicata una configurazione, l'opzione non sarà disponibile.  È necessario rimuovere la configurazione esistente prima di aggiungerne un'altra.
 6. Fare clic sulla configurazione ambito creata.
 7. Osservare lo **Stato** della configurazione per assicurarsi che sia **Operazione riuscita**.  Se lo stato indica un errore, fare clic sui tre puntini a destra della configurazione e selezionare **Modifica configurazione dell'ambito** per apportare modifiche.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Soluzioni e agenti di cui non è possibile eseguire il targeting
Di seguito sono riportati i criteri che determinano gli agenti e le soluzioni non utilizzabili con il targeting della soluzione.

- Il targeting della soluzione si applica alle soluzioni distribuite agli agenti.
- Il targeting della soluzione si applica alle soluzioni fornite da Microsoft.  Non è applicabile alle soluzioni [create dall'utente o da partner](solutions-creating.md).
- È possibile filtrare solo gli agenti che si connettono direttamente a Monitoraggio di Azure.  Le soluzioni saranno distribuite automaticamente a tutti gli agenti che fanno parte di un gruppo di gestione Operations Manager connesso, che siano o meno inclusi in una configurazione di ambito.

### <a name="exceptions"></a>Eccezioni
Il targeting della soluzione non può essere usato con le soluzioni seguenti anche se soddisfano i criteri stabiliti.

- Valutazione dell'integrità dell'agente

## <a name="next-steps"></a>Passaggi successivi
- Vedere altre informazioni sulle soluzioni di monitoraggio, tra cui le soluzioni disponibili per l'installazione nell'ambiente, riportate in [Aggiungere soluzioni di monitoraggio di Azure Log Analytics all'area di lavoro](solutions.md).
- Vedere altre informazioni sulla creazione di gruppi di computer riportate in [Gruppi di computer nelle query di log in Monitoraggio di Azure](../platform/computer-groups.md).
