---
title: Piano per l'elaborazione dei pagamenti di Azure - Requisiti per i dati di titolari di carte
description: Requisito 3 di PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9736f7c8-c632-4b86-8b8a-6e4f45c1a7aa
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 356599cbe1e4e1948a5ec16d0d504835fa7dcd43
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="chd-requirements-for-pci-dss-compliant-environments"></a>Requisiti per i dati di titolari di carte per ambienti conformi a PCI DSS
## <a name="pci-dss-requirement-3"></a>Requisito 3 di PCI DSS

**Proteggere i dati di titolari di carte archiviati**

> [!NOTE]
> Questi requisiti sono definiti dal [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) nell'ambito di [PCI Data Security Standard (DSS) versione 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Per informazioni sulle procedure di test e indicazioni per ogni requisito, vedere lo standard PCI DSS.

I metodi di protezione, ad esempio crittografia, troncamento, mascheramento e hashing sono componenti cruciali per la protezione dei dati di titolari di carte. Se un intruso aggira altri controlli di sicurezza e ottiene l'accesso ai dati crittografati, senza le chiavi crittografiche appropriate i dati risulteranno illeggibili e inutilizzabili per tale persona. È opportuno prendere in considerazione anche altri metodi di protezione dei dati archiviati efficaci come potenziale opportunità di mitigazione dei rischi. Alcuni esempi di metodi per la mitigazione dei rischi sono evitare di archiviare i dati di titolari di carte se non assolutamente necessario, troncare i dati di titolari di carte se non occorrono numeri PAN completi ed evitare l'invio di PAN non protetti tramite tecnologie di messaggistica per gli utenti finali, come posta elettronica e messaggistica istantanea.
Fare riferimento al glossario dei termini, delle abbreviazioni e degli acronimi di PCI DSS e PA-DSS per le definizioni di "crittografia avanzata" e altri termini relativi a PCI DSS.

## <a name="pci-dss-requirement-31"></a>Requisito 3.1 di PCI DSS

**3.1** Ridurre al minimo l'archiviazione dei dati di titolari di carte implementando criteri di conservazione ed eliminazione dei dati, procedure e processi che tengano conto di quanto segue per l'archiviazione di tutti i dati di titolari di carte:
- Limitazione della quantità di archiviazione dei dati e dei periodi di conservazione a quanto obbligatorio per soddisfare requisiti legali, normativi e aziendali
- Requisiti specifici per la conservazione dei dati di titolari di carte
- Processi per l'eliminazione sicura dei dati quando non sono più necessari
- Processo trimestrale per l'identificazione e l'eliminazione sicura dei dati di titolari di carte archiviati una volta trascorso il periodo di conservazione definito.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure è responsabile di assicurarsi che i dati dei clienti designati per l'eliminazione vengono rimossi in modo sicuro tramite protocolli conformi a NIST 800-88 specificati nei criteri di eliminazione sicura. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore non elimina o distrugge dati di titolari di carte archiviati. Tuttavia, tutti i dati vengono crittografati e non vengono archiviati dati PAN (Primary Account Number).<br /><br />|



## <a name="pci-dss-requirement-32"></a>Requisito 3.2 di PCI DSS

**3.2** Non archiviare dati di autenticazione sensibili dopo l'autorizzazione (anche se crittografati). Se si ricevono dati di autenticazione sensibili, rendere irrecuperabili tutti i dati dopo il completamento del processo di autorizzazione. 
- Esiste una giustificazione aziendale, e 
- I dati vengono archiviati in modo sicuro.
I dati di autenticazione sensibili includono i dati con le caratteristiche citate nei requisiti seguenti da 3.2.1 a 3.2.3:


**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore non elimina o distrugge dati di titolari di carte archiviati. I dati di esempio vengono archiviati solo a scopo dimostrativo. Tuttavia, tutti i dati vengono crittografati e non vengono archiviati dati PAN (Primary Account Number).<br /><br />|



### <a name="pci-dss-requirement-321"></a>Requisito 3.2.1 di PCI DSS

**3.2.1** Non archiviare l'intero contenuto di qualsiasi traccia (dalla banda magnetica sul retro di una carta, dati equivalenti contenuti in un chip o altrove) dopo l'autorizzazione. Questi dati sono altrimenti denominati traccia completa, traccia, traccia 1, traccia 2 e dati della banda magnetica. 

