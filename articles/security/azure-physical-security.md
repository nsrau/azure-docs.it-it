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
ms.openlocfilehash: a6a9b1d6e12dabb09cde684c34481b4b3442c1b8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101530"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Azure facilities, premises, and physical security (Sicurezza fisica, presupposti e strutture di Azure)
Cloud di Microsoft è costituito da una [infrastruttura del data center distribuita a livello globale](https://azure.microsoft.com/global-infrastructure/) che supporta migliaia di servizi online e che si estende su più di 100 strutture altamente protette in tutto il mondo.

L'infrastruttura è progettata per avvicinare le applicazioni agli utenti in tutto il mondo, mantenendo la residenza dei dati e fornendo ai clienti opzioni complete per la conformità e la resilienza. Azure ha 52 regioni in tutto il mondo ed è disponibile in 140 paesi.

Un'area è un set di Data Center interconnessi tramite una rete di grandi dimensioni e resiliente. La rete comprende la distribuzione del contenuto, il bilanciamento del carico, la ridondanza e la crittografia per impostazione predefinita. Con un numero di aree globali superiore rispetto a qualsiasi altro provider di servizi cloud, Azure offre ai clienti la flessibilità necessaria per distribuire le applicazioni dove necessario.

Le aree di Azure sono organizzate in aree geografiche. Un'area geografica di Azure assicura il rispetto dei requisiti di residenza, sovranità, conformità e resilienza entro limiti geografici.

Le aree geografiche consentono ai clienti con esigenze specifiche a livello di residenza dei dati e conformità di mantenere vicini i propri dati e le proprie applicazioni. Le aree geografiche offrono la tolleranza di errore per resistere a un errore completo dell'area tramite la connessione all'infrastruttura di rete a capacità elevata.

Le zone di disponibilità sono località separate fisicamente entro un'area di Azure. Ogni zona di disponibilità è costituita da uno o più data center dotati di impianti indipendenti per l'energia, il raffreddamento e la rete. Le zone di disponibilità consentono ai clienti di eseguire applicazioni cruciali con disponibilità elevata e replica a bassa latenza.

La figura seguente mostra come l'infrastruttura globale di Azure associa l'area e le zone di disponibilità con gli stessi limiti di residenza dei dati per assicurare disponibilità elevata, ripristino di emergenza e backup.

![Limite della residenza dei dati][1]

Un grande footprint geografico distribuito dei Data Center consente a Microsoft di essere più vicino ai clienti per ridurre la latenza di rete e consentire il backup con ridondanza geografica e il failover.

## <a name="physical-security"></a>Sicurezza fisica
Microsoft progetta, crea e opera Data Center in un modo che controlli rigorosamente l'accesso fisico alle aree in cui sono archiviati i dati dei clienti. Microsoft comprende l'importanza di proteggere i dati del cliente e si impegna ad aiutare a proteggere i Data Center che contengono i dati. C'è un'intera divisione Microsoft dedicata alla progettazione, alla compilazione e alla gestione delle installazioni fisiche che supportano Azure. Questo team viene usato per mantenere la sicurezza fisica d'avanguardia.

Microsoft adotta un approccio a più livelli per la sicurezza fisica per ridurre il rischio che utenti non autorizzati ottengano l'accesso fisico ai dati e alle risorse dei Data Center. I Data Center gestiti da Microsoft hanno estesi livelli di protezione: accedere all'approvazione nel perimetro della struttura, nel perimetro dell'edificio, all'interno della compilazione e sul pavimento del Data Center. I livelli di sicurezza fisica sono:

- Richieste di accesso e di approvazione. È necessario richiedere l'accesso prima di arrivare al Data Center. Viene richiesto di fornire una motivazione aziendale valida per la visita, ad esempio ai fini di controllo o di conformità. Tutte le richieste sono approvate su una base di necessità di accesso a cura dei dipendenti Microsoft. La base di necessità di accesso consente di mantenere il numero di persone necessarie per completare un'attività nei Data Center per il livello minimo. Una volta che viene concessa l'autorizzazione, un singolo può accedere solo all'area discreta del Data Center in base alla motivazione aziendale approvata. Le autorizzazioni sono limitate a un determinato periodo di tempo e scadono dopo il periodo di tempo consentito.

- Perimetro dell'impianto - quando si arriva a un Data Center, sarà necessario passare attraverso un punto di accesso ben definito. In genere, le altezze delle staccionate fatte in acciaio e cemento includono ogni singolo centimetro del perimetro. Esistono fotocamere digitali intorno ai Data Center, con un team di protezione che monitora i video 24 ore su 24, 7 giorni su 7, 365 giorni all'anno.

- Entrata di compilazione - L'ingresso del Data Center è occupato dai responsabili della sicurezza professionale che sono stati sottoposti a rigorose verifiche di training e in background. I responsabili della sicurezza pattugliano anche periodicamente il Data Center mentre monitorano anche i video delle fotocamere digitali all'interno del Data Center 24 ore su 24, 7 giorni su 7, 365 giorni all'anno.

- All'interno della compilazione - dopo essere entrati nella compilazione, è necessario passare l'autenticazione a due fattori con dati biometrici per continuare a spostarsi all'interno del Data Center. Se l'identità dell'utente è convalidata, è possibile inserire la parte del datacenter a cui è stato autorizzato l'accesso. Si può rimanere lì solo per la durata del tempo approvato.

- Tetto minimo de datacenter. È possibile entrare solo nel tetto minimo approvato. Viene richiesto di passare attraverso uno screening di rilevamento dei metalli su tutto il corpo. Per ridurre il rischio che dati non autorizzati entrino o escano dal datacenter a nostra insaputa, solo i dispositivi approvati possono entrare nel data center. Inoltre, le videocamere monitorano la parte anteriore e posteriore di ogni rack del server. Lo screening di rilevamento dei metalli su tutto il corpo viene ripetuto quando si esce dal piano del data center. Per lasciare il Data Center, viene richiesto di passare attraverso un'analisi aggiuntiva di sicurezza.

Ai visitatori viene richiesto di restituire i badge all'uscita da qualsiasi struttura Microsoft.

## <a name="physical-security-reviews"></a>Verifiche sulla sicurezza fisica
Le verifiche sulla sicurezza fisica delle strutture vengono eseguite periodicamente per assicurare che i Data Center indirizzino in modo corretto i requisiti di sicurezza di Microsoft Azure. Il personale del provider di hosting del Data Center non fornisce la gestione dei servizi di Microsoft Azure. Il personale non può accedere ai sistemi di Azure o accedere fisicamente alle cabine o alle stanze di collocazione di Azure.

## <a name="data-bearing-devices"></a>Dispositivi che si basano sui dati
Microsoft usa le procedure consigliate migliori e una soluzione di pulizia che sia [conforme a NIST 800-88](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). Per i dischi rigidi che non possono essere cancellati, viene usato un processo di distruzione che li distrugge e rende impossibile il recupero delle informazioni. Il processo di eliminazione potrebbe essere disintegrato, suddiviso, polverizzato o bruciato. Il metodo di smaltimento più appropriato dipende dal tipo di asset. Vengono comunque mantenuta la documentazione relativa alla distruzione.  

## <a name="equipment-disposal"></a>Smaltimento delle apparecchiature
Microsoft Azure implementa questo principio per conto dei clienti. Al termine del ciclo di vita di un sistema, il personale operativo Microsoft segue rigorosi processi di gestione dei dati e di eliminazione dell'hardware per garantire che nessun componente hardware in cui possano essere contenuti dati dei clienti sia reso disponibile a parti non fidate. Per le unità che lo supportano, viene seguito un approccio di tipo "cancellazione sicura" (tramite il firmware dell'unità disco rigido). Per i dischi rigidi che non possono essere cancellati, viene usato un processo di distruzione che li distrugge e rende impossibile il recupero delle informazioni. Il processo di eliminazione potrebbe essere disintegrato, suddiviso, polverizzato o bruciato. Il metodo di smaltimento più appropriato dipende dal tipo di asset. Vengono comunque mantenuta la documentazione relativa alla distruzione. Tutti i servizi di Microsoft Azure usano servizi approvati per l'archiviazione dei contenuti multimediali e la gestione dello smaltimento.

