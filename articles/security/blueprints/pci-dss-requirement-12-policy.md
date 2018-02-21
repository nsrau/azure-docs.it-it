---
title: Piano per l'elaborazione dei pagamenti di Azure - Requisiti per i criteri
description: Requisito 12 di PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: a79d59d8-20e3-4efe-8686-c8f4ed80e220
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 05e9ed7c886d37a024db1eedbc541705b7d8a9a9
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="policy-requirements-for-pci-dss-compliant-environments"></a>Requisiti per i criteri per ambienti conformi a PCI DSS  
## <a name="pci-dss-requirement-12"></a>Requisito 12 di PCI DSS

**Gestire criteri che garantiscano la sicurezza delle informazioni per tutto il personale**

> [!NOTE]
> Questi requisiti sono definiti dal [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) nell'ambito di [PCI Data Security Standard (DSS) versione 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Per informazioni sulle procedure di test e indicazioni per ogni requisito, vedere lo standard PCI DSS.

Criteri di sicurezza rigidi definiscono il livello di sicurezza per l'intera entità e chiariscono ai membri del personale quali sono le aspettative nei loro confronti. Tutto il personale deve essere consapevole della riservatezza dei dati e delle proprie responsabilità in termini di protezione. Ai fini del requisito 12, per "personale" si intendono dipendenti a tempo pieno e parziale, dipendenti con contratto a tempo determinato, terzisti e consulenti che svolgono le proprie prestazioni presso la sede dell'entità o hanno in altro modo accesso all'ambiente dei dati dei titolari di carte.

## <a name="pci-dss-requirement-121"></a>Requisito 12.1 di PCI DSS

**12.1** Stabilire, pubblicare, gestire e diffondere criteri di sicurezza.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a stabilire e gestire criteri di sicurezza delle informazioni.|



### <a name="pci-dss-requirement-1211"></a>Requisito 12.1.1 di PCI DSS

**12.1.1** Rivedere i criteri di sicurezza almeno una volta all'anno e aggiornarli in caso di modifica dell'ambiente.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti ad aggiornare i propri criteri di sicurezza delle informazioni almeno una volta all'anno o in caso di modifiche all'ambiente dei dati dei titolari di carte (CDE, Cardholder Data Environment).|



## <a name="pci-dss-requirement-122"></a>Requisito 12.2 di PCI DSS

**12.2** Implementare un processo di valutazione dei rischi con le caratteristiche seguenti:
- Viene eseguito almeno una volta all'anno e in occasione di modifiche significative all'ambiente, come acquisizioni, fusioni, trasferimenti e così via.
- Identifica asset critici, minacce e vulnerabilità.
- Genera un'analisi formale documentata dei rischi.
- > Le metodologie di valutazione dei rischi includono, ad esempio, OCTAVE, ISO 27005 e NIST SP 800-30.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a implementare un processo di valutazione dei rischi che gestisca tutte le minacce elencate nel requisito 12.2.|



## <a name="pci-dss-requirement-123"></a>Requisito 12.3 di PCI DSS

**12.3** Sviluppare criteri di utilizzo per le tecnologie critiche e definire l'uso corretto di queste tecnologie.

> [!NOTE]
> Le tecnologie critiche includono, ad esempio, tecnologie wireless e di accesso remoto, laptop, tablet, supporti elettronici rimovibili e utilizzo di posta elettronica e Internet.
Assicurarsi che questi criteri di utilizzo richiedano quanto riportato di seguito.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a creare e gestire criteri che impongano procedure corrette di utilizzo, implementazione e autenticazione per le tecnologie critiche all'interno dell'ambiente CDE.|



### <a name="pci-dss-requirement-1231"></a>Requisito 12.3.1 di PCI DSS

**12.3.1** Approvazione esplicita delle parti autorizzate

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a creare e gestire criteri che impongano procedure corrette di utilizzo, implementazione e autenticazione per le tecnologie critiche all'interno dell'ambiente CDE.|



### <a name="pci-dss-requirement-1232"></a>Requisito 12.3.2 di PCI DSS

