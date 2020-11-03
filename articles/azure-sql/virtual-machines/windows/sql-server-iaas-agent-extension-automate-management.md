---
title: Che cos'è la SQL Server estensione dell'agente IaaS?
description: Questo articolo descrive il modo in cui l'estensione SQL Server agente IaaS consente di automatizzare le attività amministrative specifiche di gestione delle SQL Server nelle VM di Azure. Sono incluse funzionalità come backup automatizzato, applicazione automatica di patch, integrazione Azure Key Vault, gestione delle licenze, configurazione dell'archiviazione e gestione centrale di tutte le istanze di VM SQL Server.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/30/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 070058eae36bf4f8546cfcf4beb85ac5023e9c79
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286181"
---
# <a name="what-is-the-sql-server-iaas-agent-extension"></a>Che cos'è la SQL Server estensione dell'agente IaaS?
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


L'estensione SQL Server IaaS Agent (SqlIaasExtension) viene eseguita su SQL Server in macchine virtuali di Azure per automatizzare le attività di gestione e amministrazione. 

Questo articolo fornisce una panoramica dell'estensione. Per installare l'estensione IaaS SQL Server per SQL Server in macchine virtuali di Azure, vedere gli articoli relativi all' [installazione automatica](sql-vm-resource-provider-automatic-registration.md), alle [macchine virtuali singole](sql-vm-resource-provider-register.md)o alle [VM in blocco](sql-vm-resource-provider-bulk-register.md). 

## <a name="overview"></a>Panoramica

L'estensione SQL Server Agent IaaS offre numerosi vantaggi per SQL Server in macchine virtuali di Azure: 