## <a name="compliance"></a>Conformità
L'infrastruttura di Azure è progettata e gestita per soddisfare un'ampia gamma di standard di conformità internazionali e specifici del settore, come ISO 27001, HIPAA, FedRAMP, SOC 1 e SOC 2. Anche gli standard specifici vengono soddisfatti, tra cui IRAP australiano, G-Cloud del Regno Unito e MTCS di Singapore. Controlli rigorosi di terzi, come quelli eseguiti dal British Standards Institute, verificano la conformità di Azure con le rigide normative di sicurezza definite da questi standard.

Vedere le [offerte di conformità](https://www.microsoft.com/trustcenter/compliance/complianceofferings) per un elenco completo degli standard di conformità rispettati da Azure.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni che Microsoft esegue per proteggere l'infrastruttura di Azure, vedere:

- [Availability of Azure infrastructure](azure-infrastructure-availability.md) (Disponibilità dell'infrastruttura di Azure)
- [Azure information system components and boundaries](azure-infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Architettura di rete di Azure](azure-infrastructure-network.md)
- [Rete di produzione di Azure](azure-production-network.md)
- [Funzionalità di sicurezza del database SQL di Microsoft Azure](azure-infrastructure-sql.md)
- [Azure production operations and management](azure-infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Monitoring of Azure infrastructure](azure-infrastructure-monitoring.md) (Monitoraggio dell'infrastruttura di Azure)
- [Integrity of Azure infrastructure](azure-infrastructure-integrity.md) (Integrità dell'infrastruttura di Azure)
- [Protezione dei dati dei clienti in Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-physical-security/data-residency-boundary.png
