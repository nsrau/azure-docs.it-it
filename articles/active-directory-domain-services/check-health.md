---
title: Verificare lo stato di Azure Active Directory Domain Services | Microsoft Docs
description: Informazioni su come verificare l'integrità di un dominio gestito di Azure Active Directory Domain Services (Azure AD DS) e conoscere i messaggi di stato tramite il portale di Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: iainfou
ms.openlocfilehash: 50b142acb457d16abeb24f22d56b653a38aca76d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898259"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Verificare l'integrità di un dominio gestito Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) esegue alcune attività in background per rendere il dominio gestito integro e aggiornato. Queste attività includono l'esecuzione di backup, l'applicazione di aggiornamenti della sicurezza e la sincronizzazione dei dati da Azure AD. Se si verificano problemi con il dominio gestito Azure AD DS, le attività potrebbero non essere eseguite correttamente. Per esaminare e risolvere eventuali problemi, è possibile verificare lo stato di integrità di un dominio gestito Azure AD DS usando il portale di Azure.

Questo articolo illustra come visualizzare lo stato di integrità di Azure AD DS e comprendere le informazioni o gli avvisi visualizzati.

## <a name="view-the-health-status"></a>Visualizzare lo stato di integrità

Lo stato di integrità di un dominio gestito Azure AD DS viene visualizzato utilizzando l'portale di Azure. È possibile visualizzare le informazioni relative all'ora dell'ultimo backup e alla sincronizzazione con Azure AD insieme a tutti gli avvisi che indicano un problema relativo all'integrità del dominio gestito. Per visualizzare lo stato di integrità di un dominio gestito di Azure AD DS, completare i passaggi seguenti:

1. Nella portale di Azure cercare e selezionare **Azure ad Domain Services**.
1. Selezionare il dominio gestito di Azure AD DS, ad esempio *contoso.com*.
1. Sul lato sinistro della finestra Azure AD DS Resource selezionare **Health (integrità**). La schermata di esempio seguente mostra un dominio gestito Azure AD DS integro e lo stato dell'ultimo backup e della sincronizzazione Azure AD:

    ![Panoramica della pagina relativa allo stato di integrità nella portale di Azure che mostra lo stato Azure Active Directory Domain Services](./media/check-health/health-page.png)

L' *ultimo timestamp valutato* della pagina di integrità indica quando è stato eseguito l'ultimo controllo del dominio gestito di Azure AD DS. L'integrità di un dominio gestito di Azure AD DS viene valutata ogni ora. Se si apportano modifiche a un dominio gestito di Azure AD DS, attendere il successivo ciclo di valutazione per visualizzare lo stato di integrità aggiornato.

Lo stato in alto a destra indica l'integrità complessiva del dominio gestito Azure AD DS. Lo stato determina tutti gli avvisi esistenti nel dominio. Nella tabella seguente vengono illustrati gli indicatori di stato disponibili:

| Stato | Icona | Spiegazione |
| --- | :----: | --- |
| In esecuzione | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Il dominio gestito Azure AD DS viene eseguito correttamente e non contiene avvisi critici o di avviso. Per il dominio possono essere presenti avvisi informativi. |
| Richiede attenzione (avviso) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Non sono presenti avvisi critici nel dominio gestito Azure AD DS, ma è necessario risolvere uno o più avvisi di avviso. |
| Richiede attenzione (critico) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Sono presenti uno o più avvisi critici sul dominio gestito di Azure AD DS che devono essere risolti. È anche possibile che siano presenti avvisi informativi o di avviso. |
| Distribuzione | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | È in corso la distribuzione del dominio Azure AD DS. |

## <a name="understand-monitors-and-alerts"></a>Informazioni sui monitoraggi e sugli avvisi

Lo stato di integrità di un dominio gestito Azure AD DS Mostra due tipi di monitoraggi informazioni e avvisi. I monitoraggi indicano il tempo di completamento delle attività in background di base. Gli avvisi forniscono informazioni o suggerimenti per migliorare la stabilità del dominio gestito.