**12.3.2** Autenticazione per l'uso della tecnologia

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a creare e gestire criteri che impongano procedure corrette di utilizzo, implementazione e autenticazione per le tecnologie critiche all'interno dell'ambiente CDE.|



### <a name="pci-dss-requirement-1233"></a>Requisito 12.3.3 di PCI DSS

**12.3.3** Elenco di tutti i dispositivi di questo tipo e del personale autorizzato all'accesso

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a creare e gestire criteri che impongano procedure corrette di utilizzo, implementazione e autenticazione per le tecnologie critiche all'interno dell'ambiente CDE.|



### <a name="pci-dss-requirement-1234"></a>Requisito 12.3.4 di PCI DSS

**12.3.4** Metodo per determinare rapidamente e accuratamente proprietario, informazioni di contatto e scopo (ad esempio, etichettatura, codifica e/o inventariazione dei dispositivi)

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a creare e gestire criteri che impongano procedure corrette di utilizzo, implementazione e autenticazione per le tecnologie critiche all'interno dell'ambiente CDE.|



### <a name="pci-dss-requirement-1235"></a>Requisito 12.3.5 di PCI DSS

**12.3.5** Usi accettabili della tecnologia

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a creare e gestire criteri che impongano procedure corrette di utilizzo, implementazione e autenticazione per le tecnologie critiche all'interno dell'ambiente CDE.|



### <a name="pci-dss-requirement-1236"></a>Requisito 12.3.6 di PCI DSS

**12.3.6** Posizioni di rete accettabili per le tecnologie

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a determinare i percorsi di rete accettabili per le VM, le risorse di archiviazione e i servizi di supporto basati sul cloud.|



### <a name="pci-dss-requirement-1237"></a>Requisito 12.3.7 di PCI DSS

**12.3.7** Elenco dei prodotti approvati dall'azienda

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a determinare i percorsi di rete accettabili per le VM, le risorse di archiviazione e i servizi di supporto basati sul cloud.|



### <a name="pci-dss-requirement-1238"></a>Requisito 12.3.8 di PCI DSS

**12.3.8** Disconnessione automatica delle sessioni per le tecnologie di accesso remoto dopo uno specifico periodo di inattività

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure usa funzionalità di blocco delle sessioni di AD aziendali Microsoft, che applicano il blocco delle sessioni dopo un determinato periodo di inattività. Le connessioni di rete vengono terminate dopo 30 minuti di inattività. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a creare e gestire criteri che impongano procedure corrette di utilizzo, implementazione e autenticazione per le tecnologie critiche all'interno dell'ambiente CDE.|



### <a name="pci-dss-requirement-1239"></a>Requisito 12.3.9 di PCI DSS

**12.3.9** Attivazione di tecnologie di accesso remoto per fornitori e partner commerciali solo quando necessario, con disattivazione immediata dopo l'uso

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a creare e gestire criteri che impongano procedure corrette di utilizzo, implementazione e autenticazione per le tecnologie critiche all'interno dell'ambiente CDE.|



### <a name="pci-dss-requirement-12310"></a>Requisito 12.3.10 di PCI DSS

**12.3.10** Per il personale che accede ai dati dei titolari di carte tramite tecnologie di accesso remoto, proibire la copia, lo spostamento e l'archiviazione dei dati dei titolari di carte su dischi rigidi locali e supporti elettronici rimovibili, a meno che ciò non sia stato espressamente autorizzato per un'esigenza aziendale specifica.
Laddove è presente un'esigenza aziendale autorizzata, i criteri di utilizzo devono richiedere la protezione dei dati in conformità a tutti i requisiti PCI DSS applicabili.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a garantire che al personale che accede ai dati dei titolari di carte tramite tecnologie di accesso remoto siano proibiti la copia, lo spostamento e l'archiviazione dei dati dei titolari di carte su dischi rigidi locali e supporti elettronici rimovibili, a meno che ciò non sia stato espressamente autorizzato per un'esigenza aziendale specifica.|



