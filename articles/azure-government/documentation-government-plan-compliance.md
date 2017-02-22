---
title: "Conformità di Azure per enti pubblici | Documentazione Microsoft"
description: "Presenta una panoramica dei servizi di conformità disponibili per Azure per enti pubblici"
services: azure-government
cloud: gov
documentationcenter: 
author: jomolesk
manager: zakramer
ms.assetid: 1d2e0938-482f-4f43-bdf6-0a5da2e9a185
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/07/2016
ms.author: jomolesk
translationtype: Human Translation
ms.sourcegitcommit: 474ee83dfc8532f8410b79c6426af3cad6f3d320
ms.openlocfilehash: cd54456c8f9e0f37f53973fca80b8bd3514b62d1


---
# <a name="azure-government-compliance"></a>Conformità di Azure per enti pubblici

## <a name="azure-blueprint"></a>Azure Blueprint

Il programma Azure Blueprint è stato progettato per semplificare l'uso sicuro e conforme di Azure per enti pubblici e provider di terze parti che operano per conto del governo. I clienti di Azure per enti pubblici possono sfruttare le certificazioni FedRAMP JAB Provisional Authority to Operate (P-ATO) o DoD Provisional Authorization (PA) di Azure per enti pubblici, riducendo l'ambito dei controlli di sicurezza a carico dei clienti nei sistemi basati su Azure. L'ereditarietà delle implementazioni dei controlli di sicurezza da Azure per enti pubblici consente ai clienti di concentrarsi sulle implementazioni dei controlli specifiche per gli ambienti IaaS, PaaS o SaaS creati in Azure.

> [!NOTE]
> Nel contesto del modello di Azure, per "cliente" si intende l'organizzazione che esegue la compilazione direttamente in Azure. I clienti di Azure possono includere gli ISV di terze parti che eseguono la compilazione per conto di enti pubblici o enti governativi direttamente in Azure.

## <a name="blueprint-customer-responsibilities-matrix"></a>Matrice delle responsabilità del cliente di Blueprint

La matrice delle responsabilità del cliente di Azure Blueprint è stata progettata per aiutare i clienti di Azure per enti pubblici a implementare e documentare controlli di sicurezza specifici del sistema implementati in Azure. La matrice delle responsabilità del cliente elenca tutti i requisiti dei controlli di sicurezza [NIST SP 800-53](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r4.pdf) per le baseline FedRAMP e DISA che includono una richiesta di implementazione del cliente. Sono inclusi i controlli con responsabilità condivisa tra Azure e i clienti di Azure, oltre ai controlli che devono essere implementati completamente dai clienti di Azure. Quando appropriato, i controlli sono illustrati in modo dettagliato fino al livello di requisiti secondari del controllo, per offrire indicazioni più specifiche.

Il formato della matrice delle responsabilità del cliente è stato progettato in modo da ottimizzare l'utilità e consente di ottenere documentazione specifica che illustra solo le parti relative ai clienti dei controlli di sicurezza implementati.

Ad esempio, il requisito del controllo AC-1 richiede procedure e criteri di controllo di accesso documentati relativi al sistema che richiedono l'autorizzazione a procedere. Per questo controllo, Microsoft ha procedure e criteri specifici per Azure relativi ai meccanismi di controllo di accesso usati per gestire l'infrastruttura e la piattaforma di Azure. I clienti devono inoltre creare criteri e procedure di controllo di accesso personalizzati in ogni sistema specifico creato in Azure. I documenti della matrice delle responsabilità del cliente controllano la parte AC-1a, che richiede di includere determinati contenuti nei criteri e nelle procedure, e la parte AC-1b, che richiede ai clienti di rivedere e aggiornare questi documenti una volta all'anno.

La matrice delle responsabilità del cliente di Blueprint è disponibile come cartella di lavoro di Microsoft Excel per le baseline FedRAMP di livello moderato ed elevato e per le baseline DISA Cloud Computing SRG L4 e L5.

Per richiedere una copia della matrice delle responsabilità del cliente di Azure Blueprint o per inviare commenti e suggerimenti, inviare un messaggio di posta elettronica all'indirizzo [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com).

## <a name="blueprint-system-security-plan"></a>Piano di sicurezza del sistema Blueprint

Il modello relativo al piano di sicurezza del sistema di Azure Blueprint è incentrato sul cliente ed è stato progettato per essere usato nello sviluppo di un piano di sicurezza del sistema che documenti le implementazioni dei controlli di sicurezza dei clienti e i controlli ereditati da Azure. I controlli che includono una responsabilità del cliente contengono indicazioni sulla documentazione dell'implementazione dei controlli con una risposta completa e conforme. Le sezioni relative all'ereditarietà di Azure documentano il modo in cui i controlli di sicurezza vengono implementati da Azure per conto del cliente.

Il piano di sicurezza del sistema di Azure Blueprint è disponibile per le baseline FedRAMP di livello moderato ed elevato e per le baseline DISA Cloud Computing SRG L4 e L5. 

Per richiedere una copia del piano di sicurezza del sistema di Azure Blueprint o per inviare commenti e suggerimenti, inviare un messaggio di posta elettronica all'indirizzo [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com).

## <a name="azure-blueprint-implementation-guidance"></a>Indicazioni sull'implementazione di Azure Blueprint

Le indicazioni sull'implementazione di Azure Blueprint sono state progettate per aiutare gli architetti delle soluzioni e gli addetti alla sicurezza a comprendere il modo in cui è possibile distribuire i servizi e le funzionalità di Azure per enti pubblici in modo da implementare un sottoinsieme di controlli di sicurezza FedRAMP e DoD di responsabilità del cliente. Sono disponibili strumenti, documentazione, modelli e altre risorse utili per la distribuzione sicura dei servizi e delle funzionalità di Azure. Le risorse di Azure possono essere distribuite usando i [blocchi predefiniti](https://github.com/mspnp/template-building-blocks) del modello di Azure Resource Manager, i [modelli di avvio rapido](https://azure.microsoft.com/resources/templates/) creati con la collaborazione della community di oppure i modelli di Resource Manager basati su JSON [creati dai clienti](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). L'architettura di una distribuzione di base in Azure include risorse di calcolo, di rete e di archiviazione. Queste indicazioni sull'implementazione illustrano il modo in cui è possibile distribuire le risorse per consentire il rispetto dei requisiti di implementazione dei controlli di sicurezza.

### <a name="virtual-machines"></a>Macchine virtuali

I modelli di Azure Resource Manager possono essere usati per distribuire macchine virtuali preconfigurate che rispettano i benchmark di sicurezza e le indicazioni di implementazione tecnica della sicurezza del settore e del Governo degli Stati Uniti. È possibile creare macchine virtuali di Azure personalizzate usando un computer preconfigurato esistente o distribuire una nuova macchina virtuale e applicare un criterio di sicurezza usando Active Directory, per computer aggiunti al dominio, o l'utilità oggetto Criteri di gruppo locale, per i computer autonomi. L'estensione Script personalizzato delle macchine virtuali di Azure può essere usata per gestire queste attività di configurazione successive alla distribuzione.

La configurazione delle macchine virtuali Windows può includere implementazioni dei controlli di sicurezza per:

- Notifica e acknowledgement dell'uso del sistema [controllo NIST SP 800-53 AC-8]

  > Dichiarazione di implementazione del controllo di esempio AC-8: *un oggetto Criteri di gruppo controllato dalla configurazione viene applicato a tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene configurato in base al benchmark CIS per Windows Server 2012 R2 e viene personalizzato per rispettare i requisiti dell'organizzazione, incluso l'uso di una notifica appropriata per l'uso del sistema, che gli utenti devono accettare prima dell'accesso (vedere le sezioni 2.3.7.4 e 2.3.7.5 relative al benchmark CIS).*

