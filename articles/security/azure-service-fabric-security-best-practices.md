---
title: Procedure consigliate per la sicurezza di Azure Service Fabric| Microsoft Docs
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
ms.date: 08/04/2017
ms.author: tomsh
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 3132e42aac72c0132c7526ac56d80bc5eec269e7
ms.contentlocale: it-it
ms.lasthandoff: 08/09/2017

---
# <a name="azure-service-fabric-security-best-practices"></a>Procedure consigliate per la sicurezza di Azure Service Fabric
Distribuire un'applicazione in Azure è veloce, semplice ed economico. Prima di distribuire l'applicazione cloud nell'ambiente di produzione è utile seguire una procedura consigliata per agevolare la valutazione dell'applicazione in base a un elenco di procedure consigliate essenziali.

Azure Service Fabric è una piattaforma di sistemi distribuiti che semplifica la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili. Service Fabric fa fronte anche alle principali problematiche correlate allo sviluppo e alla gestione delle applicazioni cloud. Gli sviluppatori e gli amministratori non devono più occuparsi di risolvere complessi problemi di infrastruttura e possono concentrarsi sull'implementazione di carichi di lavoro cruciali e impegnativi, con la certezza di assicurare scalabilità, affidabilità e gestibilità. 

Per ogni procedura consigliata verrà illustrato:

-   Qual è la procedura consigliata
-   Il motivo per cui si vuole abilitare tale procedura consigliata
-   Quale potrebbe essere il risultato se non fosse possibile abilitare la procedura consigliata
-   Come imparare ad abilitare la procedura consigliata

Sono al momento disponibili le seguenti procedure consigliate per Azure Service Fabric:

-   Usare il modello di Azure Resource Manager(ARM) e il modulo Azure PowerShell di Service Fabric per creare un cluster sicuro
-   Usare certificati X.509
-   Configurare criteri di sicurezza
-   Configurazione della sicurezza in Reliable Actors
-   Configurare l'autenticazione SSL per Azure Service Fabric
-   Isolamento/sicurezza di rete con Azure Service Fabric
-   Configurare un insieme di credenziali delle chiavi per la sicurezza
-   Assegnare utenti ai ruoli


## <a name="best-practices-for-securing-your-cluster"></a>Procedure consigliate per proteggere il cluster

**Quadro generale**

Usare sempre un cluster sicuro
-   Sicurezza del cluster: usare i certificati
-   Accesso client (Admin e sola lettura): usare AAD

Usare distribuzioni automatiche
-   Usare script per generare, installare e distribuire segreti
-   Conservare i segreti nell'insieme di credenziali delle chiavi, usare AD per tutti gli altri accessi client
-   Nessun utente dovrebbe potervi accedere senza autenticazione.

Valutare anche gli aspetti seguenti:
-   Creare reti perimetrali usando gruppi di sicurezza di rete (NSG)
-   Usare server di collegamento a RDP nelle VM del cluster o per gestire il cluster

Per impedire a utenti non autorizzati di connettersi ai cluster, è necessario proteggerli, in particolare quando sono in esecuzione carichi di lavoro di produzione. La creazione di cluster non protetti, anche se possibile, consente a utenti anonimi di connettersi a un cluster che espone gli endpoint di gestione a Internet pubblico.

Tecnologie usate per implementare tali scenari. Gli [scenari di sicurezza del cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) sono:

-   Sicurezza da nodo a nodo: protegge la comunicazione tra le macchine virtuali e i computer nel cluster. Assicura che solo i computer autorizzati a connettersi al cluster possono partecipare all'hosting di applicazioni e servizi nel cluster.
I cluster eseguiti in Azure o i cluster autonomi eseguiti in Windows possono usare la [sicurezza basata su certificati](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) o la [sicurezza di Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-windows-security) per computer Windows Server.
-   Sicurezza da client a nodo: protegge la comunicazione tra un client Service Fabric e i singoli nodi nel cluster.
-   Controllo degli accessi in base al ruolo (RBAC): al momento della creazione del cluster si specificano i ruoli di amministrazione e di utente nel client specificando identità separate (certificati, AAD e così via) per ognuno.
-   Raccomandazioni per la sicurezza: per i cluster di Azure è consigliabile usare la sicurezza di AAD per autenticare i client e i certificati per la sicurezza da nodo a nodo.

