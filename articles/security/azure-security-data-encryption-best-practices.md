---
title: Sicurezza dei dati e la crittografia procedure consigliate - Microsoft Azure
description: Questo articolo presenta una serie di procedure consigliate per la sicurezza dei dati e la crittografia usando le funzionalità integrate di Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 9955450b468ef38ba456d7ee73d9681de677494d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65190717"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Procedure consigliate per la protezione dei dati di Azure e crittografia
Questo articolo descrive le procedure consigliate per la protezione dei dati e la crittografia.

Le procedure consigliate si basano su opinioni concordanti e funzionino con le caratteristiche e le capacità correnti della piattaforma Azure. Le opinioni e le tecnologie cambiano nel tempo e questo articolo viene aggiornato regolarmente per riflettere tali modifiche.

## <a name="protect-data"></a>Proteggere i dati
Per facilitare la protezione dei dati nel cloud, tenere conto dei possibili stati in cui possono trovarsi i dati e dei controlli disponibili per tale stato. Le procedure consigliate per la crittografia e la protezione dei dati di Azure sono correlate agli stati seguenti dei dati:

- Inattivi: sono inclusi tutti gli oggetti, i contenitori e i tipi di archiviazione di informazioni esistenti in forma statica nei supporti fisici, siano essi dischi magnetici o dischi ottici.
- In transito: quando vengono trasferiti tra componenti, posizioni o programmi, i dati vengono considerati in transito. Esempi di questo stato sono il trasferimento in rete, attraverso un bus di servizio, da locale a cloud e viceversa e incluse le connessioni ibride come ExpressRoute, o durante un processo di input/output.

## <a name="choose-a-key-management-solution"></a>Scegliere una soluzione di gestione delle chiavi

La protezione delle chiavi è essenziale per proteggere i dati nel cloud.

[Azure Key Vault](../key-vault/key-vault-overview.md) consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. L'insieme di credenziali chiave semplifica il processo di gestione delle chiavi e consente di mantenere il controllo delle chiavi che accedono ai dati e li crittografano. Gli sviluppatori possono creare chiavi per lo sviluppo e il test in pochi minuti ed eseguirne facilmente la migrazione alle chiavi di produzione. Gli amministratori della sicurezza possono concedere (e revocare) le autorizzazioni per chiavi, in base alle esigenze.

Key Vault consente di creare più contenitori sicuri denominati insiemi di credenziali. Questi insiemi di credenziali sono supportati da moduli di protezione hardware. Gli insiemi di credenziali consentono di ridurre le probabilità di perdita accidentale di informazioni di sicurezza centralizzando l'archiviazione dei segreti delle applicazioni. Gli insiemi di credenziali delle chiavi controllano e registrano anche l'accesso a tutti gli elementi archiviati al loro interno. Azure Key Vault può gestire la richiesta e il rinnovo dei certificati TLS (Transport Layer Security). Fornisce funzionalità che creano una solida soluzione per la gestione del ciclo di vita dei certificati.

Azure Key Vault è progettato per supportare chiavi e segreti delle applicazioni. Key Vault non è progettato come archivio per le password utente.

Di seguito vengono indicate le procedure consigliate per l'uso di Key Vault.

**Procedura consigliata**: concedere l'accesso a utenti, gruppi e applicazioni in un ambito specifico.   
**Dettagli**: usare i ruoli predefiniti per il controllo degli accessi in base al ruolo. Ad esempio, per concedere l'accesso a un utente in modo che possa gestire insiemi di credenziali delle chiavi, si assegna all'utente un ruolo predefinito [Collaboratore di Key Vault](../role-based-access-control/built-in-roles.md) in un ambito specifico. In questo caso, l'ambito può essere una sottoscrizione, un gruppo di risorse o semplicemente uno specifico insieme di credenziali delle chiavi. Se i ruoli predefiniti non soddisfano specifiche esigenze, è possibile [definire ruoli personalizzati](../role-based-access-control/custom-roles.md).

**Procedura consigliata**: verificare ciò a cui gli utenti hanno accesso.   
**Dettagli**: L'accesso a un insieme di credenziali delle chiavi è controllato tramite due interfacce separate: piano di gestione e piano dati. I controlli di accesso al piano di gestione e al piano dati funzionano in maniera indipendente.

