---
title: Automatizzare le migrazioni VMware senza agente in Azure Migrate
description: Viene descritto come usare gli script per eseguire la migrazione di un numero elevato di macchine virtuali VMware in Azure Migrate
author: rahulgup
ms.service: azure-migrate
ms.topic: article
ms.date: 10/30/2020
ms.author: rahugup
ms.openlocfilehash: e1b34db7f2473e16d3ebde11376652f654f2f778
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377956"
---
# <a name="scale-migration-of-vmware-vms"></a>Ridimensionare la migrazione delle macchine virtuali VMware 

Questo articolo illustra come usare gli script per eseguire la migrazione di un numero elevato di macchine virtuali (VM) VMware usando il metodo senza agente. Per ridimensionare le migrazioni, è possibile usare [Azure migrate modulo di PowerShell](https://aka.ms/azuremigratepowershellvmware). 

Gli script di automazione della migrazione di Azure Migrate VMware sono disponibili per il download nel repository [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) su GitHub. Gli script possono essere usati per eseguire la migrazione di macchine virtuali VMware in Azure usando il metodo di migrazione senza agente. I comandi di Azure Migrate PowerShell usati in questi script sono descritti [qui](https://aka.ms/azuremigratepowershellvmware).

## <a name="current-limitations"></a>Limitazioni correnti
- Questi script supportano la migrazione di macchine virtuali VMware con tutti i dischi. È possibile aggiornare gli script se si desidera replicare in modo selettivo i dischi collegati a una VM VMware. 
- Gli script supportano l'uso di raccomandazioni di valutazione. Se non vengono usate le raccomandazioni relative alla valutazione, viene eseguita la migrazione di tutti i dischi collegati alla VM VMware allo stesso tipo di disco gestito (standard o Premium). È possibile aggiornare gli script se si vogliono usare più tipi di dischi gestiti con la stessa macchina virtuale

## <a name="prerequisites"></a>Prerequisiti

- [Completare l'esercitazione sull'individuazione](tutorial-discover-vmware.md) per preparare Azure e VMware per la migrazione.
- È consigliabile completare la seconda esercitazione per [valutare le macchine virtuali VMware](tutorial-assess-vmware.md) prima di eseguirne la migrazione ad Azure.
- Avere a disposizione il modulo `Az` di Azure PowerShell. Se è necessario installare o aggiornare Azure PowerShell, vedere questa [guida all'installazione e alla configurazione di Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="install-azure-migrate-powershell-module"></a>Installare il modulo Azure Migrate di PowerShell

Il modulo Azure Migrate di PowerShell è disponibile in anteprima. È necessario installare il modulo di PowerShell usando il comando seguente. 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="csv-input-file"></a>File di input CSV
Una volta completati tutti i prerequisiti, è necessario creare un file CSV contenente i dati per ogni VM di origine di cui si vuole eseguire la migrazione. Tutti gli script sono progettati per funzionare nello stesso file CSV. Un modello CSV di esempio è disponibile nella cartella degli script come riferimento. Il file CSV è configurabile in modo da poter usare le raccomandazioni di valutazione e persino specificare se alcune operazioni non devono essere attivate per una determinata macchina virtuale. 

> [!NOTE]
> Lo stesso file CSV può essere usato per eseguire la migrazione di macchine virtuali in più progetti Azure Migrate.

### <a name="csv-file-schema"></a>Schema file CSV

**Intestazione di colonna** | **Descrizione**
--- | ---
AZMIGRATEPROJECT_SUBSCRIPTION_ID | Fornire Azure Migrate ID sottoscrizione progetto.
AZMIGRATEPROJECT_RESOURCE_GROUP_NAME | Specificare Azure Migrate nome del gruppo di risorse.
AZMIGRATEPROJECT_NAME | Consente di specificare il nome del progetto Azure Migrate in cui si desidera eseguire la migrazione dei server. 
SOURCE_MACHINE_NAME | Fornire il nome descrittivo (nome visualizzato) per la macchina virtuale individuata nel progetto Azure Migrate.
AZMIGRATEASSESSMENT_NAME | Fornire il nome della valutazione da sfruttare per la migrazione.
AZMIGRATEGROUP_NAME | Consente di specificare il nome del gruppo utilizzato per la valutazione del Azure Migrate.
TARGET_RESOURCE_GROUP_NAME | Specificare il nome del gruppo di risorse di Azure in cui deve essere eseguita la migrazione della macchina virtuale.
TARGET_VNET_NAME| Specificare il nome della rete virtuale di Azure che deve essere usata dalla VM migrata.
TARGET_SUBNET_NAME | Fornire il nome della subnet nella rete virtuale di destinazione che deve essere usata dalla VM migrata. Se viene lasciato vuoto, verrà usata la subnet "default".
TARGET_MACHINE_NAME | Specificare il nome che deve essere usato dalla VM migrata in Azure. Se viene lasciato vuoto, verrà usato il nome del computer di origine.  
TARGET_MACHINE_SIZE | Fornire lo SKU che la macchina virtuale deve usare in Azure. Per eseguire la migrazione di una macchina virtuale a D2_v2 macchina virtuale in Azure, specificare il valore in questo campo come "Standard_D2_v2". Se si usa una valutazione, questo valore verrà derivato in base alla raccomandazione di valutazione.
LICENSE_TYPE | Specificare se si desidera utilizzare Vantaggio Azure Hybrid per le macchine virtuali Windows Server. Usare il valore "WindowsServer" per sfruttare i vantaggi della Vantaggio Azure Hybrid. In caso contrario, lasciare vuoto o usare "NoLicenseType".
OS_DISK_ID | Fornire l'ID del disco del sistema operativo per la macchina virtuale da migrare. L'ID disco da usare è la proprietà identificatore univoco (UUID) per il disco recuperato tramite il cmdlet Get-AzMigrateServer. Lo script userà il primo disco della macchina virtuale come disco del sistema operativo nel caso in cui non venga fornito alcun valore.
TARGET_DISKTYPE | Specificare il tipo di disco da usare per tutti i dischi della macchina virtuale in Azure. Usare ' Premium_LRS ' per i dischi gestiti Premium,' StandardSSD_LRS ' per dischi SSD standard è Standard_LRS ' per usare dischi HDD standard. Se si sceglie di usare una valutazione, lo script verrà assegnato in ordine di priorità usando i tipi di disco consigliati per ogni disco della macchina virtuale. Se non si utilizza la valutazione o si specifica alcun valore, lo script utilizzerà dischi HDD standard per impostazione predefinita.    
AVAILABILITYZONE_NUMBER | Specificare il numero della zona di disponibilità da usare per la macchina virtuale di cui è stata eseguita la migrazione. È possibile lasciare questo campo vuoto se non si vogliono usare le zone di disponibilità. 
AVAILABILITYSET_NAME | Specificare il nome del set di disponibilità da usare per la macchina virtuale di cui è stata eseguita la migrazione. È possibile lasciare questo campo vuoto se non si vuole usare il set di disponibilità.
TURNOFF_SOURCESERVER | Specificare "Y" Se si desidera disattivare la VM di origine al momento della migrazione. In caso contrario, usare ' n'. Se viene lasciato vuoto, lo script presuppone che il valore sia ' n'.
TESTMIGRATE_VNET_NAME | Specificare il nome della rete virtuale da utilizzare per la migrazione dei test.
UPDATED_TARGET_RESOURCE_GROUP_NAME | Se si vuole aggiornare il gruppo di risorse che verrà usato dalla macchina virtuale migrata in Azure, specificare il nome del gruppo di risorse di Azure. in caso contrario, lasciare vuoto. 
UPDATED_TARGET_VNET_NAME | Se si vuole aggiornare la rete virtuale che deve essere usata dalla VM migrata in Azure, specificare il nome della rete virtuale di Azure. in caso contrario, lasciare vuoto.
UPDATED_TARGET_MACHINE_NAME | Se si vuole aggiornare il nome che verrà usato dalla macchina virtuale migrata in Azure, specificare il nuovo nome da usare. in caso contrario, lasciare vuoto.
UPDATED_TARGET_MACHINE_SIZE | Se si vuole aggiornare lo SKU che verrà usato dalla macchina virtuale migrata in Azure, specificare il nuovo SKU da usare. in caso contrario, lasciare vuoto.
UPDATED_AVAILABILITYZONE_NUMBER | Se si vuole aggiornare la zona di disponibilità per l'uso da parte della macchina virtuale migrata in Azure, specificare la nuova zona di disponibilità da usare. in caso contrario, lasciare vuoto.
UPDATED_AVAILABILITYSET_NAME | Se si vuole aggiornare il set di disponibilità che verrà usato dalla macchina virtuale migrata in Azure, specificare il nuovo set di disponibilità da usare. in caso contrario, lasciare vuoto.
UPDATE_NIC1_ID | Specificare l'ID della scheda di interfaccia di rete da aggiornare. Se lasciato vuoto, lo script presuppone che il valore sia la prima scheda di interfaccia di rete della macchina virtuale individuata. Se non si vuole aggiornare la scheda di interfaccia di rete della macchina virtuale, lasciare vuoti tutti i campi che contengono il nome della scheda di interfaccia di rete. 
UPDATED_TARGET_NIC1_SELECTIONTYPE | Specificare il valore da usare per questa scheda di interfaccia di rete. Usare "Primary", "Secondary" o "DoNotCreate" per specificare se questa scheda di interfaccia di rete deve essere primaria, secondaria o non deve essere creata nella macchina virtuale di cui è stata eseguita la migrazione. Per la VM è possibile specificare un'unica scheda di interfaccia di rete come primaria. Lasciare vuoto se non si desidera aggiornare.
UPDATED_TARGET_NIC1_SUBNET_NAME | Specificare il nome della subnet da usare per la scheda di interfaccia di rete nella macchina virtuale di cui è stata eseguita la migrazione. Lasciare vuoto se non si desidera aggiornare.
UPDATED_TARGET_NIC1_IP | Specificare l'indirizzo IPv4 che verrà usato dalla scheda di interfaccia di rete nella macchina virtuale migrata se si vuole usare un indirizzo IP statico. Usare "auto" Se si vuole assegnare automaticamente l'indirizzo IP. Lasciare vuoto se non si desidera aggiornare.
UPDATE_NIC2_ID | Specificare l'ID della scheda di interfaccia di rete da aggiornare. Se viene lasciato vuoto, lo script presuppone che il valore sia la seconda scheda di interfaccia di rete della macchina virtuale individuata. Se non si vuole aggiornare la scheda di interfaccia di rete della macchina virtuale, lasciare vuoti tutti i campi che contengono il nome della scheda di interfaccia di rete.
UPDATED_TARGET_NIC2_SELECTIONTYPE | Specificare il valore da usare per questa scheda di interfaccia di rete. Usare "Primary", "Secondary" o "DoNotCreate" per specificare se questa scheda di interfaccia di rete deve essere primaria, secondaria o non deve essere creata nella macchina virtuale di cui è stata eseguita la migrazione. Per la VM è possibile specificare un'unica scheda di interfaccia di rete come primaria. Lasciare vuoto se non si desidera aggiornare.
UPDATED_TARGET_NIC2_SUBNET_NAME | Specificare il nome della subnet da usare per la scheda di interfaccia di rete nella macchina virtuale di cui è stata eseguita la migrazione. Lasciare vuoto se non si desidera aggiornare.
UPDATED_TARGET_NIC2_IP | Specificare l'indirizzo IPv4 che verrà usato dalla scheda di interfaccia di rete nella macchina virtuale migrata se si vuole usare un indirizzo IP statico. Usare "auto" Se si vuole assegnare automaticamente l'indirizzo IP. Lasciare vuoto se non si desidera aggiornare.
OK_TO_UPDATE | Usare "Y" per indicare se è necessario aggiornare le proprietà della macchina virtuale quando si esegue lo script AzMigrate_UpdateMachineProperties. Usare ' n'o lasciare vuoto in caso contrario.
OK_TO_MIGRATE | Usare "Y" per indicare se la macchina virtuale deve essere migrata quando si esegue lo script AzMigrate_StartMigration. Usare ' n'o lasciare vuoto se non si vuole eseguire la migrazione della macchina virtuale. 
OK_TO_USE_ASSESSMENT | Usare "Y" per indicare se la macchina virtuale deve avviare la replica usando le raccomandazioni relative alla valutazione quando si esegue lo script AzMigrate_StartReplication. Questa operazione sostituirà i valori TARGET_MACHINE_SIZE e TARGET_DISKTYPE nel file CSV. Usare ' n'o lasciare vuoto se non si vogliono usare le raccomandazioni per la valutazione.
OK_TO_TESTMIGRATE | Usare "Y" per indicare se la macchina virtuale deve essere sottoposta a test di cui è stata eseguita la migrazione quando si esegue lo script AzMigrate_StartTestMigration. Usare ' n'o lasciare vuoto se non si vuole testare la migrazione della macchina virtuale. 
OK_TO_RETRIEVE_REPLICATIONSTATUS | Usare "Y" per indicare se lo stato di replica della macchina virtuale deve essere aggiornato quando si esegue lo script AzMigrate_ReplicationStatus. Utilizzare ' n'o lasciare vuoto se non si desidera aggiornare lo stato della replica.
OK_TO_CLEANUP | Usare "Y" per indicare se la replica per la macchina virtuale deve essere pulita quando si esegue lo script AzMigrate_StopReplication. Usare ' n'o lasciare vuoto in caso contrario.
OK_TO_TESTMIGRATE_CLEANUP | Usare "Y" per indicare se è necessario pulire la migrazione di test per la macchina virtuale quando si esegue lo script AzMigrate_CleanUpTestMigration. Usare ' n'o lasciare vuoto in caso contrario.


## <a name="script-execution"></a>Esecuzione degli script

Quando il volume condiviso cluster è pronto, è possibile eseguire la procedura seguente per eseguire la migrazione delle macchine virtuali VMware locali.

**N.ro passaggio** | **Nome dello script** | **Descrizione**
--- | --- | ---
1 | AzMigrate_StartReplication.ps1 | Abilitare la replica per tutte le macchine virtuali elencate nel volume condiviso cluster, lo script crea un output CSV e un file di log per la risoluzione dei problemi.
2 | AzMigrate_ReplicationStatus.ps1 | Verificare lo stato della replica. lo script crea un output CSV con lo stato per ogni macchina virtuale e un file di log per la risoluzione dei problemi.
3 | AzMigrate_UpdateMachineProperties.ps1 | Dopo aver completato la replica iniziale delle macchine virtuali, usare questo script per aggiornare le proprietà di destinazione della VM (calcolo e proprietà di rete). Lo script crea un output CSV con i dettagli del processo per ogni macchina virtuale.
4 | AzMigrate_StartTestMigration.ps1 |  Avviare il failover di test per tutte le macchine virtuali elencate nel volume condiviso cluster configurate per la migrazione di test. Lo script crea un output CSV con i dettagli del processo per ogni macchina virtuale.
5 | AzMigrate_CleanUpTestMigration.ps1 | Una volta convalidate manualmente le macchine virtuali di cui è stato eseguito il failover, usare questo script per pulire le VM di failover di test per tutte le macchine virtuali elencate nel volume condiviso cluster configurate per la pulizia della migrazione di test. Lo script crea un output CSV con i dettagli del processo per ogni macchina virtuale.
6 | AzMigrate_StartMigration.ps1 | Avviare la migrazione di tutte le macchine virtuali elencate nel volume condiviso cluster configurate per la migrazione. Lo script crea un output CSV con i dettagli del processo per ogni macchina virtuale.
7 | AzMigrate_StopReplication.ps1 | Arresta la replica per la macchina virtuale dopo che è stata eseguita la migrazione o se si desidera annullare la replica a causa di altri motivi. Lo script crea un output CSV con i dettagli del processo per ogni macchina virtuale.


Gli script seguenti vengono richiamati da altri script per tutte le operazioni Azure Migrate come l'abilitazione della replica, l'avvio della migrazione di test, l'aggiornamento delle proprietà delle VM e così via Verificare che tutti gli script siano presenti nella stessa cartella/percorso. 

**N.ro passaggio** | **Nome dello script** | **Descrizione**
--- | --- | ---
1 | AzMigrate_Shared.ps1 | Script comune contenente funzioni per il recupero delle proprietà di valutazione (tramite API), delle VM individuate e della replica delle macchine virtuali. 
2 | AzMigrate_CSV_Processor.ps1 | Script comune contenente le funzioni utilizzate per le operazioni sui file CSV, inclusi il caricamento, la lettura e la stampa per i log. 
3 | AzMigrate_Logger.ps1 | Script comune richiamato per generare il file di log per Azure Migrate operazioni di automazione. Il formato del file di log sarà log.Scriptname.Datetime.txt.

Oltre a quanto sopra riportato, la cartella contiene anche AzMigrate_Template.ps1 che contiene il Framework Skeleton per la creazione di script personalizzati per diverse operazioni di Azure Migrate. 

### <a name="script-execution-syntax"></a>Sintassi di esecuzione dello script

Una volta scaricati gli script, gli script possono essere eseguiti come indicato di seguito.

Se si vuole eseguire lo script per avviare la replica per le macchine virtuali usando il file di Input.csv, usare la sintassi seguente. 

```azurepowershell
".\AzMigrate_StartReplication.ps1" .\Input.csv 
```

Per altre informazioni sull'uso di Azure PowerShell per la migrazione di macchine virtuali VMware con Azure Migrate, seguire l' [esercitazione](https://aka.ms/azuremigratepowershellvmware).