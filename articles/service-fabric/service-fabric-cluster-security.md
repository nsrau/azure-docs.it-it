<properties
   pageTitle="Proteggere un cluster di Service Fabric | Microsoft Azure"
   description="Descrive gli scenari di sicurezza per un cluster Service Fabric e le diverse tecnologie usate per implementare questi scenari."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/01/2016"
   ms.author="chackdan"/>

# Scenari di sicurezza di un cluster di Service Fabric

Un cluster di Service Fabric è una risorsa di cui si è proprietari. Per impedire l'accesso non autorizzato alla risorsa, è necessario proteggerla, in particolare quando sono in esecuzione carichi di lavoro di produzione. Questo articolo descrive una panoramica degli scenari di sicurezza per i cluster in esecuzione in Azure e Windows Server e delle varie tecnologie usate per implementare questi scenari. Gli scenari di sicurezza del cluster sono:

- Sicurezza da nodo a nodo
- Sicurezza da client a nodo
- Controllo degli accessi in base al ruolo (RBAC)

## Sicurezza da nodo a nodo
Protegge la comunicazione tra le macchine virtuali e i computer del cluster. Assicura che solo i computer autorizzati a connettersi al cluster possono partecipare all'hosting di applicazioni e servizi nel cluster.

![Diagramma della comunicazione da nodo a nodo][Node-to-Node]

