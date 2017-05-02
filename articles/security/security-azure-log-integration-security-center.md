---
title: Integrazione log di Azure con il Centro sicurezza | Documentazione Microsoft
description: Informazioni su come far funzionare gli avvisi del Centro sicurezza di Azure con Integrazione log
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
ms.date: 03/22/2017
ms.author: barclayn
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: ba97d1a98b72c5afc98bbdf6c81b0ec19f338e74
ms.lasthandoff: 04/13/2017


---

# <a name="how-to-get-your-security-center-alerts-in-azure-log-integration"></a>Come far funzionare gli avvisi del Centro sicurezza di Azure con l'integrazione dei log
Questo articolo riporta i passaggi necessari per abilitare il servizio Integrazione log di Azure per estrarre informazioni sugli avvisi di sicurezza generati dal Centro sicurezza di Azure. Prima di eseguire i passaggi riportati in questo articolo è necessario avere completato correttamente i passaggi descritti nell'articolo [Introduzione](security-azure-log-integration-get-started.md).

## <a name="detailed-steps"></a>Procedura dettagliata
I passaggi seguenti creeranno l'entità di servizio di Azure Active Directory necessaria e assegneranno le autorizzazioni di lettura dell'entità servizio alla sottoscrizione:
1. Aprire il prompt dei comandi e passare alla directory **C:\Programmi\Integrazione log di Microsoft Azure**
2. Eseguire il comando ``azlog createazureid``

    Questo comando richiede le credenziali di accesso di Azure. Il comando crea quindi un' [entità servizio di Azure Active Directory](../active-directory/develop/active-directory-application-objects.md) nei tenant di Azure AD che ospitano le sottoscrizioni di Azure in cui l'utente connesso è amministratore, coamministratore o proprietario. Se l'utente connesso è solo un utente Guest nel tenant di Azure AD, il comando avrà esito negativo. L'autenticazione in Azure avviene tramite Azure Active Directory (AD). La creazione di un'entità servizio per l'integrazione dei log di Azure crea l'identità di Azure AD a cui verrà consentito l'accesso in lettura dalle sottoscrizioni di Azure.

2. Quindi si eseguirà un comando che assegna l'accesso in lettura per la sottoscrizione all'entità servizio creata nel passaggio 2. Se non si specifica un ID sottoscrizione, il comando tenta di assegnare il ruolo Lettore all'entità servizio per tutte le sottoscrizioni verso cui si dispone di qualsiasi tipo di accesso. </br></br>
``azlog authorize <SubscriptionID>`` </br> ad esempio </br>
``azlog authorize 0ee55555-0bc4-4a32-a4e8-c29980000000``

    >[!NOTE]
    Se si esegue il comando authorize subito dopo il comando createazureid, potrebbero essere visualizzati avvisi. Tra il momento in cui viene creato l'account Azure AD e quello in cui l'account è disponibile per l'uso c'è una certa latenza. Per non visualizzare tali avvisi, attendere circa 60 secondi tra l'esecuzione del comando createazureid e l'esecuzione del comando authorize.

4. Verificare che nelle cartelle seguenti siano presenti i file JSON del log di controllo:
 * **c:\Users\azlog\AzureResourceManagerJson**
 * **c:\Users\azlog\AzureResourceManagerJsonLD** </br></br>
5. Verificare che nelle cartelle seguenti siano presenti avvisi del Centro sicurezza di Azure:</br></br>
 * **c:\Users\azlog\AzureSecurityCenterJson**
 * **c:\Users\azlog\AzureSecurityCenterJsonLD** </br></br>

Se si verificano problemi durante l'installazione e la configurazione, aprire una [richiesta di supporto](/azure-supportability/how-to-create-azure-support-request.md) e selezionare **Integrazione log** come servizio per cui si richiede il supporto.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Integrazione log di Azure, vedere i documenti seguenti:

* [Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324) (Integrazione log di Microsoft Azure) - Area download per informazioni dettagliate, requisiti di sistema e istruzioni di installazione per l'integrazione dei log di Azure.
* [Introduzione all'integrazione dei log di Azure](security-azure-log-integration-overview.md) – Questo documento presenta l'integrazione dei log di Azure, le funzionalità chiave e il funzionamento.
* [Passaggi per la configurazione dei partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – Questo post di blog mostra come configurare l'integrazione dei log di Azure per lavorare con le soluzioni partner Splunk, HP ArcSight e IBM QRadar.
* [Domande frequenti sull'integrazione dei log di Azure](security-azure-log-integration-faq.md) - Queste domande frequenti riguardano l'integrazione dei log di Azure.
* [Integrazione degli avvisi del Centro sicurezza con l'integrazione dei log di Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) - Questo documento mostra come sincronizzare gli avvisi del Centro sicurezza di Azure, insieme agli eventi di sicurezza delle macchine virtuali raccolti da Diagnostica di Azure e dai log di controllo di Azure, con la propria soluzione SIEM o di analisi dei log.
* [Nuove funzionalità per Diagnostica di Azure e i log di controllo di Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) - Questo post di blog presenta i log di controllo di Azure e altre funzionalità che consentono di ottenere informazioni dettagliate sulle operazioni delle risorse di Azure.

