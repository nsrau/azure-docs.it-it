---
title: Comprendere le workstation sicure, gestita da Azure - Azure Active Directory
description: Informazioni sulle workstation sicura, gestita da Azure e comprendere il motivo per cui sono importanti.
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
ms.openlocfilehash: 02a6ddef294c4872f2d7e50e8940ecbb4b4b7bc4
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491600"
---
# <a name="understand-secure-azure-managed-workstations"></a>Comprendere le workstation sicure, gestita da Azure

Le workstation protette e isolate sono estremamente importanti per la sicurezza dei ruoli sensibili, ad esempio amministratori, sviluppatori e operatori critiche dei servizi. Se la sicurezza delle workstation client è compromesso, molti controlli di sicurezza e garanzie possono avere esito negativo o essere inefficace.

Questo documento illustra le procedure necessarie per la creazione di una workstation sicura, spesso nota come una workstation amministrative con privilegi (PAW). Inoltre, l'articolo contiene istruzioni dettagliate per configurare i controlli di sicurezza iniziale. Questa guida descrive la tecnologia basata su cloud possono gestire il servizio. Si basa sulle funzionalità di sicurezza che sono stati introdotti in Windows 10RS5 Microsoft Defender Advanced Threat Protection (ATP), Azure Active Directory e Intune.