## <a name="pci-dss-requirement-124"></a>Requisito 12.4 di PCI DSS

**12.4** Assicurarsi che i criteri e le procedure di sicurezza definiscano chiaramente le responsabilità in relazione alla sicurezza delle informazioni per tutto il personale.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a creare e gestire criteri che impongano procedure corrette di utilizzo, implementazione e autenticazione per le tecnologie critiche all'interno dell'ambiente CDE.|



### <a name="pci-dss-requirement-1241"></a>Requisito 12.4.1 di PCI DSS

**12.4.1** Requisito aggiuntivo solo per provider di servizi: i dirigenti dovranno stabilire la responsabilità della protezione dei dati dei titolari di carte e un programma di conformità PCI DSS in modo da includere quanto segue.
- Responsabilità generale del rispetto della conformità PCI DSS
- Definizione di un documento di dichiarazione di intenti per un programma di conformità PCI DSS e la comunicazione ai dirigenti 

> [!NOTE]
> Questo requisito è una procedura consigliata fino al 31 gennaio 2018 e in seguito diventerà un requisito.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti provider di servizi sono tenuti a documentare il proprio programma di conformità allo standard PCI.|



## <a name="pci-dss-requirement-125"></a>Requisito 12.5 di PCI DSS

**12.5** Assegnare a una persona o un team le responsabilità di gestione della sicurezza delle informazioni riportate di seguito.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a definire e assegnare ai propri dipendenti le responsabilità in relazione alla sicurezza delle informazioni.|



### <a name="pci-dss-requirement-1251"></a>Requisito 12.5.1 di PCI DSS

**12.5.1** Stabilire, documentare e distribuire i criteri e le procedure di sicurezza.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a definire e assegnare ai propri dipendenti le responsabilità in relazione alla sicurezza delle informazioni.|



### <a name="pci-dss-requirement-1252"></a>Requisito 12.5.2 di PCI DSS

**12.5.2** Monitorare e analizzare gli avvisi e le informazioni sulla sicurezza e distribuirli al personale appropriato.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a definire e assegnare ai propri dipendenti le responsabilità in relazione alla sicurezza delle informazioni.|



### <a name="pci-dss-requirement-1253"></a>Requisito 12.5.3 di PCI DSS

**12.5.3** Stabilire, documentare e distribuire le procedure di risposta ed escalation per gli eventi imprevisti relativi alla sicurezza per garantire una gestione tempestiva ed efficace di tutte le situazioni.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a creare e gestire criteri che impongano procedure corrette di utilizzo, implementazione e autenticazione per le tecnologie critiche all'interno dell'ambiente CDE.|



### <a name="pci-dss-requirement-1254"></a>Requisito 12.5.4 di PCI DSS

**12.5.4** Amministrare gli account utente, incluse aggiunte, eliminazioni e modifiche.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a creare e gestire criteri che impongano procedure corrette di utilizzo, implementazione e autenticazione per le tecnologie critiche all'interno dell'ambiente CDE.|



### <a name="pci-dss-requirement-1255"></a>Requisito 12.5.5 di PCI DSS

**12.5.5** Monitorare e controllare tutti gli accessi ai dati.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a creare e gestire criteri che impongano procedure corrette di utilizzo, implementazione e autenticazione per le tecnologie critiche all'interno dell'ambiente CDE.|



## <a name="pci-dss-requirement-126"></a>Requisito 12.6 di PCI DSS

**12.6** Implementare un programma formale di consapevolezza della sicurezza per rendere tutto il personale consapevole dell'importanza dei criteri e delle procedure di sicurezza per i dati dei titolari di carte.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a creare e gestire criteri per la consapevolezza della sicurezza per il personale che ha accesso all'ambiente CDE.|



### <a name="pci-dss-requirement-1261"></a>Requisito 12.6.1 di PCI DSS

**12.6.1** Formare il personale al momento dell'assunzione e almeno una volta all'anno. 

