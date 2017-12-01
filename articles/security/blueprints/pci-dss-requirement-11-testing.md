---
title: Piano per l'elaborazione dei pagamenti di Azure - Requisiti di test
description: Requisito 11 di PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9753853b-ad80-4be4-9416-2583b8fd2029
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 57429741afd2ffd16c09a0f1485cb1cfbdda5571
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="testing-requirements-for-pci-dss-compliant-environments"></a>Requisiti di test per ambienti conformi a PCI DSS 
## <a name="pci-dss-requirement-11"></a>Requisito 11 di PCI DSS

**Testare regolarmente sistemi e processi di sicurezza**

> [!NOTE]
> Questi requisiti sono definiti dal [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) nell'ambito di [PCI Data Security Standard (DSS) versione 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Per informazioni sulle procedure di test e indicazioni per ogni requisito, vedere lo standard PCI DSS.

Nuove vulnerabilità vengono individuate continuamente da utenti malintenzionati e ricercatori nonché introdotte da nuovo software. I componenti di sistema, i processi e il software personalizzato devono essere sottoposti frequentemente a test affinché i controlli di sicurezza continuino a riflettere un ambiente in evoluzione.

## <a name="pci-dss-requirement-111"></a>Requisito 11.1 di PCI DSS

**11.1** Implementare processi per verificare la presenza di punti di accesso wireless (802.11) e rilevare e identificare tutti i punti di accesso wireless autorizzati e non autorizzati con cadenza trimestrale.

> [!NOTE]
> I metodi utilizzabili nel processo includono, tra gli altri, analisi della rete wireless, controlli di tipo fisico e logico dell'infrastruttura e dei componenti di sistema, controllo dell'accesso alla rete e sistemi di rilevamento/prevenzione intrusioni wireless.
Indipendentemente dal metodo usato, questo deve essere sufficiente a rilevare e identificare sia i dispositivi autorizzati che quelli non autorizzati.


**Responsabilità:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure non autorizza o consente connessioni wireless nell'ambiente di rete di Azure. Team di sicurezza interni verificano regolarmente, con cadenza trimestrale, la presenza di segnali wireless non autorizzati, che vengono analizzati e rimossi. I clienti non sono autorizzati a distribuire tecnologia wireless nell'ambiente Azure. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Nella soluzione non vengono implementate funzionalità SNMP e wireless.|



### <a name="pci-dss-requirement-1111"></a>Requisito 11.1.1 di PCI DSS

**11.1.1** Mantenere un inventario dei punti di accesso wireless autorizzati, che includa una motivazione aziendale documentata.

**Responsabilità:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 11.1](#pci-dss-requirement-11-1). |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Nella soluzione non vengono implementate funzionalità SNMP e wireless.|



### <a name="pci-dss-requirement-1112"></a>Requisito 11.1.2 di PCI DSS

**11.1.2** Implementare procedure di risposta agli eventi imprevisti in caso di rilevamento di punti di accesso wireless non autorizzati.


**Responsabilità:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 11.1](#pci-dss-requirement-11-1). |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Nella soluzione non vengono implementate funzionalità SNMP e wireless.|



## <a name="pci-dss-requirement-112"></a>Requisito 11.2 di PCI DSS

**11.2** Eseguire analisi interne ed esterne delle vulnerabilità di rete almeno una volta ogni tre mesi e dopo ogni modifica significativa nella rete, come installazioni di nuovi componenti di sistema, modifiche della topologia di rete o delle regole del firewall e aggiornamenti dei prodotti. 

> [!NOTE]
> È possibile combinare più report di analisi per il processo di analisi trimestrale per mostrare che sono stati analizzati tutti i sistemi e sono state risolte tutte le vulnerabilità applicabili. Potrebbe essere necessaria una documentazione aggiuntiva per verificare che le vulnerabilità non corrette siano in fase di risoluzione.
> Per la conformità iniziale a PCI DSS, non è necessario che vengano completate quattro analisi trimestrali positive se il valutatore verifica che 1) il risultato dell'analisi più recente era positivo, 2) l'entità ha procedure e criteri documentati che richiedono l'analisi trimestrale e 3) le vulnerabilità indicate nei risultati dell'analisi sono state corrette come dimostrato da una o più nuove analisi. Per gli anni successivi all'esame PCI DSS iniziale, devono essere eseguite quattro analisi trimestrali con esito positivo.


