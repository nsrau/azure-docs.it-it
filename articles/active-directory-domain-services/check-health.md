---
title: Controllare l'integrità di Servizi di dominio Azure Active Directory Documenti Microsoft
description: Informazioni su come controllare l'integrità di un dominio gestito di Servizi di dominio Azure Active Directory (Azure AD DS) e comprendere i messaggi di stato tramite il portale di Azure.Learn how to check the health of an Azure Active Directory Domain Services (Azure AD DS) managed domain and understand status messages using the Azure portal.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 27ab14af25704a4f7fb46aa5e86cdaf881c49442
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655646"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Controllare l'integrità di un dominio gestito di Servizi di dominio Azure Active DirectoryCheck the health of an Azure Active Directory Domain Services managed domain

Servizi di dominio Azure Active Directory (Azure AD DS) esegue alcune attività in background per mantenere aggiornato e aggiornato il dominio gestito. Queste attività includono l'elfattoa, l'applicazione di aggiornamenti della sicurezza e la sincronizzazione dei dati da Azure AD. Se si verificano problemi con il dominio gestito di Azure AD DS, queste attività potrebbero non essere completate correttamente. Per esaminare e risolvere eventuali problemi, è possibile controllare lo stato di integrità di un dominio gestito di Servizi di dominio Active Directory di Azure usando il portale di Azure.To review and resolve any issues, you can check the health status of an Azure AD DS managed domain using the Azure portal.

Questo articolo illustra come visualizzare lo stato di integrità di Servizi di dominio Active Directory di Azure e comprendere le informazioni o gli avvisi visualizzati.

## <a name="view-the-health-status"></a>Visualizzare lo stato di integrità

Lo stato di integrità per un dominio gestito di Servizi di dominio Active Directory di Azure viene visualizzato tramite il portale di Azure.The health status for an Azure AD DS managed domain is viewed using the Azure portal. È possibile visualizzare informazioni sull'ora dell'ultimo backup e sulla sincronizzazione con Azure AD, insieme a tutti gli avvisi che indicano un problema con l'integrità del dominio gestito. Per visualizzare lo stato di integrità per un dominio gestito di Servizi di dominio Active Directory di Azure, completare i passaggi seguenti:To view the health status for an Azure AD DS managed domain, complete the following steps:

1. Nel portale di Azure cercare e selezionare Servizi di **dominio Azure AD.**
1. Selezionare il dominio gestito di Servizi di dominio Active Directory di Azure, ad esempio *aaddscontoso.com*.
1. Sul lato sinistro della finestra delle risorse di Servizi di dominio Active Directory di Azure selezionare **Integrità**. La schermata di esempio seguente mostra un dominio gestito di Azure AD DS integro e lo stato dell'ultimo backup e della sincronizzazione di Azure AD:The following example screenshot shows a healthy Azure AD DS managed domain and the status of the last backup and Azure AD synchronization:

    ![Panoramica della pagina Integrità nel portale di Azure che mostra lo stato di Servizi di dominio Azure Active Directory](./media/check-health/health-page.png)

La pagina Ultimo timestamp *valutato* dell'integrità mostra quando è stato controllato l'ultima volta che il dominio gestito di Servizi di dominio Active Directory di Azure è stato controllato. L'integrità di un dominio gestito di Servizi di dominio Active Directory di Azure viene valutata ogni ora. Se si apportano modifiche a un dominio gestito di Servizi di dominio Active Directory di Azure, attendere il ciclo di valutazione successivo per visualizzare lo stato di integrità aggiornato.

Lo stato in alto a destra indica l'integrità complessiva del dominio gestito di Servizi di dominio Active Directory di Azure.The status in the top right indicates the overall health of the Azure AD DS managed domain. Lo stato determina tutti gli avvisi esistenti nel dominio. La tabella seguente descrive in dettaglio gli indicatori di stato disponibili:

| Stato | Icona | Spiegazione |
| --- | :----: | --- |
| In esecuzione | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Il dominio gestito di Servizi di dominio Active Directory di Azure viene eseguito correttamente e non dispone di avvisi critici o di avviso. Il dominio potrebbe avere avvisi informativi. |
| Necessità di attenzione (avviso) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Non sono presenti avvisi critici nel dominio gestito di Servizi di dominio Active Directory di Azure, ma è necessario risolvere uno o più avvisi di avviso. |
| Richiede attenzione (critica) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Nel dominio gestito di Servizi di dominio Active Directory di Azure che devono essere risolti è necessario risolvere uno o più avvisi critici. È inoltre possibile che siano presenti avvisi di avviso e/o avvisi informativi. |
| Distribuzione | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | È in corso la distribuzione del dominio di Azure AD DS.The Azure AD DS domain is being deployed. |

## <a name="understand-monitors-and-alerts"></a>Comprendere i monitoraggi e gli avvisi

Lo stato di integrità per un dominio gestito di Servizi di dominio Active Directory di Azure mostra due tipi di informazioni: *monitora*e *avvisi.* I monitoraggi mostrano il tempo di completamento delle attività in background principali. Gli avvisi forniscono informazioni o suggerimenti per migliorare la stabilità del dominio gestito.

