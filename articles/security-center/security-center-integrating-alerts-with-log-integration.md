---
title: Integrazione degli avvisi del Centro sicurezza di Azure con l&quot;integrazione dei log di Azure (anteprima) | Documentazione Microsoft
description: Questo articolo consente di iniziare a usare gli avvisi del Centro sicurezza con l&quot;integrazione dei log di Azure.
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
ms.date: 11/17/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: f6436e7682c6fd9fdfb1c787a6040f5aa67ac8fa
ms.openlocfilehash: 1d4506b59ebf13c1bdb7cebcc035a0230d79062d


---
# <a name="integrating-azure-security-center-alerts-with-azure-log-integration-preview"></a>Integrazione degli avvisi del Centro sicurezza di Azure con l'integrazione dei log di Azure (anteprima)
Molti team dedicati alle operazioni di sicurezza e alla risposta ai problemi fanno affidamento su una soluzione SIEM (Security Information and Event Management) come punto di partenza per la valutazione e l'analisi degli avvisi di sicurezza. Con l'integrazione dei log di Azure, i clienti possono sincronizzare gli avvisi del Centro sicurezza e gli eventi di sicurezza delle macchine virtuali, raccolti da Diagnostica di Azure e dai log di controllo di Azure, con la propria soluzione SIEM o di analisi dei log quasi in tempo reale.

L'integrazione dei log di Azure è compatibile con HP ArcSight, Splunk, IBM QRadar e altre soluzioni.

## <a name="what-logs-can-i-integrate"></a>Quali log è possibile integrare?
Azure produce registrazioni complete per ogni servizio. I log sono classificati nel modo seguente:

* **Log di gestione/controllo** , che offrono visibilità sulle operazioni CREATE, UPDATE e DELETE di Azure Resource Manager.
* **Log del piano dati** , che offrono visibilità sugli eventi generati durante l'uso di una risorsa di Azure. Un esempio è rappresentato dal registro eventi di Windows: log eventi dell'applicazione e di sicurezza in una macchina virtuale.

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
   > Se si esegue il comando **authorize** subito dopo il comando **createazureid**, potrebbero essere visualizzati avvisi dovuti a una certa latenza tra la creazione dell'account Azure AD e il momento in cui è disponibile per l'uso. Per non visualizzare tali avvisi, attendere circa 10 secondi tra l'esecuzione del comando **createazureid** e l'esecuzione del comando **authorize**.
   >
   >
4. Verificare che nelle cartelle seguenti siano presenti i file JSON del log di controllo:

   * **c:\Users\azlog\AzureResourceManagerJson**
   * **c:\Users\azlog\AzureResourceManagerJsonLD**
5. Verificare che nelle cartelle seguenti siano presenti avvisi del Centro sicurezza di Azure:

   * **c:\Users\azlog\ AzureSecurityCenterJson**
   * **c:\Users\azlog\AzureSecurityCenterJsonLD**
6. Puntare il connettore del server di inoltro del file SIEM standard alla cartella appropriata per reindirizzare i dati all'istanza di SIEM. Per informazioni sulla configurazione SIEM, vedere la pagina relativa alle [configurazioni SIEM](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm).

In caso di domande sull'integrazione dei log di Azure, inviare un messaggio di posta elettronica all'indirizzo [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui log di controllo di Azure e le definizioni delle proprietà, vedere:

* [Operazioni di controllo con Gestione risorse](../resource-group-audit.md)
* [Elencare gli eventi di gestione in una sottoscrizione](https://msdn.microsoft.com/library/azure/dn931934.aspx) : per il recupero degli eventi del log di controllo.

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : informazioni e notizie aggiornate sulla sicurezza di Azure.



<!--HONumber=Nov16_HO3-->