**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure esegue analisi interne ed esterne trimestrali delle vulnerabilità. Le analisi vengono eseguite da personale qualificato. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | La soluzione Contoso Webstore è stata sottoposta a test di penetrazione e analisi delle vulnerabilità "così com'è". I risultati del test di penetrazione possono essere duplicati con strumenti comuni come nmap o pentest-tools.com. Il test di penetrazione offrirà risultati non significativi per la superficie di attacco, senza elementi sfruttabili. Il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations), inoltre, offrono informazioni sulle vulnerabilità e la relativa correzione.|



### <a name="pci-dss-requirement-1121"></a>Requisito 11.2.1 di PCI DSS

**11.2.1** Eseguire analisi interne trimestrali delle vulnerabilità. Risolvere le vulnerabilità ed eseguire nuove analisi per verificare che tutte le vulnerabilità "ad alto rischio" siano state risolte, in base alla classificazione di vulnerabilità dell'entità (secondo il requisito 6.1). Le analisi devono essere eseguite da personale qualificato.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure esegue analisi delle vulnerabilità sull'infrastruttura sottostante che rientra nell'ambito. Microsoft Azure implementa l'analisi delle vulnerabilità su sistemi operativi server, database e dispositivi di rete con gli strumenti di analisi delle vulnerabilità appropriati. Le applicazioni Web di Azure vengono analizzate son soluzioni di analisi di settore appropriate. Le analisi delle vulnerabilità vengono eseguite con cadenza trimestrale.<br /><br />Le nuove analisi vengono eseguite in base alle esigenze su tutti i sistemi finché non vengono risolte tutte le vulnerabilità "ad alto rischio" (in base alla definizione contenuta nel requisito 6.1). |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | La soluzione Contoso Webstore è stata sottoposta a test di penetrazione e analisi delle vulnerabilità "così com'è". I risultati del test di penetrazione possono essere duplicati con strumenti comuni come nmap o pentest-tools.com. Il test di penetrazione offrirà risultati non significativi per la superficie di attacco, senza elementi sfruttabili. Il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations), inoltre, offrono informazioni sulle vulnerabilità e la relativa correzione.|



### <a name="pci-dss-requirement-1122"></a>Requisito 11.2.2 di PCI DSS

**11.2.2** Eseguire analisi esterne trimestrali delle vulnerabilità, tramite un fornitore di servizi di analisi approvato (ASV, Approved Scanning Vendor) autorizzato dal Payment Card Industry Security Standards Council (PCI SSC). Ripetere le analisi in base alle esigenze, finché non si ottengono analisi positive. 

> [!NOTE]
> Le analisi esterne trimestrali delle vulnerabilità devono essere eseguite da un ASV autorizzato dal Payment Card Industry Security Standards Council (PCI SSC).
> Per informazioni sulle responsabilità dei clienti in relazione alle analisi, sulla preparazione delle analisi e così via, vedere la guida del programma ASV pubblicata nel sito Web PCI SSC.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure esegue analisi esterne delle vulnerabilità sull'infrastruttura sottostante inclusa nell'ambito che è accessibile esternamente. Le analisi vengono eseguite da un ASV.<br /><br />Microsoft Azure sottoscrive le notifiche mensili di patch MSRC/OSSC ed esegue l'analisi delle vulnerabilità almeno una volta ogni tre mesi. Le vulnerabilità identificate vengono valutate e corrette nei tempi stabiliti, in base al livello di rischio.<br /><br />Ogni analisi completa e mirata trimestrale delle vulnerabilità di sicurezza sui componenti con priorità dell'ambiente Microsoft Azure ha lo scopo di identificare le vulnerabilità di sicurezza. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Distribuendo Contoso Webstore, i clienti della demo sono tenuti a eseguire analisi esterne trimestrali delle vulnerabilità e ripetere le analisi in base alle esigenze su tutte le istanze PaaS nel proprio ambiente dei dati dei titolari di carte (CDE, Cardholder Data Environment), avvalendosi di un ASV autorizzato dal Payment Card Industry Security Standards Council.<br /><br />|



