---
title: Autorizzare l'accesso con una firma di accesso condiviso negli hub eventi di AzureAuthorize access with a shared access signature in Azure Event Hubs
description: Questo articolo fornisce informazioni sull'autorizzazione dell'accesso alle risorse di Hub eventi di Azure tramite firme di accesso condiviso.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: bdb1896f8a40c6de21ae76b536bfccec316341cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992796"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Autorizzazione dell'accesso alle risorse di Hub eventi tramite firme di accesso condiviso
Una firma di accesso condiviso offre un modo per concedere un accesso limitato alle risorse nello spazio dei nomi degli hub eventi. SAS protegge l'accesso alle risorse di Hub eventi in base alle regole di autorizzazione. Queste regole sono configurate in uno spazio dei nomi o in un'entità (hub eventi o argomento). Questo articolo fornisce una panoramica del modello di sAS ed esamina le procedure consigliate per la sAS.

## <a name="what-are-shared-access-signatures"></a>Che cosa sono le firme di accesso condiviso?
Una firma di accesso condiviso fornisce l'accesso delegato alle risorse di Hub eventi in base alle regole di autorizzazione. Una regola di autorizzazione ha un nome, è associata a diritti specifici e include una coppia di chiavi di crittografia. Il nome e la chiave della regola vengono utilizzati dai client Hub eventi o nel codice per generare token di firma di accesso condiviso. Un client può quindi passare il token agli hub eventi per dimostrare l'autorizzazione per l'operazione richiesta.

SAS è un meccanismo di autorizzazione basato sulle attestazioni che usa token semplici. Quando si usano le firme di accesso, le chiavi non vengono mai passate durante il transito. Le chiavi vengono usate per firmare crittograficamente informazioni che possono essere verificate in un secondo momento dal servizio. L'uso delle firme di accesso condiviso è paragonabile a quello della combinazione di nome utente e password in cui il client entra immediatamente in possesso di un nome di regola di autorizzazione e una chiave corrispondente. La firma di accesso condiviso può essere usata in modo simile a un modello di sicurezza federato, in cui il client riceve un token di accesso firmato e limitato nel tempo da un servizio token di sicurezza senza mai entrare in possesso della chiave di firma.

