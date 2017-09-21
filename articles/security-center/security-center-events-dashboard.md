---
title: Monitoraggio ed elaborazione degli eventi di sicurezza nel Centro sicurezza di Azure | Microsoft Docs
description: "Informazioni su come è possibile usare il dashboard di eventi del Centro sicurezza per visualizzare gli eventi di sicurezza da computer che non hanno Azure e dalle macchine virtuali di Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 01ac75244839c0e3c1ac350d4271677feb21a9d7
ms.contentlocale: it-it
ms.lasthandoff: 09/13/2017

---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Monitoraggio ed elaborazione degli eventi di sicurezza nel Centro sicurezza di Azure
Il dashboard degli eventi offre una panoramica del numero di eventi di sicurezza raccolti nel tempo e un elenco di eventi importanti che potrebbero richiedere l'attenzione dell'utente.  

> [!NOTE]
> Per usare questa funzionalità, l'area di lavoro deve eseguire la versione 2 di Log Analytics e trovarsi nel livello Standard del Centro sicurezza. Per altre informazioni sul livello Standard vedere la [pagina dei prezzi](security-center-pricing.md) del Centro sicurezza.
>
>

## <a name="what-is-a-security-event"></a>Definizione di evento di sicurezza
Il Centro sicurezza usa Microsoft Monitoring Agent per raccogliere diverse configurazione ed eventi correlati alla sicurezza dai computer e archivia questi eventi nelle aree di lavoro dell'utente. Ecco alcuni esempi di dati: registri del sistema operativo, ovvero registri eventi di Windows, processi in esecuzione ed eventi delle soluzioni di sicurezza integrati nel Centro sicurezza. Microsoft Monitoring Agent copia anche i file di dump di arresto anomalo del sistema nelle aree di lavoro.

## <a name="events-processed-dashboard"></a>Dashboard degli eventi elaborati
Dal menu principale del Centro sicurezza o dal pannello **Panoramica** del Centro sicurezza si accede al dashboard **Eventi**.  

![Dashboard degli eventi elaborati][1]

Il riquadro **Eventi** nel **Centro sicurezza** mostra il numero di eventi che passano nel Centro sicurezza dai computer che non hanno Azure e dalle macchine virtuali di Azure.

Il **dashboard Eventi** offre una panoramica del numero di eventi elaborati nel tempo e un elenco di eventi.

 ![Dashboard][2]

 La parte superiore del dashboard mostra tutti gli eventi elaborati nell'ultima settimana. La parte inferiore del dashboard elenca gli eventi rilevanti e tutti gli eventi per tipo:

 - Gli **Eventi rilevanti** includono le query di eventi contenute nel Centro sicurezza e le query di eventi create e aggiunte. Il dashboard offre anche una visualizzazione rapida del conteggio di ogni evento rilevante.
 - La sezione **Tutti gli eventi per tipo** mostra i tipi di eventi che vengono ricevuti e un conteggio per ogni tipo. Esempi di tipo di evento sono SecurityEvent, CommonSecurityLog, WindowsFirewall e W3CIISLog.

