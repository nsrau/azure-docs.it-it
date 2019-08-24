---
title: Autorizzare l'accesso con una firma di accesso condiviso in hub eventi di Azure
description: Questo articolo fornisce informazioni su come autorizzare l'accesso alle risorse di hub eventi di Azure usando le firme di accesso condiviso (SAS).
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: bdb1896f8a40c6de21ae76b536bfccec316341cd
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992796"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Autorizzazione dell'accesso alle risorse di hub eventi tramite firme di accesso condiviso
Una firma di accesso condiviso fornisce un modo per concedere l'accesso limitato alle risorse nello spazio dei nomi di hub eventi. SAS protegge l'accesso alle risorse di hub eventi in base alle regole di autorizzazione. Queste regole vengono configurate in uno spazio dei nomi o in un'entità (hub eventi o argomento). Questo articolo fornisce una panoramica del modello SAS ed esamina le procedure consigliate per la firma di accesso condiviso.

## <a name="what-are-shared-access-signatures"></a>Che cosa sono le firme di accesso condiviso?
Una firma di accesso condiviso (SAS) fornisce accesso delegato alle risorse di hub eventi in base alle regole di autorizzazione. Una regola di autorizzazione ha un nome, è associata a diritti specifici e include una coppia di chiavi di crittografia. Usare il nome e la chiave della regola tramite i client di hub eventi o nel codice per generare i token di firma di accesso condiviso. Un client può quindi passare il token a hub eventi per dimostrare l'autorizzazione per l'operazione richiesta.

SAS è un meccanismo di autorizzazione basato sulle attestazioni che usa token semplici. Quando si usano le firme di accesso, le chiavi non vengono mai passate durante il transito. Le chiavi vengono usate per firmare crittograficamente informazioni che possono essere verificate in un secondo momento dal servizio. L'uso delle firme di accesso condiviso è paragonabile a quello della combinazione di nome utente e password in cui il client entra immediatamente in possesso di un nome di regola di autorizzazione e una chiave corrispondente. La firma di accesso condiviso può essere usata in modo analogo a un modello di sicurezza federata, in cui il client riceve un token di accesso con firma temporale e limitato da un servizio token di sicurezza senza mai entrare in possesso della chiave di firma.