> [!NOTE]
> I metodi possono variare in funzione del ruolo svolto dal personale e del relativo livello di accesso ai dati dei titolari di carte.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a garantire che il personale riceva e confermi di aver ricevuto formazione per la consapevolezza della sicurezza delle informazioni e dello standard PCI-DSS almeno una volta all'anno.|



### <a name="pci-dss-requirement-1262"></a>Requisito 12.6.2 di PCI DSS

**12.6.2** Richiedere al personale di certificare almeno una volta all'anno di aver letto e compreso i criteri e le procedure di sicurezza.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a garantire che il personale riceva e confermi di aver ricevuto formazione per la consapevolezza della sicurezza delle informazioni e dello standard PCI-DSS almeno una volta all'anno.|



## <a name="pci-dss-requirement-127"></a>Requisito 12.7 di PCI DSS

**12.7** Sottoporre il personale potenziale a screening prima dell'assunzione per ridurre al minimo il rischio di attacchi da fonti interne. Esempi di indagini sul background del personale includono informazioni su impieghi precedenti, precedenti penali, storico del credito e controlli delle referenze. 

> [!NOTE]
> Per il personale potenziale da assumere per determinare posizioni che hanno accesso a un solo numero di carta alla volta durante una transazione, come i cassieri dei negozi, questo requisito è solo consigliato.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a garantire accurati controlli del background del personale che ha accesso all'ambiente CDE.|



## <a name="pci-dss-requirement-128"></a>Requisito 12.8 di PCI DSS

**12.8** Implementare e gestire criteri e procedure per gestire i provider di servizi con cui vengono condivisi i dati dei titolari di carte o che potrebbero incidere sulla sicurezza di tali dati, come indicato di seguito.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a monitorare la conformità allo standard PCI dei provider di servizi con cui vengono condivisi i dati dei titolari di carte o che potrebbero incidere sulla sicurezza dell'ambiente CDE. I clienti devono mantenere un elenco di tutti i provider di servizi usati nell'ambiente CDE.|



### <a name="pci-dss-requirement-1281"></a>Requisito 12.8.1 di PCI DSS

**12.8.1** Mantenere un elenco dei provider di servizi, con una descrizione del servizio fornito.


**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a monitorare la conformità allo standard PCI dei provider di servizi con cui vengono condivisi i dati dei titolari di carte o che potrebbero incidere sulla sicurezza dell'ambiente CDE. I clienti devono mantenere un elenco di tutti i provider di servizi usati nell'ambiente CDE.|



### <a name="pci-dss-requirement-1282"></a>Requisito 12.8.2 di PCI DSS

**12.8.2** Stipulare un accordo scritto contenente il riconoscimento delle responsabilità dei provider di servizi in relazione alla sicurezza dei dati dei titolari di carte di cui sono in possesso o che archiviano, elaborano o trasmettono in altro modo per conto del cliente o nella misura in cui potrebbero avere un impatto sulla sicurezza dell'ambiente dei dati dei titolari di carte del cliente. 

> [!NOTE]
> La formulazione esatta del riconoscimento dipenderà dall'accordo tra le due parti, dai dettagli del servizio fornito e dalle responsabilità assegnate a ognuna delle parti. Il riconoscimento non deve includere l'esatta formulazione usata in questo requisito.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a stipulare accordi scritti con cui i provider di servizi riconoscono la responsabilità di mantenere la sicurezza dei dati dei titolari di carte.|



### <a name="pci-dss-requirement-1283"></a>Requisito 12.8.3 di PCI DSS

**12.8.3** Assicurarsi che esista un processo definito per incaricare i provider di servizi che includa le attività di due diligence appropriate prima dell'incarico.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a garantire l'esistenza di un processo definito per incaricare i provider di servizi che includa le attività di due diligence appropriate prima dell'incarico.|



### <a name="pci-dss-requirement-1284"></a>Requisito 12.8.4 di PCI DSS

**12.8.4** Gestire un programma per monitorare lo stato di conformità PCI DSS dei provider di servizi almeno una volta all'anno.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a gestire un programma per monitorare lo stato di conformità PCI DSS dei provider di servizi almeno una volta all'anno.|



