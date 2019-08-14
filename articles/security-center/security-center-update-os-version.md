---
title: Aggiornare la versione del sistema operativo nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo articolo illustra come implementare la raccomandazione **Aggiorna la versione del sistema operativo** del Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: a17bc3cf8c8f1ffbb2a91e17944bfc1c203f439d
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "60905861"
---
# <a name="update-os-version-in-azure-security-center"></a>Aggiornare la versione del sistema operativo nel Centro sicurezza di Azure
Per le macchine virtuali nei servizi cloud il Centro sicurezza di Azure consiglierà l'aggiornamento del sistema operativo se è disponibile una versione più recente.  Vengono monitorati solo i ruoli Web e di lavoro dei servizi cloud in esecuzione negli slot di produzione.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questa non è una guida dettagliata.
> 
> 

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
1. Nel pannello **Raccomandazioni** selezionare **Aggiorna la versione del sistema operativo**.
   ![Aggiornare la versione sistema operativo][1]
2. Viene visualizzato il pannello **Aggiorna la versione del sistema operativo**. Seguire i passaggi in questo pannello per aggiornare la versione del sistema operativo.

## <a name="see-also"></a>Vedere anche
Questo documento illustra come implementare la raccomandazione "Aggiorna la versione del sistema operativo" del Centro sicurezza. Per altre informazioni sui servizi cloud e sull'aggiornamento della versione del sistema operativo per un servizio cloud, vedere:

* [Perché scegliere Servizi cloud](../cloud-services/cloud-services-choose-me.md)
* [Come aggiornare un servizio cloud](../cloud-services/cloud-services-update-azure-service.md)
* [Come configurare i servizi cloud](../cloud-services/cloud-services-how-to-configure-portal.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/) : informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
