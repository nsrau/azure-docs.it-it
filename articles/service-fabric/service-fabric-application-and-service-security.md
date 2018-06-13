---
title: Informazioni sulla sicurezza dell'applicazione Azure Service Fabric | Microsoft Docs
description: Panoramica su come eseguire in sicurezza applicazioni di microservizi in Service Fabric. Informazioni su come eseguire servizi e script di avvio con account di sicurezza diversi, autenticare e autorizzare utenti, gestire i segreti delle applicazioni, proteggere comunicazioni di servizio, usare un gateway API e proteggere i dati inattivi delle applicazioni.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: ryanwi
ms.openlocfilehash: fa6d46186ad833b68e60c24f742d210b7845759a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207911"
---
# <a name="service-fabric-application-and-service-security"></a>Sicurezza del servizio e dell'applicazione Service Fabric
Un'architettura di microservizi può apportare [numerosi vantaggi](service-fabric-overview-microservices.md). Gestire la sicurezza dei microservizi, tuttavia, richiede procedure complesse, diverse da quelle necessarie per gestire la sicurezza delle tradizionali applicazioni monolitiche. 

Con una struttura monolitica, l'applicazione viene normalmente eseguita su uno o più server all'interno di una rete ed è più semplice identificare le porte esposte e le API e gli indirizzi IP. Nella maggior parte dei casi, inoltre, è presente un perimetro o un limite e un database da proteggere. Se il sistema viene compromesso a seguito di un attacco o di una violazione della sicurezza, quindi, è probabile che tutti gli elementi all'interno del sistema risultino a disposizione dell'autore dell'attacco. Con i microservizi, il sistema è più complesso.  I servizi vengono decentralizzati e distribuiti su molti host e migrano dall'uno all'altro.  Con strumenti di sicurezza appropriati, è possibile limitare i privilegi di cui può usufruire il responsabile dell'attacco e la quantità di dati disponibili con un singolo attacco perpetrato tramite la violazione di un servizio.  Le comunicazioni, inoltre, non avvengono internamente ma in rete e sono presenti numerose porte esposte e interazioni tra servizi. Conoscere queste interazioni e sapere quando si verificano è di fondamentale importanza per la sicurezza dell'applicazione.

Questo articolo non è una guida alla sicurezza dei microservizi, poiché online sono disponibili molte risorse di questo tipo, ma descrive come possono essere applicati in Service Fabric vari aspetti relativi alla sicurezza.

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione
È spesso necessario che le risorse e le API esposte da un servizio vengano limitate a determinati client o utenti attendibili. L'autenticazione è il processo con cui si accerta in modo affidabile l'identità di un utente,  mentre l'autorizzazione è il processo che rende le API o i servizi disponibili per alcuni utenti autenticati, ma non per altri.

