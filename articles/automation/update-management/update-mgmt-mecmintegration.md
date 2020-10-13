---
title: Integrare Gestione aggiornamenti di automazione di Azure con Microsoft endpoint Configuration Manager
description: Questo articolo descrive come configurare Microsoft Endpoint Configuration Manager con Gestione aggiornamenti per distribuire gli aggiornamenti software ai client di gestione.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 09c8ef818428157c7de8c3a87bcce8a7b80e62ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88245911"
---
# <a name="integrate-update-management-with-microsoft-endpoint-configuration-manager"></a>Integrare Gestione aggiornamenti con Microsoft endpoint Configuration Manager

I clienti che hanno investito in Microsoft Endpoint Configuration Manager per gestire PC, server e dispositivi mobili si affidano alle caratteristiche potenti e avanzate di questa soluzione anche per gestire gli aggiornamenti software come parte del loro ciclo di gestione degli aggiornamenti software.

È possibile segnalare e aggiornare i server Windows gestiti creando e pre-staging di distribuzioni di aggiornamenti software in Microsoft endpoint Configuration Manager e ottenere lo stato dettagliato delle distribuzioni di aggiornamenti completate usando [Gestione aggiornamenti](update-mgmt-overview.md). Se si usa Microsoft endpoint Configuration Manager per la creazione di report di conformità degli aggiornamenti, ma non per la gestione delle distribuzioni di aggiornamenti con i server Windows, è possibile continuare a creare report a Microsoft endpoint Configuration Manager mentre gli aggiornamenti della sicurezza vengono gestiti con Gestione aggiornamenti di automazione di Azure.

>[!NOTE]
>Mentre Gestione aggiornamenti supporta la valutazione degli aggiornamenti e l'applicazione di patch di Windows Server 2008 R2, non supporta i client gestiti da Microsoft endpoint Configuration Manager l'esecuzione di questo sistema operativo.

## <a name="prerequisites"></a>Prerequisiti

* È necessario avere aggiunto [Gestione aggiornamenti di Automazione di Azure](update-mgmt-overview.md) all'account di Automazione.
* I server Windows attualmente gestiti dall'ambiente Microsoft endpoint Configuration Manager devono anche creare report nell'area di lavoro Log Analytics in cui è abilitato anche Gestione aggiornamenti.
* Questa funzionalità è abilitata in Microsoft endpoint Configuration Manager Current Branch versione 1606 e successive. Per integrare il sito di amministrazione centrale Microsoft endpoint Configuration Manager o un sito primario autonomo con i log di monitoraggio di Azure e le raccolte di importazione, vedere [connettere Configuration Manager ai log di monitoraggio di Azure](../../azure-monitor/platform/collect-sccm.md).  
* Gli agenti Windows devono essere configurati per comunicare con un server di Windows Server Update Services (WSUS) o avere accesso a Microsoft Update se non ricevono gli aggiornamenti della sicurezza da Microsoft endpoint Configuration Manager.

La modalità di gestione dei client ospitati in Azure IaaS con l'ambiente Microsoft endpoint Configuration Manager esistente dipende principalmente dalla connessione tra i Data Center di Azure e l'infrastruttura. Questa connessione influisce sulle modifiche di progettazione che possono essere necessarie per l'infrastruttura di Microsoft endpoint Configuration Manager e sui costi correlati per supportare le modifiche necessarie. Per comprendere le considerazioni sulla pianificazione da valutare prima di procedere, vedere [Configuration Manager in Azure: domande frequenti](/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="manage-software-updates-from-microsoft-endpoint-configuration-manager"></a>Gestire gli aggiornamenti software da Microsoft endpoint Configuration Manager

Se si intende continuare a gestire le distribuzioni degli aggiornamenti da Microsoft endpoint Configuration Manager, seguire questa procedura. Automazione di Azure si connette a Microsoft endpoint Configuration Manager per applicare gli aggiornamenti ai computer client connessi all'area di lavoro di Log Analytics. Il contenuto dell'aggiornamento è disponibile dalla cache del computer client come se la distribuzione fosse stata gestita da Microsoft endpoint Configuration Manager.

1. Creare una distribuzione degli aggiornamenti software dal sito di livello superiore nella gerarchia di Microsoft endpoint Configuration Manager usando la procedura descritta in [distribuire gli aggiornamenti software](/configmgr/sum/deploy-use/deploy-software-updates). L'unica impostazione che deve essere configurata in modo diverso rispetto a una distribuzione standard è l'opzione **Non installare aggiornamenti software** per controllare il comportamento di download del pacchetto di distribuzione. Questo comportamento viene gestito in Gestione aggiornamenti creando una distribuzione di aggiornamenti pianificata nel passaggio successivo.

2. Selezionare **Gestione aggiornamenti** in Automazione di Azure. Creare una nuova distribuzione seguendo i passaggi descritti in [creazione di una distribuzione degli aggiornamenti](update-mgmt-deploy-updates.md#schedule-an-update-deployment) e selezionare i **gruppi importati** nell'elenco a discesa **tipo** per selezionare la raccolta di Configuration Manager dell'endpoint Microsoft appropriata. Tenere presente questi punti importanti:

    a. Se è stata definita una finestra di manutenzione per l'endpoint Microsoft selezionato Configuration Manager raccolta dispositivi, i membri della raccolta lo rispettano anziché l'impostazione **durata** definita nella distribuzione pianificata.

    b. I membri della raccolta di destinazione devono avere una connessione Internet diretta, tramite un server proxy o il gateway di Log Analytics.

Dopo aver completato la distribuzione di aggiornamenti tramite Automazione di Azure, i computer di destinazione che sono membri del gruppo di computer selezionato installeranno gli aggiornamenti all'ora pianificata dalla cache client locale. È possibile [visualizzare lo stato della distribuzione di aggiornamenti](update-mgmt-deploy-updates.md#check-deployment-status) per monitorare i risultati della distribuzione.

## <a name="manage-software-updates-from-azure-automation"></a>Gestire gli aggiornamenti software da Automazione di Azure

Per gestire gli aggiornamenti per le macchine virtuali Windows Server che sono client Microsoft endpoint Configuration Manager, è necessario configurare i criteri client per disabilitare la funzionalità Gestione aggiornamenti software per tutti i client gestiti da Gestione aggiornamenti. Per impostazione predefinita, le impostazioni client si applicano a tutti i dispositivi nella gerarchia. Per altre informazioni su questa impostazione dei criteri e su come configurarla, vedere [Come configurare le impostazioni client in Configuration Manager](/configmgr/core/clients/deploy/configure-client-settings).

Dopo aver eseguito questa modifica alla configurazione, creare una nuova distribuzione seguendo i passaggi descritti in [creazione di una distribuzione degli aggiornamenti](update-mgmt-deploy-updates.md#schedule-an-update-deployment) e selezionare i **gruppi importati** nell'elenco a discesa **tipo** per selezionare la raccolta di Configuration Manager dell'endpoint Microsoft appropriata.

## <a name="next-steps"></a>Passaggi successivi

Per configurare una pianificazione dell'integrazione, vedere [Pianificare la distribuzione degli aggiornamenti](update-mgmt-deploy-updates.md#schedule-an-update-deployment).
