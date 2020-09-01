---
title: Avviare manualmente un failover in SQL Istanza gestita
description: Informazioni su come eseguire manualmente il failover delle repliche primarie e secondarie in Azure SQL Istanza gestita.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 08/31/2020
ms.openlocfilehash: 0d5390beff6c3e0045c6b887f0262a54a737a851
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181766"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>Failover manuale avviato dall'utente in SQL Istanza gestita

Questo articolo illustra come eseguire il failover manuale di un nodo primario nei livelli di servizio di SQL Istanza gestita per utilizzo generico (GP) e business critical (BC) e come eseguire manualmente il failover di un nodo di replica secondaria di sola lettura nel livello di servizio BC.

## <a name="when-to-use-manual-failover"></a>Quando usare il failover manuale

La [disponibilità elevata](../database/high-availability-sla.md) è una parte fondamentale della piattaforma SQL istanza gestita che funziona in modo trasparente per le applicazioni di database. I failover da nodi primari a secondari in caso di riduzione del nodo o rilevamento di errori o durante gli aggiornamenti software mensili regolari rappresentano un'occorrenza prevista per tutte le applicazioni che usano SQL Istanza gestita in Azure.

È possibile considerare l'esecuzione di un [failover manuale](../database/high-availability-sla.md#testing-application-fault-resiliency) in SQL istanza gestita per alcuni dei motivi seguenti:
- Testare l'applicazione per la resilienza del failover prima della distribuzione nell'ambiente di produzione
- Testare i sistemi end-to-end per la resilienza degli errori nei failover automatici
- Testare il modo in cui il failover influisca sulle sessioni di database esistenti
- Verificare se un failover modifica le prestazioni end-to-end a causa di modifiche alla latenza di rete
- In alcuni casi di riduzioni delle prestazioni delle query, il failover manuale consente di attenuare il problema di prestazioni.

> [!NOTE]
> Assicurandosi che le applicazioni siano resilienti al failover prima della distribuzione nell'ambiente di produzione, è possibile ridurre il rischio di errori dell'applicazione in produzione e contribuire alla disponibilità dell'applicazione per i clienti.

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>Avviare il failover manuale in SQL Istanza gestita

### <a name="rbac-permissions-required"></a>Autorizzazioni RBAC richieste

Per avviare un failover, l'utente deve avere uno dei ruoli RBAC seguenti:

- Ruolo di proprietario della sottoscrizione o
- Istanza gestita ruolo Collaboratore o
- Ruolo personalizzato con l'autorizzazione seguente:
  - `Microsoft.Sql/managedInstances/failover/action`

### <a name="using-powershell"></a>Utilizzo di PowerShell

La versione minima di AZ. SQL deve essere [v 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0). Si consiglia di usare [Azure cloud Shell](../../cloud-shell/overview.md) dal portale di Azure in cui è sempre disponibile la versione più recente di PowerShell. 

Come prerequisito, usare lo script di PowerShell seguente per installare i moduli di Azure necessari. Inoltre, selezionare la sottoscrizione in cui si trova Istanza gestita si desidera eseguire il failover.

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

Usare il comando PowerShell [Invoke-AzSqlInstanceFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlinstancefailover) con l'esempio seguente per avviare il failover del nodo primario, applicabile al livello di servizio BC e GP.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

Usare il comando PS seguente per eseguire il failover del nodo secondario Read, applicabile solo al livello di servizio BC.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>Uso dell'interfaccia della riga di comando

Assicurarsi di aver installato gli script dell'interfaccia della riga di comando più recenti.

Usare il comando AZ SQL mi failover CLI con l'esempio seguente per avviare il failover del nodo primario, applicabile al livello di servizio BC e GP.

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

Usare il comando dell'interfaccia della riga di comando seguente per eseguire il failover del nodo secondario Read, applicabile solo al livello di servizio BC.

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>Uso dell'API REST

