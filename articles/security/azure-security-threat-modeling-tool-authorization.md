---
title: 'Autorizzazione: Microsoft Threat Modeling Tool - Azure | Microsoft Docs'
description: Procedure di mitigazione delle minacce esposte in Threat Modeling Tool
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: rodsan
translationtype: Human Translation
ms.sourcegitcommit: 8251f44200c11d3efcec04b7ac99857232b2f9ed
ms.openlocfilehash: 9c5483e73874b0f030f7fd99cad4430b70e378a8
ms.lasthandoff: 02/15/2017


---

# <a name="security-frame-authorization--mitigations"></a>Infrastruttura di sicurezza: autorizzazione - Procedure di mitigazione 
| Prodotto o servizio | Articolo |
| --------------- | ------- |
| Limite di trust dei computer | <ul><li>[Verificare che gli ACL appropriati siano configurati per limitare l'accesso non autorizzato ai dati nel dispositivo](#acl-restricted-access)</li><li>[Verificare che il contenuto sensibile dell'applicazione specifico dell'utente venga archiviato nella directory del profilo utente](#sensitive-directory)</li><li>[Verificare che le applicazioni distribuite vengano eseguite con privilegi minimi](#deployed-privileges)</li></ul> |
| Applicazione Web. | <ul><li>[Applicare l'ordine dei passaggi in sequenza durante l'elaborazione di flussi di logica di business](#sequential-logic)</li><li>[Implementare un meccanismo di limitazione della frequenza per impedire l'enumerazione](#rate-enumeration)</li><li>[Verificare che sia presente l'autorizzazione necessaria e che venga seguito il principio dei privilegi minimi](#principle-least-privilege)</li><li>[Le decisioni riguardanti la logica di business e l'autorizzazione di accesso alle risorse non devono basarsi sui parametri di richiesta in ingresso](#logic-request-parameters)</li><li>[Verificare che il contenuto e le risorse non siano enumerabili o accessibili tramite browsing forzato](#enumerable-browsing)</li></ul> |
| Database | <ul><li>[Assicurarsi che vengano usati account con privilegi minimi per connettersi al server di database](#privileged-server)</li><li>[Implementare la sicurezza a livello di riga per impedire ai tenant di accedere ai dati degli altri tenant](#rls-tenants)</li><li>[Il ruolo sysadmin deve avere solo utenti necessari validi](#sysadmin-users)</li></ul> |
| Gateway IoT cloud | <ul><li>[Connettersi al gateway nel cloud usando token con privilegi minimi](#cloud-least-privileged)</li></ul> |
| Hub eventi di Azure | <ul><li>[Usare una chiave di firma di accesso condiviso per autorizzazioni di solo invio per la generazione di token di dispositivo](#sendonly-sas)</li><li>[Non usare token di accesso che consentono l'accesso diretto all'hub eventi](#access-tokens-hub)</li><li>[Connettersi all'hub eventi tramite chiavi di firma di accesso condiviso aventi solo le autorizzazioni minime necessarie](#sas-minimum-permissions)</li></ul> |
| Azure DocumentDB | <ul><li>[Usare i token delle risorse per la connessione a DocumentDB, quando possibile](#resource-docdb)</li></ul> |
| Limite di trust di Azure | <ul><li>[Abilitare la gestione degli accessi con granularità fine per la sottoscrizione di Azure usando il controllo degli accessi in base al ruolo](#grained-rbac)</li></ul> |
| Limite di trust di Service Fabric | <ul><li>[Limitare l'accesso del client alle operazioni di cluster con il controllo degli accessi in base al ruolo](#cluster-rbac)</li></ul> |
| Dynamics CRM | <ul><li>[Eseguire la modellazione di sicurezza e usare la sicurezza a livello di campo quando richiesto](#modeling-field)</li></ul> |
| Portale di Dynamics CRM | <ul><li>[Eseguire la modellazione di sicurezza degli account del portale tenendo presente che il modello di sicurezza per il portale è diverso dagli altri componenti di CRM](#portal-security)</li></ul> |
| Archiviazione di Azure | <ul><li>[Concedere l'autorizzazione con granularità fine in un intervallo di entità nell'archiviazione tabelle di Azure](#permission-entities)</li><li>[Abilitare il controllo degli accessi in base al ruolo per l'account di archiviazione di Azure usando Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| Client per dispositivi mobili | <ul><li>[Implementare il rilevamento implicito di jailbreak o rooting](#rooting-detection)</li></ul> |
| WCF | <ul><li>[Riferimento debole alla classe in WCF](#weak-class-wcf)</li><li>[WCF: implementare il controllo di autorizzazione](#wcf-authz)</li></ul> |
| API Web | <ul><li>[Implementare il meccanismo di autorizzazione appropriato nell'API Web ASP.NET](#authz-aspnet)</li></ul> |
| Dispositivo IoT | <ul><li>[Eseguire i controlli di autorizzazione nel dispositivo se il dispositivo supporta azioni che richiedono diversi livelli di autorizzazione](#device-permission)</li></ul> |
| Gateway IoT sul campo | <ul><li>[Eseguire i controlli di autorizzazione nel gateway sul campo se il gateway supporta azioni che richiedono diversi livelli di autorizzazione](#field-permission)</li></ul> |

## <a name="a-idacl-restricted-accessaensure-that-proper-acls-are-configured-to-restrict-unauthorized-access-to-data-on-the-device"></a><a id="acl-restricted-access"></a>Verificare che gli ACL appropriati siano configurati per limitare l'accesso non autorizzato ai dati nel dispositivo

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Limite di trust dei computer | 
| Fase SDL               | Distribuzione |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Verificare che gli ACL appropriati siano configurati per limitare l'accesso non autorizzato ai dati nel dispositivo|

## <a name="a-idsensitive-directoryaensure-that-sensitive-user-specific-application-content-is-stored-in-user-profile-directory"></a><a id="sensitive-directory"></a>Verificare che il contenuto sensibile dell'applicazione specifico dell'utente venga archiviato nella directory del profilo utente

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Limite di trust dei computer | 
| Fase SDL               | Distribuzione |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Verificare che il contenuto sensibile dell'applicazione specifico dell'utente venga archiviato nella directory del profilo utente. Si impedisce così che un utente del computer possa accedere ai dati degli altri utenti.|

## <a name="a-iddeployed-privilegesaensure-that-the-deployed-applications-are-run-with-least-privileges"></a><a id="deployed-privileges"></a>Verificare che le applicazioni distribuite vengano eseguite con privilegi minimi

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Limite di trust dei computer | 
| Fase SDL               | Distribuzione |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Verificare che l'applicazione distribuita venga eseguita con privilegi minimi. |

## <a name="a-idsequential-logicaenforce-sequential-step-order-when-processing-business-logic-flows"></a><a id="sequential-logic"></a>Applicare l'ordine dei passaggi in sequenza durante l'elaborazione di flussi di logica di business

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Applicazione Web. | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Per verificare che questa fase sia stata eseguita da un utente reale si vuole far sì che l'applicazione elabori i flussi della logica di business solo nell'ordine dei passaggi in sequenza, con tutti i passaggi elaborati in tempi umanamente realistici e senza elaborazioni in un ordine diverso, passaggi omessi, passaggi elaborati da un altro utente o transazioni inviate troppo rapidamente.|

## <a name="a-idrate-enumerationaimplement-rate-limiting-mechanism-to-prevent-enumeration"></a><a id="rate-enumeration"></a>Implementare un meccanismo di limitazione della frequenza per impedire l'enumerazione

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Applicazione Web. | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Assicurarsi che gli identificatori sensibili siano casuali. Implementare il controllo CAPTCHA nelle pagine anonime. Verificare che errori ed eccezioni non rivelino dati specifici|

## <a name="a-idprinciple-least-privilegeaensure-that-proper-authorization-is-in-place-and-principle-of-least-privileges-is-followed"></a><a id="principle-least-privilege"></a>Verificare che sia presente l'autorizzazione necessaria e che venga seguito il principio dei privilegi minimi

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Applicazione Web. | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | <p>Con questo principio, all'account utente vengono assegnati solo i privilegi essenziali per il lavoro. Un utente del backup non deve ad esempio installare software, di conseguenza sarà autorizzato solo a eseguire backup e applicazioni correlate al backup. Gli altri privilegi, ad esempio l'installazione di nuovo software, saranno bloccati. Il principio è applicabile anche a un utente di PC che in genere svolge il lavoro in un normale account utente e apre un account con privilegi protetto da password (ovvero un utente con privilegi avanzati) solo quando la situazione lo rende assolutamente necessario. </p><p>Questo principio è applicabile anche alle applicazioni Web. Invece di dipendere esclusivamente da metodi di autenticazione basati sul ruolo che usano le sessioni, si preferisce assegnare privilegi agli utenti tramite un sistema di autenticazione basato su database. Si continuano a usare le sessioni per determinare se l'utente ha effettuato l'accesso correttamente, solo che ora invece di assegnare all'utente un ruolo specifico gli vengono assegnati privilegi per verificare le azioni che è autorizzato a eseguire nel sistema. Un grande vantaggio di questo metodo risiede nel fatto che quando a un utente devono essere assegnati meno privilegi, le modifiche verranno applicate immediatamente perché l'assegnazione non dipende dalla sessione che altrimenti dovrebbe prima scadere.</p>|

## <a name="a-idlogic-request-parametersabusiness-logic-and-resource-access-authorization-decisions-should-not-be-based-on-incoming-request-parameters"></a><a id="logic-request-parameters"></a>Le decisioni riguardanti la logica di business e l'autorizzazione di accesso alle risorse non devono basarsi sui parametri di richiesta in ingresso

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Applicazione Web. | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Quando si verifica se un utente è limitato alla visualizzazione di dati specifici, le limitazioni di accesso devono essere elaborate sul lato server. L'ID utente deve essere archiviato all'interno di una variabile di sessione all'accesso e deve essere usato per recuperare i dati utente dal database |

### <a name="example"></a>Esempio
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Ora un utente malintenzionato non può manomettere e modificare il funzionamento dell'applicazione perché l'identificatore per il recupero dei dati viene gestito sul lato server.

## <a name="a-idenumerable-browsingaensure-that-content-and-resources-are-not-enumerable-or-accessible-via-forceful-browsing"></a><a id="enumerable-browsing"></a>Verificare che il contenuto e le risorse non siano enumerabili o accessibili tramite browsing forzato

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Applicazione Web. | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | <p>I file statici e di configurazione sensibili non devono essere conservati nella directory webroot. Per il contenuto che non deve essere pubblico, applicare i controlli di accesso appropriati oppure rimuovere il contenuto stesso.</p><p>Il browsing forzato si associa di solito a tecniche di forza bruta per raccogliere informazioni tentando di accedere al maggior numero di URL possibile per enumerare directory e file in un server. Gli utenti malintenzionati possono cercare tutte le varianti dei file comunemente esistenti. La ricerca di file di password include ad esempio file come psswd.txt, password.htm, password.dat e altre varianti.</p><p>Per attenuare questo problema è necessario includere funzionalità di rilevamento di tentativi di attacchi di forza bruta.</p>|

## <a name="a-idprivileged-serveraensure-that-least-privileged-accounts-are-used-to-connect-to-database-server"></a><a id="privileged-server"></a>Assicurarsi che vengano usati account con privilegi minimi per connettersi al server di database

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Database | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | [Gerarchia delle autorizzazioni del database SQL](https://msdn.microsoft.com/library/ms191465), [Entità a protezione diretta del database SQL](https://msdn.microsoft.com/library/ms190401) |
| Passi | È necessario usare account con privilegi minimi per connettersi al database. L'account di accesso dell'applicazione deve essere limitato nel database ed eseguire solo stored procedure specifiche. L'account di accesso dell'applicazione non deve avere accesso diretto alle tabelle. |

## <a name="a-idrls-tenantsaimplement-row-level-security-rls-to-prevent-tenants-from-accessing-each-others-data"></a><a id="rls-tenants"></a>Implementare la sicurezza a livello di riga per impedire ai tenant di accedere ai dati degli altri tenant

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Database | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | SQL Azure, locale |
| Attributi              | Versione SQL:&12;, versione SQL: MSSQL2016 |
| Riferimenti              | [Sicurezza a livello di riga di SQL Server](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| Passi | <p>La sicurezza a livello di riga consente ai clienti di controllare l'accesso alle righe in una tabella di database in base alle caratteristiche dell'utente che esegue una query, ad esempio l'appartenenza a un gruppo o il contesto di esecuzione.</p><p>La sicurezza a livello di riga semplifica la progettazione e la codifica della sicurezza nell'applicazione. Consente di implementare limitazioni per l'accesso alle righe di dati, assicurando ad esempio che i collaboratori possano accedere solo alle righe di dati pertinenti per il proprio reparto o limitando l'accesso ai dati di un cliente ai soli dati di interesse per l'azienda.</p><p>La logica di restrizione dell'accesso si trova nel livello database e non lontano dai dati in un altro livello applicazione. Il sistema di database applica le restrizioni di accesso a ogni tentativo di accesso ai dati da qualsiasi livello. Il sistema di sicurezza è così più affidabile e solido, grazie alla riduzione della superficie del sistema di sicurezza.</p><p>|

Si noti che la sicurezza a livello di riga come funzionalità di database predefinita è applicabile solo a SQL Server a partire dalla versione 2016 e al database SQL di Azure. Se non viene implementata la funzionalità predefinita della sicurezza a livello di riga, assicurarsi l'accesso ai dati sia limitato tramite viste e procedure

## <a name="a-idsysadmin-usersasysadmin-role-should-only-have-valid-necessary-users"></a><a id="sysadmin-users"></a>Il ruolo sysadmin deve avere solo utenti necessari validi

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Database | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | [Gerarchia delle autorizzazioni del database SQL](https://msdn.microsoft.com/library/ms191465), [Entità a protezione diretta del database SQL](https://msdn.microsoft.com/library/ms190401) |
| Passi | I membri del ruolo predefinito SysAdmin del server devono essere molto limitati e non contenere mai account usati dalle applicazioni.  Esaminare l'elenco di utenti nel ruolo e rimuovere gli account non necessari|

## <a name="a-idcloud-least-privilegedaconnect-to-cloud-gateway-using-least-privileged-tokens"></a><a id="cloud-least-privileged"></a>Connettersi al gateway nel cloud usando token con privilegi minimi

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Gateway IoT cloud | 
| Fase SDL               | Distribuzione |  
| Tecnologie applicabili | Generico |
| Attributi              | Opzione gateway: Hub IoT di Azure |
| Riferimenti              | [Controllo di accesso dell'hub IoT](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| Passi | Assegnare autorizzazioni con privilegi minimi a vari componenti che si connettono al gateway cloud (hub IoT). Esempio tipico: il componente di gestione/provisioning di dispositivi usa registryread/write, l'elaboratore eventi (ASA) usa Connessione servizio. I singoli dispositivi si connettono usando le credenziali dispositivo|

## <a name="a-idsendonly-sasause-a-send-only-permissions-sas-key-for-generating-device-tokens"></a><a id="sendonly-sas"></a>Usare una chiave di firma di accesso condiviso per autorizzazioni di solo invio per la generazione di token di dispositivo

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Hub eventi di Azure | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | [Panoramica del modello di sicurezza e autenticazione di Hub eventi](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| Passi | Viene usata una chiave di firma di accesso condiviso per generare i singoli token di dispositivo. Usare una chiave di firma di accesso condiviso per autorizzazioni di solo invio per la generazione di token di dispositivo per un determinato server di pubblicazione|

## <a name="a-idaccess-tokens-hubado-not-use-access-tokens-that-provide-direct-access-to-the-event-hub"></a><a id="access-tokens-hub"></a>Non usare token di accesso che consentono l'accesso diretto all'hub eventi

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Hub eventi di Azure | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | [Panoramica del modello di sicurezza e autenticazione di Hub eventi](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| Passi | Non assegnare al dispositivo un token che concede l'accesso diretto all'hub eventi. L'uso di un token con privilegi minimi per il dispositivo che fornisce l'accesso solo a un server di pubblicazione consente di identificare e aggiungere il dispositivo alla blacklist se ritenuto non autorizzato o compromesso.|

## <a name="a-idsas-minimum-permissionsaconnect-to-event-hub-using-sas-keys-that-have-the-minimum-permissions-required"></a><a id="sas-minimum-permissions"></a>Connettersi all'hub eventi tramite chiavi di firma di accesso condiviso aventi solo le autorizzazioni minime necessarie

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Hub eventi di Azure | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | [Panoramica del modello di sicurezza e autenticazione di Hub eventi](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| Passi | Assegnare autorizzazioni con privilegi minimi a varie applicazioni back-end che si connettono all'hub eventi. Generare chiavi di firma di accesso condiviso separate per ogni applicazione back-end e assegnare solo le autorizzazioni necessarie, ovvero invio, ricezione o gestione.|

## <a name="a-idresource-docdbause-resource-tokens-to-connect-to-documentdb-whenever-possible"></a><a id="resource-docdb"></a>Usare i token delle risorse per la connessione a DocumentDB, quando possibile

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Azure DocumentDB | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | un token di una risorsa è associato a una risorsa autorizzazione di DocumentDB e acquisisce la relazione tra l'utente di un database e l'autorizzazione dell'utente per una risorsa applicazione di DocumentDB specifica (ad esempio, raccolta, documento). Usare sempre un token delle risorse per accedere a DocumentDB se il client non è attendibile con la gestione delle chiavi master o di sola lettura, ad esempio un'applicazione per l'utente finale come un client desktop o per dispositivi mobili. Usare la chiave master o le chiavi di sola lettura di applicazioni back-end che possano archiviare queste chiavi in modo sicuro.|

## <a name="a-idgrained-rbacaenable-fine-grained-access-management-to-azure-subscription-using-rbac"></a><a id="grained-rbac"></a>Abilitare la gestione degli accessi con granularità fine per la sottoscrizione di Azure usando il controllo degli accessi in base al ruolo

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Limite di trust di Azure | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| Passi | Il Controllo degli accessi in base al ruolo di Azure (RBAC) consente la gestione specifica degli accessi per Azure. L'uso del Controllo degli accessi in base al ruolo permette di concedere agli utenti solo il livello di accesso necessario per lavorare.|

## <a name="a-idcluster-rbacarestrict-clients-access-to-cluster-operations-using-rbac"></a><a id="cluster-rbac"></a>Limitare l'accesso del client alle operazioni di cluster con il controllo degli accessi in base al ruolo

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Limite di trust di Service Fabric | 
| Fase SDL               | Distribuzione |  
| Tecnologie applicabili | Generico |
| Attributi              | Ambiente: Azure |
| Riferimenti              | [Controllo degli accessi in base al ruolo per i client di Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| Passi | <p>Azure Service Fabric supporta due tipi di controllo di accesso diversi per i client connessi a un cluster di Service Fabric: amministratore e utente. Il Controllo di accesso consente all'amministratore del cluster di limitare l'accesso a determinate operazioni del cluster per diversi gruppi di utenti, rendendo più sicuro il cluster.</p><p>Gli amministratori hanno accesso completo alle funzionalità di gestione, incluse funzionalità di lettura/scrittura. Gli utenti, per impostazione predefinita, hanno solo l'accesso in lettura alle funzionalità di gestione, ad esempio funzionalità di query, e la possibilità di risolvere applicazioni e servizi.</p><p>I due ruoli di client, amministratore o client, vengono specificati al momento della creazione del cluster fornendo certificati separati per ognuno di essi.</p>|

## <a name="a-idmodeling-fieldaperform-security-modeling-and-use-field-level-security-where-required"></a><a id="modeling-field"></a>Eseguire la modellazione di sicurezza e usare la sicurezza a livello di campo quando richiesto

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Dynamics CRM | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Eseguire la modellazione di sicurezza e usare la sicurezza a livello di campo quando richiesto|

## <a name="a-idportal-securityaperform-security-modeling-of-portal-accounts-keeping-in-mind-that-the-security-model-for-the-portal-differs-from-the-rest-of-crm"></a><a id="portal-security"></a>Eseguire la modellazione di sicurezza degli account del portale tenendo presente che il modello di sicurezza per il portale è diverso dagli altri componenti di CRM

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Portale di Dynamics CRM | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Eseguire la modellazione di sicurezza degli account del portale tenendo presente che il modello di sicurezza per il portale è diverso dagli altri componenti di CRM|

## <a name="a-idpermission-entitiesagrant-fine-grained-permission-on-a-range-of-entities-in-azure-table-storage"></a><a id="permission-entities"></a>Concedere l'autorizzazione con granularità fine in un intervallo di entità nell'archiviazione tabelle di Azure

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Archiviazione di Azure | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | Tipo di archiviazione: tabella |
| Riferimenti              | [Come delegare l'accesso agli oggetti nell'account di archiviazione di Azure con la firma di accesso condiviso](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| Passi | In alcuni scenari di business, l'archiviazione tabelle di Azure potrebbe essere necessaria per archiviare dati sensibili per diverse parti, ad esempio, dati sensibili relativi a paesi diversi. In questi casi, le firme di accesso condiviso possono essere create specificando gli intervalli di chiavi di partizione e di riga, in modo che un utente possa accedere a dati specifici per un determinato paese.| 

## <a name="a-idrbac-azure-manageraenable-role-based-access-control-rbac-to-azure-storage-account-using-azure-resource-manager"></a><a id="rbac-azure-manager"></a>Abilitare il controllo degli accessi in base al ruolo per l'account di archiviazione di Azure usando Azure Resource Manager

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Archiviazione di Azure | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | [Come proteggere l'account di archiviazione con il controllo degli accessi in base al ruolo](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| Passi | <p>Quando si crea un nuovo account di archiviazione, si seleziona un modello di distribuzione classica o di Azure Resource Manager. Il modello di distribuzione classica per la creazione di risorse in Azure consente solo l'accesso di tipo "tutto o niente" alla sottoscrizione e, di conseguenza, all'account di archiviazione.</p><p>Con il modello di Azure Resource Manager si inserisce l'account di archiviazione in un gruppo di risorse e si controlla l'accesso al piano di gestione di quello specifico account di archiviazione tramite Azure Active Directory. Ad esempio, è possibile concedere a utenti specifici la possibilità di accedere alle chiavi dell'account di archiviazione, mentre altri utenti possono visualizzare le informazioni sull'account di archiviazione, ma non accedere alle relative chiavi.</p>|

## <a name="a-idrooting-detectionaimplement-implicit-jailbreak-or-rooting-detection"></a><a id="rooting-detection"></a>Implementare il rilevamento implicito di jailbreak o rooting

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Client per dispositivi mobili | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | <p>L'applicazione deve proteggere i propri dati di configurazione e dell'utente in caso di jailbreak o rooting del telefono. Il rooting/jailbreak implica un accesso non autorizzato che gli utenti normali non eseguono sui propri telefoni. L'applicazione deve quindi avere una logica di rilevamento implicito all'avvio, per rilevare se è stato eseguito il rooting del telefono.</p><p>La logica di rilevamento può prevedere semplicemente l'accesso a file cui normalmente solo gli utenti ROOT possono accedere, ad esempio:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>Se può accedere a questi file, l'applicazione è in esecuzione come utente ROOT.</p>|

## <a name="a-idweak-class-wcfaweak-class-reference-in-wcf"></a><a id="weak-class-wcf"></a>Riferimento debole alla classe in WCF

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | WCF | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico, .NET Framework 3 |
| Attributi              | N/D  |
| Riferimenti              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/vulncat/index.html) |
| Passi | <p>Il sistema usa un riferimento debole alla classe che potrebbe consentire a un utente malintenzionato di eseguire codice non autorizzato. Il programma fa riferimento a una classe definita dall'utente che non è identificata in modo univoco. Quando .NET carica questa classe con identificazione debole, il caricatore di tipo CLR cerca la classe nei percorsi seguenti nell'ordine specificato:</p><ol><li>Se l'assembly del tipo è noto, il caricatore esegue una ricerca nei percorsi di reindirizzamento del file di configurazione, in GAC, nell'assembly corrente usando le informazioni di configurazione e nella directory base dell'applicazione</li><li>Se l'assembly è sconosciuto, il caricatore esegue una ricerca nell'assembly corrente, in mscorlib e nel percorso restituito dal gestore eventi TypeResolve</li><li>Questo ordine di ricerca CLR può essere modificato con hook quali il meccanismo di inoltro dei tipi e l'evento AppDomain.TypeResolve</li></ol><p>Se un utente malintenzionato sfrutta l'ordine di ricerca CLR creando una classe alternativa con lo stesso nome e inserendola in un percorso alternativo che CLR caricherà per primo, il CLR eseguirà involontariamente il codice specificato dall'utente malintenzionato</p>|

### <a name="example"></a>Esempio
L'elemento `<behaviorExtensions/>` del file di configurazione WCF seguente indica a WCF di aggiungere una classe di comportamento personalizzata a una particolare estensione WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
L'uso di nomi completi (sicuri) identifica un tipo in modo univoco e aumenta la sicurezza del sistema. Usare nomi di assembly completi durante la registrazione di tipi nei file machine.config e app.config.

### <a name="example"></a>Esempio
L'elemento `<behaviorExtensions/>` del file di configurazione WCF seguente indica a WCF di aggiungere una classe di comportamento personalizzata con riferimento sicuro a una particolare estensione WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a name="a-idwcf-authzawcf-implement-authorization-control"></a><a id="wcf-authz"></a>WCF: implementare il controllo di autorizzazione

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | WCF | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico, .NET Framework 3 |
| Attributi              | N/D  |
| Riferimenti              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/vulncat/index.html) |
| Passi | <p>Questo servizio non usa un controllo di autorizzazione. Quando un client chiama un servizio WCF specifico, WCF mette a disposizione vari schemi di autorizzazione per verificare che il chiamante sia autorizzato a eseguire il metodo del servizio nel server. Se i controlli di autorizzazione non sono abilitati per i servizi WCF, un utente autenticato può ottenere l'escalation dei privilegi.</p>|

### <a name="example"></a>Esempio
La configurazione seguente indica a WCF di non verificare il livello di autorizzazione del client quando si esegue il servizio:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Usare uno schema di autorizzazione del servizio per verificare che il chiamante del metodo del servizio sia autorizzato a eseguire l'operazione. WCF offre due modalità e consente la definizione di uno schema di autorizzazione personalizzato. La modalità UseWindowsGroups usa i ruoli e gli utenti di Windows, mentre la modalità UseAspNetRoles usa un provider di ruoli ASP.NET, ad esempio SQL Server, per l'autenticazione.

### <a name="example"></a>Esempio
La configurazione seguente indica a WCF di verificare che il client faccia parte del gruppo di amministratori prima di eseguire il servizio di aggiunta:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Il servizio viene quindi dichiarato come indicato di seguito:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a name="a-idauthz-aspnetaimplement-proper-authorization-mechanism-in-aspnet-web-api"></a><a id="authz-aspnet"></a>Implementare il meccanismo di autorizzazione appropriato nell'API Web ASP.NET

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | API Web | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico, MVC&5; |
| Attributi              | N/D, provider di identità: AD FS, provider di identità: Azure AD |
| Riferimenti              | [Autenticazione e autorizzazione nell'API Web ASP.NET](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| Passi | <p>Le informazioni sui ruoli per gli utenti dell'applicazione possono essere ottenute da attestazioni di Azure AD o AD FS se l'applicazione si basa su di essi come provider di identità. In alternativa, le informazioni possono essere fornite dall'applicazione stessa. In questi casi, l'implementazione di autorizzazione personalizzata deve convalidare le informazioni sui ruoli utente.</p><p>Le informazioni sui ruoli per gli utenti dell'applicazione possono essere ottenute da attestazioni di Azure AD o AD FS se l'applicazione si basa su di essi come provider di identità. In alternativa, le informazioni possono essere fornite dall'applicazione stessa. In questi casi, l'implementazione di autorizzazione personalizzata deve convalidare le informazioni sui ruoli utente.</p>

### <a name="example"></a>Esempio
```C#
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
Tutti i controller e i metodi di azione da proteggere devono essere decorati con l'attributo indicato in precedenza.
```C#
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a name="a-iddevice-permissionaperform-authorization-checks-in-the-device-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="device-permission"></a>Eseguire i controlli di autorizzazione nel dispositivo se il dispositivo supporta azioni che richiedono diversi livelli di autorizzazione

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Dispositivo IoT | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | <p>Il dispositivo deve autorizzare il chiamante per verificare se il chiamante è in possesso delle autorizzazioni necessarie per eseguire l'azione richiesta. Si supponga ad esempio che il dispositivo sia una serratura intelligente che può essere monitorata dal cloud e che offra funzionalità quali la chiusura remota della porta.</p><p>La serratura intelligente consente l'apertura solo quando qualcuno si avvicina fisicamente alla porta con una scheda. In questo caso, l'implementazione del comando e del controllo remoto deve essere eseguita in modo da non fornire funzionalità di apertura della porta perché il gateway nel cloud non è autorizzato a inviare un comando per l'apertura della porta.</p>|

## <a name="a-idfield-permissionaperform-authorization-checks-in-the-field-gateway-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="field-permission"></a>Eseguire i controlli di autorizzazione nel gateway sul campo se il gateway supporta azioni che richiedono diversi livelli di autorizzazione

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Gateway IoT sul campo | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Il gateway sul campo deve autorizzare il chiamante per verificare se il chiamante è in possesso delle autorizzazioni necessarie per eseguire l'azione richiesta. Devono essere ad esempio disponibili autorizzazioni diverse per un'interfaccia/API di utente amministratore usata per configurare un gateway sul campo rispetto ai dispositivi che si connettono al gateway.|