### <a name="authentication"></a>Authentication
Il primo passaggio del processo decisionale relativo all'attendibilità a livello di API è l'autenticazione, ovvero il processo con cui si accerta in modo affidabile l'identità di un utente.  In scenari di microservizi, l'autenticazione viene in genere gestita centralmente. Se si usa un gateway API, tuttavia, è possibile [scaricare l'autenticazione](/azure/architecture/patterns/gateway-offloading) sul gateway. Se si usa questo approccio, accertarsi che i singoli servizi non possano essere raggiunti direttamente (senza il gateway API), a meno che non siano stati configurati strumenti di sicurezza aggiuntivi per l'autenticazione dei messaggi, indipendentemente dal fatto che provengano o meno dal gateway.

Se è possibile accedere direttamente ai servizi, per autenticare gli utenti è possibile usare un servizio di autenticazione come Azure Active Directory o un microservizio di autenticazione dedicato che svolge la funzione di servizio token di sicurezza. Le decisioni sull'attendibilità vengono condivise tra i servizi tramite cookie o token di sicurezza. 

Per ASP.NET Core, il principale meccanismo di [autenticazione degli utenti](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) è il sistema di appartenenze ASP.NET Core Identity, che archivia le informazioni sugli utenti (inclusi dati di accesso, ruoli e attestazioni) in un archivio dati configurato dallo sviluppatore. ASP.NET Core Identity supporta l'autenticazione a due fattori,  ma anche provider di autenticazione esterni, in modo che gli utenti possano accedere tramite processi di autenticazione esistenti forniti da provider come Microsoft, Google, Facebook o Twitter. 

### <a name="authorization"></a>Authorization
Dopo l'autenticazione, i servizi devono autorizzare l'accesso utente o determinare cosa può fare un utente. Questo processo, ad esempio, consente a un servizio di rendere le API disponibili per alcuni utenti autenticati, ma non per tutti. L'autorizzazione è ortogonale e indipendente dall'autenticazione, che costituisce invece il processo con cui si accerta l'identità di un utente. L'autenticazione, inoltre, può creare una o più identità per l'utente corrente.

L'[autorizzazione di ASP.NET Core](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) può essere eseguita in base ai ruoli degli utenti o a criteri personalizzati come il controllo delle attestazioni o altre regole euristiche.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Limitare e proteggere l'accesso con un gateway API
Le applicazioni cloud necessitano in genere di un gateway front-end per garantire un singolo punto di ingresso per utenti, dispositivi o altre applicazioni. Un [gateway API](/azure/architecture/microservices/gateway) si trova tra i client e i servizi e costituisce il punto di ingresso per tutti i servizi forniti dall'applicazione. e funge da proxy inverso, indirizzando le richieste dai client ai servizi. Può anche eseguire varie attività trasversali come l'autenticazione e l'autorizzazione, la terminazione SSL e la limitazione della frequenza. Se non si distribuisce un gateway, i client devono inviare le richieste direttamente ai servizi front-end.

In Service Fabric un gateway può essere qualsiasi servizio senza stato, ad esempio un'[applicazione ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), o un altro servizio progettato per l'ingresso del traffico, ad esempio [Træfik](https://docs.traefik.io/), [Hub eventi](https://docs.microsoft.com/azure/event-hubs/), [Hub IoT](https://docs.microsoft.com/azure/iot-hub/) o [Gestione API di Azure](https://docs.microsoft.com/azure/api-management).

Gestione API si integra direttamente in Service Fabric, consentendo di pubblicare API con un ampio set di regole di routing nei servizi Service Fabric back-end.  Consente inoltre di proteggere l'accesso ai servizi back-end, impedire attacchi DoS con la limitazione o verificare chiavi API, token JWT, certificati e altre credenziali. Per altre informazioni, vedere [Panoramica di Service Fabric con Gestione API di Azure](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Gestire i segreti dell'applicazione
I segreti possono essere informazioni riservate, ad esempio le stringhe di connessione di archiviazione, le password o altri valori che non devono essere gestiti in testo normale. Questo articolo usa Azure Key Vault per gestire chiavi e segreti. Tuttavia, l' *uso* di segreti in un'applicazione è indipendente dalla piattaforma cloud per consentire alle applicazioni di essere distribuite in un cluster ospitato in un punto qualsiasi.

Il metodo consigliato per gestire le impostazioni di configurazione del servizio è tramite i [pacchetti di configurazione del servizio][config-package]. I pacchetti di configurazione dispongono di controllo delle versioni e sono aggiornabili tramite gli aggiornamenti in sequenza gestiti con convalida dell'integrità e rollback automatico. Questo approccio è da preferire alla configurazione globale in quanto riduce le probabilità di un'interruzione del servizio globale. I segreti crittografati non rappresentano un'eccezione. Service Fabric offre funzionalità incorporate per crittografare e decrittografare i valori in un file Settings.xml del pacchetto configurazione tramite la crittografia del certificato.

Il diagramma seguente illustra il flusso di base per la gestione dei segreti in un'applicazione di Service Fabric:

![panoramica della gestione dei segreti][overview]

In questo flusso sono presenti quattro passaggi principali:

1. Ottenere un certificato di crittografia dei dati.
2. Installare il certificato nel cluster.
3. Crittografare i valori dei segreti quando si distribuisce un'applicazione con il certificato e inserirli nel file di configurazione Settings.xml del servizio.
4. Leggere i valori crittografati risultati da Settings. XML eseguendo la decrittografia con lo stesso certificato di crittografia. 

[Azure Key Vault][key-vault-get-started] viene usato come percorso di archiviazione sicuro per i certificati e come un modo per ottenere i certificati installati nei cluster Service Fabric in Azure. Se non si esegue la distribuzione in Azure, non è necessario usare l'insieme di credenziali delle chiavi per gestire i segreti nelle applicazioni di Service Fabric.

Per un esempio, vedere [Gestire i segreti dell'applicazione](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Proteggere l'ambiente di hosting
Azure Service Fabric consente di proteggere le applicazioni in esecuzione nel cluster con account utente diversi. Service Fabric permette anche di proteggere le risorse usate dalle applicazioni in fase di distribuzione con l'account utente, ad esempio file, directory e certificati. In questo modo le applicazioni in esecuzione, anche in un ambiente ospitato condiviso, sono reciprocamente protette.

Il manifesto dell'applicazione dichiara le entità di sicurezza (utenti e gruppi) necessarie per eseguire i servizi e proteggere le risorse.  Alle entità di sicurezza viene fatto riferimento nei criteri, ad esempio nei criteri RunAs, di binding degli endpoint, di condivisione dei pacchetti e di accesso di sicurezza.  I criteri vengono quindi applicati alle risorse del servizio nella sezione **ServiceManifestImport** del manifesto dell'applicazione.

Quando si dichiarano le entità, è possibile anche definire e creare gruppi di utenti per aggiungere uno o più utenti a ogni gruppo e gestirli insieme. Questo aspetto è utile quando sono presenti più utenti per punti di ingresso del servizio differenti che devono avere determinati privilegi comuni disponibili a livello di gruppo.

Per impostazione predefinita, le applicazioni di Service Fabric vengono eseguite con lo stesso account con cui viene eseguito il processo Fabric.exe. Service Fabric consente anche di eseguire le applicazioni con un account utente locale o un account di sistema locale, specificato nel manifesto dell'applicazione. Per altre informazioni, vedere [Run a service as a local user account or local system account](service-fabric-application-runas-security.md) (Eseguire un servizio come account utente locale o account di sistema locale)  o [Eseguire uno script di avvio del servizio come account utente o di sistema locale](service-fabric-run-script-at-service-startup.md).

Quando si esegue Service Fabric su un cluster Windows autonomo, è possibile eseguire un servizio con [account di dominio di Active Directory](service-fabric-run-service-as-ad-user-or-group.md) o [account del servizio gestito del gruppo](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Proteggere i contenitori
Service Fabric fornisce un meccanismo per i servizi all'interno di un contenitore per accedere a un certificato che viene installato nei nodi in un cluster di Windows o Linux (versione 5.7 o versioni successive). Questo certificato PFX può essere usato per autenticare l'applicazione o il servizio o per proteggere la comunicazione con altri servizi. Per altre informazioni, vedere [Import a certificate into a container](service-fabric-securing-containers.md) (Importare un certificato in un contenitore).

Service Fabric supporta anche gMSA (account del servizio gestito del gruppo) per i contenitori di Windows. Per altre informazioni, vedere [Configurare gMSA per i contenitori di Windows](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Proteggere le comunicazioni dei servizi
In Service Fabric un servizio viene eseguito in una posizione nel cluster di Service Fabric, in genere distribuito su più macchine virtuali. Service Fabric offre varie opzioni per la protezione delle comunicazioni di servizio.

È possibile abilitare endpoint HTTPS nei servizi Web [ASP.NET Core o Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).

È possibile anche stabilire una connessione protetta tra il proxy inverso e i servizi, abilitando un canale protetto end-to-end. La connessione ai servizi protetti è supportata solo quando il proxy inverso è configurato per l'ascolto su HTTPS. Per informazioni sulla configurazione del proxy inverso, vedere [Proxy inverso in Azure Service Fabric](service-fabric-reverseproxy.md).  L'articolo [Connettersi a un servizio protetto](service-fabric-reverseproxy-configure-secure-communication.md) descrive come stabilire una connessione sicura tra il proxy inverso e i servizi.

Il framework di applicazioni di Reliable Services offre alcuni stack e strumenti predefiniti che è possibile usare per migliorare la sicurezza. Sono disponibili alcuni articoli che illustrano come migliorare la sicurezza quando si usa la comunicazione remota per un servizio (in [C#](service-fabric-reliable-services-secure-communication.md) o [Java](service-fabric-reliable-services-secure-communication-java.md)) o [Windows Communication Foundation](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Crittografare i dati inattivi delle applicazioni
Ogni [tipo di nodo](service-fabric-cluster-nodetypes.md) in un cluster di Service Fabric in esecuzione in Azure è supportato da un [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Usando un modello di Azure Resource Manager, è possibile collegare dischi dati ai set di scalabilità che costituiscono il cluster di Service Fabric.  Se i servizi salvano i dati su un disco dati collegato, è possibile proteggere i dati dell'applicazione [crittografando i dischi dati](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md).

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
* [Run a setup script at service startup](service-fabric-run-script-at-service-startup.md) (Eseguire uno script di configurazione all'avvio del servizio)
* [Specificare le risorse in un manifesto del servizio](service-fabric-service-manifest-resources.md)
* [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)
* [Informazioni sulla sicurezza del cluster](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png