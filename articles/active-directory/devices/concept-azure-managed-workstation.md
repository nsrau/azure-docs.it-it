---
title: Comprendere le workstation protette gestite da Azure - Azure Active DirectoryUnderstand secure, Azure-managed workstations - Azure Active Directory
description: Scopri le workstation sicure e gestite da Azure e scopri perché sono importanti.
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
ms.openlocfilehash: 05a3a8cf14a591dd3037175e4eed5b5bd8d3096c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672654"
---
# <a name="understand-secure-azure-managed-workstations"></a>Comprendere le workstation protette e gestite da AzureUnderstand secure, Azure-managed workstations

Le workstation protette e isolate sono di fondamentale importanza per la sicurezza dei ruoli sensibili come amministratori, sviluppatori e operatori di servizi critici. Se la sicurezza della workstation client viene compromessa, molti controlli e garanzie di sicurezza possono avere esito negativo o essere inefficaci.

Questo documento spiega cosa serve per la creazione di una workstation sicura, spesso nota come workstation con accesso privilegiato (PAW). L'articolo contiene anche istruzioni dettagliate per impostare i controlli di sicurezza iniziali. Questa guida descrive come la tecnologia basata su cloud può gestire il servizio. Si basa sulle funzionalità di sicurezza introdotte in Windows 10RS5, Microsoft Defender Advanced Threat Protection (ATP), Azure Active Directory e Microsoft Intune.

> [!NOTE]
> In questo articolo viene illustrato il concetto di una workstation sicura e la sua importanza. Se si ha già familiarità con il concetto e si desidera passare alla distribuzione, vedere [Deploy a Secure Workstation](howto-azure-managed-workstation.md).

## <a name="why-secure-workstation-access-is-important"></a>Perché è importante proteggere l'accesso alla workstation

La rapida adozione dei servizi cloud e la capacità di lavorare da qualsiasi luogo hanno creato un nuovo metodo di sfruttamento. Sfruttando i controlli di sicurezza deboli sui dispositivi in cui gli amministratori lavorano, gli utenti malintenzionati possono ottenere l'accesso alle risorse con privilegi.

L'uso improprio e gli attacchi alla catena di fornitura sono tra i primi cinque metodi utilizzati dagli aggressori per violare le organizzazioni. Sono anche la seconda tattica più comunemente rilevata in incidenti segnalati nel 2018 secondo il [rapporto Verizon Threat](https://enterprise.verizon.com/resources/reports/dbir/), e il [Security Intelligence Report](https://aka.ms/sir).

La maggior parte degli aggressori segue questi passaggi:

