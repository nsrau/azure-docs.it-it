---
title: Panoramica dell'architettura - Azure Active Directory | Microsoft Docs
description: Informazioni sul tenant di Azure Active Directory e su come gestire Azure con Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d0511f008a3d5bc39a0fb2d9406d33b72dbede6
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532952"
---
# <a name="what-is-the-azure-active-directory-architecture"></a>Che cos'è l'architettura di Azure Active Directory?

Azure Active Directory (Azure AD) consente di gestire in modo sicuro l'accesso ai servizi e alle risorse di Azure per gli utenti. In Azure AD è inclusa una suite completa di funzionalità di gestione delle identità. Per informazioni sulle funzionalità di Azure AD, vedere [Informazioni su Azure Active Directory](active-directory-whatis.md).

Con Azure AD, è possibile creare e gestire utenti e gruppi e abilitare le autorizzazioni per consentire o negare l'accesso alle risorse aziendali. Per informazioni sulla gestione delle identità, vedere [Nozioni fondamentali sulla gestione delle identità di Azure](active-directory-whatis.md).

## <a name="azure-ad-architecture"></a>Architettura di Azure AD

L'architettura distribuita geograficamente di Azure AD combina funzionalità complete di monitoraggio, reindirizzamento automatizzato, failover e ripristino che offrono ai clienti disponibilità e prestazioni a livello di organizzazione.

In questo articolo vengono illustrati gli elementi dell'architettura seguenti:

*   Progettazione dell'architettura del servizio
*   Scalabilità
*   Disponibilità continua
*   Data center

### <a name="service-architecture-design"></a>Progettazione dell'architettura del servizio

L'approccio più diffuso per la compilazione di un sistema accessibile, utilizzabile e ad alto contenuto di dati consiste nell'usare blocchi predefiniti o unità di scala indipendenti. Per il livello di dati di Azure AD, le unità di scala sono note come *partizioni*.

Il livello dati ha diversi servizi front-end che forniscono funzionalità di lettura/scrittura. Il diagramma seguente illustra come vengono distribuiti i componenti di una partizione a directory singola in data center distribuiti geograficamente.

  ![Diagramma delle partizioni a directory singola](./media/active-directory-architecture/active-directory-architecture.png)

I componenti dell'architettura di Azure AD includono una replica primaria e una replica secondaria.

#### <a name="primary-replica"></a>Replica primaria

La *replica primaria* riceve tutte le *operazioni di scrittura* per la partizione a cui appartiene. Ogni operazione di scrittura viene immediatamente replicata in una replica secondaria in un data center diverso prima di restituire un'operazione riuscita al chiamante, assicurando così la durabilità con ridondanza geografica delle operazioni di scrittura.

#### <a name="secondary-replicas"></a>Repliche secondarie

Tutte le *letture* di directory sono gestite da *repliche secondarie*, che si trovano in data center che si trovano fisicamente in aree geografiche diverse. Esistono molte repliche secondarie perché i dati vengono replicati in modo asincrono. Le letture di directory, ad esempio le richieste di autenticazione, vengono gestite dai data center vicini ai clienti. Le repliche secondarie sono responsabili della scalabilità delle operazioni di lettura.

### <a name="scalability"></a>Scalabilità

La scalabilità è la possibilità per un servizio di espandersi per poter soddisfare le crescenti richieste in termini di prestazioni. La scalabilità delle operazioni di scrittura richiede il partizionamento dei dati. La scalabilità delle operazioni di lettura richiede la replica dei dati da una partizione a più repliche secondarie distribuite in tutto il mondo.

Le richieste provenienti dalle applicazioni directory vengono instradate al data center fisicamente più vicino. Le operazioni di scrittura vengono reindirizzate in modo trasparente alla replica primaria per garantire la coerenza delle operazioni di lettura/scrittura. Le repliche secondarie estendono considerevolmente la scalabilità delle partizioni perché in genere le directory gestiscono per lo più operazioni di lettura.

