---
title: Integrazione dei log di Azure con i log di controllo di Azure Active Directory | Microsoft Docs
description: Informazioni su come installare il servizio di integrazione dei log di Azure e integrare i log dai log di controllo di Azure
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 01/14/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 473f9db7eb507fa910657696590bb656a8aff52f
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109878"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrare i log di controllo di Azure Active Directory

Gli eventi di controllo di Azure Active Directory (Azure AD) aiutano i clienti a identificare le azioni con privilegi che si sono verificate in Azure Active Directory. I tipi di eventi di cui è possibile tenere traccia sono visibili esaminando gli [eventi dei report di controllo di Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md).


>[!IMPORTANT]
> La funzionalità di integrazione dei log di Azure verrà dichiarata deprecata entro il 01/06/2019. Il download di AzLog è stato disabilitato il 27 giugno 2018. Per materiale sussidiario su cosa fare dopo, vedere il post [Use Azure monitor to integrate with SIEM tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) (Usare Monitoraggio di Azure per eseguire l'integrazione con gli strumenti per le informazioni di sicurezza e gestione degli eventi) 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Passaggi necessari per integrare i log di controllo di Azure Active Directory

> [!NOTE]
> Prima di eseguire la procedura descritta in questo articolo, è necessario esaminare l'articolo [introduttivo](security-azure-log-integration-get-started.md) e completare i passaggi pertinenti in esso descritti.

1. Aprire il prompt dei comandi ed eseguire questo comando:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Eseguire questo comando: 
 
   ``azlog createazureid``

   Questo comando richiede le credenziali di accesso di Azure. Il comando crea quindi un'entità servizio di Azure Active Directory nei tenant di Azure AD che ospitano le sottoscrizioni di Azure in cui l'utente connesso è amministratore, coamministratore o proprietario. Se l'utente connesso è solo un utente guest nel tenant di Azure AD, il comando avrà esito negativo. L'autenticazione in Azure avviene tramite Azure AD. La creazione di un'entità servizio per l'integrazione dei log di Azure crea l'identità di Azure AD a cui verrà consentito l'accesso in lettura dalle sottoscrizioni di Azure.

3. Eseguire il comando seguente per fornire l'ID del tenant. Per eseguire il comando è necessario essere membri del ruolo di amministratore tenant.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Esempio:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Verificare che siano stati creati i file JSON del log di controllo di Azure Active Directory nelle cartelle seguenti:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

Il video seguente illustra i passaggi descritti in questo articolo:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Per istruzioni specifiche su come includere le informazioni dei file JSON nel sistema di gestione delle informazioni e degli eventi di sicurezza (SIEM), contattare il fornitore SIEM.

L'assistenza della community è disponibile tramite il [forum MSDN di Integrazione log di Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Questo forum consente ai membri della community di aiutarsi reciprocamente con domande, risposte, suggerimenti e trucchi. Inoltre, il team di integrazione dei log di Azure monitora questo forum e offre il suo contributo quando possibile.

Si può anche aprire un [richiesta di supporto](../azure-supportability/how-to-create-azure-support-request.md). Selezionare **Integrazione log** come servizio per cui richiedere il supporto.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'integrazione dei log di Azure, vedere:

* [Integrazione log di Microsoft Azure per i log di Azure](https://www.microsoft.com/download/details.aspx?id=53324): la pagina dell'Area download include informazioni dettagliate, requisiti di sistema e istruzioni di installazione per Integrazione log di Azure.
* [Introduzione a Integrazione log di Azure](security-azure-log-integration-overview.md): Questo articolo presenta Integrazione log di Azure, le funzionalità chiave e il funzionamento.
* [Domande frequenti su Integrazione log di Azure](security-azure-log-integration-faq.md): Questo articolo contiene le risponde alle domande sull'integrazione dei log di Azure.
* [New features for Azure Diagnostics and Azure audit logs (Nuove funzionalità per Diagnostica di Azure e i log di controllo di Azure)](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): questo post del blog presenta i log di controllo di Azure e altre funzionalità che consentono di ottenere informazioni dettagliate sulle operazioni delle risorse di Azure.
