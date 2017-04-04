---
title: Informazioni sull&quot;architettura di Azure Active Directory | Microsoft Docs
description: Illustra il significato di tenant di Azure AD e come gestire Azure con Azure Active Directory
services: active-directory
documentationcenter: 
author: markvi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 5c60fa737c0133482af8b653f795bf9086c39969
ms.lasthandoff: 03/28/2017


---
# <a name="understand-azure-active-directory-architecture"></a>Informazioni sull'architettura di Azure Active Directory
Azure Active Directory (Azure AD) consente di gestire in modo sicuro l'accesso ai servizi e alle risorse di Azure per gli utenti. In Azure AD è inclusa una suite completa di funzionalità di gestione delle identità. Per informazioni sulle funzionalità di Azure AD, vedere [Informazioni su Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis).

Con Azure AD, è possibile creare e gestire utenti e gruppi e abilitare le autorizzazioni per consentire o negare l'accesso alle risorse aziendali. Per informazioni sulla gestione delle identità, vedere [Nozioni fondamentali sulla gestione delle identità di Azure](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity).

## <a name="azure-ad-architecture"></a>Architettura di Azure AD
L'architettura distribuita geograficamente di Azure AD combina funzionalità complete di monitoraggio, reindirizzamento automatizzato, failover e ripristino che consentono di offrire ai clienti disponibilità e prestazioni a livello di organizzazione.

In questo articolo vengono illustrati gli elementi dell'architettura seguenti:
 *    Progettazione dell'architettura del servizio
 *    Scalabilità 
 *    Disponibilità continua
 *    Data center

### <a name="service-architecture-design"></a>Progettazione dell'architettura del servizio
L'approccio più diffuso per la compilazione di un sistema scalabile, con disponibilità elevata e ad alto contenuto di dati consiste nell'usare blocchi predefiniti o unità di scala indipendenti per il livello dati di Azure AD. Le unità di scala sono denominate *partizioni*. 

Il livello dati ha diversi servizi front-end che forniscono funzionalità di lettura/scrittura. Il diagramma seguente illustra come i componenti di una partizione di directory singola vengono suddivisi in data center distribuiti geograficamente. 

  ![Partizioni di directory singola](./media/active-directory-architecture/active-directory-architecture.png)

I componenti dell'architettura di Azure AD includono una replica primaria e una replica secondaria.

**Replica primaria**

La *replica primaria* riceve tutte le *operazioni di scrittura* per la partizione a cui appartiene. Ogni operazione di scrittura viene immediatamente replicata in una replica secondaria in un data center diverso prima di restituire un'operazione riuscita al chiamante, assicurando così la durabilità con ridondanza geografica delle operazioni di scrittura.

**Repliche secondarie**

Tutte le *operazioni di lettura* delle directory vengono gestite dalle *repliche secondarie*, che si trovano in data center situati fisicamente in aree geografiche diverse. Esistono molte repliche secondarie perché i dati vengono replicati in modo asincrono. Le operazioni di lettura delle directory, ad esempio le richieste di autenticazione, vengono gestite da data center vicini ai clienti. Le repliche secondarie sono responsabili della scalabilità delle operazioni di lettura.

### <a name="scalability"></a>Scalabilità

La scalabilità è la possibilità per un servizio di espandersi per poter soddisfare le crescenti richieste in termini di prestazioni. La scalabilità delle operazioni di scrittura richiede il partizionamento dei dati. La scalabilità delle operazioni di lettura richiede la replica dei dati da una partizione a più repliche secondarie distribuite in tutto il mondo.

Le richieste provenienti dalle applicazioni directory vengono in genere instradate al data center fisicamente più vicino. Le operazioni di scrittura vengono reindirizzate in modo trasparente alla replica primaria per garantire la coerenza delle operazioni di lettura/scrittura. Le repliche secondarie estendono considerevolmente la scalabilità delle partizioni perché in genere le directory gestiscono per lo più operazioni di lettura.

