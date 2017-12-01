---
title: Progetto per l'elaborazione dei pagamenti di Azure - Requisiti per l'accesso fisico
description: Requisito 9 di PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 91595a69-e9ce-4f9c-8388-10224165d9c0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 89f7b20a130e988bfe4964d50ae97de788ca4623
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="physical-access-requirements-for-pci-dss-compliant-environments"></a>Requisiti per l'accesso fisico per ambienti conformi a PCI DSS 
## <a name="pci-dss-requirement-9"></a>Requisito 9 di PCI DSS

**Limitare l'accesso fisico ai dati di titolari di carte**

> [!NOTE]
> Questi requisiti sono definiti dal [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) nell'ambito di [PCI Data Security Standard (DSS) versione 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Per informazioni sulle procedure di test e indicazioni per ogni requisito, vedere lo standard PCI DSS.

Qualsiasi accesso fisico ai dati o ai sistemi che ospitano dati di titolari di carte offre l'opportunità a utenti singoli di accedere ai dispositivi o ai dati e rimuovere sistemi o copie cartacee, quindi deve essere limitato in modo appropriato. Ai fini del requisito 9, per "personale in sede" si intendono dipendenti a tempo pieno e part-time, dipendenti con contratto a tempo determinato, terzisti e consulenti fisicamente presenti nei locali dell'entità. Con "visitatore" si intende un fornitore, un ospite del personale in sede, addetti all'assistenza o chiunque abbia la necessità di entrare nella struttura per breve tempo, in genere non più di un giorno. Con "supporti" si intendono tutti i supporti cartacei ed elettronici contenenti i dati di titolari di carte.

## <a name="pci-dss-requirement-91"></a>Requisito 9.1 di PCI DSS

**9.1** Usare i controlli di accesso alla struttura appropriati per limitare e monitorare l'accesso fisico ai sistemi nell'ambiente dei dati di titolari di carte.

**Responsabilità:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure è responsabile dell'implementazione, dell'applicazione e del monitoraggio della sicurezza dell'accesso fisico per i data center. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile|



### <a name="pci-dss-requirement-911"></a>Requisito 9.1.1 di PCI DSS

**9.1.1** Usare videocamere o meccanismi di controllo dell'accesso (o entrambi) per monitorare l'accesso fisico alle aree sensibili. Verificare i dati raccolti, anche in correlazione con altri dati. Archiviare i dati per almeno tre mesi, in assenza di restrizioni legali diverse.

> [!NOTE]
> Con "aree sensibili" si intende qualsiasi data center, sala server o area che ospita sistemi in cui vengono archiviati, elaborati o trasmessi i dati di titolari di carte. Sono escluse le aree aperte al pubblico in cui sono presenti solo terminali per punto vendita, come le aree delle casse in un negozio al dettaglio.

**Responsabilità:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure è responsabile dell'implementazione, dell'applicazione e del monitoraggio dei meccanismi per il controllo dell'accesso biometrici e con TV a circuito chiuso per i data center. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile|



### <a name="pci-dss-requirement-912"></a>Requisito 9.1.2 di PCI DSS

**9.1.2** Implementare controlli fisici e/o logici per limitare l'accesso alle prese di rete accessibili pubblicamente. 

Ad esempio, è possibile disabilitare le prese di rete in aree pubbliche e nelle aree accessibili ai visitatori e abilitarle solo in caso di autorizzazione esplicita dell'accesso alla rete. In alternativa, si potrebbero implementare procedure per assicurarsi che i visitatori siano sempre accompagnati nelle aree con prese di rete attive.

**Responsabilità:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | All'interno della piattaforma Microsoft Azure non sono disponibili prese di rete accessibili pubblicamente. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile|



### <a name="pci-dss-requirement-913"></a>Requisito 9.1.3 di PCI DSS

**9.1.3** Limitare l'accesso fisico a punti di accesso wireless, gateway, dispositivi palmari, hardware di rete o per le comunicazioni e linee di telecomunicazione.

**Responsabilità:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | L'accesso fisico ai componenti hardware di rete di Microsoft Azure è sottoposto a controlli rigorosi tramite elenchi di accesso, più forme di autenticazione, barriere fisiche all'ingresso e approvazione dell'accesso ai dispositivi per esigenze aziendali comprovate. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile|



## <a name="pci-dss-requirement-92"></a>Requisito 9.2 di PCI DSS

**9.2** Sviluppare procedure che consentano di distinguere facilmente tra personale in sede e visitatori, incluso quanto segue:
- Identificazione del personale in sede e dei visitatori, ad esempio con l'assegnazione di badge.
- Modifiche ai requisiti di accesso
- Revoca o terminazione degli strumenti di identificazione del personale in sede o dei visitatori per scadenza, ad esempio i badge.

