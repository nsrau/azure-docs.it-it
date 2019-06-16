---
title: Customer Lockbox per Microsoft Azure
description: Panoramica tecnica su Customer Lockbox per Microsoft Azure, che consente di controllare l'accesso del provider di cloud quando Microsoft potrebbe dover accedere ai dati dei clienti.
author: cabailey
ms.service: security
ms.topic: article
ms.author: cabailey
manager: barbkess
ms.date: 05/07/2019
ms.openlocfilehash: 468e392cd2c45d79cbb24f8d737a6e83fbcd2725
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65079271"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Customer Lockbox per Microsoft Azure

> [!NOTE]
> Per usare questa funzionalità, l'organizzazione deve avere un [piano di supporto tecnico di Azure](https://azure.microsoft.com/support/plans/) con un livello minimo di **Developer**.

Customer Lockbox per Microsoft Azure fornisce un'interfaccia per i clienti esaminare e approvare o rifiutare le richieste di accesso dei dati dei clienti. Viene usato nei casi in cui deve accedere ai dati dei clienti durante una richiesta di supporto tecnico Microsoft.

Questo articolo illustra come le richieste a Customer Lockbox vengono avviate, rilevate e archiviate per controlli e le verifiche successive.

Customer Lockbox è ora disponibile a livello generale ed è attualmente abilitato per l'accesso desktop remoto alle macchine virtuali.

## <a name="workflow"></a>Flusso di lavoro

I passaggi seguenti illustrano un flusso di lavoro tipico per una richiesta a Customer Lockbox.

1. Un utente in un'organizzazione ha un problema con il carico di lavoro di Azure.

2. Dopo questa persona consente di risolvere il problema, ma non è possibile risolverlo, aprono un ticket di supporto di [portale di Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). Il ticket viene assegnato a un tecnico di supporto clienti di Azure.

3. Un tecnico del supporto Azure esamina la richiesta di servizio e determina i passaggi successivi per risolvere il problema.

4. Se il tecnico del supporto non è possibile risolvere il problema usando i dati di telemetria e gli strumenti standard, il passaggio successivo consiste nel richiedere autorizzazioni elevate usando un servizio di accesso JIT (JIT). Questa richiesta può essere dal tecnico del supporto originale. In alternativa, può essere da un tecnico diversi perché il problema viene eseguita l'escalation al team di DevOps di Azure.

5. Dopo l'accesso viene inoltrata una richiesta per il tecnico di Azure, Just-In-Time viene confrontata la richiesta tenendo conto di altri fattori, ad esempio:
    - L'ambito della risorsa
    - Se il richiedente è un'identità di tipo isolata o multi-factor Authentication
    - Livelli di autorizzazione
    
    In base alla regola JIT, la richiesta può includere anche un'approvazione da responsabili approvazione di Microsoft interno. Il responsabile approvazione, ad esempio, potrebbe essere il responsabile del supporto dei clienti o il responsabile reparto DevOps.

6. Quando la richiesta richiede l'accesso diretto ai dati del cliente, viene avviata una richiesta a Customer Lockbox. Ad esempio, l'accesso desktop remoto alla macchina virtuale del cliente.
    
    La richiesta è ora in un **cliente una notifica** stato, in attesa di approvazione del cliente prima di concedere l'accesso.

7. All'organizzazione del cliente, l'utente che ha il [ruolo di proprietario](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) per Azure sottoscrizione riceve un messaggio di posta elettronica da Microsoft, per informarli riguardo la richiesta di accesso in sospeso. Per le richieste a Customer Lockbox, questa persona è il responsabile dell'approvazione designato.
    
    Posta elettronica di esempio:
    
    ![Azure Customer Lockbox - notifica di posta elettronica](./media/azure-customer-lockbox/customer-lockbox-email-notification.png)

8. Notifica di posta elettronica viene fornito un collegamento per il **Customer Lockbox** pannello nel portale di Azure. Utilizzando questo collegamento, il responsabile dell'approvazione designato esegue l'accesso al portale di Azure per visualizzare eventuali richieste che l'organizzazione dispone per Customer Lockbox in sospeso:
    
    ![Azure Customer Lockbox - pagina di destinazione](./media/azure-customer-lockbox/customer-lockbox-landing-page.png)
    
   La richiesta rimane nella coda del cliente per quattro giorni. Trascorso questo periodo, la richiesta di accesso scadrà automaticamente e non consentire l'accesso viene concesso ai tecnici di Microsoft.

9. Per ottenere i dettagli della richiesta in sospeso, il responsabile dell'approvazione designato può selezionare i lockbox richiesta dal **richieste in sospeso**:
    
    ![Azure Customer Lockbox - visualizzare la richiesta in sospeso](./media/azure-customer-lockbox/customer-lockbox-pending-requests.png)