> [!NOTE]
> Questo articolo illustra il concetto di una workstation sicura e la sua importanza. Se si ha già familiarità con il concetto e si vuole ignorare alla distribuzione, visitare [distribuire una Workstation sicura](https://docs.microsoft.com/azure/active-directory/devices/howto-azure-managed-workstation).

## <a name="why-secure-workstation-access-is-important"></a>Perché è importante utilizzare access workstation sicura

La rapida adozione di servizi cloud e la possibilità di lavorare ovunque ha creato un nuovo metodo di sfruttamento. Sfruttando i controlli di sicurezza debole nei dispositivi in cui gli amministratori lavorano, gli utenti malintenzionati possono accedere alle risorse con privilegi.

Usare impropriamente con privilegi e supply chain browser sono tra i primi cinque metodi che gli utenti malintenzionati usano per essere violato le organizzazioni. Poiché sono il secondo più comunemente rilevata tattiche in eventi imprevisti segnalati nel 2018 in base al [report sulle minacce di Verizon](https://enterprise.verizon.com/resources/reports/dbir/)e il [Security Intelligence Report](https://aka.ms/sir).

La maggior parte degli utenti malintenzionati seguono questi passaggi:

1. Esplorazione per trovare un modo, che spesso specifiche del settore.
1. Analisi per raccogliere informazioni e identificare il modo migliore per infiltrarsi una workstation in cui viene percepita come valore minimo.
1. Il salvataggio permanente per cercare un mezzo per spostare [lateralmente](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Esfiltrazione dei dati sensibili e riservati.

Durante la ricognizione, gli utenti malintenzionati spesso infiltrarsi i dispositivi che sembrano a basso rischio o sottovalutato. Usano questi dispositivi vulnerabili per individuare un'opportunità per lo spostamento laterale e per individuare i dispositivi e utenti con privilegi amministrativi. Dopo che si ottengono l'accesso ai ruoli utente con privilegi, gli utenti malintenzionati identificano sottrarre e correttamente i dati di valore elevato che i dati.

![Modello tipico di compromissione](./media/concept-azure-managed-workstation/typical-timeline.png)

Questo documento illustra una soluzione che consente di proteggere i dispositivi di elaborazione da tali attacchi laterali. Consente di isolare la soluzione di gestione e i servizi da dispositivi meno utili di produttività, interruzione della catena prima che il dispositivo che dispone dell'accesso alle risorse cloud sensibili possa essere infiltrated. La soluzione Usa servizi di Azure nativi che fanno parte dello stack di Microsoft 365 Enterprise:

* Intune per la gestione dei dispositivi e un elenco di applicazioni e gli URL sicuro
* AutoPilot per la configurazione del dispositivo, la distribuzione e aggiornamento
* Azure AD per la gestione degli utenti, l'accesso condizionale e autenticazione a più fattori
* Windows 10 (versione corrente) per device health attestation e l'esperienza utente
* Defender ATP per la protezione di endpoint cloud gestito, il rilevamento e risposta
* Azure AD Privileged Identity Management per la gestione di just-in-time (JIT) e l'autorizzazione di accesso alle risorse con privilegi

## <a name="who-benefits-from-a-secure-workstation"></a>Che trae vantaggio da una workstation sicura?

Tutti gli utenti e gli operatori vantaggi quando si usa una workstation sicura. Un utente malintenzionato che compromette un PC o dispositivo possa rappresentare memorizzato nella cache tutti gli account. Quando è connesso al dispositivo, potrebbe anche usare le credenziali e token. Questo rischio rende essenziale per proteggere i dispositivi usati per i ruoli con privilegi, tra cui quelli amministrativi. I dispositivi con gli account con privilegi sono le destinazioni per lo spostamento laterale e attacchi di escalation dei privilegi. Questi account possono essere usati per un'ampia gamma di asset, ad esempio:

* Amministratore dei sistemi basati su cloud o in locale
* Workstation di sviluppo per sistemi critici
* Amministratore dell'account con livello di esposizione dei social media
* Workstation estremamente riservate, ad esempio un pagamento SWIFT terminal
* Segreti commerciali di gestione delle workstation

Per ridurre il rischio, è necessario implementare i controlli di sicurezza con privilegi elevati per workstation con privilegi che fanno uso di questi account. Per altre informazioni, vedere la [Guida alla distribuzione di funzionalità di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [roadmap di Office 365](https://aka.ms/o365secroadmap), e [roadmap per la protezione dell'accesso con privilegi](https://aka.ms/sparoadmap)).

## <a name="why-use-dedicated-workstations"></a>Perché usare workstation dedicate?

Sebbene sia possibile aggiungere protezione a un dispositivo esistente, è preferibile iniziare con una base sicura. Per inserire l'organizzazione nella posizione ideale per mantenere un livello di sicurezza elevata, iniziare con un dispositivo che è protetta e implementare un set di controlli di sicurezza noti.

Un numero crescente di vettori di attacco tramite posta elettronica ed esplorazione del web rende sempre più difficile essere certi che un dispositivo può essere considerato attendibile. Questa guida si presuppone che una workstation dedicata è isolata da standard di produttività, l'esplorazione e indirizzo di posta elettronica. Rimozione di produttività, esplorazione del web e indirizzo di posta elettronica da un dispositivo può avere un impatto negativo sulla produttività. Tuttavia, questa misura di sicurezza è in genere accettabile per gli scenari in cui le attività di processo non è richiesto in modo esplicito e rischio di un evento imprevisto della sicurezza è elevato.

> [!NOTE]
> Esplorazione del Web qui fa riferimento per l'accesso generale ai siti Web arbitrario che può essere un'attività ad alto rischio. Questo tipo di esplorazione è nettamente diverso dall'uso di un web browser per accedere a un numero ridotto di siti Web di amministrazione noto per i servizi come Azure, Office 365, altri provider di servizi cloud e applicazioni SaaS.

Strategie di contenimento rafforzare la sicurezza se si aumenta il numero e il tipo di controlli che svolgono un'azione deterrente un utente malintenzionato di accedere a risorse sensibili. Il modello descritto in questo articolo usa una progettazione di privilegi a più livelli e limita i privilegi amministrativi a dispositivi specifici.

## <a name="supply-chain-management"></a>Gestione della supply chain

Essenziale per una workstation protetta è una soluzione della supply chain in cui si usa una workstation attendibile denominata "radice di attendibilità". Per questa soluzione, viene utilizzata la radice di attendibilità [Autopilot Microsoft](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) tecnologia. Per proteggere una workstation, Autopilot consente di sfruttare i dispositivi di Microsoft OEM ottimizzato per Windows 10. Questi dispositivi vengono forniti in uno stato noto soddisfacente dal produttore. Invece di ricreazione dell'immagine di un dispositivo potenzialmente non sicuro, Autopilot è possibile trasformare un dispositivo Windows in uno stato "aziendali". Applica le impostazioni e i criteri, consente di installare le App e viene modificato anche l'edizione di Windows 10. Ad esempio, Autopilot cambi installazione di un dispositivo Windows da Windows 10 Pro a Windows 10 Enterprise in modo da poter utilizzare le funzionalità avanzate.

![Livelli di workstation sicura](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>I profili e i ruoli di dispositivo

Questo materiale sussidiario fa riferimento a diversi profili di sicurezza e ruoli che consentono di creano soluzioni più sicure per il personale IT, sviluppatori e utenti. Questi profili bilanciare usabilità e i rischi per gli utenti comuni che possono trarre vantaggio da una workstation sicura o avanzata. Le impostazioni delle configurazioni fornite di seguito sono basate sugli standard del settore accettato. Questa guida illustra come rafforzare la protezione di Windows 10 e ridurre i rischi associati all'utente o dispositivo compromesso. Esegue l'operazione con la tecnologia e i criteri per gestire i rischi e le funzionalità di sicurezza.
![Livelli di workstation sicura](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Sicurezza bassa** -una workstation standard, gestita offre un buon punto di partenza per la maggior parte degli ambienti domestici o piccole aziendale. Questi dispositivi sono registrati in Azure AD e gestiti con Intune. Questo profilo consente agli utenti di eseguire tutte le applicazioni ed esplorare qualsiasi sito Web. Ad esempio una soluzione antimalware [Defender Microsoft](https://www.microsoft.com/windows/comprehensive-security) deve essere abilitata.

* **Protezione avanzata** – questa soluzione di base, protetta è utile per gli utenti privati, gli utenti aziendali di piccole dimensioni e gli sviluppatori generali.

   La workstation avanzata è un modo per aumentare la sicurezza del profilo di basso livello di protezione basata su criteri. Fornisce un modo sicuro per lavorare con i dati dei clienti, usando anche strumenti di produttività come messaggio di posta elettronica ed esplorazione del web. È possibile utilizzare criteri di controllo e Intune per monitorare una workstation avanzata per l'utilizzo di comportamento e del profilo utente. Distribuire il profilo di workstation avanzate con lo script di Windows 10 (1809) e che si avvale di malware avanzato di protezione utilizzando [Advanced Threat Protection (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp).

* **Sicurezza elevata** – il mezzo più efficace per ridurre la superficie di attacco di una workstation consiste nel rimuovere la possibilità di self-administer workstation. La rimozione di diritti amministrativi locali è un passaggio che migliora la sicurezza, ma può ridurre la produttività se implementata in modo non corretto. Il profilo di sicurezza elevata si basa sul profilo di sicurezza avanzata con una modifica di una notevole: la rimozione dell'amministratore locale. Questo profilo è destinato agli utenti di alto profilo: i dirigenti, retribuzioni e dati sensibili a utenti, i responsabili approvazione per i servizi e processi.

   L'utente di protezione elevato richiede un ambiente più controllato pur consentendo di eseguire le attività, ad esempio indirizzo di posta elettronica e web browser in un'esperienza semplice da usare. Gli utenti prevedono funzionalità quali i cookie, Preferiti e altri tasti di scelta rapida per lavorare. Tuttavia, questi utenti non richiedano la possibilità di modificare o eseguire il debug proprio dispositivo. Inoltre non devono installare i driver. Il profilo di sicurezza elevato viene distribuito usando l'elevata sicurezza - script di Windows 10 (1809).

* **Specializzato** : gli utenti malintenzionati sottopongono gli sviluppatori e amministratori IT in quanto è possibile modificare i sistemi di interesse per gli utenti malintenzionati. La workstation specializzata amplia i criteri della workstation di protezione elevato, la gestione delle applicazioni locali e limitando i siti Web. Limita inoltre le funzionalità di produttività ad alto rischio, ad esempio ActiveX, Java, plugin del browser e altri controlli di Windows. Distribuire questo profilo con il DeviceConfiguration_NCSC - script SecurityBaseline Windows10 (1803).

* **Protetto** : un utente malintenzionato che compromette un account amministrativo può causare danni significativi business dal furto dei dati, l'alterazione dei dati o un'interruzione del servizio. In questo stato, con protezione avanzato della workstation consente a tutti i controlli di sicurezza e i criteri che limitano l'utilizzo di un controllo diretto della gestione delle applicazioni locali. Una workstation protetta ha nessuno strumento di produttività. pertanto, il dispositivo più difficile da compromettere. Blocca il vettore più comune per gli attacchi di phishing: messaggio di posta elettronica e social media.  La workstation protetta può essere distribuita con la Workstation Secure - script SecurityBaseline Windows10 (1809).

   ![Workstation protetti](./media/concept-azure-managed-workstation/secure-workstation.png)

   Una workstation sicura consente agli amministratori di una workstation con protezione avanzata con controllo delle applicazioni non crittografato e application guard. La workstation Usa credential guard, protezione del dispositivo ed exploit guard per proteggere l'host da comportamenti dannosi. Tutti i dischi locali vengono anche crittografati con BitLocker.

* **Isolato** : questo scenario personalizzato, non in linea rappresenta la fine estrema dello spettro. Nessuno script di installazione sono disponibili per questo caso. Si potrebbe essere necessario gestire una funzione di business-critical che richiede un sistema operativo legacy non supportato o non aggiornato. Ad esempio, una linea di produzione di valore elevato o un sistema di supporto tecnico – vita. Poiché la protezione è fondamentale e non sono disponibili i servizi cloud, è possibile gestire e aggiornare tali computer manualmente o con un'architettura di foresta di Active Directory, ad esempio il migliorato sicurezza Admin ambiente (ESAE) isolata. In questi casi, provare a rimuovere tutti gli accessi ad eccezione di controlli di integrità di Intune e ATP base.

  * [Requisito di comunicazioni di rete di Intune](https://docs.microsoft.com/intune/network-bandwidth-use)
  * [Requisito di comunicazioni di rete di ATP](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Passaggi successivi

[Distribuire una workstation sicura gestita da Azure](howto-azure-managed-workstation.md).
