---
title: Eseguire la migrazione di Servizi di dominio Azure AD da una rete virtuale classica. Documenti Microsoft
description: Informazioni su come eseguire la migrazione di un'istanza di dominio gestito di Servizi di dominio Azure AD esistente dal modello di rete virtuale classica a una rete virtuale basata su Resource Manager.Learn how to migrate an existing Azure AD Domain Services managed domain instance from the Classic virtual network model to a Resource Manager-based virtual network.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 6acf9301367ae2c6947f6935c43f420d3d7cac65
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655025"
---
# <a name="migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Eseguire la migrazione di Servizi di dominio Azure AD dal modello di rete virtuale classica a Resource ManagerMigrate Azure AD Domain Services from the Classic virtual network model to Resource Manager

Servizi di dominio Azure Active Directory supporta uno spostamento una tantera per i clienti che attualmente utilizza il modello di rete virtuale classica nel modello di rete virtuale di Resource Manager.Azure Active Directory Domain Services (AD DS) supports a one time move for customers currently using the Classic virtual network model to the Resource Manager virtual network model. I domini gestiti di Servizi di dominio Active Directory di Azure che usano il modello di distribuzione di Resource Manager forniscono funzionalità aggiuntive, ad esempio criteri granulari per le password, log di controllo e protezione del blocco degli account.

Questo articolo descrive i vantaggi e le considerazioni per la migrazione, quindi i passaggi necessari per eseguire correttamente la migrazione di un'istanza di Servizi di dominio Azure AD esistente.

