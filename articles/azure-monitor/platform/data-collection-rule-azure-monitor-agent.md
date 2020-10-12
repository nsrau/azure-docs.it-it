---
title: Configurare la raccolta dati per l'agente di monitoraggio di Azure (anteprima)
description: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: aa3225378f921792d1e8ba0442f2c555d095fb9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90968402"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Configurare la raccolta dati per l'agente di monitoraggio di Azure (anteprima)
Le regole di raccolta dati (DCR) definiscono i dati in arrivo in monitoraggio di Azure e specificano la posizione in cui devono essere inviati. Questo articolo descrive come creare una regola di raccolta dati per raccogliere dati dalle macchine virtuali usando l'agente di monitoraggio di Azure.

Per una descrizione completa delle regole di raccolta dati, vedere [regole di raccolta dati in monitoraggio di Azure (anteprima)](data-collection-rule-overview.md).

> [!NOTE]
> Questo articolo descrive come configurare i dati per le macchine virtuali con l'agente di monitoraggio di Azure, attualmente disponibile in anteprima. Vedere [Panoramica degli agenti di monitoraggio di Azure](agents-overview.md) per una descrizione degli agenti disponibili a livello generale e come usarli per raccogliere i dati.


## <a name="dcr-associations"></a>Associazioni DCR
Per applicare un DCR a una macchina virtuale, è necessario creare un'associazione per la macchina virtuale. Una macchina virtuale può avere un'associazione a più DCR e a un DCR possono essere associate più macchine virtuali. In questo modo è possibile definire un set di DCR, ognuno dei quali corrisponde a un requisito specifico, e applicarli solo alle macchine virtuali in cui si applicano. 

Si consideri, ad esempio, un ambiente con un set di macchine virtuali che eseguono un'applicazione line-of-business e altri che eseguono SQL Server. Potrebbe essere presente una regola di raccolta dati predefinita che si applica a tutte le macchine virtuali e a regole di raccolta dati separate che raccolgono i dati in modo specifico per l'applicazione line-of-business e per SQL Server. Le associazioni per le macchine virtuali alle regole di raccolta dati saranno simili a quelle del diagramma seguente.

![Il diagramma mostra le macchine virtuali che ospitano le applicazioni line-of-business e SQL Server associate alle regole di raccolta dati denominate Central-i t-default e LOB-app per l'applicazione line-of-business e i t-i t-default centrali e s q l per SQL Server.](media/data-collection-rule-azure-monitor-agent/associations.png)

## <a name="create-using-the-azure-portal"></a>Creare usando il portale di Azure
È possibile utilizzare il portale di Azure per creare una regola di raccolta dati e associare le macchine virtuali nella sottoscrizione a tale regola. L'agente di monitoraggio di Azure verrà installato automaticamente e verrà creata un'identità gestita per le macchine virtuali in cui non è già installato.

Nel menu **monitoraggio di Azure** della portale di Azure selezionare regole di **raccolta dati** nella sezione **Impostazioni** . Fare clic su **Aggiungi** per aggiungere una nuova regola di raccolta dati e assegnazione.

[![Regole di raccolta dati](media/azure-monitor-agent/data-collection-rules.png)](media/azure-monitor-agent/data-collection-rules.png#lightbox)

Fare clic su **Aggiungi** per creare una nuova regola e un set di associazioni. Fornire un **nome di regola** e specificare una **sottoscrizione** e un **gruppo di risorse**. Specifica la posizione in cui verrà creato il DCR. Le macchine virtuali e le relative associazioni possono trovarsi in qualsiasi sottoscrizione o gruppo di risorse nel tenant.

[![Nozioni fondamentali sulle regole di raccolta dati](media/azure-monitor-agent/data-collection-rule-basics.png)](media/azure-monitor-agent/data-collection-rule-basics.png#lightbox)

Nella scheda **macchine virtuali** aggiungere le macchine virtuali a cui deve essere applicata la regola di raccolta dati. L'agente di monitoraggio di Azure verrà installato nelle macchine virtuali in cui non è già installato.

[![Macchine virtuali della regola di raccolta dati](media/azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

Nella scheda **Raccogli e Distribuisci** fare clic su **Aggiungi origine dati** per aggiungere un'origine dati e un set di destinazione. Selezionare un **tipo di origine dati**. verranno visualizzati i dettagli corrispondenti da selezionare. Per i contatori delle prestazioni, è possibile scegliere tra un set predefinito di oggetti e la relativa frequenza di campionamento. Per gli eventi, è possibile scegliere da un set di log o strutture e il livello di gravità. 

[![Base origine dati](media/azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Per specificare altri log e contatori delle prestazioni, selezionare **personalizzato**. È quindi possibile specificare un [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) per tutti i valori specifici da raccogliere. Per esempi, vedere l' [esempio DCR](data-collection-rule-overview.md#sample-data-collection-rule) .

[![Personalizzata origine dati](media/azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

Nella scheda **destinazione** aggiungere una o più destinazioni per l'origine dati. Le origini dati degli eventi di Windows e syslog possono essere inviate solo ai log di monitoraggio di Azure. I contatori delle prestazioni possono inviare le metriche di monitoraggio di Azure e i log di monitoraggio di Azure.

[![Destination](media/azure-monitor-agent/data-collection-rule-destination.png)](media/azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Fare clic su **Aggiungi origine dati** , quindi su **+ Crea** per esaminare i dettagli della regola di raccolta dati e l'associazione con il set di macchine virtuali. Fare clic su **Crea** per crearlo.

> [!NOTE]
> Una volta create le associazioni e la regola di raccolta dei dati, potrebbero essere necessari fino a 5 minuti per l'invio dei dati alle destinazioni.

## <a name="createusingrestapi"></a>Creare usando l'API REST
Attenersi alla procedura seguente per creare un DCR e le associazioni usando l'API REST. 
1.Creare manualmente il file DCR usando il formato JSON illustrato nell' [esempio DCR](data-collection-rule-overview.md#sample-data-collection-rule).
2.Creare la regola usando l' [API REST](https://docs.microsoft.com/rest/api/monitor/datacollectionrules/create#examples).
3.Creare un'associazione per ogni macchina virtuale alla regola di raccolta dati usando l' [API REST](https://docs.microsoft.com/rest/api/monitor/datacollectionruleassociations/create#examples).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull' [agente di monitoraggio di Azure](azure-monitor-agent-overview.md).
- Altre informazioni sulle [regole di raccolta dati](data-collection-rule-overview.md).