I cluster eseguiti in Azure o i cluster autonomi eseguiti in Windows possono usare la [sicurezza basata su certificati](https://msdn.microsoft.com/library/ff649801.aspx) o la [sicurezza di Windows](https://msdn.microsoft.com/library/ff649396.aspx). La sicurezza basata su certificati viene configurata durante la creazione del cluster (tramite il portale di Azure o tramite i modelli ARM) specificando un certificato primario e un certificato secondario facoltativo. I certificati primario e secondario specificati devono essere diversi dai certificati del client di amministrazione e del client di sola lettura specificati per la [sicurezza da client a nodo](#client-to-node-security). Per informazioni su come configurare la sicurezza basata su certificati in un cluster eseguito in Azure, vedere [Proteggere un cluster di Service Fabric in Azure con i certificati](service-fabric-secure-azure-cluster-with-certs.md) o [Impostare un cluster usando un modello ARM](service-fabric-cluster-creation-via-arm.md).

## Sicurezza da client a nodo
Autentica i client e protegge la comunicazione tra un client e i singoli nodi del cluster. Questo tipo di sicurezza autentica e protegge le comunicazioni client, garantendo che solo gli utenti autorizzati possano accedere al cluster e alle applicazioni distribuite nel cluster. I client vengono identificati in modo univoco con le credenziali di sicurezza di Windows o le credenziali di sicurezza del relativo certificato.

![Diagramma della comunicazione da client a nodo][Client-to-Node]

I cluster eseguiti in Azure o i cluster autonomi eseguiti in Windows possono usare la [sicurezza basata su certificati](https://msdn.microsoft.com/library/ff649801.aspx) o la [sicurezza di Windows](https://msdn.microsoft.com/library/ff649396.aspx). La sicurezza basata su certificati da client a nodo viene configurata durante la creazione del cluster (tramite il portale di Azure o tramite i modelli ARM) specificando un certificato del client di amministrazione e/o un certificato del client di sola lettura. I certificati del client di amministrazione e del client di sola lettura specificati devono essere diversi dai certificati primario e secondario specificati per la [sicurezza da nodo a nodo](#node-to-node-security). I client che si connettono al cluster tramite il certificato di amministrazione o il certificato primario hanno l'accesso completo alle funzionalità di gestione. I client che si connettono al cluster tramite il certificato di sola lettura hanno l'accesso di sola lettura alle funzionalità di gestione. Per informazioni su come configurare la sicurezza basata su certificati in un cluster eseguito in Azure, vedere [Proteggere un cluster di Service Fabric in Azure con i certificati](service-fabric-secure-azure-cluster-with-certs.md) o [Impostare un cluster usando un modello ARM](service-fabric-cluster-creation-via-arm.md).

Service Fabric usa i certificati server X.509 specificati durante le configurazioni del tipo di nodo quando si crea un cluster. Alla fine di questo articolo viene fornita una rapida panoramica di questi certificati e di come è possibile acquisirli o crearli.

I cluster eseguiti in Azure possono inoltre proteggere l'accesso agli endpoint di gestione usando Azure Active Directory (AAD). Per informazioni su come creare i necessari elementi AAD, su come popolarli durante la creazione dei cluster e su come connettersi a tali cluster in seguito, vedere [Creare un cluster di Service Fabric usando Azure Active Directory per l'autenticazione client](service-fabric-cluster-security-client-auth-with-aad.md).

## Controllo degli accessi in base al ruolo (RBAC)
Il Controllo di accesso consente all'amministratore del cluster di limitare l'accesso a determinate operazioni del cluster per diversi gruppi di utenti, rendendo più sicuro il cluster. Per i client che si connettono a un cluster, sono supportati due tipi di controllo di accesso diversi: amministratore e utente. Gli amministratori hanno accesso completo alle funzionalità di gestione, incluse funzionalità di lettura/scrittura. Gli utenti, per impostazione predefinita, hanno solo l'accesso in lettura alle funzionalità di gestione, ad esempio funzionalità di query, e la possibilità di risolvere applicazioni e servizi. I ruoli client amministratore e utente vengono specificati al momento della creazione del cluster fornendo certificati separati per ognuno di essi. Per altre informazioni sulle impostazioni predefinite del controllo di accesso e su come modificarle, vedere l'articolo dedicato al [controllo di accesso basato sui ruoli per i client](service-fabric-cluster-security-roles.md).


## Certificati X.509 e Service Fabric
I certificati digitali X509 vengono comunemente usati per autenticare client e server e per crittografare e firmare digitalmente i messaggi. Per altri dettagli su questi certificati, vedere la pagina relativa all'[uso dei certificati](http://msdn.microsoft.com/library/ms731899.aspx).

Alcuni elementi importanti da considerare:

- È consigliabile creare i certificati usati nei cluster che eseguono carichi di lavoro di produzione con un servizio certificati di Windows Server configurato correttamente oppure ottenerli da un'[Autorità di certificazione (CA)](https://en.wikipedia.org/wiki/Certificate_authority) approvata.
- Non usare mai in fase di produzione certificati temporanei o di test creati con strumenti come MakeCert.exe.
- È possibile usare un certificato autofirmato, ma lo si deve fare solo per i cluster di test e non nell'ambiente di produzione.

### Certificati server X.509

L'attività principale dei certificati del server è l'autenticazione di un server (nodo) nei client o di un server (nodo) in un server (nodo). Uno dei primi controlli eseguiti quando un client o un nodo autentica un nodo consiste nel controllare il valore del nome comune nel campo Oggetto. Questo nome comune o uno dei nomi alternativi del soggetto dei certificati deve essere presente nell'elenco di nomi comuni consentiti.

L'articolo seguente descrive come generare certificati con nomi alternativi del soggetto: [Come aggiungere un nome alternativo soggetto a un certificato LDAP sicuro](http://support.microsoft.com/kb/931351).

Il campo del soggetto può contenere diversi valori, tutti preceduti da un'inizializzazione per indicare il valore. L'inizializzazione è quasi sempre "CN" per il nome comune, ad esempio, "CN = www.contoso.com". Il campo del soggetto può anche essere vuoto. Se il campo facoltativo Nome alternativo soggetto è popolato, deve contenere sia il nome comune del certificato sia una voce per ogni nome alternativo del soggetto. Queste voci vengono immesse come valori di nomi DNS.

Il valore del campo Scopi designati del certificato deve includere un valore appropriato, ad esempio "Autenticazione server" o "Autenticazione client".

### Certificati client X.509

I certificati client in genere non vengono rilasciati da un'autorità di certificazione di terze parti. In genere l'archivio personale della località utente corrente contiene invece certificati client inseriti da un'autorità radice, con lo scopo designato "Autenticazione client". Il client può usare tali certificati quando è necessaria l'autenticazione reciproca.

>[AZURE.NOTE] Tutte le operazioni di gestione in un cluster di Service Fabric richiedono certificati server. I certificati client non possono essere usati per la gestione.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## Passaggi successivi
Dopo aver configurato il cluster, per informazioni su come aggiornarlo, vedere:

- [Processo di aggiornamento del cluster di infrastruttura di servizi e operazioni](service-fabric-cluster-upgrade.md)

Ulteriori informazioni sulla sicurezza:

- [Sicurezza delle applicazioni e RunAs](service-fabric-application-runas-security.md)

- [Proteggere le comunicazioni dei servizi](service-fabric-reliable-services-secure-communication.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=AcomDC_0608_2016-->