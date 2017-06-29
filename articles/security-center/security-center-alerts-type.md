---
title: Avvisi di sicurezza per tipo nel Centro sicurezza di Azure | Microsoft Docs
description: Questo articolo illustra i diversi tipi di avvisi di sicurezza disponibili nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 19f71e0d5a8a4642b86ae60a3ab2a4042fa2990e
ms.contentlocale: it-it
ms.lasthandoff: 06/17/2017


---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure
Questo articolo consente di comprendere i diversi tipi di avvisi di sicurezza e le informazioni significative che ne derivano disponibili nel Centro sicurezza di Azure. Per altre informazioni sulla gestione degli avvisi e degli eventi imprevisti, vedere [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md).

> [!NOTE]
> Per configurare le funzionalità di rilevamento avanzato, eseguire l'aggiornamento al livello Standard del Centro sicurezza di Azure. È disponibile una versione di valutazione gratuita di 60 giorni. Per eseguire l'aggiornamento, selezionare il **Piano tariffario** nei [criteri di sicurezza](security-center-policies.md). Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).
>

## <a name="what-type-of-alerts-are-available"></a>Quali tipi di avvisi sono disponibili?
Il Centro sicurezza di Azure usa una serie di [funzionalità di rilevamento](security-center-detection-capabilities.md) per avvisare i clienti riguardo a potenziali attacchi contro gli ambienti in cui operano. Questi avvisi contengono informazioni importanti relative a cosa ha attivato l'avviso, alle risorse interessate e all'origine dell'attacco. Le informazioni incluse in un avviso variano in base al tipo di analisi usata per rilevare la minaccia. Anche gli eventi imprevisti possono contenere ulteriori informazioni contestuali utili nel corso dell'analisi di una minaccia.  Questo articolo fornisce informazioni sui tipi di avvisi seguenti:

* Analisi del comportamento delle macchine virtuali (VMBA)
* Analisi di rete
* Analisi delle risorse
* Informazioni contestuali

## <a name="virtual-machine-behavioral-analysis"></a>Analisi del comportamento delle macchine virtuali
Il Centro sicurezza di Azure può usare le analisi del comportamento per identificare le risorse compromesse in base all'analisi del registro eventi delle macchine virtuali, ad esempio eventi di creazione di processi ed eventi di accesso. Esiste inoltre una correlazione con altri segnali per verificare la presenza di elementi a riprova di una campagna su larga scala.

> [!NOTE]
> Per altre informazioni sulle funzionalità di rilevamento del Centro sicurezza, vedere [Funzionalità di rilevamento del Centro sicurezza di Azure](security-center-detection-capabilities.md).
>

### <a name="crash-analysis"></a>Analisi degli arresti anomali
L'analisi della memoria del dump di arresto anomalo è un metodo usato per rilevare malware sofisticato in grado di eludere le soluzioni di sicurezza tradizionali. Varie forme di malware provano a ridurre le probabilità di essere rilevate dai prodotti antivirus non scrivendo mai sul disco oppure crittografando i componenti software scritti sul disco. Ciò rende il malware difficile da rilevare con gli approcci antimalware tradizionali. Tuttavia, questo tipo di malware può essere rilevato tramite l'analisi della memoria, perché per funzionare il malware deve lasciare tracce in memoria.

Quando il software si arresta in modo anomalo, un dump di arresto anomalo acquisisce una porzione della memoria al momento dell'arresto. L'arresto anomalo può essere causato da malware o da problemi generali del sistema o delle applicazioni. Analizzando la memoria nel dump di arresto anomalo, il Centro sicurezza può rilevare le tecniche usate per sfruttare le vulnerabilità del software, accedere ai dati riservati e rimanere permanentemente all'interno di un computer infetto in modo furtivo. Questa operazione viene eseguita con un impatto minimo sulle prestazioni degli host perché l'analisi viene eseguita dal Centro sicurezza in back-end.

I campi seguenti sono comuni per gli esempi di avvisi relativi al dump di arresto anomalo del sistema, disponibili più avanti in questo articolo:

* DUMPFILE (FILE DUMP): nome del file di dump di arresto anomalo del sistema.
* PROCESSNAME (NOME PROCESSO): nome del processo bloccato.
* PROCESSVERSION (VERSIONE PROCESSO): versione del processo bloccato.

