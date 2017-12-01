---
title: "Piano per l'elaborazione dei pagamenti di Azure - Requisiti di identità"
description: Requisito 8 di PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 1a398601-8c48-4f8e-b3d4-eba94edad61c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: f77cc3c9926b5316913c70e5f4412383e55c5193
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="identity-requirements-for-pci-dss-compliant-environments"></a>Requisiti di identità per ambienti conformi a PCI DSS 
## <a name="pci-dss-requirement-8"></a>Requisito 8 di PCI DSS

**Identificare e autenticare l'accesso ai componenti di sistema**

> [!NOTE]
> Questi requisiti sono definiti dal [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) nell'ambito di [PCI Data Security Standard (DSS) versione 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Per informazioni sulle procedure di test e indicazioni per ogni requisito, vedere lo standard PCI DSS.

Assegnare un'identificazione univoca (ID) a tutti gli utenti che eseguono l'accesso, per garantire che ogni utente sia responsabile in modo univoco per le proprie azioni. In questo modo, le azioni effettuate su dati e sistemi critici vengono eseguite da utenti e processi noti e autorizzati e possono essere tracciate come tali.
L'efficacia di una password dipende soprattutto dalla progettazione e dall'implementazione del sistema di autenticazione, in particolare dalla frequenza dei tentativi di inserimento di password da parte di utenti malintenzionati e dai metodi di sicurezza usati per proteggere le password degli utenti nel punto di ingresso, durante la trasmissione e l'archiviazione.

> [!NOTE]
> Questi requisiti sono applicabili a tutti gli account, inclusi gli account dei punti vendita, con funzionalità amministrative e tutti gli account usati per visualizzare o accedere a dati dei titolari di carte o per accedere a sistemi con dati dei titolari di carte. Sono inclusi gli account usati dai fornitori e da terze parti (ad esempio, per assistenza o manutenzione). Questi requisiti non si applicano agli account usati dagli utenti (ad esempio, titolari di carte). I requisiti 8.1.1, 8.2, 8.5, quelli da 8.2.3 a 8.2.5 e da 8.1.6 a 8.1.8 tuttavia non sono validi per gli account utente in un'applicazione di pagamento dei punti vendita, che hanno accesso a un solo numero di carta alla volta per facilitare una singola transazione (ad esempio, gli account cassiere).
 
## <a name="pci-dss-requirement-81"></a>Requisito 8.1 di PCI DSS

**8.1** Definire e implementare criteri e procedure per garantire una corretta gestione dell'identificazione degli utenti non consumatori e degli amministratori in tutti i componenti di sistema nel modo seguente.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore fornisce un caso d'uso e una descrizione per l'utilizzo corretto degli amministratori per la distribuzione di esempio.|



### <a name="pci-dss-requirement-811"></a>Requisito 8.1.1 di PCI DSS

**8.1.1** Assegnare a tutti gli utenti un ID univoco prima di consentire l'accesso ai componenti di sistema o ai dati dei titolari di carte.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore implementa Azure Active Directory e il controllo degli accessi in base al ruolo di Azure Active Directory per garantire che tutti gli utenti abbiano un ID univoco. Per altre informazioni, vedere [PCI Guidance - Identity Management](payment-processing-blueprint.md#identity-management) (Linee guida per PCI - Gestione delle identità).<br /><br />|



### <a name="pci-dss-requirement-812"></a>Requisito 8.1.2 di PCI DSS

