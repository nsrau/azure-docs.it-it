---
title: Proteggere un cluster di Azure Service Fabric | Documentazione Microsoft
description: "Informazioni sugli scenari di sicurezza per un cluster di Azure Service Fabric e le varie tecnologie che è possibile usare per la relativa implementazione."
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
ms.date: 06/28/2017
ms.author: chackdan
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: e75929ee5d3f57af77c66910cc294a7c0fb6629a
ms.contentlocale: it-it
ms.lasthandoff: 09/06/2017

---
# <a name="service-fabric-cluster-security-scenarios"></a>Scenari di sicurezza di un cluster di Service Fabric
Un cluster di Azure Service Fabric è una risorsa di cui si è proprietari. È necessario proteggere i cluster per evitare che utenti non autorizzati si connettano a essi. Un cluster sicuro è particolarmente importante quando si eseguono carichi di lavoro nel cluster. La creazione di cluster non protetti, anche se possibile, potrebbe consentire a utenti anonimi di connettersi a un cluster che espone gli endpoint di gestione a Internet pubblico. 

In questo articolo viene fornita una panoramica degli scenari di sicurezza per i cluster di Azure e i cluster autonomi e le varie tecnologie che è possibile usare per la relativa implementazione:

* Sicurezza da nodo a nodo
* Sicurezza da client a nodo
* Controllo degli accessi in base al ruolo

## <a name="node-to-node-security"></a>Sicurezza da nodo a nodo
La sicurezza da nodo a nodo aiuta a proteggere la comunicazione tra le macchine virtuali o i computer del cluster. Questo scenario di sicurezza assicura che solo i computer autorizzati a connettersi al cluster possano partecipare all'hosting di applicazioni e servizi nel cluster.

![Diagramma della comunicazione da nodo a nodo][Node-to-Node]