### <a name="shellcode-discovered"></a>Individuato attacco shellcode
Uno shellcode è il payload che viene eseguito dopo che il malware ha sfruttato una vulnerabilità del software. Questo avviso indica che l'analisi di dump di arresto anomalo del sistema ha rilevato codice eseguibile che presenta un comportamento comunemente adottato dai payload dannosi. Sebbene anche il software non dannoso possa mostrare comportamenti simili, questo non fa generalmente parte delle normali procedure di sviluppo software.

L'esempio di avviso Shellcode fornisce il campo aggiuntivo seguente:

* ADDRESS (INDIRIZZO): posizione in memoria dello shellcode.

Esempio di questo tipo di avviso:

![Avviso per shellcode](./media/security-center-alerts-type/security-center-alerts-type-fig2.png)

### <a name="module-hijacking-discovered"></a>Individuato hijack del modulo
Windows usa le librerie a collegamento dinamico (DLL) per consentire al software di usare funzionalità del sistema di Windows comuni. L'hijack della DLL si verifica quando il malware modifica l'ordine di caricamento della DLL per caricare payload dannosi in memoria, dove è possibile eseguire codice arbitrario. Questo avviso indica che l'analisi del dump di arresto anomalo del sistema ha rilevato un modulo con nome simile caricato da due percorsi diversi. Uno dei percorsi caricati proviene da un percorso binario di sistema di Windows.

Gli sviluppatori di software legittimo cambiano occasionalmente l'ordine di caricamento della DLL per motivi non dannosi, ad esempio per instrumentare o estendere il sistema operativo Windows o un'applicazione Windows. Per distinguere le modifiche dannose da quelle potenzialmente non dannose all'ordine di caricamento della DLL, il Centro sicurezza di Azure verifica se un modulo caricato corrisponde a un profilo sospetto. Il risultato di questa verifica è indicato dal campo "SIGNATURE" (FIRMA) dell'avviso e si riflette nella gravità dell'avviso, nella descrizione dell'avviso e nella procedura di correzione dell'avviso. Per verificare se il modulo è legittimo o dannoso, analizzare la copia su disco del modulo di hijack. È ad esempio possibile verificare la firma digitale del file o eseguire un'analisi antivirus.

Oltre ai campi comuni descritti nella sezione "Individuato shellcode" precedente, questo avviso presenta i campi seguenti:

* SIGNATURE (FIRMA): indica se il modulo di hijack corrisponde a un profilo di comportamento sospetto.
* HIJACKEDMODULE (MODULO CONTROLLATO): nome del modulo di sistema Windows di cui è stato assunto il controllo.
* HIJACKEDMODULEPATH (PERCORSO MODULO CONTROLLATO): percorso del modulo di sistema Windows di cui è stato assunto il controllo.
* HIJACKINGMODULEPATH (PERCORSO MODULO HIJACK): percorso del modulo di hijack.

Esempio di questo tipo di avviso:

![Avviso di hijack del modulo](./media/security-center-alerts-type/security-center-alerts-type-fig3.png)

### <a name="masquerading-windows-module-detected"></a>Rilevato modulo Windows mascherato
Il malware può usare nomi comuni di file binari di sistema Windows, ad esempio SVCHOST.EXE, oppure moduli, come NTDLL.DLL, per *mimetizzarsi* e nascondere la natura del software dannoso agli amministratori di sistema. Questo avviso indica che l'analisi di dump di arresto anomalo del sistema ha rilevato che il file di dump di arresto anomalo del sistema contiene moduli che usano nomi di moduli di sistema Windows ma che non soddisfano i criteri tipici dei moduli di Windows. L'analisi della copia su disco del modulo mascherato può fornire altre informazioni in merito alla natura legittima o dannosa di questo modulo. L'analisi può includere:

* La conferma che indica che il file in questione è incluso in un pacchetto software legittimo.
* La verifica della firma digitale del file.
* L'esecuzione dell'analisi antivirus sul file.

Oltre ai campi comuni descritti nella sezione "Individuato shellcode" precedente, questo avviso presenta i campi aggiuntivi seguenti:

* DETAILS (DETTAGLI): descrive se i metadati dei moduli sono validi e se il modulo è stato caricato da un percorso di sistema.
* NAME (NOME): nome del modulo di Windows mascherato.
* PATH (PERCORSO): percorso del modulo di Windows mascherato.

Questo avviso, inoltre, estrae e visualizza determinati campi dell'intestazione del modulo PE, ad esempio "CHECKSUM" e "TIMESTAMP". Questi campi vengono visualizzati solo se sono presenti nel modulo. Per informazioni dettagliate su questi campi, vedere il documento [Microsoft PE and COFF Specification](https://msdn.microsoft.com/windows/hardware/gg463119.aspx) (Specifica dei formati PE e COFF Microsoft).

