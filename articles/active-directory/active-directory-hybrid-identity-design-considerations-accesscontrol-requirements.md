
<properties
	pageTitle="Considerazioni di progettazione dell'identità ibrida di Azure Active Directory - Determinare i requisiti di controllo di accesso | Microsoft Azure"
	description="Illustra gli elementi fondamentali dell'identità e specifica i requisiti di accesso alle risorse previsti per gli utenti in un ambiente ibrido."
	documentationCenter=""
	services="active-directory"
	authors="yuridio"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="11/11/2015"
	ms.author="yuridio"/>

# Determinare i requisiti di controllo di accesso per una soluzione di identità ibrida
Durante la progettazione di una soluzione di identità ibrida, le aziende hanno la possibilità di valutare i requisiti di accesso per le risorse che prevedono di rendere disponibili agli utenti. L'accesso ai dati coinvolge tutti i quattro pilastri dell'identità, ovvero:

- Amministrazione
- Autenticazione
- Authorization
- Controllo

La sezione seguente descrive nel dettaglio gli aspetti relativi all'autenticazione e all'autorizzazione, mentre i concetti inerenti alla gestione e al controllo verranno discussi nell'ambito del ciclo di vita delle identità ibride. Per altre informazioni su queste funzionalità, leggere l'articolo sulle modalità per [determinare le attività di gestione delle identità ibride](active-directory-hybrid-identity-design-considerations-hybridId-management-tasks.md).

>[AZURE.NOTE]Per altre informazioni su ciascuno dei pilastri, leggere l'articolo relativo ai [quattro pilastri dell'identità e alla gestione delle identità nell'era dell'IT ibrido](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx).

## Autenticazione e autorizzazione
Sono disponibili vari scenari di autenticazione e autorizzazione, ciascuno dei quali presenta requisiti specifici a cui deve attenersi la soluzione di identità ibrida che l'azienda si appresta ad adottare. Gli scenari che prevedono comunicazioni B2B (Business-to-Business), ad esempio, comportano per gli amministratori IT la necessità di verificare che il metodo di autenticazione e autorizzazione usato dall'azienda consenta la comunicazione con i partner commerciali. Durante la definizione dei requisiti di autenticazione e autorizzazione, accertarsi quindi che venga fornita una risposta alle domande seguenti:

- L'azienda intende autenticare e autorizzare solo gli utenti presenti nel proprio sistema di gestione delle identità?
 - È già stato definito un piano per gli scenari B2B?
 - In caso affermativo, sono già stati stabiliti i protocolli (SAML, OAuth, Kerberos, token o certificati) da usare per le connessioni tra le aziende?
- La soluzione di identità ibrida che si intende adottare supporta tali protocolli?

Altri aspetti importanti di cui tener conto sono la posizione dell'archivio di autenticazione che verrà usato dagli utenti e dai partner e il modello amministrativo adottato. Il modello può essere centralizzato, in cui le credenziali utente, i criteri e le attività di amministrazione vengono gestite centralmente in locale o nel cloud, oppure ibrido, in cui le credenziali utente, i criteri e le attività di amministrazione vengono gestite centralmente in locale e quindi replicate nel cloud.

Il modello adottato da ogni azienda dipenderà dalle rispettive esigenze. È importante quindi rispondere alle domande seguenti per capire dove risiederà il sistema di gestione delle identità e scegliere la modalità amministrativa da usare.

- L'azienda dispone già di un sistema locale per la gestione delle identità?
 - In caso affermativo, prevede di mantenerlo?
 - Esistono norme o requisiti di conformità a cui deve attenersi l'azienda e in cui si stabilisce dove deve risiedere il sistema di gestione delle identità?
- L'azienda usa un accesso di tipo Single Sign-On per le app installate in locale o nel cloud?
 - In caso affermativo, l'adozione di un modello di identità ibrida può avere ripercussioni su questo processo?

## Controllo dell’accesso
Sebbene l'autenticazione e l'autorizzazione costituiscano elementi essenziali per consentire l'accesso ai dati aziendali tramite la convalida degli utenti, è altrettanto importante controllare il livello di accesso assegnato agli utenti, nonché il livello di accesso di cui dispongono gli amministratori sulle risorse che devono gestire. Per questo motivo, le soluzioni di identità ibrida devono essere in grado di fornire un accesso granulare alle risorse, funzioni di delega e opzioni di controllo degli accessi in base al ruolo. Assicurarsi che venga fornita una risposta alle domande seguenti relative al controllo di accesso:

- Nell'azienda è presente più di utente con privilegi elevati per la gestione del sistema di identità?
 - In caso affermativo, è necessario che ogni utente disponga dello stesso livello di accesso?
- È possibile che sia necessario delegare l'accesso ad altri utenti per la gestione di risorse specifiche?
 - In caso affermativo, con quale frequenza si verifica questa situazione? 
- L'azienda prevede di integrare funzionalità di controllo di accesso tra risorse locali e nel cloud?
- L'azienda potrebbe limitare l'accesso alle risorse in base a determinate condizioni?
- L'azienda si avvale di una o più applicazioni che richiedono un controllo di accesso personalizzato per alcune risorse?
 - In caso affermativo, dove si trovano tali app (in locale o nel cloud)?
 - In caso affermativo, dove si trovano le risorse interessate (in locale o nel cloud)?
 
>[AZURE.NOTE]Accertarsi di prendere nota di ogni risposta e comprendere la logica che ne sta alla base. L'articolo sulla [definizione della strategia di protezione dei dati](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) esamina le opzioni disponibili con i relativi vantaggi e svantaggi. Rispondendo a queste domande sarà più facile scegliere l'opzione migliore in base alle specifiche esigenze aziendali.

## Passaggi successivi

[Determinare i requisiti di risposta agli eventi imprevisti](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## Vedere anche
[Panoramica delle considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=Nov15_HO4-->