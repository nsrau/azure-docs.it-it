---
title: Il motivo per cui workstation sicure sono importanti - Azure Active Directory
description: Scopri perché le organizzazioni devono creare workstation sicure gestiti di Azure
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05d21910d1b3601346fbd038cbc25f8f2be61f99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110708"
---
# <a name="building-secure-workstations"></a>Compilazione di workstation sicure

Workstation isolata protetta sono estremamente importanti per la sicurezza dei ruoli sensibili, ad esempio amministratori, sviluppatori e operatori di servizi critici. Molti altri controlli di sicurezza e garanzie di esiti negativi o non hanno alcun effetto se la protezione delle workstation client sottostante è stata compromessa.

Questo documento illustra gli elementi necessari per compilare una workstation client protette con istruzioni passo passo dettagliate, incluse le procedure impostare l'avvio dei controlli di protezione. Questo tipo di workstation a volte viene chiamato una workstation amministrative con privilegi (PAW), che in questo riferimento viene usato e compilato in base. Tuttavia, il materiale sussidiario è simile alla tecnologia basata sul cloud per gestire il servizio e introduce funzionalità di sicurezza introdotte a partire da 10RS5 Windows Defender ATP di Microsoft, Azure Active Directory e Intune.

> [!NOTE]
> Questo articolo illustra il concetto di workstation sicura e la sua importanza. Se si ha già familiarità con il concetto e si vuole ignorare alla distribuzione, leggere [come distribuire una Workstation sicura](https://docs.microsoft.com/azure/active-directory/devices/howto-azure-managed-workstation).

## <a name="why-securing-workstation-access-is-important"></a>Il motivo per cui è importante proteggere l'accesso di workstation

La rapida adozione di servizi cloud e la possibilità di lavorare ovunque ha creato un nuovo metodo per lo sfruttamento. Gli utenti malintenzionati continuano a sfruttare i controlli di sicurezza debole nei dispositivi in cui gli amministratori di lavoro e sono in grado di accedere alle risorse con privilegi.

Come documentato nel [report sulle minacce di Verizon](https://enterprise.verizon.com/resources/reports/dbir/), e [Security Intelligence Report](https://aka.ms/sir) usare impropriamente con privilegi e supply chain browser sono tra i meccanismi di cinque principali usati per violare le organizzazioni e il in secondo luogo più comunemente rilevata tattica in eventi imprevisti segnalati nel 2018.

La maggior parte degli utenti malintenzionati seguono il percorso seguente:

* Ricognizione, spesso specifica di un settore, per trovare un modo per iniziare
* Analizzare le informazioni raccolte per identificare il mezzo migliore per ottenere l'accesso (infiltrazione) di una workstation valore percepito bassa
* Persistenza e osservare i mezzi per spostare [lateralmente](https://en.wikipedia.org/wiki/Network_Lateral_Movement)
* Sottrarre dati sensibili e riservati

Gli utenti malintenzionati spesso infiltrarsi i dispositivi che sembrano a basso rischio o sottovalutato per esplorazione. Questi dispositivi vulnerabili vengono quindi utilizzati per individuare un'opportunità di movimento laterale, individuare gli utenti amministratori e i dispositivi e identificare i dati importanti elevati, alle informazioni sottrarre correttamente una volta che imparano questi ruoli utente con privilegi.

![Modello tipico di compromissione](./media/concept-azure-managed-workstation/typical-timeline.png)

Questo documento offre una soluzione per proteggere i dispositivi di elaborazione isolando management e servizi per proteggere il movimento laterale o attacchi da parte di dispositivi per la produttività meno importanti. La progettazione consente di ridurre la possibilità di eseguire correttamente una violazione interrompendo la catena prima infiltrazione del dispositivo usato per gestire o accedere alle risorse cloud sensibili. La soluzione descritta utilizzerà i servizi di Azure nativi che fanno parte di stack Microsoft 365 Enterprise, tra cui:

* Intune per la gestione dei dispositivi, tra cui applicazioni e URL nell'elenco elementi consentiti
* AutoPilot per la configurazione del dispositivo e la distribuzione e aggiornamento 
* Azure AD per la gestione degli utenti, l'accesso condizionale e autenticazione a più fattori
* Windows 10 (versione corrente) per device health attestation e l'esperienza utente
* Microsoft Defender Advanced Threat Protection (ATP) per la protezione di endpoint, il rilevamento e risposta con la gestione cloud
* Accesso alle risorse con privilegi di Azure AD Privileged Identity Management per la gestione delle autorizzazioni, tra cui Just In Time (JIT)

## <a name="who-benefit-from-using-a-secure-workstation"></a>Che trae vantaggio dall'utilizzo di una workstation sicura

Tutti gli utenti e gli operatori di trarre vantaggio dall'utilizzo di una workstation sicura. Un utente malintenzionato compromette un PC o dispositivo può eseguire diverse operazioni che includono rappresentano tutti gli account memorizzati nella cache e Usa le credenziali e i token usati in tale dispositivo mentre sono connessi. Questo rischio consente di proteggere i dispositivi usati per tutti i ruoli con privilegi tra cui quelli amministrativi così importante in quanto i dispositivi in cui viene usato un account con privilegi sono le destinazioni per lo spostamento laterale e attacchi di escalation dei privilegi. Questi account possono essere usati per un'ampia gamma di asset, ad esempio:

* Amministratori dei sistemi basati su cloud e locali
* Workstation di sviluppo per sistemi critici
* Amministratore di account dei social media con rischio elevato
* Workstation estremamente riservate, ad esempio terminali di pagamento SWIFT
* Workstation di gestione di segreti industriali

Microsoft consiglia di implementare i controlli di sicurezza con privilegi elevati per workstation con privilegi in cui vengono usati per ridurre il rischio. Sono disponibili informazioni aggiuntive nel [Guida alla distribuzione di funzionalità di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [roadmap di Office 365](https://aka.ms/o365secroadmap), e [roadmap per la protezione dell'accesso con privilegi](https://aka.ms/sparoadmap)).

## <a name="why-dedicated-workstations"></a>Il motivo per cui le workstation dedicate

Sebbene sia possibile aggiungere protezione a un dispositivo esistente, è preferibile iniziare con una base sicura. A partire da un buon dispositivo noto e un set di inserisce i controlli di sicurezza noti dell'organizzazione nella posizione ideale per mantenere che aumentato a livello di sicurezza. Con il numero in continua crescita di vettori di attacco consentito da casual posta elettronica e web browser, è sempre più difficile garantire che un dispositivo può essere considerato attendibile. Questa guida può essere usata in base al presupposto di una workstation dedicata separati da standard di produttività, esplorazione, e sono state completate le attività di posta elettronica. Rimozione di produttività, esplorazione del web e indirizzo di posta elettronica da un dispositivo può avere un impatto negativo sulla produttività, ma tale salvaguardia è in genere accettabile per gli scenari in cui le attività di processo non è richiesto in modo esplicito e rischio di un evento imprevisto della sicurezza è elevato.

> [!NOTE]
> Esplorazione del Web qui fa riferimento a accesso generale ai siti Web arbitrari, ovvero un rischio elevato per diverso dall'uso di un web browser per accedere a un numero ridotto di siti Web di amministrazione ben noti per servizi come Azure, Office 365, altri provider di servizi cloud e SaaS applicazioni.

Strategie di contenimento forniscono garanzie di sicurezza maggiore, aumentando il numero e tipo dei controlli che dispone di un utente malintenzionato per superare per poter accedere a risorse sensibili. Il modello sviluppato di seguito fornisce il contenimento dei privilegi amministrativi a dispositivi specifici utilizzando un modello di privilegi a più livelli.

## <a name="supply-chain-management"></a>Gestione della supply chain

Essenziale per una workstation protetta è una soluzione della supply chain in cui è attendibile, la workstation è usare una "radice di attendibilità". Questa soluzione soddisferà la radice di attendibilità con il [Autopilot Microsoft](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) tecnologia. Per una workstation protetta Autopilot Microsoft offre la possibilità di sfruttare i dispositivi di Microsoft OEM ottimizzato per Windows 10 che forniscono uno stato noto soddisfacente dal produttore. Invece di ricreazione dell'immagine di un dispositivo che non sia attendibile, Microsoft Autopilot possono trasformare un dispositivo Windows in uno stato "aziendali", applicare le impostazioni e i criteri, installazione di App, e persino modifica dell'edizione di Windows 10 in uso (ad esempio, da Windows 10 Pro a Windows 10 Enterprise, per supportare le funzionalità avanzate).

![Livelli di workstation sicura](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>I profili e i ruoli di dispositivo

In tutto il materiale sussidiario, verranno risolti più profili di protezione e ruoli per ottenere una soluzione più sicura per gli utenti, sviluppatori e il personale IT. Questi profili sono stati allineati per supportare utenti comuni nelle organizzazioni che possono trarre vantaggio da una workstation sicura o avanzata, senza rinunciare all'equilibro usabilità e rischio. Le linee guida fornirà la configurazione delle impostazioni in base agli standard del settore accettato. Questa guida viene utilizzata per illustrare un metodo di protezione avanzata di Windows 10 e riducendo i rischi associati all'utente o dispositivo compromesso tramite le tecnologie e dei criteri per gestire i rischi e le funzionalità di sicurezza.
![Livelli di workstation sicura](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Sicurezza bassa** -una workstation standard gestita fornisce un buon punto di partenza per la maggior parte degli ambienti domestici o piccole aziendale. Questi dispositivi sono registrati in Azure AD e gestiti da Intune. Il profilo consente agli utenti di eseguire tutte le applicazioni ed esplorare qualsiasi sito Web. Ad esempio una soluzione antimalware [Defender Microsoft](https://www.microsoft.com/windows/comprehensive-security) deve essere abilitata.
* **Protezione avanzata** – è una soluzione protetta a livello di voce, bene per gli utenti privati, utenti aziendali di piccole dimensioni, nonché gli sviluppatori generali.
   * La workstation avanzata fornisce un criterio di base migliorare la sicurezza del profilo di sicurezza limitati. Questo profilo consente di soluzioni sicure lavorare con i dati dei clienti e in grado di utilizzare gli strumenti di produttività quali la verifica tramite posta elettronica ed esplorazione del web. Una workstation avanzato utilizzabile per controllare il comportamento degli utenti e utilizzo dei profili di una workstation per l'abilitazione di criteri di controllo e registrazione in Intune. In questo profilo, le workstation consentirà i controlli di sicurezza e i criteri descritti nel contenuto e distribuite in Workstation migliorato - Windows10 script (1809). La distribuzione si avvale inoltre dell'avanzate malware protection utilizzando [Advanced Threat Protection (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp)
* **Sicurezza elevata** – il mezzo più efficace per ridurre la superficie di attacco di una workstation consiste nel rimuovere la possibilità di self-administer workstation. Rimozione di diritti amministrativi locali è un passaggio che migliora la sicurezza e può influire sulla produttività se implementata in modo non corretto. Il profilo di sicurezza elevata si basa sul profilo di sicurezza avanzata con un'unica modifica considerevole, la rimozione dell'amministratore locale. Questo profilo è progettato per facilitare gli utenti che forse una basata sul profilo alto, ad esempio un dirigente o più utenti che possono avere contatto con i dati sensibili, ad esempio payroll, o l'approvazione di servizi e processi.
   * Il profilo utente di protezione elevato richiede un ambiente controllato superiore pur consentendo di eseguire le attività di produttività, ad esempio posta elettronica e mantenendo una semplice per usare l'esperienza di esplorazione del web. Gli utenti prevedono funzionalità quali i cookie, Preferiti e altri tasti di scelta rapida per il funzionamento. Tuttavia questi utenti non richiedano la possibilità di modificare o eseguire il debug proprio dispositivo e non saranno necessario installare i driver. Il profilo di sicurezza elevato viene distribuito usando l'elevata sicurezza - script di Windows 10 (1809).
* **Specializzato** : consente agli sviluppatori e gli amministratori IT un obiettivo ideale per gli utenti malintenzionati perché questi ruoli possono modificare i sistemi di interesse per gli utenti malintenzionati. La workstation specializzata accetta lo sforzo distribuito nelle workstation con protezione elevata ed emphases ulteriormente la protezione per la gestione delle applicazioni locali, limitare i siti web di internet e limitando le funzionalità di produttività elevate di rischio, ad esempio ActiveX, Java, plug-in del browser e diversi altri controlli rischiosi noti su un dispositivo Windows. In questo profilo, le workstation consentirà i controlli di sicurezza e i criteri descritti nel contenuto e della distribuzione nel DeviceConfiguration_NCSC - Windows10 script SecurityBaseline (1803).
* **Protetto** : un utente malintenzionato potrebbe compromettere un account amministrativo in genere può causare danni significativi business dal furto dei dati, l'alterazione dei dati o un'interruzione del servizio. In questo stato finalizzato, workstation consentirà tutti i controlli di sicurezza e i criteri che limitano l'utilizzo di un controllo diretto della gestione delle applicazioni locali e gli strumenti di produttività vengono rimossi. Di conseguenza, compromettere il dispositivo diventa più difficile come posta elettronica e social network sono bloccati che riflettono il modo più comune di attacchi di phishing possono avere esito positivo.  La workstation protetta può essere distribuita con la Workstation Secure - script SecurityBaseline Windows10 (1809).

   ![Workstation protetti](./media/concept-azure-managed-workstation/secure-workstation.png)

   Una workstation sicura fornisce un amministratore di una workstation con protezione avanzata con controllo delle applicazioni non crittografato e application guard. La workstation utilizzerà exploit guard, dispositivo e le credenziali per proteggere l'host da comportamenti dannosi. Locali inoltre tutti i dischi vengono crittografati con la crittografia Bitlocker.

* **Isolato** : questo scenario offline personalizzato rappresenta la fine estrema dello spettro (non gli script di installazione vengono forniti per questo caso). Le organizzazioni potrebbe essere necessario gestire una funzione di critici aziendali isolati, ad esempio una linea di produzione di valore elevato o sistemi di supporto di vita che richiede i sistemi operativi legacy non supportato/senza patch. Poiché la protezione è fondamentale e servizi cloud non sono disponibili, le organizzazioni possono manualmente gestire/aggiornare tali computer o usare un'architettura di foresta di Active Directory (ad esempio il migliorata sicurezza Admin ambiente (ESAE)) isolata per gestirli. In queste circostanze la rimozione di tutti gli accessi ad eccezione di base Intune e ATP il controllo di integrità deve essere considerato.
   * [Requisito di comunicazioni di rete di Intune](https://docs.microsoft.com/intune/network-bandwidth-use)
   * [Requisito di comunicazioni di rete di ATP](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Passaggi successivi

[Distribuzione di una workstation sicura gestita da Azure](howto-azure-managed-workstation.md)