1. Ricognizione per trovare un modo per trovare un modo, spesso specifico per un settore.
1. Analisi per raccogliere informazioni e identificare il modo migliore per infiltrarsi in una workstation che viene percepita come basso valore.
1. Persistenza alla ricerca di un mezzo per muoversi [lateralmente](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Esfiltrazione di dati riservati e sensibili.

Durante la ricognizione, gli aggressori si infiltrano frequentemente in dispositivi che sembrano a basso rischio o sottovalutati. Usano questi dispositivi vulnerabili per individuare un'opportunità di movimento laterale e per trovare utenti e dispositivi amministrativi. Dopo aver ottenuto l'accesso ai ruoli utente con privilegi, gli utenti malintenzionati identificano dati di valore elevato ed esfiltratino correttamente.

![Modello di compromesso tipico](./media/concept-azure-managed-workstation/typical-timeline.png)

Questo documento descrive una soluzione che consente di proteggere i dispositivi di elaborazione da tali attacchi laterali. La soluzione isola la gestione e i servizi dai dispositivi di produttività meno preziosi, rompendo la catena prima che il dispositivo che ha accesso alle risorse cloud sensibili possa essere infiltrato. La soluzione usa servizi nativi di Azure che fanno parte dello stack di Microsoft 365 Enterprise:

* Intune per la gestione dei dispositivi e un elenco sicuro di applicazioni e URL
* Pilota automatico per la configurazione, la distribuzione e l'aggiornamento dei dispositivi
* Azure AD per la gestione degli utenti, l'accesso condizionale e l'autenticazione a più fattoriAzure AD for user management, Conditional Access, and multi-factor authentication
* Windows 10 (versione corrente) per l'attestazione dell'integrità del dispositivo e l'esperienza utente
* Defender ATP per la protezione, il rilevamento e la risposta agli endpoint gestiti nel cloud
* Azure AD PIM per la gestione dell'autorizzazione e dell'accesso con privilegi JIT alle risorseAzure AD PIM for managing authorization and just-in-time (JIT) privileged access to resources
* Log Analytics e Sentinel per il monitoraggio e gli avvisi

## <a name="who-benefits-from-a-secure-workstation"></a>Chi beneficia di una workstation sicura?

Tutti gli utenti e gli operatori traggono vantaggio dall'utilizzo di una workstation sicura. Un utente malintenzionato che compromette un PC o un dispositivo può rappresentare tutti gli account memorizzati nella cache. Una volta effettuato l'accesso al dispositivo, potrebbero anche usare credenziali e token. Questo rischio rende importante proteggere i dispositivi utilizzati per i ruoli con privilegi, inclusi i diritti amministrativi. I dispositivi con account con privilegi sono obiettivi di movimenti laterali e attacchi di escalation dei privilegi. Questi conti possono essere utilizzati per una varietà di attività, ad esempio:

* Amministratore di sistemi locali o basati su cloud
* Workstation di sviluppo per sistemi critici
* Amministratore dell'account di social media con esposizione elevata
* Workstation altamente sensibile, come un terminale di pagamento SWIFT
* Gestione dei segreti commerciali per la gestione delle workstation

Per ridurre i rischi, è consigliabile implementare controlli di sicurezza elevati per le workstation con privilegi che utilizzano questi account. Per altre informazioni, vedere la [Guida alla distribuzione delle funzionalità](../fundamentals/active-directory-deployment-checklist-p2.md)di Azure Active Directory , la roadmap di Office [365](https://aka.ms/o365secroadmap)e la Guida di orientamento alla [protezione dell'accesso con privilegi](https://aka.ms/sparoadmap).

## <a name="why-use-dedicated-workstations"></a>Perché utilizzare workstation dedicate?

Sebbene sia possibile aggiungere sicurezza a un dispositivo esistente, è preferibile iniziare con una base sicura. Per mettere l'organizzazione nella posizione migliore per mantenere un livello di sicurezza elevato, iniziare con un dispositivo che si sa essere sicuro e implementare un set di controlli di sicurezza noti.

Un numero crescente di vettori di attacco tramite e-mail e navigazione web rende sempre più difficile essere sicuri che un dispositivo può essere attendibile. In questa guida si presuppone che una workstation dedicata sia isolata dalla produttività, dall'esplorazione e dalla posta elettronica standard. La rimozione della produttività, l'esplorazione del Web e la posta elettronica da un dispositivo possono avere un impatto negativo sulla produttività. Tuttavia, questa protezione è in genere accettabile per gli scenari in cui le attività di processo non lo richiedono in modo esplicito e il rischio di un incidente di sicurezza è elevato.

> [!NOTE]
> La navigazione web qui si riferisce all'accesso generale a siti web arbitrari che possono essere un'attività ad alto rischio. Tale esplorazione è distintamente diversa dall'utilizzo di un Web browser per accedere a un numero ridotto di siti Web amministrativi noti per servizi come Azure, Office 365, altri provider cloud e applicazioni SaaS.

Le strategie di contenimento rafforzano la sicurezza aumentando il numero e il tipo di controlli che dissuadano un utente malintenzionato dall'ottenere risorse sensibili all'accesso. Il modello descritto in questo articolo usa una progettazione dei privilegi a più livelli e limita i privilegi amministrativi a dispositivi specifici.

## <a name="supply-chain-management"></a>Gestione della catena di approvvigionamento

Essenziale per una workstation protetta è una soluzione di supply chain in cui si utilizza una workstation attendibile denominata "radice di trust". La tecnologia che deve essere considerata nella selezione dell'hardware root of trust deve includere le seguenti tecnologie incluse nei computer portatili moderni: 

* [Trusted Platform Module (TPM) 2.0](/windows-hardware/design/device-experiences/oem-tpm)
* [Crittografia unità BitLocker](/windows-hardware/design/device-experiences/oem-bitlocker)
* [Avvio protetto UEFI](/windows-hardware/design/device-experiences/oem-secure-boot)
* [Driver e firmware distribuiti tramite Windows Update](/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [Virtualizzazione e HVCI abilitate](/windows-hardware/design/device-experiences/oem-vbs)
* [Driver e applicazioni HVCI-Ready](/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [Protezione I/O DMA](/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [Guardia di sistema](/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [Standby moderno](/windows-hardware/design/device-experiences/modern-standby)

Per questa soluzione, la radice di attendibilità verrà distribuita utilizzando la tecnologia [Microsoft Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) con hardware che soddisfa i requisiti tecnici moderni. Per proteggere una workstation, Autopilot consente di sfruttare i dispositivi Windows 10 ottimizzati per Microsoft OEM. Questi dispositivi sono disponibili in uno stato buono noto dal produttore. Invece di ricreare l'immagine di un dispositivo potenzialmente insicuro, Autopilot può trasformare un dispositivo Windows in uno stato "business-ready". Applica impostazioni e criteri, installa app e modifica persino l'edizione di Windows 10. Ad esempio, Autopilot potrebbe modificare l'installazione di Windows di un dispositivo da Windows 10 Pro a Windows 10 Enterprise in modo che possa usare funzionalità avanzate.

![Livelli di workstation sicuri](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Profili e ruoli del dispositivo

Questa guida fa riferimento a diversi profili e ruoli di sicurezza che consentono di creare soluzioni più sicure per utenti, sviluppatori e personale IT. Questi profili bilanciano l'usabilità e i rischi per gli utenti comuni che possono trarre vantaggio da una workstation avanzata o sicura. Le configurazioni delle impostazioni fornite qui si basano sugli standard accettati dal settore. Questa guida mostra come eseguire la protezione avanzata di Windows 10 e ridurre i rischi associati alla compromissione del dispositivo o dell'utente. Per sfruttare la moderna tecnologia hardware e la radice del dispositivo di attendibilità, utilizzeremo [Device Health Attestation](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643), che è abilitato a partire dal profilo **High Security.** Questa funzionalità è presente per garantire che gli aggressori non possano essere persistenti durante l'avvio anticipato di un dispositivo. Lo fa utilizzando criteri e tecnologia per gestire le funzionalità e i rischi di sicurezza.
![Livelli di workstation sicuri](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Sicurezza di base** – Una workstation standard gestita fornisce un buon punto di partenza per la maggior parte dell'uso domestico e delle piccole imprese. Questi dispositivi sono registrati in Azure AD e gestiti con Intune.These devices are registered in Azure AD and managed with Intune. Questo profilo consente agli utenti di eseguire qualsiasi applicazione e sfogliare qualsiasi sito Web. Una soluzione antimalware come [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) deve essere abilitata.

* **Sicurezza avanzata:** questa soluzione protetta entry-level è valida per gli utenti privati, le piccole imprese e gli sviluppatori generici.

   La workstation avanzata è un modo basato su criteri per aumentare la sicurezza del profilo di protezione basso. Fornisce un mezzo sicuro per lavorare con i dati dei clienti, utilizzando anche strumenti di produttività come e-mail e navigazione web. È possibile usare i criteri di controllo e Intune per monitorare una workstation avanzata per il comportamento degli utenti e l'utilizzo dei profili. Distribuire il profilo della workstation avanzata con lo script di Windows10 (1809) e si avvale della protezione avanzata da malware utilizzando [Advanced Threat Protection (ATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection).

* **Alta sicurezza** – Il mezzo più efficace per ridurre la superficie di attacco di una workstation è quello di rimuovere la capacità di auto-amministrare la workstation. La rimozione dei diritti amministrativi locali è un passaggio che migliora la sicurezza, ma può influire sulla produttività se implementato in modo non corretto. Il profilo di sicurezza elevato si basa sul profilo di sicurezza avanzato con una modifica notevole: la rimozione dell'amministratore locale. Questo profilo è progettato per gli utenti di alto profilo: dirigenti, buste paga e utenti di dati sensibili, approvatori per servizi e processi.

   L'utente ad alta sicurezza richiede un ambiente più controllato pur essendo in grado di svolgere attività come la posta elettronica e la navigazione web in un'esperienza semplice da usare. Gli utenti si aspettano funzionalità come cookie, preferiti e altri collegamenti per funzionare. Tuttavia, questi utenti potrebbero non richiedere la possibilità di modificare o eseguire il debug del dispositivo. Inoltre, non è necessario installare i driver. Il profilo di sicurezza elevato viene distribuito utilizzando lo script High Security - Windows10 (1809).

* **Specializzato** – Gli aggressori prendono di mira gli sviluppatori e gli amministratori IT perché possono modificare i sistemi di interesse per gli aggressori. La workstation specializzata espande i criteri della workstation ad alta sicurezza gestendo le applicazioni locali e limitando i siti Web. Limita inoltre le funzionalità di produttività ad alto rischio, ad esempio ActiveX, Java, plug-in del browser e altri controlli Windows. Distribuire questo profilo con lo script DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.

* **Protetto:** un utente malintenzionato che compromette un account amministrativo può causare danni aziendali significativi a causa di furto di dati, alterazione dei dati o interruzione del servizio. In questo stato con protezione con protezione dati, la workstation abilita tutti i controlli di sicurezza e i criteri che limitano il controllo diretto della gestione delle applicazioni locali. Una workstation protetta non dispone di strumenti di produttività, pertanto il dispositivo è più difficile da compromettere. Blocca il vettore più comune per gli attacchi di phishing: e-mail e social media. La workstation protetta può essere distribuita con lo script Secure Workstation - Windows10 (1809) SecurityBaseline.

   ![Stazione di lavoro protetta](./media/concept-azure-managed-workstation/secure-workstation.png)

   Una workstation sicura fornisce a un amministratore una workstation con protezione sicura con un controllo chiaro delle applicazioni e protezione delle applicazioni. La workstation utilizza protezione delle credenziali, protezione del dispositivo e protezione degli exploit per proteggere l'host da comportamenti dannosi. Anche tutti i dischi locali vengono crittografati con BitLocker.

* **Isolato:** questo scenario personalizzato e offline rappresenta l'estremità estrema dello spettro. Non vengono forniti script di installazione per questo caso. Potrebbe essere necessario gestire una funzione business-critical che richiede un sistema operativo legacy non supportato o senza patch. Ad esempio, una linea di produzione di alto valore o un sistema di supporto vitale. Poiché la sicurezza è fondamentale e i servizi cloud non sono disponibili, è possibile gestire e aggiornare questi computer manualmente o con un'architettura di foresta di Active Directory isolata, ad esempio Enhanced Security Admin Environment (ESAE). In queste circostanze, è consigliabile rimuovere tutti gli accessi ad eccezione dei controlli di integrità di base di Intune e ATP.

   * [Requisito di comunicazione di rete IntuneIntune network communications requirement](/intune/network-bandwidth-use)
   * [Requisiti di comunicazione di rete ATP](/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Passaggi successivi

[Distribuire una workstation protetta gestita da Azure.](howto-azure-managed-workstation.md)