Le applicazioni directory si connettono ai data center più vicini, migliorando le prestazioni e rendendo quindi possibile l'aumento del numero di istanze. Poiché una partizione di directory può avere più repliche secondarie, le repliche secondarie possono essere poste più vicino ai client delle directory. Solo i componenti del servizio directory interni con un numero elevato di operazioni di scrittura specificano come destinazione direttamente la replica primaria attiva.

### <a name="continuous-availability"></a>Disponibilità continua

La disponibilità (o tempo di attività) definisce la possibilità per un sistema di funzionare senza interruzioni. La disponibilità elevata di Azure AD si basa sul fatto che i servizi possono spostare rapidamente il traffico tra più data center distribuiti geograficamente. Ogni data center è indipendente e ciò abilita le modalità di errore con annullamento della correlazione.

La progettazione delle partizioni di Azure AD è più semplice rispetto alla progettazione di AD aziendale, il che è importante per aumentare le prestazioni del sistema. È stato adottato uno schema master singolo che include un processo di failover della replica primaria deterministico e orchestrato con attenzione.

**Tolleranza di errore**

Un sistema è più disponibile se è a tolleranza di errore di hardware, di rete e di software. Per ogni partizione nella directory, esiste una replica master a disponibilità elevata: la replica primaria. In questa replica vengono eseguite solo operazioni di scrittura nella partizione. Questa replica viene monitorata in modo continuo e attento e le operazioni di scrittura possono essere immediatamente spostate in un'altra replica, che diventa la nuova replica primaria, se viene rilevato un errore. Durante il failover, è possibile che si verifichi una perdita di disponibilità in scrittura, in genere per 1-2 minuti. La disponibilità in lettura non è invece interessata durante questo intervallo di tempo.

Le operazioni di lettura (che sono più numerose di quelle di scrittura di diversi ordini di grandezza) passano solo alle repliche secondarie. Poiché le repliche secondarie sono idempotenti, la perdita di una replica in una determinata partizione viene facilmente compensata indirizzando le operazioni di lettura a un'altra replica, in genere nello stesso data center.

**Durabilità dei dati**

Il commit durevole delle operazioni di scrittura viene eseguito in almeno due data center prima che ne venga accertata la ricezione. A questo scopo, viene prima eseguito il commit dell'operazione di scrittura nella replica primaria e immediatamente dopo l'operazione di scrittura viene replicata in almeno un altro data center. In questo modo, una possibile perdita irreversibile del data center che ospita la replica primaria non comporta una perdita di dati.

