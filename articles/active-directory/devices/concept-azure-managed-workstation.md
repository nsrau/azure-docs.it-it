---
title: Informazioni sulle workstation sicure gestite da Azure, Azure Active Directory
description: Informazioni sulle workstation sicure gestite da Azure per capire il motivo per cui sono importanti.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c26197a14e78b1cf1a1e078ba0145eca207206bf
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561953"
---
# <a name="understand-secure-azure-managed-workstations"></a>Informazioni sulle workstation sicure gestite da Azure

Le workstation protette e isolate sono di fondamentale importanza per la sicurezza dei ruoli sensibili, ad esempio amministratori, sviluppatori e operatori di servizi critici. Se la protezione della workstation client viene compromessa, molti controlli di sicurezza e garanzie possono avere esito negativo o essere inefficaci.

Questo documento illustra gli elementi necessari per la creazione di una workstation sicura, spesso nota come workstation di accesso con privilegi (PAW). L'articolo contiene anche istruzioni dettagliate per la configurazione dei controlli di sicurezza iniziali. Questa guida descrive come la tecnologia basata sul cloud può gestire il servizio. Si basa sulle funzionalità di sicurezza introdotte in Windows 10RS5, Microsoft Defender Advanced Threat Protection (ATP), Azure Active Directory e Microsoft Intune.

> [!NOTE]
> In questo articolo viene illustrato il concetto di workstation protetta e la sua importanza. Se si ha già familiarità con il concetto e si vuole passare alla distribuzione, vedere [distribuire una workstation protetta](howto-azure-managed-workstation.md).

## <a name="why-secure-workstation-access-is-important"></a>Motivi per cui l'accesso alla workstation protetta è importante

La rapida adozione dei servizi cloud e la possibilità di lavorare da qualsiasi luogo hanno creato un nuovo metodo di sfruttamento. Sfruttando i controlli di sicurezza vulnerabili nei dispositivi in cui gli amministratori lavorano, gli utenti malintenzionati possono accedere alle risorse con privilegi.

