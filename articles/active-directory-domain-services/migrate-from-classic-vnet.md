---
title: Eseguire la migrazione di Azure AD Domain Services da una rete virtuale classica | Microsoft Docs
description: Informazioni su come eseguire la migrazione di un'istanza di dominio gestito Azure AD Domain Services esistente dal modello di rete virtuale classica a una rete virtuale basata su Gestione risorse.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: iainfou
ms.openlocfilehash: 8cba2cbf8fcbad1acae8c36892308c3249fc4181
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2019
ms.locfileid: "72674912"
---
# <a name="preview---migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Anteprima: eseguire la migrazione Azure AD Domain Services dal modello di rete virtuale classica al Gestione risorse

Azure Active Directory Domain Services (AD DS) supporta uno spostamento monouso per i clienti che attualmente usano il modello di rete virtuale classica per il modello di rete virtuale Gestione risorse.

Questo articolo descrive i vantaggi e le considerazioni per la migrazione, quindi i passaggi necessari per eseguire correttamente la migrazione di un'istanza di Azure AD DS esistente. Questa funzionalità è attualmente in anteprima.

## <a name="overview-of-the-migration-process"></a>Panoramica del processo di migrazione

Il processo di migrazione accetta un'istanza di Azure AD DS esistente che viene eseguita in una rete virtuale classica e la sposta in una rete virtuale Gestione risorse esistente. La migrazione viene eseguita con PowerShell e prevede due fasi principali di *preparazione* e *migrazione*dell'esecuzione.

![Panoramica del processo di migrazione per Azure AD DS](media/migrate-from-classic-vnet/migration-overview.png)

Nella fase di *preparazione* Azure AD DS esegue un backup del dominio per ottenere lo snapshot più recente di utenti, gruppi e password sincronizzati con il dominio gestito. La sincronizzazione viene quindi disabilitata e il servizio cloud che ospita il dominio gestito Azure AD DS viene eliminato. Durante la fase di preparazione, il dominio gestito Azure AD DS non è in grado di autenticare gli utenti.

![Fase di preparazione per la migrazione di Azure AD DS](media/migrate-from-classic-vnet/migration-preparation.png)

Nella fase di *migrazione* , i dischi virtuali sottostanti per i controller di dominio del dominio gestito classico Azure AD DS vengono copiati per creare le macchine virtuali usando il modello di distribuzione gestione risorse. Il dominio gestito Azure AD DS viene quindi ricreato, che include la configurazione LDAPs e DNS. Viene riavviata la sincronizzazione con Azure AD e vengono ripristinati i certificati LDAP. Non è necessario aggiungere nuovamente i computer a un dominio gestito di Azure AD DS, che continuano a essere aggiunti al dominio gestito ed eseguiti senza modifiche.