- Restrizioni sull'account del computer locale, inclusi il blocco dell'account [controllo NIST SP 800-53 AC-7], il controllo delle sessioni simultanee [controllo NIST SP 800-53 AC-10], il blocco della sessione [NIST SP-800-53 controllo AC-11], la gestione dell'autenticatore [controllo NIST SP 800-53 IA-5] e altro ancora

  > Dichiarazione di implementazione del controllo di esempio AC-7: *un oggetto Criteri di gruppo controllato dalla configurazione viene applicato a tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene configurato in base al benchmark CIS per Windows Server 2012 R2 e viene personalizzato per rispettare i requisiti dell'organizzazione, incluso l'avvio di un blocco dell'account dopo tentativi di accesso non riusciti consecutivi (vedere 1.2.1, 1.2.2, 1.2.3 relative al benchmark CIS. Nota: i valori predefiniti del benchmark CIS devono essere modificati per rispettare i requisiti dei parametri di FedRAMP e DoD).*

  > Dichiarazione di implementazione del controllo di esempio AC-10: *un oggetto Criteri di gruppo controllato dalla configurazione viene applicato a tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene configurato in base al benchmark CIS per Windows Server 2012 R2 e viene personalizzato per rispettare i requisiti dell'organizzazione, inclusa la limitazione degli utenti a una singola sessione di Servizi Desktop remoto, che rispetta i requisiti di FedRAMP e DoD (vedere la sezione 18.9.48.3.2 relativa al benchmark CIS).*

  > Dichiarazione di implementazione del controllo di esempio AC-11: *un oggetto Criteri di gruppo controllato dalla configurazione viene applicato a tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene configurato in base al benchmark CIS per Windows Server 2012 R2 e viene personalizzato per rispettare i requisiti dell'organizzazione, incluso l'avvio di un blocco di sessione dopo 900 secondi (15 minuti) di inattività, che viene mantenuto fino alla ripetizione dell'autenticazione dell'utente (vedere le sezioni 2.3.7.3, 19.1.3.1 e 19.1.3.3 relative al benchmark CIS).*

  > Dichiarazione di implementazione del controllo di esempio IA-5: *un oggetto Criteri di gruppo controllato dalla configurazione viene applicato a tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene configurato in base al benchmark CIS per Windows Server 2012 R2 e viene personalizzato per rispettare i requisiti dell'organizzazione, incluse le restrizioni sulle password dell'account locale che impongono restrizioni minime (1 giorno) e massime (60 giorni) per la durata, le condizioni di riutilizzo (24 password), la lunghezza (14 caratteri) e i requisiti di complessità, che rispettano i requisiti di FedRAMP (vedere le sezioni 1.1.1, 1.1.2., 1.1.3., 1.1.4 e 1.1.5 relative al benchmark CIS. Nota: i valori predefiniti del benchmark CIS devono essere modificati per rispettare i requisiti dei parametri di DoD).*