Le applicazioni directory si connettono ai data center più vicini, migliorando le prestazioni e rendendo possibile l'aumento del numero di istanze. Poiché una partizione di directory può avere più repliche secondarie, le repliche secondarie possono essere poste più vicino ai client delle directory. Solo i componenti del servizio directory interni con un numero elevato di operazioni di scrittura specificano come destinazione direttamente la replica primaria attiva.

### <a name="continuous-availability"></a>Disponibilità continua

La disponibilità (o tempo di attività) definisce la possibilità per un sistema di funzionare senza interruzioni. La chiave per Azure AD la disponibilità elevata è che i servizi possono spostare rapidamente il traffico tra più data center distribuiti geograficamente. Ogni data center è indipendente, che Abilita le modalità di errore decorrelato. Grazie a questa progettazione a disponibilità elevata, Azure AD non richiede alcun tempo di inattività per le attività di manutenzione.

La progettazione delle partizioni di Azure AD è più semplice rispetto alla progettazione aziendale di AD, in quanto viene usata una progettazione a master singolo che include un processo di failover della replica primaria deterministico orchestrato con attenzione.

#### <a name="fault-tolerance"></a>Tolleranza di errore

Un sistema è più disponibile se è a tolleranza di errore di hardware, di rete e di software. Per ogni partizione nella directory, esiste una replica master a disponibilità elevata: la replica primaria. In questa replica vengono eseguite solo operazioni di scrittura nella partizione. Questa replica viene monitorata in modo continuo e attento e le operazioni di scrittura possono essere immediatamente spostate in un'altra replica, che diventa la nuova replica primaria, se viene rilevato un errore. Durante il failover, è possibile che si verifichi una perdita di disponibilità in scrittura, in genere per 1-2 minuti. La disponibilità in lettura non è invece interessata durante questo intervallo di tempo.

Le operazioni di lettura (che sono più numerose di quelle di scrittura di diversi ordini di grandezza) passano solo alle repliche secondarie. Poiché le repliche secondarie sono idempotenti, la perdita di una replica in una determinata partizione viene facilmente compensata indirizzando le operazioni di lettura a un'altra replica, in genere nello stesso data center.

#### <a name="data-durability"></a>Durabilità dei dati

Una scrittura viene impegnata in modo durevole in almeno due Data Center prima che venga riconosciuta. Questo problema si verifica eseguendo prima il commit della scrittura sul database primario e quindi replicando immediatamente la scrittura in almeno un altro Data Center. Questa azione di scrittura garantisce che una potenziale perdita irreversibile del data center che ospita il database primario non comporti la perdita di dati.