### <a name="pci-dss-requirement-1285"></a>Requisito 12.8.5 di PCI DSS

**12.8.5** Mantenere le informazioni su quali requisiti PCI DSS vengono gestiti da ogni provider di servizi e quali vengono gestiti dall'entità.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a conservare una copia della [matrice di riepilogo delle responsabilità](https://aka.ms/pciblueprintcrm32), che descrive i requisiti PCI DSS che rientrano nelle responsabilità del cliente e quelli di cui è responsabile Microsoft Azure.|



## <a name="pci-dss-requirement-129"></a>Requisito 12.9 di PCI DSS

**12.9** Requisito aggiuntivo solo per provider di servizi: i provider di servizi riconoscono per iscritto nei confronti dei clienti di essere responsabili della sicurezza dei dati dei titolari di carte di cui sono in possesso o che archiviano, elaborano o trasmettono in altro modo per conto del cliente o nella misura in cui potrebbero avere un impatto sulla sicurezza dell'ambiente dei dati dei titolari di carte del cliente. 

> [!NOTE]
> La formulazione esatta del riconoscimento dipenderà dall'accordo tra le due parti, dai dettagli del servizio fornito e dalle responsabilità assegnate a ognuna delle parti. Il riconoscimento non deve includere l'esatta formulazione usata in questo requisito.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti provider di servizi sono tenuti a riconoscere le proprie responsabilità per il rispetto della conformità allo standard PCI. |



## <a name="pci-dss-requirement-1210"></a>Requisito 12.10 di PCI DSS

**12.10** Implementare un piano di risposta agli eventi imprevisti. Prepararsi a rispondere immediatamente a una violazione del sistema.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a sviluppare piani di risposta agli eventi imprevisti e test che considerino tutti i controlli del cliente correlati ai touch point condivisi e a tutte le applicazioni del cliente che sfruttano l'infrastruttura di Azure. È responsabilità del cliente fornire ad Azure informazioni di contatto accurate nel caso in cui debba segnalare un evento imprevisto che influisce sull'applicazione o sui dati.|



### <a name="pci-dss-requirement-12101"></a>Requisito 12.10.1 di PCI DSS

**12.10.1** Creare il piano di risposta agli eventi imprevisti da implementare in caso di violazione del sistema. Assicurarsi che il piano includa almeno gli elementi seguenti:
- Ruoli, responsabilità e strategie di comunicazione e contatto in caso di compromissione, con notifica almeno ai marchi di pagamento
- Procedure specifiche di risposta agli eventi imprevisti
- Procedure di ripristino e continuità aziendale
- Processi di backup dei dati
- Analisi dei requisiti legali per la segnalazione delle compromissioni
- Copertura e risposte per tutti i componenti di sistema critici
- Riferimento o inclusione delle procedure di risposta agli eventi imprevisti dei marchi di pagamento

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a sviluppare piani di risposta agli eventi imprevisti e test che considerino tutti i controlli del cliente correlati ai touch point condivisi e a tutte le applicazioni del cliente che sfruttano l'infrastruttura di Azure. È responsabilità del cliente fornire ad Azure informazioni di contatto accurate nel caso in cui debba segnalare un evento imprevisto che influisce sull'applicazione o sui dati.|



### <a name="pci-dss-requirement-12102"></a>Requisito 12.10.2 di PCI DSS

**12.10.2** Rivedere e testare il piano, includendo tutti gli elementi elencati nel requisito 12.10.1, almeno una volta all'anno.


**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a sviluppare piani di risposta agli eventi imprevisti e test che considerino tutti i controlli del cliente correlati ai touch point condivisi e a tutte le applicazioni del cliente che sfruttano l'infrastruttura di Azure. È responsabilità del cliente fornire ad Azure informazioni di contatto accurate nel caso in cui debba segnalare un evento imprevisto che influisce sull'applicazione o sui dati.|



### <a name="pci-dss-requirement-12103"></a>Requisito 12.10.3 di PCI DSS

