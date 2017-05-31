---
title: Integrazione log di Azure (AZLOG) con i log di controllo di Active directory | Documentazione Microsoft
description: Informazioni su come installare il servizio Integrazione log di Azure e integrare i log dai log di controllo di Azure
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
ms.date: 05/09/2017
ms.author: barclayn
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5e135be1e21173add3236f851609f1df0a5b0dee
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---

#<a name="integrate-azure-active-directory-audit-logs"></a>Integrare i log di controllo di Azure Active Directory

Gli eventi di controllo di Azure Active Directory aiutano i clienti a identificare le azioni con privilegi che si sono verificate in Azure Active Directory. I tipi di eventi di cui è possibile tenere traccia sono visibili esaminando gli [eventi dei report di controllo di Azure Active Directory](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md)

>[!NOTE]
Prima di eseguire i passaggi descritti in questo articolo, è consigliabile rivedere l'articolo [Introduzione](security-azure-log-integration-get-started.md) e completare tutti i passaggi fino al passaggio 3 della sezione **Passaggi successivi all'installazione e di convalida**.

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Passaggi necessari per integrare i log di controllo di Azure Active Directory

1. Aprire il prompt dei comandi e digitare cd per passare alla directory **C:\Programmi\Integrazione log di Microsoft Azure**
2. Eseguire il comando:

 ``azlog createazureid``

 Questo comando richiede le credenziali di accesso di Azure. Il comando crea quindi un'entità servizio di Azure Active Directory nei tenant di Azure AD che ospitano le sottoscrizioni di Azure in cui l'utente connesso è amministratore, coamministratore o proprietario. Se l'utente connesso è solo un utente Guest nel tenant di Azure AD, il comando avrà esito negativo. L'autenticazione in Azure avviene tramite Azure Active Directory (AD). La creazione di un'entità servizio per l'integrazione dei log di Azure crea l'identità di Azure AD a cui verrà consentito l'accesso in lettura dalle sottoscrizioni di Azure.

3. Eseguire il comando specificando l'ID del tenant. Per eseguire il comando è necessario essere membri del ruolo di amministratore tenant.

``Azlog.exe authorizedirectoryreader tenantId``

Esempio

``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

Verificare che siano stati creati i file JSON del log di controllo di Azure Active Directory nelle cartelle seguenti:

* **C:\Users\azlog\AzureActiveDirectoryJson**
* **C:\Users\azlog\AzureActiveDirectoryJsonLD**

Puntare il connettore del server di inoltro del file SIEM standard alla cartella appropriata per reindirizzare i dati all'istanza di SIEM. A seconda del prodotto SIEM in uso, potrebbe essere necessario il mapping dei campi.

L'assistenza della community è disponibile tramite il [forum MSDN di Integrazione log di Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Il forum offre ai membri della community di AzLog la possibilità di supporto reciproco con domande, risposte, suggerimenti e trucchi su come ottenere il massimo da Integrazione log di Azure. Inoltre, il team di Integrazione log di Azure monitora questo forum e offrirà il suo contributo quando possibile.

Si può anche aprire un [richiesta di supporto](../azure-supportability/how-to-create-azure-support-request.md). A tale scopo selezionare **Integrazione log** come servizio per cui richiedere supporto.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Integrazione log di Azure, vedere i documenti seguenti:

* [Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324) (Integrazione log di Microsoft Azure) - Area download per informazioni dettagliate, requisiti di sistema e istruzioni di installazione per l'integrazione dei log di Azure.
* [Introduzione all'integrazione dei log di Azure](security-azure-log-integration-overview.md) – Questo documento presenta l'integrazione dei log di Azure, le funzionalità chiave e il funzionamento.
* [Passaggi per la configurazione dei partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – Questo post di blog mostra come configurare l'integrazione dei log di Azure per lavorare con le soluzioni partner Splunk, HP ArcSight e IBM QRadar.
* [Domande frequenti sull'integrazione dei log di Azure](security-azure-log-integration-faq.md) - Queste domande frequenti riguardano l'integrazione dei log di Azure.
* [Integrazione degli avvisi del Centro sicurezza con l'integrazione dei log di Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) - Questo documento mostra come sincronizzare gli avvisi del Centro sicurezza di Azure, insieme agli eventi di sicurezza delle macchine virtuali raccolti da Diagnostica di Azure e dai log di controllo di Azure, con la propria soluzione SIEM o di analisi dei log.
* [Nuove funzionalità per Diagnostica di Azure e i log di controllo di Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) - Questo post di blog presenta i log di controllo di Azure e altre funzionalità che consentono di ottenere informazioni dettagliate sulle operazioni delle risorse di Azure.

