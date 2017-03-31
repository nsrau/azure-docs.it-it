---
title: 'Dati sensibili: Microsoft Threat Modeling Tool - Azure | Microsoft Docs'
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
ms.openlocfilehash: da8c5dc18fda8756bae26c37c12be8cefe99e29a
ms.lasthandoff: 02/15/2017


---

# <a name="security-frame-sensitive-data--mitigations"></a>Infrastruttura di sicurezza: dati sensibili - Procedure di mitigazione 
| Prodotto o servizio | Articolo |
| --------------- | ------- |
| Limite di trust dei computer | <ul><li>[Assicurarsi che i file binari che contengono informazioni riservate vengano offuscati](#binaries-info)</li><li>[Valutare l'uso della tecnologia EFS (Encrypting File System) per proteggere dati riservati specifici dell'utente](#efs-user)</li><li>[Assicurarsi che i dati sensibili archiviati dall'applicazione nel file system vengano crittografati](#filesystem)</li></ul> | 
| Applicazione Web | <ul><li>[Assicurarsi che i contenuti sensibili non vengano memorizzati nella cache del browser](#cache-browser)</li><li>[Crittografare le sezioni dei file di configurazione dell'app Web contenenti dati sensibili](#encrypt-data)</li><li>[Disabilitare in modo esplicito l'attributo HTML di completamento automatico in input e moduli sensibili](#autocomplete-input)</li><li>[Assicurarsi che i dati sensibili visualizzati nella schermata dell'utente vengano mascherati](#data-mask)</li></ul> | 
| Database | <ul><li>[Implementare la maschera dati dinamica per limitare l'esposizione di dati sensibili a utenti senza privilegi](#dynamic-users)</li><li>[Assicurarsi che le password vengano archiviate in formato hash con valori salt](#salted-hash)</li><li>[Assicurarsi che i dati sensibili nelle colonne di database vengano crittografati](#db-encrypted)</li><li>[Assicurarsi che venga abilitata la crittografia a livello di database (TDE)](#tde-enabled)</li><li>[Assicurarsi che i backup di database vengano crittografati](#backup)</li></ul> | 
| API Web | <ul><li>[Assicurarsi che i dati sensibili relativi all'API Web non vengano memorizzati nell'archivio del browser](#api-browser)</li></ul> | 
| Azure DocumentDB | <ul><li>[Crittografare i dati sensibili archiviati in DocumentDB](#encrypt-docdb)</li></ul> | 
| Limite di trust della macchina virtuale IaaS di Azure | <ul><li>[Usare Crittografia dischi di Azure per crittografare i dischi usati dalle macchine virtuali](#disk-vm)</li></ul> | 
| Limite di trust di Service Fabric | <ul><li>[Crittografare i segreti nelle applicazioni di Service Fabric](#fabric-apps)</li></ul> | 
| Dynamics CRM | <ul><li>[Eseguire la modellazione di sicurezza e usare team e unità aziendali quando richiesto](#modeling-teams)</li><li>[Ridurre al minimo l'accesso alla funzionalità di condivisione nelle entità critiche](#entities)</li><li>[Istruire gli utenti sui rischi associati alla funzionalità di condivisione di Dynamics CRM e sulle procedure di sicurezza consigliate](#good-practices)</li><li>[Includere una regola sugli standard di sviluppo che vieta la visualizzazione dei dettagli di configurazione nella gestione delle eccezioni](#exception-mgmt)</li></ul> | 
| Archiviazione di Azure | <ul><li>[Usare Crittografia del servizio di archiviazione di Azure per dati inattivi (anteprima)](#sse-preview)</li><li>[Usare la crittografia lato client per archiviare dati sensibili in Archiviazione di Azure](#client-storage)</li></ul> | 
| Client per dispositivi mobili | <ul><li>[Crittografare le informazioni personali o i dati sensibili scritti nell'archivio locale del telefono](#pii-phones)</li><li>[Offuscare i file binari generati prima della distribuzione agli utenti finali](#binaries-end)</li></ul> | 
| WCF | <ul><li>[Impostare clientCredentialType su Certificate o su Windows](#cert)</li><li>[WCF: la modalità di sicurezza non è abilitata](#security)</li></ul> | 

## <a name="a-idbinaries-infoaensure-that-binaries-are-obfuscated-if-they-contain-sensitive-information"></a><a id="binaries-info"></a>Assicurarsi che i file binari che contengono informazioni riservate vengano offuscati

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Limite di trust dei computer | 
| Fase SDL               | Distribuzione |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Assicurarsi che vengano offuscati i file binari che contengono informazioni riservate, come segreti commerciali o logica di business riservata che non deve essere sottoposta a reverse engineering. Questa misura consente di prevenire il reverse engineering degli assembly. A questo scopo, è possibile usare strumenti come Crypto Obfuscator. |

## <a name="a-idefs-useraconsider-using-encrypted-file-system-efs-is-used-to-protect-confidential-user-specific-data"></a><a id="efs-user"></a>Valutare l'uso della tecnologia EFS (Encrypting File System) per proteggere dati riservati specifici dell'utente

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Limite di trust dei computer | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Valutare l'uso della tecnologia EFS (Encrypting File System) per proteggere i dati riservati specifici dell'utente da antagonisti che abbiano fisicamente accesso al computer. |

## <a name="a-idfilesystemaensure-that-sensitive-data-stored-by-the-application-on-the-file-system-is-encrypted"></a><a id="filesystem"></a>Assicurarsi che i dati sensibili archiviati dall'applicazione nel file system vengano crittografati

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Limite di trust dei computer | 
| Fase SDL               | Distribuzione |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Se non è possibile applicare la tecnologia EFS, assicurarsi che i dati sensibili archiviati dall'applicazione nel file system vengano crittografati, ad esempio tramite DPAPI. |

## <a name="a-idcache-browseraensure-that-sensitive-content-is-not-cached-on-the-browser"></a><a id="cache-browser"></a>Assicurarsi che i contenuti sensibili non vengano memorizzati nella cache del browser

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Applicazione Web | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico, Web Form, MVC&5;, MVC&6; |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | I browser possono archiviare informazioni per motivi di memorizzazione nella cache e cronologia. Questi file memorizzati nella cache vengono archiviati in una cartella, come la cartella File temporanei Internet nel caso di Internet Explorer. Quando queste pagine vengono visitate di nuovo, il browser le visualizza dalla cache. Se vengono visualizzate informazioni riservate, come l'indirizzo dell'utente, i dati della carta di credito, il codice fiscale o il nome utente, tali informazioni possono essere memorizzate nella cache del browser e quindi recuperate esaminando la cache del browser o semplicemente premendo il pulsante "Indietro" del browser. Impostare il valore dell'intestazione della risposta del controllo cache su "no-store" per tutte le pagine. |

### <a name="example"></a>Esempio
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-cache" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>Esempio
Per l'implementazione è possibile usare un filtro. È possibile usare l'esempio seguente: 
```C#
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || attributes.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a name="a-idencrypt-dataaencrypt-sections-of-web-apps-configuration-files-that-contain-sensitive-data"></a><a id="encrypt-data"></a>Crittografare le sezioni dei file di configurazione dell'app Web contenenti dati sensibili

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Applicazione Web | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | [How To: Encrypt Configuration Sections in ASP.NET 2.0 Using DPAPI](https://msdn.microsoft.com/library/ff647398.aspx) (Procedura: Crittografare le sezioni di configurazione in ASP.NET 2.0 usando DPAPI), [Specifica di un provider di configurazione protetta](https://msdn.microsoft.com/library/68ze1hb2.aspx), [Using Azure Key Vault to protect application secrets](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) (Uso di Azure Key Vault per proteggere i segreti dell'applicazione) |
| Passi | I file di configurazione, ad esempio Web.config e appsettings.json, vengono spesso usati per memorizzare informazioni sensibili, inclusi nomi utente, password, stringhe di connessione del database e chiavi di crittografia. Se non si proteggono queste informazioni, l'applicazione è vulnerabile agli utenti malintenzionati che ottengono informazioni sensibili, ad esempio nomi account utente e password, nomi dei database e nomi dei server. In base al tipo di distribuzione (Azure/locale), crittografare le sezioni sensibili dei file config usando DPAPI o servizi come Azure Key Vault. |

## <a name="a-idautocomplete-inputaexplicitly-disable-the-autocomplete-html-attribute-in-sensitive-forms-and-inputs"></a><a id="autocomplete-input"></a>Disabilitare in modo esplicito l'attributo HTML di completamento automatico in input e moduli sensibili

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Applicazione Web | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | [MSDN: autocomplete attribute](http://msdn.microsoft.com/library/ms533486(VS.85).aspx) (Attributo autocomplete), [Using AutoComplete in HTML Forms](http://msdn.microsoft.com/library/ms533032.aspx) (Uso di AutoComplete nei form HTML), [Vulnerabilità legata alla disinfezione del contenuto HTML](http://technet.microsoft.com/security/bulletin/MS10-071), [Autocomplete...again?!](http://blog.mindedsecurity.com/2011/10/autocompleteagain.html) (Ancora autocomplete?) |
| Passi | L'attributo autocomplete specifica se la funzionalità di completamento automatico di un modulo debba essere abilitata o disabilitata. Quando il completamento automatico è abilitato, il browser completa automaticamente i valori in base a valori immessi in precedenza dall'utente. Ad esempio, quando si immette un nuovo nome e una password in un modulo, che poi viene inviato, il browser chiede se si vuole salvare la password. Quando il modulo viene visualizzato successivamente, il nome e la password vengono popolati automaticamente o vengono compilati immettendo il nome. Un utente malintenzionato con accesso in locale può ottenere la password non crittografata dalla cache del browser. Il completamento automatico è abilitato per impostazione predefinita e deve essere disabilitato in modo esplicito. |

### <a name="example"></a>Esempio
```C#
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a name="a-iddata-maskaensure-that-sensitive-data-displayed-on-the-user-screen-is-masked"></a><a id="data-mask"></a>Assicurarsi che i dati sensibili visualizzati nella schermata dell'utente vengano mascherati

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Applicazione Web | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | I dati sensibili, come password, numeri di carte di credito, codice fiscale e così via devono essere mascherati quando vengono visualizzati sullo schermo. Questa misura serve a impedire l'accesso ai dati da parte di personale non autorizzato, ad esempio con la tecnica dello shoulder surfing o la visualizzazione del codice fiscale dell'utente da parte del personale di supporto. Assicurarsi che questi elementi di dati non siano visibili come testo normale e vengano mascherati in modo appropriato. Questo deve avvenire sia quando vengono accettati come input, ad esempio input type="password", sia quando vengono visualizzati nuovamente sullo schermo, ad esempio mostrando solo le ultime 4 cifre del numero di carta di credito. |

## <a name="a-iddynamic-usersaimplement-dynamic-data-masking-to-limit-sensitive-data-exposure-non-privileged-users"></a><a id="dynamic-users"></a>Implementare la maschera dati dinamica per limitare l'esposizione di dati sensibili a utenti senza privilegi

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Database | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | SQL Azure, locale |
| Attributi              | Versione SQL:&12;, versione SQL: MSSQL2016 |
| Riferimenti              | [Maschera dati dinamica](https://msdn.microsoft.com/library/mt130841) |
| Passi | Lo scopo della maschera dati dinamica è limitare l'esposizione dei dati sensibili, impedendone la visualizzazione da parte di utenti che non dovrebbero avervi accesso. La maschera dati dinamica non impedisce agli utenti di database di connettersi direttamente al database ed eseguire query complete che espongono parti di dati sensibili. La maschera dati dinamica è complementare ad altre funzionalità di sicurezza di SQL Server, come il controllo, la crittografia, la sicurezza a livello di riga e così via, ed è consigliabile usarla insieme a tali funzionalità per proteggere meglio i dati sensibili nel database. Si noti che questa funzionalità è supportata unicamente da SQL Server, a partire dalla versione 2016, e dal database SQL di Azure. |

## <a name="a-idsalted-hashaensure-that-passwords-are-stored-in-salted-hash-format"></a><a id="salted-hash"></a>Assicurarsi che le password vengano archiviate in formato hash con valori salt

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Database | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | [Hash delle password con le API di crittografia di .NET](http://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| Passi | Le password non devono essere archiviate in database dell'archivio utenti personalizzati. Gli hash delle password devono invece essere archiviati con valori salt. Assicurarsi che il valore salt per l'utente sia sempre univoco e di applicare bcrypt, scrypt o PBKDF2 prima di archiviare la password, con un conteggio delle iterazioni del fattore lavoro minimo di 150.000 cicli per eliminare la possibilità di attacchi di forza bruta.| 

## <a name="a-iddb-encryptedaensure-that-sensitive-data-in-database-columns-is-encrypted"></a><a id="db-encrypted"></a>Assicurarsi che i dati sensibili nelle colonne di database vengano crittografati

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Database | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | Versione SQL: tutte |
| Riferimenti              | [Crittografia di dati sensibili in SQL Server](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [Procedura: Crittografare una colonna di dati in SQL Server](https://msdn.microsoft.com/library/ms179331), [Crittografare con un certificato](https://msdn.microsoft.com/library/ms188061) |
| Passi | I dati sensibili, come i numeri di carte di credito, devono essere crittografati nel database. A tale scopo è possibile usare la crittografia a livello di colonna o una funzione di applicazione tramite funzioni di crittografia. |

## <a name="a-idtde-enabledaensure-that-database-level-encryption-tde-is-enabled"></a><a id="tde-enabled"></a>Assicurarsi che venga abilitata la crittografia a livello di database (TDE)

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Database | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | [Informazioni su Transparent Data Encryption (TDE) in SQL Server](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| Passi | La funzionalità Transparent Data Encryption (TDE) in SQL Server consente di crittografare i dati sensibili in un database e proteggere le chiavi usate per crittografare i dati con un certificato. Questo impedisce l'uso dei dati senza le chiavi. TDE protegge i dati inattivi, ovvero i file di dati e di log. Offre inoltre la possibilità di conformarsi a diverse leggi, normative e linee guida stabilite in vari settori. |

## <a name="a-idbackupaensure-that-database-backups-are-encrypted"></a><a id="backup"></a>Assicurarsi che i backup di database vengano crittografati

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Database | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | SQL Azure, locale |
| Attributi              | Versione SQL:&12;, versione SQL: MSSQL2014 |
| Riferimenti              | [Crittografia dei backup del database SQL](https://msdn.microsoft.com/library/dn449489) |
| Passi | SQL Server permette di crittografare i dati durante la creazione di un backup. Specificando l'algoritmo di crittografia e il componente di crittografia, ad esempio un certificato o una chiave asimmetrica, durante la creazione di un backup, è possibile creare un file di backup crittografato. |

## <a name="a-idapi-browseraensure-that-sensitive-data-relevant-to-web-api-is-not-stored-in-browsers-storage"></a><a id="api-browser"></a>Assicurarsi che i dati sensibili relativi all'API Web non vengano memorizzati nell'archivio del browser

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | API Web | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | MVC 5, MVC 6 |
| Attributi              | Provider di identità: AD FS, provider di identità: Azure AD |
| Riferimenti              | N/D  |
| Passi | <p>In alcune implementazioni, gli elementi sensibili relativi all'autenticazione dell'API Web vengono memorizzati nell'archivio locale del browser. Si tratta, ad esempio, di elementi di autenticazione di Azure AD come adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key e così via.</p><p>Tutti questi elementi sono disponibili anche dopo la disconnessione o la chiusura del browser. Se un antagonista ottiene accesso a questi elementi, può riutilizzarli per accedere alle risorse protette, ovvero le API. Assicurarsi che tutti gli elementi sensibili relativi all'API Web non vengano memorizzati nell'archivio del browser. Nei casi in cui non è possibile evitare l'archiviazione lato client, ad esempio nel caso delle applicazioni a pagina singola che fanno uso di flussi OAuth/OpenID Connect impliciti e che devono archiviare i token di accesso in locale, usare le opzioni di archiviazione senza persistenza. Ad esempio, è preferibile usare SessionStorage anziché LocalStorage.</p>| 

### <a name="example"></a>Esempio
Il frammento JavaScript seguente proviene da una libreria di autenticazione personalizzata che archivia gli elementi di autenticazione nell'archivio locale. Evitare le implementazioni di questo tipo. 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a name="a-idencrypt-docdbaencrypt-sensitive-data-stored-in-documentdb"></a><a id="encrypt-docdb"></a>Crittografare i dati sensibili archiviati in DocumentDB

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Azure DocumentDB | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Crittografare i dati sensibili a livello dell'applicazione prima di archiviarli in DocumentDB o in altre soluzioni di archiviazione come Archiviazione di Azure o SQL Azure.| 

## <a name="a-iddisk-vmause-azure-disk-encryption-to-encrypt-disks-used-by-virtual-machines"></a><a id="disk-vm"></a>Usare Crittografia dischi di Azure per crittografare i dischi usati dalle macchine virtuali

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Limite di trust della macchina virtuale IaaS di Azure | 
| Fase SDL               | Distribuzione |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | [Uso di Crittografia dischi di Azure per crittografare i dischi usati dalle macchine virtuali](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| Passi | <p>Crittografia dischi di Azure è una nuova funzionalità attualmente in anteprima. Questa funzionalità consente di crittografare i dischi dati e del sistema operativo usati da una macchina virtuale IaaS. Per Windows, le unità vengono crittografate mediante la tecnologia di crittografia BitLocker standard del settore. Per Linux, i dischi vengono crittografati mediante la tecnologia DM-Crypt, integrata nell'insieme di credenziali delle chiavi per consentire il controllo e la gestione delle chiavi di crittografia del disco. La soluzione Crittografia dischi di Azure supporta i tre scenari di crittografia dei clienti descritti di seguito:</p><ul><li>Abilitare la crittografia in nuove VM IaaS create da file VHD crittografati dal cliente e chiavi di crittografia fornite dal cliente, che vengono archiviate nell'insieme di credenziali delle chiavi di Azure.</li><li>Abilitare la crittografia di nuove VM IaaS create da Azure Marketplace.</li><li>Abilitare la crittografia delle VM IaaS esistenti già in esecuzione in Azure.</li></ul>| 

## <a name="a-idfabric-appsaencrypt-secrets-in-service-fabric-applications"></a><a id="fabric-apps"></a>Crittografare i segreti nelle applicazioni di Service Fabric

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Limite di trust di Service Fabric | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | Ambiente: Azure |
| Riferimenti              | [Gestione dei segreti nelle applicazioni di Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| Passi | I segreti possono essere informazioni riservate, ad esempio le stringhe di connessione di archiviazione, le password o altri valori che non devono essere gestiti in testo normale. Usare Azure Key Vault per gestire le chiavi e i segreti nelle applicazioni di Service Fabric. |

## <a name="a-idmodeling-teamsaperform-security-modeling-and-use-business-unitsteams-where-required"></a><a id="modeling-teams"></a>Eseguire la modellazione di sicurezza e usare team e unità aziendali quando richiesto

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Dynamics CRM | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Eseguire la modellazione di sicurezza e usare team e unità aziendali quando richiesto. |

## <a name="a-identitiesaminimize-access-to-share-feature-on-critical-entities"></a><a id="entities"></a>Ridurre al minimo l'accesso alla funzionalità di condivisione nelle entità critiche

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Dynamics CRM | 
| Fase SDL               | Distribuzione |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Ridurre al minimo l'accesso alla funzionalità di condivisione nelle entità critiche. |

## <a name="a-idgood-practicesatrain-users-on-the-risks-associated-with-the-dynamics-crm-share-feature-and-good-security-practices"></a><a id="good-practices"></a>Istruire gli utenti sui rischi associati alla funzionalità di condivisione di Dynamics CRM e sulle procedure di sicurezza consigliate

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Dynamics CRM | 
| Fase SDL               | Distribuzione |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Istruire gli utenti sui rischi associati alla funzionalità di condivisione di Dynamics CRM e sulle procedure di sicurezza consigliate. |

## <a name="a-idexception-mgmtainclude-a-development-standards-rule-proscribing-showing-config-details-in-exception-management"></a><a id="exception-mgmt"></a>Includere una regola sugli standard di sviluppo che vieta la visualizzazione dei dettagli di configurazione nella gestione delle eccezioni

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Dynamics CRM | 
| Fase SDL               | Distribuzione |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | N/D  |
| Passi | Includere una regola sugli standard di sviluppo che vieta la visualizzazione dei dettagli di configurazione nella gestione delle eccezioni al di fuori del contesto di sviluppo. Testare tale regola come parte di revisioni del codice o ispezioni periodiche.|

## <a name="a-idsse-previewause-azure-storage-service-encryption-sse-for-data-at-rest-preview"></a><a id="sse-preview"></a>Usare Crittografia del servizio di archiviazione di Azure per dati inattivi (anteprima)

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Archiviazione di Azure | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | Tipo di archiviazione: BLOB |
| Riferimenti              | [Crittografia del servizio di archiviazione di Azure per dati inattivi (anteprima)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| Passi | <p>Crittografia del servizio di archiviazione di Azure per dati inattivi consente di proteggere e salvaguardare i dati, in modo da soddisfare i criteri di sicurezza e conformità dell'organizzazione. Questa funzionalità consente ad Archiviazione di Azure di crittografare automaticamente i dati prima della persistenza nella risorsa di archiviazione e di decrittografarli prima del recupero. La crittografia, la decrittografia e la gestione delle chiavi sono completamente trasparenti per gli utenti. SSE viene applicato solo per BLOB in blocchi, BLOB di pagine e BLOB di aggiunta. Altri tipi di dati, tra cui tabelle, code e file, non verranno crittografati.</p><p>Flusso di lavoro di crittografia e decrittografia:</p><ul><li>Il cliente abilita la crittografia sull'account di archiviazione.</li><li>Quando il cliente scrive nuovi dati (PUT Blob, PUT Block, PUT Page e così via) nell'archivio BLOB, ogni operazione di scrittura viene crittografata mediante la crittografia AES a 256 bit, una delle crittografie a blocchi più solide tra quelle disponibili.</li><li>Quando il cliente deve accedere ai dati (GET Blob e così via), i dati vengono decrittografati automaticamente prima della restituzione all'utente.</li><li>Se la crittografia è disabilitata, le nuove operazioni di scrittura non vengono più crittografate e i dati crittografati esistenti rimangono crittografati fino a quando non vengono riscritti dall'utente. Quando la crittografia è abilitata, le operazioni di scrittura nell'archivio BLOB verranno sempre crittografate. Lo stato dei dati non cambia quando l'utente abilita/disabilita la crittografia per l'account di archiviazione.</li><li>Tutte le chiavi di crittografia vengono archiviate, crittografate e gestite da Microsoft.</li></ul><p>Al momento, le chiavi usate per la crittografia sono gestite da Microsoft. Microsoft genera le chiavi in origine e ne gestisce l'archiviazione protetta, nonché la rotazione regolare secondo quanto definito dai criteri interni di Microsoft. In futuro i clienti potranno gestire le relative chiavi di crittografia e verrà fornito un percorso di migrazione dalle chiavi gestite da Microsoft alle chiavi gestite dal cliente.</p>| 

## <a name="a-idclient-storageause-client-side-encryption-to-store-sensitive-data-in-azure-storage"></a><a id="client-storage"></a>Usare la crittografia lato client per archiviare dati sensibili in Archiviazione di Azure

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Archiviazione di Azure | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | [Crittografia lato client e Azure Key Vault per Archiviazione di Microsoft Azure](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [Esercitazione: Crittografare e decrittografare i BLOB in Archiviazione di Microsoft Azure tramite Azure Key Vault](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), [Storing Data Securely in Azure Blob Storage with Azure Encryption Extensions](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) (Archiviazione protetta dei dati nell'archivio BLOB di Azure con le estensioni di crittografia di Azure) |
| Passi | <p>Il pacchetto NuGet della libreria client di archiviazione di Azure per .NET supporta la crittografia dei dati all'interno delle applicazioni client prima del caricamento in Archiviazione di Azure, nonché la decrittografia dei dati durante il download nel client. La libreria supporta anche l'integrazione con l'insieme di credenziali delle chiavi di Azure per la gestione delle chiavi dell'account di archiviazione. Ecco una breve descrizione del funzionamento della crittografia lato client:</p><ul><li>Azure Storage Client SDK genera una chiave di crittografia del contenuto (CEK) che funziona come chiave simmetrica monouso.</li><li>I dati del cliente vengono crittografati con la chiave CEK.</li><li>Viene quindi eseguito il wrapping della chiave CEK mediante la chiave di crittografia della chiave (KEK). La chiave KEK è identificata con un identificatore di chiave e può essere costituita da una coppia di chiavi asimmetriche o da una chiave simmetrica. Può essere gestita localmente o archiviata nell'insieme di credenziali delle chiavi di Azure. Il client di archiviazione non ha mai accesso alla chiave KEK. Richiama solo l'algoritmo di wrapping della chiave fornito dall'insieme di credenziali chiave. Volendo, i clienti possono scegliere di usare provider personalizzati per il wrapping o la rimozione del wrapping delle chiavi.</li><li>I dati crittografati vengono quindi caricati nel servizio Archiviazione di Azure. Per informazioni dettagliate sull'implementazione, vedere i collegamenti riportati nella sezione dei riferimenti.</li></ul>|

## <a name="a-idpii-phonesaencrypt-sensitive-or-pii-data-written-to-phones-local-storage"></a><a id="pii-phones"></a>Crittografare le informazioni personali o i dati sensibili scritti nell'archivio locale del telefono

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Client per dispositivi mobili | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico, Xamarin  |
| Attributi              | N/D  |
| Riferimenti              | [Gestire impostazioni e funzionalità nei dispositivi con i criteri di Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies#create-a-configuration-policy), [Keychain Valet](https://components.xamarin.com/view/square.valet) |
| Passi | <p>Se l'applicazione scrive le informazioni sensibili, come le informazioni personali dell'utente, vale a dire messaggi di posta elettronica, numero di telefono, nome, cognome, preferenze e così via, nel file system del dispositivo mobile, le informazioni devono essere crittografate prima della scrittura nel file system locale. Se si tratta di un'applicazione aziendale, valutare la possibilità di pubblicare l'applicazione tramite Microsoft Intune.</p>|

### <a name="example"></a>Esempio
Per proteggere i dati sensibili, è possibile configurare Intune con i criteri di sicurezza seguenti: 
```C#
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Esempio
Se non si tratta di un'applicazione aziendale, usare l'archivio chiavi offerto dalla piattaforma e i portachiavi per archiviare le chiavi di crittografia, tramite l'operazione di crittografia che può essere eseguita nel file system. Il frammento di codice seguente mostra come accedere alla chiave dal portachiavi usando Xamarin: 
```C#
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a name="a-idbinaries-endaobfuscate-generated-binaries-before-distributing-to-end-users"></a><a id="binaries-end"></a>Offuscare i file binari generati prima della distribuzione agli utenti finali

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | Client per dispositivi mobili | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico |
| Attributi              | N/D  |
| Riferimenti              | [Crypto Obfuscator For .Net](http://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| Passi | È necessario offuscare i file binari generati, ovvero gli assembly all'interno di APK, per impedire che gli assembly siano sottoposti a reverse engineering. A questo scopo, è possano utilizzare strumenti come Crypto Obfuscator. |

## <a name="a-idcertaset-clientcredentialtype-to-certificate-or-windows"></a><a id="cert"></a>Impostare clientCredentialType su Certificate o su Windows

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | WCF | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | .NET Framework 3 |
| Attributi              | N/D  |
| Riferimenti              | [Fortify](https://vulncat.fortify.com/en/vulncat/index.html) |
| Passi | L'uso di UsernameToken con una password in testo normale tramite un canale non crittografato espone la password a utenti malintenzionati che potrebbero eseguire lo sniffing dei messaggi SOAP. I provider di servizi che fanno uso di UsernameToken possono accettare le password inviate in testo normale, ma l'invio di password in testo normale tramite un canale non crittografato può esporre le credenziali a utenti malintenzionati che potrebbero eseguire lo sniffing dei messaggi SOAP. | 

### <a name="example"></a>Esempio
La configurazione del provider di servizi WCF riportata di seguito fa uso di UsernameToken: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Impostare clientCredentialType su Certificate o su Windows. 

## <a name="a-idsecurityawcf-security-mode-is-not-enabled"></a><a id="security"></a>WCF: la modalità di sicurezza non è abilitata

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| Componente               | WCF | 
| Fase SDL               | Compilare |  
| Tecnologie applicabili | Generico, .NET Framework 3 |
| Attributi              | Modalità di sicurezza: trasporto, modalità di sicurezza: messaggio |
| Riferimenti              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/vulncat/index.html), [CoDe Magazine: Fundamentals of WCF Security](http://www.codemag.com/article/0611051) (Concetti fondamentali sulla sicurezza in WCF) |
| Passi | Non è stata definita alcuna sicurezza del trasporto o del messaggio. Le applicazioni che trasmettono messaggi senza sicurezza del trasporto o del messaggio non possono garantire l'integrità o la riservatezza dei messaggi. Quando un'associazione di sicurezza di WCF è impostata su None, la sicurezza del trasporto e quella del messaggio sono entrambe disabilitate. |

### <a name="example"></a>Esempio
La configurazione seguente imposta la modalità di sicurezza su None. 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>Esempio
Le associazioni ai servizi presentano cinque modalità di sicurezza possibili: 
* None. Disabilita la sicurezza. 
* Transport. Usa la sicurezza del trasporto per la protezione reciproca del messaggio e dell'autenticazione. 
* Message. Usa la sicurezza del messaggio per la protezione reciproca del messaggio e dell'autenticazione. 
* Both. Permette di specificare le impostazioni per la sicurezza del trasporto e a livello di messaggio (supportata solo in MSMQ). 
* TransportWithMessageCredential. Le credenziali vengono passate con il messaggio. La protezione dei messaggi e l'autenticazione server sono garantite dal livello di trasporto. 
* TransportCredentialOnly. Le credenziali del client vengono passate con il livello di trasporto e non viene applicata alcuna protezione al messaggio. Usare la sicurezza del messaggio e del trasporto per proteggere l'integrità e riservatezza dei messaggi. La configurazione seguente indica al servizio di usare la sicurezza del trasporto con le credenziali del messaggio.
```
<system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
</system.serviceModel> 
```