- Impostazioni di configurazione, inclusa la funzionalità minima [controlli NIST SP 800-53 CM-6, CM-7]

  > Dichiarazione di implementazione del controllo di esempio CM-6: *un oggetto Criteri di gruppo controllato dalla configurazione viene gestito per tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene configurato in base al benchmark CIS per Windows Server 2012 R2 e viene personalizzato per rispettare i requisiti dell'organizzazione specifici per le esigenze operative del sistema. Questo oggetto Criteri di gruppo è stato creato, oltre ad altre risorse di configurazione, per documentare e assicurare l'implementazione coerente delle impostazioni di configurazione.*

  > Dichiarazione di implementazione del controllo di esempio CM-7: *un oggetto Criteri di gruppo controllato dalla configurazione viene gestito per tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene configurato in base al benchmark CIS per Windows Server 2012 R2 e viene personalizzato per rispettare i requisiti dell'organizzazione specifici per le esigenze operative del sistema. L'oggetto Criteri di gruppo viene usato per definire un'immagine di macchina virtuale baseline, che viene mantenuta per tutte le macchine virtuali Windows in Azure e riflette la configurazione delle macchine virtuali Windows per offrire solo le funzionalità essenziali per il funzionamento del sistema.*

  Gli strumenti di correzione baseline della sicurezza, inclusi oggetti Criteri di gruppo preconfigurati per macchine virtuali Windows e script della shell per macchine virtuali Linux, sono disponibili in [Microsoft](https://technet.microsoft.com/itpro/windows/keep-secure/windows-security-baselines), per macchine virtuali Windows, in [Center for Internet Security](https://benchmarks.cisecurity.org/) e in [Defense Information Systems Agency (DISA)](http://iase.disa.mil/stigs), per macchine virtuali Windows e Linux.  

Le macchine virtuali di Azure possono essere crittografate per rispettare i requisiti dei controlli di sicurezza per la protezione delle informazioni inattive [controllo NIST SP 800-53 SC-28]. I modelli di avvio rapido di Azure sono disponibili per distribuire la crittografia alle macchine virtuali [nuove](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image) ed esistenti per [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm) e [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm). 

> Dichiarazione di implementazione del controllo di esempio SC-28: *le macchine virtuali controllate dai clienti in Azure implementano la crittografia dei dischi per proteggere la riservatezza e l'integrità delle informazioni inattive. La Crittografia dischi di Azure per Windows viene implementata usando la funzionalità BitLocker di Windows. La crittografia dei dischi per macchine virtuali Linux viene implementata usando la funzionalità DM-Crypt di Linux.

È possibile distribuire alcune estensioni delle macchine virtuali di Azure per rispettare i requisiti dei controlli di sicurezza. L'[estensione Microsoft Antimalware per le macchine virtuali](https://docs.microsoft.com/azure/security/azure-security-antimalware) può essere distribuita in macchine virtuali nuove ed esistenti. L'estensione Antimalware può abilitare la protezione in tempo reale, oltre ad analisi periodiche e pianificate [controllo NIST SP 800-53 SI-3].

> Dichiarazione di implementazione del controllo di esempio SI-3: *le funzionalità di protezione antimalware per macchine virtuali Windows controllate dai clienti in Azure vengono implementate usando l'estensione Microsoft Antimalware per le macchine virtuali. Questa estensione viene configurata per eseguire analisi in tempo reale e periodiche (settimanali), aggiornare automaticamente il motore antimalware e le firme di protezione, eseguire azioni di correzione automatiche e fornire notifiche tramite Diagnostica di Azure.*

*Risorse e strumenti aggiuntivi*

[Blocchi predefiniti](https://github.com/mspnp/template-building-blocks) del modello di Azure Resource Manager che possono essere personalizzati e usati per distribuire le risorse di Azure, inclusa la distribuzione di [macchine virtuali](https://github.com/mspnp/template-building-blocks/tree/master/templates/buildingBlocks/multi-vm-n-nic-m-storage) ed [estensioni per le macchine virtuali](https://github.com/mspnp/template-building-blocks/tree/master/templates/buildingBlocks/virtualMachine-extensions). 

### <a name="virtual-network"></a>Rete virtuale

Rete virtuale di Azure (VNet) offre il controllo completo dei criteri di sicurezza e del routing in architetture di rete virtuale tramite la distribuzione e la configurazione di subnet, gruppi di sicurezza di rete e route definite dall'utente. I gruppi di sicurezza di rete possono essere applicati alle subnet o alle singole macchine virtuali, separando in modo logico le risorse in base al carico di lavoro, sulla base di un'architettura a più livelli, o per altre finalità. Nell'architettura di riferimento disponibile di seguito le risorse vengono raggruppate in subnet separate per il livello Web, aziendale e di dati e in subnet per le risorse e la gestione di Active Directory. I gruppi di sicurezza di rete vengono applicati a ogni subnet per limitare il traffico di rete nella rete virtuale. 
 
![Architettura di livello N con Microsoft Azure](./media/documentation-government-plan-compliance/compute-n-tier.png)

I gruppi di sicurezza di rete offrono il controllo completo dei percorsi di comunicazione tra le risorse [controllo NIST SP 800-53 AC-4].

> Dichiarazione di implementazione del controllo di esempio AC-4: *le macchine virtuali controllate dai clienti in Azure che implementano funzioni del livello Web, aziendale e dati, sono separate in base alla subnet. I gruppi di sicurezza di rete vengono definiti e applicati per ogni subnet che limita il flusso di informazioni a livello di rete al minimo necessario per il funzionamento del sistema informativo.*

I gruppi di sicurezza di rete possono essere applicati alle comunicazioni in uscita dalle subnet e dalle macchine virtuali. Ciò consente il controllo completo sulle comunicazioni tra i componenti del sistema informativo in Azure e i sistemi informativi esterni [controllo NIST SP 800-53 CA-3 (5)]. La regola di elaborazione dei gruppi di sicurezza di rete viene implementata come funzione che consente di negare tutto o di consentire in base a eccezioni. È anche possibile configurare route definite dall'utente per indirizzare le comunicazioni in entrata e in uscita da subnet e macchine virtuali specifiche tramite appliance virtuale, ad esempio un firewall o IDS/IPS, consentendo una gestione aggiuntiva delle comunicazioni del sistema.

> Dichiarazione di implementazione del controllo di esempio CA-3 (5): *tutte le comunicazioni in uscita originate dalle risorse controllate dai clienti in Azure vengono limitate tramite l'implementazione dei gruppi di sicurezza di rete, che sono configurati con un set di regole in uscita che nega tutto il traffico ad eccezione di quello consentito esplicitamente per supportare il funzionamento del sistema informativo, rispettando i requisiti di FedRAMP e DoD L4. Le route definite dall'utente vengono anche configurate per indirizzare tutto il traffico in uscita attraverso un'appliance virtuale di tipo firewall, configurato con un set di regole che consente la comunicazione solo con i sistemi informativi esterni approvati.*

L'architettura di riferimento precedente illustra il modo in cui le risorse di Azure possono essere raggruppate logicamente in subnet separate con set di regole dei gruppi di sicurezza di rete applicati per assicurare l'isolamento delle funzioni di sicurezza di rete e delle funzioni non relative alla sicurezza. In questo caso, i tre livelli di applicazione Web sono isolati dalla subnet di Active Directory e dalla subnet di gestione, che potrebbero ospitare il sistema informativo e gli strumenti e le risorse di gestione della sicurezza [controllo NIST SP 800-53 SC-3].

> Dichiarazione di implementazione del controllo di esempio SC-3: *le funzioni di sicurezza vengono isolate dalle funzioni non relative alla sicurezza nell'ambiente di Azure controllato dai clienti tramite l'implementazione di subnet e gruppi di sicurezza di rete applicati a tali subnet. Le risorse del sistema informativo dedicate al livello Web, aziendale e dati dell'applicazione Web vengono separate in modo logico dalla subnet di gestione, in cui vengono eseguite le attività correlate alla sicurezza.*

L'architettura di riferimento implementa anche i punti di controllo di accesso gestiti per l'accesso remoto al sistema informativo [controllo NIST SP 800-53 AC-17 (3)]. Un servizio di bilanciamento del carico con connessione Internet viene distribuito per gestire il traffico Internet in ingresso verso l'applicazione Web e la subnet di gestione include un jumpbox, o bastion host, tramite il quale viene controllato completamente l'accesso remoto correlato alla gestione per il sistema. I gruppi di sicurezza di rete limitano il traffico nella rete virtuale, assicurando che il traffico esterno venga indirizzato solo a risorse pubbliche designate.

> Dichiarazione di implementazione del controllo di esempio AC-17 (3): *l'accesso remoto ai componenti controllati dai clienti del sistema informativo in Azure è limitato a due punti di controllo di accesso di rete gestiti. 1) Il traffico Internet designato per l'applicazione Web viene gestito tramite un servizio di bilanciamento del carico con connessione Internet, che distribuisce il traffico a risorse di livello Web. 2) L'accesso remoto di gestione viene gestito tramite un bastion host in una subnet separata nell'ambiente. Il gruppo di sicurezza di rete applicato alla subnet di gestione, in cui si trova il jumpbox, consente connessioni solo dagli indirizzi IP pubblici consentiti e viene limitato solo al traffico di Desktop remoto.*

I gruppi di sicurezza di rete consentono il controllo completo delle comunicazioni tra le risorse di Azure e host e sistemi esterni, oltre alle comunicazioni tra subnet e host interni, separando i componenti dei sistemi informativi designati come accessibili pubblicamente e quelli non accessibili. Oltre alle soluzioni illustrate nell'architettura di riferimento precedente, Azure consente la distribuzione di appliance virtuali, ad esempio firewall e soluzioni IDS/IPS, che, quando usati insieme a route definite dall'utente, proteggono e gestiscono ulteriormente le connessioni tra le risorse di Azure e le reti e i sistemi informativi esterni [controllo NIST SP 800-53 SC-7].

> Dichiarazione di implementazione del controllo di esempio SC-7: *le risorse controllate dai clienti in Azure vengono protette tramite alcuni meccanismi distribuiti di protezione dei limiti. I gruppi di sicurezza di rete vengono definiti e applicati alle subnet di rete che limitano il traffico a livello di sistema informativo e nella rete virtuale. I gruppi di sicurezza di rete includono le regole dell'elenco di controllo di accesso che controllano il traffico da e verso le subnet e le macchine virtuali. I servizi di bilanciamento del carico vengono distribuiti per indirizzare il traffico Internet in ingresso a risorse specifiche. Le subnet assicurano che i componenti del sistema informativo accessibili pubblicamente vengano separate in modo logico dalle risorse non pubbliche.*

I set di regole per i gruppi di controllo di rete abilitano le restrizioni su porte di rete e protocolli specifici. Questo è un componente fondamentale per assicurare che i sistemi informativi vengano implementati in modo da fornire solo le funzionalità essenziali [controllo NIST SP 800-53 CM-7].

> Dichiarazione di implementazione del controllo di esempio CM-7: *i gruppi di sicurezza di rete applicati alle subnet e alle macchine virtuali nell'ambiente di Azure controllato dai clienti vengono implementati usando un approccio che consente di negare tutto il traffico tranne quello consentito in base alle eccezioni. In questo modo si assicura che sia permesso solo il traffico di rete su porte approvate in modo specifico, supportando il concetto della funzionalità minima.*

*Risorse e strumenti aggiuntivi*

La documentazione per l'architettura di riferimento precedente è disponibile nel [sito della documentazione](https://docs.microsoft.com/azure/guidance/guidance-compute-n-tier-vm) di Azure. I modelli di Azure Resource Manager per la distribuzione dell'architettura di riferimento sono inclusi nella stessa pagina. Il sito della documentazione di Azure include anche informazioni dettagliate sui [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) e sulle [route definite dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

### <a name="storage"></a>Archiviazione

I dati salvati nell'archiviazione di Azure vengono replicati in modo da assicurare la disponibilità elevata. Benché siano disponibili molte opzioni di replica, l'archiviazione con ridondanza geografica (GRS, Geo-Redundant Storage) e l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS, Read-Access Geo-Redundant Storage) assicurano che i dati vengano replicati in un'area secondaria. Le aree primarie e secondarie vengono abbinate per assicurare la distanza necessaria tra i data center, in modo da garantire la disponibilità in caso di interruzione del servizio o emergenza che interessa l'intera area [controllo NIST SP 800-53 CP-9]. Per ottenere un'archiviazione con ridondanza geografica e disponibilità elevata, selezionare l'archiviazione con ridondanza geografica (GRS) o l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) durante la creazione di un nuovo account di archiviazione. 

