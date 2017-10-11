---
title: Integrazione degli avvisi del Centro sicurezza di Azure con l'integrazione dei log di Azure | Microsoft Docs
description: Questo articolo consente di iniziare a usare gli avvisi del Centro sicurezza con l'integrazione dei log di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: d2d088d3-d38d-47ff-a062-c78e0fd59226
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: terrylan
ms.openlocfilehash: d13e5b87c446e587091551b22d80fe568d5d8093
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="integrating-azure-security-center-alerts-with-azure-log-integration"></a>Integrazione degli avvisi del Centro sicurezza di Azure con l'integrazione dei log di Azure
Molti team dedicati alle operazioni di sicurezza e alla risposta ai problemi fanno affidamento su una soluzione SIEM (Security Information and Event Management) come punto di partenza per la valutazione e l'analisi degli avvisi di sicurezza. Con l'integrazione dei log di Azure è possibile integrare gli avvisi del Centro sicurezza di Azure con la soluzione SIEM in uso.

L'integrazione dei log di Azure supporta attualmente HP ArcSight, Splunk e IBM QRadar.

## <a name="what-logs-can-i-integrate"></a>Quali log è possibile integrare?
Azure produce registrazioni complete per ogni servizio. I log sono classificati nel modo seguente:

* **Log di gestione/controllo**, che offrono visibilità sulle operazioni CREATE, UPDATE e DELETE di Azure Resource Manager. Questi eventi del piano di controllo vengono rilevati nei log attività di Azure
* **Log del piano dati**, che offrono visibilità sugli eventi generati durante l'uso di una risorsa di Azure. Un esempio è il registro eventi di Windows, che visualizza informazioni sugli eventi di sicurezza dal canale di sicurezza del Visualizzatore eventi. Gli eventi del piano dati (che vengono generati da una macchina virtuale o un servizio di Azure) vengono replicati dal log di diagnostica di Azure.

L'integrazione dei log di Azure attualmente supporta l'integrazione di:

* Log delle macchine virtuali di Azure
* Log di controllo di Azure
* Avvisi del Centro sicurezza di Azure

## <a name="install-azure-log-integration"></a>Installare l'integrazione dei log di Azure
Scaricare l' [integrazione dei log di Azure](https://www.microsoft.com/download/details.aspx?id=53324).

Il servizio di integrazione dei log di Azure raccoglie i dati di telemetria dal computer in cui è installato.  I dati di telemetria raccolti sono:

* Eccezioni che si verificano durante l'esecuzione dell'integrazione dei log di Azure
* Metriche relative al numero di query e di eventi elaborati
* Statistiche sulle opzioni della riga di comando Azlog.exe che vengono usate

> [!NOTE]
> Per disabilitare la raccolta dei dati di telemetria è possibile deselezionare questa opzione.
>
>

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrare i log di controllo di Azure e gli avvisi del Centro sicurezza di Azure
1. Aprire il prompt dei comandi e digitare **cd** per passare alla directory **c:\Program Files\Microsoft Azure Log Integration**.
2. Eseguire il comando **azlog createazureid** per creare un' [entità servizio di Azure Active Directory](../active-directory/active-directory-application-objects.md) nei tenant di Azure Active Directory (AD) che ospitano le sottoscrizioni di Azure.

    Verrà richiesto l'account di accesso di Azure.

   > [!NOTE]
   > È necessario essere proprietario o coamministratore della sottoscrizione.
   >
   >

    L'autenticazione in Azure avviene tramite Azure AD.  La creazione di un'entità servizio per l'integrazione dei log di Azure crea l'identità di Azure AD a cui verrà consentito l'accesso in lettura dalle sottoscrizioni di Azure.
3. Eseguire il comando **azlog authorize<SubscriptionID>** per assegnare l'accesso in lettura per la sottoscrizione all'entità servizio creata nel passaggio 2. Se non si specifica un **ID sottoscrizione**, all'entità servizio viene assegnato il ruolo Lettore per tutte le sottoscrizioni a cui si ha accesso.

   > [!NOTE]
   > Se si esegue il comando **authorize** subito dopo il comando c**createazureid**, potrebbero essere visualizzati avvisi. Tra il momento in cui viene creato l'account Azure AD e quello in cui l'account è disponibile per l'uso c'è una certa latenza. Per non visualizzare tali avvisi, attendere circa 10 secondi tra l'esecuzione del comando **createazureid** e l'esecuzione del comando **authorize**.
   >
   >
4. Verificare che nelle cartelle seguenti siano presenti i file JSON del log di controllo:

   * **c:\Users\azlog\AzureResourceManagerJson**
   * **c:\Users\azlog\AzureResourceManagerJsonLD**
5. Verificare che nelle cartelle seguenti siano presenti avvisi del Centro sicurezza di Azure:

   * **c:\Users\azlog\ AzureSecurityCenterJson**
   * **c:\Users\azlog\AzureSecurityCenterJsonLD**
6. Configurare il connettore del server d'inoltro file SIEM nella cartella appropriata. La procedura varia in base alla soluzione SIEM in uso.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui log attività di Azure e sulle definizioni delle proprietà, vedere:

* [Operazioni di controllo con Gestione risorse](../azure-resource-manager/resource-group-audit.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : informazioni e notizie aggiornate sulla sicurezza di Azure.
