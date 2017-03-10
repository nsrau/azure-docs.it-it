---
title: Proteggere un cluster di Service Fabric | Documentazione Microsoft
description: Descrive gli scenari di sicurezza per un cluster Service Fabric e le diverse tecnologie usate per implementare questi scenari.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 628df1df8f5de99a5c18d0df5b7ee41e2fb747df
ms.openlocfilehash: c3ff370b105a1f9bdacd1bdb4b32d6209e150be2
ms.lasthandoff: 12/08/2016


---
# <a name="service-fabric-cluster-security-scenarios"></a>Scenari di sicurezza di un cluster di Service Fabric
Un cluster di Service Fabric è una risorsa di cui si è proprietari. Per impedire a utenti non autorizzati di connettersi ai cluster, è necessario proteggerli, in particolare quando sono in esecuzione carichi di lavoro di produzione. La creazione di cluster non protetti, anche se possibile, consente a utenti anonimi di connettersi a un cluster che espone gli endpoint di gestione a Internet pubblico. 

Questo articolo offre una panoramica degli scenari di sicurezza per i cluster autonomi o in esecuzione in Azure e delle varie tecnologie usate per implementare tali scenari. Gli scenari di sicurezza del cluster sono:

* Sicurezza da nodo a nodo
* Sicurezza da client a nodo
* Controllo degli accessi in base al ruolo

## <a name="node-to-node-security"></a>Sicurezza da nodo a nodo
Protegge la comunicazione tra le VM o i computer del cluster. Assicura che solo i computer autorizzati a connettersi al cluster possono partecipare all'hosting di applicazioni e servizi nel cluster.

![Diagramma della comunicazione da nodo a nodo][Node-to-Node]

