---
title: Creare un cluster di Service Fabric nel portale di Azure | Documentazione Microsoft
description: Questo articolo descrive come configurare un cluster di Service Fabric protetto in Azure tramite il portale di Azure e l'insieme di credenziali delle chiavi di Azure.
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
ms.date: 02/09/2018
ms.author: chackdan
ms.openlocfilehash: 864952554b04d8d6bc14aad92d168298e554710e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Creare un cluster di Service Fabric in Azure tramite il portale di Azure
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portale di Azure](service-fabric-cluster-creation-via-portal.md)
> 
> 

Questo articolo contiene una guida dettagliata che illustra i passaggi per la configurazione di un cluster di Service Fabric (Linux or Windows) in Azure tramite il portale di Azure. La guida fornisce istruzioni dettagliate sulle operazioni seguenti:

* Creare un cluster in Azure tramite il portale di Azure.
* Autenticare gli amministratori che usano i certificati.

> [!NOTE]
> Per le opzioni di sicurezza avanzata, ad esempio l'autenticazione dell'utente con Azure Active Directory e la configurazione dei certificati per la protezione delle applicazioni, [creare il cluster usando Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Sicurezza del cluster 
I certificati vengono usati in Service Fabric per fornire l'autenticazione e la crittografia e proteggere i vari aspetti di un cluster e delle sue applicazioni. Per altre informazioni sull'uso dei certificati in Service Fabric, vedere [Scenari di sicurezza di un cluster di Service Fabric][service-fabric-cluster-security].

Se è la prima volta che si sta creando un cluster dell'infrastruttura di servizio o si sta distribuendo un cluster per dei carichi di lavoro di prova, è possibile passare alla sezione successiva (**Crea cluster nel portale di Azure**) e ottenere dal sistema i certificati necessari per i cluster che eseguono carichi di lavoro di prova. Se si configura un cluster per i carichi di lavoro di produzione, continuare la lettura.

#### <a name="cluster-and-server-certificate-required"></a>Cluster e certificato del server (obbligatorio)
Questo certificato è richiesto per proteggere un cluster e impedirne accessi non autorizzati. Il certificato fornisce protezione del cluster in due modi:

* **Autenticazione del cluster:** autentica la comunicazione da nodo a nodo per la federazione di cluster. Solo i nodi che possono dimostrare la propria identità con il certificato possono essere aggiunti al cluster.
* **Autenticazione del server:** autentica gli endpoint di gestione del cluster in un client di gestione, in modo che il client di gestione sappia con certezza di comunicare con il cluster reale. Questo certificato fornisce anche SSL per l'API di gestione HTTPS e per Service Fabric Explorer tramite HTTPS.

A tale scopo, il certificato deve soddisfare i requisiti seguenti:

* Il certificato deve includere una chiave privata.
* Il certificato deve essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange).
* Il nome del **soggetto del certificato deve corrispondere al dominio** usato per accedere al cluster Service Fabric. È necessario fornire il SSL per gli endpoint di gestione HTTPS del cluster e Service Fabric Explorer. Non è possibile ottenere un certificato SSL da un'Autorità di certificazione (CA) per il dominio `.cloudapp.azure.com` . Acquistare un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per il cluster.

#### <a name="client-authentication-certificates"></a>Certificati di autenticazione client
I certificati client aggiuntivi autenticano gli amministratori per le attività di gestione del cluster. Service Fabric ha due livelli di accesso: **admin** e **utente di sola lettura**. Deve essere usato almeno un certificato per l'accesso amministrativo. Per l'accesso aggiuntivo a livello di utente, è necessario specificare un certificato separato. Per altre informazioni sui ruoli di accesso, vedere [Controllo degli accessi in base al ruolo per i client di Service Fabric][service-fabric-cluster-security-roles].

Per usare Service Fabric non è necessario caricare I certificati di autenticazione client nell'insieme di credenziali delle chiavi. Questi certificati devono essere forniti solo agli utenti autorizzati alla gestione del cluster. 

