---
title: Informazioni sul processo di spostamento in Spostamento risorse di Azure
description: Informazioni sul processo di spostamento delle risorse tra aree con Spostamento risorse di Azure
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.openlocfilehash: 5261904dd1ee7f280209015d8f756a055dfab57e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522946"
---
# <a name="about-the-move-process"></a>Informazioni sul processo di spostamento

[Spostamento risorse di Azure](overview.md) consente di spostare risorse di Azure tra aree di Azure. Questo articolo riepiloga i componenti usati da Spostamento risorse e descrive il processo di spostamento. 


## <a name="components"></a>Componenti

Durante lo spostamento tra aree vengono usati questi componenti.

**Componente** | **Dettagli**
--- | ---
**Spostamento risorse** |  Spostamento risorse si coordina con i [provider di risorse di Azure](../azure-resource-manager/management/resource-providers-and-types.md) per orchestrare lo spostamento delle risorse tra le aree. Analizza le dipendenze delle risorse e ne gestisce lo stato durante il processo di spostamento. 
**Raccolta di spostamento** |  Una raccolta di spostamento è un oggetto [Azure Resource Manager](../azure-resource-manager/management/overview.md).<br/><br/> La raccolta di spostamento viene creata durante il processo di spostamento tra aree, per ogni combinazione abbinata di aree di origine e di destinazione in una sottoscrizione. La raccolta contiene i metadati e le informazioni di configurazione sulle risorse che si desidera spostare.<br/><br/>Le risorse aggiunte a una raccolta di spostamento devono trovarsi nella stessa sottoscrizione, ma possono essere incluse in gruppi di risorse diversi. 
**Risorsa di spostamento** | Una risorsa che viene aggiunta a una raccolta di spostamento viene rilevata da Spostamento risorse come risorsa di spostamento.<br/><br/> Spostamento risorse gestisce le informazioni per tutte le risorse di spostamento nella raccolta di spostamento e mantiene una relazione uno-a-uno tra la risorsa di origine e quella di destinazione. 
**Dipendenze** | Spostamento risorse convalida le risorse aggiunte a una raccolta e controlla se hanno dipendenze non incluse nella raccolta di spostamento.<br/><br/> Dopo aver identificato le dipendenze per una risorsa, è possibile aggiungerle alla raccolta di spostamento in modo da spostarle. È anche possibile selezionare risorse esistenti alternative nell'area di destinazione. Tutte le dipendenze devono essere risolte prima di avviare lo spostamento. 



## <a name="move-region-process"></a>Processo di spostamento tra aree 

![Diagramma che mostra i passaggi di spostamento](./media/about-move-process/move-steps.png)

Ogni risorsa di spostamento attraversa i passaggi riepilogati.