## <a name="view-processed-event-details"></a>Visualizzare i dettagli dell'evento elaborato
1. Selezionare **Eventi elaborati** nel menu principale del **Centro sicurezza**.
2. Potrebbe aprirsi il selettore dell'area di lavoro degli **Eventi elaborati**. Se si dispone solo di un'area di lavoro, questo selettore dell'area di lavoro non viene visualizzato. Se si dispone di più di un'area di lavoro, è necessario selezionare un'area di lavoro per visualizzarne i dettagli dell'evento elaborato. Se si dispone di più di un'area di lavoro, selezionare un'area di lavoro dall'elenco.

  ![Elenco dell'area di lavoro][3]

3. Si apre il dashboard **Eventi elaborati** che mostra i dettagli dell'evento per l'area di lavoro selezionata. È possibile visualizzare gli eventi rilevanti e tutti gli eventi per tipo.  In questo esempio, è stato selezionato **Notable events** (Eventi rilevanti).

  ![Eventi rilevanti][4]

4. È possibile eseguire una query per più dati nell'area di lavoro se si seleziona un tipo di evento. In questo esempio, è stato selezionato **SecurityEvent**.

  ![Selezione di un tipo di evento][5]

5. Si apre **Ricerca log** con dettagli aggiuntivi sul tipo di evento.

  ![Ricerca log][6]

## <a name="add-a-notable-event"></a>Aggiungere un evento rilevante
Il Centro sicurezza contiene eventi rilevanti predefiniti. È possibile aggiungere eventi rilevanti in base alle proprie query usando il [linguaggio di query di Log Analytics](../log-analytics/log-analytics-search-reference.md). Per aggiungere un evento rilevante è necessario ritornare nel dashboard **Eventi elaborati**.

1. Selezionare **Add Notable Event** (Aggiungi un evento rilevante).

  ![Aggiungere un evento rilevante][7]

2. Si apre **Add custom notable event** (Aggiungi evento rilevante personalizzato).  In **Nome visualizzato** immettere un nome per l'evento rilevante. In **Query di ricerca** immettere la query per l'evento.

  ![Immettere la query][8]

4. Selezionare **OK**.

## <a name="update-your-workspace-for-events-processing"></a>Aggiornare l'area di lavoro per l'elaborazione degli eventi
Per usa l'elaborazione dell'evento ne Centro sicurezza, l'area di lavoro deve eseguire la versione 2 di Log Analytics e trovarsi nel livello Standard del Centro sicurezza. Il selettore dell'area di lavoro **Eventi** identifica le aree di lavoro che non soddisfano questi requisiti.

![L'area di lavoro non soddisfa i requisiti][9]

Se la riga dell'area di lavoro:

- contiene **REQUIRES UPDATE** (AGGIORNAMENTO NECESSARIO), è necessario aggiornare l'area di lavoro alla versione 2 di Log Analytics
- contiene **UPGRADE PLAN** (AGGIORNA PIANO), è necessario aggiornare l'area di lavoro a livello Standard del Centro sicurezza
- è vuoto, l'area di lavoro soddisfa i requisiti e per aprire il dashboard basta selezionare un'area di lavoro

> [!NOTE]
> In **Eventi**, la colonna **EVENTI** indica la quantità di eventi in ogni area di lavoro.  Questa colonna è vuota per alcune aree di lavoro perché il livello Gratuito del Centro sicurezza viene applicato all'area di lavoro. Nel livello Gratuito il Centro sicurezza raccoglierà gli eventi che però non vengono salvati in Log Analytics e non sono disponibili nel dashboard.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Aggiornare l'area di lavoro alla versione 2 di Log Analytics
1. Selezionare un'area di lavoro con **REQUIRES UPDATE** (AGGIORNAMENTO NECESSARIO).
2. Si apre **Cerca aggiornamento**. Selezionare **Upgrade Now** (Aggiorna ora).

  ![Aggiorna ora][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Passare al piano Standard del Centro sicurezza
1. Selezionare un'area di lavoro con **UPGRADE PLAN** (AGGIORNA PIANO).
2. Viene aperto il **dashboard Eventi**. Selezionare **Try the Events Processed dashboard** (Prova il dashboard degli eventi elaborati).

  ![Prova il dashboard][11]

3. In **Onboarding nella protezione avanzata** selezionare l'area di lavoro che si sta aggiornando.
4. In **Prezzi** selezionare **Standard**.
5. Selezionare **Salva**.

  ![Passare al livello Standard][12]

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato descritto come usare il dashboard degli eventi elaborati nel Centro sicurezza. Per altre informazioni sul funzionamento del dashboard e su come scrivere le proprie query di eventi, vedere:

- [Che cos'è Log Analytics?](../log-analytics/log-analytics-overview.md) : Panoramica di Log Analytics
- [Informazioni sulle ricerche log in Log Analytics](../log-analytics/log-analytics-log-search-new.md): descrive come vengono usate le ricerche log in Log Analytics e illustra i concetti con cui occorre avere familiarità prima di creare una ricerca log
- [Guida di riferimento alla ricerca in Log Analytics](../log-analytics/log-analytics-search-reference.md): informazioni su come scrivere le query di eventi usando il linguaggio di query in Log

Per altre informazioni sul Centro sicurezza, vedere:

- [Panoramica del Centro sicurezza](security-center-intro.md): descrive le funzionalità principali del Centro sicurezza

<!--Image references-->
[1]: ./media/security-center-events-dashboard/events-processed.png
[2]: ./media/security-center-events-dashboard/dashboard.png
[3]: ./media/security-center-events-dashboard/view-processed-event.png
[4]: ./media/security-center-events-dashboard/notable-event.png
[5]: ./media/security-center-events-dashboard/events-by-type.png
[6]: ./media/security-center-events-dashboard/log-search-detail.png
[7]: ./media/security-center-events-dashboard/add-notable-event.png
[8]: ./media/security-center-events-dashboard/create-query.png
[9]: ./media/security-center-events-dashboard/requires-update.png
[10]: ./media/security-center-events-dashboard/search-upgrade.png
[11]: ./media/security-center-events-dashboard/try-dashboard.png
[12]: ./media/security-center-events-dashboard/onboard-workspace.png

