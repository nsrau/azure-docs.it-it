---
title: Customer Lockbox per Microsoft Azure
description: Panoramica tecnica di Customer Lockbox per Microsoft Azure, che consente di controllare l'accesso al provider cloud quando Microsoft potrebbe dover accedere ai dati dei clienti.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/04/2019
ms.openlocfilehash: abc16ae7f7ab8bf15173248a6e7668e689e127de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561970"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Customer Lockbox per Microsoft Azure

> [!NOTE]
> Per usare questa funzionalità, l'organizzazione deve disporre di un piano di supporto di [Azure](https://azure.microsoft.com/support/plans/) con un livello minimo di **developer**.

Customer Lockbox per Microsoft Azure offre ai clienti un'interfaccia per esaminare e approvare o rifiutare le richieste di accesso ai dati dei clienti. Viene utilizzato nei casi in cui un tecnico Microsoft deve accedere ai dati dei clienti durante una richiesta di supporto.

In questo articolo viene illustrato come le richieste Customer Lockbox vengono avviate, monitorate e archiviate per revisioni e controlli successivi.

Customer Lockbox è ora generalmente disponibile e attualmente abilitato per l'accesso desktop remoto alle macchine virtuali.

## <a name="workflow"></a>Flusso di lavoro

I passaggi seguenti delineano un flusso di lavoro tipico per una richiesta di Customer Lockbox.

1. Un utente di un'organizzazione ha un problema con il carico di lavoro di Azure.Someone at an organization has an issue with their Azure workload.

2. Dopo che questa persona risolve il problema, ma non può risolverlo, apre un ticket di supporto dal portale di [Azure.](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) Il ticket viene assegnato a un tecnico del supporto clienti di Azure.The ticket is assigned to an Azure Customer Support Engineer.

3. Un tecnico del supporto di Azure esamina la richiesta di servizio e determina i passaggi successivi per risolvere il problema.

4. Se il tecnico del supporto non è in grado di risolvere il problema usando strumenti standard e telemetria, il passaggio successivo consiste nel richiedere autorizzazioni elevate utilizzando un servizio di accesso JIT (Just-In-Time). Questa richiesta può essere inviata dal tecnico del supporto originale. In alternativa, può provenire da un ingegnere diverso perché il problema viene riassegnato al team DevOps di Azure.Or, it can be from a different engineer because the problem is escalad to the Azure DevOps team.

5. Dopo che la richiesta di accesso è stata inviata dal tecnico di Azure, il servizio Just-In-Time valuta la richiesta tenendo conto di fattori quali:After the access request is submitted by the Azure Engineer, Just-In-Time service evaluates the request taking into factor such as:
    - L'ambito della risorsa
    - Se il richiedente è un'identità isolata o utilizza l'autenticazione a più fattori
    - Livelli di autorizzazione

    In base alla regola JIT, questa richiesta può includere anche un'approvazione da parte dei responsabili approvazione Microsoft interni. Ad esempio, l'approvatore potrebbe essere il responsabile del supporto clienti o DevOps Manager.For example, the approver might be the Customer support lead or the DevOps Manager.

6. Quando la richiesta richiede l'accesso diretto ai dati dei clienti, viene avviata una richiesta Customer Lockbox. Ad esempio, l'accesso desktop remoto alla macchina virtuale di un cliente.

    La richiesta è ora in uno stato **di notifica cliente,** in attesa dell'approvazione del cliente prima di concedere l'accesso.