10. Il responsabile dell'approvazione designato può inoltre selezionare il **ID richiesta di servizio** per visualizzare la richiesta di ticket di supporto che è stata creata dall'utente originale. Queste informazioni forniscono contesto per il motivo per cui viene avviato il supporto tecnico Microsoft e la cronologia del problema segnalato. Ad esempio:
    
    ![Azure Customer Lockbox - visualizzare la richiesta del ticket di supporto](./media/azure-customer-lockbox/customer-lockbox-support-ticket.png)

11. Dopo aver esaminato la richiesta, seleziona il responsabile dell'approvazione designato **Approve** oppure **Deny**:
    
    ![Azure Customer Lockbox - selezionare approva o rifiuta](./media/azure-customer-lockbox/customer-lockbox-approval.png)
    
    Come risultato la selezione:
    - **Approvare**:  L'accesso viene concesso al tecnico di Microsoft. L'accesso viene concesso per un periodo predefinito di otto ore.
    - **Negare**: La richiesta di accesso con privilegi elevati per il tecnico di Microsoft viene rifiutata e viene eseguita alcuna azione ulteriore.

A scopo di controllo, le azioni eseguite in questo flusso di lavoro effettuate [log delle richieste Customer Lockbox](#auditing-logs).

## <a name="auditing-logs"></a>Log di controllo

Customer Lockbox log vengono archiviati nei log attività. Nel portale di Azure, selezionare **log attività** per visualizzare le informazioni di controllo relativi a Customer Lockbox richieste. È possibile filtrare per azioni specifiche, ad esempio:
- **Negare la richiesta Lockbox**
- **Creare i Lockbox richiesta**
- **Approva la richiesta Lockbox**
- **Scadenza lockbox richiesta**

Ad esempio:

![Azure Customer Lockbox - log attività](./media/azure-customer-lockbox/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios"></a>Scenari e servizi supportati

I servizi e gli scenari seguenti sono attualmente disponibili a livello di Customer Lockbox.

### <a name="remote-desktop-access-to-virtual-machines"></a>Accesso desktop remoto alle macchine virtuali

Customer Lockbox è attualmente abilitata per le richieste di accesso desktop remoto alle macchine virtuali. Sono supportati i carichi di lavoro seguenti:
- Piattaforma distribuita come servizio (PaaS) - versione 1
- Infrastruttura distribuita come servizio (IaaS) - Windows e Linux (solo Azure Resource Manager)
- Set di scalabilità di macchine virtuali - Windows e Linux

> [!NOTE]
> Istanze del servizio IaaS versione classica non sono supportate da Customer Lockbox. Se hai carichi di lavoro in esecuzione in istanze di servizio IaaS versione classica, è consigliabile che eseguirne la migrazione dal modello Classico a modelli di distribuzione Resource Manager. Per istruzioni, vedere [migrazione supportata dalla piattaforma di risorse IaaS dal modello classico ad Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Log di controllo dettagliati

Per gli scenari che coinvolgono l'accesso desktop remoto, è possibile utilizzare i registri eventi di Windows per esaminare le azioni eseguite dal tecnico Microsoft. Provare a usare Centro sicurezza di Azure per raccogliere i registri eventi e copiare i dati nell'area di lavoro per l'analisi. Per altre informazioni, vedere [la raccolta dei dati nel Centro sicurezza Azure](../security-center/security-center-enable-data-collection.md).

## <a name="exclusions"></a>Esclusioni

Customer Lockbox richieste non vengono attivate nei seguenti scenari di supporto tecnico:

- Un ingegnere Microsoft deve eseguire un'attività che non rientra procedure operative standard. Ad esempio, per recuperare o ripristinare i servizi negli scenari imprevedibili o imprevisti.

- Un ingegnere Microsoft accede la piattaforma Azure come parte della risoluzione dei problemi e inavvertitamente ha accesso ai dati dei clienti. Ad esempio, il Team di rete di Azure esegue la risoluzione dei problemi che comporta un'acquisizione di pacchetti in un dispositivo di rete. Tuttavia, se il cliente crittografati i dati mentre era in transito, il tecnico non può leggere i dati.

## <a name="next-steps"></a>Passaggi successivi

Customer Lockbox è automaticamente disponibile per tutti i clienti che dispongono di un [piano di supporto tecnico di Azure](https://azure.microsoft.com/support/plans/) con un livello minimo di **Developer**.

Quando si dispone di un piano di supporto idonei, è richiesto alcun intervento da parte dell'utente per consentire a Customer Lockbox. Customer Lockbox richieste possono essere avviate da un tecnico Microsoft se questa azione è necessaria per l'avanzamento di un ticket di supporto che viene segnalato da un utente nell'organizzazione.