![Migrazione di Azure AD DS](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>Vantaggi della migrazione

Quando si sposta un dominio gestito Azure AD DS utilizzando questo processo di migrazione, si evita la necessità di aggiungere di nuovo i computer al dominio gestito o di eliminare l'istanza di Azure AD DS e crearne una da zero. Le macchine virtuali continuano a essere unite al dominio gestito Azure AD DS alla fine del processo di migrazione.

Dopo la migrazione, Azure AD DS fornisce molte funzionalità disponibili solo per i domini che usano Gestione risorse reti virtuali, ad esempio:

* Supporto per i criteri granulari per le password.
* Protezione del blocco dell'account Active Directory.
* Notifiche tramite posta elettronica degli avvisi nel dominio gestito Azure AD DS.
* Log di controllo con monitoraggio di Azure.
* Integrazione di File di Azure
* Integrazione di HD Insights

Azure AD domini gestiti di DS che usano una rete virtuale Gestione risorse consentono di rimanere sempre aggiornati sulle nuove funzionalità più recenti. Il supporto per Azure AD DS con reti virtuali classiche verrà deprecato in futuro.

## <a name="example-scenarios-for-migration"></a>Scenari di esempio per la migrazione

Alcuni scenari comuni per la migrazione di un dominio gestito Azure AD DS includono gli esempi seguenti.

> [!NOTE]
> Non convertire la rete virtuale classica fino a quando non viene confermata una migrazione riuscita. Quando si converte la rete virtuale, viene rimossa l'opzione per eseguire il rollback o il ripristino del dominio gestito Azure AD DS se si verificano problemi durante le fasi di migrazione e verifica.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Eseguire la migrazione di Azure AD DS a una rete virtuale Gestione risorse esistente (scelta consigliata)

Uno scenario comune è la posizione in cui sono già state spostate altre risorse classiche esistenti in un modello di distribuzione Gestione risorse e in una rete virtuale. Il peering viene quindi usato dalla rete virtuale Gestione risorse alla rete virtuale classica che continua a eseguire Azure AD DS. Questo approccio consente alle applicazioni e ai servizi Gestione risorse di usare le funzionalità di autenticazione e gestione del dominio gestito Azure AD DS nella rete virtuale classica. Una volta eseguita la migrazione, tutte le risorse vengono eseguite utilizzando il modello di distribuzione Gestione risorse e la rete virtuale.

![Eseguire la migrazione di Azure AD DS a una rete virtuale Gestione risorse esistente](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

I passaggi generali necessari in questo scenario di migrazione di esempio includono le parti seguenti:

1. Rimuovere i gateway VPN esistenti o il peering di rete virtuale configurato nella rete virtuale classica.
1. Eseguire la migrazione del dominio gestito di Azure AD DS usando la procedura descritta in questo articolo.
1. Testare e confermare una migrazione riuscita, quindi eliminare la rete virtuale classica.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Eseguire la migrazione di più risorse, tra cui Azure AD DS

In questo scenario di esempio si esegue la migrazione di Azure AD DS e di altre risorse associate dal modello di distribuzione classica al modello di distribuzione di Gestione risorse. Se alcune risorse continuano a essere eseguite nella rete virtuale classica insieme al dominio gestito di Azure AD DS, possono trarre vantaggio dalla migrazione al modello di distribuzione di Gestione risorse.

![Eseguire la migrazione di più risorse al modello di distribuzione Gestione risorse](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

I passaggi generali necessari in questo scenario di migrazione di esempio includono le parti seguenti:

1. Rimuovere i gateway VPN esistenti o il peering di rete virtuale configurato nella rete virtuale classica.
1. Eseguire la migrazione del dominio gestito di Azure AD DS usando la procedura descritta in questo articolo.
1. Configurare il peering di rete virtuale tra la rete virtuale classica e la rete Gestione risorse.
1. Testare e confermare una migrazione riuscita.
1. [Spostare risorse classiche aggiuntive, come le macchine virtuali][migrate-iaas].

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Eseguire la migrazione di Azure AD DS mantenendo le altre risorse nella rete virtuale classica

Con questo scenario di esempio, si ha la quantità minima di tempo di inattività in una sessione. Si esegue solo la migrazione di Azure AD DS a una rete virtuale Gestione risorse e si mantengono le risorse esistenti sul modello di distribuzione classica e sulla rete virtuale. In un periodo di manutenzione successivo, è possibile eseguire la migrazione delle risorse aggiuntive dal modello di distribuzione classica e dalla rete virtuale in base alle esigenze.

![Eseguire la migrazione di Azure AD DS solo al modello di distribuzione Gestione risorse](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

I passaggi generali necessari in questo scenario di migrazione di esempio includono le parti seguenti:

1. Rimuovere i gateway VPN esistenti o il peering di rete virtuale configurato nella rete virtuale classica.
1. Eseguire la migrazione del dominio gestito di Azure AD DS usando la procedura descritta in questo articolo.
1. Configurare il peering di rete virtuale tra la rete virtuale classica e la nuova rete virtuale Gestione risorse.
1. Successivamente, [eseguire la migrazione delle risorse aggiuntive][migrate-iaas] dalla rete virtuale classica in base alle esigenze.

## <a name="before-you-begin"></a>Prima di iniziare

Quando si prepara e quindi si esegue la migrazione di un dominio gestito Azure AD DS, esistono alcune considerazioni sulla disponibilità dei servizi di autenticazione e gestione. Il dominio gestito di Azure AD DS non è disponibile per un periodo di tempo durante la migrazione. Le applicazioni e i servizi che si basano su un tempo di inattività Azure AD DS durante la migrazione.

> [!IMPORTANT]
> Prima di iniziare il processo di migrazione, leggere l'articolo relativo alla migrazione e le linee guida. Il processo di migrazione influiscono sulla disponibilità dei controller di dominio Azure AD DS per periodi di tempo. Utenti, servizi e applicazioni non possono eseguire l'autenticazione nel dominio gestito durante il processo di migrazione.

### <a name="ip-addresses"></a>Indirizzi IP

Gli indirizzi IP del controller di dominio per un dominio gestito Azure AD DS cambiano dopo la migrazione. Che include l'indirizzo IP pubblico per l'endpoint LDAP sicuro. I nuovi indirizzi IP si trovano all'interno dell'intervallo di indirizzi per la nuova subnet nella rete virtuale Gestione risorse.

In caso di rollback, gli indirizzi IP possono cambiare dopo il rollback.

Azure AD DS USA in genere i primi due indirizzi IP disponibili nell'intervallo di indirizzi, ma ciò non è garantito. Attualmente non è possibile specificare gli indirizzi IP da usare dopo la migrazione.

### <a name="downtime"></a>Tempo di inattività

Il processo di migrazione prevede che i controller di dominio siano offline per un determinato periodo di tempo. I controller di dominio sono inaccessibili mentre viene eseguita la migrazione di Azure AD DS al modello di distribuzione Gestione risorse e alla rete virtuale. In media, il tempo di inattività è di circa da 1 a 3 ore. Questo periodo di tempo si verifica quando i controller di dominio vengono portati offline nel momento in cui il primo controller di dominio torna online. Questa media non include il tempo necessario per la replica del secondo controller di dominio o il tempo impiegato per eseguire la migrazione di risorse aggiuntive al modello di distribuzione Gestione risorse.

### <a name="account-lockout"></a>Blocco dell'account

Azure AD domini gestiti di DS eseguiti in reti virtuali classiche non hanno criteri di blocco degli account Active Directory. Se le macchine virtuali sono esposte a Internet, gli utenti malintenzionati potrebbero usare i metodi di spruzzatura delle password per forzare gli account in modo forzato. Non sono presenti criteri di blocco degli account per arrestare questi tentativi. Per i domini gestiti di Azure AD DS che usano il modello di distribuzione Gestione risorse e le reti virtuali, i criteri di blocco degli account di Active Directory proteggono da questi attacchi con spray da password.

Per impostazione predefinita, 5 tentativi di accesso con password errata in 2 minuti bloccano un account per 30 minuti.

Un account bloccato non può essere connesso a, che può interferire con la possibilità di gestire il dominio gestito Azure AD DS o le applicazioni gestite dall'account. Dopo la migrazione di un dominio gestito di Azure AD DS, gli account possono riscontrare un blocco permanente a causa di tentativi ripetuti non riusciti di accesso. Di seguito sono riportati due scenari comuni di migrazione:

* Un account del servizio che usa una password scaduta.
    * L'account del servizio tenta ripetutamente di accedere con una password scaduta, che blocca l'account. Per risolvere il problema, individuare l'applicazione o la macchina virtuale con le credenziali scadute e aggiornare la password.
* Un'entità dannosa USA tentativi di forza bruta per accedere agli account.
    * Quando le macchine virtuali sono esposte a Internet, gli utenti malintenzionati provano spesso a usare combinazioni comuni di nome utente e password quando tentano di firmare Questi tentativi di accesso non riusciti ripetuti possono bloccare gli account. Non è consigliabile usare gli account amministratore con nomi generici, ad esempio *amministratore o* *amministratore*, per ridurre al minimo la chiusura degli account amministrativi.
    * Ridurre al minimo il numero di macchine virtuali esposte a Internet. È possibile usare [Azure Bastion (attualmente in anteprima)][azure-bastion] per connettersi in modo sicuro alle macchine virtuali usando il portale di Azure.

Se si ritiene che alcuni account possano essere bloccati dopo la migrazione, i passaggi finali della migrazione descrivono come abilitare il controllo o modificare le impostazioni dei criteri granulari per le password.

### <a name="roll-back-and-restore"></a>Rollback e ripristino

Se la migrazione non riesce, è necessario eseguire il rollback o il ripristino di un dominio gestito Azure AD DS. Rollback è un'opzione self-service che consente di restituire immediatamente lo stato del dominio gestito prima del tentativo di migrazione. I tecnici del supporto tecnico di Azure possono anche ripristinare un dominio gestito dal backup come ultima risorsa. Per ulteriori informazioni, vedere [come eseguire il rollback o il ripristino da una migrazione non riuscita](#roll-back-and-restore-from-migration).

### <a name="restrictions-on-available-virtual-networks"></a>Restrizioni sulle reti virtuali disponibili

Esistono alcune restrizioni per le reti virtuali a cui è possibile eseguire la migrazione di un dominio gestito Azure AD DS. Il Gestione risorse di destinazione rete virtuale deve soddisfare i requisiti seguenti:

* La rete virtuale Gestione risorse deve trovarsi nella stessa sottoscrizione di Azure della rete virtuale classica in cui è attualmente distribuito Azure AD DS.
* La rete virtuale Gestione risorse deve trovarsi nella stessa area della rete virtuale classica in cui è attualmente distribuito Azure AD DS.
* La subnet della rete virtuale Gestione risorse deve avere almeno 3-5 indirizzi IP disponibili.
* La subnet della rete virtuale Gestione risorse deve essere una subnet dedicata per Azure AD DS e non deve ospitare altri carichi di lavoro.

Per ulteriori informazioni sui requisiti della rete virtuale, vedere [considerazioni sulla progettazione della rete virtuale e opzioni di configurazione][network-considerations].

## <a name="migration-steps"></a>Passaggi della migrazione

La migrazione al modello di distribuzione Gestione risorse e alla rete virtuale è suddivisa in 5 passaggi principali:

| Passaggio    | Eseguito tramite  | Tempo stimato  | Tempo di inattività  | Eseguire il rollback o il ripristino? |
|---------|--------------------|-----------------|-----------|-------------------|
| [Passaggio 1: aggiornare e individuare la nuova rete virtuale](#update-and-verify-virtual-network-settings) | Portale di Azure | 15 minuti | Nessun tempo di inattività necessario | N/D |
| [Passaggio 2: preparare il dominio gestito di Azure AD DS per la migrazione](#prepare-the-managed-domain-for-migration) | PowerShell | 15-30 minuti in media | Il tempo di inattività di Azure AD DS viene avviato dopo il completamento di questo comando. | Rollback e ripristino disponibili. |
| [Passaggio 3: spostare il dominio gestito di Azure AD DS in una rete virtuale esistente](#migrate-the-managed-domain) | PowerShell | da 1 a 3 ore in media | Un controller di dominio è disponibile al termine del comando. il tempo di inattività termina. | In caso di errore, sono disponibili sia rollback (self service) che Restore. |
| [Passaggio 4: testare e attendere il controller di dominio di replica](#test-and-verify-connectivity-after-the-migration)| PowerShell e portale di Azure | 1 ora o più, a seconda del numero di test | Entrambi i controller di dominio sono disponibili e funzionano normalmente. | N/D. Una volta eseguita la migrazione della prima VM, non è possibile eseguire il rollback o il ripristino. |
| [Passaggio 5-procedura di configurazione facoltativa](#optional-post-migration-configuration-steps) | portale di Azure e macchine virtuali | N/D | Nessun tempo di inattività necessario | N/D |

> [!IMPORTANT]
> Per evitare tempi di inattività aggiuntivi, prima di avviare il processo di migrazione leggere tutti gli articoli e le linee guida per la migrazione. Il processo di migrazione influiscono sulla disponibilità dei controller di dominio Azure AD DS per un certo periodo di tempo. Utenti, servizi e applicazioni non possono eseguire l'autenticazione nel dominio gestito durante il processo di migrazione.

## <a name="update-and-verify-virtual-network-settings"></a>Aggiornare e verificare le impostazioni della rete virtuale

Prima di iniziare la migrazione, completare i controlli e gli aggiornamenti iniziali seguenti. Questi passaggi possono verificarsi in qualsiasi momento prima della migrazione e non influiscono sul funzionamento del dominio gestito Azure AD DS.

1. Aggiornare l'ambiente di Azure PowerShell locale alla versione più recente. Per completare i passaggi della migrazione, è necessario almeno la versione *2.3.2*.

    Per informazioni su come eseguire il controllo e l'aggiornamento, vedere [Azure PowerShell Overview][azure-powershell].

1. Creare o scegliere una rete virtuale Gestione risorse esistente.

    Assicurarsi che le impostazioni di rete non blocchino le porte necessarie per Azure AD DS. Le porte devono essere aperte sia nella rete virtuale classica che nella rete virtuale Gestione risorse. Queste impostazioni includono le tabelle di route (anche se non è consigliabile usare le tabelle di route) e i gruppi di sicurezza di rete.

    Per visualizzare le porte necessarie, vedere [gruppi di sicurezza di rete e porte obbligatorie][network-ports]. Per ridurre al minimo i problemi di comunicazione di rete, è consigliabile attendere e applicare un gruppo di sicurezza di rete o una tabella di route alla rete virtuale Gestione risorse dopo che la migrazione è stata completata correttamente.

    Prendere nota di questo gruppo di risorse di destinazione, la rete virtuale di destinazione e la subnet della rete virtuale di destinazione. Questi nomi di risorse vengono usati durante il processo di migrazione.

1. Controllare l'integrità del dominio gestito Azure AD DS nel portale di Azure. Se sono presenti avvisi per il dominio gestito, risolverli prima di avviare il processo di migrazione.
1. Facoltativamente, se si intende spostare altre risorse nel modello di distribuzione Gestione risorse e nella rete virtuale, verificare che sia possibile eseguire la migrazione di tali risorse. Per altre informazioni, vedere [migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al gestione risorse][migrate-iaas].

    > [!NOTE]
    > Non convertire la rete virtuale classica in una rete virtuale Gestione risorse. In tal caso, non è possibile eseguire il rollback o il ripristino del dominio gestito Azure AD DS.

## <a name="prepare-the-managed-domain-for-migration"></a>Preparare il dominio gestito per la migrazione

Azure PowerShell viene usato per preparare il dominio gestito di Azure AD DS per la migrazione. Questi passaggi includono l'esecuzione di un backup, la sospensione della sincronizzazione e l'eliminazione del servizio cloud che ospita Azure AD DS. Al termine di questo passaggio, Azure AD DS viene portato offline per un periodo di tempo. Se il passaggio di preparazione ha esito negativo, è possibile [eseguire il rollback allo stato precedente](#roll-back).

Per preparare il dominio gestito di Azure AD DS per la migrazione, attenersi alla procedura seguente:

1. Installare lo script `Migrate-Aaads` dalla [PowerShell Gallery][powershell-script]. Questo script di migrazione di PowerShell è firmato digitalmente dal team di progettazione di Azure AD.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. Creare una variabile che contenga le credenziali per lo script di migrazione usando il cmdlet [Get-Credential][get-credential] .

    L'account utente specificato richiede i privilegi di *amministratore globale* nel tenant di Azure ad per abilitare Azure AD DS e quindi i privilegi di *collaboratore* nella sottoscrizione di Azure per creare le risorse Azure AD DS necessarie.

    Quando richiesto, immettere un account utente e una password appropriati:

    ```powershell
    $creds = Get-Credential
    ```

1. A questo punto, eseguire il cmdlet `Migrate-Aadds` usando il parametro *-Prepare* . Fornire *-ManagedDomainFqdn* per il dominio gestito di Azure AD DS, ad esempio *contoso.com*:

    ```powershell
    Migrate-Aadds `
        -Prepare -ManagedDomainFqdn contoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>Eseguire la migrazione del dominio gestito

Con il dominio gestito di Azure AD DS preparato e sottoposto a backup, è possibile eseguire la migrazione del dominio. Questo passaggio ricrea le macchine virtuali del controller di dominio Azure AD Domain Services usando il modello di distribuzione Gestione risorse. Il completamento di questo passaggio può richiedere da 1 a 3 ore.

Eseguire il cmdlet `Migrate-Aadds` utilizzando il parametro *-commit* . Specificare *-ManagedDomainFqdn* per il dominio gestito Azure AD DS preparato nella sezione precedente, ad esempio *contoso.com*:

Specificare il gruppo di risorse di destinazione che contiene la rete virtuale di cui si vuole eseguire la migrazione Azure AD DS, ad esempio *myResourceGroup*. Fornire la rete virtuale di destinazione, ad esempio *myVnet*, e la subnet, ad esempio *DomainServices*.

Dopo l'esecuzione di questo comando, non è possibile eseguire il rollback:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn contoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

Dopo la convalida dello script, il dominio gestito è pronto per la migrazione, immettere *Y* per avviare il processo di migrazione.

> [!IMPORTANT]
> Non convertire la rete virtuale classica in una rete virtuale Gestione risorse durante il processo di migrazione. Se si converte la rete virtuale, non è possibile eseguire il rollback o il ripristino del dominio gestito di Azure AD DS perché la rete virtuale originale non esiste più.

Ogni due minuti durante il processo di migrazione, un indicatore di stato segnala lo stato corrente, come illustrato nell'output di esempio seguente:

![Indicatore di stato della migrazione di Azure AD DS](media/migrate-from-classic-vnet/powershell-migration-status.png)

Il processo di migrazione continua a essere eseguito, anche se si chiude lo script di PowerShell. Nella portale di Azure lo stato del dominio gestito viene segnalato come *migrazione*.

Una volta completata la migrazione, è possibile visualizzare l'indirizzo IP del primo controller di dominio nell'portale di Azure o tramite Azure PowerShell. Viene visualizzata anche una stima temporale sul secondo controller di dominio disponibile.

In questa fase è possibile spostare facoltativamente altre risorse esistenti dal modello di distribuzione classica e dalla rete virtuale. In alternativa, è possibile salvare le risorse nel modello di distribuzione classica e il peering tra le reti virtuali dopo aver completato la migrazione del Azure AD DS.

## <a name="test-and-verify-connectivity-after-the-migration"></a>Testare e verificare la connettività dopo la migrazione

La distribuzione e la disponibilità del secondo controller di dominio possono richiedere del tempo per l'uso nel dominio gestito di Azure AD DS.

Con il modello di distribuzione Gestione risorse, le risorse di rete per il dominio gestito Azure AD DS vengono visualizzate nella portale di Azure o Azure PowerShell. Per altre informazioni sulle risorse di rete, vedere [risorse di rete usate da Azure AD DS][network-resources].

Quando è disponibile almeno un controller di dominio, completare i passaggi di configurazione seguenti per la connettività di rete con le macchine virtuali:

* **Aggiornare le impostazioni del server DNS** Per consentire ad altre risorse nella rete virtuale Gestione risorse di risolvere e utilizzare il dominio gestito Azure AD DS, aggiornare le impostazioni DNS con gli indirizzi IP dei nuovi controller di dominio. Il portale di Azure può configurare automaticamente queste impostazioni. Per altre informazioni su come configurare la rete virtuale Gestione risorse, vedere [aggiornare le impostazioni DNS per la rete virtuale di Azure][update-dns].
* **Riavviare le macchine virtuali appartenenti** a un dominio: gli indirizzi IP del server DNS per i controller di dominio Azure AD DS cambiano, riavviare tutte le macchine virtuali appartenenti a un dominio in modo che usino quindi le nuove impostazioni del server DNS. Se le impostazioni DNS sono state configurate manualmente da applicazioni o macchine virtuali, aggiornarle manualmente con i nuovi indirizzi IP del server DNS dei controller di dominio visualizzati nel portale di Azure.

Testare ora la connessione alla rete virtuale e la risoluzione dei nomi. In una VM connessa alla rete virtuale Gestione risorse o con peering, provare i test di comunicazione di rete seguenti:

1. Controllare se è possibile effettuare il ping dell'indirizzo IP di uno dei controller di dominio, ad esempio `ping 10.1.0.4`
    * Gli indirizzi IP dei controller di dominio vengono visualizzati nella pagina **Proprietà** del dominio gestito Azure AD DS nel portale di Azure.
1. Verificare la risoluzione dei nomi del dominio gestito, ad esempio `nslookup contoso.com`
    * Specificare il nome DNS per il dominio gestito Azure AD DS per verificare che le impostazioni DNS siano corrette e risolte.

Il secondo controller di dominio deve essere disponibile 1-2 ore dopo il completamento del cmdlet di migrazione. Per verificare se il secondo controller di dominio è disponibile, esaminare la pagina delle **Proprietà** per il dominio gestito Azure AD DS nel portale di Azure. Se vengono visualizzati due indirizzi IP, il secondo controller di dominio è pronto.

## <a name="optional-post-migration-configuration-steps"></a>Procedura di configurazione post-migrazione facoltativa

Una volta completato il processo di migrazione, alcuni passaggi di configurazione facoltativi includono l'abilitazione di log di controllo o notifiche tramite posta elettronica o l'aggiornamento dei criteri granulari per le password.

#### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Sottoscrivere i log di controllo con monitoraggio di Azure

Azure AD DS espone i log di controllo per semplificare la risoluzione dei problemi e visualizzare gli eventi nei controller di dominio. Per altre informazioni, vedere [abilitare e usare i log di controllo][security-audits].

È possibile usare i modelli per monitorare le informazioni importanti esposte nei log. Il modello di cartella di lavoro log di controllo, ad esempio, può monitorare i possibili blocchi degli account nel dominio gestito Azure AD DS.

#### <a name="configure-azure-ad-domain-services-email-notifications"></a>Configurare Azure AD Domain Services notifiche tramite posta elettronica

Per ricevere una notifica quando viene rilevato un problema nel dominio gestito Azure AD DS, aggiornare le impostazioni di notifica tramite posta elettronica nel portale di Azure. Per altre informazioni, vedere [configurare le impostazioni di notifica][notifications].

#### <a name="update-fine-grained-password-policy"></a>Aggiornare i criteri granulari per le password

Se necessario, è possibile aggiornare i criteri granulari per le password in modo che siano meno restrittivi rispetto alla configurazione predefinita. È possibile usare i log di controllo per determinare se un'impostazione meno restrittiva è sensata, quindi configurare il criterio in base alle esigenze. Usare i passaggi generali seguenti per esaminare e aggiornare le impostazioni dei criteri per gli account che vengono bloccati ripetutamente dopo la migrazione:

1. [Configurare i criteri password][password-policy] per un minor numero di restrizioni sul dominio gestito Azure AD DS e osservare gli eventi nei log di controllo.
1. Se gli account del servizio utilizzano password scadute, come indicato nei log di controllo, aggiornare tali account con la password corretta.
1. Se la macchina virtuale è esposta a Internet, verificare la presenza di nomi di account generici come *amministratore*, *utente*o *Guest* con tentativi di accesso elevati. Laddove possibile, aggiornare le macchine virtuali in modo da usare account meno denominati in modo generico.
1. Usare una traccia di rete nella macchina virtuale per individuare l'origine degli attacchi e bloccare tali indirizzi IP per poter provare gli accessi.
1. Quando si verificano problemi di blocco minimi, aggiornare i criteri granulari per le password in modo che siano più restrittivi in base alle esigenze.

#### <a name="creating-a-network-security-group"></a>Creazione di un gruppo di sicurezza di rete

Azure AD DS necessita di un gruppo di sicurezza di rete per proteggere le porte necessarie per il dominio gestito e bloccare tutto il traffico in ingresso. Questo gruppo di sicurezza di rete funge da livello aggiuntivo di protezione per bloccare l'accesso al dominio gestito e non viene creato automaticamente. Per creare il gruppo di sicurezza di rete e aprire le porte necessarie, esaminare i passaggi seguenti:

1. Nella portale di Azure selezionare la risorsa Azure AD DS. Nella pagina panoramica viene visualizzato un pulsante per creare un gruppo di sicurezza di rete, se non ne è associato alcuno a Azure AD Domain Services.
1. Se si usa l'accesso LDAP sicuro, aggiungere una regola al gruppo di sicurezza di rete per consentire il traffico in ingresso per la porta *TCP* *636*. Per altre informazioni, vedere [configurare LDAP sicuro][secure-ldap].

## <a name="roll-back-and-restore-from-migration"></a>Eseguire il rollback e il ripristino dalla migrazione

### <a name="roll-back"></a>Esegui rollback

Se si verifica un errore quando si esegue il cmdlet di PowerShell per preparare la migrazione nel passaggio 2 o per la migrazione stessa nel passaggio 3, il dominio gestito Azure AD DS può eseguire il rollback alla configurazione originale. Per eseguire il rollback è necessaria la rete virtuale classica originale. Si noti che gli indirizzi IP possono comunque cambiare dopo il rollback.

Eseguire il cmdlet `Migrate-Aadds` utilizzando il parametro *-Abort* . Fornire *-ManagedDomainFqdn* per il dominio gestito di Azure AD DS preparato in una sezione precedente, ad esempio *contoso.com*:

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn contoso.com `
    -Credentials $creds
```

### <a name="restore"></a>Ripristinare

Come ultima risorsa, è possibile ripristinare Azure AD Domain Services dall'ultimo backup disponibile. Nel passaggio 1 della migrazione viene effettuato un backup per assicurarsi che sia disponibile il backup più recente. Questo backup viene archiviato per 30 giorni.

Per ripristinare il dominio gestito di Azure AD DS dal backup, [aprire un ticket del caso di supporto usando il portale di Azure][azure-support]. Specificare l'ID directory, il nome di dominio e il motivo per il ripristino. Il completamento del processo di supporto e ripristino può richiedere più giorni.

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si verificano problemi dopo la migrazione al modello di distribuzione Gestione risorse, esaminare alcune delle seguenti aree comuni di risoluzione dei problemi:

* [Risolvere i problemi di aggiunta al dominio][troubleshoot-domain-join]
* [Risolvere i problemi di blocco degli account][troubleshoot-account-lockout]
* [Risolvere i problemi di accesso dell'account][troubleshoot-sign-in]
* [Risolvere i problemi di connettività LDAP sicura][tshoot-ldaps]

## <a name="next-steps"></a>Passaggi successivi

Con il dominio gestito di Azure AD DS migrato al modello di distribuzione Gestione risorse, [creare e aggiungere un dominio a una macchina virtuale Windows][join-windows] e quindi [installare gli strumenti di gestione][tutorial-create-management-vm].

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
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/1.0
