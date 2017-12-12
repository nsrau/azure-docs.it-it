---
title: Gestire gli aggiornamenti usando raccolte SCCM in Gestione aggiornamenti OMS | Microsoft Docs
description: Questo articolo fornisce supporto per la configurazione di System Center Configuration Manager con questa soluzione per gestire gli aggiornamenti dei computer gestiti da SCCM.
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.openlocfilehash: 04540524f83e367f92912171ddc55b6e6f82f80e
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="integrate-system-center-configuration-manager-with-oms-update-management"></a>Integrare System Center Configuration Manager con Gestione aggiornamenti OMS

I clienti che hanno investito in System Center Configuration Manager per gestire PC, server e dispositivi mobili si affidano alle caratteristiche potenti e avanzate di questa soluzione anche per gestire gli aggiornamenti software come parte del loro ciclo di Gestione aggiornamenti software.  

Sfruttando l'integrazione esistente tra OMS e Configuration Manager, è possibile creare report e aggiornare i server Windows gestiti tramite la creazione e il pre-staging di distribuzioni di aggiornamenti software in Configuration Manager, oltre che ottenere lo stato dettagliato delle distribuzioni di aggiornamenti completate usando la [soluzione Gestione aggiornamenti](../operations-management-suite/oms-solution-update-management.md). Se si usa Configuration Manager per creare report di conformità degli aggiornamenti, ma non per la gestione delle distribuzioni di aggiornamenti con i server Windows, è possibile continuare a creare report in Configuration Manager, mentre gli aggiornamenti della sicurezza vengono gestiti con la soluzione Gestione aggiornamenti OMS.

## <a name="prerequisites"></a>Prerequisiti

* È necessario che la [soluzione Gestione aggiornamenti](../operations-management-suite/oms-solution-update-management.md) sia stata aggiunta all'area di lavoro di Log Analytics e collegata con l'account di Automazione nello stesso gruppo di risorse e nella stessa area.   
* I server Windows attualmente gestiti dall'ambiente System Center Configuration Manager devono inoltre fare riferimento all'area di lavoro di Log Analytics in cui è abilitata la soluzione Gestione aggiornamenti.  
* Questa funzionalità supporta System Center Configuration Manager Current Branch versioni 1606 e successive.  Per integrare il sito di amministrazione centrale di Configuration Manager o un sito primario autonomo con Log Analytics e importare le raccolte, vedere [Connettere Configuration Manager a Log Analytics](../log-analytics/log-analytics-sccm.md).  
* Gli agenti Windows devono essere configurati per comunicare con un server Windows Server Update Services (WSUS) o avere accesso a Microsoft Update, se non ricevono gli aggiornamenti della sicurezza da Configuration Manager.   

Le modalità di gestione dei client ospitati nella soluzione IaaS di Azure con l'ambiente Configuration Manager esistente dipendono principalmente dalla connessione tra i data center di Azure e l'infrastruttura. Questa connessione influisce su eventuali modifiche di progettazione che potrebbe essere necessario apportare all'infrastruttura di Configuration Manager e sui costi correlati per supportare le modifiche necessarie.  Per comprendere le considerazioni sulla pianificazione da valutare prima di procedere, vedere [Configuration Manager in Azure: domande frequenti](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure#networking).    

## <a name="configuration"></a>Configurazione

### <a name="manage-software-updates-from-configuration-manager"></a>Gestire gli aggiornamenti software da Configuration Manager 

Se si vuole continuare a gestire le distribuzioni di aggiornamenti da Configuration Manager, eseguire la procedura illustrata di seguito.  OMS si connette a Configuration Manager per applicare gli aggiornamenti ai computer client connessi all'area di lavoro di Log Analytics. Il contenuto degli aggiornamenti è disponibile dalla cache del computer client, come se la distribuzione fosse stata gestita da Configuration Manager.  

1. Creare una distribuzione di aggiornamenti software dal sito principale della gerarchia di Configuration Manager usando il processo descritto in [Distribuire gli aggiornamenti software](https://docs.microsoft.com/en-us/sccm/sum/deploy-use/deploy-software-updates).  L'unica impostazione che deve essere configurata in modo diverso rispetto a una distribuzione standard è l'opzione **Non installare aggiornamenti software** per controllare il comportamento di download del pacchetto di distribuzione. Questo comportamento viene gestito dalla soluzione Gestione aggiornamenti OMS creando una distribuzione di aggiornamenti pianificata nel passaggio successivo.  

1. Nel portale di OMS aprire il dashboard Gestione aggiornamenti.  Creare una nuova distribuzione seguendo i passaggi descritti in [Creazione di una distribuzione degli aggiornamenti](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment) e selezionare la raccolta appropriata di Configuration Manager rappresentata come gruppo di computer OMS nell'elenco a discesa.  Tenere presente questi punti importanti:
    1. Se è definita una finestra di manutenzione per la raccolta di dispositivi di Configuration Manager selezionata, i membri della raccolta rispettano questa finestra invece dell'impostazione **Durata** definita nella distribuzione pianificata in OMS.
    1. I membri della raccolta di destinazione devono avere una connessione Internet (diretta, attraverso un server proxy o attraverso il gateway OMS).  

Dopo aver completato la distribuzione di aggiornamenti con la soluzione OMS, i computer di destinazione che sono membri del gruppo di computer selezionato installeranno gli aggiornamenti all'ora pianificata dalla cache client locale.  È possibile [visualizzare lo stato della distribuzione di aggiornamenti](../operations-management-suite/oms-solution-update-management.md#viewing-update-deployments) per monitorare i risultati della distribuzione.  


### <a name="manage-software-updates-from-oms"></a>Gestire gli aggiornamenti software da OMS

Per gestire gli aggiornamenti per macchine virtuali Windows Server che sono client di Configuration Manager, è necessario configurare i criteri client per disabilitare la funzionalità Gestione aggiornamenti software per tutti i client gestiti da questa soluzione.  Per impostazione predefinita, le impostazioni client si applicano a tutti i dispositivi nella gerarchia.  Per altre informazioni su questa impostazione dei criteri e su come configurarla, vedere [Come configurare le impostazioni client in System Center Configuration Manager](https://docs.microsoft.com/sccm/core/clients/deploy/configure-client-settings).  

Dopo avere apportato questa modifica alla configurazione, creare una nuova distribuzione seguendo i passaggi descritti in [Creazione di una distribuzione degli aggiornamenti](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment) e selezionare la raccolta appropriata di Configuration Manager rappresentata come gruppo di computer OMS nell'elenco a discesa. 