7. Nell'organizzazione del cliente, l'utente con il [ruolo Proprietario](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) per la sottoscrizione di Azure riceve un messaggio di posta elettronica da Microsoft per notificare la richiesta di accesso in sospeso. Per le richieste di Customer Lockbox, questa persona è l'approvatore designato.

    Posta elettronica di esempio:

    ![Blocco cliente di Azure - notifica tramite posta elettronicaAzure Customer Lockbox - email notification](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. La notifica tramite posta elettronica fornisce un collegamento al pannello Customer Lockbox nel portale di Azure.The email notification provides a link to the **Customer Lockbox** blade in the Azure portal. Usando questo collegamento, l'approvatore designato accede al portale di Azure per visualizzare tutte le richieste in sospeso di cui l'organizzazione ha per Customer Lockbox:

    ![Blocco cliente di Azure - pagina di destinazioneAzure Customer Lockbox - landing page](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   La richiesta rimane nella coda del cliente per quattro giorni. Dopo questo periodo, la richiesta di accesso scade automaticamente e non viene concesso alcun accesso ai tecnici Microsoft.

9. Per ottenere i dettagli della richiesta in sospeso, l'approvatore designato può selezionare la richiesta lockbox da **Richieste in sospeso**:

    ![Blocco cliente di Azure - visualizzare la richiesta in sospesoAzure Customer Lockbox - view the pending request](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. L'approvatore designato può inoltre selezionare **l'ID RICHIESTA di servizio** per visualizzare la richiesta ticket di supporto creata dall'utente originale. Queste informazioni forniscono il contesto per il motivo per cui il supporto tecnico Microsoft è impegnato e la cronologia del problema segnalato. Ad esempio:

    ![Blocco cliente di Azure - visualizzare la richiesta del ticket di supportoAzure Customer Lockbox - view the support ticket request](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Dopo aver esaminato la richiesta, l'approvatore designato seleziona **Approva** o **Nega**:

    ![Blocco cliente di Azure - selezionare Approva o Nega](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    Come risultato della selezione:
    - **Approva**: L'accesso viene concesso al tecnico Microsoft. L'accesso viene concesso per un periodo predefinito di otto ore.
    - **Deny**: La richiesta di accesso con privilegi elevati da parte del tecnico Microsoft viene rifiutata e non viene intrapresa alcuna ulteriore azione.

Ai fini del controllo, le azioni eseguite in questo flusso di lavoro vengono registrate nei [registri delle richieste di Customer Lockbox](#auditing-logs).

## <a name="auditing-logs"></a>Log di controllo

I registri di Customer Lockbox vengono archiviati nei log attività. Nel portale di Azure selezionare Registri attività per visualizzare le informazioni di controllo relative alle richieste di Customer Lockbox.In the Azure portal, select **Activity Logs** to view auditing information related to Customer Lockbox requests. È possibile filtrare per azioni specifiche, ad esempio:You can filter for specific actions, such as:
- **Nega richiesta Lockbox**
- **Crea richiesta Lockbox**
- **Approva richiesta Lockbox**
- **Scadenza richiesta Lockbox**

Ad esempio:

![Blocco clienti di Azure - log attività](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>Servizi e scenari supportati nella disponibilità generale

I servizi e gli scenari seguenti sono attualmente disponibili in generale per Customer Lockbox.The following services and scenarios are currently in general availability for Customer Lockbox.

### <a name="remote-desktop-access-to-virtual-machines"></a>Accesso desktop remoto alle macchine virtuali

Customer Lockbox è attualmente abilitato per le richieste di accesso desktop remoto alle macchine virtuali. Sono supportati i carichi di lavoro seguenti:The following workloads are supported:
- Piattaforma come servizio (PaaS) - Servizi cloud di Azure (ruolo Web e ruolo di lavoro)Platform as a service (PaaS) - Azure Cloud Services (web role and worker role)
- Infrastruttura come servizio (IaaS) - Windows e Linux (solo Azure Resource Manager)Infrastructure as a service (IaaS) - Windows and Linux (Azure Resource Manager only)
- Set di scalabilità delle macchine virtuali - Windows e LinuxVirtual machine scale set - Windows and Linux

> [!NOTE]
> Le istanze IaaS Classic non sono supportate da Customer Lockbox. Se si dispone di carichi di lavoro in esecuzione nelle istanze di IaaS Classic, è consigliabile eseguirne la migrazione dai modelli di distribuzione Classic a Resource Manager.If you have workloads running on IaaS Classic instances, we recommend you migrate them from Classic to Resource Manager deployment models. Per istruzioni, vedere [Migrazione supportata dalla piattaforma di risorse IaaS dal classico a Azure Resource Manager.For instructions, see Platform-supported migration of IaaS resources from classic to Azure Resource Manager](../../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Registri di controllo dettagliati

Per gli scenari che implicano l'accesso desktop remoto, è possibile utilizzare i registri eventi di Windows per esaminare le azioni eseguite dal tecnico Microsoft.For scenarios that involve remote desktop access, you can use Windows event logs to review the actions taken by the Microsoft engineer. È consigliabile usare il Centro sicurezza di Azure per raccogliere i log eventi e copiare i dati nell'area di lavoro per l'analisi. Per altre informazioni, vedere Raccolta di dati nel Centro sicurezza di Azure.For more information, see [Data collection in Azure Security Center.](../../security-center/security-center-enable-data-collection.md)

## <a name="supported-services-and-scenarios-in-preview"></a>Servizi e scenari supportati in anteprima

I seguenti servizi sono attualmente in anteprima per Customer Lockbox:

- Archiviazione di Azure

- Database SQL di Azure

- Esplora dati di Azure

- Macchine virtuali (che ora coprono anche l'accesso ai dump di memoria e ai dischi gestiti)

- Trasferimenti di sottoscrizioni di AzureAzure subscription transfers

Per abilitare Customer Lockbox per queste offerte di anteprima per l'organizzazione, iscriversi a [Customer Lockbox for Azure Public Preview](https://aka.ms/customerlockbox/insiderprogram).


## <a name="exclusions"></a>Esclusioni

Le richieste di Customer Lockbox non vengono attivate nei seguenti scenari di supporto tecnico:

- Un tecnico Microsoft deve eseguire un'attività che non rientra nelle procedure operative standard. Ad esempio, per ripristinare o ripristinare i servizi in scenari imprevisti o imprevedibili.

- Un tecnico Microsoft accede alla piattaforma Azure come parte della risoluzione dei problemi e ha inavvertitamente accesso ai dati dei clienti. Ad esempio, il team di rete di Azure esegue la risoluzione dei problemi che comporta un'acquisizione di pacchetti in un dispositivo di rete. Tuttavia, se il cliente ha crittografato i dati mentre erano in transito, il tecnico non è in grado di leggere i dati.

## <a name="next-steps"></a>Passaggi successivi

Customer Lockbox è automaticamente disponibile per tutti i clienti che dispongono di un piano di [supporto di Azure](https://azure.microsoft.com/support/plans/) con un livello minimo di **developer**.

Quando si dispone di un piano di supporto idoneo, non è richiesta alcuna azione per abilitare Customer Lockbox. Le richieste di Customer Lockbox vengono avviate da un tecnico Microsoft se questa azione è necessaria per far avanzare un ticket di supporto che viene archiviato da qualcuno nell'organizzazione.