I primi cinque metodi usati dagli utenti malintenzionati per la violazione delle organizzazioni sono gli attacchi con utilizzo improprio e la supply chain. Si tratta anche della seconda tattica più rilevata negli eventi imprevisti segnalati in 2018 in base al [report sulle minacce di Verizon](https://enterprise.verizon.com/resources/reports/dbir/)e al report di intelligence per la [sicurezza](https://aka.ms/sir).

La maggior parte degli utenti malintenzionati segue questa procedura:

1. Esplorazione per trovare un metodo in, spesso specifico di un settore.
1. Analisi per raccogliere informazioni e identificare il modo migliore per infiltrare una workstation percepita come valore basso.
1. Persistenza per cercare un mezzo di spostamento in un [secondo momento](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Exfiltration di dati riservati e sensibili.

Durante la fase di esplorazione, gli utenti malintenzionati infiltrano spesso i dispositivi che sembrano a basso rischio o sottovalutati. Usano questi dispositivi vulnerabili per individuare un'opportunità di spostamento laterale e per trovare utenti e dispositivi amministrativi. Dopo aver acquisito l'accesso ai ruoli utente con privilegi, gli utenti malintenzionati possono identificare i dati di valore elevato e sottrarre correttamente i dati.

![Modello di compromesso tipico](./media/concept-azure-managed-workstation/typical-timeline.png)

Questo documento descrive una soluzione che consente di proteggere i dispositivi di elaborazione da tali attacchi laterali. La soluzione isola la gestione e i servizi da dispositivi di produttività meno importanti, suddividendo la catena prima che il dispositivo che ha accesso alle risorse cloud sensibili possa essere infiltrato. La soluzione USA i servizi nativi di Azure che fanno parte dello stack di Microsoft 365 Enterprise:

* Intune per la gestione dei dispositivi e un elenco sicuro di applicazioni e URL
* Autopilot per la configurazione, la distribuzione e l'aggiornamento del dispositivo
* Azure AD per la gestione degli utenti, l'accesso condizionale e l'autenticazione a più fattori
* Windows 10 (versione corrente) per l'attestazione dell'integrità del dispositivo e l'esperienza utente
* Defender ATP per Endpoint Protection, rilevamento e risposta gestiti dal cloud
* Azure AD PIM per la gestione dell'autorizzazione e dell'accesso con privilegi JIT (just-in-Time) alle risorse
* Log Analytics e Sentinel per il monitoraggio e l'invio di avvisi

## <a name="who-benefits-from-a-secure-workstation"></a>Chi beneficia di una workstation sicura?

Tutti gli utenti e gli operatori traggono vantaggio dall'utilizzo di una workstation protetta. Un utente malintenzionato che compromette un PC o un dispositivo può rappresentare tutti gli account memorizzati nella cache. Quando si è connessi al dispositivo, è possibile che usino anche credenziali e token. Questo rischio rende importante proteggere i dispositivi utilizzati per i ruoli con privilegi, inclusi i diritti amministrativi. I dispositivi con account con privilegi sono destinazioni per gli attacchi di spostamento laterale e di escalation dei privilegi. Questi account possono essere usati per un'ampia gamma di asset, ad esempio:

* Amministratore di sistemi locali o basati su cloud
* Workstation per sviluppatori per sistemi critici
* Amministratore di account di social media con esposizione elevata
* Workstation estremamente sensibile, ad esempio un terminale di pagamento SWIFT
* Gestione dei segreti commerciali con la workstation

Per ridurre il rischio, è necessario implementare controlli di sicurezza elevati per le workstation con privilegi che usano questi account. Per ulteriori informazioni, vedere la [Guida alla distribuzione della funzionalità Azure Active Directory, guida](../fundamentals/active-directory-deployment-checklist-p2.md)di [orientamento per Office 365](https://aka.ms/o365secroadmap)e [sicurezza per l'accesso con privilegi](https://aka.ms/sparoadmap).

## <a name="why-use-dedicated-workstations"></a>Perché usare le workstation dedicate?

Sebbene sia possibile aggiungere sicurezza a un dispositivo esistente, è preferibile iniziare con una base sicura. Per fare in modo che l'organizzazione si trovi nella posizione migliore per mantenere un livello di sicurezza elevato, iniziare con un dispositivo che si conosce è sicuro e implementare un set di controlli di sicurezza noti.

Un numero crescente di vettori di attacco attraverso la posta elettronica e l'esplorazione Web rende sempre più difficile assicurarsi che un dispositivo possa essere considerato attendibile. In questa guida si presuppone che una workstation dedicata sia isolata dalla produttività standard, dall'esplorazione e dalla posta elettronica. La rimozione della produttività, l'esplorazione Web e la posta elettronica da un dispositivo possono avere un impatto negativo sulla produttività. Tuttavia, questa protezione è in genere accettabile per gli scenari in cui le attività di processo non lo richiedono in modo esplicito e il rischio di un evento imprevisto di sicurezza è elevato.

> [!NOTE]
> Il web browsing si riferisce all'accesso generale a siti Web arbitrari che possono essere un'attività a rischio elevato. Questo tipo di esplorazione è diverso rispetto all'uso di un Web browser per accedere a un piccolo numero di siti Web amministrativi noti per servizi quali Azure, Office 365, altri provider di servizi cloud e applicazioni SaaS.

Le strategie di contenimento rafforzano la sicurezza aumentando il numero e il tipo di controlli che impedisce a un utente malintenzionato di ottenere risorse sensibili all'accesso. Il modello descritto in questo articolo usa una progettazione di privilegi a più livelli e limita i privilegi amministrativi a dispositivi specifici.

## <a name="supply-chain-management"></a>Gestione della supply chain

Essenziale per una workstation protetta è una soluzione di supply chain in cui si usa una workstation attendibile denominata "radice di attendibilità". Le tecnologie che devono essere prese in considerazione nella selezione della radice dell'hardware di trust devono includere le tecnologie seguenti incluse nei portatili moderni: 

* [Trusted Platform Module (TPM) 2,0](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-tpm)
* [Crittografia unità BitLocker](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-bitlocker)
* [Avvio protetto UEFI](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-secure-boot)
* [Driver e firmware distribuiti tramite Windows Update](https://docs.microsoft.com/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [Virtualizzazione e HVCI obbligatoria abilitate](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-vbs)
* [Driver e app HVCI obbligatoria-Ready](https://docs.microsoft.com/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](https://docs.microsoft.com/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [Protezione I/O DMA](https://docs.microsoft.com/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [System Guard](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [Standby moderna](https://docs.microsoft.com/windows-hardware/design/device-experiences/modern-standby)

Per questa soluzione, la radice del trust verrà distribuita usando la tecnologia [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) con hardware che soddisfa i requisiti tecnici moderni. Per proteggere una workstation, Autopilot consente di sfruttare i dispositivi Windows 10 ottimizzati per OEM Microsoft. Questi dispositivi sono in uno stato valido noto del produttore. Anziché ricreazione dell'immagine di un dispositivo potenzialmente non sicuro, Autopilot può trasformare un dispositivo Windows in uno stato "pronto per l'azienda". Applica le impostazioni e i criteri, installa le app e persino modifica l'edizione di Windows 10. Ad esempio, Autopilot potrebbe modificare l'installazione di Windows di un dispositivo da Windows 10 Pro a Windows 10 Enterprise, in modo da poter utilizzare funzionalità avanzate.

![Livelli di workstation sicuri](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Profili e ruoli del dispositivo

Questa guida fa riferimento a diversi profili e ruoli di sicurezza che consentono di creare soluzioni più sicure per utenti, sviluppatori e personale IT. Questi profili bilanciano l'usabilità e i rischi per gli utenti comuni che possono trarre vantaggio da una workstation migliorata o sicura. Le configurazioni delle impostazioni disponibili sono basate sugli standard accettati dal settore. Questa guida illustra come finalizzare Windows 10 e ridurre i rischi associati alla compromissione del dispositivo o dell'utente. Per sfruttare i vantaggi della tecnologia hardware moderna e della radice del dispositivo di attendibilità, si userà [attestazione dell'integrità del dispositivo](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643), abilitata a partire dal profilo di **sicurezza elevato** . Questa funzionalità è presente per assicurarsi che gli utenti malintenzionati non possano essere persistenti durante l'avvio iniziale di un dispositivo. Questa operazione viene eseguita usando i criteri e la tecnologia per gestire le funzionalità di sicurezza e i rischi.
![livelli di workstation protetti](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Sicurezza di base** : una workstation standard gestita fornisce un valido punto di partenza per la maggior parte delle attività domestiche e piccole imprese. Questi dispositivi sono registrati in Azure AD e gestiti con Intune. Questo profilo consente agli utenti di eseguire qualsiasi applicazione ed esplorare qualsiasi sito Web. È necessario abilitare una soluzione antimalware come [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) .

* **Sicurezza avanzata** : questa soluzione protetta a livello di voce è ideale per gli utenti privati, gli utenti di piccole imprese e gli sviluppatori generali.

   La workstation avanzata è un modo basato sui criteri per aumentare la sicurezza del profilo di sicurezza basso. Offre un mezzo sicuro per lavorare con i dati del cliente, usando anche strumenti di produttività come la posta elettronica e l'esplorazione Web. È possibile usare i criteri di controllo e Intune per monitorare una workstation migliorata per il comportamento degli utenti e l'utilizzo del profilo. Il profilo workstation migliorato viene distribuito con lo script Windows10 (1809) e si avvale di Advanced Malware Protection con [Advanced Threat Protection (ATP)](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection).

* **Sicurezza elevata** : il modo più efficace per ridurre la superficie di attacco di una workstation consiste nel rimuovere la possibilità di amministrare autonomamente la workstation. La rimozione dei diritti amministrativi locali è un passaggio che migliora la sicurezza, ma può influisca sulla produttività se implementata in modo errato. Il profilo di sicurezza elevato si basa sul profilo di sicurezza avanzato con una modifica considerevole: la rimozione dell'amministratore locale. Questo profilo è progettato per gli utenti con profilo elevato: dirigenti, retribuzioni e utenti dati sensibili, responsabili approvazione per servizi e processi.

   L'utente con sicurezza elevata richiede un ambiente più controllato pur continuando a eseguire attività quali la posta elettronica e l'esplorazione Web in un'esperienza semplice da usare. Gli utenti si aspettano funzionalità come cookie, Preferiti e altri collegamenti da usare. Tuttavia, questi utenti potrebbero non essere in grado di modificare o eseguire il debug del dispositivo. Non è inoltre necessario installare i driver. Il profilo di sicurezza elevato viene distribuito con lo script High Security-Windows10 (1809).

* **Specializzata** : gli utenti malintenzionati hanno come destinazione sviluppatori e amministratori IT perché possono modificare i sistemi di interesse per gli utenti malintenzionati. La workstation specializzata si espande in base ai criteri della workstation con sicurezza elevata gestendo le applicazioni locali e limitando i siti Web. Limita inoltre le funzionalità di produttività ad alto rischio, come ActiveX, Java, plug-in del browser e altri controlli Windows. Questo profilo viene distribuito con lo script SecurityBaseline DeviceConfiguration_NCSC-Windows10 (1803).

* **Protetto** : un utente malintenzionato che compromette un account amministrativo può causare danni aziendali significativi in caso di furto dei dati, modifica dei dati o rottura del servizio. In questo stato finalizzato, la workstation Abilita tutti i controlli di sicurezza e i criteri che limitano il controllo diretto della gestione delle applicazioni locali. Una workstation protetta non dispone di strumenti di produttività, quindi il dispositivo è più difficile da compromettere. Blocca il vettore più comune per gli attacchi di phishing: posta elettronica e social media. La workstation protetta può essere distribuita con lo script SecurityBaseline Secure Workstation-Windows10 (1809).

   ![Workstation protetta](./media/concept-azure-managed-workstation/secure-workstation.png)

   Una workstation protetta fornisce un amministratore con una workstation con protezione avanzata che dispone di un controllo delle applicazioni chiaro e di un'applicazione di protezione. La workstation USA Credential Guard, Device Guard e exploit Guard per proteggere l'host dal comportamento dannoso. Anche tutti i dischi locali vengono crittografati con BitLocker.

* **Isolated** : questo scenario personalizzato offline rappresenta l'estremità estremo dello spettro. Per questo caso non sono disponibili script di installazione. Potrebbe essere necessario gestire una funzione cruciale per l'azienda che richiede un sistema operativo legacy non supportato o senza patch. Ad esempio, una linea di produzione di valore elevato o un sistema di supporto della vita. Poiché la sicurezza è fondamentale e i servizi cloud non sono disponibili, è possibile gestire e aggiornare questi computer manualmente o con un'architettura di foresta Active Directory isolata, ad esempio l'ambiente di amministrazione della sicurezza avanzata (ESAE). In queste circostanze, provare a rimuovere tutti gli accessi eccetto i controlli di integrità di base di Intune e ATP.

   * [Requisito per le comunicazioni di rete di Intune](https://docs.microsoft.com/intune/network-bandwidth-use)
   * [Requisito di comunicazione di rete ATP](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Passaggi successivi

[Distribuire una workstation protetta gestita da Azure](howto-azure-managed-workstation.md).