### <a name="pci-dss-requirement-1123"></a>Requisito 11.2.3 di PCI DSS

**11.2.3** Eseguire analisi interne ed esterne, nonché ripeterle se necessario, dopo ogni modifica significativa. Le analisi devono essere eseguite da personale qualificato.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | I risultati vengono segnalati agli stakeholder e il team di sicurezza di Azure tiene traccia della correzione fino alla chiusura. I risultati dei test di Azure possono essere condivisi con i clienti con un accordo di riservatezza. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a eseguire analisi interne ed esterne trimestrali delle vulnerabilità e ripetere le analisi in base alle esigenze su tutte le istanze PaaS nel proprio ambiente CDE. Le analisi devono essere eseguite dopo modifiche significative nell'ambiente che rientra nell'ambito.<br /><br />Le analisi devono essere eseguite da un ASV o da personale indipendente dall'organizzazione.|



## <a name="pci-dss-requirement-113"></a>Requisito 11.3 di PCI DSS

**11.3** Implementare una metodologia di test di penetrazione con le caratteristiche seguenti:
- È basata su approcci ai test di penetrazione accettati dal settore (ad esempio, NIST SP800-115)
- Include la copertura dell'intero perimetro dell'ambiente CDE e dei sistemi critici
- Include test sia dall'interno che dall'esterno della rete
- Include test per la convalida di qualsiasi controllo di segmentazione e riduzione dell'ambito
- Definisce i test di penetrazione a livello di applicazione in modo da includere almeno le vulnerabilità elencate nel requisito 6.5
- Definisce i test di penetrazione a livello di rete in modo da includere i componenti che supportano le funzioni di rete nonché i sistemi operativi
- Include l'esame e la valutazione delle minacce e delle vulnerabilità verificatesi negli ultimi 12 mesi
- Specifica la conservazione dei risultati dei test di penetrazione e delle attività di correzione

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure convalida i servizi usando test di penetrazione di terze parti basati sulla Top 10 OWASP (Open Web Application Security Project) con tester con certificazione CREST. Dei risultati dei test viene tenuta traccia tramite un registro dei rischi, che viene controllato ed esaminato regolarmente per garantire la conformità alle procedure di sicurezza. <br /><br />Microsoft usa anche il Red Teaming per l'infrastruttura, i servizi e le applicazioni gestiti da Microsoft. Nel corso dei test di penetrazione su siti live e delle attività di Red Teaming non vengono deliberatamente usati dati dei clienti finali. I test vengono eseguiti sull'infrastruttura e sulle piattaforme Microsoft Azure, oltre che su applicazioni e dati di Microsoft. I tenant, le applicazioni e i dati dei clienti ospitati in Azure non vengono mai coinvolti.<br /><br />Microsoft Azure ha incaricato un valutatore indipendente di sviluppare un piano di valutazione dei sistemi ed eseguire una valutazione dei controlli. Le valutazioni dei controlli vengono eseguite annualmente e i risultati vengono segnalati alle parti coinvolte. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | La soluzione Contoso Webstore è stata sottoposta a test di penetrazione e analisi delle vulnerabilità "così com'è". I risultati del test di penetrazione possono essere duplicati con strumenti comuni come nmap o pentest-tools.com. Il test di penetrazione offrirà risultati non significativi per la superficie di attacco, senza elementi sfruttabili. Il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations), inoltre, offrono informazioni sulle vulnerabilità e la relativa correzione.|



### <a name="pci-dss-requirement-1131"></a>Requisito 11.3.1 di PCI DSS

