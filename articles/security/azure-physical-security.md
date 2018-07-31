---
title: Azure facilities, premises, and physical security (Sicurezza fisica, presupposti e strutture di Azure) | Microsoft Docs
description: L'articolo descrive i Data Center di Azure, tra cui le offerte di conformità, sicurezza e infrastruttura fisica.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 69a54640ae3b4e71d7782712ad1764babab7104a
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170678"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Azure facilities, premises, and physical security (Sicurezza fisica, presupposti e strutture di Azure)
Azure è costituito da un'[infrastruttura del Data Center distribuita a livello globale](https://azure.microsoft.com/global-infrastructure/), che supporta migliaia di servizi online e che si estende su più di 100 strutture altamente protette in tutto il mondo.

L'infrastruttura è progettata per avvicinare le applicazioni agli utenti in tutto il mondo, mantenendo la residenza dei dati e fornendo ai clienti opzioni complete per la conformità e la resilienza. Azure ha 52 regioni in tutto il mondo ed è disponibile in 140 paesi.

Un'area è un set di Data Center interconnessi tramite una rete di grandi dimensioni e resiliente. La rete comprende la distribuzione del contenuto, il bilanciamento del carico, la ridondanza e la crittografia per impostazione predefinita. Con un numero di aree globali superiore rispetto a qualsiasi altro provider di servizi cloud, Azure offre la flessibilità necessaria per distribuire le applicazioni dove necessario.

Le aree di Azure sono organizzate in aree geografiche. Un'area geografica di Azure assicura il rispetto dei requisiti di residenza, sovranità, conformità e resilienza entro limiti geografici.

Le aree geografiche consentono ai clienti con esigenze specifiche a livello di residenza dei dati e conformità di mantenere vicini i propri dati e le proprie applicazioni. Le aree geografiche offrono la tolleranza di errore per resistere a un errore completo dell'area tramite la connessione all'infrastruttura di rete a capacità elevata.

Le zone di disponibilità sono località separate fisicamente entro un'area di Azure. Ogni zona di disponibilità è costituita da uno o più Data Center dotati di impianti indipendenti per l'energia, il raffreddamento e la rete. Le zone di disponibilità consentono di eseguire applicazioni cruciali con disponibilità elevata e replica a bassa latenza.

La figura seguente mostra come l'infrastruttura globale di Azure associa l'area e le zone di disponibilità con gli stessi limiti di residenza dei dati per assicurare disponibilità elevata, ripristino di emergenza e backup.

![Diagramma che mostra il limite della residenza dei dati][1]

Data Center distribuiti geograficamente consentono a Microsoft di essere più vicino ai clienti per ridurre la latenza di rete e consentire il backup con ridondanza geografica e il failover.

## <a name="physical-security"></a>Sicurezza fisica
Microsoft progetta, crea e opera Data Center in modo che controllino rigorosamente l'accesso fisico alle aree in cui sono archiviati i dati dei clienti. Microsoft comprende l'importanza di proteggere i dati del cliente e si impegna ad aiutare a proteggere i Data Center che contengono i dati. C'è un'intera divisione Microsoft dedicata alla progettazione, alla compilazione e alla gestione delle installazioni fisiche che supportano Azure. Questo team viene usato per mantenere la sicurezza fisica d'avanguardia.

Microsoft adotta un approccio a più livelli per la sicurezza fisica per ridurre il rischio che utenti non autorizzati ottengano l'accesso fisico ai dati e alle risorse dei Data Center. I Data Center gestiti da Microsoft hanno estesi livelli di protezione: accedere all'approvazione nel perimetro della struttura, nel perimetro dell'edificio, all'interno della compilazione e sul pavimento del Data Center. I livelli di sicurezza fisica sono:

- **Richiesta di accesso e approvazione.** È necessario richiedere l'accesso prima di arrivare al Data Center. Viene richiesto di fornire una motivazione aziendale valida per la visita, ad esempio ai fini di controllo o di conformità. Tutte le richieste sono approvate su una base di necessità di accesso a cura dei dipendenti Microsoft. La base di necessità di accesso consente di mantenere il numero di persone necessarie per completare un'attività nei Data Center per il livello minimo. Una volta che viene concessa l'autorizzazione da parte di Microsoft, un singolo può accedere solo all'area discreta del Data Center per la quale ha presentato richiesta in base alla motivazione aziendale approvata. Le autorizzazioni sono limitate a un determinato periodo di tempo, dopodiché scadono.

- **Perimetro della struttura.** Quando si arriva a un Data Center, sarà necessario passare attraverso un punto di accesso ben definito. In genere, le altezze delle staccionate fatte in acciaio e cemento includono ogni singolo centimetro del perimetro. Esistono fotocamere digitali intorno ai Data Center, con un team di protezione che monitora costantemente i video.