**12.10.3** Designare personale specifico che dovrà essere disponibile 24 ore su 24, 7 giorni su 7, per rispondere agli avvisi.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a sviluppare piani di risposta agli eventi imprevisti e test che considerino tutti i controlli del cliente correlati ai touch point condivisi e a tutte le applicazioni del cliente che sfruttano l'infrastruttura di Azure. È responsabilità del cliente fornire ad Azure informazioni di contatto accurate nel caso in cui debba segnalare un evento imprevisto che influisce sull'applicazione o sui dati.|



### <a name="pci-dss-requirement-12104"></a>Requisito 12.10.4 di PCI DSS

**12.10.4** Formare in modo appropriato il personale responsabile della risposta alle violazioni della sicurezza.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a sviluppare piani di risposta agli eventi imprevisti e test che considerino tutti i controlli del cliente correlati ai touch point condivisi e a tutte le applicazioni del cliente che sfruttano l'infrastruttura di Azure. È responsabilità del cliente fornire ad Azure informazioni di contatto accurate nel caso in cui debba segnalare un evento imprevisto che influisce sull'applicazione o sui dati.|



### <a name="pci-dss-requirement-12105"></a>Requisito 12.10.5 di PCI DSS

**12.10.5** Includere avvisi di sistemi di monitoraggio della sicurezza come, tra gli altri, il rilevamento e la prevenzione delle intrusioni, i firewall e i sistemi di monitoraggio dell'integrità dei file.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a sviluppare piani di risposta agli eventi imprevisti e test che considerino tutti i controlli del cliente correlati ai touch point condivisi e a tutte le applicazioni del cliente che sfruttano l'infrastruttura di Azure. È responsabilità del cliente fornire ad Azure informazioni di contatto accurate nel caso in cui debba segnalare un evento imprevisto che influisce sull'applicazione o sui dati.|



### <a name="pci-dss-requirement-12106"></a>Requisito 12.10.6 di PCI DSS

**12.10.6** Sviluppare un processo per modificare e migliorare il piano di risposta agli eventi imprevisti in base alle lezioni apprese e incorporare gli sviluppi del settore.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono tenuti a sviluppare piani di risposta agli eventi imprevisti e test che considerino tutti i controlli del cliente correlati ai touch point condivisi e a tutte le applicazioni del cliente che sfruttano l'infrastruttura di Azure. È responsabilità del cliente fornire ad Azure informazioni di contatto accurate nel caso in cui debba segnalare un evento imprevisto che influisce sull'applicazione o sui dati.|



## <a name="pci-dss-requirement-1211"></a>Requisito 12.11 di PCI DSS

**12.11** **Requisito aggiuntivo solo per provider di servizi:** Eseguire analisi almeno una volta ogni tre mesi per verificare che il personale stia seguendo i criteri e le procedure operative di sicurezza.
Le analisi devono coprire i processi seguenti:
- Analisi dei log giornalieri
- Analisi dei set di regole dei firewall
- Applicazione degli standard di configurazione ai nuovi sistemi
- Rispondere agli avvisi di sicurezza
- Processi di gestione del cambiamento 

> [!NOTE]
> Questo requisito è una procedura consigliata fino al 31 gennaio 2018 e in seguito diventerà un requisito.


**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti provider di servizi sono tenuti a documentare le analisi dei processi per la verifica delle prestazioni dei controlli della conformità allo standard PCI.|



### <a name="pci-dss-requirement-12111"></a>Requisito 12.11.1 di PCI DSS

**12.11.1** Requisito aggiuntivo solo per provider di servizi: gestire la documentazione del processo di analisi trimestrale in modo da includere quanto segue.
- Documentazione dei risultati delle analisi
- Analisi e approvazione dei risultati da parte del personale a cui è assegnata la responsabilità del programma di conformità PCI DSS 

> [!NOTE]
> Questo requisito è una procedura consigliata fino al 31 gennaio 2018 e in seguito diventerà un requisito.


**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti provider di servizi sono tenuti a documentare le analisi dei processi per la verifica delle prestazioni dei controlli della conformità allo standard PCI.|




