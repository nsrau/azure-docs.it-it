
---
title: Creare un cluster di Service Fabric nel portale di Azure | Documentazione Microsoft
description: Questo articolo descrive come configurare un cluster di Service Fabric protetto in Azure tramite il portale di Azure e l&quot;insieme di credenziali delle chiavi di Azure.
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c413f415cb056f079ed30cf444af4edbe20364ea
ms.lasthandoff: 01/25/2017


---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Creare un cluster di Service Fabric in Azure tramite il portale di Azure
> [!div class="op_single_selector"]
> * [Gestione risorse di Azure](service-fabric-cluster-creation-via-arm.md)
> * [Portale di Azure](service-fabric-cluster-creation-via-portal.md)
> 
> 

Questo articolo contiene una guida dettagliata che illustra i passaggi per la configurazione di un cluster di Service Fabric protetto in Azure tramite il portale di Azure. La guida fornisce istruzioni dettagliate sulle operazioni seguenti:

* Configurare l'insieme di credenziali delle chiavi per la gestione delle chiavi ai fini della protezione del cluster.
* Creare un cluster protetto in Azure tramite il portale di Azure.
* Autenticare gli amministratori che usano i certificati.

> [!NOTE]
> Per le opzioni di sicurezza avanzata, ad esempio l'autenticazione dell'utente con Azure Active Directory e la configurazione dei certificati per la protezione delle applicazioni, [creare il cluster usando Azure Resource Manager][create-cluster-arm].
> 
> 

Un cluster protetto è un cluster che impedisce l'accesso non autorizzato alle operazioni di gestione, tra cui distribuzione, aggiornamento ed eliminazione di applicazioni, servizi e dati in esso contenuti. Un cluster non protetto è un cluster a cui tutti gli utenti possono connettersi in qualsiasi momento ed eseguire operazioni di gestione. Sebbene sia possibile creare un cluster non protetto, è **consigliabile creare un cluster protetto**. Un cluster non protetto **non può essere protetto in un secondo momento**. Sarà necessario crearne uno nuovo.