### <a name="monitors"></a>Monitoraggi

I monitoraggi sono aree di un dominio gestito Azure AD DS che vengono controllate regolarmente. Se sono presenti avvisi attivi per il dominio gestito Azure AD DS, è possibile che uno dei monitoraggi segnali un problema. Azure AD Domain Services monitora attualmente le aree seguenti:

* Backup
* Sincronizzazione con Azure AD

#### <a name="backup-monitor"></a>Monitoraggio backup

Il monitoraggio backup controlla che i backup regolari automatici del dominio gestito Azure AD DS siano stati eseguiti correttamente. La tabella seguente illustra in dettaglio lo stato di monitoraggio backup disponibile:

| Valore dettagli | Spiegazione |
| --- | --- |
| Mai eseguito il backup | Questo stato è normale per i nuovi domini gestiti di Azure AD DS. Il primo backup deve essere creato 24 ore dopo la distribuzione del dominio gestito di Azure AD DS. Se lo stato è permanente, [aprire una richiesta di supporto di Azure][azure-support]. |
| Ultimo backup eseguito da 1 a 14 giorni fa | Questo intervallo di tempo è lo stato previsto per il monitor di backup. I backup regolari automatici devono essere eseguiti in questo periodo. |
| Ultimo backup eseguito da più di 14 giorni | Un intervallo di tempo più lungo di due settimane indica che si è verificato un problema con i backup regolari automatici. Gli avvisi critici attivi possono impedire il backup del dominio gestito Azure AD DS. Risolvere tutti gli avvisi attivi per il dominio gestito Azure AD DS. Se il monitoraggio di backup non aggiorna lo stato per segnalare un backup recente, [aprire una richiesta di supporto di Azure][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Sincronizzazione con monitoraggio Azure AD

Un dominio gestito Azure AD DS si sincronizza regolarmente con Azure Active Directory. Il numero di utenti e oggetti gruppo e il numero di modifiche apportate nella directory Azure AD dall'ultima sincronizzazione influiscono sul tempo necessario per la sincronizzazione. Se il dominio gestito di Azure AD DS è stato sincronizzato l'ultima volta per tre giorni fa, verificare e risolvere eventuali avvisi attivi. Se il monitoraggio della sincronizzazione non aggiorna lo stato per visualizzare una sincronizzazione recente, [aprire una richiesta di supporto di Azure][azure-support].

### <a name="alerts"></a>Avvisi

Gli avvisi vengono generati per problemi in un dominio gestito Azure AD DS che devono essere risolti affinché il servizio venga eseguito correttamente. Ogni avviso illustra il problema e fornisce un URL che descrive i passaggi specifici per la risoluzione del problema. Per ulteriori informazioni sui possibili avvisi e sulle relative risoluzioni, vedere [risoluzione dei problemi relativi agli avvisi](troubleshoot-alerts.md).

Gli avvisi sullo stato di integrità sono suddivisi in categorie nei livelli di gravità seguenti:

 * Gli **avvisi critici** sono problemi che incidono gravemente sul dominio gestito di Azure AD DS. Questi avvisi devono essere risolti immediatamente. La piattaforma Azure non può monitorare, gestire, applicare patch e sincronizzare il dominio gestito fino a quando i problemi non vengono risolti.
 * Gli **avvisi di avviso** segnalano i problemi che potrebbero influito sulle operazioni del dominio gestito Azure AD DS se il problema persiste. Questi avvisi offrono anche consigli per proteggere il dominio gestito.
 * Gli **avvisi informativi** sono notifiche che non influiscono negativamente sul dominio gestito di Azure AD DS. Gli avvisi informativi forniscono informazioni su ciò che accade nel dominio gestito.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sugli avvisi visualizzati nella pagina stato integrità, vedere [risolvere gli avvisi nel dominio gestito][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