- **Vantaggi della funzionalità** : l'estensione consente di sbloccare diversi vantaggi della funzionalità di automazione, ad esempio la gestione del portale, la flessibilità delle licenze, il backup automatico, l'applicazione automatica di patch e altro ancora. Per informazioni dettagliate, vedere [vantaggi della funzionalità](#feature-benefits) più avanti in questo articolo. 

- **Conformità** : l'estensione offre un metodo semplificato per soddisfare la richiesta di notifica a Microsoft che la vantaggio Azure Hybrid è stata abilitata come indicato nelle condizioni del prodotto. Questo processo rimuove la necessità di gestire i moduli di registrazione delle licenze per ogni risorsa.  

- **Gratuito** : l'estensione in tutte e tre le modalità di gestibilità è completamente gratuita. Non sono previsti costi aggiuntivi per il provider di risorse o con modalità di gestione diverse. 

- **Gestione semplificata delle licenze** : l'estensione semplifica SQL Server la gestione delle licenze e consente di identificare rapidamente SQL Server VM con la vantaggio Azure Hybrid abilitata usando il [portale di Azure](manage-sql-vm-portal.md), l'interfaccia della riga di comando di Azure o PowerShell: 

   # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---


> [!IMPORTANT]
> L'estensione SQL IaaS Agent raccoglie i dati per lo scopo esplicito di fornire ai clienti vantaggi facoltativi quando si usa SQL Server all'interno di macchine virtuali di Azure. Microsoft non utilizzerà questi dati per i controlli delle licenze senza il consenso anticipato del cliente. Per ulteriori informazioni, vedere la [SQL Server supplemento sulla privacy](/sql/sql-server/sql-server-privacy#non-personal-data) .


## <a name="feature-benefits"></a>Vantaggi delle funzionalità 

Il SQL Server estensione dell'agente IaaS sblocca diversi vantaggi di funzionalità per la gestione della SQL Server VM. 

La tabella seguente illustra i vantaggi seguenti: 


| Funzionalità | Descrizione |
| --- | --- |
| **Gestione nel portale** | Sblocca [la gestione nel portale](manage-sql-vm-portal.md), in modo che sia possibile visualizzare tutte le macchine virtuali SQL Server in un'unica posizione, in modo che sia possibile abilitare e disabilitare funzionalità specifiche di SQL direttamente dal portale. 
| **Backup automatico** |Consente di automatizzare la pianificazione delle operazioni di backup per tutti i database correlati all'istanza predefinita o a un'istanza denominata [correttamente installata](frequently-asked-questions-faq.md#administration) di SQL Server nella macchina virtuale. Per altre informazioni, vedere l'articolo [Backup automatico per SQL Server in macchine virtuali di Azure (Resource Manager)](automated-backup-sql-2014.md). |
| **Applicazione automatica delle patch** |Configura una finestra di manutenzione durante la quale è possibile eseguire aggiornamenti importanti di Windows e SQL Server della sicurezza per la macchina virtuale, in modo da evitare gli aggiornamenti durante i periodi di picco del carico di lavoro. Per altre informazioni, vedere l'articolo [Applicazione automatica delle patch per SQL Server in macchine virtuali di Azure (Resource Manager)](automated-patching.md). |
| **Integrazione di Azure Key Vault** |Consente di installare e configurare automaticamente l'insieme di credenziali delle chiavi di Azure nella VM di SQL Server. Per altre informazioni, vedere l'articolo [Configurare l'integrazione di Azure Key Vault per SQL Server in macchine virtuali di Azure (Resource Manager)](azure-key-vault-integration-configure.md). |
| **Licenze flessibili** | Risparmia sui costi grazie alla [transizione senza interruzioni](licensing-model-azure-hybrid-benefit-ahb-change.md) dalla tua licenza Bring your own License (nota anche come vantaggio Azure Hybrid) al modello di licenza con pagamento in base al consumo e viceversa. | 
| **Versione flessibile/edizione** | Se si decide di modificare la [versione](change-sql-server-version.md) o l' [edizione](change-sql-server-edition.md) di SQL Server, è possibile aggiornare i metadati all'interno dell'portale di Azure senza dover ridistribuire l'intera macchina virtuale SQL Server.  | 


## <a name="management-modes"></a>Modalità di gestione

L'estensione SQL IaaS dispone di tre modalità di gestione:

- La modalità **semplificata** non richiede il riavvio di SQL Server, ma supporta solo la modifica del tipo di licenza e dell'edizione di SQL Server. Usare questa opzione per le macchine virtuali SQL Server con più istanze o per partecipare a un'istanza del cluster di failover (FCI). Questa modalità di gestione mantiene i file binari dell'estensione SQL IaaS Agent nel computer, ma non installa l'agente. La modalità Lightweight è la modalità di gestione predefinita quando si utilizza la funzionalità di [registrazione automatica](sql-vm-resource-provider-automatic-registration.md) oppure quando un tipo di gestione non viene specificato durante la registrazione manuale. Non vi è alcun impatto sulla memoria o sulla CPU quando si usa la modalità semplificata e non vi sono costi associati. Si consiglia di eseguire prima la registrazione della macchina virtuale di SQL Server in modalità semplificata, quindi di eseguire l'aggiornamento alla modalità completa durante una finestra di manutenzione pianificata.

- La modalità **completa** offre tutte le funzionalità, ma richiede il riavvio di SQL Server e le autorizzazioni di amministratore di sistema. Usarla per gestire una macchina virtuale di SQL Server con una singola istanza. La modalità completa installa due servizi Windows che hanno un effetto minimo sulla memoria e sulla CPU, monitorabili tramite Gestione attività. Non è previsto alcun costo associato all'uso della modalità di gestione completa. 

- La modalità **NoAgent** è dedicata a SQL Server 2008 e SQL Server 2008 R2 installati in Windows Server 2008. Non vi è alcun effetto sulla memoria o sulla CPU quando si usa la modalità NoAgent. Non è previsto alcun costo associato all'uso della modalità di gestione NoAgent. 

È possibile visualizzare la modalità corrente dell'agente di SQL Server IaaS usando Azure PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="installation"></a>Installazione

Quando si registrano le VM SQL Server con il provider di risorse VM SQL, viene installata l'SQL Server estensione agente IaaS. La registrazione con il provider di risorse crea la _risorsa_ della **macchina virtuale SQL** nella sottoscrizione, che è una risorsa _separata_ dalla risorsa della macchina virtuale. Se si annulla la registrazione della macchina virtuale SQL Server dal provider di risorse, si rimuoverà la **risorsa** _macchina virtuale SQL_ , ma la macchina virtuale effettiva non verrà eliminata.

La distribuzione di un'immagine di Azure Marketplace di macchina virtuale SQL Server tramite il portale di Azure registra automaticamente una macchina virtuale di SQL Server con il provider di risorse. Tuttavia, se si sceglie di installare autonomamente SQL Server in una macchina virtuale di Azure o di effettuare il provisioning di una macchina virtuale di Azure da un disco rigido virtuale personalizzato, è necessario registrare la VM SQL Server con il provider di risorse VM SQL per installare l'estensione SQL IaaS Agent. 

Per installare l'estensione, registrare la macchina virtuale SQL Server con il provider di risorse:
- [Automaticamente per tutte le macchine virtuali correnti e future in una sottoscrizione](sql-vm-resource-provider-automatic-registration.md)
- [Per una singola macchina virtuale](sql-vm-resource-provider-register.md)
- [Per più macchine virtuali in blocco](sql-vm-resource-provider-bulk-register.md)

### <a name="named-instance-support"></a>Supporto per istanze denominate

L'estensione SQL Server IaaS funzionerà con un'istanza denominata di SQL Server se è l'unica istanza di SQL Server disponibile nella macchina virtuale. L'installazione dell'estensione non verrà eseguita nelle macchine virtuali che dispongono di più istanze di SQL Server. 

Per usare un'istanza denominata di SQL Server, distribuire una macchina virtuale di Azure, installare una singola istanza di SQL Server denominata e quindi registrarla con il [provider di risorse VM SQL](sql-vm-resource-provider-register.md) per installare l'estensione.

In alternativa, per usare un'istanza denominata con un'immagine del SQL Server di Azure Marketplace, seguire questa procedura: 

   1. Distribuire una macchina virtuale di SQL Server da Azure Marketplace. 
   1. [Annullare la registrazione](sql-vm-resource-provider-register.md#unregister-from-rp) della macchina virtuale SQL Server dal provider di risorse della macchina virtuale SQL. 
   1. Disinstallare completamente SQL Server nella macchina virtuale di SQL Server.
   1. Installare SQL Server con un'istanza denominata nella macchina virtuale di SQL Server. 
   1. Installare l'estensione SQL IaaS Agent [registrando la macchina virtuale SQL Server con il provider di risorse VM SQL](sql-vm-resource-provider-register.md#register-with-rp). 

## <a name="verify-status-of-extension"></a>Verificare lo stato dell'estensione

Usare il portale di Azure o Azure PowerShell per verificare lo stato dell'estensione. 


### <a name="azure-portal"></a>Portale di Azure

Verificare che l'estensione sia installata nell'portale di Azure. 

Selezionare **tutte le impostazioni** nel riquadro macchina virtuale e quindi fare clic su **estensioni**. L'estensione **SQLIaaSExtension** dovrebbe essere visualizzata nell'elenco.

![Stato del SQL Server estensione dell'agente IaaS nella portale di Azure](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

È anche possibile usare il cmdlet **Get-AzVMSqlServerExtension** di Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Il comando precedente verifica se l'agente è installato e visualizza informazioni generali sullo stato. È anche possibile ottenere informazioni specifiche sullo stato del backup e dell'applicazione di patch in modalità automatica usando i comandi seguenti:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>Limitazioni

L'estensione SQL IaaS Agent supporta solo: 

- Macchine virtuali di SQL Server distribuite tramite Azure Resource Manager. Le macchine virtuali di SQL Server distribuite tramite il modello classico non sono supportate. 
- Macchine virtuali di SQL Server distribuite nel cloud pubblico o Azure per enti pubblici. Le distribuzioni in altri cloud privati o governativi non sono supportate. 


## <a name="frequently-asked-questions"></a>Domande frequenti 

**È necessario registrare la macchina virtuale di SQL Server sottoposta a provisioning da un'immagine di SQL Server in Azure Marketplace?**

No. Microsoft registra automaticamente le macchine virtuali di cui è stato eseguito il provisioning dalle immagini di SQL Server in Azure Marketplace. La registrazione con il provider di risorse macchine virtuali SQL è necessaria solo se la macchina virtuale *non* è stata sottoposta a provisioning dalle immagini SQL Server in Azure Marketplace e SQL Server è stato installato autonomamente.

**Il provider di risorse di macchine virtuali SQL è disponibile per tutti i clienti?** 

Sì. I clienti devono registrare le proprie macchine virtuali SQL Server con il provider di risorse macchine virtuali SQL se non hanno usato un'immagine di SQL Server da Azure Marketplace ma dispongono invece di SQL Server installato automaticamente o hanno usato un disco rigido virtuale personalizzato. Le macchine virtuali di proprietà di tutti i tipi di sottoscrizioni (Direct, Contratto Enterprise e Cloud Solution Provider) possono eseguire la registrazione con il provider di risorse macchine virtuali SQL.

**Qual è la modalità di gestione predefinita durante la registrazione con il provider di risorse macchine virtuali SQL?**

La modalità di gestione predefinita quando si esegue la registrazione con il provider di risorse VM SQL è *leggera*. Se la proprietà gestione SQL Server non è impostata quando si esegue la registrazione con il provider di risorse VM SQL, la modalità verrà impostata come Lightweight e il servizio di SQL Server non verrà riavviato. Si consiglia di eseguire prima la registrazione con il provider di risorse di macchine virtuali SQL in modalità semplificata, quindi di eseguire l'aggiornamento alla modalità completa durante una finestra di manutenzione. Analogamente, anche la gestione predefinita è leggera quando si usa la [funzionalità di registrazione automatica](sql-vm-resource-provider-automatic-registration.md).

**Quali sono i prerequisiti per la registrazione con il provider di risorse macchine virtuali SQL?**

Non sono previsti prerequisiti per la registrazione con il provider di risorse VM SQL, ad eccezione del fatto che SQL Server installato nella macchina virtuale. Si noti che se l'estensione SQL IaaS Agent è installata in modalità completa, il servizio SQL Server verrà riavviato, pertanto è consigliabile eseguire questa operazione durante una finestra di manutenzione.

**La registrazione con il provider di risorse macchine virtuali SQL installerà un agente nella macchina virtuale?**

Sì, la registrazione con il provider di risorse VM SQL in modalità di gestibilità completa consente di installare un agente nella macchina virtuale. La registrazione in modalità lightweight o noagent non lo è. 

La registrazione con il provider di risorse VM SQL in modalità Lightweight consente di copiare solo i *file binari* dell'estensione SQL IaaS Agent nella macchina virtuale, ma non di installare l'agente. Questi file binari vengono quindi utilizzati per installare l'agente quando la modalità di gestione viene aggiornata a piena.


**La registrazione con il provider di risorse VM SQL riavvierà SQL Server nella macchina virtuale?**

Dipende dalla modalità specificata durante la registrazione. Se si specifica la modalità lightweight o noagent, il servizio SQL Server non verrà riavviato. Tuttavia, se si specifica la modalità di gestione come completa, il servizio di SQL Server verrà riavviato. La funzionalità di registrazione automatica registra le macchine virtuali SQL Server in modalità Lightweight, a meno che la versione di Windows Server non sia 2008, nel qual caso la VM SQL Server verrà registrata in modalità noagent. 

**Qual è la differenza tra le modalità di gestione Lightweight e noagent durante la registrazione con il provider di risorse VM SQL?** 

La modalità di gestione noagent è l'unica modalità di gestione disponibile per SQL Server 2008 e SQL Server 2008 R2 in Windows Server 2008. Per tutte le versioni successive di Windows Server, le due modalità di gestione disponibili sono leggero e pieno. 

Per la modalità noagent sono richieste SQL Server proprietà Version ed Edition impostate dal cliente. La modalità semplificata esegue una query sulla macchina virtuale per trovare la versione e l'edizione dell'istanza di SQL Server.

**È possibile eseguire la registrazione con il provider di risorse macchine virtuali SQL senza specificare il tipo di licenza di SQL Server?**

No. Il tipo di licenza di SQL Server non è una proprietà facoltativa quando si esegue la registrazione con il provider di risorse della macchina virtuale SQL. È necessario impostare il tipo di licenza SQL Server come Vantaggio Azure Hybrid con pagamento in base al consumo o quando si esegue la registrazione con il provider di risorse VM SQL in tutte le modalità di gestibilità (noagent, leggero e completo). Se è installata una delle versioni gratuite di SQL Server, ad esempio Developer o Evaluation Edition, è necessario registrarsi con le licenze con pagamento in base al consumo. Vantaggio Azure Hybrid è disponibile solo per le versioni a pagamento di SQL Server, ad esempio le edizioni Enterprise e standard.

**È possibile aggiornare l'estensione SQL Server IaaS dalla modalità noagent alla modalità completa?**

No. L'aggiornamento della modalità di gestibilità a full o Lightweight non è disponibile per la modalità noagent. Si tratta di una limitazione tecnica di Windows Server 2008. Sarà necessario aggiornare prima il sistema operativo a Windows Server 2008 R2 o una versione successiva per potere poi eseguire l'aggiornamento alla modalità di gestione completa. 

**È possibile aggiornare l'estensione IaaS di SQL Server dalla modalità semplificata alla modalità completa?**

Sì. L'aggiornamento della modalità di gestibilità da Lightweight a Full è supportato tramite Azure PowerShell o l'portale di Azure. Verrà attivato il riavvio del servizio SQL Server.

**È possibile effettuare il downgrade del SQL Server estensione IaaS dalla modalità completa a noagent o alla modalità di gestione Lightweight?**

No. Il downgrade della modalità di gestibilità dell'estensione IaaS di SQL Server non è supportato. Non è possibile effettuare il downgrade della modalità di gestibilità dalla modalità completa alla modalità lightweight o noagent e non è possibile effettuare il downgrade dalla modalità Lightweight alla modalità noagent. 

Per modificare la modalità di gestibilità dalla gestione completa, [annullare la registrazione](sql-vm-resource-provider-register.md#unregister-from-rp) della macchina virtuale SQL Server dal provider di risorse VM SQL eliminando la _risorsa_ della macchina virtuale SQL e registrare nuovamente la VM SQL Server con il provider di risorse VM SQL in una modalità di gestione diversa.

**È possibile eseguire la registrazione con il provider di risorse macchine virtuali SQL dal portale di Azure?**

No. La registrazione con il provider di risorse macchine virtuali SQL non è disponibile nel portale di Azure. La registrazione con il provider di risorse VM SQL è supportata solo con l'interfaccia della riga di comando di Azure o con Azure PowerShell. 

**È possibile registrare una macchina virtuale con il provider di risorse macchine virtuali SQL prima di installare SQL Server?**

No. Per eseguire correttamente la registrazione con il provider di risorse VM SQL, una macchina virtuale deve avere almeno un'istanza SQL Server (motore di database). Se non è presente alcuna istanza di SQL Server nella macchina virtuale, la nuova risorsa Microsoft.SqlVirtualMachine avrà uno stato di errore.

**È possibile registrare una macchina virtuale con il provider di risorse macchine virtuali SQL se sono presenti più istanze di SQL Server?**

Sì. Il provider di risorse macchine virtuali SQL registrerà una sola istanza di SQL Server (motore di database). Il provider di risorse macchine virtuali SQL registrerà l'istanza predefinita di SQL Server nel caso di più istanze. Se non è presente alcuna istanza predefinita, è supportata solo la registrazione in modalità semplificata. Per eseguire l'aggiornamento dalla modalità semplificata alla gestione completa, è necessario che sia presente l'istanza predefinita di SQL Server o che la macchina virtuale disponga di una sola istanza denominata SQL Server.

**È possibile registrare un'istanza del cluster di failover di SQL Server con il provider di risorse macchine virtuali SQL?**

Sì. Le istanze del cluster di failover di SQL Server in una macchina virtuale di Azure possono essere registrate con il provider di risorse macchine virtuali SQL in modalità semplificata. Tuttavia, le istanze del cluster di failover di SQL Server non possono essere aggiornate alla modalità di gestione completa.

**È possibile registrare la macchina virtuale con il provider di risorse macchine virtuali SQL se è configurato un gruppo di disponibilità Always On?**

Sì. Non sono presenti restrizioni per la registrazione di un'istanza di SQL Server in una macchina virtuale di Azure con il provider di risorse macchine virtuali SQL se si partecipa a una configurazione del gruppo di disponibilità Always On.

**Qual è il costo per la registrazione con il provider di risorse macchine virtuali SQL o con l'aggiornamento alla modalità di gestibilità completa?**

No. Non sono previste tariffe associate alla registrazione con il provider di risorse macchine virtuali SQL o con una delle tre modalità di gestibilità. La gestione della macchina virtuale SQL Server con il provider di risorse è completamente gratuita. 

**Qual è l'effetto sulle prestazioni dell'uso delle diverse modalità di gestibilità?**

Non vi è alcun effetto quando si usano le modalità di gestione *NoAgent* e *semplificata*. Quando si usa la modalità di gestione *completa* da due servizi installati nel sistema operativo, l'effetto è minimo. Questi servizi sono monitorabili tramite Gestione attività e possono essere visualizzati nella console Servizi Windows incorporata. 

I due nomi di servizio sono:
- `SqlIaaSExtensionQuery` (Nome visualizzato: `Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension` (Nome visualizzato: `Microsoft SQL Server IaaS Agent`)

**Ricerca per categorie rimuovere l'estensione?**

Rimuovere l'estensione [annullando la registrazione](sql-vm-resource-provider-register.md#unregister-from-rp) della macchina virtuale SQL Server dal provider di risorse della macchina virtuale SQL. 

## <a name="next-steps"></a>Passaggi successivi

Per installare l'estensione IaaS SQL Server per SQL Server in macchine virtuali di Azure, vedere gli articoli relativi all' [installazione automatica](sql-vm-resource-provider-automatic-registration.md), alle [macchine virtuali singole](sql-vm-resource-provider-register.md)o alle [VM in blocco](sql-vm-resource-provider-bulk-register.md).

Per altre informazioni sull'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [Cos'è SQL Server in Macchine virtuali di Azure?](sql-server-on-azure-vm-iaas-what-is-overview.md).