I cluster eseguiti in Azure o i cluster autonomi eseguiti in Windows possono usare la [sicurezza basata su certificati](https://msdn.microsoft.com/library/ff649801.aspx) o la [sicurezza di Windows](https://msdn.microsoft.com/library/ff649396.aspx) per computer Windows Server.

### <a name="node-to-node-certificate-security"></a>Sicurezza basata su certificati da nodo a nodo
Service Fabric usa i certificati server X.509 specificati durante le configurazioni del tipo di nodo quando si crea un cluster. Alla fine di questo articolo viene fornita una rapida panoramica di questi certificati e di come è possibile acquisirli o crearli.

La sicurezza basata su certificati viene configurata durante la creazione del cluster tramite il portale di Azure, i modelli di Azure Resource Manager o un modello JSON autonomo. È possibile specificare un certificato primario e un certificato secondario facoltativo che viene usato per i rollover dei certificati. I certificati primario e secondario specificati devono essere diversi dai certificati client di amministrazione e dai certificati client di sola lettura specificati per la [sicurezza da client a nodo](#client-to-node-security).

Per informazioni su come configurare la sicurezza basata su certificati in un cluster per Azure, vedere [Configurare un cluster di Service Fabric usando un modello di Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) .

Per cluster Windows Server autonomi, vedere [Proteggere un cluster autonomo in Windows con certificati X.509 ](service-fabric-windows-cluster-x509-security.md)

### <a name="node-to-node-windows-security"></a>Sicurezza di Windows da nodo a nodo
Per cluster Windows Server autonomi, vedere [Proteggere un cluster autonomo in Windows tramite la funzionalità di sicurezza di Windows](service-fabric-windows-cluster-windows-security.md)

## <a name="client-to-node-security"></a>Sicurezza da client a nodo
Autentica i client e protegge la comunicazione tra un client e i singoli nodi del cluster. Questo tipo di sicurezza autentica e protegge le comunicazioni client, garantendo che solo gli utenti autorizzati possano accedere al cluster e alle applicazioni distribuite nel cluster. I client vengono identificati in modo univoco con le credenziali di sicurezza di Windows o le credenziali di sicurezza del relativo certificato.

![Diagramma della comunicazione da client a nodo][Client-to-Node]

I cluster eseguiti in Azure o i cluster autonomi eseguiti in Windows possono usare la [sicurezza basata su certificati](https://msdn.microsoft.com/library/ff649801.aspx) o la [sicurezza di Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Sicurezza basata su certificati da client a nodo
 La sicurezza basata su certificati da client a nodo viene configurata durante la creazione del cluster tramite il portale di Azure, modelli di Azure Resource Manager o un modello JSON autonomo specificando un certificato client di amministrazione e/o un certificato client di sola lettura.  I certificati client di amministrazione e i certificati client utente specificati devono essere diversi dai certificati primario e secondario specificati per la [sicurezza da nodo a nodo](#node-to-node-security).

I client che si connettono al cluster con il certificato di amministrazione hanno accesso completo alle funzionalità di gestione.  I client che si connettono al cluster con il certificato client utente di sola lettura hanno solo l'accesso in lettura alle funzionalità di gestione. In altri termini, questi certificati vengono usati per il controllo degli accessi in base al ruolo descritto di seguito in questo articolo.

Per informazioni su come configurare la sicurezza basata su certificati in un cluster per Azure, vedere [Configurare un cluster di Service Fabric usando un modello di Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) .

Per cluster Windows Server autonomi, vedere [Proteggere un cluster autonomo in Windows con certificati X.509 ](service-fabric-windows-cluster-x509-security.md)

### <a name="client-to-node-azure-active-directory-aad-security-on-azure"></a>Sicurezza di Azure Active Directory (AAD) da client a nodo in Azure
I cluster eseguiti in Azure possono inoltre proteggere l'accesso agli endpoint di gestione usando Azure Active Directory (AAD). Per informazioni su come creare i necessari elementi di AAD, su come popolarli durante la creazione dei cluster e su come connettersi a tali cluster in seguito, vedere [Configurare un cluster di Service Fabric usando un modello di Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) .

## <a name="security-recommendations"></a>Raccomandazioni sulla sicurezza
Per i cluster di Azure è consigliabile usare la sicurezza di AAD per l'autenticazione dei client e dei certificati per la sicurezza da nodo a nodo.

È consigliabile usare la sicurezza di Windows con account gestiti di gruppo Per i cluster Windows Server autonomi, se sono presenti Windows Server 2012 R2 e Active Directory. In caso contrario, usare comunque la sicurezza di Windows con account di Windows.

## <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo (RBAC)
Il Controllo di accesso consente all'amministratore del cluster di limitare l'accesso a determinate operazioni del cluster per diversi gruppi di utenti, rendendo più sicuro il cluster. Per i client che si connettono a un cluster, sono supportati due tipi di controllo di accesso diversi: il ruolo di amministratore e il ruolo utente.

Gli amministratori hanno accesso completo alle funzionalità di gestione, incluse funzionalità di lettura/scrittura. Gli utenti, per impostazione predefinita, hanno solo l'accesso in lettura alle funzionalità di gestione, ad esempio funzionalità di query, e la possibilità di risolvere applicazioni e servizi.

I ruoli client amministratore e utente vengono specificati al momento della creazione del cluster fornendo per ognuno identità separate, ad esempio certificati, AAD e così via. Per altre informazioni sulle impostazioni predefinite del controllo di accesso e su come modificarle, vedere [Controllo di accesso basato sui ruoli per i client di Service Fabric](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certificati X.509 e Service Fabric
I certificati digitali X509 vengono comunemente usati per autenticare client e server e per crittografare e firmare digitalmente i messaggi. Per altri dettagli su questi certificati, vedere [Utilizzo dei certificati](http://msdn.microsoft.com/library/ms731899.aspx).

Alcuni elementi importanti da considerare:

* È consigliabile creare i certificati usati nei cluster che eseguono carichi di lavoro di produzione con un servizio certificati di Windows Server configurato correttamente oppure ottenerli da un' [autorità di certificazione (CA)](https://en.wikipedia.org/wiki/Certificate_authority)approvata.
* Non usare mai in fase di produzione certificati temporanei o di test creati con strumenti come MakeCert.exe.
* È possibile usare un certificato autofirmato, ma lo si deve fare solo per i cluster di test e non nell'ambiente di produzione.

### <a name="server-x509-certificates"></a>Certificati server X.509
L'attività principale dei certificati del server è l'autenticazione di un server (nodo) nei client o di un server (nodo) in un server (nodo). Uno dei primi controlli eseguiti quando un client o un nodo autentica un nodo consiste nel controllare il valore del nome comune nel campo Oggetto. Questo nome comune o uno dei nomi alternativi del soggetto dei certificati deve essere presente nell'elenco di nomi comuni consentiti.

L'articolo seguente descrive come generare certificati con nomi alternativi del soggetto: [Come aggiungere un nome alternativo del soggetto a un certificato LDAP sicuro](http://support.microsoft.com/kb/931351).

Il campo del soggetto può contenere diversi valori, tutti preceduti da un'inizializzazione per indicare il valore. L'inizializzazione è quasi sempre "CN" per il nome comune, ad esempio, "CN = www.contoso.com". Il campo del soggetto può anche essere vuoto. Se il campo facoltativo Nome alternativo soggetto è popolato, deve contenere sia il nome comune del certificato sia una voce per ogni nome alternativo del soggetto. Queste voci vengono immesse come valori di nomi DNS.

Il valore del campo Scopi designati del certificato deve includere un valore appropriato, ad esempio "Autenticazione server" o "Autenticazione client".

### <a name="client-x509-certificates"></a>Certificati client X.509
I certificati client in genere non vengono rilasciati da un'autorità di certificazione di terze parti. In genere l'archivio personale della località utente corrente contiene invece certificati client inseriti da un'autorità radice, con lo scopo designato "Autenticazione client". Il client può usare tali certificati quando è necessaria l'autenticazione reciproca.

> [!NOTE]
> Tutte le operazioni di gestione in un cluster di Service Fabric richiedono certificati server. I certificati client non possono essere usati per la gestione.
> 
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## <a name="next-steps"></a>Passaggi successivi
Questo articolo contiene informazioni di carattere generale sulla protezione del cluster. Successivamente,


1.  [creare un cluster in Azure con un modello di Resource Manager](service-fabric-cluster-creation-via-arm.md) 
2.  [portale di Azure](service-fabric-cluster-creation-via-portal.md).

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

