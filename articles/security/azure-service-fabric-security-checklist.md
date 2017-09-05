---
title: Elenco di controllo per la sicurezza di Azure Service Fabric | Microsoft Docs
description: Questo articolo include un set di elenchi di controllo per la sicurezza di Azure Service Fabric.
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
ms.openlocfilehash: d0441f1e96e94352d4112ec387058b47074d8b0b
ms.contentlocale: it-it
ms.lasthandoff: 08/09/2017

---
# <a name="azure-service-fabric-security-checklist"></a>Elenco di controllo per la sicurezza di Azure Service Fabric
Questo articolo include un elenco di controllo di facile utilizzo che aiuta a proteggere l'ambiente Azure Service Fabric.

## <a name="introduction"></a>Introduzione
Azure Service Fabric è una piattaforma di sistemi distribuiti che semplifica la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili. Service Fabric fa fronte anche alle principali problematiche correlate allo sviluppo e alla gestione delle applicazioni cloud. Gli sviluppatori e gli amministratori non devono più occuparsi di risolvere complessi problemi di infrastruttura e possono concentrarsi sull'implementazione di carichi di lavoro cruciali e impegnativi, con la certezza di assicurare scalabilità, affidabilità e gestibilità.

## <a name="checklist"></a>Elenco di controllo
Usare l'elenco seguente per assicurarsi di non trascurare eventuali problemi importanti nella gestione e nella configurazione di una soluzione di Azure Service Fabric protetta.


|Categoria dell'elenco di controllo| Descrizione |
| ------------ | -------- |
|[Controllo degli accessi in base al ruolo (RBAC)](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security-roles) | <ul><li>Il Controllo di accesso consente all'amministratore del cluster di limitare l'accesso a determinate operazioni del cluster per diversi gruppi di utenti, rendendo più sicuro il cluster.</li><li>Gli amministratori hanno accesso completo alle funzionalità di gestione, incluse funzionalità di lettura/scrittura. </li><li>   Gli utenti, per impostazione predefinita, hanno solo l'accesso in lettura alle funzionalità di gestione, ad esempio funzionalità di query, e la possibilità di risolvere applicazioni e servizi.</li></ul>|
|[Certificati X.509 e Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security) | <ul><li>È consigliabile creare i [certificati](https://docs.microsoft.com/en-us/dotnet/framework/wcf/feature-details/working-with-certificates) usati nei cluster che eseguono carichi di lavoro di produzione con un servizio certificati di Windows Server configurato correttamente oppure ottenerli da un'[autorità di certificazione (CA)](https://en.wikipedia.org/wiki/Certificate_authority) approvata.</li><li>Non usare mai in fase di produzione [certificati temporanei o di test](https://docs.microsoft.com/en-us/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) creati con strumenti come [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>È possibile usare un [certificato autofirmato](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-x509-security), ma lo si deve fare solo per i cluster di test e non nell'ambiente di produzione.</li></ul>|
|[Sicurezza del cluster](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security) | <ul><li>Gli scenari di sicurezza del cluster includono la sicurezza da nodo a nodo e da client a nodo e il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security-roles).</li></ul>|
|[Autenticazione del cluster](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Autentica la [comunicazione da nodo a nodo](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) per la federazione di cluster. </li></ul>|
|[Autenticazione del server](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Autentica gli [endpoint di gestione del cluster](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-portal) rispetto a un client di gestione.</li></ul>|
|[Sicurezza delle applicazioni](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm)| <ul><li>Crittografia e decrittografia dei valori di configurazione dell'applicazione.</li><li> Crittografia dei dati tra i nodi durante la replica.</li></ul>|
|[Certificato del cluster](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-x509-security) | <ul><li>Questo certificato è necessario per proteggere la comunicazione tra i nodi di un cluster.</li><li>  Impostare l'identificazione personale del certificato primario nella sezione Identificazione personale e quella del certificato secondario nelle variabili ThumbprintSecondary.</li></ul>|
|[ServerCertificate](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-x509-security)| <ul><li>Questo certificato viene presentato al client quando tenta di connettersi al cluster. È possibile usare due diversi certificati del server, uno primario e uno secondario per l'aggiornamento.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Si tratta di un set di certificati che da installare nei client autenticati. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Impostare il nome comune del primo certificato client per CertificateCommonName. CertificateIssuerThumbprint è l'identificazione personale dell'autorità emittente del certificato. </li></ul>|
|ReverseProxyCertificate| <ul><li>Si tratta di un certificato facoltativo che è possibile specificare se si vuole proteggere il [proxy inverso](https://docs.microsoft.com/en-in/azure/service-fabric/service-fabric-reverseproxy). </li></ul>|
|Insieme di credenziali delle chiavi| <ul><li>Serve a gestire i certificati dei cluster di Service Fabric in Azure.  </li></ul>|


## <a name="next-steps"></a>Passaggi successivi
- [Processo di aggiornamento del cluster di infrastruttura di servizi e operazioni eseguibile dall'utente](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-upgrade)
- [Gestione delle applicazioni di Infrastruttura di servizi in Visual Studio](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-manage-application-in-visual-studio).
- [Introduzione al monitoraggio dell'integrità di Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-health-introduction).