Per configurare il cluster Windows autonomo, vedere [Impostazioni di configurazione per un cluster autonomo in Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Usare il modello di Azure Resource Manager e il modulo Azure PowerShell di Service Fabric per creare un cluster sicuro.
Le istruzioni per configurare un cluster sicuro di Azure Service Fabric in Azure tramite Azure Resource Manager sono disponibili [qui](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Usare il modello di Azure Resource Manager per personalizzare il cluster
-   Archiviazione gestita tramite il programma di installazione per i dischi rigidi virtuali delle macchine virtuali

Usare il modello di Azure Resource Manager per apportare modifiche al gruppo di risorse
-   Gestione facilitata della configurazione
-   Controllo

Gestire la configurazione del cluster come codice
-   Controllare approfonditamente le distribuzioni che si desidera distribuire
-   Evitare di usare comandi impliciti per modificare direttamente le risorse

È possibile automatizzare numerosi aspetti del [ciclo di vita dell'applicazione Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) . Il [modulo Azure PowerShell di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatizza le attività di uso più comune per la distribuzione, l'aggiornamento, la rimozione e il test delle applicazioni di Azure Service Fabric. Sono disponibili anche API gestite e HTTP per la gestione delle app.

## <a name="use-x509-certificates"></a>Usare certificati X.509
I cluster devono sempre essere protetti con certificati x.509 o la sicurezza di Windows. La sicurezza viene configurata solo in fase di creazione del cluster e non è possibile abilitare la sicurezza dopo la creazione del cluster.

Se si specifica un [certificato di cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), impostare il valore di ClusterCredentialType su X509. Se si specifica un certificato del server per le connessioni esterne, impostare il valore di ServerCredentialType su X509.

-   È consigliabile creare i certificati usati nei cluster che eseguono carichi di lavoro di produzione con un servizio certificati di Windows Server configurato correttamente oppure ottenerli da un'Autorità di certificazione (CA) approvata.
-   Non usare mai in fase di produzione certificati temporanei o di test creati con strumenti come MakeCert.exe.
-   È possibile usare un certificato autofirmato, ma lo si deve fare solo per i cluster di test e non nell'ambiente di produzione.

Se il cluster è senza protezione. Chiunque si può connettere in modo anonimo ed eseguire operazioni di gestione. È quindi necessario proteggere sempre i cluster di produzione usando certificati X.509 o la sicurezza di Windows.

Per altre informazioni su come abilitare i certificati nel cluster Service Fabric, vedere [Aggiungere o rimuovere certificati per un cluster Service Fabric in Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Configurare criteri di sicurezza
Service Fabric permette anche di proteggere le risorse usate dalle applicazioni in fase di distribuzione con l'account utente, ad esempio file, directory e certificati. In questo modo le applicazioni in esecuzione, anche in un ambiente ospitato condiviso, sono reciprocamente protette.

-   Usare un utente o un gruppo di dominio Active Directory: è possibile eseguire il servizio con le credenziali per un account utente o un gruppo di Active Directory. Si tratta di Active Directory locale nel dominio e non di Azure Active Directory (Azure AD). Usando un utente o un gruppo del dominio, sarà quindi possibile accedere ad altre risorse del dominio (ad esempio, condivisioni file) a cui sono state concesse le autorizzazioni.

-   Assegnare i criteri di accesso di sicurezza per gli endpoint HTTP e HTTPS: se si applicano criteri RunAs a un servizio e il manifesto del servizio dichiara le risorse dell'endpoint con il protocollo HTTP, è necessario specificare un elemento SecurityAccessPolicy per garantire che le porte allocate a questi endpoint siano correttamente inserite nell'elenco di controllo di accesso per l'account utente RunAs con cui viene eseguito il servizio. In caso contrario, http.sys non ha accesso al servizio e le chiamate del client hanno esito negativo.
Per altre informazioni su come abilitare i criteri di sicurezza in Service Fabric, vedere [Configurare i criteri di sicurezza per l'applicazione](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="reliable-actors-security-configuration"></a>Configurazione della sicurezza in Reliable Actors
Service Fabric Reliable Actors è un'implementazione dello schema progettuale dell'attore. Come per gli altri schemi progettuali del software, la scelta di usare un criterio specifico dipende dalla capacità di un problema di progettazione software di seguire lo schema.

In generale conviene prendere in considerazione il criterio dell'attore per modellare il problema o lo scenario nei casi seguenti:
-   Il problema riguarda un gran numero (migliaia o più) di piccole unità indipendenti e isolate di stato e logica.
-   Si desidera usare oggetti a thread singolo che non richiedono un'interazione significativa con componenti esterni, incluse query dello stato tra un insieme di attori.
-   Le istanze degli attori non bloccano i chiamanti con ritardi imprevedibili eseguendo operazioni I/O.

In Service Fabric gli attori sono implementati nel framework Reliable Actors: un framework di applicazioni imperniato sul criterio attore e basato su [Service Fabric Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Ogni servizio di tipo Reliable Actor che viene scritto è di fatto un servizio Reliable partizionato con stato.
Ogni attore è definito come un'istanza di un tipo di attore, come un oggetto .NET è un'istanza di un tipo .NET. Ad esempio, può essere definito un tipo di attore che implementa la funzionalità di una calcolatrice e possono esistere molti attori di quel tipo distribuiti su vari nodi di un cluster. Ciascuno di questi attori è identificato in modo univoco da un ID.

Le [configurazioni di sicurezza del replicatore](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) consentono di proteggere il canale di comunicazione usato durante la replica. Questo significa che i servizi non potranno visualizzare l'uno il traffico di replica dell'altro, garantendo la sicurezza dei dati a disponibilità elevata. Per impostazione predefinita, una sezione di configurazione della sicurezza vuota non abilita la sicurezza della replica.
Le configurazioni del replicatore riguardano il replicatore responsabile di garantire l'elevata affidabilità dello stato del provider di stato degli attori.

## <a name="configure-ssl-for-azure-service-fabric"></a>Configurare l'autenticazione SSL per Azure Service Fabric

Autenticazione del server: [autentica](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) gli endpoint di gestione del cluster in un client di gestione, in modo che il client di gestione sappia con certezza di comunicare con il cluster reale. Questo certificato fornisce anche un certificato [SSL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm) per l'API di gestione HTTPS e per Service Fabric Explorer tramite HTTPS.
È necessario ottenere un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per il cluster.

Per configurare SSL per un'applicazione, è necessario prima ottenere un certificato SSL che sia stato firmato da un'Autorità di certificazione (CA), ovvero un ente di terze parti attendibile che rilascia certificati per questo scopo. Se non se ne dispone già, è necessario ottenerne uno da un rivenditore di certificati SSL.

Il certificato deve soddisfare i requisiti seguenti per i certificati SSL in Azure:
-   Il certificato deve includere una chiave privata.
-   Il certificato deve essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange).
-   Il nome del soggetto del certificato deve corrispondere al dominio usato per accedere al servizio cloud. Non è possibile ottenere un certificato SSL da un'Autorità di certificazione (CA) per il dominio cloudapp.net. È necessario acquistare un nome di dominio personalizzato da utilizzare per accedere al servizio. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato utilizzato per accedere all'applicazione. Se ad esempio il nome di dominio personalizzato è contoso.com, occorre richiedere alla CA un certificato per **.contoso.com** o **www.contoso.com.**
-   Per il certificato deve essere usata una crittografia di almeno 2048 bit.

Il protocollo HTTP non è protetto ed è soggetto ad attacchi di intercettazione poiché i dati trasferiti dal Web browser al server Web o tra altri endpoint viene trasmesso in testo non crittografato. Ciò significa che gli utenti malintenzionati possono intercettare e visualizzare i dati sensibili, ad esempio i dati della carta di credito e gli account di accesso. Quando i dati vengono inviati o pubblicati tramite un browser che usa il protocollo HTTPS, l'autenticazione SSL assicura che tali informazioni siano crittografate e protette da intercettazione.

Per altre informazioni, vedere [Configurazione di SSL per un'applicazione in Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="network-isolationsecurity-with-azure-service-fabric"></a>Isolamento/sicurezza di rete con Azure Service Fabric
Usare il [modello di Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) come esempio per la configurazione di un cluster protetto a tre tipi di nodo e per controllare il traffico di rete in ingresso e in uscita tramite gruppi di sicurezza di rete.

Il modello dispone di un gruppo di sicurezza di rete per ogni set di scalabilità di macchine virtuali per controllare il traffico da e verso tali set. Per impostazione predefinita, le regole vengono configurate per consentire tutto il traffico richiesto dai servizi di sistema e dalle porte delle applicazioni specificate nel modello. Rivedere tali regole e modificarle in base alle proprie esigenze, inclusa l'aggiunta di nuove regole per le applicazioni in uso.

Per altre informazioni, vedere [Azure Service Fabric: Modelli di rete di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-a-key-vault-for-security"></a>Configurare un insieme di credenziali delle chiavi per la sicurezza
I certificati vengono usati in Service Fabric per fornire l'autenticazione e la crittografia e proteggere i vari aspetti di un cluster e delle sue applicazioni.

Service Fabric usa certificati X.509 per proteggere un cluster e fornire le funzionalità di sicurezza dell'applicazione. Si usa Key Vault per [gestire i certificati](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) dei cluster di Service Fabric in Azure. Quando viene distribuito un cluster in Azure, il provider di risorse di Azure responsabile della creazione di cluster Service Fabric estrae i certificati da Key Vault e li installa nelle macchine virtuali del cluster.

La relazione tra [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), un cluster di Service Fabric e il provider di risorse di Azure che usa i certificati archiviati in un insieme di credenziali delle chiavi durante la creazione di un cluster.

**Creare un gruppo di risorse** Il primo passaggio consiste nel creare un gruppo di risorse specifico per l'insieme di credenziali delle chiavi. È consigliabile inserire l'insieme di credenziali delle chiavi in un proprio gruppo di risorse. Questa azione consente di rimuovere i gruppi di risorse di calcolo e di archiviazione, incluso il gruppo di risorse contenente il cluster di Service Fabric, senza perdere le chiavi e i segreti. Il gruppo di risorse che contiene l'insieme di credenziali delle chiavi deve essere situato nella stessa area del cluster che lo usa.

**Creare un insieme di credenziali delle chiavi nel nuovo gruppo di risorse** L'insieme di credenziali delle chiavi deve essere abilitato per la distribuzione per consentire al provider di risorse di calcolo di ottenere i certificati e installarli nelle istanze delle macchine virtuali.
Per altre informazioni su come configurare l'insieme di credenziali delle chiavi di Azure, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-roles"></a>Assegnare utenti ai ruoli
Dopo aver creato le applicazioni per rappresentare il cluster, assegnare gli utenti ai ruoli supportati da Service Fabric: sola lettura e amministratore. È possibile assegnare i ruoli usando il portale di Azure classico.

>[!Note]
> Per altre informazioni sui ruoli in Service Fabric, vedere [Controllo degli accessi in base al ruolo per i client di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

Azure Service Fabric supporta due tipi di controllo di accesso diversi per i client che sono connessi a un [cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm): amministratore e utente. Il Controllo di accesso consente all'amministratore del cluster di limitare l'accesso a determinate operazioni del cluster per diversi gruppi di utenti, rendendo più sicuro il cluster.

## <a name="next-steps"></a>Passaggi successivi
- Configurazione dell'[ambiente di sviluppo di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started).
- Informazioni sulle [opzioni di supporto di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).