Usare il controllo degli accessi in base al ruolo per verificare ciò a cui gli utenti hanno accesso. Se ad esempio si vuole consentire a un'applicazione l'uso delle chiavi di un insieme di credenziali delle chiavi, è sufficiente concedere autorizzazioni di accesso al piano dati usando criteri di accesso all'insieme di credenziali delle chiavi. Per questa applicazione non sono necessarie autorizzazioni di accesso al piano di gestione. Al contrario, se si vuole che un utente sia in grado di leggere i tag e le proprietà dell'insieme di credenziali, senza avere accesso a chiavi, segreti o certificati, è possibile concedere l'accesso "read" usando il controllo degli accessi in base al ruolo e non sono necessarie autorizzazioni di accesso al piano dati.

**Procedura consigliata**: archiviare i certificati nell'insieme di credenziali delle chiavi. I certificati sono preziosi. Se finiscono nelle mani sbagliate, la sicurezza dell'applicazione o dei dati può essere compromessa.   
**Dettagli**: Azure Resource Manager può distribuire in modo sicuro i certificati archiviati in Azure Key Vault quando vengono distribuite le macchine virtuali. Impostando criteri di accesso appropriati per l'insieme di credenziali delle chiavi, è possibile anche controllare chi ottiene accesso al certificato. Un altro vantaggio è rappresentato dal fatto che è possibile gestire tutti i certificati da un unico punto in Azure Key Vault. Per altre informazioni, vedere il blog relativo alla [distribuzione di certificati nelle macchine virtuali dall'insieme di credenziali delle chiavi gestito dal cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).

**Procedura consigliata**: assicurarsi che sia possibile ripristinare un'eliminazione di insiemi di credenziali delle chiavi o di oggetti al loro interno.   
**Dettagli**: l'eliminazione di insiemi di credenziali delle chiavi o di oggetti al loro interno può essere un'attività accidentale o intenzionale. Abilitare le funzionalità di eliminazione temporanea e di protezione dall'eliminazione nell'insieme di credenziali delle chiavi, in particolare per le chiavi usate per crittografare i dati inattivi. Poiché l'eliminazione di queste chiavi equivarrebbe a perdere i dati, se necessario, è possibile recuperare gli insiemi di credenziali e gli oggetti al loro interno eliminati. Esercitarsi a svolgere operazioni di ripristino degli insiemi di credenziali delle chiavi a intervalli regolari.

> [!NOTE]
> Se un utente dispone di autorizzazioni di collaboratore (controllo degli accessi in base al ruolo) per un piano di gestione dell'insieme di credenziali delle chiavi, può concedere a se stesso l'accesso al piano dati impostando i criteri di accesso per l'insieme di credenziali delle chiavi. È consigliabile controllare rigorosamente gli utenti che dispongono dei diritti di accesso di collaboratore all'insieme di credenziali delle chiavi per avere la sicurezza che solo le persone autorizzate possano accedere e gestire insiemi di credenziali delle chiavi, chiavi, segreti e certificati.
>
>

## <a name="manage-with-secure-workstations"></a>Gestire con workstation sicure

> [!NOTE]
> L'amministratore o il proprietario della sottoscrizione deve usare una workstation con accesso protetto o una workstation con accesso con privilegi.
>
>

Poiché la maggior parte degli attacchi prende di mira l'utente finale, l'endpoint diventa uno dei principali punti di attacco. Un utente malintenzionato che compromette l'endpoint può usare le credenziali dell'utente per accedere ai dati dell'organizzazione. Nella maggior parte dei casi, gli attacchi agli endpoint sfruttano il fatto che gli utenti finali sono amministratori delle workstation locali.

