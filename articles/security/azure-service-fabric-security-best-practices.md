---
title: Procedure consigliate per la sicurezza di Azure Service Fabric | Microsoft Docs
description: Questo articolo offre un set di procedure consigliate per la sicurezza di Azure Service Fabric.
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: tomsh
ms.openlocfilehash: a8b76e2895edcdbbddafbee7116e163d1789c06d
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="azure-service-fabric-security-best-practices"></a>Procedure consigliate per la sicurezza di Azure Service Fabric
Distribuire un'applicazione in Azure è veloce, semplice ed economico. Prima di distribuire l'applicazione cloud in produzione, esaminare l'elenco di procedure consigliate essenziali e consigliate per l'implementazione di cluster sicuri nell'applicazione.

Azure Service Fabric è una piattaforma di sistemi distribuiti che semplifica la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili. Service Fabric fa fronte anche alle principali problematiche correlate allo sviluppo e alla gestione delle applicazioni cloud. Gli sviluppatori e gli amministratori non devono più occuparsi di risolvere complessi problemi di infrastruttura e possono concentrarsi sull'implementazione di carichi di lavoro cruciali e impegnativi, con la certezza di assicurare scalabilità, affidabilità e gestibilità. 

Per ogni procedura consigliata verrà illustrato:

-   In cosa consiste la procedura consigliata.
-   Motivi per cui implementare la procedura consigliata.
-   Scenari possibili in caso di mancata implementazione della procedura consigliata.
-   Come imparare a implementare la procedura consigliata.

È consigliabile implementare le procedure consigliate seguenti per la sicurezza di Azure Service Fabric:

-   Usare i modelli di Azure Resource Manager e il modulo PowerShell di Service Fabric per creare cluster sicuri.
-   Usare certificati X.509.
-   Configurare i criteri di sicurezza.
-   Implementare la configurazione della sicurezza in Reliable Actors.
-   Configurare l'autenticazione SSL per Azure Service Fabric.
-   Usare l'isolamento e la sicurezza di rete con Azure Service Fabric.
-   Configurare Azure Key Vault per la sicurezza.
-   Assegnare utenti ai ruoli.


## <a name="best-practices-for-securing-your-clusters"></a>Procedure consigliate per proteggere i cluster

Usare sempre un cluster sicuro:
-   Implementare la sicurezza del cluster usando certificati.
-   Garantire l'accesso client (amministratore e sola lettura) usando Azure Active Directory (Azure AD).

Usare distribuzioni automatiche:
-   Usare script per generare, implementare e distribuire segreti.
-   Archiviare i segreti in Azure Key Vault e usare Azure AD per l'accesso in tutti gli altri client.
-   Richiedere l'autenticazione per l'accesso umano ai segreti.

Valutare anche le opzioni di configurazione seguenti:
-   Creare reti perimetrali usando gruppi di sicurezza di rete (NSG, Network Security Group).
-   Accedere a macchine virtuali (VM) cluster o gestire il cluster usando server di collegamento con Connessione Desktop remoto.

Per impedire a utenti non autorizzati di connettersi ai cluster, è necessario proteggerli, in particolare quando è in esecuzione un cluster. La creazione di cluster non protetti, anche se possibile, potrebbe consentire a utenti anonimi di connettersi a un cluster che espone gli endpoint di gestione a Internet pubblico.

Sono disponibili tre [scenari](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) per implementare la sicurezza del cluster con varie tecnologie.

-   Sicurezza da nodo a nodo: questo scenario protegge la comunicazione tra le macchine virtuali e i computer nel cluster. Questa forma di sicurezza assicura che solo i computer autorizzati a connettersi al cluster possano partecipare all'hosting di applicazioni e servizi nel cluster.
In questo scenario i cluster in esecuzione in Azure o i cluster autonomi in esecuzione in Windows possono usare la [sicurezza basata su certificati](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) o la [sicurezza di Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) per computer Windows Server.
-   Sicurezza da client a nodo: questo scenario protegge la comunicazione tra un client Service Fabric e i singoli nodi nel cluster.
-   Controllo degli accessi in base al ruolo: questo scenario usa identità separate (certificati, Azure AD e così via) per ogni ruolo client di amministratore e utente che accede al cluster. Specificare le identità del ruolo quando si crea il cluster.

