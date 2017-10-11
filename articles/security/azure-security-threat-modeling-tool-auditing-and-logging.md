---
title: Controllo e registrazione - Microsoft Threat Modeling Tool - Azure | Documentazione Microsoft
description: Procedure di riduzione delle minacce esposte in Threat Modeling Tool
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
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 3f1933fc59862eca7ae6ee40bbd5136e449e5cf1
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Infrastruttura di sicurezza: controllo e registrazione | Soluzioni di riduzione 
| Prodotto o servizio | Articolo |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identificare le entità sensibili nella soluzione e implementare il controllo delle modifiche](#sensitive-entities)</li></ul> |
| **Applicazione Web** | <ul><li>[Assicurarsi che la funzionalità di controllo e registrazione venga applicata all'applicazione](#auditing)</li><li>[Assicurarsi che la rotazione e la separazione dei log siano abilitate](#log-rotation)</li><li>[Assicurarsi che l'applicazione non registri nel log i dati sensibili degli utenti](#log-sensitive-data)</li><li>[Assicurarsi che l'accesso ai file di log e di controllo sia limitato](#log-restricted-access)</li><li>[Assicurarsi che gli eventi di Gestione utenti vengano registrati nel log](#user-management)</li><li>[Assicurarsi che il sistema disponga di difese integrate contro l'utilizzo improprio](#inbuilt-defenses)</li><li>[Abilitare la registrazione diagnostica per le app Web nel servizio app di Azure](#diagnostics-logging)</li></ul> |
| **Database** | <ul><li>[Verificare che il controllo degli accessi sia abilitato in SQL Server](#identify-sensitive-entities)</li><li>[Abilitare il rilevamento delle minacce in SQL Azure](#threat-detection)</li></ul> |
| **Archiviazione di Azure** | <ul><li>[Usare la funzionalità di analisi di Archiviazione di Azure per controllare gli accessi di Archiviazione di Azure](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementare un livello di registrazione sufficiente](#sufficient-logging)</li><li>[Implementare un livello di gestione degli errori di controllo sufficiente](#audit-failure-handling)</li></ul> |
| **API Web** | <ul><li>[Assicurarsi che la funzionalità di controllo e registrazione venga applicata nell'API Web](#logging-web-api)</li></ul> |
| **Gateway IoT sul campo** | <ul><li>[Assicurarsi che la funzionalità di controllo e registrazione appropriata venga applicata nel gateway sul campo](#logging-field-gateway)</li></ul> |
| **Gateway IoT cloud** | <ul><li>[Assicurarsi che la funzionalità di controllo e registrazione appropriata venga applicata nel gateway nel cloud](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>Identificare le entità sensibili nella soluzione e implementare il controllo delle modifiche

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi**                   | Identificare le entità nella soluzione che contengono dati sensibili e implementare il controllo delle modifiche in tali entità e campi |

## <a id="auditing"></a>Assicurarsi che la funzionalità di controllo e registrazione venga applicata nell'applicazione

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi**                   | Abilitare il controllo e la registrazione su tutti i componenti. I log di controllo devono acquisire il contesto utente. Identificare tutti gli eventi importanti e registrarli nel log. Implementare la registrazione centralizzata |

## <a id="log-rotation"></a>Assicurarsi che la rotazione e la separazione dei log siano abilitate

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi**                   | <p>La rotazione dei log è un processo automatizzato usato nell'amministrazione di sistema che prevede l'archiviazione dei file di log datati. I server che eseguono applicazioni di grandi dimensioni spesso registrano ogni richiesta. A fronte di log ingombranti, la rotazione rappresenta un modo per limitarne le dimensioni totali mantenendo comunque l'analisi degli eventi recenti. </p><p>La separazione dei log si basa sulla necessità di archiviare i file di log in una partizione diversa da quella in cui viene eseguito il sistema operativo o l'applicazione al fine di evitare un attacco Denial of Service o il downgrade delle prestazioni dell'applicazione</p>|

## <a id="log-sensitive-data"></a>Assicurarsi che l'applicazione non registri nel log i dati sensibili degli utenti

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi**                   | <p>Verificare di non registrare nei log i dati sensibili che un utente invia al sito. Verificare la registrazione intenzionale, nonché gli effetti collaterali causati da problemi di progettazione. Esempi di dati sensibili:</p><ul><li>Credenziali dell'utente</li><li>Codice fiscale o altre informazioni di identificazione</li><li>Numeri di carta di credito o altre informazioni finanziarie</li><li>Informazioni sulla salute</li><li>Chiavi private o altri dati che potrebbero essere usati per decrittografare le informazioni crittografate</li><li>Informazioni sul sistema o sull'applicazione che possono essere usate per attaccare l'applicazione in modo più efficace</li></ul>|

## <a id="log-restricted-access"></a>Assicurarsi che l'accesso ai file di log e di controllo sia limitato

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi**                   | <p>Verificare che i diritti di accesso ai file di log siano impostati in modo appropriato. Gli account dell'applicazione devono avere l'accesso in sola scrittura, mentre gli operatori e il personale di supporto devono disporre dell'accesso in sola lettura a seconda delle esigenze.</p><p>Gli account degli amministratori sono gli unici che devono avere accesso completo. Controllare l'ACL di Windows nei file di log per verificare che l'accesso sia limitato nel modo appropriato:</p><ul><li>Gli account dell'applicazione devono disporre dell'accesso in sola scrittura</li><li>Gli operatori e il personale di supporto devono disporre dell'accesso in sola lettura a seconda delle esigenze</li><li>Gli amministratori sono gli unici account che devono avere accesso completo</li></ul>|

## <a id="user-management"></a>Assicurarsi che gli eventi di Gestione utenti vengano registrati nel log

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi**                   | <p>Assicurarsi che l'applicazione monitori gli eventi di gestione degli utenti, quali gli accessi utente riusciti e non riusciti, le reimpostazioni e le modifiche delle password, il blocco degli account e la registrazione degli utenti. Questo monitoraggio consente di rilevare comportamenti potenzialmente sospetti e di reagire con azioni appropriate. Consente inoltre di raccogliere dati operativi, ad esempio per tenere traccia di chi accede all'applicazione.</p>|

## <a id="inbuilt-defenses"></a>Assicurarsi che il sistema disponga di difese integrate contro l'utilizzo improprio

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi**                   | <p>È necessario impostare dei controlli che generino un'eccezione di sicurezza in caso di utilizzo improprio dell'applicazione. Ad esempio, se la convalida dell'input è attiva e un utente malintenzionato tenta di inserire codice dannoso che non corrisponde all'espressione regolare, il sistema può generare un'eccezione di sicurezza che può essere indicativa di un utilizzo improprio.</p><p>È consigliabile ad esempio registrare le eccezioni di sicurezza e le azioni intraprese per i seguenti problemi:</p><ul><li>Convalida dell'input</li><li>Violazioni di richiesta intersito falsa</li><li>Attacchi di forza bruta (limite massimo del numero di richieste per utente per risorsa)</li><li>Violazioni di caricamento file</li><ul>|

## <a id="diagnostics-logging"></a>Abilitare la registrazione diagnostica per le app Web nel servizio app di Azure

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | Tipo di ambiente: Azure |
| **Riferimenti**              | N/D  |
| **Passaggi** | <p>Azure offre funzionalità di diagnostica integrate per facilitare il debug di un'app Web del servizio app. La diagnostica viene applicata anche alle app per le API e alle app per dispositivi mobili. App Web del servizio app offre funzionalità diagnostiche per la registrazione di informazioni sia dal server Web sia dall'applicazione Web,</p><p>logicamente separate in diagnostica server Web e diagnostica applicazioni.</p>|

## <a id="identify-sensitive-entities"></a>Verificare che il controllo degli accessi sia abilitato in SQL Server

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Database | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Configurare il controllo degli accessi](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Passaggi** | <p>Il controllo degli accessi al server di database deve essere abilitato per rilevare/confermare eventuali attacchi mirati a determinare le password di accesso. È importante acquisire i tentativi di accesso non riusciti. L'acquisizione dei tentativi di accesso riusciti e non offre vantaggi aggiuntivi nelle indagini forensi</p>|

## <a id="threat-detection"></a>Abilitare il rilevamento delle minacce in SQL Azure

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Database | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | SQL Azure |
| **Attributes (Attributi) (Attributi)**              | Versione SQL: 12 |
| **Riferimenti**              | [Introduzione al rilevamento delle minacce nel database SQL](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Passaggi** |<p>La funzionalità di rilevamento delle minacce individua le attività di database che indicano la presenza di potenziali minacce alla sicurezza nel database. Tale funzionalità offre un nuovo livello di protezione che consente ai clienti di rilevare e di rispondere alle minacce potenziali non appena si verificano, fornendo avvisi di sicurezza sulle attività anomale.</p><p>Gli utenti possono esaminare gli eventi sospetti tramite il servizio di controllo del database SQL di Azure per determinare se sono il risultato di un tentativo di accesso, una violazione o un exploit dei dati nel database.</p><p>Il rilevamento delle minacce consente di affrontare le minacce potenziali al database senza dover essere esperti della sicurezza o gestire sistemi di controllo di sicurezza avanzati.</p>|

## <a id="analytics"></a>Usare la funzionalità di analisi di Archiviazione di Azure per controllare gli accessi di Archiviazione di Azure

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Archiviazione di Azure | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D |
| **Riferimenti**              | [Uso di Analisi archiviazione per monitorare il tipo di autorizzazione](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Passaggi** | <p>Per ogni account di archiviazione è possibile abilitare la funzionalità di analisi di Archiviazione di Azure per eseguire la registrazione e archiviare i dati di metrica. I log di analisi dell'archiviazione rendono disponibili informazioni importanti, quali il metodo di autenticazione usato dagli utenti per accedere alla risorsa di archiviazione.</p><p>Queste informazioni possono essere molto utili se l'accesso alla risorsa di archiviazione e strettamente protetto. Ad esempio, nell'archivio BLOB è possibile impostare tutti i contenitori su privato e implementare l'uso di un servizio di firma di accesso condiviso per tutte le applicazioni. È quindi possibile controllare i log regolarmente per verificare se l'accesso ai BLOB viene eseguito con chiavi dell'account di archiviazione, cosa che può indicare una violazione della sicurezza, o se i BLOB sono pubblici ma non dovrebbero esserlo.</p>|

## <a id="sufficient-logging"></a>Implementare un livello di registrazione sufficiente

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | .NET Framework |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passaggi** | <p>La mancanza di un'audit trail adeguata dopo un attacco può compromettere le indagini forensi. Windows Communication Foundation (WCF) offre la possibilità di registrare i tentativi di autenticazione riusciti e/o non riusciti.</p><p>La registrazione dei tentativi di autenticazione non riusciti può avvisare gli amministratori di potenziali attacchi di forza bruta. Analogamente, la registrazione di eventi di autenticazione riusciti può offrire una utile audit trail in presenza di compromissioni dei account validi. Abilitare la funzionalità di controllo di sicurezza del servizio di WCF |

### <a name="example"></a>Esempio
Di seguito è riportato un esempio di configurazione con la funzionalità di controllo abilitata
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a id="audit-failure-handling"></a>Implementare un livello di gestione degli errori di controllo sufficiente

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | .NET Framework |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passaggi** | <p>La soluzione sviluppata è configurata in modo da non generare eccezioni quando non riesce a scrivere in un log di controllo. Se WCF è configurato per non generare un'eccezione quando non è in grado di scrivere un log di controllo, il programma non viene avvisato dell'errore ed è possibile che il controllo degli eventi critici della protezione non avvenga.</p>|

### <a name="example"></a>Esempio
L'elemento `<behavior/>` seguente del file di configurazione WCF indica a WCF di non notificare all'applicazione quando WCF non riesce a scrivere in un log di controllo.
````
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
````
Configurare WCF in modo da notificare al programma ogni volta che non riesce a scrivere in un log di controllo. Il programma deve disporre di uno schema di notifica alternativo per avvisare l'organizzazione che le audit trail non vengono mantenute. 

## <a id="logging-web-api"></a>Assicurarsi che la funzionalità di controllo e registrazione venga applicata nell'API Web

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Abilitare il controllo e la registrazione su tutte le API Web. I log di controllo devono acquisire il contesto utente. Identificare tutti gli eventi importanti e registrarli nel log. Implementare la registrazione centralizzata |

## <a id="logging-field-gateway"></a>Assicurarsi che la funzionalità di controllo e registrazione appropriata venga applicata nel gateway sul campo

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Gateway IoT sul campo | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | <p>Quando più dispositivi si connettono a un gateway sul campo, assicurarsi che i tentativi di connessione e lo stato di autenticazione (esito positivo o negativo) per i singoli dispositivi vengano registrati e conservati nel gateway sul campo.</p><p>Nei casi in cui il gateway sul campo gestisca le credenziali IoT Hub per i singoli dispositivi, assicurarsi anche che il controllo venga eseguito durante il recupero di queste credenziali. Sviluppare un processo per caricare periodicamente i log nella risorsa di archiviazione Azure/IoT Hub per la conservazione a lungo termine.</p> |

## <a id="logging-cloud-gateway"></a>Assicurarsi che la funzionalità di controllo e registrazione appropriata venga applicata nel gateway nel cloud

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Gateway IoT cloud | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Introduzione al monitoraggio delle operazioni IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Passaggi** | <p>Progettare la raccolta e l'archiviazione dei dati di controllo raccolti tramite il monitoraggio delle operazioni IoT Hub. Abilitare le categorie di monitoraggio seguenti:</p><ul><li>Operazioni relative alle identità dei dispositivi</li><li>Comunicazioni da dispositivo a cloud</li><li>Comunicazioni da cloud a dispositivo</li><li>Connessioni</li><li>Caricamenti di file</li></ul>|