I cluster eseguiti in Azure e i cluster autonomi eseguiti in Windows possono usare entrambi la [sicurezza basata su certificati](https://msdn.microsoft.com/library/ff649801.aspx) o la [sicurezza di Windows](https://msdn.microsoft.com/library/ff649396.aspx) per computer Windows Server.

### <a name="node-to-node-certificate-security"></a>Sicurezza basata su certificati da nodo a nodo
Service Fabric usa i certificati server X.509 specificati durante le configurazioni del tipo di nodo quando si crea un cluster. Alla fine di questo articolo viene fornita una rapida panoramica di questi certificati e di come è possibile acquisirli o crearli.

Impostare la sicurezza basata su certificati durante la creazione del cluster tramite il portale di Azure usando un modello di Azure Resource Manager o un modello JSON autonomo. È possibile impostare un certificato primario e un certificato secondario facoltativo che viene usato per i rollover dei certificati. I certificati primario e secondario impostcati devono essere diversi dai certificati client di amministrazione e dai certificati client di sola lettura impostati per la [sicurezza da client a nodo](#client-to-node-security).

Per informazioni su come impostare la sicurezza basata su certificati in un cluster per Azure, vedere [Configurare un cluster di Service Fabric usando un modello di Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) .

Per informazioni su come impostare la sicurezza basata su certificati in un cluster di Windows Server autonomo, vedere [proteggere un cluster autonomo in Windows mediante certificati X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Sicurezza di Windows da nodo a nodo
Per informazioni su come impostare la sicurezza Windows in un cluster di Windows Server autonomo, vedere [proteggere un cluster autonomo in Windows mediante la sicurezza Windows](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Sicurezza da client a nodo
La sicurezza da client a nodo autentica i client e aiuta a proteggere la comunicazione tra un client e i singoli nodi del cluster. Questo tipo di sicurezza aiuta a garantire che solo gli utenti autorizzati possano accedere al cluster e alle applicazioni distribuite nel cluster. I client vengono identificati in modo univoco con le credenziali di sicurezza di Windows o le credenziali di sicurezza del relativo certificato.

![Diagramma della comunicazione da client a nodo][Client-to-Node]

I cluster eseguiti in Azure e i cluster autonomi eseguiti in Windows possono usare la [sicurezza basata su certificati](https://msdn.microsoft.com/library/ff649801.aspx) o la [sicurezza di Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Sicurezza basata su certificati da client a nodo
Impostare la sicurezza basata su certificati da client a nodo durante la creazione del cluster tramite il portale di Azure usando un modello di Resource Manager o un modello JSON autonomo. Per creare il certificato, specificare un certificato client di amministrazione o un certificato client utente. Come procedura consigliata, i certificati client di amministrazione e i certificati client utente specificati devono essere diversi dai certificati primario e secondario specificati per la [sicurezza da nodo a nodo](#node-to-node-security). Per impostazione predefinita, i certificati cluster per la sicurezza da nodo a nodo vengono aggiunti all'elenco di certificati Amministratore client consentiti.

I client che si connettono al cluster con il certificato di amministrazione hanno accesso completo alle funzionalità di gestione. I client che si connettono al cluster con il certificato client utente di sola lettura hanno solo l'accesso in lettura alle funzionalità di gestione. Questi certificati vengono usati per il controllo degli accessi in base al ruolo descritto più avanti in questo articolo.

Per informazioni su come impostare la sicurezza basata su certificati in un cluster per Azure, vedere [Configurare un cluster di Service Fabric usando un modello di Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) .

Per informazioni su come impostare la sicurezza basata su certificati in un cluster di Windows Server autonomo, vedere [proteggere un cluster autonomo in Windows mediante certificati X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Sicurezza di Azure Active Directory da client a nodo in Azure
Per i cluster eseguiti in Azure è anche possibile proteggere l'accesso agli endpoint di gestione usando Azure Active Directory (Azure AD). Per informazioni su come creare i necessari elementi di AAD, su come popolarli durante la creazione dei cluster e su come connettersi a tali cluster in seguito, vedere [Configurare un cluster di Service Fabric usando un modello di Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="security-recommendations"></a>Suggerimenti per la sicurezza
Per i cluster di Azure si consiglia di usare la sicurezza di Azure AD per l'autenticazione dei client e dei certificati per la sicurezza da nodo a nodo.

Per i cluster di Windows Server autonomi, se sono presenti Windows Server 2012 R2 e Active Directory è consigliabile usare la sicurezza di Windows con account gestiti di gruppo. In caso contrario, usare comunque la sicurezza di Windows con account di Windows.

## <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo
È possibile usare il controllo di accesso per limitare l'accesso a determinate operazioni di cluster per diversi gruppi di utenti. In questo modo il cluster è più sicuro. Per i client che si connettono a un cluster, sono supportati due tipi di controllo di accesso diversi: il ruolo di amministratore e il ruolo utente.

Gli utenti assegnati al ruolo di Amministratore hanno accesso completo alle funzionalità di gestione, incluse funzionalità di lettura/scrittura. Gli utenti assegnati al ruolo di Utente, per impostazione predefinita, hanno solo l'accesso in lettura alle funzionalità di gestione, ad esempio funzionalità di query, e la possibilità di risolvere applicazioni e servizi.

Impostare i ruoli del client di Amministratore e Utente quando si crea il cluster. Assegnare i ruoli fornendo identità separate (ad esempio, tramite certificati o Azure AD) per ogni tipo di ruolo. Per altre informazioni sulle impostazioni predefinite del controllo di accesso e su come modificarle, vedere [Controllo di accesso basato sui ruoli per i client di Service Fabric](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certificati X.509 e Service Fabric
I certificati digitali X.509 vengono comunemente usati per autenticare client e server, oltre a essere usati per crittografare e firmare digitalmente i messaggi. Per altre informazioni sui certificati digitali X.509, vedere [Uso dei certificati](http://msdn.microsoft.com/library/ms731899.aspx).

Alcuni elementi importanti da considerare:

* Per creare i certificati usati nei cluster che eseguono carichi di lavoro di produzione, usare un servizio certificati di Windows Server configurato correttamente oppure ottenerli da un'[Autorità di certificazione (CA)](https://en.wikipedia.org/wiki/Certificate_authority) approvata.
* Non usare mai certificatii temporanei o di test creati mediante strumenti come MakeCert.exe in un ambiente di produzione.
* È possibile usare un certificato autofirmato, ma solo in un cluster di test. Non usare un certificato autofirmato nell'ambiente di produzione.

### <a name="server-x509-certificates"></a>Certificati server X.509
L'attività principale dei certificati del server è l'autenticazione di un server (nodo) nei client o di un server (nodo) in un server (nodo). Uno dei primi controlli eseguiti quando un client o un nodo autentica un nodo consiste nel controllare il valore del nome comune nel campo **Oggetto**. Questo nome comune o uno dei nomi alternativi del soggetto (SAN) dei certificati deve essere presente nell'elenco di nomi comuni consentiti.

Per informazioni su come generare certificati che dispongano di SAN, vedere [Come aggiungere un nome alternativo del soggetto a un certificato LDAP sicuro](http://support.microsoft.com/kb/931351).

Il campo **Soggetto** può avere più valori. Ogni valore è preceduto da un'inizializzazione per indicare il tipo di valore. L'inizializzazione è di norma **CN** per il *nome comune*, ad esempio, **CN = www.contoso.com**. Il campo **Soggetto** può essere vuoto. Se il campo facoltativo **Nome alternativo soggetto** è popolato, deve contenere sia il nome comune del certificato sia una voce per ogni nome alternativo del soggetto. Queste voci vengono immesse come valori di **nomi DNS**.

Il valore del campo **Scopi designati** del certificato deve includere un valore appropriato, ad esempio **Autenticazione server** o **Autenticazione client**.

### <a name="client-x509-certificates"></a>Certificati client X.509
I certificati client in genere non vengono rilasciati da un'autorità di certificazione di terze parti. In genere l'archivio personale della località utente corrente contiene invece certificati client inseriti da un'autorità radice, con lo **scopo designato** **Autenticazione client**. Il client può usare tali certificati quando è necessaria l'autenticazione reciproca.

> [!NOTE]
> Tutte le operazioni di gestione in un cluster di Service Fabric richiedono certificati server. I certificati client non possono essere usati per la gestione.

## <a name="next-steps"></a>Passaggi successivi
* [Creare un cluster in Azure con un modello di Resource Manager](service-fabric-cluster-creation-via-arm.md) 
* [Creare un cluster usando il portale di Azure](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