Esempio di questo tipo di avviso:

![Avviso di modulo Windows mascherato](./media/security-center-alerts-type/security-center-alerts-type-fig4.png)

### <a name="modified-system-binary-discovered"></a>Individuato file binario di sistema modificato
Il malware può modificare i file binari del sistema di base per accedere di nascosto ai dati o per essere furtivamente salvato in modo permanente in un sistema compromesso. Questo avviso indica che l'analisi di dump di arresto anomalo del sistema ha rilevato che i file binari del sistema operativo Windows di base sono stati modificati in memoria o su disco.

Gli sviluppatori di software legittimo modificano occasionalmente i moduli del sistema in memoria per motivi non dannosi, ad esempio per il pacchetto Detours o per la compatibilità delle applicazioni. Per distinguere i moduli dannosi da quelli potenzialmente legittimi, il Centro sicurezza di Azure verifica se un modulo caricato corrisponde a un profilo sospetto. Il risultato di questa verifica si riflette nella gravità dell'avviso, nella descrizione dell'avviso e nella procedura di correzione dell'avviso.

Oltre ai campi comuni descritti nella sezione "Individuato shellcode" precedente, questo avviso presenta i campi aggiuntivi seguenti:

* MODULENAME (NOME MODULO): nome del file binario di sistema modificato.
* MODULEVERSION (VERSIONE MODULO): versione del file binario di sistema modificato.

Esempio di questo tipo di avviso:

![Avviso di file binario di sistema](./media/security-center-alerts-type/security-center-alerts-type-fig5.png)

### <a name="suspicious-process-executed"></a>Processo sospetto eseguito
Centro sicurezza identifica il processo sospetto in esecuzione nella macchina virtuale di destinazione e quindi attiva un avviso. Il rilevamento non cerca un nome specifico, ma cerca i parametri del file eseguibile. Anche se l'autore dell'attacco rinomina il file eseguibile, il Centro sicurezza può quindi rilevare comunque il processo sospetto.

Esempio di questo tipo di avviso:

![Avviso di processo sospetto](./media/security-center-alerts-type/security-center-alerts-type-fig6-new.png)

### <a name="multiple-domain-accounts-queried"></a>Tentativi ripetuti di query sugli account di dominio
Il Centro sicurezza può rilevare tentativi di query ripetuti sugli account di dominio di Active Directory, attività in genere eseguita dagli utenti malintenzionati durante la ricognizione della rete. Gli utenti malintenzionati possono sfruttare questa tecnica per eseguire query sul dominio e identificare gli utenti, gli account di amministrazione del dominio, i computer che fungono da controller di dominio e anche potenziali relazioni di trust di dominio con altri domini.

Esempio di questo tipo di avviso:

![Avviso di tentativi di query multipli su account di dominio](./media/security-center-alerts-type/security-center-alerts-type-fig7-new.png)

### <a name="local-administrators-group-members-were-enumerated"></a>Sono stati enumerati i membri del gruppo Administrators locale

Il Centro sicurezza attiverà un avviso quando si verifica l'evento di sicurezza 4798 in Windows 10 e Windows Server 2016. L'evento si verifica quando i gruppi Administrators locali vengono enumerati, attività in genere eseguita dagli utenti malintenzionati durante la ricognizione della rete. Gli utenti malintenzionati possono sfruttare questa tecnica per eseguire una query l'identità degli utenti con privilegi amministrativi.

Esempio di questo tipo di avviso:

![Amministratore locale](./media/security-center-alerts-type/security-center-alerts-type-fig14-new.png)

### <a name="anomalous-mix-of-upper-and-lower-case-characters"></a>Combinazione anomala di lettere maiuscole e minuscole

Il Centro sicurezza attiva un avviso quando rileva l'uso di una combinazione di lettere maiuscole e minuscole nella riga di comando. Alcuni utenti malintenzionati possono usare questa tecnica per nascondersi da regole della macchina con distinzione tra maiuscole e minuscole o basate su hash.

Esempio di questo tipo di avviso:

![Combinazione anomala](./media/security-center-alerts-type/security-center-alerts-type-fig15-new.png)

### <a name="suspected-kerberos-golden-ticket-attack"></a>Sospetto attacco con Golden Ticket Kerberos

