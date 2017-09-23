---
title: Targeting della solution in OMS | Documentazione Microsoft
description: "Il targeting della soluzione è una funzione di Operations Management Suite (OMS) che consente di limitare le soluzioni di gestione a un set specifico di agenti.  Questo articolo descrive come creare una configurazione di ambito e applicarla a una soluzione."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: cb73a2d7ae57a5a11869259dbe913ae83ffb2b01
ms.contentlocale: it-it
ms.lasthandoff: 04/28/2017

---
# <a name="use-solution-targeting-in-operations-management-suite-oms-to-scope-management-solutions-to-specific-agents-preview"></a>Usare il targeting della soluzione in Operations Management Suite (OMS) per definire l'ambito delle soluzioni di gestione e assegnarlo a determinati agenti (anteprima)
Quando si aggiunge una soluzione a OMS, viene automaticamente distribuita per impostazione predefinita a tutti gli agenti di Windows e Linux connessi all'area di lavoro di Log Analytics.  Si consiglia di gestire i costi e limitare la quantità di dati raccolti per una soluzione limitandola a un determinato set di agenti.  Questo articolo descrive come usare il **targeting della soluzione**, una funzionalità OMS che consente di applicare un ambito alle soluzioni.

## <a name="how-to-target-a-solution"></a>Come definire un ambito per una soluzione
Definire l'ambito di una soluzione è una procedura di tre passaggi, come descritto nelle sezioni seguenti.  Per completare questi passaggi è necessario sia il portale OMS che il portale di Azure.


### <a name="1-create-a-computer-group"></a>1. Creare un gruppo di computer
Specificare i computer che si desidera includere in un ambito creando un [gruppo di computer](../log-analytics/log-analytics-computer-groups.md) in Log Analytics.  Il gruppo di computer può essere basato su una ricerca log o importato da altre origini, ad esempio gruppi di Active Directory o WSUS. Come [descritto di seguito](#solutions-and-agents-that-cant-be-targeted), solo i computer direttamente collegati a Log Analytics verranno inclusi nell'ambito.

Dopo avere creato il gruppo di computer nell'area di lavoro è necessario includerlo in una configurazione di ambito che può essere applicata a una o più soluzioni.
 
 
 ### <a name="2-create-a-scope-configuration"></a>2. Creare una configurazione ambito
 Una **configurazione ambito** include uno o più gruppi di computer e può essere applicata a una o più soluzioni. 
 
 Creare una configurazione ambito con la seguente procedura.  

 1. Nel portale di Azure passare a **Log Analytics** e selezionare la propria area di lavoro.
 2. Nelle proprietà dell'area di lavoro in **Origini dati dell'area di lavoro** selezionare **Configurazioni ambito**.
 3. Fare clic su **Aggiungi** per creare una nuova configurazione ambito.
 4. Digitare un **Nome** per la configurazione ambito.
 5. Fare clic su **Selezionare i gruppi di computer**.
 6. Selezionare il gruppo di computer che è stato creato e, facoltativamente, tutti gli altri gruppi da aggiungere alla configurazione.  Fare clic su **Seleziona**.  
 6. Fare clic su **OK** per creare la configurazione ambito. 


 ### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Applicare la configurazione ambito a una soluzione.
Dopo aver creato una configurazione ambito, è possibile applicarla a una o più soluzioni.  Si noti che è possibile usare una sola configurazione ambito con più soluzioni e che ogni soluzione può usare solo una configurazione ambito.

Applicare una configurazione ambito con la seguente procedura.  

 1. Nel portale di Azure passare a **Log Analytics** e selezionare la propria area di lavoro.
 2. Nelle proprietà dell'area di lavoro selezionare **Soluzioni**.
 3. Fare clic sulla soluzione a cui applicare l'ambito.
 4. Nelle proprietà della soluzione in **Origini dati dell'area di lavoro** selezionare **Targeting della soluzione**.  Se l'opzione non è disponibile significa che [non è possibile eseguire il targeting di questa soluzione](#solutions-and-agents-that-cant-be-targeted).
 5. Fare clic su **Aggiungi configurazione dell'ambito**.  Se a questa soluzione è già applicata una configurazione, l'opzione non sarà disponibile.  È necessario rimuovere la configurazione esistente prima di aggiungerne un'altra.
 6. Fare clic sulla configurazione ambito creata.
 7. Osservare lo **Stato** della configurazione per assicurarsi che sia **Operazione riuscita**.  Se lo stato indica un errore, fare clic sui tre puntini a destra della configurazione e selezionare **Modifica configurazione dell'ambito** per apportare modifiche.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Soluzioni e agenti di cui non è possibile eseguire il targeting
Di seguito sono riportati i criteri che determinano gli agenti e le soluzioni non utilizzabili con il targeting della soluzione.

- Il targeting della soluzione si applica alle soluzioni distribuite agli agenti.
- Il targeting della soluzione si applica alle soluzioni fornite da Microsoft.  Non è applicabile alle soluzioni [create dall'utente o da partner](operations-management-suite-solutions-creating.md).
- È possibile filtrare solo gli agenti che si connettono direttamente a Log Analytics.  Le soluzioni saranno distribuite automaticamente a tutti gli agenti che fanno parte di un gruppo di gestione Operations Manager connesso, che siano o meno inclusi in una configurazione di ambito.

### <a name="exceptions"></a>Eccezioni
Il targeting della soluzione non può essere usato con le soluzioni seguenti anche se soddisfano i criteri stabiliti.

- Valutazione dell'integrità dell'agente

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle soluzioni di gestione, tra cui le soluzioni disponibili per l'installazione nell'ambiente, sono riportate in [Aggiungere soluzioni di gestione di Log Analytics di Azure all'area di lavoro](../log-analytics/log-analytics-add-solutions.md).
- Altre informazioni sulla creazione di gruppi di computer sono riportate in [Gruppi di computer nelle ricerche log in Log Analytics](../log-analytics/log-analytics-computer-groups.md).
