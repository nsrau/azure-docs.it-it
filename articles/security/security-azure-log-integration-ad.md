---
title: Integrazione dei log di Azure con i log di controllo di Azure Active Directory | Microsoft Docs
description: Informazioni su come installare il servizio di integrazione dei log di Azure e integrare i log dai log di controllo di Azure
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/08/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 8a1295cc86057ed72940e774d0bd423d61142e31
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrare i log di controllo di Azure Active Directory

Gli eventi di controllo di Azure Active Directory (Azure AD) aiutano i clienti a identificare le azioni con privilegi che si sono verificate in Azure Active Directory. I tipi di eventi di cui è possibile tenere traccia sono visibili esaminando gli [eventi dei report di controllo di Azure Active Directory](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md).

> [!NOTE]
> Prima di eseguire la procedura descritta in questo articolo, è necessario esaminare l'articolo [introduttivo](security-azure-log-integration-get-started.md) e completare la procedura in esso descritta.

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Passaggi necessari per integrare i log di controllo di Azure Active Directory

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

* [Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324) (Integrazione log di Microsoft Azure): pagina di Area download con informazioni dettagliate, requisiti di sistema e istruzioni di installazione per l'integrazione dei log di Azure.
* [Introduzione all'integrazione dei log di Azure](security-azure-log-integration-overview.md): questo articolo presenta l'integrazione dei log di Azure, le funzionalità chiave e il funzionamento.
* [Partner configuration steps](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) (Passaggi per la configurazione dei partner): questo post di blog mostra come configurare l'integrazione dei log di Azure per lavorare con le soluzioni partner Splunk, HP ArcSight e IBM QRadar.
* [Domande frequenti sull'integrazione dei log di Azure](security-azure-log-integration-faq.md): questo articolo contiene le risponde alle domande sull'integrazione dei log di Azure.
* [Integrazione degli avvisi del Centro sicurezza con l'integrazione dei log di Azure](../security-center/security-center-integrating-alerts-with-log-integration.md): questo articolo mostra come sincronizzare gli avvisi del Centro sicurezza di Azure, insieme agli eventi di sicurezza delle macchine virtuali raccolti da Diagnostica di Azure e dai log di controllo di Azure, con la propria soluzione SIEM o di analisi dei log.
* [New features for Azure Diagnostics and Azure audit logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) (Nuove funzionalità per Diagnostica di Azure e i log di controllo di Azure): questo post di blog presenta i log di controllo di Azure e altre funzionalità che consentono di ottenere informazioni dettagliate sulle operazioni delle risorse di Azure.
