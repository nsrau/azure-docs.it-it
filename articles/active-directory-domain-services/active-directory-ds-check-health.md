---
title: "Azure AD Domain Services - Verificare l'integrità del dominio gestito | Microsoft Docs"
description: "Verificare l'integrità del dominio gestito usando la pagina apposita del portale Azure."
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: a9421ace7abf1f3d45b1f8cd810067d79faa92ec
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Verificare l'integrità di un dominio gestito Azure AD Domain Services

## <a name="overview-of-the-health-page"></a>Panoramica della pagina di integrità
Usando la pagina di integrità nel portale di Azure, è possibile restare al corrente su ciò che succede nel dominio gestito. Questo articolo esamina gli elementi della pagina.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>Come visualizzare l'integrità del dominio gestito
1. Passare alla [pagina Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) nel portale di Azure.
2. Fare clic sul dominio di cui si desidera visualizzare l'integrità.
3. Nel riquadro di spostamento a sinistra fare clic su **Integrità**.

L'immagine seguente illustra una pagina di integrità di esempio: ![pagina di integrità di esempio](.\media\active-directory-domain-services-alerts\health-page.png)

>[!NOTE]
> L'integrità del dominio gestito viene valutata ogni ora. Dopo aver apportato modifiche al dominio gestito, attendere il successivo ciclo di valutazione per visualizzare lo stato aggiornato del dominio gestito. Il timestamp "Ultima valutazione" nell'angolo superiore destro mostra a quando risale l'ultima valutazione dell'integrità del dominio gestito.
>

### <a name="status-of-your-managed-domain"></a>Stato del dominio gestito
Lo stato nella parte superiore destra della pagina di integrità indica l'integrità complessiva del dominio gestito. Lo stato entra in gioco in tutti gli avvisi esistenti nel dominio. È anche possibile visualizzare lo stato del dominio nella pagina con la panoramica di Azure AD Domain Services.

| Status | Icona | Spiegazione |
| --- | :----: | --- |
| In esecuzione | <img src= ".\media\active-directory-domain-services-alerts\running-icon.png" width = "15"> | Il dominio gestito è eseguito senza problemi e non presenta avvisi critici o di avvertenza. Questo dominio potrebbe presentare avvisi informativi. |
| Richiede attenzione (avviso di avvertenza) | <img src= ".\media\active-directory-domain-services-alerts\warning-icon.png" width = "15"> | Non sono presenti avvisi critici nel dominio gestito, ma sono presenti uno o più avvisi di avvertenza da risolvere. |
| Richiede attenzione (avviso critico) | <img src= ".\media\active-directory-domain-services-alerts\critical-icon.png" width = "15"> | Sono presenti uno o più avvisi critici nel dominio gestito. Potrebbero esserci anche avvisi di avvertenza e/o informativi. |
| Distribuzione | <img src= ".\media\active-directory-domain-services-alerts\deploying-icon.png" width = "15"> | Il dominio è in fase di distribuzione. |

## <a name="monitors"></a>Monitoraggi
I monitoraggi sono gli aspetti del dominio gestito che Azure AD Domain Services monitora a intervalli regolari. Il modo migliore per mantenerli in uno stato di integrità è risolvere eventuali avvisi attivi per il dominio gestito.

Azure AD Domain Services monitora attualmente:
 - Backup
 - Sincronizzazione con Azure AD

### <a name="the-backup-monitor"></a>Il monitoraggio del backup
Monitora se vengono eseguiti backup regolari del dominio gestito. La tabella seguente spiega cosa aspettarsi nella colonna dei dettagli del monitoraggio del backup:

| Valore dettagli | Spiegazione |
| --- | --- |
|Nessun backup eseguito | Questo stato è normale per un dominio gestito appena creato. In genere, il primo backup viene creato a 24 ore dopo il provisioning del dominio gestito. Se il dominio gestito non è stato appena creato o se lo stato viene visualizzato per un periodo di tempo anomalo, [contattare il supporto](active-directory-ds-contact-us.md). |
| Ultimo backup eseguito da 1 a 14 giorni fa | In generale, si tratta del valore previsto per il monitoraggio del backup. |
| Ultimo backup eseguito da più di 14 giorni | Qualsiasi periodo superiore a due settimane è un tempo insolitamente lungo dall'ultimo backup. Avvisi critici attivi potrebbero impedire al dominio gestito di eseguire il backup a intervalli regolari. Innanzitutto, risolvere gli avvisi attivi per il dominio gestito, poi, se il problema persiste, [contattare il supporto](active-directory-ds-contact-us.md). |


### <a name="the-synchronization-with-azure-ad-monitor"></a>Monitoraggio della sincronizzazione con Azure AD
Microsoft monitora la frequenza di sincronizzazione del dominio gestito con Azure Active Directory. Il numero di oggetti (utenti e gruppi) e il numero di modifiche apportate nella directory di Azure AD rispetto all'ultima sincronizzazione possono entrambi influire sul tempo richiesto dalla sincronizzazione. Se il dominio gestito è stato sincronizzato da più di tre giorni, [contattare il supporto](active-directory-ds-contact-us.md).

## <a name="alerts"></a>Avvisi
Vengono generati avvisi per i problemi nel dominio gestito che devono essere risolti per poter eseguire Azure AD Domain Services. Ogni avviso illustra il problema e offre un URL di risoluzione che descrive i passaggi specifici per risolvere il problema. Per visualizzare tutti gli avvisi e le relative risoluzioni, consultare l'articolo [Risolvere i problemi correlati agli avvisi](active-directory-ds-troubleshoot-alerts.md).

### <a name="alert-severity"></a>Gravità degli avvisi
Gli avvisi sono suddivisi in categorie in base a tre diversi livelli di gravità: critici, di avvertenza e informativi.

 * Gli **avvisi critici** sono problemi che influiscono seriamente sul dominio gestito. Devono essere risolti immediatamente, dal momento che Microsoft non può monitorare, gestire, applicare patch e sincronizzare il dominio gestito. 
 * Gli **avvisi di avvertenza** comunicano la presenza di problemi che in futuro potrebbero influire sul dominio gestito. Offrono consigli per proteggere il dominio gestito.
 * Gli **avvisi informativi** comunicano la presenza di problemi che non influiscono negativamente sul dominio. Sono pensati per informare di ciò che sta succedendo nel dominio e in Azure AD Domain Services.

## <a name="next-steps"></a>Passaggi successivi
- [Risolvere i problemi correlati agli avvisi nel dominio gestito](active-directory-ds-troubleshoot-alerts.md)
- [Altre informazioni su Azure AD Domain Services](active-directory-ds-overview.md)
- [Contattare il team del prodotto](active-directory-ds-contact-us.md)
