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
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 34f4ecfd4cc3432babbd26d65bd37ea4d1fb882c
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86040419"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Verificare l'integrità di un dominio gestito Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) esegue alcune attività in background per rendere il dominio gestito integro e aggiornato. Queste attività includono l'esecuzione di backup, l'applicazione di aggiornamenti della sicurezza e la sincronizzazione dei dati da Azure AD. Se si verificano problemi con il dominio gestito Azure AD DS, le attività potrebbero non essere completate correttamente. Per esaminare e risolvere eventuali problemi, è possibile verificare lo stato di integrità di un dominio gestito utilizzando il portale di Azure.

Questo articolo illustra come visualizzare lo stato di integrità di Azure AD DS e comprendere le informazioni o gli avvisi visualizzati.

## <a name="view-the-health-status"></a>Visualizzare lo stato di integrità

Lo stato di integrità di un dominio gestito viene visualizzato utilizzando il portale di Azure. È possibile visualizzare le informazioni relative all'ora dell'ultimo backup e alla sincronizzazione con Azure AD insieme a tutti gli avvisi che indicano un problema relativo all'integrità del dominio gestito. Per visualizzare lo stato di integrità di un dominio gestito, attenersi alla procedura seguente:

1. Nella portale di Azure cercare e selezionare **Azure ad Domain Services**.
1. Selezionare il dominio gestito, ad esempio *aaddscontoso.com*.
1. Sul lato sinistro della finestra Azure AD DS Resource selezionare **Health (integrità**). La schermata di esempio seguente mostra un dominio gestito integro e lo stato dell'ultimo backup e Azure AD sincronizzazione:

    ![Panoramica della pagina relativa allo stato di integrità nella portale di Azure che mostra lo stato Azure Active Directory Domain Services](./media/check-health/health-page.png)

L' *ultimo timestamp valutato* della pagina di integrità indica quando è stato eseguito l'ultimo controllo del dominio gestito. L'integrità di un dominio gestito viene valutata ogni ora. Se si apportano modifiche a un dominio gestito, attendere il successivo ciclo di valutazione per visualizzare lo stato di integrità aggiornato.

Lo stato in alto a destra indica l'integrità complessiva del dominio gestito. Lo stato determina tutti gli avvisi esistenti nel dominio. Nella tabella seguente vengono illustrati gli indicatori di stato disponibili:

| Stato | Icona | Spiegazione |
| --- | :----: | --- |
| In esecuzione | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Il dominio gestito viene eseguito correttamente e non contiene avvisi critici o di avviso. Per il dominio possono essere presenti avvisi informativi. |
| Richiede attenzione (avviso) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Non sono presenti avvisi critici nel dominio gestito, ma è necessario risolvere uno o più avvisi di avviso. |
| Richiede attenzione (critico) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Sono presenti uno o più avvisi critici nel dominio gestito che devono essere risolti. È anche possibile che siano presenti avvisi informativi o di avviso. |
| Distribuzione | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | È in corso la distribuzione del dominio gestito. |

## <a name="understand-monitors-and-alerts"></a>Informazioni sui monitoraggi e sugli avvisi

Lo stato di integrità di un dominio gestito Mostra due tipi di informazioni, *monitoraggi*e *avvisi*. I monitoraggi indicano il tempo di completamento delle attività in background di base. Gli avvisi forniscono informazioni o suggerimenti per migliorare la stabilità del dominio gestito.

### <a name="monitors"></a>Monitoraggi

I monitoraggi sono aree di un dominio gestito che vengono controllate regolarmente. Se sono presenti avvisi attivi per il dominio gestito, è possibile che uno dei monitoraggi segnali un problema. In Azure AD DS sono attualmente disponibili monitoraggi per le aree seguenti:

* Backup
* Sincronizzazione con Azure AD

#### <a name="backup-monitor"></a>Monitoraggio backup

Il monitoraggio backup controlla che i backup regolari automatici del dominio gestito siano stati eseguiti correttamente. La tabella seguente illustra in dettaglio lo stato di monitoraggio backup disponibile:

| Valore dettagli | Spiegazione |
| --- | --- |
| Mai eseguito il backup | Questo stato è normale per i nuovi domini gestiti. Il primo backup deve essere creato 24 ore dopo la distribuzione del dominio gestito. Se lo stato è permanente, [aprire una richiesta di supporto di Azure][azure-support]. |
| Ultimo backup eseguito da 1 a 14 giorni fa | Questo intervallo di tempo è lo stato previsto per il monitor di backup. I backup regolari automatici devono essere eseguiti in questo periodo. |
| Ultimo backup eseguito da più di 14 giorni | Un intervallo di tempo più lungo di due settimane indica che si è verificato un problema con i backup regolari automatici. Gli avvisi critici attivi possono impedire il backup del dominio gestito. Risolvere tutti gli avvisi attivi per il dominio gestito. Se il monitoraggio di backup non aggiorna lo stato per segnalare un backup recente, [aprire una richiesta di supporto di Azure][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Sincronizzazione con monitoraggio Azure AD

Un dominio gestito si sincronizza regolarmente con Azure Active Directory. Il numero di utenti e oggetti gruppo e il numero di modifiche apportate nella directory Azure AD dall'ultima sincronizzazione influiscono sul tempo necessario per la sincronizzazione. Se il dominio gestito è stato sincronizzato per l'ultima volta per tre giorni fa, controllare e risolvere eventuali avvisi attivi. Se il monitoraggio della sincronizzazione non aggiorna lo stato per visualizzare una sincronizzazione recente dopo aver indirizzato gli avvisi attivi, [aprire una richiesta di supporto tecnico di Azure][azure-support].

### <a name="alerts"></a>Avvisi

Gli avvisi vengono generati per i problemi in un dominio gestito che devono essere risolti affinché il servizio venga eseguito correttamente. Ogni avviso illustra il problema e fornisce un URL che descrive i passaggi specifici per la risoluzione del problema. Per ulteriori informazioni sui possibili avvisi e sulle relative risoluzioni, vedere [risoluzione dei problemi relativi agli avvisi](troubleshoot-alerts.md).

Gli avvisi sullo stato di integrità sono suddivisi in categorie nei livelli di gravità seguenti:

 * Gli **avvisi critici** sono problemi che incidono gravemente sul dominio gestito. Questi avvisi devono essere risolti immediatamente. La piattaforma Azure non può monitorare, gestire, applicare patch e sincronizzare il dominio gestito fino a quando i problemi non vengono risolti.
 * Gli **avvisi di avviso** segnalano i problemi che potrebbero influisca sulle operazioni del dominio gestito se il problema persiste. Questi avvisi offrono anche consigli per proteggere il dominio gestito.
 * Gli **avvisi informativi** sono notifiche che non influiscono negativamente sul dominio gestito. Gli avvisi informativi forniscono informazioni su ciò che accade nel dominio gestito.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sugli avvisi visualizzati nella pagina stato integrità, vedere [risolvere gli avvisi nel dominio gestito][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