I concetti sono gli stessi per la creazione di cluster protetti, sia Linux che Windows. Per altre informazioni e script helper per la creazione di cluster Linux protetti, vedere [Creare cluster protetti in Linux](service-fabric-cluster-creation-via-arm.md#secure-linux-cluster). I parametri ottenuti dallo script helper fornito possono essere immessi direttamente nel portale come descritto nella sezione [Creare un cluster nel portale di Azure](#create-cluster-portal).

## <a name="log-in-to-azure"></a>Accedere ad Azure
Questa guida usa [Azure PowerShell][azure-powershell]. Quando si avvia una nuova sessione di PowerShell, accedere al proprio account Azure e selezionare la sottoscrizione prima di eseguire i comandi di Azure.

Accedere all'account Azure:

```powershell
Login-AzureRmAccount
```

Selezionare la propria sottoscrizione:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>Configurare l'insieme di credenziali delle chiavi
Questa parte della guida illustra la creazione di un insieme di credenziali delle chiavi per un cluster di Service Fabric in Azure e per le applicazioni di Service Fabric. Per una guida completa sull'insieme di credenziali delle chiavi, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure][key-vault-get-started].

Per la protezione del cluster, Service Fabric usa certificati X.509. L'insieme di credenziali delle chiavi di Azure viene usato per gestire i certificati dei cluster di Service Fabric in Azure. Quando viene distribuito un cluster in Azure, il provider di risorse di Azure responsabile della creazione di cluster Service Fabric estrae i certificati dall'insieme di credenziali delle chiavi e li installa nelle macchine virtuali del cluster.

Nel diagramma seguente viene illustrata la relazione tra l'insieme di credenziali delle chiavi, un cluster di Service Fabric e il provider di risorse di Azure che usa i certificati archiviati nell'insieme di credenziali delle chiavi durante la creazione di un cluster:

![Installazione del certificato][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Il primo passaggio consiste nel creare un nuovo gruppo di risorse specifico per l'insieme di credenziali delle chiavi. È consigliabile inserire l'insieme di credenziali delle chiavi nel proprio gruppo di risorse in modo che sia possibile rimuovere i gruppi di risorse di calcolo e archiviazione, ad esempio il gruppo di risorse con il cluster Service Fabric, senza perdere le chiavi e i segreti. Il gruppo di risorse che contiene l'insieme di credenziali delle chiavi deve essere situato nella stessa area del cluster che lo usa.

```powershell

    PS C:\Users\vturecek> New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet will be modified in a future release.

    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>Creare un insieme di credenziali delle chiavi
Creare un insieme di credenziali delle chiavi nel nuovo gruppo di risorse. L'insieme di credenziali delle chiavi **deve essere abilitato per la distribuzione** per consentire al provider di risorse di Service Fabric di ottenere i certificati e installarli nei nodi del cluster:

```powershell

    PS C:\Users\vturecek> New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment


    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```

Se si dispone già di un insieme di credenziali delle chiavi, è possibile abilitarlo per la distribuzione tramite l'interfaccia della riga di comando di Azure:

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```


## <a name="add-certificates-to-key-vault"></a>Aggiungere i certificati all'insieme di credenziali delle chiavi
I certificati vengono usati in Service Fabric per fornire l'autenticazione e la crittografia e proteggere i vari aspetti di un cluster e delle sue applicazioni. Per altre informazioni sull'uso dei certificati in Service Fabric, vedere [Scenari di sicurezza di un cluster di Service Fabric][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Cluster e certificato del server (obbligatorio)
Questo certificato è richiesto per proteggere un cluster e impedirne accessi non autorizzati. Il certificato fornisce protezione del cluster in due modi:

* **Autenticazione del cluster:** autentica la comunicazione da nodo a nodo per la federazione di cluster. Solo i nodi che possono dimostrare la propria identità con il certificato possono essere aggiunti al cluster.
* **Autenticazione del server:** autentica gli endpoint di gestione del cluster in un client di gestione, in modo che il client di gestione sappia con certezza di comunicare con il cluster reale. Questo certificato fornisce anche SSL per l'API di gestione HTTPS e per Service Fabric Explorer tramite HTTPS.

A tale scopo, il certificato deve soddisfare i requisiti seguenti:

* Il certificato deve includere una chiave privata.
* Il certificato deve essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange).
* Il nome del soggetto del certificato deve corrispondere al dominio usato per accedere al cluster Service Fabric. È necessario fornire il SSL per gli endpoint di gestione HTTPS del cluster e Service Fabric Explorer. Non è possibile ottenere un certificato SSL da un'Autorità di certificazione (CA) per il dominio `.cloudapp.azure.com` . Acquistare un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per il cluster.

### <a name="client-authentication-certificates"></a>Certificati di autenticazione client
I certificati client aggiuntivi autenticano gli amministratori per le attività di gestione del cluster. Service Fabric ha due livelli di accesso: **admin** e **utente di sola lettura**. Deve essere usato almeno un certificato per l'accesso amministrativo. Per l'accesso aggiuntivo a livello di utente, è necessario specificare un certificato separato. Per altre informazioni sui ruoli di accesso, vedere [Controllo degli accessi in base al ruolo per i client di Service Fabric][service-fabric-cluster-security-roles].

Per usare Service Fabric non è necessario caricare I certificati di autenticazione client nell'insieme di credenziali delle chiavi. Questi certificati devono essere forniti solo agli utenti autorizzati alla gestione del cluster. 

> [!NOTE]
> Azure Active Directory è il metodo consigliato per autenticare i client per le operazioni di gestione del cluster. Per usare Azure Active Directory, è necessario [creare un cluster tramite Azure Resource Manager][create-cluster-arm].
> 
> 

### <a name="application-certificates-optional"></a>Certificati delle applicazioni (facoltativo)
Per motivi di sicurezza dell'applicazione, è possibile installare nel cluster numerosi certificati aggiuntivi. Prima di creare il cluster, considerare gli scenari di protezione delle applicazioni che richiedono l'installazione di un certificato sui nodi, ad esempio:

* Crittografia e decrittografia dei valori di configurazione dell'applicazione
* Crittografia dei dati tra i nodi durante la replica 

I certificati delle applicazioni non possono essere configurati durante la creazione di un cluster tramite il portale di Azure. Per configurare i certificati delle applicazioni in fase di installazione del cluster, è necessario [creare un cluster tramite Azure Resource Manager][create-cluster-arm]. È anche possibile aggiungere certificati delle applicazione al cluster dopo la creazione.

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Formattazione dei certificati per l'uso di provider di risorse di Azure
I file di chiave privata (con estensione pfx) possono essere aggiunti e usati direttamente tramite l'insieme di credenziali delle chiavi. Tuttavia, il provider di risorse di Azure richiede l'archiviazione delle chiavi in un particolare formato JSON che include il file con estensione pfx come stringa con codifica Base64 e la password della chiave privata. Per soddisfare questi requisiti, le chiavi devono essere inserite in una stringa JSON e quindi archiviate come *segreti* nell'insieme di credenziali delle chiavi.

Per semplificare questo processo, è [disponibile su GitHub][service-fabric-rp-helpers] un modulo di PowerShell. Per usare il modulo, seguire questa procedura:

1. Scaricare l'intero contenuto del repository in una directory locale. 
2. Importare il modulo nella finestra di PowerShell:

```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

Il comando `Invoke-AddCertToKeyVault` in questo modulo di PowerShell formatta in modo automatico una chiave privata del certificato in una stringa JSON e la carica nell'insieme di credenziali delle chiavi. Usare il comando per aggiungere il certificato del cluster ed eventuali certificati aggiuntivi delle applicazioni all'insieme di credenziali delle chiavi. Ripetere questo passaggio per tutti i certificati aggiuntivi che si vuole installare nel cluster.

```powershell
PS C:\Users\vturecek> Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet will be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault


Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

Questi sono tutti i prerequisiti dell'insieme di credenziali delle chiavi per la configurazione di un modello di Cluster Resource Manager di Service Fabric che consente di installare certificati per l'autenticazione dei nodi, per la protezione degli endpoint di gestione e per l'autenticazione, nonché qualsiasi funzionalità aggiuntiva per la sicurezza delle applicazioni che usano certificati X.509. A questo punto, dovrebbe essere visualizzata l'impostazione seguente in Azure:

* Gruppo di risorse dell'insieme di credenziali delle chiavi
  * Insieme di credenziali di chiave
    * Certificato di autenticazione del server del cluster

</a "create-cluster-portal" ></a>

## <a name="create-cluster-in-the-azure-portal"></a>Creare un cluster nel portale di Azure
### <a name="search-for-the-service-fabric-cluster-resource"></a>Cercare la risorsa cluster di Service Fabric
![ricerca del modello di cluster di Service Fabric nel portale di Azure.][SearchforServiceFabricClusterTemplate]

1. Accedere al [portale di Azure][azure-portal].
2. Fare clic su **+ Nuovo** per aggiungere un nuovo modello di risorsa. Cercare il modello del cluster di Service Fabric in **Marketplace**, nella sezione **Tutto**.
3. Selezionare **Cluster di Service Fabric** nell'elenco.
4. Passare al pannello **Cluster di Service Fabric** e fare clic su **Crea**.
5. Il pannello **Crea cluster di Service Fabric** include i quattro passaggi descritti di seguito.

#### <a name="1-basics"></a>1. Nozioni di base
![Schermata della creazione di un nuovo gruppo di risorse.][CreateRG]

Nel pannello Informazioni di base è necessario fornire i dettagli di base per il cluster.

1. Immettere il nome del cluster.
2. Scegliere un **nome utente** e una **Password** per il desktop remoto delle macchine virtuali.
3. Assicurarsi di selezionare la **sottoscrizione** in cui si vuole distribuire il cluster, soprattutto se sono presenti più sottoscrizioni.
4. Creare un **nuovo gruppo di risorse**. Assegnare al gruppo lo stesso nome del cluster per poterlo trovare più facilmente in seguito, soprattutto quando si cerca di apportare modifiche alla distribuzione e/o eliminare il cluster.
   
   > [!NOTE]
   > Anche se è possibile decidere di usare un gruppo di risorse esistente, è meglio crearne uno nuovo, in modo da poter eliminare facilmente i cluster non più necessari.
   > 
   > 
5. Selezionare l' **area** in cui si desidera creare il cluster. È necessario usare la stessa area in cui è situato l'insieme di credenziali delle chiavi.

#### <a name="2-cluster-configuration"></a>2. Configurazione del cluster
![Creare un tipo di nodo][CreateNodeType]

Configurare i nodi del cluster. poiché definiscono le dimensioni delle VM, il numero di VM e le relative proprietà. Il cluster può avere più di un tipo di nodo, ma il tipo di nodo primario, ovvero il primo che si definisce nel portale, deve essere costituito da almeno cinque macchine virtuali, poiché in questo tipo di nodo si trovano i servizi di sistema di Service Fabric. Non è necessario configurare le proprietà relative alla posizione **** , perché una proprietà relativa alla posizione predefinita "NodeTypeName" viene aggiunta automaticamente.

> [!NOTE]
> Uno scenario comune per diversi tipi di nodi è costituito da un'applicazione contenente un servizio front-end e un servizio back-end. Si vuole inserire il servizio front-end in macchine virtuali più piccole, ad esempio con dimensioni D2, con porte aperte a Internet e il servizio back-end in macchine virtuali più grandi, ad esempio con dimensioni D4, D6, D15 e così via, senza porte con connessione Internet aperte.
> 
> 

1. Scegliere un nome per il tipo di nodo (da 1 a 12 caratteri, contenenti solo lettere e numeri).
2. Le **dimensioni minime** delle macchine virtuali per il tipo di nodo primario sono determinate dal livello di **durabilità** scelto per il cluster. Il valore predefinito per il livello di durabilità è Bronze. Per altre informazioni sulla durabilità, vedere [come scegliere l'affidabilità e la durabilità di un cluster di Service Fabric][service-fabric-cluster-capacity].
3. Selezionare le dimensioni della macchina virtuale e il piano tariffario. Le macchine virtuali della serie D dispongono di unità SSD e sono consigliate per applicazioni con stato. Non usare SKU di VM con core parziali o meno di 7 GB di capacità disco disponibile. 
4. Il **numero** minimo di macchine virtuali per il tipo di nodo primario è determinato dal livello di **affidabilità** scelto per il cluster. Il valore predefinito per il livello di affidabilità è Silver. Per altre informazioni sull'affidabilità, vedere [come scegliere l'affidabilità e la durabilità di un cluster di Service Fabric][service-fabric-cluster-capacity].
5. Scegliere il numero di macchine virtuali per il tipo di nodo. È possibile aumentare o ridurre il numero di macchine virtuali in un tipo di nodo in un secondo momento ma, per il tipo di nodo primario, il minimo è determinato dal livello di affidabilità che si è scelto. Gli altri tipi di nodo possono avere un minimo di 1 VM.
6. Configurare gli endpoint personalizzati. Questo campo consente di immettere un elenco di porte delimitato da virgole che si desidera esporre tramite Azure Load Balancer alla rete Internet pubblica per le applicazioni. Ad esempio, se si prevede di distribuire un'applicazione Web nel cluster, immettere "80" per consentire il traffico sulla porta 80 del cluster. Per altre informazioni sugli endpoint, vedere [Comunicazioni con le applicazioni][service-fabric-connect-and-communicate-with-services]
7. Configurare la **diagnostica**del cluster. Per impostazione predefinita, la diagnostica è abilitata nel cluster come supporto per la risoluzione dei problemi. Per disabilitare la diagnostica, impostare lo **stato** su **No**. **Non** è consigliabile disattivare la diagnostica.
8. Selezionare la modalità di aggiornamento di Fabric che si vuole impostare per il cluster. Selezionare **Automatic**(Automatici) se si vuole che il sistema acquisisca automaticamente la versione più recente disponibile e provi ad aggiornare il cluster. Impostare la modalità su **Manual**(Manuali) se si vuole scegliere una versione supportata.

> [!NOTE]
> Microsoft supporta solo i cluster che eseguono versioni supportate di Service Fabric. Selezionando la modalità **Manual** (Manuali), l'utente si assume la responsabilità di aggiornare il cluster a una versione supportata. Per altri dettagli sulla modalità di aggiornamento di Fabric, vedere il documento sull'[aggiornamento di un cluster di Service Fabric][service-fabric-cluster-upgrade].
> 
> 

#### <a name="3-security"></a>3. Sicurezza
![Schermata delle configurazioni di sicurezza nel portale di Azure.][SecurityConfigs]

Il passaggio finale consiste nel fornire informazioni sul certificato per proteggere il cluster tramite l'insieme di credenziali delle chiavi e il certificato creato in precedenza.

* Compilare i campi del certificato primario con l'output ottenuto dal caricamento del **certificato del cluster** nell'insieme di credenziali delle chiavi usando il comando `Invoke-AddCertToKeyVault` di PowerShell.

```powershell
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47
```

* Selezionare la casella **Configura impostazioni avanzate** per l'immissione dei certificati client per **amministratore client** e **client di sola lettura**. In questi campi, immettere l'identificazione personale del certificato client amministratore e l'identificazione personale del certificato client utente di sola lettura, se applicabile. Quando gli amministratori provano a connettersi al cluster, viene concesso l'accesso solo se hanno un certificato con identificazione personale corrispondente ai valori di identificazione personale immessi in questi campi.  

#### <a name="4-summary"></a>4. Riepilogo
![Schermata iniziale con la voce "Distribuzione di un cluster di Service Fabric" visualizzata. ][Notifications]

Per completare la creazione del cluster, fare clic su **Riepilogo** per visualizzare le configurazioni specificate oppure scaricare il modello di Azure Resource Manager usato per distribuire il cluster. Dopo che sono state specificate le impostazioni obbligatorie, il pulsante **OK** diventa di colore verde ed è possibile avviare il processo di creazione del cluster facendo clic su di esso.

È possibile visualizzare lo stato di avanzamento del processo di creazione nell'area delle notifiche: fare clic sull'icona a forma di campana accanto alla barra di stato nell'angolo superiore destro della schermata. Se durante la creazione del cluster si è fatto clic su **Aggiungi alla Schermata iniziale**, la **distribuzione del cluster di Service Fabric ** verrà indicata nella **schermata iniziale**.

### <a name="view-your-cluster-status"></a>Visualizzare lo stato del cluster
![Schermata dei dettagli del cluster nel dashboard.][ClusterDashboard]

Al termine della creazione del cluster è possibile esaminare il cluster nel portale.

1. Selezionare **Sfoglia** e fare clic su **Cluster di Service Fabric**.
2. Trovare il cluster e selezionarlo.
3. È ora possibile visualizzare i dettagli del cluster nel dashboard, inclusi l'endpoint pubblico del cluster e un link per Service Fabric Explorer.

La sezione **Node Monitor** (Monitoraggio dei nodi) nel pannello del dashboard del cluster indica il numero di macchine virtuali integre e di quelle non integre. Per altre informazioni sull'integrità del cluster, vedere [Introduzione al monitoraggio dell'integrità di Service Fabric][service-fabric-health-introduction].

> [!NOTE]
> I cluster di Service Fabric richiedono che sia sempre attivo un certo numero di nodi allo scopo di mantenere la disponibilità e lo stato, ossia per "mantenere il quorum". Di conseguenza, in genere non è sicuro arrestare tutti i computer del cluster se prima non è stato eseguito un [backup completo dello stato][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Connessione remota a un'istanza di set di scalabilità di macchine virtuali o a un nodo del cluster
Ognuno dei tipi di nodo specificati nel cluster determina la configurazione di un set di scalabilità di macchine virtuali. Per informazioni dettagliate, vedere [Connessione remota a un'istanza di set di scalabilità di macchine virtuali][remote-connect-to-a-vm-scale-set].

## <a name="next-steps"></a>Passaggi successivi
A questo punto, è stato creato un cluster protetto tramite i certificati per l'autenticazione di gestione. Successivamente, [connettersi al cluster](service-fabric-connect-to-secure-cluster.md) e scoprire come [gestire i segreti delle applicazioni](service-fabric-application-secret-management.md).  Vedere anche [Service Fabric support options](service-fabric-support.md) (Opzioni di supporto di Service Fabric).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md#remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png