> [!NOTE]
> Per le normali attività aziendali, potrebbe essere necessario conservare gli elementi dati seguenti dalla banda magnetica: 
> - Nome del titolare 
> - Numero della carta (PAN, Primary Account Number) 
> - Data di scadenza 
> - Codice di servizio 
>
> Per contenere i rischi, archiviare solo gli elementi dati necessari per l'attività.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore non archivia il contenuto completo dei dati di titolari di carte.|



### <a name="pci-dss-requirement-322"></a>Requisito 3.2.2 di PCI DSS

**3.2.2** Non archiviare il codice o il valore di verifica della carta (il numero di tre o quattro cifre stampato nella parte anteriore o posteriore di una carta di pagamento e usato per verificare le transazioni in assenza della carta) dopo l'autorizzazione.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore crittografa tutti i dati, inclusi gli esempi di CVV.|



### <a name="pci-dss-requirement-323"></a>Requisito 3.2.3 di PCI DSS

**3.2.3** Non archiviare il numero di identificazione personale (PIN) o il blocco PIN crittografato dopo l'autorizzazione.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore non archivia informazioni sui PIN.|



## <a name="pci-dss-requirement-33"></a>Requisito 3.3 di PCI DSS

**3.3** Mascherare il numero PAN in caso di visualizzazione (possono essere visualizzate al massimo le prime sei e le ultime quattro cifre), in modo che solo il personale con un'esigenza professionale legittima possa visualizzare il PAN completo. 

> [!NOTE]
> Questo requisito non sostituisce requisiti più restrittivi in essere per la visualizzazione dei dati di titolari di carte, ad esempio i requisiti di marchio della carta di pagamento o legali per le ricevute del punto vendita.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore maschera il numero PAN tramite crittografia dei dati trasparente, colonne sempre crittografate e mascheramento dei dati dinamico. Per altre informazioni, vedere [PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) (Linee guida per PCI - Database SQL di Azure).|



## <a name="pci-dss-requirement-34"></a>Requisito 3.4 di PCI DSS