**11.3.1** Eseguire test di penetrazione *esterna* almeno una volta all'anno e dopo ogni aggiornamento o modifica significativa dell'infrastruttura o delle applicazioni, come un aggiornamento del sistema operativo o l'aggiunta all'ambiente di una subnet o un server Web.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 11.3](#pci-dss-requirement-11-3). |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | La soluzione Contoso Webstore è stata sottoposta a test di penetrazione e analisi delle vulnerabilità "così com'è". I risultati del test di penetrazione possono essere duplicati con strumenti comuni come nmap o pentest-tools.com. Il test di penetrazione offrirà risultati non significativi per la superficie di attacco, senza elementi sfruttabili. Il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations), inoltre, offrono informazioni sulle vulnerabilità e la relativa correzione.|



### <a name="pci-dss-requirement-1132"></a>Requisito 11.3.2 di PCI DSS

**11.3.2** Eseguire test di penetrazione *interna* almeno una volta all'anno e dopo ogni aggiornamento o modifica significativa dell'infrastruttura o delle applicazioni, come un aggiornamento del sistema operativo o l'aggiunta all'ambiente di una subnet o un server Web.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure si avvale di valutatori indipendenti per l'esecuzione di test di penetrazione dei limiti di Microsoft Azure. Vengono anche eseguite regolarmente esercitazioni di Red Teaming, i cui risultati vengono usati per apportare miglioramenti della sicurezza. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | La soluzione Contoso Webstore è stata sottoposta a test di penetrazione e analisi delle vulnerabilità "così com'è". I risultati del test di penetrazione possono essere duplicati con strumenti comuni come nmap o pentest-tools.com. Il test di penetrazione offrirà risultati non significativi per la superficie di attacco, senza elementi sfruttabili. Il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations), inoltre, offrono informazioni sulle vulnerabilità e la relativa correzione.|



### <a name="pci-dss-requirement-1133"></a>Requisito di 11.3.3 PCI DSS

**11.3.3** Le vulnerabilità sfruttabili trovate durante i test di penetrazione vengono corrette e i test vengono ripetuti per verificare le correzioni.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Sono state stabilite procedure per monitorare i componenti della piattaforma Microsoft Azure in relazione alle vulnerabilità di sicurezza note. <br /><br /><br /><br />Ogni analisi completa e mirata trimestrale delle vulnerabilità di sicurezza sui componenti con priorità dell'ambiente di produzione Azure ha lo scopo di identificare le vulnerabilità di sicurezza. I risultati vengono segnalati agli stakeholder e il team tiene traccia della correzione fino alla chiusura. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Per garantire la correzione di tutti i problemi in sospeso per l'ambiente CDE della demo Contoso Webstore sono stati usati il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations), che offrono informazioni sulle vulnerabilità e la relativa correzione.|



### <a name="pci-dss-requirement-1134"></a>Requisito 11.3.4 di PCI DSS

**11.3.4** Se si usa la segmentazione per isolare l'ambiente CDE dalle altre reti, eseguire i test di penetrazione almeno una volta all'anno e dopo qualsiasi modifica ai controlli/metodi di segmentazione per verificare che i metodi di segmentazione siano operativi ed efficaci e isolare tutti i sistemi che non rientrano nell'ambito dai sistemi nell'ambiente CDE.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Sono state stabilite procedure per monitorare i componenti della piattaforma Microsoft Azure in relazione alle vulnerabilità di sicurezza note. <br /><br /><br /><br />Ogni analisi completa e mirata trimestrale delle vulnerabilità di sicurezza sui componenti con priorità dell'ambiente di produzione Azure ha lo scopo di identificare le vulnerabilità di sicurezza. I risultati vengono segnalati agli stakeholder e il team tiene traccia della correzione fino alla chiusura. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Per garantire la correzione di tutti i problemi in sospeso per l'ambiente CDE della demo Contoso Webstore sono stati usati il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations), che offrono informazioni sulle vulnerabilità e la relativa correzione.|



### <a name="pci-dss-requirement-11341"></a>Requisito 11.3.4.1 di PCI DSS

**11.3.4.1** *Requisito aggiuntivo solo per provider di servizi:* se si usa la segmentazione, verificare l'ambito PCI DSS eseguendo test di penetrazione nei controlli di segmentazione almeno ogni sei mesi e dopo qualsiasi modifica ai controlli/metodi di segmentazione.

> [!NOTE]
> Questo requisito è una procedura consigliata fino al 31 gennaio 2018 e in seguito diventerà un requisito.