**Responsabilità:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure è responsabile dell'implementazione, dell'applicazione e del monitoraggio della sicurezza dell'accesso fisico e dell'identificazione di dipendenti o consulenti esterni che visitano i data center. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile|



## <a name="pci-dss-requirement-93"></a>Requisito 9.3 di PCI DSS

**9.3** Controllare l'accesso fisico del personale in sede alle aree sensibili come indicato di seguito:
- L'accesso deve essere autorizzato e approvato in base agli specifici ruoli professionali.
- L'accesso viene revocato immediatamente in caso di interruzione del rapporto di lavoro, e tutti i meccanismi di accesso fisico, ad esempio chiavi, schede di accesso e così via, vengono restituiti o disabilitati.

**Responsabilità:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Le autorizzazioni di accesso ai data center Microsoft sono controllate tramite un elenco di accessi autorizzati approvato dal team del data center in base al principio dei privilegi minimi. L'elenco di controllo di accesso viene rivisto, verificato e aggiornato trimestralmente.<br /><br />Per i data center di Microsoft Azure vengono usati dispositivi di accesso fisico come cancelli perimetrali, lettori di badge di accesso elettronico, lettori biometrici, porte/portoni di sicurezza a bussola e dispositivi anti-passback. I dispositivi per i badge di accesso sono costantemente monitorati. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile|



## <a name="pci-dss-requirement-94"></a>Requisito 9.4 di PCI DSS

**9.4** Implementare procedure per identificare e autorizzare i visitatori. Le procedure devono includere quanto segue.

**Responsabilità:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure è responsabile di assicurarsi che le consegne di spedizioni preapprovate vengano ricevute in un'area di carico sicuro fisicamente isolata dai locali di elaborazione delle informazioni e che siano monitorate da personale autorizzato. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile|



### <a name="pci-dss-requirement-941"></a>Requisito 9.4.1 di PCI DSS

**9.4.1** I visitatori devono essere autorizzati prima dell'ingresso e sempre accompagnati nelle aree in cui vengono elaborati o conservati i dati di titolari di carte.


**Responsabilità:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure è responsabile di assicurarsi che le consegne di spedizioni preapprovate vengano ricevute in un'area di carico sicuro fisicamente isolata dai locali di elaborazione delle informazioni e che siano monitorate da personale autorizzato. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile|



### <a name="pci-dss-requirement-942"></a>Requisito 9.4.2 di PCI DSS

**9.4.2** I visitatori vengono identificati e viene loro consegnato un badge o un altro meccanismo di identificazione soggetto a scadenza, che consenta di distinguere visibilmente i visitatori dal personale in sede.

**Responsabilità:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | L'accesso ai data center di Microsoft deve essere approvato preventivamente e i visitatori autorizzati hanno l'obbligo di presentarsi al personale di sicurezza al punto di arrivo e fornire un documento di identità valido prima dell'ingresso. I badge indicano chiaramente i dipendenti. I collaboratori esterni e i visitatori ricevono badge temporanei che devono essere restituiti al momento dell'uscita dalla struttura. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile|



### <a name="pci-dss-requirement-943"></a>Requisito 9.4.3 di PCI DSS

**9.4.3** Ai visitatori viene richiesto di restituire il badge o lo strumento di identificazione prima di lasciare la struttura o alla scadenza.

**Responsabilità:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Ai visitatori viene richiesto di restituire i badge all'uscita da qualsiasi struttura Microsoft. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile|



### <a name="pci-dss-requirement-944"></a>Requisito 9.4.4 di PCI DSS

**9.4.4** Viene usato un registro delle visite per ottenere un log di controllo fisico dell'attività dei visitatori nella struttura, nonché nelle sale computer e nei data center in cui vengono archiviati o trasmessi i dati di titolari di carte.
Registrare il nome del visitatore, l'azienda rappresentata e il personale in sede che autorizza l'accesso fisico.
Conservare questo registro per un minimo di tre mesi, in assenza di restrizioni legali diverse.

**Responsabilità:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure è responsabile della creazione e dell'aggiornamento di un registro delle visite come log di controllo fisico dell'attività dei visitatori nella struttura, nonché nelle sale computer e nei data center in cui vengono archiviati o trasmessi i dati di titolari di carte. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile|



## <a name="pci-dss-requirement-95"></a>Requisito 9.5 di PCI DSS

**9.5** Proteggere fisicamente tutti i supporti.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore archivia tutti i dati nel database SQL di Azure. Un'istanza di database SQL PaaS viene usata per presentare le misure di sicurezza del database. Per altre informazioni, vedere [PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) (Linee guida per PCI - Database SQL di Azure).|