**8.1.2** Controllare le operazioni di aggiunta, eliminazione e modifica di ID utente, credenziali e altri oggetti identificatore.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore implementa Azure Active Directory e il controllo degli accessi in base al ruolo di Azure Active Directory per garantire che tutti gli utenti abbiano un ID univoco. Per altre informazioni, vedere [PCI Guidance - Identity Management](payment-processing-blueprint.md#identity-management) (Linee guida per PCI - Gestione delle identità).<br /><br />|



### <a name="pci-dss-requirement-813"></a>Requisito 8.1.3 di PCI DSS

**8.1.3** Revocare immediatamente l'accesso per gli utenti terminati.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore utilizza Azure Active Directory per la gestione utenti. La revoca degli utenti può essere eseguita in Active Directory.|



### <a name="pci-dss-requirement-814"></a>Requisito 8.1.4 di PCI DSS

**8.1.4** Rimuovere o disabilitare gli account utente inattivi entro 90 giorni.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa Azure Active Directory per la gestione utenti. È possibile impostare l'opzione `-enableADDomainPasswordPolicy` per assicurarsi che le password scadano entro 90 giorni.|



### <a name="pci-dss-requirement-815"></a>Requisito 8.1.5 di PCI DSS

**8.1.5** Gestire gli ID usati da terze parti per accedere, supportare o gestire i componenti di sistema tramite accesso remoto come segue:
- Abilitati solo durante il periodo di tempo necessario e disabilitati se non in uso.
- Monitorati se in uso.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure ha adottato criteri di sicurezza aziendali applicabili, inclusi criteri di sicurezza delle informazioni. I criteri sono stati approvati, pubblicati e comunicati a Microsoft Azure. In base ai criteri di sicurezza delle informazioni l'accesso agli asset di Microsoft Azure deve essere concesso in base a una motivazione aziendale, con l'autorizzazione del proprietario dell'asset e limitato secondo i principi "solo quando necessario" e "privilegi minimi". I criteri soddisfano anche i requisiti per il ciclo di vita della gestione degli accessi, inclusi provisioning degli accessi, autenticazione, autorizzazione di accesso, rimozione dei diritti di accesso e verifiche di accesso periodiche. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | La demo Contoso Webstore ha implementato Azure Active Directory e il controllo degli accessi in base al ruolo di Azure Active Directory per gestire l'accesso degli utenti all'installazione. Per altre informazioni, vedere [PCI Guidance - Identity Management](payment-processing-blueprint.md#identity-management) (Linee guida per PCI - Gestione delle identità).<br /><br />|



### <a name="pci-dss-requirement-816"></a>Requisito 8.1.6 di PCI DSS

**8.1.6** Limitare i tentativi di accesso ripetuti bloccando l'ID utente dopo un massimo di sei tentativi.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore ha implementato una separazione netta dei compiti per tutti gli utenti della demo. Per altre informazioni, vedere "Azure Active Directory Identity Protection" in [PCI Guidance - Identity Management](payment-processing-blueprint.md#identity-management) (Linee guida per PCI - Gestione delle identità).|



### <a name="pci-dss-requirement-817"></a>Requisito 8.1.7 di PCI DSS

**8.1.7** Impostare la durata del blocco su un minimo di 30 minuti o finché un amministratore non abilita l'ID utente.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | I clienti sono responsabili della creazione, dell'applicazione e del monitoraggio di criteri password conformi ai requisiti di PCI DSS.|



### <a name="pci-dss-requirement-818"></a>Requisito 8.1.8 di PCI DSS

**8.1.8** Se una sessione è stata inattiva per più di 15 minuti, è necessario che l'utente effettui di nuovo l'autenticazione per riattivare il terminale o la sessione.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | I clienti sono responsabili della creazione, dell'applicazione e del monitoraggio di criteri password conformi ai requisiti di PCI DSS.|



## <a name="pci-dss-requirement-82"></a>Requisito 8.2 di PCI DSS

**8.2** Oltre ad assegnare un ID univoco, garantire la corretta gestione dell'autenticazione degli utenti non clienti e amministratori in tutti i componenti di sistema adottando almeno uno dei metodi seguenti per autenticare tutti gli utenti:
- Qualcosa che l'utente conosce, ad esempio una password o una passphrase
- Qualcosa in possesso dell'utente, ad esempio un dispositivo token o una smart card
- Qualcosa che l'utente è, ad esempio un elemento biometrico

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | L'implementazione di Contoso Webstore per l'autenticazione a più fattori è stata disabilitata per semplificare l'uso della demo. L'autenticazione a più fattori può essere implementata usando [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/).|



### <a name="pci-dss-requirement-821"></a>Requisito 8.2.1 di PCI DSS

**8.2.1** Usando la crittografia avanzata, rendere illeggibili tutte le credenziali di autenticazione (ad esempio, password/frasi) durante la trasmissione e l'archiviazione in tutti i componenti di sistema.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure ha stabilito procedure di gestione delle chiavi per tutto il ciclo di vita (ad esempio, generazione, distribuzione, revoca). Microsoft Azure usa l'infrastruttura PKI aziendale di Microsoft. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore applica le password complesse, documentate nella guida alla distribuzione. Per altre informazioni, vedere [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Linee guida per PCI - Crittografia).<br /><br />|



### <a name="pci-dss-requirement-822"></a>Requisito 8.2.2 di PCI DSS

**8.2.2** Verificare l'identità dell'utente prima di modificare le credenziali di autenticazione, ad esempio eseguendo reimpostazioni della password, effettuando il provisioning di nuovi token o generando nuove chiavi.


**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure ha stabilito procedure di gestione delle chiavi per tutto il ciclo di vita (ad esempio, generazione, distribuzione, revoca). Microsoft Azure usa l'infrastruttura PKI aziendale di Microsoft. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore applica le password complesse, documentate nella guida alla distribuzione. Per altre informazioni, vedere [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Linee guida per PCI - Crittografia).|



### <a name="pci-dss-requirement-823"></a>Requisito 8.2.3 di PCI DSS

**8.2.3** Le password/passphrase devono soddisfare quanto segue:
- Richiedere una lunghezza minima di almeno sette caratteri.
- Contenere caratteri sia alfabetici che numerici.
In alternativa, le password/passphrase devono avere complessità e attendibilità pari almeno ai parametri specificati sopra.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore applica le password complesse, documentate nella guida alla distribuzione.|



### <a name="pci-dss-requirement-824"></a>Requisito 8.2.4 di PCI DSS

**8.2.4** Modificare le password/passphrase dell'utente almeno una volta ogni 90 giorni.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa Azure Active Directory per la gestione utenti. È possibile impostare l'opzione `-enableADDomainPasswordPolicy` per assicurarsi che le password scadano almeno una volta ogni 90 giorni.|



### <a name="pci-dss-requirement-825"></a>Requisito 8.2.5 di PCI DSS

**8.2.5** Non consentire l'invio di una nuova password/passphrase uguale a una delle ultime quattro password/passphrase usate.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore applica le password complesse, documentate nella guida alla distribuzione. Per altre informazioni, vedere [PCI Guidance - Identity Management](payment-processing-blueprint.md#identity-management) (Linee guida per PCI - Gestione delle identità).<br /><br />|



### <a name="pci-dss-requirement-826"></a>Requisito 8.2.6 PCI DSS

**8.2.6** Impostare le password/passphrase per il primo accesso e il ripristino su un valore univoco per ogni utente e modificarlo immediatamente dopo il primo uso.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore applica le password complesse, documentate nella guida alla distribuzione. Per altre informazioni, vedere [PCI Guidance - Identity Management](payment-processing-blueprint.md#identity-management) (Linee guida per PCI - Gestione delle identità).<br /><br />|



## <a name="pci-dss-requirement-83"></a>Requisito 8.3 di PCI DSS

**8.3** Proteggere ogni singolo accesso amministrativo non da console e ogni accesso remoto all'ambiente dei dati dei titolari di carte usando l'autenticazione a più fattori.

> [!NOTE]
> L'autenticazione a più fattori richiede l'uso di almeno due dei tre metodi di autenticazione. Per le descrizioni dei metodi di autenticazione, vedere il Requisito 8.2. L'uso di un fattore per due volte (ad esempio, l'uso di due password distinte) non viene considerato un'autenticazione a più fattori.


**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Gli amministratori di Azure devono usare l'autenticazione a più fattori per accedere ai sistemi e ai server di Azure quando eseguono la manutenzione e l'amministrazione. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore crea tre account durante la distribuzione: admin, sqladmin ed edna (l'utente predefinito connesso all'app Web durante l'esecuzione demo). L'autenticazione a più fattori non è implementata per la demo.|



### <a name="pci-dss-requirement-831"></a>Requisito 8.3.1 di PCI DSS

**8.3.1** Incorporare l'autenticazione a più fattori per ogni accesso non da console all'ambiente dei dati dei titolari di carte per il personale con accesso amministrativo.

> [!NOTE]
> Questo requisito è una procedura consigliata fino al 31 gennaio 2018 e in seguito diventerà un requisito.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Gli amministratori di Azure devono usare l'autenticazione a più fattori per accedere ai sistemi e ai server di Azure quando eseguono la manutenzione e l'amministrazione. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore crea tre account durante la distribuzione: admin, sqladmin ed edna (l'utente predefinito connesso all'app Web durante l'esecuzione demo). L'autenticazione a più fattori non è implementata per la demo.|



### <a name="pci-dss-requirement-832"></a>Requisito 8.3.2 di PCI DSS

**8.3.2** Incorporare l'autenticazione a più fattori per ogni accesso remoto alla rete (sia utente che amministratore e incluso l'accesso di terze parti per supporto o manutenzione) originato al di fuori della rete dell'entità.


**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Gli amministratori di Azure devono usare l'autenticazione a più fattori per accedere ai sistemi e ai server di Azure quando eseguono la manutenzione e l'amministrazione. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore crea tre account durante la distribuzione: admin, sqladmin ed edna (l'utente predefinito connesso all'app Web durante l'esecuzione demo). L'autenticazione a più fattori non è implementata per la demo.|



## <a name="pci-dss-requirement-84"></a>Requisito 8.4 di PCI DSS

**8.4** Documentare e comunicare le procedure e i criteri di autenticazione a tutti gli utenti, incluse:
- Indicazioni sulla selezione di credenziali di autenticazione complesse
- Indicazioni su come gli utenti devono proteggere le proprie credenziali di autenticazione
- Istruzioni per non riutilizzare le password utilizzate precedentemente
- Istruzioni per modificare le password in caso di sospetta compromissione delle password

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | I clienti hanno la responsabilità di seguire le indicazioni e di documentare e comunicare a tutti gli utenti le procedure e i criteri di autenticazione.|



## <a name="pci-dss-requirement-85"></a>Requisito 8.5 di PCI DSS

**8.5** Non usare ID e password di gruppo, condivisi o generici né altri metodi di autenticazione, come segue:
- Gli ID utente generici sono disabilitati o rimossi.
- Non esistono ID utente condivisi per le attività di amministrazione del sistema e altre funzioni critiche.
- Gli ID utente condivisi e generici non vengono usati per gestire i componenti di sistema.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore crea tre account durante la distribuzione: admin, sqladmin ed edna (l'utente predefinito connesso all'app Web durante l'esecuzione demo). L'autenticazione a più fattori non è implementata per la demo.|



### <a name="pci-dss-requirement-851"></a>Requisito 8.5.1 di PCI DSS

**8.5.1** **Requisito aggiuntivo solo per provider di servizi:** i provider di servizi con accesso remoto alle sedi dei clienti (ad esempio, per il supporto di server o sistemi POS) devono usare credenziali di autenticazione univoche (quali password/passphrase) per ogni cliente. 

> [!NOTE]
> Questo requisito non è valido per i provider di hosting condiviso che accedono al proprio ambiente di hosting, in cui sono ospitati più ambienti dei clienti.

**Responsabilità:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile per i clienti di Microsoft Azure. |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Non applicabile per i clienti di Microsoft Azure.|



## <a name="pci-dss-requirement-86"></a>Requisito 8.6 di PCI DSS

**8.6** Se vengono usati altri meccanismi di autenticazione (ad esempio, token di sicurezza fisici o logici, smart card, certificati e così via), l'uso di questi meccanismi deve essere assegnato come segue:
- I meccanismi di autenticazione devono essere assegnati a un singolo account e non vanno condivisi tra più account.
- Devono essere adottati controlli fisici e/o logici per assicurare che solo un determinato account possa usare tale meccanismo di accesso.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore crea tre account durante la distribuzione: admin, sqladmin ed edna (l'utente predefinito connesso all'app Web durante l'esecuzione demo). L'autenticazione a più fattori non è implementata per la demo. Ogni accesso viene gestito tramite [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), che consente di proteggere le chiavi crittografiche e i segreti usati da servizi e applicazioni cloud. |



## <a name="pci-dss-requirement-87"></a>Requisito 8.7 di PCI DSS

**8.7** L'accesso ai database contenenti dati dei titolari di carte (incluso l'accesso da parte di applicazioni, amministratori e tutti gli altri utenti) è limitato come segue:
- Tutti gli accessi, le query e le azioni dell'utente nei database vengono eseguiti tramite metodi programmatici.
- Solo gli amministratori del database hanno la possibilità di accedere o eseguire query direttamente sui database.
- Gli ID applicazione per le applicazioni di database possono essere usati esclusivamente dalle applicazioni e non da utenti singoli o altri processi non relativi alle applicazioni.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore protegge tutti i dati dei titolari di carte con Azure Key Vault. La crittografia dei record è illustrata nella documentazione della distribuzione. Per altre informazioni, vedere [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Linee guida per PCI - Crittografia).<br /><br />|



## <a name="pci-dss-requirement-88"></a>Requisito 8.8 di PCI DSS

**8.8** Assicurarsi che i criteri di sicurezza e le procedure operative per l'identificazione e l'autenticazione siano documentati, applicati e noti a tutte le parti interessate.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | I clienti hanno la responsabilità di assicurarsi che i criteri di sicurezza e le procedure operative per l'identificazione e l'autenticazione siano documentati, applicati e noti a tutte le parti interessate.|




