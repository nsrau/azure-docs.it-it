---
title: Uso di account di archiviazione gestiti dal cliente in monitoraggio di Azure Log Analytics
description: Usare il proprio account di archiviazione per scenari di Log Analytics
ms.subservice: logs
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: 9d54e6eb84e3269eb95f8d314875474f78536652
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526426"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Uso di account di archiviazione gestiti dal cliente in monitoraggio di Azure Log Analytics

Log Analytics si basa su archiviazione di Azure in diversi scenari. Questo utilizzo viene in genere gestito automaticamente. Tuttavia, in alcuni casi è necessario fornire e gestire il proprio account di archiviazione, noto anche come account di archiviazione gestito dal cliente. Questo documento illustra in dettaglio l'uso dell'archiviazione gestita dal cliente per l'inserimento di log WAD/LAD, scenari specifici di collegamento privato e crittografia CMK. 

> [!NOTE]
> Si consiglia di non assumere una dipendenza dal contenuto Log Analytics caricamenti nell'archiviazione gestita dal cliente, dato che la formattazione e il contenuto possono cambiare.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Inserimento di log estensioni di Diagnostica di Azure (WAD/LAD)
Gli agenti di estensione Diagnostica di Azure (detti anche WAD e LAD per gli agenti Windows e Linux rispettivamente) raccolgono vari log del sistema operativo e li archiviano in un account di archiviazione gestito dal cliente. È quindi possibile inserire questi log in Log Analytics per esaminarli e analizzarli.
Come raccogliere i log di estensione Diagnostica di Azure dall'account di archiviazione, connettere l'account di archiviazione all'area di lavoro di Log Analytics come origine dati di archiviazione usando [il portale di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-logs#collect-logs-from-azure-storage) o chiamando l' [API di archiviazione Insights](https://docs.microsoft.com/rest/api/loganalytics/connectedsources/storage%20insights/createorupdate).

Tipi di dati supportati:
* syslog
* Eventi Windows
* Service Fabric
* Eventi ETW
* Log IIS

## <a name="using-private-links"></a>Uso di collegamenti privati
Gli account di archiviazione gestiti dal cliente sono necessari in alcuni casi d'uso, quando si usano i collegamenti privati per connettersi alle risorse di monitoraggio di Azure. Un caso di questo tipo è l'inserimento di log personalizzati o di log di IIS. Questi tipi di dati vengono prima caricati come BLOB in un account di archiviazione di Azure intermediario e quindi inseriti solo in un'area di lavoro. Analogamente, alcune soluzioni di monitoraggio di Azure possono usare gli account di archiviazione per archiviare file di grandi dimensioni, ad esempio i file di dump Watson, usati dalla soluzione del Centro sicurezza di Azure. 

##### <a name="private-link-scenarios-that-require-a-customer-managed-storage"></a>Scenari di collegamento privato che richiedono una risorsa di archiviazione gestita dal cliente
* Inserimento di log personalizzati e log di IIS
* Consentire alla soluzione ASC di raccogliere i file di dump di Watson

### <a name="how-to-use-a-customer-managed-storage-account-over-a-private-link"></a>Come usare un account di archiviazione gestito dal cliente tramite un collegamento privato
##### <a name="workspace-requirements"></a>Requisiti dell'area di lavoro
Quando ci si connette a monitoraggio di Azure tramite un collegamento privato, gli agenti Log Analytics possono inviare log solo alle aree di lavoro collegate alla rete tramite un collegamento privato. Questa regola richiede la configurazione corretta di un oggetto ambito di collegamento privato (AMPLS) di monitoraggio di Azure, la connessione alle aree di lavoro e la connessione di AMPLS alla rete tramite un collegamento privato. Per altre informazioni sulla procedura di configurazione AMPLS, vedere [usare il collegamento privato di Azure per connettere in modo sicuro le reti a monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security). 
##### <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione
Perché l'account di archiviazione si connetta correttamente al collegamento privato, è necessario:
* Trovarsi nella VNet o in una rete con peering e connettersi alla VNet tramite un collegamento privato. In questo modo gli agenti nel VNet possono inviare i log all'account di archiviazione.
* Trovarsi nella stessa area dell'area di lavoro a cui è collegato.
* Consentire a monitoraggio di Azure di accedere all'account di archiviazione. Se si sceglie di consentire solo la selezione di reti per l'accesso all'account di archiviazione, è necessario consentire anche questa eccezione: "Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione". Questo consente Log Analytics di leggere i log inseriti in questo account di archiviazione.
* Se l'area di lavoro gestisce anche il traffico da altre reti, è necessario configurare l'account di archiviazione per consentire il traffico in ingresso proveniente dalle reti/Internet pertinenti.

##### <a name="link-your-storage-account-to-a-log-analytics-workspace"></a>Collegare l'account di archiviazione a un'area di lavoro Log Analytics
È possibile collegare l'account di archiviazione all'area di lavoro tramite l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace/linked-storage) o l' [API REST](https://docs.microsoft.com/rest/api/loganalytics/linkedstorageaccounts). Valori dataSourceType applicabili:
* CustomLogs: per usare l'archiviazione per i log personalizzati e i log di IIS durante l'inserimento.
* AzureWatson: usare i file di dump di archiviazione per Watson caricati dalla soluzione ASC (Centro sicurezza di Azure). Per altre informazioni sulla gestione della conservazione, sulla sostituzione di un account di archiviazione collegato e sul monitoraggio dell'attività dell'account di archiviazione, vedere [gestione degli account di archiviazione collegati](#managing-linked-storage-accounts). 

## <a name="encrypting-data-with-cmk"></a>Crittografia dei dati con CMK
Archiviazione di Azure crittografa tutti i dati inattivi in un account di archiviazione. Per impostazione predefinita, i dati vengono crittografati con le chiavi gestite da Microsoft (MMK). Tuttavia, archiviazione di Azure consente invece di usare una chiave gestita dal cliente (CMK) da Azure Key Vault per crittografare i dati di archiviazione. È possibile importare le proprie chiavi in Azure Key Vault oppure è possibile usare le API Azure Key Vault per generare chiavi.
##### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>Scenari CMK che richiedono un account di archiviazione gestito dal cliente
* Crittografia di query di avviso di log con CMK
* Crittografia delle query salvate con CMK

### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>Come applicare CMK a account di archiviazione gestiti dal cliente
##### <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione
L'account di archiviazione e l'insieme di credenziali chiave devono essere nella stessa area, ma possono appartenere a sottoscrizioni diverse. Per altre informazioni sulla crittografia di archiviazione di Azure e sulla gestione delle chiavi, vedere [crittografia di archiviazione di Azure per dati](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)inattivi.

##### <a name="apply-cmk-to-your-storage-accounts"></a>Applicare CMK agli account di archiviazione
Per configurare l'account di archiviazione di Azure in modo da usare chiavi gestite dal cliente con Azure Key Vault, usare il [portale di Azure](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal?toc=/azure/storage/blobs/toc.json), [PowerShell](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-powershell?toc=/azure/storage/blobs/toc.json) o l' [interfaccia](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-cli?toc=/azure/storage/blobs/toc.json)della riga di comando. 

## <a name="managing-linked-storage-accounts"></a>Gestione degli account di archiviazione collegati

Per collegare o scollegare gli account di archiviazione nell'area di lavoro, usare l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace/linked-storage) o l' [API REST](https://docs.microsoft.com/rest/api/loganalytics/linkedstorageaccounts).

##### <a name="create-or-modify-a-link"></a>Creare o modificare un collegamento
Quando si collega un account di archiviazione a un'area di lavoro, Log Analytics inizierà a usarlo al posto dell'account di archiviazione di proprietà del servizio. È possibile 
* Registrare più account di archiviazione per suddividere il carico dei log tra di essi
* Riutilizzare lo stesso account di archiviazione per più aree di lavoro

##### <a name="unlink-a-storage-account"></a>Scollegare un account di archiviazione
Per interrompere l'uso di un account di archiviazione, scollegare lo spazio di archiviazione dall'area di lavoro. Lo scollegamento di tutti gli account di archiviazione da un'area di lavoro significa che Log Analytics tenterà di utilizzare gli account di archiviazione gestiti dal servizio. Se la rete ha accesso limitato a Internet, queste archiviazioni potrebbero non essere disponibili e gli eventuali scenari che si basano sull'archiviazione avranno esito negativo.

##### <a name="replace-a-storage-account"></a>Sostituire un account di archiviazione
Per sostituire un account di archiviazione usato per l'inserimento,
1.  **Creare un collegamento a un nuovo account di archiviazione.** Gli agenti di registrazione otterranno la configurazione aggiornata e inizieranno a inviare i dati anche alla nuova risorsa di archiviazione. Il processo potrebbe richiedere alcuni minuti.
2.  **Scollegare quindi l'account di archiviazione precedente in modo che gli agenti interrompano la scrittura nell'account rimosso.** Il processo di inserimento continua a leggere i dati da questo account fino a quando non vengono inseriti tutti. Non eliminare l'account di archiviazione finché non vengono visualizzati tutti i log inseriti.

### <a name="maintaining-storage-accounts"></a>Gestione degli account di archiviazione
##### <a name="manage-log-retention"></a>Gestire la conservazione dei log
Quando si usa il proprio account di archiviazione, la conservazione dipende dall'utente. In altre parole, Log Analytics non elimina i log archiviati nella risorsa di archiviazione privata. È necessario invece configurare un criterio per gestire il carico in base alle proprie preferenze.

##### <a name="consider-load"></a>Prendere in considerazione il carico
Gli account di archiviazione possono gestire un determinato carico di richieste di lettura e scrittura prima di iniziare la limitazione delle richieste. per altri dettagli, vedere [obiettivi di scalabilità e prestazioni per l'archiviazione BLOB](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account) . La limitazione influiscono sul tempo necessario per inserire i log. Se l'account di archiviazione è sovraccarico, registrare un account di archiviazione aggiuntivo per suddividere il carico tra di essi. Per monitorare la capacità e le prestazioni dell'account di archiviazione, esaminare le [informazioni dettagliate nella portale di Azure]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Addebiti correlati
Gli account di archiviazione vengono addebitati in base al volume dei dati archiviati, al tipo di archiviazione e al tipo di ridondanza. Per informazioni dettagliate, vedere [Prezzi per i BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs) e [Prezzi di Archiviazione tabelle di Azure](https://azure.microsoft.com/pricing/details/storage/tables).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull' [uso del collegamento privato di Azure per connettere in modo sicuro le reti a monitoraggio di Azure](private-link-security.md)
- Informazioni sulle [chiavi gestite dal cliente di monitoraggio di Azure](customer-managed-keys.md)