### <a name="pci-dss-requirement-951"></a>Requisito 9.5.1 di PCI DSS

**9.5.1** Archiviare i backup dei supporti in un luogo sicuro, preferibilmente una struttura esterna, ad esempio una sede alternativa o di backup o una struttura di archiviazione commerciale. Verificare la sicurezza del luogo almeno ogni anno.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore archivia tutti i dati nel database SQL di Azure. Un'istanza di database SQL PaaS viene usata per presentare le misure di sicurezza del database. Per altre informazioni, vedere [PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) (Linee guida per PCI - Database SQL di Azure).|



## <a name="pci-dss-requirement-96"></a>Requisito 9.6 di PCI DSS

**9.6** Controllare in modo rigoroso la distribuzione all'interno o all'esterno di qualsiasi tipo di supporti, inclusi i requisiti seguenti.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore archivia tutti i dati nel database SQL di Azure. Un'istanza di database SQL PaaS viene usata per presentare le misure di sicurezza del database. Per altre informazioni, vedere [PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) (Linee guida per PCI - Database SQL di Azure).|



### <a name="pci-dss-requirement-961"></a>Requisito 9.6.1 di PCI DSS

**9.6.1** Classificare i supporti in modo che sia possibile determinare il livello di riservatezza dei dati.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore archivia tutti i dati nel database SQL di Azure. Un'istanza di database SQL PaaS viene usata per presentare le misure di sicurezza del database. Per altre informazioni, vedere [PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) (Linee guida per PCI - Database SQL di Azure).|



### <a name="pci-dss-requirement-962"></a>Requisito 9.6.2 di PCI DSS

**9.6.2** Inviare i supporti tramite un servizio di trasporto valori o qualsiasi altro metodo di spedizione che possa essere tracciato in modo accurato.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore archivia tutti i dati nel database SQL di Azure. Un'istanza di database SQL PaaS viene usata per presentare le misure di sicurezza del database. Per altre informazioni, vedere [PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) (Linee guida per PCI - Database SQL di Azure).|



### <a name="pci-dss-requirement-963"></a>Requisito 9.6.3 di PCI DSS

**9.6.3** Assicurarsi che la direzione approvi tutti gli eventuali spostamenti di supporti da un'area protetta (inclusa la distribuzione di supporti a singoli).

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore archivia tutti i dati nel database SQL di Azure. Un'istanza di database SQL PaaS viene usata per presentare le misure di sicurezza del database. Per altre informazioni, vedere [PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) (Linee guida per PCI - Database SQL di Azure).|



## <a name="pci-dss-requirement-97"></a>Requisito 9.7 di PCI DSS

**9.7** Controllare in modo rigoroso l'archiviazione e l'accessibilità dei supporti.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore archivia tutti i dati nel database SQL di Azure. Un'istanza di database SQL PaaS viene usata per presentare le misure di sicurezza del database. Per altre informazioni, vedere [PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) (Linee guida per PCI - Database SQL di Azure).|



### <a name="pci-dss-requirement-971"></a>Requisito 9.7.1 di PCI DSS

**9.7.1** Creare e mantenere aggiornati in modo appropriato log di inventario di tutti i supporti ed eseguire inventari dei supporti almeno ogni anno.


**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore archivia tutti i dati nel database SQL di Azure. Un'istanza di database SQL PaaS viene usata per presentare le misure di sicurezza del database. Per altre informazioni, vedere [PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) (Linee guida per PCI - Database SQL di Azure).|



## <a name="pci-dss-requirement-98"></a>Requisito 9.8 di PCI DSS

**9.8** Distruggere i supporti non più necessari per motivi aziendali o legali come indicato di seguito.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore archivia tutti i dati nel database SQL di Azure. Un'istanza di database SQL PaaS viene usata per presentare le misure di sicurezza del database. Per altre informazioni, vedere [PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) (Linee guida per PCI - Database SQL di Azure).|



### <a name="pci-dss-requirement-981"></a>Requisito 9.8.1 di PCI DSS

**9.8.1** Triturare, bruciare o macerare il materiale stampato, in modo che non sia possibile ricostruire i dati di titolari di carte. Proteggere i contenitori di archiviazione usati per i materiali che devono essere distrutti.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore archivia tutti i dati nel database SQL di Azure. Un'istanza di database SQL PaaS viene usata per presentare le misure di sicurezza del database. Per altre informazioni, vedere [PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) (Linee guida per PCI - Database SQL di Azure).|



### <a name="pci-dss-requirement-982"></a>Requisito 9.8.2 di PCI DSS

