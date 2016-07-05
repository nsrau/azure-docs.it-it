<properties 
    pageTitle="Autenticazione e autorizzazione del bus di servizio | Microsoft Azure"
    description="Panoramica dell'autenticazione della firma di accesso condiviso."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/22/2016"
    ms.author="sethm" />

# Autenticazione e autorizzazione del bus di servizio

Le applicazioni possono eseguire l'autenticazione al bus di servizio di Microsoft Azure tramite l'autenticazione della firma di accesso condiviso o tramite il servizio di controllo di accesso di Microsoft Azure Active Directory (anche noto come Servizio di controllo di accesso o ACS). L'autenticazione della firma di accesso condiviso consente alle applicazioni di eseguire l'autenticazione al bus di servizio tramite una chiave di accesso configurata nello spazio dei nomi o nell'entità a cui sono associati diritti specifici. È quindi possibile usare questa chiave per generare un token di firma di accesso condiviso che i client possono usare per eseguire l'autenticazione al bus di servizio.

La firma di accesso condiviso offre uno schema di autenticazione per il bus di servizio semplice, flessibile e di facile utilizzo. Le applicazioni possono usare la firma di accesso condiviso in scenari in cui non è necessario gestire la nozione di "utente" autorizzato.

## Autenticazione della firma di accesso condiviso

L'[autenticazione della firma di accesso condiviso](service-bus-sas-overview.md) garantisce l'accesso dell'utente alle risorse del bus di servizio con diritti specifici. Nel bus di servizio, l'autenticazione della firma di accesso condiviso implica la configurazione di una chiave di crittografia con i relativi diritti in una risorsa del bus di servizio. I client possono quindi ottenere accesso a questa risorsa presentando un token di firma di accesso condiviso composto dall'URI della risorsa a cui si vuole accedere e da una scadenza firmata con la chiave configurata.

È possibile configurare le chiavi per la firma di accesso condiviso in uno spazio dei nomi del bus di servizio. La chiave si applica a tutte le entità di messaggistica nello spazio dei nomi. È anche possibile configurare le chiavi nelle code e negli argomenti del bus di servizio. La firma di accesso condiviso è supportata anche negli inoltri del bus di servizio.

Per usare la firma di accesso condiviso, è possibile configurare un oggetto [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) in uno spazio dei nomi, una coda o un argomento costituito dai parametri seguenti:

- *KeyName* che identifica la regola.

- *PrimaryKey* che è una chiave di crittografia usata per firmare/convalidare i token di firma di accesso condiviso.

- *SecondaryKey* che è una chiave di crittografia usata per firmare/convalidare i token di firma di accesso condiviso.

- *Rights* che rappresenta la raccolta di diritti Listen, Send o Manage concessi.

Le regole di autorizzazione configurate a livello di spazio dei nomi possono garantire l'accesso a tutte le entità in uno spazio dei nomi per i client con token firmati che usano la chiave corrispondente. In uno spazio dei nomi, una coda o un argomento del bus di servizio è possibile configurare fino a 12 regole di autorizzazione. Per impostazione predefinita, un oggetto [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) con tutti i diritti viene configurato per ogni spazio dei nomi quando ne viene eseguito il provisioning per la prima volta.

Per accedere a un'entità, è necessario un token di firma di accesso condiviso generato usando un oggetto [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). Il token di firma di accesso condiviso viene generato usando l'algoritmo HMAC-SHA256 di una stringa di risorsa composta dall'URI della risorsa a cui si vuole accedere e da una scadenza, seguiti da una chiave di crittografia associata alla regola di autorizzazione.

Il supporto per l'autenticazione della firma di accesso condiviso per il bus di servizio è incluso in Azure .NET SDK 2.0 e versioni successive. Nella firma di accesso condiviso è incluso il supporto per un oggetto [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). Tutte le API che accettano una stringa di connessione come parametro includono il supporto per le stringhe di connessione della firma di accesso condiviso.

## Autenticazione tramite il servizio di controllo di accesso (ACS)

L'autenticazione del bus di servizio tramite ACS viene gestita con uno spazio dei nomi ACS "-sb" specifico. Se si vuole che venga creato uno spazio dei nomi ACS specifico per uno spazio dei nomi del bus di servizio, non è possibile creare lo spazio dei nomi del bus di servizio usando il portale di Azure classico, ma è necessario usare il cmdlet di PowerShell [New-AzureSBNamespace](https://msdn.microsoft.com/library/azure/dn495165.aspx). Ad esempio:

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

Per evitare di creare uno spazio dei nomi ACS, eseguire il comando seguente:

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

Se ad esempio si crea uno spazio dei nomi del bus di servizio denominato **contoso.servicebus.windows.net**, viene generato automaticamente uno spazio dei nomi di ACS denominato **contoso-sb.accesscontrol.windows.net** specifico. Per tutti gli spazi dei nomi creati prima di agosto 2014, è stato creato il relativo spazio dei nomi ACS.

Per impostazione predefinita, in questo spazio dei nomi ACS viene eseguito il provisioning di un "proprietario" dell'identità del servizio predefinito con tutti i relativi diritti. Per ottenere un controllo con granularità fine su qualsiasi entità del bus di servizio tramite ACS, configurare le relazioni di trust appropriate. È anche possibile configurare identità del servizio aggiuntive per la gestione dell'accesso alle entità del bus di servizio.

Per accedere a un'entità, il client richiede un token in formato Token Web semplice (SWT) da ACS con le attestazioni appropriate, presentando le relative credenziali. Il token SWT deve quindi essere inviato come parte della richiesta al bus di servizio per abilitare l'autorizzazione del client per l'accesso all'entità.

Il supporto per l'autenticazione tramite ACS per il bus di servizio è incluso in Azure .NET SDK 2.0 e versioni successive. Questa autenticazione include il supporto per un oggetto [SharedSecretTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedsecrettokenprovider.aspx). Tutte le API che accettano una stringa di connessione come parametro includono il supporto per le stringhe di connessione ACS.

## Passaggi successivi

Per altre informazioni sulla firma di accesso condiviso, vedere [Autenticazione della firma di accesso condiviso con il bus di servizio](service-bus-shared-access-signature-authentication.md).

Per una panoramica generale della firma di accesso condiviso nel bus di servizio, vedere [Firme di accesso condiviso](service-bus-sas-overview.md).

Per altre informazioni sui token ACS, vedere [Procedura: Richiedere un token da ACS tramite il protocollo OAuth WRAP](https://msdn.microsoft.com/library/hh674475.aspx).

<!---HONumber=AcomDC_0622_2016-->