Per gli utenti avanzati che potrebbero avere l'esigenza di automatizzare i failover delle istanze di SQL gestite per l'implementazione di una pipeline di testing continuo o di mitigatori delle prestazioni automatizzati, questa funzione può essere eseguita tramite l'avvio del failover tramite una chiamata API. per informazioni dettagliate, vedere [istanze gestite: API REST di failover](https://docs.microsoft.com/rest/api/sql/managed%20instances%20-%20failover/failover) .

Per avviare il failover usando la chiamata all'API REST, generare prima il token di autenticazione usando il client API scelto. Il token di autenticazione generato viene usato come proprietà di autorizzazione nell'intestazione della richiesta API ed è obbligatorio.

Il codice seguente è un esempio dell'URI dell'API da chiamare:

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

Le proprietà seguenti devono essere passate nella chiamata API:

| **Proprietà API** | **Parametro** |
| --- | --- |
| subscriptionId | ID sottoscrizione in cui viene distribuita l'istanza gestita |
| resourceGroupName | Gruppo di risorse che contiene l'istanza gestita |
| managedInstanceName | Nome dell'istanza gestita |
| replicaType | Opzionale (Primario o ReadableSecondary). Questi parametri rappresentano il tipo di replica di cui eseguire il failover, ovvero primario o secondario leggibile. Se non è specificato, per impostazione predefinita il failover verrà avviato sulla replica primaria. |
| api-version | Il valore statico e attualmente deve essere "2019-06-01-Preview" |

La risposta API sarà una delle due seguenti:

- 202 - Accettato
- Uno degli errori 400 della richiesta.

Lo stato dell'operazione può essere rilevato tramite la revisione delle risposte API nelle intestazioni di risposta. Per altre informazioni, vedere [stato delle operazioni asincrone di Azure](../../azure-resource-manager/management/async-operations.md).

## <a name="monitor-the-failover"></a>Monitorare il failover

Per monitorare lo stato del failover manuale avviato dall'utente, eseguire la query T-SQL seguente nel client preferito, ad esempio SSMS, in SQL Istanza gestita. Verrà letta la vista di sistema sys. dm_hadr_fabric_replica_states e le repliche di report disponibili nell'istanza. Aggiornare la stessa query dopo l'avvio del failover manuale.

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

Prima di avviare il failover, l'output indicherà la replica primaria corrente nel livello di servizio BC contenente uno primario e tre database secondari nel gruppo di disponibilità AlwaysOn. Al momento dell'esecuzione di un failover, è necessario che l'esecuzione di questa query indichi una modifica del nodo primario.

Non sarà possibile visualizzare lo stesso output con il livello di servizio di GP come quello riportato sopra per BC. Questo perché il livello di servizio di GP si basa su un solo nodo. L'output di query T-SQL per il livello di servizio di GP mostrerà un solo nodo prima e dopo il failover. La perdita di connettività dal client durante il failover, che in genere dura meno di un minuto, sarà l'indicazione dell'esecuzione del failover.

> [!NOTE]
> Il completamento del processo di failover (non la mancata disponibilità effettiva) potrebbe richiedere diversi minuti alla volta in caso di carichi di lavoro **ad alta intensità** . Questo perché il motore dell'istanza sta occupando tutte le transazioni correnti nel database primario e viene aggiornato sul nodo secondario, prima di poter eseguire il failover.

> [!IMPORTANT]
> Limitazioni funzionali del failover manuale avviato dall'utente:
> - Potrebbe essere stato avviato un failover (1) nella stessa Istanza gestita ogni **30 minuti**.
> - Per le istanze BC è necessario che esista il quorum delle repliche affinché venga accettata la richiesta di failover.
> - Per le istanze BC non è possibile specificare la replica secondaria leggibile su cui avviare il failover.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla disponibilità elevata della disponibilità elevata dell'istanza gestita [per il istanza gestita SQL di Azure](../database/high-availability-sla.md).
- Per una panoramica, vedere informazioni su [Azure SQL istanza gestita](sql-managed-instance-paas-overview.md).