**9.8.2** Rendere irrecuperabili i dati di titolari di carte su supporti elettronici, in modo che tali dati non possano essere ricostruiti.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Le tecniche di distruzione dei dati variano a seconda del tipo di oggetto dati da distruggere, ovvero sottoscrizioni, archiviazione, macchine virtuali o database. Nell'ambiente multi-tenant di Microsoft Azure si dedica particolare attenzione ad assicurarsi che i dati di un cliente non entrino in contatto con i dati di un altro cliente oppure che quando un cliente eliminata dati, nessun altro cliente possa ottenere l'accesso ai dati eliminati, incluso nella maggior parte dei casi anche il cliente precedentemente proprietario di tali dati.<br /><br />Microsoft Azure segue le linee guida NIST 800-88 per la cancellazione sicura dei dati (Guidelines on Media Sanitization) che offrono indicazioni per una problematica prioritaria, ovvero assicurarsi che i dati non vengano cancellati involontariamente. Queste linee guida si applicano alla cancellazione sicura sia elettronica che fisica. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | È possibile eliminare interamente Contoso Webstore eliminando il gruppo di risorse usato durante la distribuzione.|



## <a name="pci-dss-requirement-99"></a>Requisito 9.9 di PCI DSS

**9.9** Proteggere da manomissioni e sostituzioni i dispositivi che acquisiscono i dati di carte di pagamento tramite l'interazione fisica diretta con la carta.

> [!NOTE]
> Questi requisiti si applicano ai dispositivi di lettura delle carte usati nelle transazioni con presenza della carta (ovvero strisciata o inserimento della carta) nel punto vendita. Questo requisito non è destinato all'applicazione a componenti per l'immissione manuale del codice, come tastiere di computer e tastierini di POS. 

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa Operations Management Suite per registrare tutte le modifiche di sistema.<br /><br />[Operations Management Suite (OMS)](/azure/operations-management-suite/) fornisce una registrazione completa delle modifiche. Le modifiche possono essere controllate e verificate per stabilirne l'accuratezza. Per istruzioni più dettagliate, vedere [PCI Guidance - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing) (Linee guida per PCI - Operations Management Suite).|



### <a name="pci-dss-requirement-991"></a>Requisito 9.9.1 di PCI DSS

**9.9.1** Creare e mantenere aggiornato un elenco di dispositivi. L'elenco dovrebbe includere le informazioni seguenti:
- Marca e modello del dispositivo
- Posizione del dispositivo (ad esempio, l'indirizzo del sito o della struttura in cui si trova il dispositivo)
- Numero di serie del dispositivo o altro metodo di identificazione univoca

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore offre un'architettura di riferimento e un elenco di tutti i servizi usati nella relativa documentazione di distribuzione.|



### <a name="pci-dss-requirement-992"></a>Requisito 9.9.2 di PCI DSS

**9.9.2** Controllare periodicamente le superfici dei dispositivi per rilevare eventuali manomissioni (ad esempio, l'aggiunta di skimmer di carte ai dispositivi) o sostituzioni (ad esempio, controllando il numero di serie o altre caratteristiche del dispositivo per verificare che non sia stato scambiato con un dispositivo fraudolento).

> [!NOTE]
> Alcuni segnali di esempio della possibile manomissione o sostituzione di un dispositivo includono componenti o cavi imprevisti collegati al dispositivo, etichette di sicurezza mancanti o modificate, involucro rotto o di colore diverso oppure modifiche al numero di serie o altri contrassegni esterni.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile|



### <a name="pci-dss-requirement-993"></a>Requisito 9.9.3 di PCI DSS

**9.9.3** Formare il personale in modo che sappia riconoscere i tentativi di manomissione o sostituzione dei dispositivi. La formazione dovrebbe riguardare gli aspetti seguenti:
- Verificare l'identità di terzi che dichiarano di essere tecnici per la riparazione o la manutenzione, prima di concedere loro l'accesso per modificare i dispositivi o risolverne i problemi.
- Non installare, sostituire o restituire i dispositivi senza le opportune verifiche.
- Imparare a riconoscere comportamenti sospetti in relazione ai dispositivi, ad esempio i tentativi da parte di sconosciuti di scollegare o aprire un dispositivo.
- Segnalare comportamenti sospetti ed eventuali segnali di manomissione o sostituzione del dispositivo al personale appropriato (ad esempio, un dirigente o un responsabile della sicurezza).

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile|



## <a name="pci-dss-requirement-910"></a>Requisito 9.10 di PCI DSS

**9.10** Assicurarsi che i criteri di sicurezza e le procedure operative per la limitazione dell'accesso fisico ai dati di titolari di carte siano documentati, applicati e noti a tutte le parti interessate.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile|