> [!NOTE]
> Azure Active Directory è il metodo consigliato per autenticare i client per le operazioni di gestione del cluster. Per usare Azure Active Directory, è necessario [creare un cluster tramite Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certificati delle applicazioni (facoltativo)
Per motivi di sicurezza dell'applicazione, è possibile installare nel cluster numerosi certificati aggiuntivi. Prima di creare il cluster, considerare gli scenari di protezione delle applicazioni che richiedono l'installazione di un certificato sui nodi, ad esempio:

* Crittografia e decrittografia dei valori di configurazione dell'applicazione
* Crittografia dei dati tra i nodi durante la replica 

I certificati delle applicazioni non possono essere configurati durante la creazione di un cluster tramite il portale di Azure. Per configurare i certificati delle applicazioni in fase di installazione del cluster, è necessario [creare un cluster tramite Azure Resource Manager][create-cluster-arm]. È anche possibile aggiungere certificati delle applicazione al cluster dopo la creazione.

</a "create-cluster-portal" ></a>

## <a name="create-cluster-in-the-azure-portal"></a>Creare un cluster nel portale di Azure

La creazione di un cluster di produzione per soddisfare le esigenze dell'applicazione implica una pianificazione che aiuti in questo compito. Si consiglia di leggere e accettare il documento [Considerazioni sulla pianificazione del Cluster di Service Fabric][service-fabric-cluster-capacity]. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Cercare la risorsa cluster di Service Fabric
![ricerca del modello di cluster di Service Fabric nel portale di Azure.][SearchforServiceFabricClusterTemplate]

1. Accedere al [portale di Azure][azure-portal].
2. Fare clic su **Crea una risorsa** per aggiungere un nuovo modello di risorsa. Cercare il modello del cluster di Service Fabric in **Marketplace**, nella sezione **Tutto**.
3. Selezionare **Cluster di Service Fabric** nell'elenco.
4. Passare al pannello **Cluster di Service Fabric** e fare clic su **Crea**.
5. Il pannello **Crea cluster di Service Fabric** include i quattro passaggi descritti di seguito:

#### <a name="1-basics"></a>1. Nozioni di base
![Schermata della creazione di un nuovo gruppo di risorse.][CreateRG]

Nel pannello Informazioni di base è necessario fornire i dettagli di base per il cluster.

1. Immettere il nome del cluster.
2. Scegliere un **nome utente** e una **Password** per il desktop remoto delle macchine virtuali.
3. Assicurarsi di selezionare la **sottoscrizione** in cui si vuole distribuire il cluster, soprattutto se sono presenti più sottoscrizioni.
4. Creare un **nuovo gruppo di risorse**. Assegnare al gruppo lo stesso nome del cluster per poterlo trovare più facilmente in seguito, soprattutto quando si cerca di apportare modifiche alla distribuzione e/o eliminare il cluster.
   
   > [!NOTE]
   > Anche se è possibile decidere di usare un gruppo di risorse esistente, è meglio crearne uno nuovo, Ciò rende facile eliminare i cluster e tutte le risorse da esso utilizzate.
   > 
   > 
5. Selezionare l' **area** in cui si desidera creare il cluster. Se si prevede di usare un certificato esistente già caricato in un insieme di credenziali delle chiavi, è necessario usare la stessa area che corrisponde all'insieme di credenziali delle chiavi. 

#### <a name="2-cluster-configuration"></a>2. Configurazione del cluster
![Creare un tipo di nodo][CreateNodeType]

Configurare i nodi del cluster. poiché definiscono le dimensioni delle VM, il numero di VM e le relative proprietà. Il cluster può avere più di un tipo di nodo, ma il tipo di nodo primario, ovvero il primo che si definisce nel portale, deve essere costituito da almeno cinque macchine virtuali, poiché in questo tipo di nodo si trovano i servizi di sistema di Service Fabric. Non è necessario configurare le **proprietà relative alla posizione**, perché una proprietà relativa alla posizione predefinita "NodeTypeName" viene aggiunta automaticamente.

> [!NOTE]
> Uno scenario comune per diversi tipi di nodi è costituito da un'applicazione contenente un servizio front-end e un servizio back-end. Si vuole inserire il servizio front-end in macchine virtuali più piccole, (ad esempio VM con dimensioni D2_V2), con porte aperte a Internet e il servizio back-end in macchine virtuali più grandi, (ad esempio VM con dimensioni D3_V2, D6_V2, D15_V2 e così via), senza porte con connessione Internet aperte.
> 
> 

1. Scegliere un nome per il tipo di nodo (da 1 a 12 caratteri, contenenti solo lettere e numeri).
2. Le **dimensioni minime** delle macchine virtuali per il tipo di nodo primario sono determinate dal livello di **durabilità** scelto per il cluster. Il valore predefinito per il livello di durabilità è Bronze. Per altre informazioni sulla durabilità, vedere [come scegliere la durabilità di un cluster di Service Fabric][service-fabric-cluster-durability].
3. Selezionare le dimensioni della VM. Le macchine virtuali della serie D dispongono di unità SSD e sono consigliate per applicazioni con stato. Non usare SKU di VM con core parziali o meno di 10 GB di capacità disco disponibile. Fare riferimento a [documento sulle considerazioni di pianificazione del cluster di service fabric][service-fabric-cluster-capacity] per informazioni su come selezionare le dimensioni della VM.
4. Scegliere il numero di macchine virtuali per il tipo di nodo. È possibile aumentare o ridurre il numero di macchine virtuali in un tipo di nodo in un secondo momento ma, per il tipo di nodo primario, il minimo è cinque per il carico di lavoro di produzione. Altri tipi di nodo possono avere un minimo di una macchina virtuale. Il valore minimo **numero** di macchine virtuali per il tipo di nodo principale condiziona l'**affidabilità** del cluster.  
5. **Cluster a un solo nodo e cluster a tre nodi** -questi sono solo a scopo di test. Non sono supportati per l'esecuzione di nessun carico di lavoro di produzione.
6. Configurare gli endpoint personalizzati. Questo campo consente di immettere un elenco di porte delimitato da virgole che si desidera esporre tramite Azure Load Balancer alla rete Internet pubblica per le applicazioni. Ad esempio, se si prevede di distribuire un'applicazione Web nel cluster, immettere "80" per consentire il traffico sulla porta 80 del cluster. Per altre informazioni sugli endpoint, vedere [Comunicazioni con le applicazioni][service-fabric-connect-and-communicate-with-services]
7. Configurare la **diagnostica**del cluster. Per impostazione predefinita, la diagnostica è abilitata nel cluster come supporto per la risoluzione dei problemi. Per disabilitare la diagnostica, impostare lo **stato** su **No**. **Non** è consigliabile disattivare la diagnostica. Se si dispone già di un progetto di Application Insights creato, assegnare le rispettive chiavi in modo che le analisi dello stack dell'applicazione vengano indirizzate verso il progetto.
8. Selezionare la modalità di aggiornamento di Fabric che si vuole impostare per il cluster. Selezionare **Automatic**(Automatici) se si vuole che il sistema acquisisca automaticamente la versione più recente disponibile e provi ad aggiornare il cluster. Impostare la modalità su **Manual**(Manuali) se si vuole scegliere una versione supportata. Per altri dettagli sulla modalità di aggiornamento di Fabric, vedere il documento sull'[aggiornamento di un cluster di Service Fabric][service-fabric-cluster-upgrade].

> [!NOTE]
> Microsoft supporta solo i cluster che eseguono versioni supportate di Service Fabric. Selezionando la modalità **Manual** (Manuali), l'utente si assume la responsabilità di aggiornare il cluster a una versione supportata. > 
> 

#### <a name="3-security"></a>3. Sicurezza
![Schermata delle configurazioni di sicurezza nel portale di Azure.][BasicSecurityConfigs]

Per facilitare l'impostazione di un cluster di test sicuro per l'utente, Microsoft ha fornito l'opzione **Basic**. Se si dispone di un certificato e lo si è già caricato nell'insieme di credenziali delle chiavi (e si è attivato l'insieme di credenziali delle chiavi per la distribuzione), usare l'opzione **Personalizzato**

##### <a name="basic-option"></a>Opzione di base
Seguire le schermate per aggiungere o usare di nuovo un insieme di credenziali delle chiavi e aggiungere un certificato. L'aggiunta del certificato è un processo sincrono e pertanto sarà necessario attendere che il certificato venga creato.


Resistere alla tentazione di uscire dalla schermata finché non viene completato il processo precedente.

![CreateKeyVault]

Ora che il certificato è aggiunto all'insieme di credenziali delle chiavi, è possibile vedere la schermata seguente che richiede di modificare i criteri di accesso per l'insieme delle credenziali delle chiavi. fare clic su **Modifica criteri di accesso per.** .

![CreateKeyVault2]

Fare clic su criteri di accesso avanzati e abilitare l'accesso alle macchine virtuali per la distribuzione. È consigliabile abilitare anche la distribuzione del modello. Dopo aver effettuato le selezioni, non dimenticare di fare clic sul pulsante **Salva** e uscire dal riquadro **Criteri di accesso**.

![CreateKeyVault3]

Ora si è pronti a procedere con il resto del processo di creazione del cluster.

![CreateKeyVault4]

##### <a name="custom-option"></a>Opzione Personalizzata
Ignorare questa sezione, se è stato già eseguita la procedura nell'opzione **Basic**.

![SecurityCustomOption]

Sono necessarie le informazioni del CertificateThumbprint, del SourceVault e del CertificateURL per completare la pagina di sicurezza. Se non è a disposizione, aprire un'altra finestra del browser ed eseguire le operazioni seguenti


1. Passare all'insieme delle credenziali delle chiavi, selezionare il certificato. 
2. Selezionare la scheda "proprietà" e copiare 'l'ID RISORSA' in "Insieme delle credenziali delle chiave di origine" nell'altra finestra del browser 

    ![CertInfo0]

3. A questo punto, selezionare la scheda "Certificati".
4. Fare clic sull'identificazione personale del certificato, che consente di passare alla pagina delle versioni.
5. Fare clic sui GUID visualizzati nella versione corrente.

    ![CertInfo1]

6. Ora si dovrebbe visualizzare la schermata seguente. Copiare 'L'identificazione personale' in 'Identificazione personale del certificato' nell'altra finestra del browser
7. Copiare le informazioni 'Identificatore del segreto' in 'URL del certificato' nell'altra finestra del browser.


![CertInfo2]


Selezionare la casella **Configura impostazioni avanzate** per l'immissione dei certificati client per **amministratore client** e **client di sola lettura**. In questi campi, immettere l'identificazione personale del certificato client amministratore e l'identificazione personale del certificato client utente di sola lettura, se applicabile. Quando gli amministratori provano a connettersi al cluster, viene concesso l'accesso solo se hanno un certificato con identificazione personale corrispondente ai valori di identificazione personale immessi in questi campi.  

#### <a name="4-summary"></a>4. Summary

È ora possibile distribuire il cluster. Prima di procedere, scaricare il certificato, cercare all'interno della grande casella informativa blu il collegamento. Assicurarsi di mantenere il certificato in un luogo sicuro. è necessario per connettersi al cluster. Poiché il certificato che è stato scaricato non ha una password, è consigliabile aggiungere uno.

Per completare la creazione del cluster fare clic su **Crea**. È possibile, facoltativamente, scaricare il modello. 

![Summary]

È possibile visualizzare lo stato di avanzamento del processo di creazione nell'area delle notifiche: fare clic sull'icona a forma di campana accanto alla barra di stato nell'angolo superiore destro della schermata. Se durante la creazione del cluster si è fatto clic su **Aggiungi alla Schermata iniziale** durante la creazione del cluster, la voce **Distribuzione di Cluster di Service Fabric** viene aggiunta alla **schermata iniziale**.

Per eseguire operazioni di gestione nel cluster tramite Powershell o Interfaccia della riga di comando, è necessario connettersi al cluster, per ottenere altre informazioni leggere [Connessione al cluster](service-fabric-connect-to-secure-cluster.md).

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
Ognuno dei tipi di nodo specificati nel cluster determina la configurazione di un set di scalabilità di macchine virtuali. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

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
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
<!--[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md#remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node -->
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Summary]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