> [!NOTE]
> Hub eventi di Azure supporta l'autorizzazione alle risorse di Hub eventi tramite Azure Active Directory (Azure AD). L'autorizzazione di utenti o applicazioni con token OAuth 2.0 restituito da Azure AD offre una sicurezza e un utilizzo superiori rispetto alle firme di accesso condiviso. Con Azure AD non è necessario archiviare i token nel codice e rischiare potenziali vulnerabilità della sicurezza.
>
> Microsoft consiglia di usare Azure AD con le applicazioni Hub eventi di Azure quando possibile. Per altre informazioni, vedere [Autorizzare l'accesso alla risorsa Hub eventi](authorize-access-azure-active-directory.md)di Azure usando Azure Active Directory.For more information, see Authorize access to Azure Event Hubs resource using Azure Active Directory .

> [!IMPORTANT]
> I token di firma di accesso condiviso sono fondamentali per proteggere le risorse. Fornendo granularità, SAS concede ai client l'accesso alle risorse di Hub eventi. Non devono essere condivisi pubblicamente. Quando si condivide, se necessario per motivi di risoluzione dei problemi, prendere in considerazione l'utilizzo di una versione ridotta di qualsiasi file di log o eliminare i token di firma di accesso condiviso (se presente) dai file di log e assicurarsi che le schermate non contengano le informazioni sulla firma di accesso condiviso.

## <a name="shared-access-authorization-policies"></a>Criteri di autorizzazione di accesso condivisoShared access authorization policies
Ogni spazio dei nomi Hub eventi e ogni entità Hub eventi (un'istanza dell'hub eventi o un argomento Kafka) dispone di un criterio di autorizzazione di accesso condiviso costituito da regole. I criteri a livello di spazio dei nomi si applicano a tutte le entità in esso incluse, indipendentemente dalle specifiche configurazioni dei criteri.
Per ogni regola del criterio di autorizzazione si stabiliscono tre informazioni: nome, ambito e diritti. Il nome è un nome univoco in tale ambito. L'ambito è l'URI della risorsa in questione. Per uno spazio dei nomi Hub eventi, l'ambito è `https://<yournamespace>.servicebus.windows.net/`il nome di dominio completo (FQDN), ad esempio .

I diritti forniti dalla regola dei criteri possono essere una combinazione di:
- **Invia:** concede il diritto di inviare messaggi all'entità
- **Ascolta** – Dà il diritto di ascoltare o ricevere all'entità
- **Gestisci:** consente di gestire la topologia dello spazio dei nomi, inclusa la creazione e l'eliminazione di entità

> [!NOTE]
> Il diritto **Gestione** include i diritti **di invio** e **ascolto.**

Uno spazio dei nomi o un criterio di entità può contenere fino a 12 regole di autorizzazione di accesso condiviso, fornendo spazio per i tre set di regole, ognuno dei quali copre i diritti di base e la combinazione di Invia e ascolta. Questo limite sottolinea che l'archivio criteri di accesso livello di accesso sole non deve essere un archivio di account utente o di servizio. Se l'applicazione deve concedere l'accesso alle risorse di Hub eventi in base alle identità dell'utente o del servizio, deve implementare un servizio token di sicurezza che emette token di firma di accesso condiviso dopo un controllo di autenticazione e accesso.

A una regola di autorizzazione vengono assegnate una **chiave primaria** e una **chiave secondaria.** Queste chiavi sono chiavi crittograficamente forti. Non perderli o perderli. Saranno sempre disponibili nel portale di Azure.They'll always be available in the Azure portal. È possibile utilizzare una delle chiavi generate ed è possibile rigenerarle in qualsiasi momento. Se si rigenera o si modifica una chiave nel criterio, tutti i token emessi in precedenza in base a tale chiave diventano immediatamente non validi. Le connessioni in corso create in base a tali token continueranno invece a funzionare fino alla scadenza del token.

Quando si crea uno spazio dei nomi Hub eventi, viene creata automaticamente una regola dei criteri denominata **RootManageSharedAccessKey** per lo spazio dei nomi. Questo criterio dispone delle autorizzazioni di **gestione** per l'intero spazio dei nomi. È consigliabile considerare questa regola come un account radice amministrativo e non usarla nell'applicazione. È possibile creare regole dei criteri aggiuntive nella scheda Configura per lo spazio dei nomi nel portale, tramite PowerShell o l'interfaccia della riga di comando di Azure.You can create additional policy rules in the **Configure** tab for the namespace in the portal, via PowerShell or Azure CLI.

## <a name="best-practices-when-using-sas"></a>Procedure consigliate per l'uso di SAS
Quando si utilizzano le firme di accesso condiviso nell'applicazione, è necessario essere consapevoli di due rischi potenziali:

- Se una sAS è trapelato, può essere utilizzata da chiunque la ottenga, il che può compromettere le risorse di Hub eventi.
- Se una configurazione di accesso base fornita a un'applicazione client scade e l'applicazione non è in grado di recuperare una nuova sAS dal servizio, le funzionalità dell'applicazione potrebbero essere ostacolate.

Per mitigare questi rischi, è consigliabile attenersi ai consigli seguenti relativi all'utilizzo di firme di accesso condiviso:

- **Chiedere ai client di rinnovare automaticamente la configurazione di accesso locale, se necessario:** i client devono rinnovare la configurazione di accesso locale ben prima della scadenza, per consentire ai tentativi se il servizio che fornisce la provider di servizi di accesso locale non è disponibile. Se la sAS deve essere usata per un numero ridotto di operazioni immediate di breve durata che si prevede vengano completate entro il periodo di scadenza, potrebbe non essere necessario poiché non è previsto il rinnovo della sAS. Se tuttavia si dispone di client che effettuano normalmente richieste tramite la firma di accesso condiviso, è necessario considerare la possibilità che la firma scada. La considerazione chiave consiste nel bilanciare la necessità che la SAS sia di breve durata (come indicato in precedenza) con la necessità di garantire che il client richieda il rinnovo abbastanza presto (per evitare interruzioni a causa della scadenza della sAS prima di un rinnovo riuscito).
- **Prestare attenzione con l'ora**di inizio della chiamata in stato di esecuzione dati : Se si imposta l'ora di inizio per la chiamata in scadenza su **ora**, quindi a causa dell'inclinazione dell'orologio (differenze nell'ora corrente in base a computer diversi), gli errori possono essere osservati in modo intermittente per i primi minuti. In generale, impostare l'ora di inizio ad almeno 15 minuti prima. Oppure, non impostarlo affatto, che lo renderà valido immediatamente in tutti i casi. Lo stesso vale generalmente anche per il tempo di scadenza. Ricorda che puoi osservare fino a 15 minuti di inclinazione dell'orologio in entrambe le direzioni su qualsiasi richiesta. 
- **Sii specifico con la risorsa a cui accedere:** una procedura consigliata per la sicurezza consiste nel fornire all'utente i privilegi minimi necessari. Se un utente necessita solo dell'accesso in lettura a una singola entità, concedere solo tale tipo di accesso per tale entità e non l'accesso in lettura/scrittura/eliminazione per tutte le entità. Aiuta anche a ridurre il danno se una SAS è compromessa perché la SAS ha meno potenza nelle mani di un utente malintenzionato.
- **Non usare sempre la sAS:** a volte i rischi associati a una particolare operazione nei tuoi Hub eventi superano i vantaggi della sAS. Per tali operazioni, creare un servizio di livello intermedio che scrive negli hub eventi dopo la convalida, l'autenticazione e il controllo delle regole business.
- **Usa sempre HTTPs:** usare sempre Https per creare o distribuire una sAS. Se una sAS viene passata su HTTP e intercettata, un utente malintenzionato che esegue una connessione man-in-the-middle è in grado di leggere la sAS e quindi utilizzarla come l'utente previsto potrebbe avere, potenzialmente compromettere i dati sensibili o consentire il danneggiamento dei dati da parte dell'utente malintenzionato.

## <a name="conclusion"></a>Conclusioni
Le firme di accesso alla condivisione sono utili per fornire ai client autorizzazioni limitate per le risorse di Hub eventi. Sono parte fondamentale del modello di sicurezza per qualsiasi applicazione che usa Hub eventi di Azure.They are vital part of the security model for any application using Azure Event Hubs. Se si seguono le procedure consigliate elencate in questo articolo, è possibile usare la funzionalità di gestione delle chiavi per fornire una maggiore flessibilità di accesso alle risorse, senza compromettere la sicurezza dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
Vedere i seguenti articoli correlati: 

- [Autenticare le richieste agli hub eventi di Azure da un'applicazione tramite Azure Active DirectoryAuthenticate requests to Azure Event Hubs from an application using Azure Active Directory](authenticate-application.md)
- [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse degli hub eventiAuthenticate a managed identity with Azure Active Directory to access Event Hubs Resources](authenticate-managed-identity.md)
- [Autenticare le richieste agli hub eventi di Azure usando le firme di accesso condivisoAuthenticate requests to Azure Event Hubs using Shared Access Signatures](authenticate-shared-access-signature.md)
- [Autorizzare l'accesso alle risorse di Hub eventi usando Azure Active DirectoryAuthorize access to Event Hubs resources using Azure Active Directory](authorize-access-azure-active-directory.md)