### <a name="monitors"></a>Monitoraggi

I monitoraggi sono aree di un dominio gestito di Servizi di dominio Active Directory di Azure che vengono controllate regolarmente. Se sono presenti avvisi attivi per il dominio gestito di Servizi di dominio Active Directory di Azure, è possibile che uno dei monitoraggi segnali un problema. Servizi di dominio Azure AD dispone attualmente di monitoraggi per le aree seguenti:Azure AD Domain Services currently has monitors for the following areas:

* Backup
* Sincronizzazione con Azure AD

#### <a name="backup-monitor"></a>Monitoraggio backup

Il monitoraggio dei backup verifica la corretta esecuzione dei backup regolari automatici del dominio gestito di Servizi di dominio Active Directory di Azure.The backup monitor checks that automated regular backups of the Azure AD DS managed domain successfully run. Nella tabella seguente viene in dettaglio lo stato disponibile del monitoraggio dei backup:

| Valore dettagli | Spiegazione |
| --- | --- |
| Non è mai stato eseguito il backup | Questo stato è normale per i nuovi domini gestiti di Servizi di dominio Active Directory di Azure.This state is normal for new Azure AD DS managed domains. Il primo backup deve essere creato 24 ore dopo la distribuzione del dominio gestito di Servizi di dominio Active Directory di Azure.The first backup should be created 24 hours after the Azure AD DS managed domain is deployed. Se questo stato persiste, aprire una richiesta di supporto di Azure.If this status persists, [open an Azure support request][azure-support]. |
| Ultimo backup eseguito da 1 a 14 giorni fa | Questo intervallo di tempo è lo stato previsto per il monitoraggio di backup. In questo periodo devono essere eseguiti backup regolari automatici. |
| Ultimo backup eseguito da più di 14 giorni | Un intervallo di tempo superiore a due settimane indica che si è verificato un problema con i backup regolari automatizzati. Gli avvisi critici attivi possono impedire il backup del dominio gestito di Servizi di dominio Active Directory di Azure.Active critical alerts may prevent the Azure AD DS managed domain from being backed up. Risolvere eventuali avvisi attivi per il dominio gestito di Servizi di dominio Active Directory di Azure. Se il monitoraggio di backup non aggiorna quindi lo stato per segnalare un backup recente, aprire una richiesta di supporto di [Azure.][azure-support] |

#### <a name="synchronization-with-azure-ad-monitor"></a>Sincronizzazione con il monitoraggio di Azure ADSynchronization with Azure AD monitor

Un dominio gestito di Azure AD DS viene sincronizzato regolarmente con Azure Active Directory.An Azure AD DS managed domain regularly synchronizes with Azure Active Directory. Il numero di utenti e oggetti gruppo e il numero di modifiche apportate nella directory di Azure AD dall'ultima sincronizzazione influiscono sul tempo necessario per la sincronizzazione. Se l'ultima sincronizzazione del dominio gestito di Servizi di dominio Active Directory di Azure è stata eseguita tre giorni fa, verificare e risolvere eventuali avvisi attivi. Se il monitoraggio della sincronizzazione non aggiorna lo stato per visualizzare una sincronizzazione recente dopo aver risolto gli avvisi attivi, aprire una richiesta di supporto di [Azure.][azure-support]

### <a name="alerts"></a>Avvisi

Gli avvisi vengono generati per i problemi in un dominio gestito di Azure AD DS che devono essere risolti per la corretta esecuzione del servizio. Ogni avviso spiega il problema e fornisce un URL che descrive i passaggi specifici per risolvere il problema. Per ulteriori informazioni sui possibili avvisi e sulle relative risoluzioni, vedere [Risoluzione dei problemi relativi agli avvisi](troubleshoot-alerts.md).

Gli avvisi relativi allo stato di integrità vengono classificati nei livelli di gravità seguenti:Health status alerts are categorized into the following levels of severity:

 * **Gli avvisi critici** sono problemi che influiscono gravemente sul dominio gestito di Servizi di dominio Active Directory di Azure.Critical alerts are issues that severely impact the Azure AD DS managed domain. Questi avvisi devono essere risolti immediatamente. La piattaforma Azure non può monitorare, gestire, applicare patch e sincronizzare il dominio gestito finché i problemi non vengono risolti.
 * **Gli avvisi** di avviso segnalano problemi che possono influire sulle operazioni del dominio gestito di Servizi di dominio Active Directory di Azure se il problema persiste. Questi avvisi offrono anche consigli per proteggere il dominio gestito.
 * **Gli avvisi informativi** sono notifiche che non influiscono negativamente sul dominio gestito di Servizi di dominio Active Directory di Azure.Informational alerts are notifications that don't negatively impact the Azure AD DS managed domain. Gli avvisi informativi forniscono alcune informazioni su ciò che accade nel dominio gestito.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli avvisi visualizzati nella pagina dello stato di integrità, vedere [Risolvere gli avvisi nel dominio gestitoFor][troubleshoot-alerts] more information on alerts that are shown in the health status page, see Resolve alerts on your managed domain

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