- **Ingresso della struttura.** L'ingresso del Data Center è occupato dai responsabili della sicurezza professionale che sono stati sottoposti a rigorose verifiche di training e idoneità. I responsabili della sicurezza pattugliano periodicamente il Data Center e monitorano costantemente anche i video delle fotocamere digitali all'interno del Data Center.

- **All'interno della struttura.** Dopo essere entrati nella struttura, è necessario superare l'autenticazione a due fattori con dati biometrici per continuare a spostarsi all'interno del Data Center. Se l'identità dell'utente è convalidata, è possibile entrare solo nella parte del Data Center per la quale è stata ottenuta l'autorizzazione. Si può rimanere lì solo per la durata del tempo approvato.

- **Piano del Data center.** È possibile accedere solo al piano per il quale è stata ottenuta l'autorizzazione. Viene richiesto di passare attraverso uno screening di rilevamento dei metalli su tutto il corpo. Per ridurre il rischio che dati non autorizzati entrino o escano dal datacenter a nostra insaputa, solo i dispositivi approvati possono entrare nel data center. Inoltre, le videocamere monitorano la parte anteriore e posteriore di ogni rack del server. Lo screening di rilevamento dei metalli su tutto il corpo viene ripetuto quando si esce dal piano del Data Center. Per lasciare il Data Center, viene richiesto di passare attraverso un'analisi aggiuntiva di sicurezza.

Ai visitatori viene richiesto di restituire i badge all'uscita di qualsiasi struttura Microsoft.

## <a name="physical-security-reviews"></a>Verifiche sulla sicurezza fisica
Periodicamente vengono condotte verifiche sulla sicurezza fisica delle strutture per assicurare che i Data Center soddisfino appieno i requisiti di sicurezza di Azure. Il personale del provider di hosting del Data Center non fornisce la gestione dei servizi di Azure. Il personale non può accedere ai sistemi di Azure o accedere fisicamente alle cabine o alle stanze di collocazione di Azure.

## <a name="data-bearing-devices"></a>Dispositivi che si basano sui dati
Microsoft usa le procedure consigliate migliori e una soluzione di pulizia che sia [conforme a NIST 800-88](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). Per i dischi rigidi che non possono essere cancellati, viene usato un processo di distruzione che li distrugge e rende impossibile il recupero delle informazioni. Il processo di distruzione può essere disintegrato, suddiviso, polverizzato o bruciato. Viene determinato il metodo di smaltimento in base al tipo di asset. Viene comunque conservata la documentazione relativa alla distruzione.  

## <a name="equipment-disposal"></a>Smaltimento delle apparecchiature
Al termine del ciclo di vita di un sistema, il personale operativo Microsoft segue rigorosi processi di gestione dei dati e di eliminazione dell'hardware per garantire che nessun componente hardware in cui possano essere contenuti dati dei clienti sia reso disponibile a parti non fidate. Viene usato un approccio di eliminazione sicura per i dischi rigidi che lo supportano. Per i dischi rigidi che non possono essere cancellati, viene usato un processo di distruzione che li distrugge e rende impossibile il recupero delle informazioni. Il processo di distruzione può essere disintegrato, suddiviso, polverizzato o bruciato. Viene determinato il metodo di smaltimento in base al tipo di asset. Viene comunque conservata la documentazione relativa alla distruzione. Tutti i servizi di Azure usano servizi approvati per l'archiviazione dei contenuti multimediali e la gestione dello smaltimento.

## <a name="compliance"></a>Conformità
L'infrastruttura di Azure è progettata e gestita per soddisfare un'ampia gamma di standard di conformità internazionali e specifici del settore, come ISO 27001, HIPAA, FedRAMP, SOC 1 e SOC 2. Anche gli standard specifici vengono soddisfatti, tra cui IRAP dell'Australia, G-Cloud del Regno Unito e MTCS di Singapore. Controlli rigorosi di terzi, come quelli eseguiti dal British Standards Institute, verificano la conformità di Azure con le rigide normative di sicurezza definite da questi standard.

Vedere le [Offerte di conformità](https://www.microsoft.com/trustcenter/compliance/complianceofferings) per un elenco completo degli standard di conformità rispettati da Azure. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure, vedere:

- [Disponibilità dell'infrastruttura di Azure](azure-infrastructure-availability.md)
- [Azure information system components and boundaries](azure-infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Architettura di rete di Azure](azure-infrastructure-network.md)
- [Rete di produzione di Azure](azure-production-network.md)
- [Funzionalità di sicurezza del database SQL di Microsoft Azure](azure-infrastructure-sql.md)
- [Azure production operations and management](azure-infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Monitoraggio dell'infrastruttura di Azure](azure-infrastructure-monitoring.md)
- [Integrità dell'infrastruttura di Azure](azure-infrastructure-integrity.md)
- [Protezione dei dati dei clienti di Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-physical-security/data-residency-boundary.png