**Procedura consigliata**: usare una workstation di gestione sicura per proteggere gli account, le attività e i dati sensibili.   
**Dettagli**: usare una [workstation con accesso con privilegi](https://technet.microsoft.com/library/mt634654.aspx) per ridurre la superficie di attacco nelle workstation. Queste workstation di gestione sicure consentono di contenere alcuni di questi attacchi e contribuiscono a proteggere i dati.

**Procedura consigliata**: abilitare Endpoint Protection.   
**Dettagli**: applicare criteri di sicurezza in tutti i dispositivi che consentono di utilizzare i dati, indipendentemente dal fatto che i dati si trovino nel cloud o in locale.

## <a name="protect-data-at-rest"></a>Proteggere i dati inattivi

[La crittografia dei dati inattivi](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) è un passaggio obbligatorio per assicurare la privacy, la conformità e la sovranità dei dati.

**Procedura consigliata**: applicare la crittografia dischi per migliorare la protezione dei dati.   
**Dettagli**: usare [Crittografia dischi di Azure](azure-security-disk-encryption.md). Consente agli amministratori IT di crittografare i dischi delle macchine virtuali IaaS Windows e Linux. Crittografia dischi combina la funzionalità standard di settore BitLocker di Windows e la funzionalità dm-crypt di Linux per fornire la crittografia del volume per i dischi del sistema operativo e dei dati.

Archiviazione di Azure e Database SQL di Azure applicano la crittografia dei dati inattivi per impostazione predefinita e molti servizi offrono la crittografia come opzione. È possibile usare Azure Key Vault per mantenere il controllo delle chiavi che accedono e ai dati e li crittografano. Per altre informazioni, vedere [Supporto per il modello di crittografia dei provider di risorse di Azure](azure-security-encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Procedure consigliate**: usare la crittografia per ridurre i rischi correlati all'accesso non autorizzato ai dati.   
**Dettagli**: crittografare le unità prima di scrivere dati sensibili.

Le organizzazioni che non applicano la crittografia dei dati sono più esposte a problemi di riservatezza dei dati. Utenti non autorizzati, ad esempio, potrebbero rubare dati negli account compromessi o ottenere l'accesso non autorizzato ai dati codificati in ClearFormat. Le aziende devono anche dimostrare di operare in modo conforme e di implementare i controlli di sicurezza appropriati per aumentare la sicurezza dei dati e per rispettare normative di settore.

## <a name="protect-data-in-transit"></a>Proteggere i dati in transito

La protezione dei dati in transito deve essere una parte essenziale della strategia di protezione dati. Poiché i dati transitano in modo bidirezionale tra molte posizioni, in generale è consigliabile usare sempre i protocolli SSL/TLS per lo scambio di dati tra posizioni diverse. In alcuni casi è consigliabile isolare l'intero canale di comunicazione tra l'infrastruttura locale e cloud tramite una VPN.

Per lo spostamento dei dati tra l'infrastruttura locale e Azure, è opportuno considerare le misure di protezione appropriate, ad esempio HTTPS o VPN. Per inviare traffico crittografato tra una rete virtuale di Azure e una posizione locale tramite Internet, usare [Gateway VPN di Azure](https://docs.microsoft.com/azure/vpn-gateway/).

Di seguito sono indicate le procedure consigliate specifiche per l'uso di Gateway VPN di Azure, HTTPS e SSL/TLS.

**Procedura consigliata**: proteggere l'accesso da più workstation dislocate localmente in una rete virtuale di Azure.   
**Dettagli**: usare [VPN da sito a sito](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

**Procedura consigliata**: proteggere l'accesso da una singola workstation dislocata localmente a una rete virtuale di Azure.   
**Dettagli**: usare [VPN da punto a sito](../vpn-gateway/vpn-gateway-point-to-site-create.md).

**Procedura consigliata**: spostare i set di dati più grandi tramite un collegamento WAN ad alta velocità dedicato.   
**Dettagli**: usare [ExpressRoute](../expressroute/expressroute-introduction.md). Se si decide di usare ExpressRoute, è possibile anche crittografare i dati a livello di applicazione usando [SSL/TLS](https://support.microsoft.com/kb/257591) o altri protocolli per una maggiore protezione.

**Procedura consigliata**: interagire con Archiviazione di Azure tramite il portale di Azure.   
**Dettagli**: tutte le transazioni avvengono via HTTPS. È anche possibile usare [API REST di archiviazione](https://msdn.microsoft.com/library/azure/dd179355.aspx) tramite HTTPS per interagire con [archiviazione di Azure](https://azure.microsoft.com/services/storage/).

Le organizzazioni che non riescono a proteggere i dati in transito sono più vulnerabili agli [attacchi man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [eavesdropping](https://technet.microsoft.com/library/gg195641.aspx) e hijack della sessione. Questi attacchi possono essere il primo passo per ottenere l'accesso ai dati riservati.

## <a name="secure-email-documents-and-sensitive-data"></a>Proteggere la posta elettronica, i documenti e i dati sensibili

È opportuno controllare e proteggere i messaggi di posta elettronica, i documenti e i dati sensibili che si condividono con entità esterne all'azienda. [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/) è una soluzione basata su cloud che consente a un'organizzazione di classificare, etichettare e proteggere i propri documenti e messaggi di posta elettronica. Questa operazione può essere eseguita automaticamente dagli amministratori che definiscono le regole e le condizioni, manualmente dagli utenti o in un sistema misto in cui gli utenti ricevono delle raccomandazioni.

La classificazione è sempre identificabile, indipendentemente dalla posizione in cui vengono archiviati i dati o dalle persone con cui sono condivisi. Le etichette includono contrassegni visivi, ad esempio un'intestazione, un piè di pagina o una filigrana. I metadati vengono aggiunti ai file e alle intestazioni dei messaggio di posta elettronica sotto forma di testo non crittografato. Il testo non crittografato assicura che gli altri servizi, ad esempio le soluzioni per evitare la perdita di dati, possano identificare la classificazione e intraprendere l'azione appropriata.

La tecnologia di protezione usa Azure Rights Management (Azure RMS). Questa tecnologia si integra con altri servizi e applicazioni cloud Microsoft, ad esempio Office 365 e Azure Active Directory. Questa tecnologia di protezione usa criteri di crittografia, identità e autorizzazione. La protezione applicata tramite Azure RMS vale per i documenti e i messaggi di posta elettronica, indipendentemente dalla posizione, all'interno o all'esterno dell'organizzazione, delle reti, dei server di file e delle applicazioni.

Questa soluzione di protezione delle informazioni favorisce il controllo dei dati, anche quando sono condivisi con altri utenti. È anche possibile usare Azure RMS con le proprie applicazioni line-of-business e soluzioni di protezione delle informazioni di altri fornitori software, sia che queste applicazioni e soluzioni siano ospitate in locale o nel cloud.

È consigliabile:

- [Distribuire Azure Information Protection](https://docs.microsoft.com/azure/information-protection/deployment-roadmap) nell'organizzazione.
- Applicare etichette che rispecchino i requisiti aziendali. Ad esempio:  applicare un'etichetta denominata "strettamente confidenziale" a tutti i documenti e i messaggi di posta elettronica che contengono dati top secret che necessitano di essere classificati e protetti. Solo gli utenti autorizzati potranno quindi accedere a questi dati, con le eventuali restrizioni specificate dall'utente.
- Configurare la [registrazione e l'analisi dell'utilizzo del servizio Azure RMS](https://docs.microsoft.com/azure/information-protection/log-analyze-usage) in modo da monitorare come l'organizzazione usa il servizio di protezione.

Le organizzazioni che sono carenti a livello di [classificazione dei dati](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) e di protezione dei file potrebbero essere più soggette alla perdita di dati o a un loro utilizzo improprio. Con un'adeguata protezione dei file, è possibile analizzare i flussi di dati per ottenere informazioni dettagliate sulle attività aziendali, rilevare comportamenti a rischio e adottare misure correttive, tenere traccia dell'accesso ai documenti e così via.

## <a name="next-steps"></a>Passaggi successivi

Per altre procedure consigliate per la sicurezza da usare nella progettazione, la distribuzione e la gestione di soluzioni cloud tramite Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](security-best-practices-and-patterns.md).

Le risorse seguenti offrono altre informazioni più generali sulla sicurezza di Azure e sui servizi Microsoft correlati:
* [Blog del team di sicurezza di Azure](https://blogs.msdn.microsoft.com/azuresecurity/): per informazioni aggiornate sulla sicurezza in Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx): consente di segnalare le vulnerabilità della sicurezza di Microsoft, inclusi i problemi relativi ad Azure, tramite posta elettronica all'indirizzo secure@microsoft.com