Azure AD mantiene un [obiettivo del tempo di ripristino (RTO, Recovery Time Objective)](https://en.wikipedia.org/wiki/Recovery_time_objective) pari a zero per il rilascio di token e le operazioni di lettura delle directory e un valore RTO di alcuni minuti (circa 5) per le operazioni di scrittura delle directory. Viene anche mantenuto un [obiettivo del punto di ripristino (RPO, Recovery Point Objective)](https://en.wikipedia.org/wiki/Recovery_point_objective) pari a zero e non si verificheranno perdite di dati in caso di failover.

### <a name="data-centers"></a>Data center

Le repliche di Azure AD vengono archiviate in data center situati in tutto il mondo. Per altre informazioni, vedere [Data center Azure](https://azure.microsoft.com/en-us/overview/datacenters).

Azure AD opera nei data center con le caratteristiche seguenti:

 * Autenticazione, Graph e gli altri servizi di AD si trovano dietro il servizio gateway. Il gateway gestisce il bilanciamento del carico di questi servizi. Verrà eseguito il failover automatico se viene rilevato che server non integri usano probe di integrità transazionali. In base a questi probe di integrità, il gateway instrada in modo dinamico il traffico ai data center integri.
 * Per le *operazioni di lettura*, la directory ha repliche secondarie e servizi front-end corrispondenti in una configurazione attiva/attiva che operano in più data center. In caso di errore di un intero data center, il traffico verrà automaticamente instradato a un altro data center.
 *    Per le *operazioni di scrittura*, la directory eseguirà il failover della replica primaria (master) nei data center tramite procedure di failover pianificate (la nuova replica primaria viene sincronizzata con quella precedente) o di emergenza. La durabilità dei dati si ottiene replicando i commit in almeno due data center.

**Coerenza dei dati**

Il modello della directory si basa sulla coerenza finale. Un problema tipico dei sistemi distribuiti che eseguono la replica asincrona è che i dati restituiti da una "particolare" replica potrebbero non essere aggiornati. 

Azure AD offre la coerenza in lettura/scrittura per le applicazioni che specificano come destinazione una replica secondaria instradandone le operazioni di scrittura alla replica primaria ed effettuando di nuovo il pull sincrono delle operazioni di scrittura alla replica secondaria.

Per le operazioni di scrittura delle applicazioni che usano l'API Graph di Azure AD non è necessario mantenere l'affinità con la replica di una directory per la coerenza in lettura/scrittura. Il servizio Graph di Azure AD gestisce una sessione logica, che è affine a una replica secondaria usata per le operazioni di lettura. L'affinità viene acquisita in un "token di replica" che il servizio Graph memorizza usando una cache distribuita. Questo token viene quindi usato per le operazioni successive nella stessa sessione logica. 

 >[!NOTE]
 >Le operazioni di scrittura vengono immediatamente replicate nella replica secondaria in cui sono state rilasciate le operazioni di lettura della sessione logica.
 >

**Protezione dei backup**

La directory implementa le eliminazioni temporanee, invece delle eliminazioni definitive, per poter ripristinare facilmente utenti e tenant in caso di eliminazione accidentale da parte di un cliente. Se l'amministratore tenant elimina accidentalmente un utente, può facilmente annullare l'operazione e ripristinare l'utente eliminato. 

Azure AD implementa backup giornalieri di tutti i dati e quindi può ripristinare autorevolmente i dati in caso di eliminazioni logiche o danneggiamenti. Il livello dati usa i codici di correzione degli errori e può quindi verificare la presenza di errori e correggere automaticamente determinati tipi di errori del disco.

**Metriche e monitoraggio**

L'esecuzione di un servizio a disponibilità elevata richiede funzionalità di monitoraggio e metriche di alto livello. Azure AD analizza e segnala ininterrottamente le principali metriche sull'integrità del servizio e i criteri di successo per ogni servizio. Vengono continuamente sviluppate e ottimizzate metriche e funzionalità di monitoraggio e avviso per ogni scenario, in ogni singolo servizio di Azure AD e nell'insieme di tutti i servizi.

Se un servizio di Azure AD non funziona come previsto, si interviene immediatamente per ripristinare la funzionalità il prima possibile. La metrica più importante di cui Azure AD tiene traccia è la velocità con cui è possibile rilevare e attenuare un problema del cliente o del sito Web. Vengono effettuati considerevoli investimenti nelle funzionalità di monitoraggio e avviso per ridurre al minimo i tempi di rilevamento (obiettivo per il tempo di rilevamento: < 5 minuti) e nella conformità operativa per ridurre al minimo i tempi di attenuazione (obiettivo per il tempo di attenuazione: < 30 minuti).

**Operazioni sicure**

Vengono usati controlli operativi, ad esempio Multi-Factor Authentication (MFA), per tutte le operazioni, oltre al controllo di tutte le operazioni. Viene anche usato un sistema di elevazione JIT per concedere l'accesso temporaneo necessario per eventuali attività operative su richiesta su base regolare. Per altre informazioni, vedere [Il cloud attendibile](https://azure.microsoft.com/en-us/support/trust-center).

## <a name="next-steps"></a>Passaggi successivi
[Guida per gli sviluppatori di Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide)