Una chiave [krbtgt](https://technet.microsoft.com/library/dn745899.aspx) compromessa può essere usata da un utente malintenzionato per creare "Golden Ticket" Kerberos, che consentono all'utente malintenzionato stesso di rappresentare qualsiasi utente. Il Centro sicurezza attiverà un avviso quando rileva questo tipo di attività.

> [!NOTE] 
> Per altre informazioni sui Golden Ticket Kerberos, vedere [Windows 10 credential theft mitigation guide](http://download.microsoft.com/download/C/1/4/C14579CA-E564-4743-8B51-61C0882662AC/Windows%2010%20credential%20theft%20mitigation%20guide.docx) (Guida alla prevenzione del furto di credenziali in Windows 10).

Esempio di questo tipo di avviso:

![Golden ticket](./media/security-center-alerts-type/security-center-alerts-type-fig16-new.png)

### <a name="suspicious-account-created"></a>Creato account sospetto

Centro sicurezza attiverà un avviso quando viene creato un account molto somigliante a un account predefinito esistente con privilegi amministrativi. Questa tecnica può essere usata da utenti malintenzionati per creare un account non autorizzato e sfuggire a una verifica umana.
 
Esempio di questo tipo di avviso:

![Account sospetto](./media/security-center-alerts-type/security-center-alerts-type-fig17-new.png)

### <a name="suspicious-firewall-rule-created"></a>Creata regola del firewall sospetta

Gli utenti malintenzionati possono provare ad aggirare la sicurezza dell'host creando regole del firewall personalizzate per consentire ad applicazioni dannose di comunicare con i server di comando e controllo o di avviare attacchi nella rete tramite l'host compromesso. Il Centro sicurezza attiverà un avviso quando rileva che è stata creata una nuova regola del firewall da un file eseguibile in una posizione sospetta.
 
Esempio di questo tipo di avviso:

![Regola del firewall](./media/security-center-alerts-type/security-center-alerts-type-fig18-new.png)

### <a name="suspicious-combination-of-hta-and-powershell"></a>Combinazione sospetta di HTA e PowerShell

Il Centro sicurezza attiverà un avviso quando rileva che un host HTA (HTML Application Host) di Microsoft sta avviando comandi di PowerShell. Si tratta di una tecnica usata dagli utenti malintenzionati per avviare script di PowerShell dannosi.
 
Esempio di questo tipo di avviso:

![HTA e PS](./media/security-center-alerts-type/security-center-alerts-type-fig19-new.png)


## <a name="network-analysis"></a>Analisi di rete
Il sistema di rilevamento delle minacce di rete del Centro sicurezza funziona mediante la raccolta automatica di informazioni sulla sicurezza dal traffico IPFIX (Internet Protocol Flow Information Export) di Azure. Per identificare le minacce, analizza queste informazioni, correlando spesso quelle raccolte da più origini.

### <a name="suspicious-outgoing-traffic-detected"></a>Rilevamento di traffico in uscita sospetto
I dispositivi di rete possono essere individuati e profilati così come altri tipi di sistemi. L'attacco ha in genere inizio con la scansione o la scansione sistematica delle porte. Nell'esempio successivo viene rilevato traffico SSH (Secure Shell) sospetto da una macchina virtuale. In questo scenario è possibile che si verifichi un attacco di forza bruta SSH o un attacco di scansione sistematica delle porte contro una risorsa esterna.

![Avviso per traffico in uscita sospetto](./media/security-center-alerts-type/security-center-alerts-type-fig8.png)

Questo avviso fornisce informazioni utili per identificare la risorsa usata per avviare l'attacco. L'avviso fornisce anche informazioni per identificare il computer compromesso, l'ora di rilevamento e il protocollo e la porta usati. Il pannello contiene anche un elenco dei passaggi di correzione che possono essere usati per attenuare il problema.

### <a name="network-communication-with-a-malicious-machine"></a>Comunicazione di rete con un computer dannoso
Sfruttando i feed di intelligence per le minacce di Microsoft, il Centro sicurezza di Azure può rilevare i computer compromessi che comunicano con indirizzi IP dannosi. In molti casi l'indirizzo dannoso corrisponde a un centro di comando e controllo. In questo caso, il Centro sicurezza ha rilevato la comunicazione con il malware Pony Loader, noto anche come [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF).

![Avviso di comunicazione di rete](./media/security-center-alerts-type/security-center-alerts-type-fig9.png)

L'avviso contiene informazioni che consentono di identificare la risorsa usata per avviare l'attacco, la risorsa che ha subito attacchi, l'IP vittima, l'IP dell'utente malintenzionato e la data e l'ora di rilevamento.

> [!NOTE]
> Gli indirizzi IP attivi sono stati rimossi dallo screenshot per motivi di privacy.
>
>

### <a name="possible-outgoing-denial-of-service-attack-detected"></a>Rilevato possibile attacco Denial of Service in uscita
Il traffico di rete anomalo generato da una macchina virtuale può indurre il Centro sicurezza ad attivare un avviso di potenziale attacco Denial of Service.

Esempio di questo tipo di avviso:

![DoS in uscita](./media/security-center-alerts-type/security-center-alerts-type-fig10-new.png)

## <a name="resource-analysis"></a>Analisi delle risorse
L'analisi delle risorse del Centro sicurezza si concentra sui servizi PaaS (Platform as a Service), ad esempio l'integrazione con la funzione di [rilevamento delle minacce nel database SQL di Azure](../sql-database/sql-database-threat-detection.md). In base ai risultati dell'analisi di queste aree, il Centro sicurezza attiva un avviso correlato alle risorse.

### <a name="potential-sql-injection"></a>Potenziale attacco SQL injection
In un attacco SQL injection, il malware viene inserito in stringhe che vengono successivamente passate a un'istanza di SQL Server per l'analisi e l'esecuzione. È consigliabile verificare la presenza di vulnerabilità a questo tipo di attacco in qualsiasi procedura che crea istruzioni SQL, perché SQL Server esegue tutte le query sintatticamente valide che riceve. Il rilevamento delle minacce SQL usa Machine Learning, l'analisi del comportamento e il rilevamento delle anomalie per individuare eventi sospetti che possono verificarsi nei database SQL di Azure. Ad esempio:

* Tentativo di accesso al database da parte di un ex dipendente
* Attacchi SQL injection
* Accesso insolito al database di produzione da parte di un utente da casa

![Avviso di potenziale SQL injection](./media/security-center-alerts-type/security-center-alerts-type-fig11.png)

Le informazioni disponibili in questo avviso sono utili per identificare la risorsa attaccata, l'ora di rilevamento e lo stato dell'attacco. L'avviso fornisce anche un collegamento a procedure di indagine aggiuntive.

### <a name="vulnerability-to-sql-injection"></a>Vulnerabilità agli attacchi SQL injection
Questo avviso viene generato in caso di rilevamento di un errore dell'applicazione in un database. L'avviso potrebbe indicare una possibile vulnerabilità ad attacchi SQL injection.

![Avviso di potenziale SQL injection](./media/security-center-alerts-type/security-center-alerts-type-fig12-new.png)

### <a name="unusual-access-from-unfamiliar-location"></a>Accesso da posizione insolita
Questo avviso viene generato quando nel server è stato rilevato un evento di accesso da un indirizzo IP insolito non osservato nell'ultimo periodo.

![Avviso di accesso insolito](./media/security-center-alerts-type/security-center-alerts-type-fig13-new.png)

## <a name="contextual-information"></a>Informazioni contestuali
Durante l'analisi, gli analisti hanno bisogno di un contesto aggiuntivo per raggiungere un verdetto sulla natura della minaccia e su come attenuarla.  Ad esempio, è stata rilevata un'anomalia di rete, ma senza conoscere cos'altro sta accadendo in rete o alla risorsa interessata è difficile comprendere quali azioni mettere in atto. Per supportare l'analisi, un evento imprevisto della sicurezza può includere artefatti, eventi correlati e informazioni che possono rivelarsi utili. La disponibilità di ulteriori informazioni varia in base al tipo di minaccia rilevata e alla configurazione dell'ambiente e non è garantita per tutti gli eventi imprevisti di sicurezza.

Se sono disponibili ulteriori informazioni, verrà visualizzato nell'evento imprevisto di sicurezza sotto l'elenco degli avvisi. Potrebbero essere riportate informazioni come:

- Eventi di cancellazione di log
- Dispositivi Plug and Play collegati da dispositivi sconosciuti
- Avvisi non operativi 

![Avviso di accesso insolito](./media/security-center-alerts-type/security-center-alerts-type-fig20.png) 


## <a name="see-also"></a>Vedere anche
In questo articolo sono stati descritti i diversi tipi di avvisi di sicurezza del Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Gestione degli eventi imprevisti della sicurezza nel Centro sicurezza di Azure](security-center-incident.md)
* [Funzionalità di rilevamento del Centro sicurezza di Azure](security-center-detection-capabilities.md)
* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md)
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.