> [!NOTE]
> Hub eventi di Azure supporta l'autorizzazione alle risorse di hub eventi usando Azure Active Directory (Azure AD). L'autorizzazione di utenti o applicazioni che usano il token OAuth 2,0 restituito da Azure AD offre sicurezza e facilità d'uso superiori rispetto alle firme di accesso condiviso (SAS). Con Azure AD, non è necessario archiviare i token nel codice e rischiare potenziali vulnerabilità della sicurezza.
>
> Microsoft consiglia di usare Azure AD con le applicazioni di hub eventi di Azure, quando possibile. Per altre informazioni, vedere [autorizzare l'accesso alla risorsa di hub eventi di Azure usando Azure Active Directory](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> I token di firma di accesso condiviso (SAS) sono fondamentali per proteggere le risorse. Garantendo la granularità, SAS concede ai client l'accesso alle risorse di hub eventi. Non devono essere condivisi pubblicamente. Quando si condivide, se necessario per motivi di risoluzione dei problemi, è consigliabile usare una versione ridotta di tutti i file di log o eliminare i token SAS (se presenti) dai file di log e assicurarsi che le schermate non contengano le informazioni di firma di accesso condiviso.

## <a name="shared-access-authorization-policies"></a>Criteri di autorizzazione dell'accesso condiviso
Ogni spazio dei nomi di hub eventi e ogni entità di hub eventi (un'istanza dell'hub eventi o un argomento Kafka) dispone di un criterio di autorizzazione di accesso condiviso costituito da regole. I criteri a livello di spazio dei nomi si applicano a tutte le entità in esso incluse, indipendentemente dalle specifiche configurazioni dei criteri.
Per ogni regola dei criteri di autorizzazione si scelgono tre tipi di informazioni: nome, ambito e diritti. Il nome è un nome univoco in tale ambito. L'ambito è l'URI della risorsa in questione. Per uno spazio dei nomi di hub eventi, l'ambito è il nome di dominio completo (FQDN) `https://<yournamespace>.servicebus.windows.net/`, ad esempio.

I diritti forniti dalla regola dei criteri possono essere una combinazione di:
- **Send** : fornisce il diritto di inviare messaggi all'entità
- **Listen** : fornisce il diritto di ascoltare o ricevere l'entità
- **Gestisci** : fornisce il diritto di gestire la topologia dello spazio dei nomi, inclusa la creazione e l'eliminazione di entità

> [!NOTE]
> Il diritto di **gestione** include i diritti di **invio** e **ascolto** .

Uno spazio dei nomi o un criterio di entità può avere fino a 12 regole di autorizzazione per l'accesso condiviso, fornendo spazio per i tre set di regole, ciascuno dei quali copre i diritti di base e la combinazione di invio e ascolto. Questo limite sottolinea che l'archivio criteri di firma di accesso condiviso non è destinato a essere un archivio di account utente o del servizio. Se l'applicazione deve concedere l'accesso alle risorse di hub eventi in base alle identità utente o del servizio, deve implementare un servizio token di sicurezza che rilascia token di firma di accesso condiviso dopo un'autenticazione e un controllo di accesso.

A una regola di autorizzazione vengono assegnate una **chiave primaria** e una **chiave secondaria**. Queste chiavi sono chiavi crittograficamente complesse. Non perderli o perderli. Saranno sempre disponibili nella portale di Azure. È possibile utilizzare una delle chiavi generate ed è possibile rigenerarle in qualsiasi momento. Se si rigenera o si modifica una chiave nel criterio, tutti i token emessi in precedenza in base a tale chiave diventano immediatamente non validi. Le connessioni in corso create in base a tali token continueranno invece a funzionare fino alla scadenza del token.

Quando si crea uno spazio dei nomi di hub eventi, viene creata automaticamente una regola di criteri denominata **RootManageSharedAccessKey** per lo spazio dei nomi. Questo criterio dispone delle autorizzazioni di **gestione** per l'intero spazio dei nomi. È consigliabile considerare questa regola come un account radice amministrativo e non usarla nell'applicazione. È possibile creare regole di criteri aggiuntive nella scheda **Configura** per lo spazio dei nomi nel portale, tramite PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="best-practices-when-using-sas"></a>Procedure consigliate per l'uso di SAS
Quando si utilizzano le firme di accesso condiviso nell'applicazione, è necessario essere consapevoli di due rischi potenziali:

- Se una firma di accesso condiviso viene persa, può essere usata da chiunque lo ottenga, che può potenzialmente compromettere le risorse di hub eventi.
- Se una firma di accesso condiviso fornita a un'applicazione client scade e l'applicazione non è in grado di recuperare una nuova firma di accesso condiviso dal servizio, le funzionalità dell'applicazione potrebbero essere ostacolate.

Per mitigare questi rischi, è consigliabile attenersi ai consigli seguenti relativi all'utilizzo di firme di accesso condiviso:

- Chiedere **ai client di rinnovare automaticamente la firma di**accesso condiviso, se necessario: I client devono rinnovare la firma di accesso condiviso prima della scadenza, per attendere i tentativi se il servizio che fornisce la firma di accesso condiviso non è disponibile. Se la firma di accesso condiviso è concepita per essere usata per un numero ridotto di operazioni immediate e di breve durata che dovrebbero essere completate entro il periodo di scadenza, potrebbe non essere necessario perché la firma di accesso condiviso non è prevista per il rinnovo. Se tuttavia si dispone di client che effettuano normalmente richieste tramite la firma di accesso condiviso, è necessario considerare la possibilità che la firma scada. La considerazione chiave consiste nel bilanciare la necessità che la firma di accesso condiviso sia di breve durata (come indicato in precedenza) con la necessità di garantire che il client stia richiedendo un rinnovo tempestivo (per evitare l'intralcio dovuto alla scadenza della firma di accesso condiviso prima di un rinnovo positivo).
- **Prestare attenzione all'ora di inizio della firma di**accesso condiviso: Se si imposta l'ora di inizio per la firma di accesso condiviso su **ora**, a causa dello sfasamento di clock (differenze nell'ora corrente in base a computer diversi), è possibile che gli errori vengano osservati in modo intermittente per i primi minuti. In generale, impostare l'ora di inizio ad almeno 15 minuti prima. In alternativa, non impostare alcun valore, in modo da renderlo immediatamente valido in tutti i casi. Lo stesso vale in genere anche per l'ora di scadenza. Tenere presente che è possibile osservare fino a 15 minuti di sfasamento dell'orologio in una delle due direzioni di qualsiasi richiesta. 
- **Essere specifici della risorsa a cui accedere**: Una procedura di sicurezza consigliata consiste nel fornire all'utente i privilegi minimi necessari. Se un utente necessita solo dell'accesso in lettura a una singola entità, concedere solo tale tipo di accesso per tale entità e non l'accesso in lettura/scrittura/eliminazione per tutte le entità. Consente inoltre di ridurre il danno se una firma di accesso condiviso viene compromessa perché la firma di accesso condiviso ha meno energia nelle mani di un utente malintenzionato.
- **Non usare sempre SAS**: A volte i rischi associati a una particolare operazione sull'hub eventi superano i vantaggi della firma di accesso condiviso. Per queste operazioni, creare un servizio di livello intermedio che scrive nell'hub eventi dopo la convalida, l'autenticazione e il controllo delle regole business.
- **Usare sempre https**: Usare sempre HTTPS per creare o distribuire una firma di accesso condiviso. Se una firma di accesso condiviso viene passata tramite HTTP e intercettata, un utente malintenzionato che esegue un attacco man-in-the-Middle può leggere la firma di accesso condiviso e quindi usarla come l'utente previsto, potenzialmente compromettendo i dati sensibili o consentendo il danneggiamento dei dati da parte di utenti malintenzionati.

## <a name="conclusion"></a>Conclusione
Le firme di accesso condiviso sono utili per fornire autorizzazioni limitate alle risorse di hub eventi ai client. Sono parte essenziale del modello di sicurezza per qualsiasi applicazione che usa hub eventi di Azure. Se si seguono le procedure consigliate elencate in questo articolo, è possibile usare la firma di accesso condiviso per offrire una maggiore flessibilità di accesso alle risorse, senza compromettere la sicurezza dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli correlati seguenti: 

- [Autenticare le richieste a hub eventi di Azure da un'applicazione usando Azure Active Directory](authenticate-application.md)
- [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse di hub eventi](authenticate-managed-identity.md)
- [Autenticare le richieste a hub eventi di Azure usando le firme di accesso condiviso](authenticate-shared-access-signature.md)
- [Autorizzare l'accesso alle risorse di hub eventi usando Azure Active Directory](authorize-access-azure-active-directory.md)