**3.4** Rendere illeggibile il numero PAN in qualsiasi posizione di archiviazione (inclusi supporti digitali, supporti di backup e log) adottando uno degli approcci seguenti:
- Hash unidirezionali, basati su crittografia avanzata, (l'hashing deve essere applicato all'intero PAN)
- Troncamento (non è possibile usare l'hashing per sostituire il segmento troncato del PAN)
- Token e pad indicizzati (i pad devono essere archiviati in modo sicuro)
- Crittografia avanzata con i processi e le procedure associati per la gestione delle chiavi. 

> [!NOTE]
> Per un utente malintenzionato è relativamente facile ricostruire i dati PAN originali se ha accesso alla versione troncata e hash del PAN. Nel caso in cui le versioni troncata e hash dello stesso PAN siano presenti nell'ambiente di un'entità, andrebbero predisposti ulteriori controlli per verificare che non sia possibile correlare le versioni troncata e hash per ricostruire il PAN originale.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore crittografa tutti i dati relativi alle carte di credito e usa Azure Key Vault per la gestione delle chiavi, impedendo il recupero dei dati di titolari di carte.<br /><br />Per altre informazioni, vedere [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Linee guida per PCI - Crittografia).|



### <a name="pci-dss-requirement-341"></a>Requisito 3.4.1 di PCI DSS

**3.4.1** Se viene usata la crittografia del disco (invece della crittografia del database a livello di file o di colonna), l'accesso logico deve essere gestito separatamente e in modo indipendente dall'autenticazione del sistema operativo e dai meccanismi di controllo di accesso nativi, ad esempio senza usare database degli account utente locali o credenziali di accesso alla rete generali. Le chiavi di decrittografia non devono essere associate ad account utente. 

> [!NOTE]
> Questo requisito si applica in aggiunta a tutti gli altri requisiti per la crittografia e la gestione delle chiavi di PCI DSS.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore crittografa tutti i dati archiviati e isola il traffico per evitare l'elevazione dei privilegi per le funzioni DevOps.<br /><br />Dato che l'ambiente del servizio app è protetto e bloccato, è necessario un meccanismo che consenta il rilascio o la modifica di DevOps in caso di necessità, ad esempio la possibilità di monitorare un app Web tramite Kudu.<br /><br />È stata creata una macchina virtuale come jumpbox (bastion host) con le seguenti configurazioni:<br /><br /><ul><li>[Estensione antimalware](/azure/security/azure-security-antimalware)</li><li>[Estensione di monitoraggio OMS](/azure/virtual-machines/virtual-machines-windows-extensions-oms)</li><li>[Estensione Diagnostica per le macchine virtuali](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)</li><li>[Disco crittografato BitLocker](/azure/security/azure-security-disk-encryption)</li></ul>L'uso di Azure Key Vault è allineato ai requisiti di Azure per enti pubblici, PCI DSS e HIPAA.|



## <a name="pci-dss-requirement-35"></a>Requisito 3.5 di PCI DSS

**3.5** Documentare e implementare procedure per proteggere le chiavi usate per proteggere i dati di titolari di carte archiviati dalla divulgazione o da usi impropri. 

> [!NOTE]
> Questo requisito si applica alle chiavi usate per crittografare i dati di titolari di carte e anche le chiavi di crittografia delle chiavi usate per proteggere le chiavi di crittografia dei dati. Tali chiavi di crittografia delle chiavi devono avere almeno la stessa forza della chiave di crittografia dei dati.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Per i clienti che usano Key Vault:**<br /><br />Microsoft Azure garantisce che gli insiemi di credenziali delle chiavi dei clienti siano isolati logicamente tra loro e dal piano di gestione del servizio Key Vault. Il servizio Key Vault è progettato in modo che Microsoft non abbia accesso permanente all'insieme di credenziali delle chiavi del cliente. <br /><br />Le chiavi vengono protette da Microsoft Azure con algoritmi, lunghezze delle chiavi e moduli di protezione hardware standard del settore.<br /><br />Una chiave archiviata in Microsoft Azure Key Vault può essere usata per proteggere un'altra chiave. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore fornisce la documentazione per illustrare e facilitare la distribuzione di una soluzione con chiave protetta per proteggere i dati di titolari di carte della demo.|



### <a name="pci-dss-requirement-351"></a>Requisito 3.5.1 di PCI DSS

**3.5.1** *Requisito aggiuntivo solo per i provider di servizi:* creare e mantenere aggiornata una descrizione documentata dell'architettura crittografica che include:
- Dettagli di tutti gli algoritmi, i protocolli e le chiavi usati per la protezione dei dati di titolari di carte, incluse forza della chiave e data di scadenza
- Descrizione dell'utilizzo per ogni chiave
- Inventario di tutti i moduli di protezione hardware di altri dispositivi crittografici sicuri usati per la gestione delle chiavi 

> [!NOTE]
> Questo requisito è una procedura consigliata fino al 31 gennaio 2018 e in seguito diventerà un requisito.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Per i clienti che usano Key Vault:**<br /><br />Microsoft Azure garantisce che gli insiemi di credenziali delle chiavi dei clienti siano isolati logicamente tra loro e dal piano di gestione del servizio Key Vault. Il servizio Key Vault è progettato in modo che Microsoft non abbia accesso permanente all'insieme di credenziali delle chiavi del cliente. <br /><br />Le chiavi vengono protette da Microsoft Azure con algoritmi, lunghezze delle chiavi e moduli di protezione hardware standard del settore.<br /><br />Una chiave archiviata in Microsoft Azure Key Vault può essere usata per proteggere un'altra chiave. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore fornisce la documentazione per illustrare e facilitare la distribuzione di una soluzione con chiave protetta per proteggere i dati di titolari di carte della demo.|



### <a name="pci-dss-requirement-352"></a>Requisito 3.5.2 di PCI DSS

**3.5.2** Limitare l'accesso alle chiavi crittografiche al minor numero di custodi necessari.


**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Per i clienti che usano Key Vault:**<br /><br />Key Vault supporta criteri di accesso granulari, che consentono a un titolare di Key Vault di concedere l'accesso a funzionalità specifiche per eseguire operazioni specifiche su entità specifiche. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | La gestione delle chiavi di Contoso Webstore è isolata limitandola a un solo account utente (admin ##@contosowebstore.com).|



### <a name="pci-dss-requirement-353"></a>Requisito 3.5.3 di PCI DSS

**3.5.3** Archiviare i segreti e le chiavi private usati per crittografare/decrittografare sempre i dati di titolari di carte in una o più delle forme seguenti:
- Crittografati con una chiave di crittografia della chiave che abbia almeno la stessa forza della chiave di crittografia dei dati e sia archiviata separatamente dalla chiave di crittografia dei dati
- All'interno di un dispositivo crittografico sicuro, ad esempio, un modulo di protezione hardware (host) o un dispositivo di punto di interazione approvato PTS
- Almeno come due componenti o condivisioni di chiavi a lunghezza integrale, in conformità a un metodo accettato nel settore 

> [!NOTE]
> Non è necessario archiviare le chiavi pubbliche in una di queste forme.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Per i clienti che usano Key Vault:**<br /><br />Le chiavi vengono archiviate negli insiemi di credenziali delle chiavi specifici identificati dal cliente.<br /><br />Key Vault è accessibile contemporaneamente e a livello globale da più applicazioni e ciò riduce la necessità di copiare una chiave e archiviarla in più posizioni. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa Azure Key Vault per tutte le attività di gestione delle chiavi. Per altre informazioni, vedere [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Linee guida per PCI - Crittografia).|



### <a name="pci-dss-requirement-354"></a>Requisito 3.5.4 di PCI DSS

**3.5.4** Archiviare le chiavi di crittografia nel minor numero possibile di posizioni.


**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Per i clienti che usano Key Vault:**<br /><br />Le chiavi vengono archiviate negli insiemi di credenziali delle chiavi specifici identificati dal cliente. <br /><br />Key Vault è accessibile contemporaneamente e a livello globale da più applicazioni e ciò riduce la necessità di copiare una chiave e archiviarla in più posizioni. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa Azure Key Vault per la gestione di tutte le chiavi. Per altre informazioni, vedere [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Linee guida per PCI - Crittografia).|



## <a name="pci-dss-requirement-36"></a>Requisito 3.6 di PCI DSS

**3.6** Documentare e implementare completamente tutti i processi e le procedure di gestione delle chiavi crittografiche usate per la crittografia dei dati di titolari di carte, incluso quanto segue. 

> [!NOTE]
> Sono disponibili numerosi standard di settore per la gestione delle chiavi in diverse risorse, tra cui il sito del NIST all'indirizzo http://csrc.nist.gov.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa Azure Key Vault per tutte le attività di gestione delle chiavi. Per altre informazioni, vedere [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Linee guida per PCI - Crittografia).|



### <a name="pci-dss-requirement-361"></a>Requisito 3.6.1 di PCI DSS

**3.6.1** Generazione di chiavi crittografiche avanzate

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Per i clienti che usano Key Vault:** <br /><br />Durante la generazione di chiavi in Key Vault, Azure è responsabile della generazione di chiavi in base alle specifiche del cliente. Le chiavi vengono generate con un modulo di protezione hardware. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa Azure Key Vault per tutte le attività di gestione delle chiavi. Per altre informazioni, vedere [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Linee guida per PCI - Crittografia).|



### <a name="pci-dss-requirement-362"></a>Requisito 3.6.2 di PCI DSS

**3.6.2** Distribuzione sicura delle chiavi crittografiche

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Per i clienti che usano Key Vault:**<br /><br />Lo strumento BYOK (Bring Your Own Key) incapsula la chiave del cliente e seleziona un insieme di credenziali di sicurezza specifiche associato a una sottoscrizione di Azure specifica. La chiave può essere importata solo nell'insieme di credenziali delle chiavi definito, nell'area specificata. Questo processo usa le procedure di crittografia specificate dal produttore dell'hardware. I clienti ricevono una notifica del corretto completamento del trasferimento. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa Azure Key Vault per tutte le attività di gestione delle chiavi. Per altre informazioni, vedere [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Linee guida per PCI - Crittografia).|



### <a name="pci-dss-requirement-363"></a>Requisito 3.6.3 di PCI DSS

**3.6.3** Archiviazione sicura delle chiavi crittografiche

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Per i clienti che usano Key Vault:**<br /><br />Le chiavi vengono archiviate in moduli di protezione hardware e protette tramite la sicurezza crittografica del produttore dell'hardware. I metadati nelle chiavi vengono archiviati in Archiviazione di Azure in uno stato crittografato univoco per ogni insieme di credenziali delle chiavi. <br /><br /> |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa Azure Key Vault per tutte le attività di gestione delle chiavi. Per altre informazioni, vedere [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Linee guida per PCI - Crittografia).|



### <a name="pci-dss-requirement-364"></a>Requisito 3.6.4 di PCI DSS

**3.6.4** Modifiche delle chiavi crittografiche per le chiavi giunte al termine del loro periodo di validità (ad esempio, una volta trascorso un periodo di tempo definito e/o dopo la produzione da parte di una determinata chiave di una quantità definita di testo cifrato), come specificato dal fornitore dell'applicazione associato o dal proprietario delle chiavi e in base alle procedure consigliate e alle linee guida del settore (ad esempio, NIST Special Publication 800-57).

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Per i clienti che usano Key Vault:**<br /><br />Key Vault supporta funzionalità per aggiornare o distribuire le chiavi, come definito dal cliente. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa Azure Key Vault per tutte le attività di gestione delle chiavi. Per altre informazioni, vedere [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Linee guida per PCI - Crittografia).|



### <a name="pci-dss-requirement-365"></a>Requisito 3.6.5 di PCI DSS

**3.6.5** Ritiro o sostituzione delle chiavi (ad esempio, archiviazione, distruzione e/o revoca) come ritenuto necessario in caso di indebolimento dell’integrità della chiave (ad esempio, dimissioni di un dipendente a conoscenza del componente in chiaro della chiave) o chiavi per le quali esista il sospetto che siano state compromesse. 

> [!NOTE]
> Se le chiavi crittografiche ritirate o sostituite devono essere conservate, sarà necessario archiviarle in modo sicuro (ad esempio usando una chiave di crittografia della chiave). Le chiavi crittografiche archiviate devono essere usate solo per scopi di decrittografia o verifica.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Per i clienti che usano Key Vault:**<br /><br />Key Vault supporta funzionalità per ritirare o sostituire le chiavi, come definito dal cliente. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa Azure Key Vault per tutte le attività di gestione delle chiavi. Per altre informazioni, vedere [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Linee guida per PCI - Crittografia).|



### <a name="pci-dss-requirement-366"></a>Requisito 3.6.6 di PCI DSS

**3.6.6** Se vengono usate operazioni manuali di gestione delle chiavi crittografiche con testo in chiaro, tali operazioni devono essere gestite in base ai principi di split knowledge e controllo duale. 

> [!NOTE]
> Esempi di operazioni manuali di gestione delle chiavi includono, a titolo esemplificativo, la generazione, la trasmissione, il caricamento, l'archiviazione e la distruzione delle chiavi.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa Azure Key Vault per tutte le attività di gestione delle chiavi. Per altre informazioni, vedere [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Linee guida per PCI - Crittografia).|



### <a name="pci-dss-requirement-367"></a>Requisito 3.6.7 di PCI DSS

**3.6.7** Prevenzione dei tentativi di sostituzione non autorizzata delle chiavi crittografiche.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Per i clienti che usano Key Vault:**<br /><br />Gli insiemi di credenziali delle chiavi sono separati dal punto di vista logico e non supportano l'autorizzazione tra directory. Questo meccanismo evita pertanto sostituzioni non autorizzate. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa Azure Key Vault per tutte le attività di gestione delle chiavi. Per altre informazioni, vedere [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Linee guida per PCI - Crittografia).|



### <a name="pci-dss-requirement-368"></a>Requisito 3.6.8 di PCI DSS

**3.6.8 tramite** Obbligo per i custodi delle chiavi crittografiche di riconoscere in modo formale che accettano e confermano di conoscere le proprie responsabilità.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | La gestione delle chiavi di Contoso Webstore è isolata limitandola a un solo account utente (admin ##@contosowebstore.com).|



## <a name="pci-dss-requirement-37"></a>Requisito 3.7 di PCI DSS

**3.7** Assicurarsi che i criteri di sicurezza e le procedure operative per la protezione dei dati di titolari di carte archiviati siano documentati, applicati e noti a tutte le parti interessate.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa Azure Key Vault per tutte le attività di gestione delle chiavi. Per altre informazioni, vedere [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Linee guida per PCI - Crittografia).|