Azure AD ha un [Obiettivo tempo di ripristino ](https://en.wikipedia.org/wiki/Recovery_time_objective) pari a zero per non perdere dati sui failover. Sono inclusi:

* Rilascio di token e operazioni di lettura directory
* Solo circa 5 minuti obiettivo tempo di ripristino concessi per le operazioni di scrittura nella directory

### <a name="datacenters"></a>Data center

Le repliche di Azure AD vengono archiviate in data center situati in tutto il mondo. Per altre informazioni, vedere [infrastruttura globale di Azure](https://azure.microsoft.com/global-infrastructure/).

Azure AD funziona nei data center con le seguenti caratteristiche:

* Autenticazione, Graph e gli altri servizi di AD si trovano dietro il servizio gateway. Il gateway gestisce il bilanciamento del carico di questi servizi. Verrà eseguito il failover automatico se viene rilevato che server non integri usano probe di integrità transazionali. In base a questi probe di integrità, il gateway instrada dinamicamente il traffico a Data Center integri.
* Per le *letture*, nella directory sono presenti repliche secondarie e servizi front-end corrispondenti in una configurazione Active-Active che opera in più data center. In caso di errore di un intero data center, il traffico verrà indirizzato automaticamente a un data center diverso.
 \* Per le *Scritture*, la directory eseguirà il failover della replica primaria (Master) tra i Data Center tramite pianificato (il nuovo database primario viene sincronizzato con quello precedente) o le procedure di failover di emergenza. La durabilità dei dati viene eseguita replicando qualsiasi commit in almeno due data center.

#### <a name="data-consistency"></a>Coerenza dei dati

Il modello della directory si basa sulla coerenza finale. Un problema tipico dei sistemi distribuiti che eseguono la replica asincrona è che i dati restituiti da una "particolare" replica potrebbero non essere aggiornati. 

Azure AD offre la coerenza in lettura/scrittura per le applicazioni che specificano come destinazione una replica secondaria instradandone le operazioni di scrittura alla replica primaria ed effettuando di nuovo il pull sincrono delle operazioni di scrittura alla replica secondaria.

Per le operazioni di scrittura delle applicazioni che usano l'API Graph di Azure AD non è necessario mantenere l'affinità con la replica di una directory per la coerenza in lettura/scrittura. Il servizio Azure AD Graph gestisce una sessione logica con affinità a una replica secondaria utilizzata per le letture. l'affinità viene acquisita in un "token di replica" memorizzato nella cache del servizio Graph utilizzando una cache distribuita nel Data Center della replica secondaria. Questo token viene quindi usato per le operazioni successive nella stessa sessione logica. Per continuare a utilizzare la stessa sessione logica, le richieste successive devono essere indirizzate allo stesso Azure AD Data Center. Non è possibile continuare una sessione logica se le richieste del client di directory vengono instradate a più data center Azure AD; in tal caso, il client dispone di più sessioni logiche con consistenze di lettura/scrittura indipendenti.

 >[!NOTE]
 >Le operazioni di scrittura vengono immediatamente replicate nella replica secondaria in cui sono state rilasciate le operazioni di lettura della sessione logica.

#### <a name="backup-protection"></a>Protezione dei backup

La directory implementa le eliminazioni temporanee, invece delle eliminazioni definitive, per poter ripristinare facilmente utenti e tenant in caso di eliminazione accidentale da parte di un cliente. Se l'amministratore del tenant elimina accidentalmente un utente, può facilmente annullare l'operazione e ripristinare l'utente eliminato.

Azure AD implementa backup giornalieri di tutti i dati e quindi può ripristinare autorevolmente i dati in caso di eliminazioni logiche o danneggiamenti. Il livello dati usa i codici di correzione degli errori e può quindi verificare la presenza di errori e correggere automaticamente determinati tipi di errori del disco.

#### <a name="metrics-and-monitors"></a>Metriche e monitoraggi

L'esecuzione di un servizio a disponibilità elevata richiede funzionalità di monitoraggio e metriche di alto livello. Azure AD analizza e segnala ininterrottamente le principali metriche sull'integrità del servizio e i criteri di successo per ogni servizio. Vengono continuamente sviluppate e ottimizzate metriche e funzionalità di monitoraggio e avviso per ogni scenario, in ogni singolo servizio di Azure AD e nell'insieme di tutti i servizi.

Se un servizio di Azure AD non funziona come previsto, vengono immediatamente eseguite operazioni per ripristinare la funzionalità il prima possibile. La metrica più importante di cui Azure AD tiene traccia è la velocità con cui è possibile rilevare e attenuare un problema relativo a siti live per i clienti. Vengono effettuati considerevoli investimenti nelle funzionalità di monitoraggio e avviso per ridurre al minimo i tempi di rilevamento (obiettivo per il tempo di rilevamento: < 5 minuti) e nella conformità operativa per ridurre al minimo i tempi di attenuazione (obiettivo per il tempo di attenuazione: < 30 minuti).

#### <a name="secure-operations"></a>Operazioni sicure

Per tutte le operazioni, vengono usati controlli operativi, ad esempio Multi-Factor Authentication (MFA), oltre al controllo di tutte le operazioni. Viene anche usato un sistema di elevazione JIT per concedere l'accesso temporaneo necessario per eventuali attività operative su richiesta su base regolare. Per altre informazioni, vedere [Il cloud attendibile](https://azure.microsoft.com/support/trust-center).

## <a name="next-steps"></a>Passaggi successivi

[Guida per gli sviluppatori di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop)