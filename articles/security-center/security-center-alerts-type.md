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
ms.date: 12/19/2017
ms.author: yurid
ms.openlocfilehash: f4614ac55cde26e921edfe41160e2766aef6bb2c
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure
Questo articolo consente di comprendere i diversi tipi di avvisi di sicurezza e le informazioni significative che ne derivano disponibili nel Centro sicurezza di Azure. Per altre informazioni sulla gestione degli avvisi e degli eventi imprevisti, vedere [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md).

Per configurare le funzionalità di rilevamento avanzato, eseguire l'aggiornamento al livello Standard del Centro sicurezza di Azure. È disponibile una versione di valutazione gratuita di 60 giorni. Per eseguire l'aggiornamento, selezionare il **Piano tariffario** nei [criteri di sicurezza](security-center-policies.md). Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Il Centro sicurezza ha rilasciato un'anteprima limitata di un nuovo set di strumenti di rilevamento, che sfruttano i record di controllo, un framework di controllo comune, per rilevare comportamenti dannosi nei computer Linux. Inviare un messaggio di posta elettronica con l'ID sottoscrizione a [Microsoft](mailto:ASC_linuxdetections@microsoft.com) per accedere all'anteprima.

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


### <a name="event-analysis"></a>Analisi di eventi
Il Centro sicurezza usa l'analisi avanzata per identificare le risorse compromesse in base all'analisi del registro eventi delle macchine virtuali, ad esempio eventi di creazione di processi ed eventi di accesso. Esiste inoltre una correlazione con altri segnali per verificare la presenza di elementi a riprova di una campagna su larga scala.

* **Esecuzione di processo sospetto rilevata**: gli utenti malintenzionati provano spesso a eseguire malware senza rilevamento mascherandolo da processo non dannoso. Avvisi simili a questi indicano che l'esecuzione di un processo corrispondeva a uno dei modelli seguenti:
    * È stato eseguito un processo usato per scopi dannosi. Anche se i singoli comandi possono sembrare non dannosi, l'avviso viene classificato in base a un'aggregazione di questi comandi. 
    * È stato eseguito un processo da una posizione non comune.
    * È stato eseguito un processo da una posizione in comune con file sospetti noti.
    * È stato eseguito un processo da un percorso sospetto.
    * È stato eseguito un processo in un contesto anomalo.
    * È stato eseguito un processo da un account insolito
    * È stato eseguito un processo con un'estensione sospetta.
    * È stato eseguito un processo con un'estensione doppia sospetta.
    * È stato eseguito un processo con un carattere da destra a sinistra sospetto nel nome del file.
    * È stato eseguito un processo con un nome molto simile, anche se diverso, a quello di un processo di uso molto frequente
    * È stato eseguito un processo il cui nome corrisponde a quello di uno strumento di attacco noto.
    * È stato eseguito un processo con un nome casuale.
    * È stato eseguito un processo con un'estensione sospetta.
    * È stato eseguito un file nascosto.
    * È stato eseguito un processo come figlio di un altro processo non correlato.
    * Un processo insolito è stato creato da un processo di sistema.
    * Un processo anomalo è stato avviato dal servizio Windows Update.
    * È stato eseguito un processo con una riga di comando insolita, il che è stato associato al fatto che viene assunto il controllo di processi legittimi per l'esecuzione di contenuto dannoso.
    * È stato eseguito un tentativo dalla riga di comando di avviare tutti i file eseguibili (*.exe) in una directory.
    * Un processo è stato eseguito dall'utilità PsExec, che può essere usata per eseguire processi in remoto.
    * Il file eseguibile padre di Apache Tomcat® (Tomcat#.exe) è stato usato per avviare processi figlio sospetti, che possono ospitare o avviare comandi dannosi.
    * "Risoluzione problemi compatibilità programmi" di Microsoft Windows (pcalua.exe) è stato usato per avviare un codice eseguibile, che potrebbe essere dannoso. 
    * È stato rilevato un burst di terminazione di un processo sospetto.
    * Il processo di sistema SVCHOST è stato eseguito in un contesto anomalo.
    * Il processo di sistema SVCHOST è stato eseguito in un gruppo di servizi raro.
    * È stata eseguita una riga di comando sospetta.
    * Uno script di PowerShell ha caratteristiche in comune con script sospetti noti.
    * È stato eseguito un cmdlet di PowerShell Powersploit dannoso noto.
    * Un utente SQL predefinito ha eseguito un processo insolito
    * È stato rilevato un file eseguibile con codifica in base 64, che potrebbe indicare che un utente malintenzionato sta provando a evitare il rilevamento creando un file eseguibile in tempo reale tramite una sequenza di comandi.

* **Attività della risorsa RDP sospetta**: gli utenti malintenzionati sottopongono spesso ad attacchi di forza bruta le porte di gestione aperte, ad esempio RDP. Avvisi simili a questi indicano un'attività di accesso Desktop remoto sospetta:
    * Sono stati tentati accessi Desktop remoto.
    * Sono stati tentati accessi Desktop remoto usando account non validi.
    * Sono stati tentati accessi Desktop remoto al computer, alcuni dei quali hanno avuto esito positivo.
* **Attività della risorsa SSH sospetta**: gli utenti malintenzionati sottopongono spesso ad attacchi di forza bruta le porte di gestione aperte, ad esempio SSH. Avvisi simili a questi indicano un'attività di accesso SSH sospetta:
    * Sono stati tentati accessi SSH non riusciti.
    * Sono stati tentati accessi SSH, alcuni dei quali hanno avuto esito positivo.
* **Valore del Registro di sistema WindowPosition sospetto**: un avviso simile a questo indica che è stata tentata una modifica alla configurazione del Registro di sistema WindowPosition, che potrebbe rivelare che le finestre dell'applicazione vengono nascoste in sezioni non visibili del desktop.
* **Potenziale tentativo di ignorare AppLocker**: AppLocker può essere usato per limitare i processi che possono essere eseguiti in Windows, limitando l'esposizione al malware. Un avviso simile a questo indica un possibile tentativo di ignorare le restrizioni di AppLocker usando file eseguibili considerati attendibili (consentiti dai criteri di AppLocker) per eseguire un codice non attendibile.
* **Comunicazioni della named pipe sospette**: un avviso simile a questo indica che sono stati scritti dati in una named pipe locale da un comando della console di Windows. È noto che le named pipe vengono usate dagli utenti malintenzionati per comunicare con una struttura dannosa e gestirla.
* **Decodifica di un file eseguibile con lo strumento certutil.exe predefinito**: un avviso simile a questo indica che un'utilità dell'account predefinito Administrator, certutil.exe, è stata usata per decodificare un file eseguibile. È noto che gli utenti malintenzionati usano in modo improprio la funzionalità degli strumenti di amministrazione legittimi per eseguire azioni dannose, ad esempio usando uno strumento come certutil.exe per decodificare un file eseguibile dannoso che verrà quindi eseguito successivamente.
* **Un log eventi è stato cancellato**: un avviso simile a questo indica un'operazione sospetta di cancellazione di un log eventi, che viene spesso usata dagli utenti malintenzionati per cercare di coprire le proprie tracce.
* **Disabilitazione ed eliminazione di file di log IIS**: un avviso simile a questo indica che i file di log IIS sono stati disabilitati e/o eliminati. Tale operazione viene spesso usata dagli utenti malintenzionati per cercare di coprire le proprie tracce.
* **Eliminazione sospetta di file**: un avviso simile a questo indica un'eliminazione sospetta di file, che può essere usata da un utente malintenzionato per rimuovere la prova di file binari dannosi.
* **Tutte le copie shadow dei file sono state eliminate**: un avviso simile a questo indica che le copie shadow sono state eliminate.
* **Un file della cronologia è stato cancellato**: un avviso simile a questo indica che il file di log della cronologia di un comando è stato cancellato. Tale operazione può essere usata da un utente malintenzionato per coprire le proprie tracce.
* **Comandi sospetti di pulizia file**: un avviso simile a questo indica una combinazione di comandi systeminfo usati per eseguire un'attività di pulizia automatica successiva a una compromissione.  Anche se *systeminfo.exe* è uno strumento legittimo di Windows, è raro che venga eseguito due volte in successione, seguito da una comando di eliminazione come in questo caso.
* **Creazione sospetta di account**: un avviso simile a questo indica che è stato creato un account molto somigliante a un account predefinito esistente con privilegi amministrativi. Questa tecnica può essere usata da utenti malintenzionati per creare un account non autorizzato senza essere rilevati. 
* **Attività di accesso sospetta**: un avviso simile a questo indica un'attività di accesso insolita, che può suggerire un attacco di forza bruta SMB (Server Message Block). Se la risorsa interessata funge da server IIS, questo avviso potrebbe essere generato da una specifica configurazione di autenticazione IIS legittima.
* **Attività di copia Shadow del volume sospetta**: un avviso simile a questo indica un'attività di eliminazione della copia shadow sulla risorsa. Copia Shadow del volume è un importante elemento che archivia gli snapshot di dati. Questa attività, anche se in genere è associata a Ransowmare, può tuttavia essere legittima.
* **Metodo di salvataggio permanente del Registro di sistema di Windows**: un avviso simile a questo indica un tentativo di salvare in modo permanente un file eseguibile nel Registro di sistema di Windows. Il malware usa spesso tale tecnica per superare un avvio.
* **Windows Firewall è stato disabilitato**: un avviso simile a questo indica che Windows Firewall è stato disabilitato.
* **Nuova regola del firewall sospetta**: un avviso simile a questo indica che è stata aggiunta una nuova regola del firewall tramite *netsh.exe* per consentire il traffico da un file eseguibile in una posizione sospetta.
* **Un nuovo utente è stato aggiunto al gruppo Administrators**: un avviso simile a questo indica che un nuovo utente è stato aggiunto al gruppo dell'amministratore locale.
* **È stato creato un nuovo servizio**: un avviso simile a questo indica che è stato creato un nuovo servizio.
* **Esecuzioni XCOPY sospette**: un avviso simile a questo indica una serie di esecuzioni XCOPY che potrebbero segnalare che uno dei computer è stato compromesso ed è stato usato per propagare il malware.
* **Eliminazione delle note legali visualizzate dagli utenti all'accesso**: un avviso simile a questo indica una modifica alla chiave del Registro di sistema che determina se le note legali vengono visualizzate dagli utenti quando effettuano l'accesso. Si tratta di un'attività comunemente eseguita dagli utenti malintenzionati dopo avere compromesso un host.
* **Rilevata combinazione anomala di caratteri maiuscoli e minuscoli nella riga di comando**: un avviso simile a questo indica l'uso di una combinazione di caratteri maiuscoli e minuscoli nella riga di comando, che è una tecnica usata dagli utenti malintenzionati per nascondersi da regole del computer con distinzione tra maiuscole e minuscole o basate su hash.
* **Riga di comando offuscata**: un avviso simile a questo indica che indicatori sospetti di offuscamento sono stati rilevati nella riga di comando.
* **Tentativi ripetuti di query sugli account di dominio**: gli utenti malintenzionati spesso eseguono query sugli account di dominio AD mentre eseguono una ricognizione degli utenti, degli account amministratore di dominio, dei controller di dominio e delle relazioni di trust tra i domini. Questo avviso indica che sono state eseguite query su un numero insolito di account di dominio distinti in un breve periodo di tempo.
* **Possibile attività di ricognizione locale**: un avviso simile a questo indica che è stata eseguita una combinazione di comandi systeminfo associati a un'attività di ricognizione.  Anche se *systeminfo.exe* è uno strumento legittimo di Windows, è raro che venga eseguito due volte in successione.
* **Possibile esecuzione del file eseguibile keygen**: un avviso simile a questo indica che è stato eseguito un processo il cui nome può indicare uno strumento keygen. Tali strumenti vengono in genere usati per vanificare i meccanismi di gestione licenze software, ma il download è spesso in bundle con altro software dannoso. 
* **Esecuzione sospetta tramite rundll32.exe**: un avviso simile a questo indica che è stato usato rundll32.exe per eseguire un processo con un nome non comune, coerente con lo schema di denominazione dei processi usato dagli utenti malintenzionati per installare un impianto di prima fase in un host compromesso.
* **Combinazione sospetta di HTA e PowerShell**: un avviso simile a questo indica che un host applicazioni HTML Microsoft (HTA) sta avviando comandi di PowerShell. Si tratta di una tecnica usata dagli utenti malintenzionati per avviare script di PowerShell dannosi.
* **Modifica a una chiave del Registro di sistema che può essere usata in modo improprio per ignorare il controllo dell'account utente**: un avviso simile a questo indica che è stata modificata una chiave del Registro di sistema che può essere usata in modo improprio per ignorare il controllo dell'account utente. Tale operazione viene spesso usata dagli utenti malintenzionati per passare dall'accesso senza privilegi (utente standard) a quello con privilegi (ad esempio, amministratore) in un host compromesso.
* **Uso di nome di dominio sospetto nella riga di comando**: un avviso simile a questo indica che è stato usato un nome di dominio sospetto, che può dimostrare che un utente malintenzionato sta ospitando strumenti dannosi e ha gli endpoint per il comando, il controllo e l'esfiltrazione dei dati. 
* **Un account è stato creato in più host nell'arco di 24 ore**: un avviso simile a questo indica che è stato fatto un tentativo di creare lo stesso account utente in più host. Tale tentativo può suggerire che un utente malintenzionato si sta muovendo lateralmente nella rete dopo che una o più entità di rete sono state compromesse.
* **Uso sospetto di CACLS per ridurre lo stato di sicurezza del sistema**: un avviso simile a questo indica che l'elenco di controllo di accesso delle modifiche (CACLS) è stato modificato. Questa tecnica viene spesso usata dagli utenti malintenzionati per concedere l'accesso completo ai file binari di sistema, ad esempio ftp.exe, net.exe, wscript.exe e così via. 
* **Sospetti parametri di attacco con Golden Ticket Kerberos**: un avviso simile a questo indica che sono stati eseguiti parametri della riga di comando coerenti con un attacco con Golden Ticket Kerberos. Una chiave krbtgt compromessa può essere usata da un utente malintenzionato per rappresentare qualsiasi utente. 
* **Abilitazione della chiave del Registro di sistema WDigest UseLogonCredential**: un avviso simile a questo indica che la chiave del Registro di sistema è stata modificata per consentire l'archiviazione delle credenziali di accesso come testo non crittografato nella memoria LSA, da dove possono quindi essere raccolte.
* **Uso potenzialmente sospetto dello strumento Telegram**: un avviso simile a questo indica l'installazione di Telegram, un servizio di messaggistica istantanea gratuito basato sul cloud usato dagli utenti malintenzionati per trasferire file binari dannosi in un altro computer, telefono o tablet.
* **Creazione di un nuovo punto di estendibilità di avvio automatico**: un avviso simile a questo indica la creazione di un nuovo punto di estendibilità di avvio automatico, che causa l'avvio automatico del nome di processo identificato nella riga di comando e che può essere usato da un utente malintenzionato per eseguire il salvataggio permanente. 
* **Modifiche sospette a Set-ExecutionPolicy e WinRM**: un avviso simile a questo indica modifiche di configurazione, associate all'uso della shell Web ChinaChopper dannosa.
* **Disabilitazione di servizi critici**: un avviso simile a questo indica che il comando "net.exe stop" è stato usato per arrestare servizi critici, ad esempio SharedAccess o il Centro sicurezza PC Windows. 
* **Uso sospetto dell'opzione -s dell'FTP**: un avviso simile a questo indica l'uso dell'opzione "-s" dell'FTP, che può essere usata dal malware per connettersi a un server FTP remoto e scaricare file binari dannosi aggiuntivi.
* **Preparazione dell'esfiltrazione di documenti tramite la backdoor di IIS**: un avviso simile a questo indica che è in corso la raccolta e la preparazione di documenti per l'esfiltrazione.
* **Esecuzione sospetta del comando VBScript.Encode**: un avviso simile a questo indica che è stato eseguito il comando *VBScript.Encode*, che codifica gli script in testo non leggibile, rendendo più difficile agli utenti esaminare il codice.
* **Allocazione di un oggetto HTTP VBScript**: un avviso simile a questo indica la creazione di un file VBScript tramite il prompt dei comandi, che può essere usato per scaricare file dannosi.
* **Attacco basato su tasti permanenti**: un avviso simile a questo indica che è possibile che un utente malintenzionato stia compromettendo un elemento binario di accessibilità (ad esempio, tasti permanenti, tastiera su schermo, assistente vocale) per fornire l'accesso tramite backdoor.
* **Indicatori ransomware Petya**: un avviso simile a questo indica che sono state osservate le tecniche associate al ransomware Petya.
* **È stato caricato un modulo kernel**: un avviso simile a questo indica che è stato caricato un modulo kernel.
* **È stato rimosso un modulo kernel**: un avviso simile a questo indica che è stato rimosso un modulo kernel.
* **Accesso anomalo a un computer**: un avviso simile a questo indica che un utente ha eseguito l'accesso da un indirizzo IP insolito.
* **Un file è stato scaricato ed eseguito**: un avviso simile a questo indica che un file è stato scaricato nel computer, gli sono stati assegnati privilegi di esecuzione e quindi è stato eseguito.
* **Tentativo di disabilitare l'interfaccia di analisi antimalware**: un avviso simile a questo indica un tentativo di disabilitare l'interfaccia di analisi antimalware per disabilitare il rilevamento antimalware.
* **Indicatori ransomware**: un avviso simile a questo indica un'attività sospetta in genere associata a un ransomware relativo alla schermata di blocco e alla crittografia.
* **File di output di raccolta delle tracce sospetto**: un avviso simile a questo indica che una traccia (ad esempio, di un'attività di rete) è stata raccolta e visualizzata in un tipo di file insolito.
* **Software a rischio elevato**: un avviso simile a questo indica l'uso di software associato all'installazione di malware. Gli utenti malintenzionati spesso inseriscono il malware in un pacchetto con strumenti di per sé non dannosi, come quello mostrato in questo avviso, ed eseguono un'installazione invisibile all'utente del malware in background.
* **Creazione di file sospetta**: un avviso simile a questo indica la creazione o l'esecuzione di un processo usato dagli utenti malintenzionati per scaricare malware aggiuntivo in un host compromesso dopo l'apertura di un allegato in un documento di phishing.
* **Credenziali sospette nella riga di comando**: un avviso simile a questo indica una password sospetta usata per eseguire un file. Questa tecnica è stata usata dagli utenti malintenzionati per eseguire il malware Pirpi.
* **Possibile esecuzione di dropper di malware**: un avviso simile a questo indica un nome di file usato da utenti malintenzionati per installare il malware.
* **Esecuzione sospetta tramite rundll32.exe**: un avviso simile a questo indica l'uso di rundll32.exe per eseguire notepad.exe o reg.exe, in modo coerente con la tecnica di inserimento processi usata dagli utenti malintenzionati.
* **Argomenti della riga di comando sospetti**: un avviso simile a questo indica argomenti della riga di comando sospetti usati insieme a una shell inversa usata dal gruppo di attività HYDROGEN.
* **Credenziali di documento sospette**: un avviso simile a questo indica un hash delle password pre-calcolato comune sospetto usato dal malware usato per eseguire un file.
* **Costruzione di script di PowerShell dinamica**: un avviso simile a questo indica che è in corso la costruzione dinamica di uno script di PowerShell. Gli utenti malintenzionati usano questa tecnica per la compilazione progressiva di uno script per potersi sottrarre ai sistemi di rilevamento delle intrusioni.
* **Indicatori Metasploit**: un avviso simile a questo indica un'attività associata al framework Metasploit, in cui sono disponibili svariate funzionalità e strumenti per gli utenti malintenzionati.
* **Manomissione di SENSE**: un avviso simile a questo indica un tentativo di disabilitare SENSE, il servizio di monitoraggio della sicurezza di Windows Defender ATP.
* **Attività sospetta di un account**: un avviso simile a questo indica un tentativo di connettersi a un computer usando un account compromesso di recente.
* **Possibile accesso sospetto basato su attività di pianificazione**: un avviso simile a questo indica che è stato eseguito un processo CRON, che può essere usato da utenti malintenzionati per eseguire programmi dannosi in base a una pianificazione.
* **Possibile accesso sospetto al file della cronologia dei comandi**: un avviso simile a questo indica un accesso insolito al file della cronologia dei comandi.
* **Creazione di account**: un avviso simile a questo indica una creazione di un nuovo account nel computer.
* **Modifica dell'impostazione di Bash**: un avviso simile a questo indica che è stato eseguito l'accesso a un file del profilo Bash. Tale operazione può suggerire che un utente malintenzionato sta provando a eseguire programmi dannosi in base a una pianificazione.
* **Sequenza sospetta di tentativi sudo non riusciti**: un avviso simile a questo indica una sequenza di comandi sudo non riusciti, osservabile spesso nei tentativi di attacco di forza bruta il cui scopo è l'escalation dei privilegi tramite utenti non autorizzati.
* **Tentativi sudo riusciti sospetti**: un avviso simile a questo indica una sequenza di comandi sudo non riusciti seguito da un tentativo sudo riuscito, osservabile spesso nei tentativi di attacco di forza bruta il cui scopo è l'escalation dei privilegi tramite utenti non autorizzati. 
* **È stato aggiunto un nuovo utente al gruppo sudoers**: un avviso simile a questo indica che un utente è stato aggiunto al gruppo sudoers per consentire ai membri di eseguire i comandi con privilegi elevati.
* **Accesso alla rete con credenziali in testo non crittografato**: un avviso simile a questo indica che è stato osservato un accesso alla rete in cui la password è stata inviata in rete in formato non crittografato. Questo comportamento è comune per gli accessi da uno script ASP tramite ADVAPI o quando un utente accede ad IIS usando la modalità di autenticazione di base di IIS. L'autenticazione di base non è il metodo consigliato a meno che non ne venga eseguito il wrapping in un livello di crittografia come SSL (ad esempio, usando solo connessioni HTTPS).

### <a name="crash-analysis"></a>Analisi degli arresti anomali


L'analisi della memoria del dump di arresto anomalo è un metodo usato per rilevare malware sofisticato in grado di eludere le soluzioni di sicurezza tradizionali. Varie forme di malware provano a ridurre le probabilità di essere rilevate dai prodotti antivirus non scrivendo mai sul disco oppure crittografando i componenti software scritti sul disco. Questa tecnica rende il malware difficile da rilevare con gli approcci antimalware tradizionali. Tuttavia, questo tipo di malware può essere rilevato tramite l'analisi della memoria, perché per funzionare il malware deve lasciare tracce in memoria.

Quando il software si arresta in modo anomalo, un dump di arresto anomalo acquisisce una porzione della memoria al momento dell'arresto. L'arresto anomalo può essere causato da malware o da problemi generali del sistema o delle applicazioni. Analizzando la memoria nel dump di arresto anomalo, il Centro sicurezza può rilevare le tecniche usate per sfruttare le vulnerabilità del software, accedere ai dati riservati e rimanere permanentemente all'interno di un computer infetto in modo furtivo. Questa operazione viene eseguita con un impatto minimo sulle prestazioni degli host perché l'analisi viene eseguita dal Centro sicurezza in back-end.

* **Individuato attacco di code injection**: un attacco di code injection consiste nell'inserimento di moduli eseguibili in processi o thread in esecuzione. Questa tecnica è usata da software dannoso per accedere ai dati e nasconderne o impedirne la rimozione, ad esempio con il salvataggio permanente. Questo avviso indica che nel dump di arresto anomalo del sistema è presente un modulo inserito. Gli sviluppatori di software legittimo eseguono occasionalmente operazioni di code injection per motivi non dannosi, ad esempio per modificare o estendere un componente esistente del sistema operativo o di un'applicazione. Per distinguere tra moduli inseriti dannosi e non dannosi, il Centro sicurezza verifica se il modulo inserito corrisponde o meno a un profilo di comportamento sospetto. Il risultato di questa verifica è indicato dal campo "SIGNATURE" (FIRMA) dell'avviso e si riflette nella gravità dell'avviso, nella descrizione dell'avviso e nella procedura di correzione dell'avviso.
* **Segmento di codice sospetto**: l'avviso di segmento di codice sospetto indica che un segmento di codice è stato allocato con metodi non standard, come quelli usati da reflective injection e hollowing del processo. Vengono elaborate caratteristiche aggiuntive del segmento di codice per offrire il contesto in relazione alle funzionalità e ai comportamenti del segmento di codice segnalato.
* **Individuato attacco shellcode**: uno shellcode è il payload che viene eseguito dopo che il malware ha sfruttato una vulnerabilità del software. Questo avviso indica che l'analisi di dump di arresto anomalo del sistema ha rilevato codice eseguibile che presenta un comportamento comunemente adottato dai payload dannosi. Sebbene anche il software non dannoso possa mostrare comportamenti simili, questo non fa generalmente parte delle normali procedure di sviluppo software.
* **Individuato hijack del modulo**: Windows usa le librerie a collegamento dinamico (DLL) per consentire al software di usare funzionalità del sistema di Windows comuni. L'hijack della DLL si verifica quando il malware modifica l'ordine di caricamento della DLL per caricare payload dannosi in memoria, dove è possibile eseguire codice arbitrario. Questo avviso indica che l'analisi del dump di arresto anomalo del sistema ha rilevato un modulo con nome simile caricato da due percorsi diversi. Uno dei percorsi caricati proviene da un percorso binario di sistema di Windows. Gli sviluppatori di software legittimo cambiano occasionalmente l'ordine di caricamento della DLL per motivi non dannosi, ad esempio per instrumentare o estendere il sistema operativo Windows o un'applicazione Windows. Per distinguere le modifiche dannose da quelle potenzialmente non dannose all'ordine di caricamento della DLL, il Centro sicurezza verifica se un modulo caricato corrisponde a un profilo sospetto. 
* **Rilevato modulo Windows mascherato**: il malware può usare nomi comuni di file binari di sistema Windows, ad esempio SVCHOST.EXE, oppure moduli, come NTDLL.DLL, per mimetizzarsi e nascondere la natura del software dannoso agli amministratori di sistema. Questo avviso indica che il file di dump di arresto anomalo del sistema contiene moduli che usano nomi di moduli di sistema Windows, ma che non soddisfano i criteri tipici dei moduli di Windows. L'analisi della copia su disco del modulo mascherato può fornire altre informazioni in merito alla natura legittima o dannosa di questo modulo.
* **Individuato file binario di sistema modificato**: il malware può modificare i file binari del sistema di base per accedere di nascosto ai dati o per essere furtivamente salvato in modo permanente in un sistema compromesso. Questo avviso indica che l'analisi di dump di arresto anomalo del sistema ha rilevato che i file binari del sistema operativo Windows di base sono stati modificati in memoria o su disco. Gli sviluppatori di software legittimo modificano occasionalmente i moduli del sistema in memoria per motivi non dannosi, ad esempio per il pacchetto Detours o per la compatibilità delle applicazioni. Per distinguere i moduli dannosi da quelli potenzialmente legittimi, il Centro sicurezza verifica se un modulo caricato corrisponde a un profilo sospetto. 

## <a name="network-analysis"></a>Analisi di rete
Il sistema di rilevamento delle minacce di rete del Centro sicurezza funziona mediante la raccolta automatica di informazioni sulla sicurezza dal traffico IPFIX (Internet Protocol Flow Information Export) di Azure. Per identificare le minacce, analizza queste informazioni, correlando spesso quelle raccolte da più origini.

* **Possibili tentativi di attacco di forza bruta a SQL in ingresso**: l'analisi del traffico di rete ha rilevato una comunicazione SQL sospetta in ingresso. L'attività è coerente con i tentativi di attacco di forza bruta ai server SQL.
* **Attività di rete RDP in ingresso sospetta da più origini**: l'analisi del traffico di rete ha rilevato una comunicazione RDP (Remote Desktop Protocol) in ingresso anomala da più origini. I dati di rete campionati visualizzano in particolare IP univoci che si connettono al computer. Tale comportamento è considerato anomalo per questo ambiente. Questa attività può indicare un tentativo di attacco di forza bruta contro l'endpoint RDP da più host (botnet).
* **Attività di rete RDP in ingresso sospetta**: l'analisi del traffico di rete ha rilevato una comunicazione RDP (Remote Desktop Protocol) in ingresso anomala. I dati di rete campionati visualizzano in particolare un numero elevato di connessioni in ingresso al computer. Tale comportamento è considerato anomalo per questo ambiente. Questa attività può indicare un tentativo di attacco di forza bruta contro l'endpoint RDP.
* **Attività di rete RDP in uscita sospetta verso più destinazioni**: l'analisi del traffico di rete ha rilevato una comunicazione RDP (Remote Desktop Protocol) in uscita anomala verso più destinazioni. Questa attività può indicare che il computer è stato compromesso e viene ora usato per attacchi di forza bruta contro gli endpoint RDP esterni. Si noti che in seguito a questo tipo di attività l'IP può venire contrassegnato come dannoso dalle entità esterne.
* **Attività di rete RDP in uscita sospetta**: l'analisi del traffico di rete ha rilevato una comunicazione RDP (Remote Desktop Protocol) in uscita anomala. I dati di rete campionati visualizzano in particolare un numero elevato di connessioni in uscita dal computer. Tale comportamento è considerato anomalo per questo ambiente. Questa attività può indicare che il computer è stato compromesso e viene ora usato per attacchi di forza bruta contro gli endpoint RDP esterni. Si noti che in seguito a questo tipo di attività l'IP può venire contrassegnato come dannoso dalle entità esterne.
* **Attività di rete SSH in ingresso sospetta**: l'analisi del traffico di rete ha rilevato una comunicazione SSH in ingresso anomala. I dati di rete campionati visualizzano in particolare un numero elevato di connessioni in ingresso al computer. Tale comportamento è considerato anomalo per questo ambiente. Questa attività può indicare un tentativo di attacco di forza bruta contro l'endpoint SSH.
* **Attività di rete SSH in ingresso sospetta da più origini**: l'analisi del traffico di rete ha rilevato una comunicazione SSH in ingresso anomala. I dati di rete campionati visualizzano in particolare IP univoci che si connettono al computer. Tale comportamento è considerato anomalo per questo ambiente. Questa attività può indicare un tentativo di attacco di forza bruta contro l'endpoint SSH da più host (botnet).
* **Attività di rete SSH in uscita sospetta**: l'analisi del traffico di rete ha rilevato una comunicazione SSH in uscita anomala. I dati di rete campionati visualizzano in particolare un numero elevato di connessioni in uscita dal computer. Tale comportamento è considerato anomalo per questo ambiente. Questa attività può indicare che il computer è stato compromesso e viene ora usato per attacchi di forza bruta contro gli endpoint SSH esterni. Si noti che in seguito a questo tipo di attività l'IP può venire contrassegnato come dannoso dalle entità esterne.
* **Attività di rete SSH in uscita sospetta verso più destinazioni**: l'analisi del traffico di rete ha rilevato una comunicazione SSH in uscita anomala verso più destinazioni. I dati di rete campionati visualizzano in particolare che il computer si connette a IP univoci. Tale comportamento è considerato anomalo per questo ambiente. Questa attività può indicare che il computer è stato compromesso e viene ora usato per attacchi di forza bruta contro gli endpoint SSH esterni. Si noti che in seguito a questo tipo di attività l'IP può venire contrassegnato come dannoso dalle entità esterne.
* **Comunicazione di rete con un computer dannoso rilevata**: l'analisi del traffico di rete indica che il computer ha comunicato con un possibile centro di comando e controllo.
* **Possibile computer compromesso rilevato**: l'analisi del traffico di rete ha rilevato un'attività in uscita, che potrebbe significare che sta operando nell'ambito di un botnet. L'analisi si basa sugli IP a cui le risorse accedono insieme ai record pubblici DNS.

 
## <a name="resource-analysis"></a>Analisi delle risorse

L'analisi delle risorse del Centro sicurezza si concentra sui servizi PaaS (Platform as a Service), ad esempio l'integrazione con la funzione di [rilevamento delle minacce nel database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection). In base ai risultati dell'analisi di queste aree, il Centro sicurezza attiva un avviso correlato alle risorse.

* **Potenziale attacco SQL injection**: in un attacco SQL injection, il malware viene inserito in stringhe che vengono successivamente passate a un'istanza di SQL Server per l'analisi e l'esecuzione. È consigliabile verificare la presenza di vulnerabilità a questo tipo di attacco in qualsiasi procedura che crea istruzioni SQL, perché SQL Server esegue tutte le query sintatticamente valide che riceve. Il rilevamento delle minacce SQL usa Machine Learning, l'analisi del comportamento e il rilevamento delle anomalie per individuare eventi sospetti che possono verificarsi nei database SQL di Azure. Ad esempio: 
    * Tentativo di accesso al database da parte di un ex dipendente
    * Attacchi SQL injection
    * Accesso insolito al database di produzione da parte di un utente da casa
* **Vulnerabilità agli attacchi SQL injection**: questo avviso viene generato in caso di rilevamento di un errore dell'applicazione in un database. L'avviso potrebbe indicare una possibile vulnerabilità ad attacchi SQL injection.
* **Accesso da posizione insolita**: questo avviso viene generato quando nel server è stato rilevato un evento di accesso da un indirizzo IP insolito non osservato nell'ultimo periodo.

## <a name="contextual-information"></a>Informazioni contestuali
Durante l'analisi, gli analisti hanno bisogno di un contesto aggiuntivo per raggiungere un verdetto sulla natura della minaccia e su come attenuarla.  Ad esempio, è stata rilevata un'anomalia di rete, ma senza conoscere cos'altro sta accadendo in rete o alla risorsa interessata è difficile comprendere quali azioni mettere in atto. Per supportare l'analisi, un evento imprevisto della sicurezza può includere artefatti, eventi correlati e informazioni che possono rivelarsi utili. La disponibilità di ulteriori informazioni varia in base al tipo di minaccia rilevata e alla configurazione dell'ambiente e non è garantita per tutti gli eventi imprevisti di sicurezza.

Se sono disponibili ulteriori informazioni, verrà visualizzato nell'evento imprevisto di sicurezza sotto l'elenco degli avvisi. Potrebbero essere riportate informazioni come:

- Eventi di cancellazione di log
- Dispositivi Plug and Play collegati da dispositivi sconosciuti
- Avvisi non operativi 

![Avviso di accesso insolito](./media/security-center-alerts-type/security-center-alerts-type-fig20.png) 


## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati descritti i diversi tipi di avvisi di sicurezza del Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Gestione degli eventi imprevisti della sicurezza nel Centro sicurezza di Azure](security-center-incident.md)
* [Funzionalità di rilevamento del Centro sicurezza di Azure](security-center-detection-capabilities.md)
* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md)
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.