> [!NOTE]
> Nel 2017, Servizi di dominio Azure AD è diventato disponibile per l'hosting in una rete di Azure Resource Manager.In 2017, Azure AD Domain Services became available to host in an Azure Resource Manager network. Da allora, siamo stati in grado di creare un servizio più sicuro usando le funzionalità moderne di Azure Resource Manager.From then, we are been able to build a more secure service using the Azure Resource Manager's modern capabilities. Poiché le distribuzioni di Azure Resource Manager sostituiscono completamente le distribuzioni classiche, le distribuzioni di reti virtuali classiche di Servizi di dominio Active Directory di Azure verranno ritirate il 1 marzo 2023.Because Azure Resource Manager deployments fully replace classic deployments, Azure AD DS classic virtual network deployments will be retired on March 1, 2023.
>
> Per ulteriori informazioni, consultare [l'avviso ufficiale di deprecazione](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="overview-of-the-migration-process"></a>Panoramica del processo di migrazione

Il processo di migrazione accetta un'istanza di Servizi di dominio Active Directory di Azure esistente che viene eseguita in una rete virtuale classica e la sposta in una rete virtuale di Resource Manager esistente. La migrazione viene eseguita utilizzando PowerShell e dispone di due fasi principali di esecuzione: *preparazione* e *migrazione*.

![Panoramica del processo di migrazione per Azure AD DSOverview of the migration process for Azure AD DS](media/migrate-from-classic-vnet/migration-overview.png)

Nella fase di *preparazione,* Servizi di dominio Active Directory di Azure esegue un backup del dominio per ottenere lo snapshot più recente di utenti, gruppi e password sincronizzati con il dominio gestito. La sincronizzazione viene quindi disabilitata e il servizio cloud che ospita il dominio gestito di Servizi di dominio Active Directory di Azure viene eliminato. Durante la fase di preparazione, il dominio gestito di Azure AD DS non è in grado di autenticare gli utenti.

![Fase di preparazione per la migrazione di Azure AD DS](media/migrate-from-classic-vnet/migration-preparation.png)

Nella fase *di migrazione,* i dischi virtuali sottostanti per i controller di dominio dal dominio gestito di Servizi di dominio Active Directory di Azure classici vengono copiati per creare le macchine virtuali usando il modello di distribuzione di Resource Manager.In the migration stage, the underlying virtual disks for the domain controllers from the Classic Azure AD DS managed domain are copied to create the VMs using the Resource Manager deployment model. Il dominio gestito di Servizi di dominio Active Directory di Azure viene quindi ricreato, che include la configurazione LDAPS e DNS. La sincronizzazione con Azure AD viene riavviata e i certificati LDAP vengono ripristinati. Non è necessario aggiungere nuovamente i computer a un dominio gestito di Servizi di dominio Active Directory di Azure: continuano a essere aggiunti al dominio gestito ed eseguiti senza modifiche.

![Migrazione di Azure AD DS](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>Vantaggi della migrazione

Quando si sposta un dominio gestito di Servizi di dominio Active Directory di Azure usando questo processo di migrazione, si evita la necessità di aggiungere nuovamente i computer al dominio gestito o eliminare l'istanza di Servizi di dominio Azure AD e crearne una da zero. Le macchine virtuali continuano a essere aggiunte al dominio gestito di Azure AD DS al termine del processo di migrazione.

Dopo la migrazione, Azure AD DS offre molte funzionalità disponibili solo per i domini che usano reti virtuali di Resource Manager, ad esempio:After migration, Azure AD DS provides many features that are only available for domains using Resource Manager virtual networks, such as:

* Supporto dei criteri granulari per le password.
* Protezione di blocco degli account AD.
* Notifiche tramite posta elettronica degli avvisi nel dominio gestito di Servizi di dominio Active Directory di Azure.Email notifications of alerts on the Azure AD DS managed domain.
* Log di controllo tramite Monitoraggio di Azure.Audit logs using Azure Monitor.
* Integrazione di File di Azure
* Integrazione con HD Insights

I domini gestiti di Servizi di dominio Active Directory di Azure che usano una rete virtuale di Resource Manager consentono di rimanere aggiornati con le nuove funzionalità più recenti. Il supporto per Servizi di dominio Active Directory di Azure tramite reti virtuali classiche deve essere deprecato in futuro.

## <a name="example-scenarios-for-migration"></a>Scenari di esempio per la migrazioneExample scenarios for migration

Di seguito sono riportati alcuni scenari comuni per la migrazione di un dominio gestito di Servizi di dominio Active Directory di Azure.Some common scenarios for migrating an Azure AD DS managed domain include the following examples.

> [!NOTE]
> Non convertire la rete virtuale classica fino a quando non è stata confermata una migrazione riuscita. La conversione della rete virtuale rimuove l'opzione per eseguire il rollback o il ripristino del dominio gestito di Servizi di dominio Active Directory di Azure in caso di problemi durante le fasi di migrazione e verifica.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Eseguire la migrazione di Azure AD DS a una rete virtuale di Resource Manager esistente (scelta consigliata)Migrate Azure AD DS to an existing Resource Manager virtual network (recommended)

Uno scenario comune è quello in cui sono già state spostate altre risorse classiche esistenti in un modello di distribuzione e una rete virtuale di Resource Manager.A common scenario is where you've already moved other existing Classic resources to a Resource Manager deployment model and virtual network. Peering is then used from the Resource Manager virtual network to the Classic virtual network that continues to run Azure AD DS. Questo approccio consente alle applicazioni e ai servizi di Resource Manager di usare la funzionalità di autenticazione e gestione del dominio gestito di Servizi di dominio Active Directory di Azure nella rete virtuale classica. Dopo la migrazione, tutte le risorse vengono eseguite usando il modello di distribuzione e la rete virtuale di Resource Manager.Once migrated, all resources run using the Resource Manager deployment model and virtual network.

![Eseguire la migrazione di Azure AD DS a una rete virtuale di Resource Manager esistenteMigrate Azure AD DS to an existing Resource Manager virtual network](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

I passaggi di alto livello coinvolti in questo scenario di migrazione di esempio includono le parti seguenti:High-level steps involved in this example migration scenario include the following parts:

1. Rimuovere i gateway VPN esistenti o il peering della rete virtuale configurato nella rete virtuale classica.
1. Eseguire la migrazione del dominio gestito di Servizi di dominio Active Directory di Azure seguendo la procedura descritta in questo articolo.
1. Testare e confermare una migrazione corretta, quindi eliminare la rete virtuale classica.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Eseguire la migrazione di più risorse, tra cui Azure AD DSMigrate multiple resources including Azure AD DS

In questo scenario di esempio viene eseguita la migrazione di Servizi di dominio Active Directory di Azure e di altre risorse associate dal modello di distribuzione classica al modello di distribuzione di Resource Manager.In this example scenario, you migrate Azure AD DS and other associated resources from the Classic deployment model to the Resource Manager deployment model. Se alcune risorse hanno continuato a essere eseguite nella rete virtuale classica insieme al dominio gestito di Servizi di dominio Active Directory di Azure, possono trarre vantaggio dalla migrazione al modello di distribuzione di Resource Manager.If some resources continued to run in the Classic virtual network alongside the Azure AD DS managed domain, they can all benefit from migrating to the Resource Manager deployment model.

![Eseguire la migrazione di più risorse al modello di distribuzione di Resource ManagerMigrate multiple resources to the Resource Manager deployment model](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

I passaggi di alto livello coinvolti in questo scenario di migrazione di esempio includono le parti seguenti:High-level steps involved in this example migration scenario include the following parts:

1. Rimuovere i gateway VPN esistenti o il peering della rete virtuale configurato nella rete virtuale classica.
1. Eseguire la migrazione del dominio gestito di Servizi di dominio Active Directory di Azure seguendo la procedura descritta in questo articolo.
1. Configurare il peering della rete virtuale tra la rete virtuale classica e la rete di Resource Manager.Set up virtual network peering between the Classic virtual network and Resource Manager network.
1. Testare e confermare una migrazione corretta.
1. [Spostare altre risorse classiche, ad esempio le macchine virtuali.][migrate-iaas]

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Eseguire la migrazione di Azure AD DS ma mantenere altre risorse nella rete virtuale classicaMigrate Azure AD DS but keep other resources on the Classic virtual network

In questo scenario di esempio, si dispone della quantità minima di tempo di inattività in una sessione. Eseguire solo la migrazione di Azure AD DS a una rete virtuale di Resource Manager e mantenere le risorse esistenti nel modello di distribuzione classica e nella rete virtuale. In un periodo di manutenzione seguente, è possibile eseguire la migrazione delle risorse aggiuntive dal modello di distribuzione classica e dalla rete virtuale come desiderato.

![Eseguire la migrazione solo di Azure AD DS al modello di distribuzione di Resource ManagerMigrate only Azure AD DS to the Resource Manager deployment model](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

I passaggi di alto livello coinvolti in questo scenario di migrazione di esempio includono le parti seguenti:High-level steps involved in this example migration scenario include the following parts:

1. Rimuovere i gateway VPN esistenti o il peering della rete virtuale configurato nella rete virtuale classica.
1. Eseguire la migrazione del dominio gestito di Servizi di dominio Active Directory di Azure seguendo la procedura descritta in questo articolo.
1. Configurare il peering della rete virtuale tra la rete virtuale classica e la nuova rete virtuale di Resource Manager.Set up virtual network peering between the Classic virtual network and the new Resource Manager virtual network.
1. Successivamente, [eseguire la migrazione delle risorse aggiuntive][migrate-iaas] dalla rete virtuale classica in base alle esigenze.

## <a name="before-you-begin"></a>Prima di iniziare

Durante la preparazione e quindi la migrazione di un dominio gestito di Servizi di dominio Active Directory di Azure, esistono alcune considerazioni sulla disponibilità dei servizi di autenticazione e gestione. Il dominio gestito di Azure AD DS non è disponibile per un periodo di tempo durante la migrazione. Applicazioni e servizi che si basano sull'esperienza di Servizi di dominio Active Directory di Azure durante la migrazione.

> [!IMPORTANT]
> Leggere tutto questo articolo sulla migrazione e le linee guida per avviare il processo di migrazione. Il processo di migrazione influisce sulla disponibilità dei controller di dominio di Azure AD DS per periodi di tempo. Gli utenti, i servizi e le applicazioni non possono eseguire l'autenticazione nel dominio gestito durante il processo di migrazione.

### <a name="ip-addresses"></a>Indirizzi IP

Gli indirizzi IP del controller di dominio per una modifica del dominio gestito di Azure AD DS dopo la migrazione. Questa modifica include l'indirizzo IP pubblico per l'endpoint LDAP sicuro. I nuovi indirizzi IP si trovano all'interno dell'intervallo di indirizzi per la nuova subnet nella rete virtuale di Resource Manager.The new IP addresses are inside the address range for the new subnet in the Resource Manager virtual network.

In caso di rollback, gli indirizzi IP possono cambiare dopo il rollback.

Servizi di dominio Active Directory di Azure usa in genere i primi due indirizzi IP disponibili nell'intervallo di indirizzi, ma ciò non è garantito. Non è attualmente possibile specificare gli indirizzi IP da utilizzare dopo la migrazione.

### <a name="downtime"></a>Tempo di inattività

Il processo di migrazione prevede che i controller di dominio siano offline per un periodo di tempo. I controller di dominio non sono accessibili durante la migrazione di Azure AD DS al modello di distribuzione e alla rete virtuale di Resource Manager.Domain controllers are inaccessible while Azure AD DS is migrated to the Resource Manager deployment model and virtual network. In media, il tempo di inattività è di circa 1 a 3 ore. Questo periodo di tempo è da quando i controller di dominio vengono portati offline al momento in cui il primo controller di dominio torna in linea. Questa media non include il tempo necessario per la replica del secondo controller di dominio o il tempo necessario per eseguire la migrazione di risorse aggiuntive al modello di distribuzione di Resource Manager.This average doesn't include the time it takes for the second domain controller to replicate, or the time it take to migrate additional resources to the Resource Manager deployment model.

### <a name="account-lockout"></a>Blocco dell'account

I domini gestiti di Servizi di dominio Active Directory eseguiti nelle reti virtuali classiche non dispongono di criteri di blocco degli account di Active Directory.Azure AD DS managed domains that run on Classic virtual networks don't have AD account lockout policies in place. Se le macchine virtuali sono esposte a Internet, gli utenti malintenzionati potrebbero utilizzare metodi di spruzzo di password per forzare la loro strada negli account. Non sono disponibili criteri di blocco degli account per arrestare tali tentativi. Per i domini gestiti di Servizi di dominio Active Directory che usano il modello di distribuzione di Resource Manager e le reti virtuali, i criteri di blocco degli account di Active Directory proteggono da questi attacchi con spruzzo di password.

Per impostazione predefinita, 5 tentativi con password errata in 2 minuti bloccano un account per 30 minuti.

Un account bloccato non può essere usato per accedere, il che può interferire con la possibilità di gestire il dominio gestito o le applicazioni di Servizi di dominio Active Directory di Azure gestite dall'account. Dopo la migrazione di un dominio gestito di Servizi di dominio Active Directory di Azure, gli account possono sperimentare quello che si sente come un blocco permanente a causa di ripetuti tentativi di accesso non riusciti. Due scenari comuni dopo la migrazione sono i seguenti:Two common scenarios after migration include the following:

* Un account di servizio che utilizza una password scaduta.
    * L'account del servizio tenta ripetutamente di accedere con una password scaduta, che blocca l'account. Per risolvere il problema, individuare l'applicazione o la macchina virtuale con credenziali scadute e aggiornare la password.
* Un'entità dannosa utilizza tentativi di forza bruta per accedere agli account.
    * Quando le macchine virtuali sono esposte a Internet, gli utenti malintenzionati spesso provano combinazioni di nome utente e password comuni mentre tentano di firmare. Questi ripetuti tentativi di accesso non riusciti possono bloccare gli account. Non è consigliabile utilizzare account amministratore con nomi generici, ad esempio *admin* o *administrator,* ad esempio per ridurre al minimo il blocco degli account amministrativi.
    * Ridurre al minimo il numero di macchine virtuali esposte a Internet.Minimize the number of VMs that are exposed to the internet. È possibile usare Azure Bastion per connettersi in modo sicuro alle macchine virtuali usando il portale di Azure.You can use [Azure Bastion][azure-bastion] to securely connect to VMs using the Azure portal.

Se si sospetta che alcuni account potrebbero essere bloccati dopo la migrazione, i passaggi finali della migrazione illustrano come abilitare il controllo o modificare le impostazioni dei criteri granulari per le password.

### <a name="roll-back-and-restore"></a>Eseguire il rollback e il ripristino

Se la migrazione non riesce, viene eseguito il rollback o il ripristino di un dominio gestito di Azure AD DS. Il rollback è un'opzione self-service per restituire immediatamente lo stato del dominio gestito prima del tentativo di migrazione. I tecnici del supporto di Azure possono anche ripristinare un dominio gestito dal backup come ultima risorsa. Per ulteriori informazioni, vedere come eseguire il rollback o il [ripristino da una migrazione non riuscita.](#roll-back-and-restore-from-migration)

### <a name="restrictions-on-available-virtual-networks"></a>Restrizioni sulle reti virtuali disponibili

Esistono alcune restrizioni sulle reti virtuali in cui è possibile eseguire la migrazione di un dominio gestito di Servizi di dominio Active Directory di Azure.There are some restrictions on the virtual networks that an Azure AD DS managed domain can be migrated to. La rete virtuale di Resource Manager di destinazione deve soddisfare i requisiti seguenti:The destination Resource Manager virtual network must meet the following requirements:

* La rete virtuale di Resource Manager deve trovarsi nella stessa sottoscrizione di Azure della rete virtuale classica in cui è attualmente distribuita la rete virtuale di Azure AD.
* La rete virtuale di Resource Manager deve trovarsi nella stessa area della rete virtuale classica in cui è attualmente distribuito Servizi di dominio Active Directory di Azure.The Resource Manager virtual network must be in the same region as the Classic virtual network that An AD DS is currently deployed in.
* La subnet della rete virtuale di Resource Manager deve avere almeno 3-5 indirizzi IP disponibili.
* La subnet della rete virtuale di Resource Manager deve essere una subnet dedicata per Servizi di dominio Active Directory di Azure e non deve ospitare altri carichi di lavoro.

Per altre informazioni sui requisiti della rete virtuale, vedere Considerazioni sulla [progettazione][network-considerations]della rete virtuale e opzioni di configurazione .

## <a name="migration-steps"></a>Passaggi della migrazione

La migrazione al modello di distribuzione e alla rete virtuale di Resource Manager è suddivisa in 5 passaggi principali:The migration to the Resource Manager deployment model and virtual network is split into 5 main steps:

| Passaggio    | Eseguito tramite  | Tempo stimato  | Tempo di inattività  | Eseguire il rollback/ripristino? |
|---------|--------------------|-----------------|-----------|-------------------|
| [Passaggio 1 - Aggiornare e individuare la nuova rete virtualeStep 1 - Update and locate the new virtual network](#update-and-verify-virtual-network-settings) | Portale di Azure | 15 minuti | Nessun tempo di inattività richiesto | N/D |
| [Passaggio 2 - Preparare il dominio gestito di Azure AD DS per la migrazioneStep 2 - Prepare the Azure AD DS managed domain for migration](#prepare-the-managed-domain-for-migration) | PowerShell | 15 – 30 minuti in media | Il tempo di inattività di Servizi di dominio Active Directory di Azure viene avviato dopo il completamento di questo comando. | Ripristino e ripristino disponibili. |
| [Passaggio 3 - Spostare il dominio gestito di Azure AD DS in una rete virtuale esistenteStep 3 - Move the Azure AD DS managed domain to an existing virtual network](#migrate-the-managed-domain) | PowerShell | 1 – 3 ore in media | Una volta completato questo comando, il tempo di inattività viene completato. | In caso di errore, sono disponibili sia il rollback (self service) che il ripristino. |
| [Passaggio 4 - Testare e attendere il controller di dominio di replicaStep 4 - Test and wait for the replica domain controller](#test-and-verify-connectivity-after-the-migration)| PowerShell e il portale di Azure | 1 ora o più, a seconda del numero di test | Entrambi i controller di dominio sono disponibili e dovrebbero funzionare normalmente. | N/D. Dopo aver eseguito correttamente la migrazione della prima macchina virtuale, non è disponibile alcuna opzione per il rollback o il ripristino. |
| [Passaggio 5 - Passaggi di configurazione facoltativiStep 5 - Optional configuration steps](#optional-post-migration-configuration-steps) | Azure portal and VMs | N/D | Nessun tempo di inattività richiesto | N/D |

> [!IMPORTANT]
> Per evitare tempi di inattività aggiuntivi, leggere tutto questo articolo sulla migrazione e le indicazioni per avviare il processo di migrazione. Il processo di migrazione influisce sulla disponibilità dei controller di dominio di Azure AD DS per un periodo di tempo. Gli utenti, i servizi e le applicazioni non possono eseguire l'autenticazione nel dominio gestito durante il processo di migrazione.

## <a name="update-and-verify-virtual-network-settings"></a>Aggiornare e verificare le impostazioni della rete virtualeUpdate and verify virtual network settings

Prima di iniziare il processo di migrazione, completare i seguenti controlli e aggiornamenti iniziali. Questi passaggi possono essere eseguiti in qualsiasi momento prima della migrazione e non influiscono sul funzionamento del dominio gestito di Servizi di dominio Active Directory di Azure.These steps can happen at any time before the migration and don't affect the operation of the Azure AD DS managed domain.

1. Aggiornare l'ambiente Azure PowerShell locale alla versione più recente. Per completare la procedura di migrazione, è necessaria almeno la versione *2.3.2.*

    Per informazioni su come controllare e aggiornare la versione di PowerShell, vedere Panoramica di Azure PowerShell.For information on how to check and update your PowerShell version, see [Azure PowerShell overview.][azure-powershell]

1. Creare o scegliere una rete virtuale di Resource Manager esistente.

    Assicurarsi che le impostazioni di rete non blocchino le porte necessarie necessarie per Servizi di dominio Azure AD. Le porte devono essere aperte sia nella rete virtuale classica che nella rete virtuale di Resource Manager. Queste impostazioni includono le tabelle di route (anche se non è consigliabile usare le tabelle di route) e i gruppi di sicurezza di rete.

    Per visualizzare le porte necessarie, vedere Gruppi di [sicurezza di rete e porte necessarie][network-ports]. Per ridurre al minimo i problemi di comunicazione di rete, è consigliabile attendere e applicare un gruppo di sicurezza di rete o una tabella di route alla rete virtuale di Resource Manager al termine della migrazione.

    Prendere nota di questo gruppo di risorse di destinazione, della rete virtuale di destinazione e della subnet della rete virtuale di destinazione. Questi nomi di risorse vengono utilizzati durante il processo di migrazione.

1. Controllare l'integrità del dominio gestito di Servizi di dominio Active Directory di Azure nel portale di Azure.Check the Azure AD DS managed domain health in the Azure portal. Se sono presenti avvisi per il dominio gestito, risolverli prima di avviare il processo di migrazione.
1. Facoltativamente, se si prevede di spostare altre risorse nel modello di distribuzione e nella rete virtuale di Resource Manager, verificare che sia possibile eseguire la migrazione di tali risorse. Per ulteriori informazioni, vedere [Migrazione supportata dalla piattaforma di risorse IaaS da Classic a Resource Manager][migrate-iaas].

    > [!NOTE]
    > Non convertire la rete virtuale classica in una rete virtuale di Resource Manager.Don't convert the Classic virtual network to a Resource Manager virtual network. In tal caso, non è disponibile alcuna opzione per eseguire il rollback o il ripristino del dominio gestito di Servizi di dominio Active Directory di Azure.If you do, there's no option to back back or restore the Azure AD DS managed domain.

## <a name="prepare-the-managed-domain-for-migration"></a>Preparare il dominio gestito per la migrazionePrepare the managed domain for migration

Azure PowerShell viene usato per preparare il dominio gestito di Azure AD DS per la migrazione. Questi passaggi includono l'assunzione di un backup, la sospensione della sincronizzazione ed l'eliminazione del servizio cloud che ospita Servizi di dominio Active Directory di Azure.These steps include taking a backup, sousing synchronization, and deleting the cloud service that hosts Azure AD DS. Al termine di questo passaggio, Azure AD DS viene disconnesso per un periodo di tempo. Se il passaggio di preparazione ha esito negativo, è possibile [ripristinare lo stato precedente.](#roll-back)

Per preparare il dominio gestito di Servizi di dominio Active Directory di Azure per la migrazione, completare i passaggi seguenti:To prepare the Azure AD DS managed domain for migration, complete the following steps:

1. Installare `Migrate-Aaads` lo script da [PowerShell Gallery][powershell-script]. Questo script di migrazione di PowerShell è firmato digitalmente dal team di progettazione di Azure AD.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. Creare una variabile per contenere le credenziali per lo script di migrazione utilizzando il cmdlet [Get-Credential.][get-credential]

    L'account utente specificato necessita di privilegi di *amministratore globale* nel tenant di Azure AD per abilitare Servizi di dominio Azure AD e quindi i privilegi *di collaboratore* nella sottoscrizione di Azure per creare le risorse di Azure AD Services necessarie.

    Quando richiesto, immettere un account utente e una password appropriati:

    ```powershell
    $creds = Get-Credential
    ```

1. Eseguire ora `Migrate-Aadds` il cmdlet utilizzando il parametro *-Prepare.* Fornire *il -ManagedDomainFqdn* per il proprio dominio gestito di Azure AD DS, ad esempio *aaddscontoso.com*:

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>Eseguire la migrazione del dominio gestitoMigrate the managed domain

Con il dominio gestito di Servizi di dominio Active Directory di Azure preparato e sottoposto a backup, è possibile eseguire la migrazione del dominio. Questo passaggio ricrea le macchine virtuali del controller di dominio di Servizi di dominio Azure AD usando il modello di distribuzione di Resource Manager.This step recreates the Azure AD Domain Services domain controller VMs using the Resource Manager deployment model. Questo passaggio può richiedere da 1 a 3 ore.

Eseguire `Migrate-Aadds` il cmdlet utilizzando il parametro *-Commit.* Fornire il *-ManagedDomainFqdn* per il proprio dominio gestito di Azure AD DS preparato nella sezione precedente, ad esempio *aaddscontoso.com:*

Specificare il gruppo di risorse di destinazione che contiene la rete virtuale in cui si vuole eseguire la migrazione di Azure AD DS, ad esempio *myResourceGroup*. Specificare la rete virtuale di destinazione, ad esempio *myVnet*, e la subnet, ad esempio *DomainServices*.

Dopo l'esecuzione di questo comando, non è possibile eseguire il rollback:After this command runs, you can't then roll back:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

Dopo che lo script convalida che il dominio gestito è preparato per la migrazione, immettere *Y* per avviare il processo di migrazione.

> [!IMPORTANT]
> Non convertire la rete virtuale classica in una rete virtuale di Resource Manager durante il processo di migrazione. Se si converte la rete virtuale, non è possibile eseguire il rollback o il ripristino del dominio gestito di Servizi di dominio Active Directory di Azure poiché la rete virtuale originale non esisterà più.

Ogni due minuti durante il processo di migrazione, un indicatore di stato segnala lo stato corrente, come illustrato nell'output di esempio seguente:Every two minutes during the migration process, a progress indicator reports the current status, as shown in the following example output:

![Indicatore di stato della migrazione di Azure AD DS](media/migrate-from-classic-vnet/powershell-migration-status.png)

Il processo di migrazione continua a essere eseguito, anche se si chiude lo script di PowerShell.The migration process continues to run, even if you close out the PowerShell script. Nel portale di Azure lo stato del dominio gestito viene segnalato come *Migrazione*.

Al termine della migrazione, è possibile visualizzare l'indirizzo IP del primo controller di dominio nel portale di Azure o tramite Azure PowerShell.When the migration successfully completes, you can view your first domain controller's IP address in the Azure portal or through Azure PowerShell. Viene inoltre visualizzata una stima del tempo sul secondo controller di dominio disponibile.

In questa fase, è possibile spostare facoltativamente altre risorse esistenti dal modello di distribuzione classica e dalla rete virtuale. In alternativa, è possibile mantenere le risorse nel modello di distribuzione classica ed eseguire il peering reciproco delle reti virtuali al termine della migrazione di Servizi di dominio Active Directory di Azure.Or, you can keep the resources on the Classic deployment model and peer the virtual networks to each other after the Azure AD DS migration is complete.

## <a name="test-and-verify-connectivity-after-the-migration"></a>Testare e verificare la connettività dopo la migrazione

La distribuzione del secondo controller di dominio può richiedere del tempo ed essere disponibile per l'uso nel dominio gestito di Servizi di dominio Active Directory di Azure.It can take some time for the second domain controller to successfully deploy and be available for use in the Azure AD DS managed domain.

Con il modello di distribuzione di Resource Manager, le risorse di rete per il dominio gestito di Servizi di dominio Active Directory di Azure vengono visualizzate nel portale di Azure o in Azure PowerShell.With the Resource Manager deployment model, the network resources for the Azure AD DS managed domain are shown in the Azure portal or Azure PowerShell. Per altre informazioni su queste risorse di rete e sulle operazioni disponibili, vedere Risorse di rete usate da Servizi di dominio Active Directory di Azure.To learn more about what these network resources are and do, see [Network resources used by Azure AD DS.][network-resources]

Quando è disponibile almeno un controller di dominio, completare i passaggi di configurazione seguenti per la connettività di rete con le macchine virtuali:When at least an domain controller is available, complete the following configuration steps for network connectivity with VMs:

* **Aggiornare le impostazioni del server DNS** Per consentire ad altre risorse nella rete virtuale di Resource Manager di risolvere e usare il dominio gestito di Azure AD DS, aggiornare le impostazioni DNS con gli indirizzi IP dei nuovi controller di dominio. Il portale di Azure può configurare automaticamente queste impostazioni. Per altre informazioni su come configurare la rete virtuale di Resource Manager, vedere [Aggiornare le impostazioni DNS per la rete virtuale][update-dns]di Azure.To learn more about how to configure the Resource Manager virtual network, see Update DNS settings for the Azure virtual network .
* **Riavviare le macchine virtuali aggiunte al dominio:** quando cambiano gli indirizzi IP del server DNS per i controller di dominio di Azure AD DS, riavviare tutte le macchine virtuali aggiunte al dominio in modo che utilizzino le nuove impostazioni del server DNS. Se le applicazioni o le macchine virtuali hanno configurato manualmente le impostazioni DNS, aggiornarle manualmente con i nuovi indirizzi IP del server DNS dei controller di dominio visualizzati nel portale di Azure.If applications or VMs have manually configured DNS settings, manually update them with the new DNS server IP addresses of the domain controllers that are shown in the Azure portal.

A questo punto testare la connessione di rete virtuale e la risoluzione dei nomi. In una macchina virtuale connessa alla rete virtuale di Resource Manager o con un'origine dati, provare i test di comunicazione di rete seguenti:On a VM that is connected to the Resource Manager virtual network, or peered to it, try the following network communication tests:

1. Verificare se è possibile eseguire il ping dell'indirizzo IP di uno dei controller di dominio, ad esempio`ping 10.1.0.4`
    * Gli indirizzi IP dei controller di dominio vengono visualizzati nella pagina Proprietà per il dominio gestito di Servizi di dominio Active Directory di Azure nel portale di Azure.The IP addresses of the domain controllers are shown on the **Properties** page for the Azure AD DS managed domain in the Azure portal.
1. Verificare la risoluzione dei nomi del dominio gestito, ad esempio`nslookup aaddscontoso.com`
    * Specificare il nome DNS per il proprio dominio gestito di Servizi di dominio Active Directory di Azure per verificare che le impostazioni DNS siano corrette e risolte.

Il secondo controller di dominio deve essere disponibile 1-2 ore dopo il completamento del cmdlet di migrazione. Per verificare se il secondo controller di dominio è disponibile, esaminare la pagina Proprietà per il dominio gestito di Servizi di dominio Active Directory di Azure nel portale di Azure.To check if the second domain controller is available, look at the **Properties** page for the Azure AD DS managed domain in the Azure portal. Se vengono visualizzati due indirizzi IP, il secondo controller di dominio è pronto.

## <a name="optional-post-migration-configuration-steps"></a>Passaggi di configurazione post-migrazione facoltativiOptional post-migration configuration steps

Quando il processo di migrazione viene completato correttamente, alcuni passaggi di configurazione facoltativi includono l'abilitazione dei registri di controllo o delle notifiche tramite posta elettronica o l'aggiornamento dei criteri granulari per le password.

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Sottoscrivere i log di controllo usando Monitoraggio di AzureSubscribe to audit logs using Azure Monitor

Servizi di dominio Active Directory di Azure espone i log di controllo per risolvere i problemi e visualizzare gli eventi nei controller di dominio. Per ulteriori informazioni, vedere [Abilitare e utilizzare i registri][security-audits]di controllo .

È possibile utilizzare i modelli per monitorare le informazioni importanti esposte nei registri. Ad esempio, il modello di cartella di lavoro del log di controllo può monitorare i possibili blocchi degli account nel dominio gestito di Servizi di dominio Active Directory di Azure.For example, the audit log workbook template can monitor possible account lockouts on the Azure AD DS managed domain.

### <a name="configure-azure-ad-domain-services-email-notifications"></a>Configurare le notifiche di posta elettronica di Servizi di dominio Azure ADConfigure Azure AD Domain Services email notifications

Per ricevere una notifica quando viene rilevato un problema nel dominio gestito di Servizi di dominio Active Directory di Azure, aggiornare le impostazioni di notifica tramite posta elettronica nel portale di Azure.To be notified when a problem is detected on the Azure AD DS managed domain, update the email notification settings in the Azure portal. Per ulteriori informazioni, vedere [Configurare le impostazioni][notifications]di notifica .

### <a name="update-fine-grained-password-policy"></a>Aggiornare i criteri granulari per le password

Se necessario, è possibile aggiornare i criteri granulari per le password in modo che siano meno restrittivi rispetto alla configurazione predefinita. È possibile utilizzare i registri di controllo per determinare se un'impostazione meno restrittiva ha senso, quindi configurare il criterio in base alle esigenze. Utilizzare la procedura generale seguente per esaminare e aggiornare le impostazioni dei criteri per gli account che vengono bloccati ripetutamente dopo la migrazione:Use the following high-level steps to review and update the policy settings for accounts that are repeatedly locked out after migration:

1. [Configurare][password-policy] i criteri password per un numero inferiore di restrizioni nel dominio gestito di Servizi di dominio Active Directory di Azure e osservare gli eventi nei log di controllo.
1. Se gli account di servizio utilizzano password scadute identificate nei registri di controllo, aggiornare tali account con la password corretta.
1. Se una macchina virtuale è esposta a Internet, verificare la ricerca di nomi di account generici come *administrator*, *user*o *guest* con tentativi di accesso elevati. Se possibile, aggiornare tali macchine virtuali per usare account con nomi meno genericamente.
1. Usare una traccia di rete nella macchina virtuale per individuare l'origine degli attacchi e impedire a tali indirizzi IP di tentare gli indirizzi di accesso.
1. Quando si verificano problemi minimi di blocco, aggiornare i criteri granulari per le password in modo che siano restrittivi in base alle esigenze.

### <a name="creating-a-network-security-group"></a>Creazione di un gruppo di sicurezza di rete

Servizi di dominio Active Directory di Azure richiede un gruppo di sicurezza di rete per proteggere le porte necessarie per il dominio gestito e bloccare tutto il traffico in ingresso. Questo gruppo di sicurezza di rete funge da ulteriore livello di protezione per bloccare l'accesso al dominio gestito e non viene creato automaticamente. Per creare il gruppo di sicurezza di rete e aprire le porte necessarie, esaminare i passaggi seguenti:To create the network security group and open the required ports, review the following steps:

1. Nel portale di Azure selezionare la risorsa di Azure AD DS.In the Azure portal, select your Azure AD DS resource. Nella pagina panoramica viene visualizzato un pulsante per creare un gruppo di sicurezza di rete se non è associato a Servizi di dominio Azure AD.
1. Se si utilizza LDAP sicuro, aggiungere una regola al gruppo di sicurezza di rete per consentire il traffico in ingresso per la porta *TCP* *636*. Per ulteriori informazioni, consultate [Configurare LDAP protetto.][secure-ldap]

## <a name="roll-back-and-restore-from-migration"></a>Eseguire il rollback e il ripristino dalla migrazione

Fino a un determinato punto del processo di migrazione, è possibile scegliere di eseguire il rollback o il ripristino del dominio gestito di Servizi di dominio Active Directory di Azure.Up to a certain point in the migration process, you can choose to rollback or restore the Azure AD DS managed domain.

### <a name="roll-back"></a>Rollback

Se si verifica un errore quando si esegue il cmdlet PowerShell per preparare la migrazione nel passaggio 2 o per la migrazione stessa nel passaggio 3, il dominio gestito di Servizi di dominio Active Directory di Azure può eseguire il rollback alla configurazione originale. Questo rollback richiede la rete virtuale classica originale. Si noti che gli indirizzi IP possono ancora cambiare dopo il rollback.

Eseguire `Migrate-Aadds` il cmdlet utilizzando il parametro *-Abort.* Specificare *-ManagedDomainFqdn* per il proprio dominio gestito di Azure AD DS preparato in una sezione precedente, ad esempio *aaddscontoso.com*, e il nome della rete virtuale classica, ad esempio *myClassicVnet*:

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds
```

### <a name="restore"></a>Restore

Come ultima risorsa, Servizi di dominio Azure AD può essere ripristinato dall'ultimo backup disponibile. Viene eseguito un backup nel passaggio 1 della migrazione per assicurarsi che sia disponibile il backup più recente. Questo backup viene archiviato per 30 giorni.

Per ripristinare il dominio gestito di Servizi di dominio Active Directory di Azure dal backup, aprire un ticket del caso di [supporto tramite il portale][azure-support]di Azure. Specificare l'ID directory, il nome di dominio e il motivo del ripristino. Il completamento del processo di supporto e ripristino potrebbe richiedere più giorni.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verificano problemi dopo la migrazione al modello di distribuzione di Resource Manager, esaminare alcune delle seguenti aree comuni di risoluzione dei problemi:

* [Risolvere i problemi relativi all'aggiunta a un dominio][troubleshoot-domain-join]
* [Risolvere i problemi di blocco degli account][troubleshoot-account-lockout]
* [Risolvere i problemi di accesso dell'account][troubleshoot-sign-in]
* [Risolvere i problemi di connettività LDAP sicura][tshoot-ldaps]

## <a name="next-steps"></a>Passaggi successivi

Con il dominio gestito di Servizi di dominio Active Directory di Azure migrato nel modello di distribuzione di Resource Manager, [creare e aggiungere al dominio una macchina virtuale Windows][join-windows] e quindi installare gli strumenti di [gestione.][tutorial-create-management-vm]

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/overview
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/windows/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