> Dichiarazione di implementazione del controllo di esempio CP-9: *tutti gli account di archiviazione controllati dai clienti in Azure implementano l'archiviazione con ridondanza geografica, assicurando che vengano mantenute sei copie di tutti i dati in nodi separati in due data center.*

La Crittografia del servizio di archiviazione di Azure protegge i dati inattivi negli account di archiviazione di Azure [controlli NIST SP 800-53 SC-28, SC-28 (1)]. Se questa funzionalità è abilitata, Azure crittografa automaticamente i dati prima del salvataggio permanente nell'archivio. I dati vengono crittografati tramite la crittografia AES a 256 bit. La Crittografia del servizio di archiviazione supporta la crittografia di BLOB in blocchi, BLOB di aggiunta e BLOB di pagine.

> Dichiarazione di implementazione del controllo di esempio SC-28, SC-28 (1): *la riservatezza e l'integrità di tutti i BLOB di archiviazione nell'ambiente di Azure controllato dai clienti vengono protette tramite la Crittografia del servizio di archiviazione di Azure, che usa la crittografia AES a 256 bit per tutti i dati inattivi.*

*Risorse e strumenti aggiuntivi*

Il sito della documentazione di Azure include informazioni dettagliate sulla [Crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/storage-service-encryption) e sulla [replica di archiviazione](https://docs.microsoft.com/azure/storage/storage-redundancy). 

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory offre funzionalità di gestione delle identità e di accesso per i sistemi informativi in esecuzione in Microsoft Azure. Tramite l'uso di servizi directory, gruppi di sicurezza e Criteri di gruppo, i clienti possono contribuire al controllo dell'accesso e dei criteri di sicurezza delle macchine virtuali che usano Azure Active Directory. Gli account e i gruppi di sicurezza possono essere usati per contribuire alla gestione dell'accesso al sistema informativo. I Criteri di gruppo sono utili per assicurare il rispetto dei requisiti di configurazione.

I gruppi di sicurezza e i servizi directory di Azure Active Directory possono contribuire all'implementazione degli schemi con controllo degli accessi in base al ruolo e al controllo dell'accesso al sistema informativo. È possibile che siano incluse implementazione dei controlli di sicurezza per:

- Gestione degli account [controllo NIST SP 800-53 AC-2]

  > Dichiarazione di implementazione del controllo di esempio AC-2.a: *Azure Active Directory viene usato per gestire l'accesso tramite l'implementazione del controllo degli accessi in base al ruolo mediante i gruppi di Active Directory. Sono previsti requisiti specifici per la gestione degli account e l'accesso agli account nei domini supportati da AAD. L'accesso ai server membri del dominio è supportato solo dai gruppi di sicurezza. Ogni gruppo ha un proprietario primario e secondario. I proprietari sono responsabili della gestione dell'accuratezza dell'appartenenza al gruppo, delle autorizzazioni e della descrizione.*

  > Dichiarazione di implementazione del controllo di esempio AC-2.c: *quando un utente richiede l'accesso a un gruppo di sicurezza, la richiesta deve essere approvata dal proprietario del gruppo in base ai criteri definiti per l'appartenenza. Le condizioni dell'account vengono determinate dai proprietari del gruppo di sicurezza.*

  > Dichiarazione di implementazione del controllo di esempio AC-2.f: *Azure Active Directory viene usato per gestire il controllo dell'accesso ai sistemi informativi. Gli amministratori degli account creano, abilitano, modificano, disabilitano o rimuovono account del sistema informativo in base alle esigenze specifiche, seguendo i criteri di gestione dell'account.*

  > Dichiarazione di implementazione del controllo di esempio AC-2 (1): *Azure Active Directory viene usato per gestire l'accesso implementando il controllo degli accessi in base al ruolo mediante i gruppi di Active Directory. Sono previsti requisiti specifici per la gestione degli account e l'accesso agli account nei domini supportati da AAD. L'accesso ai server membri del dominio è supportato solo dai gruppi di sicurezza. Ogni gruppo ha un proprietario primario e secondario. I proprietari sono responsabili della gestione dell'accuratezza dell'appartenenza al gruppo, delle autorizzazioni e della descrizione.*

  > Dichiarazione di implementazione del controllo di esempio AC-2 (2): *Azure Active Directory viene usato per gestire il controllo dell'accesso ai sistemi informativi. Gli amministratori degli account creano account temporanei in base ai criteri di gestione degli account. È necessario che questi account temporanei siano configurati con una scadenza conforme ai requisiti dei criteri.*

  > Dichiarazione di implementazione del controllo di esempio AC-2 (3): *Azure Active Directory gestisce il controllo dell'accesso ai sistemi informativi. Gli amministratori degli account creano account temporanei in base ai criteri di gestione degli account. È necessario che questi account temporanei siano configurati con una scadenza conforme ai requisiti dei criteri.*

  >Dichiarazione di implementazione del controllo di esempio AC-2 (7): *Azure Active Directory gestisce il controllo dell'accesso ai sistemi informativi in base a uno schema di tipo controllo degli accessi in base al ruolo, che organizza i privilegi del sistema informativo in ruoli che vengono assegnati ai gruppi di sicurezza. Gli amministratori dei gruppi di sicurezza sono responsabili della concessione dell'accesso agli utenti per l'assegnazione degli utenti al gruppo di sicurezza corretto. A ogni gruppo di sicurezza vengono assegnate autorizzazioni con l'accesso minimo appropriato per eseguire correttamente le rispettive attività.*

  > Dichiarazione di implementazione del controllo di esempio AC-2 (11): *Azure Active Directory gestisce il controllo dell'accesso ai sistemi informativi in Azure. Le restrizioni di utilizzo richieste dai criteri di sicurezza vengono definite e applicate in Azure Active Directory da parte degli amministratori degli account.*

- Imposizione dell'accesso [controllo NIST SP 800-53 AC-3]

  > Dichiarazione di implementazione del controllo di esempio AC-3: *Azure Active Directory impone le autorizzazioni approvate all'ambiente dei clienti usando il controllo degli accessi in base al ruolo. L'accesso ai gruppi di sicurezza di Azure Active Directory viene gestito dagli amministratori dei gruppi di sicurezza. Gli utenti vengono inseriti nei gruppi di sicurezza appropriati in base ai rispettivi ruoli, usando i principi dei privilegi minimi.*

- Privilegi minimi [controllo NIST SP 800-53 AC-6]

  > Dichiarazione di implementazione del controllo di esempio AC-6 (10): *Azure Active Directory impone le autorizzazioni approvate all'ambiente dei clienti usando il controllo degli accessi in base al ruolo. L'accesso ai gruppi di sicurezza di Azure Active Directory viene gestito dagli amministratori dei gruppi di sicurezza. Agli utenti senza privilegi non viene concesso l'accesso ai gruppi di sicurezza che consentirebbe a tali utenti di accedere alle funzioni con privilegi, incluse eventuali autorizzazioni che consentirebbero agli utenti di disabilitare, aggirare o modificare le misure di protezione.*

- Accesso remoto [controllo NIST SP 800-53 AC-17]

  > Dichiarazione di implementazione del controllo di esempio AC-17 (1): *Azure Active Directory viene usato per monitorare e controllare tutti gli accessi remoti. Azure Active Directory include report relativi a sicurezza, attività e controllo per la directory.*

- Protezione delle informazioni di controllo [controllo NIST SP 800-53 AU-9]

  > Dichiarazione di implementazione del controllo di esempio AU-9: *i controlli di accesso a gestione rigorosa vengono usati per proteggere le informazioni e gli strumenti di controllo da accesso, modifica ed eliminazione non autorizzati. Azure Active Directory applica l'accesso logico approvato agli utenti che usano un approccio a più livelli per le autorizzazioni di accesso e i controlli logici mediante i criteri di Active Directory e le appartenenze ai gruppi basati sui ruoli. La possibilità di visualizzare le informazioni di controllo e usare gli strumenti di controllo è limitata agli utenti che necessitano di queste autorizzazioni.*

  > Dichiarazione di implementazione del controllo di esempio AU-9 (4): *Azure Active Directory limita la gestione della funzionalità di controllo ai membri dei gruppi di sicurezza appropriati. Queste autorizzazioni vengono concesse solo al personale con esigenze specifiche di accesso alla gestione della funzionalità di controllo.*

- Restrizioni di accesso per la modifica [controllo NIST SP 800-53 CM-5]

  > Dichiarazione di implementazione del controllo di esempio CM-5: *il controllo degli accessi in base al ruolo imposto da Azure Active Directory viene usato per definire, documentare, approvare e imporre le restrizioni di accesso logico associate alle modifiche. Tutti gli account creati nel sistema sono basati sui ruoli. Il personale richiede l'accesso agli amministratori degli account e, in caso di approvazione, gli utenti vengono inseriti nei gruppi di sicurezza appropriati, in base ai rispettivi ruoli. L'accesso all'ambiente di produzione è consentito solo ai membri di gruppi di sicurezza specifici dopo l'approvazione.*

  > Dichiarazione di implementazione del controllo di esempio CM-5 (1): *Azure Active Directory impone restrizioni di accesso logico tramite le appartenenze ai gruppi di sicurezza. È quindi necessario che i proprietari dei gruppi di sicurezza concedano l'accesso a un gruppo di sicurezza specifico.*

- Identificazione e autenticazione degli utenti [controllo NIST SP 800-53 IA-2]

  > Dichiarazione di implementazione del controllo di esempio IA-2: *il personale che accede all'ambiente del sistema informativo viene identificato in modo univoco in base al rispettivo nome utente e l'autenticazione viene eseguita tramite Azure Active Directory. L'autenticazione ad Azure Active Directory è necessaria per accedere al sistema informativo.*

  > Dichiarazione di implementazione del controllo di esempio IA-2 (8): *l'accesso all'ambiente di produzione viene protetto da attacchi di riproduzione tramite la funzionalità predefinita Kerberos di Azure Active Directory. Nell'autenticazione Kerberos l'autenticatore inviato dal client contiene dati aggiuntivi, ad esempio un elenco di indirizzi IP crittografato, timestamp di client e durata del ticket. Se un pacchetto viene riprodotto, viene controllato il timestamp. Se il timestamp è precedente o uguale a quello di un autenticatore precedente, il pacchetto viene rifiutato.*

  > Dichiarazione di implementazione del controllo di esempio IA-2 (9): *tutti gli account vengono protetti da attacchi di riproduzione tramite la funzionalità predefinita Kerberos di Azure Active Directory. Nell'autenticazione Kerberos l'autenticatore inviato dal client contiene dati aggiuntivi, ad esempio un elenco di indirizzi IP crittografato, timestamp di client e durata del ticket. Se un pacchetto viene riprodotto, viene controllato il timestamp. Se il timestamp è precedente o uguale a quello di un autenticatore precedente, il pacchetto viene rifiutato.*

- Gestione degli identificatori [controllo NIST SP 800-53 IA-4]

  > Dichiarazione di implementazione del controllo di esempio IA-4.b: *gli identificatori di account Azure Active Directory vengono usati per identificare gli utenti. Questi identificatori univoci non vengono riutilizzati.*

  > Dichiarazione di implementazione del controllo di esempio IA-4.c: *Azure Active Directory è il repository di account centrale usato per offrire l'accesso all'ambiente del servizio. Quando vengono creati account in Azure Active Directory, l'identificatore univoco dell'utente viene assegnato al singolo utente.*

  > Dichiarazione di implementazione del controllo di esempio IA-4.d: *gli identificatori univoci degli utenti non vengono mai riutilizzati. Questo comportamento viene applicato da Azure Active Directory.*

  > Dichiarazione di implementazione del controllo di esempio IA-4.e: *tutti gli account in Azure Active Directory sono configurati per la disabilitazione automatica dopo 35 giorni di inattività.*

  > Dichiarazione di implementazione del controllo di esempio IA-4 (4): *Azure Active Directory indica i terzisti e i fornitori usando convenzioni di denominazione applicate alle rispettive credenziali univoche di Azure Active Directory.*

- Gestione degli autenticatori [controllo NIST SP 800-53 IA-5]

  > Dichiarazione di implementazione del controllo di esempio IA-5.b: *Azure Active Directory assegna un'identificazione univoca e una password temporanea casuale che rispetta i requisiti dei criteri nella fase iniziale della creazione dell'account. Azure Active Directory gestisce l'identificazione univoca associata all'account per tutta la durata dell'account. L'identificazione dell'account non viene mai ripetuta con Azure Active Directory*

  > Dichiarazione di implementazione del controllo di esempio IA-5.c: *Azure Active Directory assicura che le password emesse rispettino i requisiti dei criteri relativi alla complessità delle password.*

  > Dichiarazione di implementazione del controllo di esempio IA-5.d: *Le procedure di distribuzione iniziale degli autenticatori vengono gestite dagli amministratori degli account. Le modifiche all'accesso agli account utente vengono sottoposte al processo di gestione degli account, in base a quanto definito in AC-2. In caso di perdita o danneggiamento di un autenticatore, gli amministratori degli account seguono i processi definiti per la reimpostazione, la nuova emissione o la revoca dell'autenticatore, in base alla necessità.*

  > Dichiarazione di implementazione del controllo di esempio IA-5 (1).c: *Azure Active Directory viene usato per assicurare che tutte le password siano protette tramite crittografia durante l'archiviazione e la trasmissione. Le password archiviate da Azure Active Directory vengono associate automaticamente ad hash come parte della funzionalità di Azure Active Directory.*

  > Dichiarazione di implementazione del controllo di esempio IA-5 (1).f: *Azure Active Directory viene usato per gestire il controllo dell'accesso al sistema informativo. Nelle fasi iniziali di ogni creazione di account o durante ogni generazione di password temporanea, Azure Active Directory viene usato per richiedere che l'utente cambi la password all'accesso successivo.*

  > Dichiarazione di implementazione del controllo di esempio IA-5 (4): *Azure Active Directory è lo strumento automatico usato per determinare se gli autenticatori di password sono sufficientemente sicuri per soddisfare le restrizioni relative a lunghezza, complessità, rotazione e durata delle password definite in IA-5 (1). Azure Active Directory assicura che il livello di sicurezza dell'autenticatore di password in fase di creazione rispetti questi standard.*

È possibile usare la configurazione di Criteri di gruppo di Azure Active Directory per distribuire criteri di sicurezza per i clienti conformi a requisiti specifici di sicurezza. Questi criteri possono essere applicati alle macchine virtuali di Azure dei clienti tramite Azure Active Directory.
Le configurazioni di Criteri di gruppo di Azure Active Directory possono includere implementazioni dei controlli di sicurezza per:

- Privilegi minimi [controllo NIST SP 800-53 AC-6]

  > Dichiarazione di implementazione del controllo di esempio AC-6 (9): *un oggetto Criteri di gruppo controllato dalla configurazione viene applicato a tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene personalizzato in modo da rispettare i requisiti dell'organizzazione, incluso il controllo dell'esecuzione di funzioni con privilegi.*

- Tentativi di accesso non riusciti [controllo NIST SP 800-53 AC-7]

  > Dichiarazione di implementazione del controllo di esempio AC-7: *un oggetto Criteri di gruppo controllato dalla configurazione viene applicato a tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene personalizzato in modo da rispettare i requisiti dell'organizzazione, incluso l'avvio di un blocco dell'account dopo tentativi di accesso non riusciti consecutivi.*

- Controllo delle sessioni simultanee [controllo NIST SP 800-53 AC-10], blocco della sessione [controllo NIST SP 800-53 AC-11], chiusura della sessione [controllo NIST SP 800-53 AC-12]

  > Dichiarazione di implementazione del controllo di esempio AC-10: *un oggetto Criteri di gruppo controllato dalla configurazione viene applicato a tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene personalizzato per rispettare i requisiti dell'organizzazione, inclusa la limitazione degli utenti a una singola sessione di Servizi Desktop remoto, che rispetta i requisiti di FedRAMP e DoD.*

  > Dichiarazione di implementazione del controllo di esempio AC-11.a: *un oggetto Criteri di gruppo controllato dalla configurazione viene applicato a tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene personalizzato per rispettare i requisiti dell'organizzazione, incluso l'avvio di un blocco di sessione dopo 900 secondi (15 minuti) di inattività, che viene mantenuto fino alla ripetizione dell'autenticazione dell'utente.*

  > Dichiarazione di implementazione del controllo di esempio AC-12: *Windows chiude automaticamente le sessioni di Desktop remoto alla ricezione di una richiesta di disconnessione dall'utente del cliente. Un oggetto Criteri di gruppo controllato dalla configurazione viene inoltre applicato a tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene personalizzato per rispettare i requisiti dell'organizzazione, inclusa la chiusura di una sessione di Desktop remoto dopo 900 secondi (15 minuti) di inattività.*

- Gestione degli autenticatori [controllo NIST SP 800-53 IA-5]

  > Dichiarazione di implementazione del controllo di esempio IA-5.f: *un oggetto Criteri di gruppo controllato dalla configurazione viene applicato a tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene personalizzato per rispettare i requisiti dell'organizzazione, incluse le restrizioni sulle password dell'account locale che impongono restrizioni minime (1 giorno) e massime (60 giorni) per la durata, le condizioni di riutilizzo (24 password), la lunghezza (14 caratteri) e i requisiti di complessità, che rispettano i requisiti di FedRAMP.*

  > Dichiarazione di implementazione del controllo di esempio IA-5.g: *un oggetto Criteri di gruppo controllato dalla configurazione viene applicato a tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene personalizzato per rispettare i requisiti dell'organizzazione, incluse le restrizioni sulle password dell'account locale che impongono restrizioni massime (60 giorni) per la durata, che rispettano i requisiti di FedRAMP.*

  > Dichiarazione di implementazione del controllo di esempio IA-5 (1).a: *un oggetto Criteri di gruppo controllato dalla configurazione viene applicato a tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene personalizzato per rispettare i requisiti dell'organizzazione, inclusi i requisiti relativi alla complessità, che rispettano i requisiti di FedRAMP.*

  > Dichiarazione di implementazione del controllo di esempio IA-5 (1).b: *un oggetto Criteri di gruppo controllato dalla configurazione viene applicato a tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene personalizzato per rispettare i requisiti dell'organizzazione, inclusi i requisiti relativi alla complessità, che rispettano i requisiti di FedRAMP.*

  > Dichiarazione di implementazione del controllo di esempio IA-5 (1).d: *un oggetto Criteri di gruppo controllato dalla configurazione viene applicato a tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene personalizzato per rispettare i requisiti dell'organizzazione, incluse le restrizioni sulle password dell'account locale che impongono restrizioni minime (1 giorno) e massime (60 giorni) per la durata, che rispettano i requisiti di FedRAMP".

  > Dichiarazione di implementazione del controllo di esempio IA-5 (1).e: *un oggetto Criteri di gruppo controllato dalla configurazione viene applicato a tutti i computer Windows controllati dai clienti in Azure. L'oggetto Criteri di gruppo viene personalizzato per rispettare i requisiti dell'organizzazione, incluse le restrizioni relative alle condizioni di riutilizzo (24 password), che rispettano i requisiti di FedRAMP.*

*Risorse e strumenti aggiuntivi*

Sono disponibili strumenti, documentazione, modelli e altre risorse utili per la distribuzione sicura dei servizi e delle funzionalità di Azure. Per un'introduzione ad Azure Active Directory, vedere la [Documentazione di Microsoft Azure](https://docs.microsoft.com/azure/active-directory/).

### <a name="key-vault"></a>Insieme di credenziali delle chiavi

Azure Key Vault offre la protezione per le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Tramite Azure Key Vault, i clienti possono creare, gestire e proteggere le chiavi e i segreti. I contenitori sicuri (insiemi di credenziali) possono essere usati per archiviare e gestire le chiavi crittografiche e i segreti in modo sicuro. Azure Key Vault può essere usato per generare chiavi crittografiche mediante moduli di protezione hardware con convalida FIPS 140-2 di livello 2.

I contenitori di Azure Key Vault consentono di archiviare in modo sicuro le chiavi crittografiche con disponibilità elevata. È possibile che siano incluse implementazione dei controlli di sicurezza per:

-   Protezione degli autenticatori [controllo NIST SP 800-53 IA-5 (7)]

  > Dichiarazione di implementazione del controllo di esempio IA-5 (7): *l'uso di autenticatori statici non crittografati incorporati in applicazioni, script di accesso o tasti funzione è esplicitamente vietato. Qualsiasi script o applicazione che usa un autenticatore effettua una chiamata a un contenitore di Azure Key Vault prima di ogni uso. L'accesso ai contenitori di Azure Key Vault viene controllato e ciò consente il rilevamento di violazioni del divieto, nel caso in cui un account del servizio venga usato per accedere a un sistema senza una chiamata corrispondente al contenitore di Azure Key Vault.*

- Attivazione e gestione delle chiavi crittografiche [controllo NIST SP 800-53 SC-12 (1)]

  > Dichiarazione di implementazione del controllo di esempio SC-12 (1): *Azure Key Vault viene usato per archiviare le chiavi crittografiche e i segreti. Questo servizio controlla e monitora l'accesso ai segreti e viene usato per assicurarsi che i segreti non vengano persi.*

Azure Key Vault può essere usato per creare chiavi crittografiche mediante moduli di protezione hardware che rispettano la convalida FIPS 140-2 di livello 2. Le configurazioni di Criteri di gruppo di Azure Active Directory possono includere implementazioni dei controlli di sicurezza per:

- Attivazione e gestione delle chiavi crittografiche [controllo NIST SP 800-53 SC-12 (2)]

  > Dichiarazione di implementazione del controllo di esempio SC-12 (2): *Azure Key Vault viene usato per produrre, controllare e distribuire le chiavi crittografiche. Queste chiavi vengono generate mediante moduli di protezione hardware con convalida FIPS 140-2 di livello 2. Le chiavi vengono archiviate e gestite in contenitori crittografati in modo sicuro in Azure Key Vault.*

*Risorse e strumenti aggiuntivi*

Sono disponibili strumenti, documentazione, modelli e altre risorse utili per la distribuzione sicura dei servizi e delle funzionalità di Azure. Per un'introduzione ad Azure Key Vault, vedere la [Documentazione di Microsoft Azure](https://docs.microsoft.com/azure/key-vault/).

### <a name="operations-management-suite"></a>Operations Management Suite

Microsoft Operations Management Suite (OMS) è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud. Le funzionalità di sicurezza e conformità consentono di identificare, valutare e ridurre i rischi di sicurezza per l'infrastruttura. Queste funzionalità di OMS vengono implementate tramite più soluzioni, incluso Log Analytics, che analizzano i dati di log e monitorano le configurazioni di sicurezza.

I servizi di Sicurezza e controllo abilitati da OMS possono contribuire all'implementazione del monitoraggio e della registrazione degli account. È possibile che siano incluse implementazione dei controlli di sicurezza per:

- Gestione degli account [controllo NIST SP 800-53 AC-2]

  > Dichiarazione di implementazione del controllo di esempio AC-2.g: *Microsoft Operation Management Suite (OMS) viene usato per monitorare l'uso degli account di sistema. OMS crea log di controllo per account di sistema, che possono essere quindi analizzati con gli strumenti di analisi di OMS, e genera avvisi in base a un set definito di criteri.*

  > Dichiarazione di implementazione del controllo di esempio AC-2 (4): *Microsoft Operation Management Suite (OMS) viene usato per generare log di controllo per funzionalità di gestione degli account, ad esempio per azioni di creazione, modifica, abilitazione, disabilitazione e rimozione degli account. OMS viene usato per avvisare il proprietario del sistema in caso di esecuzione di una delle condizioni indicate.*

  > Dichiarazione di implementazione del controllo di esempio AC-2 (12): *Microsoft Operation Management Suite (OMS) viene usato per monitorare l'uso degli account di sistema alla ricerca di usi atipici. OMS crea log di controllo per account di sistema, che possono essere quindi analizzati con gli strumenti di analisi di OMS e avvisa il personale appropriato in base a un set definito di criteri.*

- Privilegi minimi [controllo NIST SP 800-53 AC-6]

  > Dichiarazione di implementazione del controllo di esempio AC-6 (9): *Microsoft Operation Management Suite (OMS) viene usato per monitorare l'esecuzione delle funzioni con privilegi. OMS crea log di controllo per un elenco di funzionalità specifiche, che possono essere quindi analizzati con gli strumenti di analisi di OMS, e genera avvisi in base a un set definito di criteri.*

 - Accesso remoto [controllo NIST SP 800-53 AC-17]

  > Dichiarazione di implementazione del controllo di esempio AC-17 (1): *Microsoft Operation Management Suite (OMS) viene usato per monitorare e controllare l'accesso remoto. OMS include report relativi a sicurezza, attività e controllo per la directory.*

- Eventi di controllo [controllo NIST SP 800-53 AU-2]

  > Dichiarazione di implementazione del controllo di esempio AU-2: *Microsoft Operation Management Suite (OMS) viene usato per controllare gli eventi di accesso all'account riusciti e non riusciti, gli eventi di gestione degli account, l'accesso agli oggetti, la modifica dei criteri, le funzionalità relative ai privilegi, il rilevamento dei processi e gli eventi di sistema. Per le applicazioni Web: tutta l'attività dell'amministratore, i controlli di autenticazione, i controlli di autorizzazione, le eliminazioni di dati, l'accesso ai dati, le modifiche dei dati e le modifiche delle autorizzazioni, oltre a qualsiasi altro set di eventi definito dai clienti.*

- Contenuto dei record di controllo [controllo NIST SP 800-53 AU-3]

  > Dichiarazione di implementazione del controllo di esempio AU-3 (2): *Microsoft Operation Management Suite (OMS) viene usato come strumento di gestione e configurazione centralizzato per tutti i record di controllo generati da apparecchiature di rete, archiviazione e calcolo.*

- Capacità dell'archiviazione di controllo [controllo NIST SP 800-53 AU-4]

  > Dichiarazione di implementazione del controllo di esempio AU-4: *Microsoft Operation Management Suite (OMS) viene usato per la gestione e la configurazione centralizzate di tutti i record di controllo generati da apparecchiature di rete, archiviazione e calcolo. Lo strumento di gestione centralizzato viene usato per configurare la capacità dell'archiviazione di controllo.*

- Risposta a errori di elaborazione di controllo [controllo NIST SP 800-53 AU-5]

  > Dichiarazione di implementazione del controllo di esempio AU-5: *Microsoft Operation Management Suite (OMS) viene usato con la soluzione Sicurezza e controllo per avvisare il personale definito dall'organizzazione in caso di errori di elaborazione di controllo.*

  > Dichiarazione di implementazione del controllo di esempio AU-5 (1): *Microsoft Operation Management Suite (OMS) viene usato con la soluzione Sicurezza e controllo per avvisare il personale definito dall'organizzazione entro il periodo di tempo definito dall'organizzazione in caso di raggiungimento di una percentuale del volume massimo del repository definita dall'organizzazione da parte dell'archiviazione del record di controllo.*

  > Dichiarazione di implementazione del controllo di esempio AU-5 (2): *Microsoft Operation Management Suite (OMS) viene usato con la soluzione Sicurezza e controllo per avvisare il personale definito dall'organizzazione in tempo reale in caso di errore degli eventi controllabili definiti nei criteri dell'organizzazione.*

- Verifica, analisi e report di controllo [controllo NIST SP 800-53 AU-6]

  > Dichiarazione di implementazione del controllo di esempio AU-6 (3): *Microsoft Operation Management Suite (OMS) viene usato con la soluzione Sicurezza e controllo, oltre che con la soluzione Log Analytics, per analizzare diversi repository di log di controllo per offrire la consapevolezza situazionale a livello di organizzazione. Lo strumento di analisi viene usato per la creazione di report relativi ai log di controllo, per offrire la consapevolezza situazionale.*

  > Dichiarazione di implementazione del controllo di esempio AU-6 (4): *Microsoft Operation Management Suite (OMS) viene usato per la gestione e la configurazione centralizzate di tutti i record di controllo. Lo strumento di gestione centralizzato consente la verifica e l'analisi di tutti i record di tutti i controlli da tutte le origini.*

  > Dichiarazione di implementazione del controllo di esempio AU-6 (5): *Microsoft Operation Management Suite (OMS) viene usato con la soluzione Sicurezza e controllo, oltre che con la soluzione Log Analytics, per analizzare diversi dati correlati alla sicurezza generati dai dati relativi all'analisi delle vulnerabilità, al monitoraggio del sistema informativo e alle prestazioni. L'analisi di queste diverse risorse consente di identificare in modo ottimale le attività sospette.*

- Riduzione del controllo e generazione di report [controllo NIST SP 800-53 AU-7]

  > Dichiarazione di implementazione del controllo di esempio AU-7: *Microsoft Operation Management Suite (OMS) viene usato con la soluzione Sicurezza e controllo, oltre che con la soluzione Log Analytics, per generare report su richiesta leggibili dagli utenti, che consentono di ottenere un'analisi degli eventi di sicurezza imprevisti dopo che si sono verificati. L'uso dello strumento Microsoft Log Analytics non modifica in modo permanente o irreversibile il contenuto del record di controllo originale o l'ordine cronologico.*

  > Dichiarazione di implementazione del controllo di esempio AU-7 (1): *Microsoft Operation Management Suite (OMS) viene usato con la soluzione Sicurezza e controllo per eseguire query relative agli eventi controllabili definiti dall'organizzazione.*

- Protezione delle informazioni di controllo [controllo NIST SP 800-53 AU-9]

  > Dichiarazione di implementazione del controllo di esempio AU-9: *Microsoft Operation Management Suite (OMS) viene usato con i gruppi di sicurezza per gestire l'accesso alle informazioni e agli strumenti di controllo, per impedire l'accesso, la modifica e l'eliminazione non autorizzati dei record di controllo. La possibilità di visualizzare le informazioni di controllo e usare gli strumenti di controllo è limitata agli utenti che necessitano di queste autorizzazioni.*

  > Dichiarazione di implementazione del controllo di esempio AU-9 (2): *Microsoft Operation Management Suite (OMS) viene usato con la soluzione Sicurezza e controllo, oltre che con Backup di Azure per eseguire il backup dei log di controllo in Azure, che quindi replica i dati in modo da assicurare l'affidabilità e la disponibilità dei dati. Backup di Azure offre una posizione sicura per l'archiviazione dei log di controllo in un sistema diverso da quello sottoposto a controllo.* 

  > Dichiarazione di implementazione del controllo di esempio AU-9 (4): *Microsoft Operation Management Suite (OMS) viene usato con i gruppi di sicurezza per gestire l'accesso alle informazioni e agli strumenti di controllo. Queste autorizzazioni vengono concesse solo al personale con esigenze specifiche di accesso alla gestione della funzionalità di controllo.*

- Conservazione dei record di controllo [controllo NIST SP 800-53 AU-11]

  > Dichiarazione di implementazione del controllo di esempio AU-11: *Microsoft Operation Management Suite (OMS) viene usato con la soluzione Sicurezza e controllo per configurare la conservazione dei log di controllo. La conservazione dei log di controllo viene configurata in modo da conservare i dati di controllo per almeno 90 giorni.*

- Generazione di controllo [controllo NIST SP 800-53 AU-12]

  > Dichiarazione di implementazione del controllo di esempio AU-12: *Microsoft Operation Management Suite (OMS) viene usato con la soluzione Sicurezza e controllo per raccogliere gli eventi controllabili definiti in AU-02 dai componenti del sistema informativo. La soluzione Sicurezza e controllo di OMS viene usata dal personale definito dall'organizzazione per definite gli eventi controllabili da raccogliere da dispositivi specifici.*

  > Dichiarazione di implementazione del controllo di esempio AU-12 (1): *Microsoft Operation Management Suite (OMS) viene usato con la soluzione Sicurezza e controllo, oltre che con la soluzione Log Analytics, per compilare i record di controllo dai componenti del sistema che possono essere ordinati in base a timestamp per creare un audit trail a livello di sistema.*

- Restrizioni di accesso per la modifica [controllo NIST SP 800-53 CM-5]

  > Dichiarazione di implementazione del controllo di esempio CM-5 (1): *Microsoft Operation Management Suite (OMS) viene usato con la soluzione Sicurezza e controllo e con Active Directory per applicare le restrizioni di accesso logico tramite le appartenenze ai gruppi di sicurezza. Le azioni eseguite da questi gruppi di sicurezza vengono controllate da OMS.*

- Inventario dei componenti del sistema informativo [controllo NIST SP 800-53 CM-8]

  > Dichiarazione di implementazione del controllo di esempio CM-8 (3): *Microsoft Operation Management Suite (OMS) viene usato con la soluzione Sicurezza e controllo, oltre che con la soluzione antimalware, per rilevare la presenza di software non autorizzato. In caso di rilevamento, OMS disabilita il componente infetto e invia un avviso al personale definito dall'organizzazione.*

Sono disponibili strumenti, documentazione, modelli e altre risorse utili per la distribuzione sicura dei servizi e delle funzionalità di Azure. Per un'introduzione a Operation Management Suite, vedere la [Documentazione di Microsoft Azure](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview/).

### <a name="additional-implementation-guidance"></a>Indicazioni aggiuntive sull'implementazione

Oltre ai servizi principali di Azure illustrati in precedenza, alcune funzionalità predefinite possono aiutare le organizzazioni a rispettare i requisiti per la conformità con i controlli di sicurezza. Tutte le risorse di Azure vengono organizzate in gruppi di risorse. Gli asset possono essere organizzati ulteriormente tramite l'applicazione di tag, ovvero coppie di chiave-valore che possono essere selezionate dai clienti per identificare le risorse in base alla categoria o ad altre proprietà. I gruppi di risorse di Azure assicurano l'identificazione completa e il rilevamento di tutte le risorse dei clienti distribuite in Azure [controllo NIST SP 800-53 CM-8]. 

> Dichiarazione di implementazione del controllo di esempio CM-8: *tutte le risorse controllate dai clienti distribuite appartengono al gruppo di risorse "Customer-resources". Nel portale di Azure tutti gli asset distribuiti vengono identificati nel pannello del gruppo di risorse, in modo da assicurare che l'inventario sia preciso e aggiornato. I tag vengono applicati alle risorse, in base alla necessità, in modo da rispettare i requisiti di rilevamento e di creazione di report.*

I modelli di Azure Resource Manager consentono ai clienti di definire la configurazione di risorse nella rispettiva distribuzione di Azure. I modelli di Resource Manager sono basati su JSON e usano la sintassi dichiarativa per documentare le risorse, i parametri di configurazione e altri aspetti di un ambiente di Azure. I modelli di Resource Manager vengono generati automaticamente quando le soluzioni di Azure vengono distribuite dal portale di Azure e possono essere anche creati e modificati manualmente, per rispettare esigenze specifiche dei clienti. Questi modelli di Resource Manager possono essere usati come rappresentazione della configurazione baseline per un sistema informativo [controllo NIST SP 800-53 CM-2]. I modelli di Resource Manager esistenti, ad esempio i blocchi predefiniti indicati in precedenza, possono essere personalizzati per rispettare le esigenze di qualsiasi distribuzione.

> Dichiarazione di implementazione del controllo di esempio CM-2: *in quanto componenti della configurazione baseline del sistema informativo, i modelli di Azure Resource Manager vengono mantenuti sotto il controllo di configurazione e rappresentano le risorse distribuite controllate dai clienti e la configurazione dei componenti del sistema informativo in Azure. Questi modelli acquisiscono le risorse distribuite, incluse le macchine virtuali, le risorse di archiviazione e le risorse di rete, ad esempio le configurazione delle risorse di rete che controllano l'ingresso e l'uscita del traffico di rete nell'ambiente di Azure del cliente.*

## <a name="next-steps"></a>Passaggi successivi

Per domande relative ai processi di Azure Blueprint, FedRAMP, DoD o Agency ATO oppure per ottenere assistenza di altro tipo per la conformità o inviare commenti e suggerimenti su Blueprint, inviare un messaggio di posta elettronica all'indirizzo [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com).

[Centro protezione Microsoft](https://www.microsoft.com/trustcenter/Compliance/default.aspx)

[Blog di Microsoft Azure per enti pubblici. ](https://blogs.msdn.microsoft.com/azuregov/)



<!--HONumber=Jan17_HO4-->


