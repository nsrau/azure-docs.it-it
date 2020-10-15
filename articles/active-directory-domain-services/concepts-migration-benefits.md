---
title: Vantaggi della migrazione della distribuzione classica in Azure AD Domain Services | Microsoft Docs
description: Scopri di più sui vantaggi della migrazione di una distribuzione classica di Azure Active Directory Domain Services al modello di distribuzione Gestione risorse
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: joflore
ms.openlocfilehash: 35224e6c9a28b1555303c1ae11b6700c1bf419bf
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962497"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>Vantaggi della migrazione dal modello di distribuzione classica a Gestione risorse in Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) consente di eseguire la migrazione di un dominio gestito esistente che usa il modello di distribuzione classica al modello di distribuzione di Gestione risorse. Azure AD domini gestiti di DS che usano il modello di distribuzione Gestione risorse forniscono funzionalità aggiuntive, ad esempio criteri granulari per le password, log di controllo e protezione del blocco degli account.

Questo articolo illustra i vantaggi per la migrazione. Per iniziare, vedere [eseguire la migrazione Azure ad Domain Services dal modello di rete virtuale classica a gestione risorse][howto-migrate].

> [!NOTE]
> In 2017 Azure AD Domain Services è diventato disponibile per ospitare in una rete Azure Resource Manager. Da allora, siamo riusciti a creare un servizio più sicuro usando le funzionalità moderne del Azure Resource Manager. Poiché le distribuzioni di Azure Resource Manager sostituiscono completamente le distribuzioni classiche, le distribuzioni di rete virtuale di Azure AD DS classico verranno ritirate il 1 ° marzo 2023.
>
> Per ulteriori informazioni, vedere l' [avviso ufficiale di deprecazione](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="migration-benefits"></a>Vantaggi della migrazione

Il processo di migrazione accetta un dominio gestito esistente che usa il modello di distribuzione classica e si sposta per usare il modello di distribuzione Gestione risorse. Quando si esegue la migrazione di un dominio gestito dal modello di distribuzione classica a Gestione risorse, si evita la necessità di aggiungere di nuovo i computer al dominio gestito o eliminare il dominio gestito e crearne uno da zero. Le macchine virtuali continuano a essere unite al dominio gestito al termine del processo di migrazione.

Al termine della migrazione, Azure AD DS fornisce molte funzionalità disponibili solo per i domini che usano il modello di distribuzione Gestione risorse, come nel seguente esempio:

* [Supporto per i criteri granulari][password-policy]per le password.
* Velocità di sincronizzazione più veloci tra Azure AD e Azure AD Domain Services.
* Due nuovi [attributi che vengono sincronizzati da Azure ad][attributes]  -  *Manager* e da *EmployeeID*.
* Accesso a controller di dominio con una maggiore velocità quando si [Aggiorna lo SKU][skus].
* Protezione del blocco dell'account Active Directory.
* [Notifiche tramite posta elettronica per gli avvisi nel dominio gestito][email-alerts].
* [Usare le cartelle di lavoro di Azure e monitoraggio di Azure per visualizzare i log di controllo e le attività di accesso][workbooks].
* In aree supportate [zone di disponibilità di Azure][availability-zones].
* Integrazioni con altri prodotti di Azure, ad esempio [file di Azure][azure-files], [HD Insights][hd-insights]e [desktop virtuale di Windows][wvd].
* Il supporto ha accesso a una maggiore telemetria e consente di risolvere i problemi in modo più efficace.
* Crittografia dei dati inattivi con [Azure Managed disks][managed-disks] per i dati nei controller di dominio gestiti.

I domini gestiti che usano un modello di distribuzione Gestione risorse consentono di rimanere sempre aggiornati sulle nuove funzionalità più recenti. Le nuove funzionalità non sono disponibili per i domini gestiti che usano il modello di distribuzione classica.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere [eseguire la migrazione Azure ad Domain Services dal modello di rete virtuale classica a gestione risorse][howto-migrate].

<!-- LINKS - INTERNAL -->
[password-policy]: password-policy.md
[skus]: change-sku.md
[email-alerts]: notifications.md
[workbooks]: use-azure-monitor-workbooks.md
[azure-files]: ../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md
[hd-insights]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[wvd]: ../virtual-desktop/overview.md
[availability-zones]: ../availability-zones/az-overview.md
[howto-migrate]: migrate-from-classic-vnet.md
[attributes]: synchronization.md#attribute-synchronization-and-mapping-to-azure-ad-ds
[managed-disks]: ../virtual-machines/managed-disks-overview.md
