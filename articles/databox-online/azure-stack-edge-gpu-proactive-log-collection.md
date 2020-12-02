---
title: Informazioni sulla raccolta di log proattivi nel dispositivo Azure Stack Edge Pro
description: Descrive il modo in cui la raccolta dei log proattivi viene eseguita in un dispositivo Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: alkohli
ms.openlocfilehash: f79de47ec0ffad11f650054b581dbbaae030edbf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466437"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Raccolta di log proattiva sul dispositivo Azure Stack Edge

È possibile abilitare la raccolta di log proattiva sul dispositivo Azure Stack Edge in base agli indicatori di integrità del sistema per risolvere in modo efficiente eventuali problemi del dispositivo. Questo articolo descrive che cos'è la raccolta di log proattiva e come abilitarla e come vengono gestiti i dati quando è abilitata la raccolta di log proattiva.
   
Le informazioni contenute in questo articolo sono valide per Azure Stack GPU Pro Edge, Azure Stack Edge Pro R e i dispositivi Mini R Azure Stack Edge.

## <a name="about-proactive-log-collection"></a>Informazioni sulla raccolta di log proattivi

I team di supporto tecnico clienti Microsoft usano i registri di sistema del dispositivo Azure Stack Edge per identificare e correggere in modo efficiente i problemi che potrebbero verificarsi durante il funzionamento. La raccolta di log proattivo è un metodo che avvisa Microsoft che un problema o un evento (vedere la sezione indicatori di raccolta dei log proattivi per gli eventi rilevati) è stato rilevato dal dispositivo Azure Stack Edge del cliente. I log di supporto relativi al problema vengono caricati automaticamente in un account di archiviazione di Azure gestito e controllato da Microsoft. Supporto tecnico Microsoft e i tecnici Microsoft esaminano i registri di supporto per determinare la migliore linea di azione per risolvere il problema con il cliente.    

> [!NOTE]
> Questi log vengono utilizzati solo a scopo di debug e forniscono supporto ai clienti in caso di problemi. 


## <a name="enabling-proactive-log-collection"></a>Abilitazione della raccolta di log proattiva

È possibile abilitare la raccolta dei log proattivi quando si tenta di attivare il dispositivo tramite l'interfaccia utente locale. 

1. Nell'interfaccia utente Web locale del dispositivo passare alla pagina **Attività iniziali**.
2. Nel riquadro **Attivazione** selezionare **Attiva**. 

    ![Interfaccia utente Web locale "dettagli cloud" pagina 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. Nel riquadro **attiva** :
    1. Immettere la **chiave** di attivazione ottenuta in [ottenere la chiave di attivazione per Azure stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

    1. È possibile abilitare la raccolta di log proattiva per consentire a Microsoft di raccogliere i log in base allo stato di integrità del dispositivo. I log raccolti in questo modo vengono caricati in un account di archiviazione di Azure.
    
    1. Selezionare **Applica**.

    ![Interfaccia utente Web locale: "Dettagli cloud" - Pagina 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)



## <a name="proactive-log-collection-indicators"></a>Indicatori di raccolta log proattivi

Dopo aver abilitato la raccolta di log proattiva, i log vengono caricati automaticamente quando nel dispositivo viene rilevato uno degli eventi seguenti:  


|Avviso/errore/condizione  |Descrizione  |
|---------|---------|
|AcsUnhealthyCondition     |I servizi coerenti di Azure non sono integri.         |
|IOTEdgeAgentNotRunningCondition      |IoT Edge Agent non è in esecuzione.         |
|UpdateInstallFailedEvent | Non è stato possibile installare l'aggiornamento.        |

 
Microsoft continuerà ad aggiungere nuovi eventi all'elenco precedente. Non è necessario alcun consenso aggiuntivo per i nuovi eventi. Fare riferimento a questa pagina per informazioni sugli indicatori di raccolta proattiva di log più aggiornati.    
 

## <a name="other-log-collection-methods"></a>Altri metodi di raccolta di log

Oltre alla raccolta dei log proattiva, che raccoglie log specifici relativi a un problema specifico rilevato, sono disponibili altre raccolte di log che possono fornire una conoscenza più approfondita dell'integrità e del comportamento del sistema. In genere queste altre raccolte di log possono essere eseguite durante una richiesta di supporto o attivate da Microsoft in base ai dati di telemetria forniti dal dispositivo.  

## <a name="handling-data"></a>Gestione dei dati

Se un cliente Abilita la raccolta proattiva dei log, accetta di raccogliere i log dal dispositivo Azure Stack Edge, come descritto nel presente documento. Il cliente inoltre riconosce e acconsente al caricamento e alla conservazione dei log in un account di archiviazione di Azure gestito e controllato da Microsoft.

Microsoft utilizza i dati per la risoluzione dei problemi relativi all'integrità del sistema e solo ai problemi. I dati non vengono utilizzati per il marketing, la pubblicità o altri scopi commerciali senza il consenso del cliente. 

I dati raccolti da Microsoft vengono gestiti in base alle procedure di privacy standard. Se un cliente decide di revocare il consenso per la raccolta dei log proattivi, i dati raccolti con il consenso prima della revoca non saranno interessati.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [raccogliere un pacchetto per il supporto](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).