**Step** | **Dettagli** | **Stato/problemi**
--- | --- | --- 
**Passaggio 1: Selezionare le risorse** | Selezionare una risorsa. La risorsa verrà aggiunta alla raccolta di spostamento. | Lo stato della risorsa passa a *Preparazione in sospeso*.<br/><br/> Se la risorsa ha dipendenze, *Validate dependency* (Convalida dipendenza) indica che è necessario aggiungere alla raccolta di spostamento risorse dipendenti.
**Passaggio 2: Convalidare le dipendenze** | Avviare il processo di convalida.<br/><br/> Se la convalida indica che le risorse dipendenti sono in sospeso, aggiungerle alla raccolta di spostamento. <br/><br/> Aggiungere tutte le risorse dipendenti, anche se non si desidera spostarle. In un secondo momento sarà possibile specificare che le risorse che si stanno spostando devono usare piuttosto risorse diverse nell'area di destinazione.<br/><br/> Eseguire di nuovo la convalida fino a esaurimento delle risorse in sospeso. | Dopo l'aggiunta di tutte le dipendenze e l'esito positivo della convalida, lo stato delle risorse passa a *Preparazione in sospeso*, senza alcun problema.
**Passaggio 3: Effettuare la preparazione** | Avviare il processo di preparazione. I passaggi di preparazione dipendono dalle risorse che verranno spostate:<br/><br/> - **Risorse senza stato**: le risorse senza stato includono solo le informazioni di configurazione. Queste risorse non richiedono la replica continua dei dati per poter essere spostate. Alcuni esempi includono le reti virtuali di Azure, le schede di rete, i servizi di bilanciamento del carico e i gruppi di sicurezza di rete. Per questo tipo di risorsa, il processo di preparazione genera un modello Azure Resource Manager.<br/><br/> - **Risorse con stato**: le risorse con stato includono sia le informazioni di configurazione che i dati che devono essere spostati. Alcuni esempi includono le macchine virtuali di Azure e i database SQL di Azure. Il processo di preparazione è diverso per ogni risorsa. Può includere la replica della risorsa di origine nell'area di destinazione. | Con l'avvio del processo, lo stato delle risorse passa a *Preparazione in corso*.<br/><br/> Al termine della preparazione, lo stato delle risorse passa ad *Avvio spostamento in sospeso*, senza problemi.<br/><br/> Se il processo ha esito negativo, lo stato passa a *Preparazione non riuscita*.
**Passaggio 4: Avviare lo spostamento** | Avviare il processo di spostamento. Il metodo di spostamento dipende dal tipo di risorsa:<br/><br/> - **Senza stato**: in genere per le risorse senza stato il processo di spostamento distribuisce nell'area di destinazione un modello importato. Il modello è basato sulle impostazioni delle risorse di origine e sulle eventuali modifiche manuali apportate alle impostazioni di destinazione.<br/><br/> - **Con stato**: per le risorse con stato, il processo di spostamento può determinare la creazione della risorsa o l'abilitazione di una copia nell'area di destinazione.<br/><br/>  Solo per le risorse con stato, l'avvio di uno spostamento può determinare tempi di inattività delle risorse di origine. Ad esempio macchine virtuali e risorse SQL. | Con l'avvio dello spostamento, lo stato passa ad *Avvio spostamento in corso*.<br/><br/> Se l'avvio dello spostamento ha esito positivo, lo stato delle risorse passa a *Commit spostamento in sospeso*, senza problemi. <br/><br/> Se il processo di spostamento ha esito negativo, lo stato passa ad *Avvio spostamento non riuscito*.
**Passaggio 5, opzione 1: Rimuovere lo spostamento** | Dopo lo spostamento iniziale, è possibile decidere se procedere con lo spostamento completo. Se non si desidera procedere, è possibile rimuovere lo spostamento e Spostamento risorse eliminerà le risorse create nella destinazione. Il processo di replica per le risorse con stato continua dopo il processo di rimozione. Questa opzione è utile per i test. | Con la rimozione delle risorse, lo stato passa a *Discard in progress* (Rimozione in corso).<br/><br/> Se la rimozione ha esito positivo, lo stato passa ad *Avvio spostamento in sospeso*, senza problemi.<br/><br/> Se invece la rimozione ha esito negativo, lo stato passa a *Rimozione origine non riuscita*, senza problemi. 
**Passaggio 5, opzione 2: Eseguire il commit dello spostamento** | Se dopo lo spostamento iniziale si desidera procedere con uno spostamento completo, è necessario verificare le risorse nell'area di destinazione e, quando si è pronti, eseguire il commit dello spostamento.<br/><br/> Solo per le risorse con stato, il commit può rendere inaccessibili risorse di origine quali le macchine virtuali o le risorse SQL. | Se si esegue il commit dello spostamento, lo stato delle risorse passa a *Commit spostamento in corso**.<br/><br/> Dopo un commit eseguito con esito positivo, lo stato delle risorse passa a *Commit move completed* (Commit spostamento completato), senza problemi.<br/><br/> Se un commit ha esito negativo, lo stato passa a *Commit spostamento non riuscito*.
**Passaggio 6: Eliminare l'origine** | Dopo aver eseguito il commit dello spostamento e aver verificato le risorse nell'area di destinazione, è possibile eliminare la risorsa di origine. | Dopo il commit dello spostamento, lo stato delle risorse passa a *Eliminazione origine in sospeso*.


## <a name="move-region-states"></a>Stati delle aree di spostamento

Il processo di spostamento ha diversi stati con l'indicazione dei problemi che possono verificarsi durante ogni stato. Questi vengono riepilogati nel diagramma di flusso.

![Diagramma di flusso per possibili stati e problemi](./media/about-move-process/process.png)


### <a name="dependency-analysis"></a>analisi delle dipendenze

Man mano che si procede con il processo di spostamento, potrebbe essere richiesto di convalidare le dipendenze se:
- Una risorsa usa risorse dipendenti non incluse nella raccolta di spostamento.
- Una risorsa dipendente nella raccolta di spostamento presenta dipendenze proprie non incluse nella raccolta di spostamento.
- Sono state modificate le impostazioni di destinazione per la risorsa ed è necessario riconvalidare le dipendenze.


### <a name="remove-resources"></a>Rimuovere le risorse

Se non si desidera spostare una risorsa, è possibile rimuoverla dalla raccolta di spostamento. In genere, la risorsa viene quindi eliminata dalla raccolta, insieme a qualsiasi azione o oggetto associato, ad esempio la replica o i modelli archiviati. Cosa accade esattamente quando si rimuove una risorsa dipende dal tipo di risorsa e dallo stato della risorsa al momento dell'eliminazione. [Altre informazioni](remove-move-resources.md) 

## <a name="move-impact"></a>Impatto dello spostamento

La tabella riepiloga gli effetti che si producono con lo spostamento tra aree.

**Comportamento** | **Tra aree**
--- | --- | --- 
**Dati** | Vengono spostati i dati e i metadati delle risorse.<br/><br/> I metadati vengono archiviati temporaneamente per tenere traccia dello stato delle operazioni e delle dipendenze delle risorse.
**Risorsa** | La risorsa di origine rimane intatta per garantire che le app continuino a funzionare e può essere rimossa facoltativamente dopo lo spostamento.<br/><br/> Viene creata una risorsa nell'area di destinazione.
**Processo di spostamento** | Processo in più passaggi che richiede intervento e monitoraggio manuali.
Test | Il test dello spostamento è importante, poiché dopo lo spostamento le app devono continuare a funzionare come previsto nell'area di destinazione.
**Tempo di inattività** |  Non è prevista alcuna perdita di dati, ma è previsto tempo di inattività per lo spostamento delle risorse.



## <a name="next-steps"></a>Passaggi successivi

[Spostare](tutorial-move-region-virtual-machines.md) macchine virtuali di Azure in un'altra area.
[Spostare](tutorial-move-region-sql.md) risorse SQL di Azure in un'altra area.