>[!NOTE]
>**Consigli di sicurezza per i cluster di Azure.** usare la sicurezza di Azure AD per l'autenticazione dei client e dei certificati per la sicurezza da nodo a nodo.

Per configurare un cluster Windows autonomo, vedere [Impostazioni di configurazione per un cluster autonomo in Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Usare i modelli di Azure Resource Manager e il modulo PowerShell di Service Fabric per creare un cluster sicuro.
Per istruzioni dettagliate sulla creazione di un cluster di Service Fabric sicuro con modelli di Azure Resource Manager, vedere [Creare un cluster di Service Fabric usando Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Usare il modello di Azure Resource Manager:
-   Personalizzare il cluster usando il modello per configurare lo spazio di archiviazione gestito per i dischi rigidi virtuali delle macchine virtuali.
-   Apportare modifiche al gruppo di risorse usando il modello per semplificare la gestione e il controllo delle configurazioni.

Gestire la configurazione del cluster come codice:
-   Controllare approfonditamente le configurazioni di distribuzione.
-   Evitare di usare comandi impliciti per modificare direttamente le risorse.

È possibile automatizzare numerosi aspetti del [ciclo di vita dell'applicazione Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) . Il [modulo PowerShell di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatizza le attività di uso più comune per la distribuzione, l'aggiornamento, la rimozione e il test delle applicazioni di Azure Service Fabric. Sono disponibili anche API gestite e HTTP per la gestione delle applicazioni.

## <a name="use-x509-certificates"></a>Usare certificati X.509
Proteggere sempre i cluster con certificati x.509 o la sicurezza di Windows. La sicurezza è configurata solo al momento della creazione del cluster. Non è possibile attivare sicurezza dopo la creazione del cluster.

Per specificare un [certificato cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), impostare il valore della proprietà **ClusterCredentialType** su X509. Per specificare un certificato del server per le connessioni esterne, impostare il valore della proprietà **ServerCredentialType** su X509.

Seguire anche queste procedure consigliate:
-   Creare i certificati per i cluster di produzione usando un servizio di certificati di Windows Server configurato correttamente. È anche possibile ottenere i certificati da un'autorità di certificazione approvata.
-   Non usare mai un certificato temporaneo o di prova per i cluster di produzione, se il certificato è stato creato con MakeCert.exe o uno strumento simile.
-   Usare un certificato autofirmato per i cluster di prova, ma non per i cluster di produzione.

Se il cluster non è protetto, chiunque può connettersi in qualsiasi momento ed eseguire operazioni di gestione. Per questo motivo, proteggere sempre i cluster di produzione con certificati X.509 o la sicurezza di Windows.

Per altre informazioni sui certificati X.509, vedere [Aggiungere o rimuovere certificati per un cluster Service Fabric in Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Configurare criteri di sicurezza
Service Fabric protegge anche le risorse usate dalle applicazioni. Risorse tra cui file, directory e certificati vengono archiviate negli account utente quando l'applicazione viene distribuita. Con questa funzione le applicazioni in esecuzione, anche in un ambiente ospitato condiviso, sono reciprocamente protette.

-   Usare un utente o un gruppo di dominio Active Directory: eseguire il servizio con le credenziali per un account utente o un gruppo di Active Directory. Accertarsi di usare Active Directory locale nel dominio e non Azure Active Directory. Accedere ad altre risorse del dominio a cui sono state concesse le autorizzazioni usando un utente o un gruppo del dominio. Ad esempio, risorse come condivisioni file.

-   Assegnare criteri di accesso di sicurezza per gli endpoint HTTP e HTTPS: specificare la proprietà **SecurityAccessPolicy** per applicare criteri **RunAs** a un servizio quando il manifesto del servizio dichiara le risorse di endpoint con HTTP. Le porte allocate agli endpoint HTTP sono elenchi con controllo corretto degli accessi per l'account utente RunAs in cui è in esecuzione il servizio. Se i criteri non sono impostati, http.sys non ha accesso al servizio e le chiamate del client possono avere esito negativo.

Per informazioni su come usare i criteri di sicurezza in un cluster di Service Fabric, vedere [Configurare i criteri di sicurezza per l'applicazione](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementare la configurazione della sicurezza in Reliable Actors
Service Fabric Reliable Actors è un'implementazione dello schema progettuale dell'attore. Come per gli altri schemi progettuali del software, la scelta di usare un criterio specifico dipende dalla capacità di un problema di progettazione software di seguire lo schema.

In generale, usare lo schema progettuale di attore per modellare soluzioni per i problemi software o gli scenari di sicurezza seguenti:
-   Il problema riguarda un gran numero (migliaia o più) di piccole unità indipendenti e isolate di stato e logica.
-   Si usano oggetti a thread singolo che non richiedono un'interazione significativa con componenti esterni, incluse query dello stato tra un insieme di attori.
-   Le istanze degli attori non bloccano i chiamanti con ritardi imprevedibili eseguendo operazioni I/O.

In Service Fabric gli attori vengono implementati nel framework dell'applicazione Reliable Actors. Questo framework è basato sul criterio di attore e su [Reliable Services di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Ogni servizio di tipo Reliable Actor che viene scritto è un servizio Reliable partizionato con stato.

Ogni attore è definito come un'istanza di un tipo di attore, come un oggetto .NET è un'istanza di un tipo .NET. Ad esempio, per un **tipo di attore** che implementa la funzionalità di una calcolatrice possono essere distribuiti diversi attori di quel tipo su vari nodi di un cluster. Ogni attore distribuito è caratterizzato in modo univoco da un identificatore.

Le [configurazioni di sicurezza del replicatore](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) consentono di proteggere il canale di comunicazione usato durante la replica. Questa configurazione impedisce ai servizi di vedere il traffico di replica reciproco e verifica la sicurezza dei dati a disponibilità elevata. Per impostazione predefinita, una sezione di configurazione della sicurezza vuota non abilita la sicurezza della replica.
Le configurazioni del replicatore riguardano il replicatore responsabile di garantire l'elevata affidabilità dello stato del provider di stato degli attori.

## <a name="configure-ssl-for-azure-service-fabric"></a>Configurare l'autenticazione SSL per Azure Service Fabric
Il processo di autenticazione server [autentica](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) gli endpoint di gestione del cluster rispetto a un client di gestione. Il client di gestione riconosce quindi di comunicare con il cluster reale. Questo certificato fornisce anche un certificato [SSL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) per l'API di gestione HTTPS e per Service Fabric Explorer tramite HTTPS.
È necessario ottenere un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da un'autorità di certificazione, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per il cluster.

Prima di configurare SSL per un'applicazione, è necessario ottenere un certificato SSL firmato da un'autorità di certificazione. L'autorità di certificazione è una terza parte attendibile che emette certificati per motivi di sicurezza SSL. Se non si dispone già di un certificato SSL, è necessario ottenerne uno da un rivenditore di certificati SSL.

Il certificato deve soddisfare i requisiti seguenti per i certificati SSL in Azure:
-   Il certificato deve includere una chiave privata.

-   Il certificato deve essere creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange).

-   Il nome del soggetto del certificato deve corrispondere al nome di dominio usato per accedere al servizio cloud.

    - Acquistare un nome di dominio personalizzato da usare per accedere al servizio cloud.
    - Richiedere un certificato da un'autorità di certificazione con un nome di soggetto corrispondente al nome di dominio personalizzato del servizio. Se ad esempio il nome di dominio personalizzato è __contoso__**.com**, il nome del soggetto del certificato dell'autorità di certificazione deve essere **.contoso.com** o __www__**.contoso.com**.

    >[!NOTE]
    >Non è possibile ottenere un certificato SSL da un'autorità di certificazione per il dominio __cloudapp__**.net**.
    
-   Per il certificato deve essere usata una crittografia di almeno 2.048 bit.

Il protocollo HTTP non è sicuro ed è soggetto ad attacchi di eavesdropping. I dati trasmessi su HTTP vengono inviati come testo normale dal Web browser al server Web o tra altri endpoint. Gli utenti malintenzionati possono intercettare e visualizzare i dati sensibili inviati tramite HTTP, ad esempio i dati della carta di credito e gli account di accesso. Quando i dati vengono inviati o pubblicati tramite un browser che usa il protocollo HTTPS, l'autenticazione SSL assicura che le informazioni sensibili siano crittografate e protette da intercettazione.

Per altre informazioni sull'uso dei certificati SSL, vedere [Configurazione di SSL per un'applicazione in Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Usare l'isolamento e la sicurezza di rete con Azure Service Fabric
Configurare un cluster 3 nodetype sicuro con il [modello di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) come esempio. Controllare il traffico di rete in ingresso e in uscita usando il modello e i gruppi di sicurezza di rete.

Il modello dispone di un gruppo di sicurezza di rete per ogni set di scalabilità di macchine virtuali e consente di controllare il traffico da e verso il set. Per impostazione predefinita, le regole vengono configurate per consentire tutto il traffico necessario per i servizi di sistema e le porte delle applicazioni specificate nel modello. Rivedere tali regole e modificarle in base alle proprie esigenze, inclusa l'aggiunta di nuove regole per le applicazioni in uso.

Per altre informazioni, vedere alcuni [scenari di rete comuni per Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-azure-key-vault-for-security"></a>Configurare Azure Key Vault per la sicurezza
Service Fabric usa certificati per fornire l'autenticazione e la crittografia e proteggere un cluster e le relative applicazioni.

Service Fabric usa certificati X.509 per proteggere un cluster e offrire le funzionalità di sicurezza dell'applicazione. Azure Key Vault viene impiegato per [gestire i certificati](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) dei cluster di Service Fabric in Azure. Il provider di risorse di Azure che crea i cluster esegue il pull di tutti i certificati da un insieme di credenziali delle chiavi. Il provider installa quindi i certificati nelle macchine virtuali quando il cluster viene distribuito in Azure.

Esiste una relazione di certificati tra [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), il cluster di Service Fabric e il provider di risorse che usa i certificati. In fase di creazione del cluster le informazioni sulla relazione di certificati vengono archiviate in un insieme di credenziali delle chiavi.

Per configurare un insieme di credenziali delle chiavi sono disponibili due passaggi di base:
1. Creare un gruppo di risorse specifico per l'insieme di credenziali delle chiavi.

    È consigliabile inserire l'insieme di credenziali delle chiavi nel relativo gruppo di risorse. Questa azione impedisce la perdita delle chiavi e dei segreti se vengono rimossi altri gruppi di risorse, ad esempio una risorsa di archiviazione, una risorsa di calcolo o il gruppo che contiene il cluster. Il gruppo di risorse che contiene l'insieme di credenziali delle chiavi deve essere situato nella stessa area del cluster che lo usa.

2. Creare un insieme di credenziali delle chiavi nel nuovo gruppo di risorse.

    L'insieme di credenziali delle chiavi deve essere abilitato per la distribuzione. Il provider di risorse di calcolo può quindi ottenere i certificati dall'insieme di credenziali e installarli nelle istanze di macchine virtuali.

Per altre informazioni su come configurare un insieme di credenziali delle chiavi, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-to-roles"></a>Assegnare utenti ai ruoli
Dopo aver creato le applicazioni per rappresentare il cluster, assegnare gli utenti ai ruoli supportati da Service Fabric: sola lettura e amministratore. È possibile assegnare questi ruoli usando il portale di Azure.

>[!NOTE]
> Per altre informazioni sull'uso dei ruoli in Service Fabric, vedere [Controllo di accesso basato sui ruoli per i client di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

Azure Service Fabric supporta due tipi di controllo di accesso per i client che sono connessi a un [cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm): amministratore e utente. L'amministratore del cluster può usare il controllo di accesso per limitare l'accesso a determinate operazioni di cluster per diversi gruppi di utenti. In questo modo il cluster è più sicuro.

## <a name="next-steps"></a>Passaggi successivi
- Configurare l'[ambiente di sviluppo](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) di Service Fabric.
- Informazioni sulle [opzioni di supporto di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