**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 11.3.4](#pci-dss-requirement-11-3-4). |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Per garantire la correzione di tutti i problemi in sospeso per l'ambiente CDE della demo Contoso Webstore sono stati usati il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations), che offrono informazioni sulle vulnerabilità e la relativa correzione.|



## <a name="pci-dss-requirement-114"></a>Requisito 11.4 di PCI DSS

**11.4** Usare tecniche di rilevamento e/o prevenzione delle intrusioni per rilevare e/o prevenire intrusioni nella rete. Monitorare tutto il traffico in corrispondenza del perimetro dell'ambiente dei dati dei titolari di carte nonché dei punti critici all'interno di tale ambiente e segnalare possibili compromissioni al personale addetto.
Mantenere aggiornati tutti i motori, le baseline e le firme di rilevamento e prevenzione delle intrusioni.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure esegue analisi in tempo reale degli eventi nel proprio ambiente operativo e i sistemi di rilevamento delle intrusioni generano avvisi in tempo reale per gli eventi che potrebbero compromettere il sistema. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore è un servizio PaaS e il rilevamento e la prevenzione delle intrusioni nella rete rientrano nelle responsabilità di Azure. Il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations) offrono funzionalità di avviso e correzione delle intrusioni.|



## <a name="pci-dss-requirement-115"></a>Requisito 11.5 di PCI DSS

**11.5** Distribuire un meccanismo di rilevamento delle modifiche (ad esempio, strumenti di monitoraggio dell'integrità dei file) per segnalare al personale la modifica non autorizzata (includendo modifiche, aggiunte ed eliminazioni) di file di sistema, di configurazione o di contenuto critici e configurare il software affinché esegua confronti dei file critici almeno una volta alla settimana. 

> [!NOTE]
> Ai fini del rilevamento delle modifiche, i file critici sono in genere i file che non vengono modificati regolarmente e la cui modifica potrebbe indicare una compromissione del sistema o un rischio di compromissione. I meccanismi di rilevamento delle modifiche come i prodotti di monitoraggio dell'integrità dei file sono in genere preconfigurati con file critici per il sistema operativo correlato. Gli altri file critici, ad esempio quelli per le applicazioni personalizzate, devono essere valutati e definiti dall'entità, ossia dall'esercente o dal provider di servizi.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure gestisce e notifica ai clienti i potenziali eventi e modifiche che potrebbero influire sulla sicurezza o la disponibilità dei servizi tramite un dashboard del servizio online. Le modifiche agli impegni e agli obblighi per la sicurezza dei clienti di Microsoft Azure vengono aggiornate nel sito Web Microsoft Azure in modo tempestivo.<br /><br />L'installazione o le modifiche di software nell'ambiente di produzione di Microsoft Azure sono limitate al personale amministrativo autorizzato e seguono specifiche procedure di gestione del cambiamento. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | La demo Contoso Webstore è un servizio PaaS e il rilevamento delle modifiche è stato implementato con OMS. Per altre informazioni, vedere la sezione relativa alle [soluzioni OMS preinstallate nelle linee guida per PCI](payment-processing-blueprint.md#oms-solutions).<br /><br />|



### <a name="pci-dss-requirement-1151"></a>Requisito 11.5.1 di PCI DSS

**11.5.1** Implementare un processo per rispondere agli avvisi generati dalla soluzione di rilevamento delle modifiche.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Le regole per gli eventi di monitoraggio di Azure offrono un livello superiore di monitoraggio per operazioni e asset ad alto rischio. I dispositivi di rete gestiti da Azure vengono monitorati in relazione alla conformità agli standard di sicurezza stabiliti. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Gli avvisi di Contoso Webstore per le modifiche vengono forniti dall'implementazione di OMS. Per altre informazioni, vedere la sezione relativa alle [soluzioni OMS preinstallate nelle linee guida per PCI](payment-processing-blueprint.md#oms-solutions).<br /><br /><br /><br />|



## <a name="pci-dss-requirement-116"></a>Requisito 11.6 di PCI DSS

**11.6** Garantire che i criteri di sicurezza e le procedure operative per il monitoraggio e i test della sicurezza siano documentati, in uso e noti a tutte le parti coinvolte.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Gli avvisi di Contoso Webstore per le modifiche vengono forniti dall'implementazione di OMS. Per altre informazioni, vedere la sezione relativa alle [soluzioni OMS preinstallate nelle linee guida per PCI](payment-processing-blueprint.md#oms-solutions).<br /><br /><br /><br />|




