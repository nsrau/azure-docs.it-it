---
title: Integrare Gestione aggiornamenti di Automazione di Azure con Windows Endpoint Configuration Manager
description: Questo articolo descrive come configurare Microsoft Endpoint Configuration Manager con Gestione aggiornamenti per distribuire gli aggiornamenti software ai client di gestione.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 183189253d11638751e1f8283b202f122131b005
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836312"
---
# <a name="integrate-update-management-with-windows-endpoint-configuration-manager"></a>Integrare Gestione aggiornamenti con Windows Endpoint Configuration Manager

I clienti che hanno investito in Microsoft Endpoint Configuration Manager per gestire PC, server e dispositivi mobili si affidano alle caratteristiche potenti e avanzate di questa soluzione anche per gestire gli aggiornamenti software come parte del loro ciclo di gestione degli aggiornamenti software.

È possibile creare report e aggiornare i server Windows gestiti tramite la creazione e il pre-staging di distribuzioni di aggiornamenti software in Windows Endpoint Configuration Manager, nonché ottenere lo stato dettagliato delle distribuzioni di aggiornamenti completate usando [Gestione aggiornamenti](automation-update-management.md). Se si usa Windows Endpoint Configuration Manager per creare report di conformità degli aggiornamenti, ma non per la gestione delle distribuzioni di aggiornamenti con i server Windows, è possibile continuare a creare report in Configuration Manager, mentre gli aggiornamenti di sicurezza vengono gestiti con Gestione aggiornamenti di Automazione di Azure.

## <a name="prerequisites"></a>Prerequisiti

* È necessario avere aggiunto [Gestione aggiornamenti di Automazione di Azure](automation-update-management.md) all'account di Automazione.
* I server Windows attualmente gestiti dall'ambiente Windows Endpoint Configuration Manager devono inoltre fare riferimento all'area di lavoro Log Analytics in cui è abilitata la soluzione Gestione aggiornamenti.
* Questa funzionalità è abilitata in Windows Endpoint Configuration Manager, versione Current Branch 1606 e successive. Per integrare il sito di amministrazione centrale di Windows Endpoint Configuration Manager o un sito primario autonomo con i log e le raccolte di importazione di Monitoraggio di Azure, vedere [Connettere Configuration Manager ai log di Monitoraggio di Azure](../azure-monitor/platform/collect-sccm.md).  
* Gli agenti Windows devono essere configurati per comunicare con un server Windows Server Update Services (WSUS) o avere accesso a Microsoft Update, se non ricevono gli aggiornamenti della sicurezza da Windows Endpoint Configuration Manager.

Le modalità di gestione dei client ospitati nella soluzione IaaS di Azure con l'ambiente Windows Endpoint Configuration Manager esistente dipendono principalmente dalla connessione tra i data center di Azure e l'infrastruttura. Questa connessione influisce su eventuali modifiche di progettazione che potrebbe essere necessario apportare all'infrastruttura di Windows Endpoint Configuration Manager e sui costi correlati per supportare le modifiche necessarie. Per comprendere le considerazioni sulla pianificazione da valutare prima di procedere, vedere [Configuration Manager in Azure: domande frequenti](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="manage-software-updates-from-windows-endpoint-configuration-manager"></a>Gestire gli aggiornamenti software da Windows Endpoint Configuration Manager

Se si vuole continuare a gestire le distribuzioni di aggiornamenti da Windows Endpoint Configuration Manager, eseguire la procedura illustrata di seguito. Automazione di Azure si connette a Windows Endpoint Configuration Manager per applicare gli aggiornamenti ai computer client connessi all'area di lavoro Log Analytics. Il contenuto degli aggiornamenti è disponibile dalla cache del computer client, come se la distribuzione fosse stata gestita da Windows Endpoint Configuration Manager.

1. Creare una distribuzione di aggiornamenti software dal sito principale della gerarchia di Windows Endpoint Configuration Manager usando il processo descritto in [Distribuire gli aggiornamenti software](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). L'unica impostazione che deve essere configurata in modo diverso rispetto a una distribuzione standard è l'opzione **Non installare aggiornamenti software** per controllare il comportamento di download del pacchetto di distribuzione. Questo comportamento viene gestito in Gestione aggiornamenti creando una distribuzione di aggiornamenti pianificata nel passaggio successivo.

1. Selezionare **Gestione aggiornamenti** in Automazione di Azure. Creare una nuova distribuzione seguendo i passaggi descritti in [Creazione di una distribuzione degli aggiornamenti](automation-tutorial-update-management.md#schedule-an-update-deployment) e selezionare **Gruppi importati** nell'elenco a discesa **Tipo** per selezionare la raccolta appropriata di Windows Endpoint Configuration Manager. Tenere presente questi punti importanti: a. Se è stata definita una finestra di manutenzione per la raccolta di dispositivi di Windows Endpoint Configuration Manager selezionata, i membri della raccolta rispettano questa finestra invece dell'impostazione **Durata** definita nella distribuzione pianificata.
    b. I membri della raccolta di destinazione devono avere una connessione Internet diretta, tramite un server proxy o il gateway di Log Analytics.

Dopo aver completato la distribuzione di aggiornamenti tramite Automazione di Azure, i computer di destinazione che sono membri del gruppo di computer selezionato installeranno gli aggiornamenti all'ora pianificata dalla cache client locale. È possibile [visualizzare lo stato della distribuzione di aggiornamenti](automation-tutorial-update-management.md#view-results-of-an-update-deployment) per monitorare i risultati della distribuzione.

## <a name="manage-software-updates-from-azure-automation"></a>Gestire gli aggiornamenti software da Automazione di Azure

Per gestire gli aggiornamenti per macchine virtuali Windows Server che sono client di Windows Endpoint Configuration Manager, è necessario configurare i criteri client per disabilitare la funzionalità Gestione aggiornamenti software per tutti i client gestiti da Gestione aggiornamenti software. Per impostazione predefinita, le impostazioni client si applicano a tutti i dispositivi nella gerarchia. Per altre informazioni su questa impostazione dei criteri e su come configurarla, vedere [Come configurare le impostazioni client in Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings).

Dopo avere apportato questa modifica alla configurazione, creare una nuova distribuzione seguendo i passaggi descritti in [Creazione di una distribuzione degli aggiornamenti](automation-tutorial-update-management.md#schedule-an-update-deployment) e selezionare **Gruppi importati** nell'elenco a discesa **Tipo** per selezionare la raccolta appropriata di Windows Endpoint Configuration Manager.

## <a name="next-steps"></a>Passaggi successivi

Per configurare una pianificazione dell'integrazione, vedere [Pianificare la distribuzione degli aggiornamenti](automation-tutorial-update-management.md#schedule-an-update-deployment).