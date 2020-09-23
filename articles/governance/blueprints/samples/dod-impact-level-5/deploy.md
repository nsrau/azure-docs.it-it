---
title: Esempio di progetto DoD Impact Level 5
description: Procedura di distribuzione per l'esempio di progetto DoD Impact Level 5, inclusi i dettagli dei parametri degli artefatti del progetto.
ms.date: 09/17/2020
ms.topic: sample
ms.openlocfilehash: deb6ffa0f886cc6a4a9bea10a38d7cd82e7df8f1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90978224"
---
# <a name="deploy-the-dod-impact-level-5-blueprint-sample"></a>Distribuire l'esempio di progetto DoD Impact Level 5

Per distribuire l'esempio di progetto Azure Blueprints Department of Defense Impact Level 5 (DoD IL5), seguire questa procedura:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare l'esempio di progetto **DoD Impact Level 5** in _Altri esempi_ e selezionare **Usa questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia dell'esempio di progetto DoD Impact Level 5.
   - **Posizione della definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti che costituiscono l'esempio di progetto. Molti artefatti hanno parametri che verranno definiti in seguito. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

## <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata in base all'ambiente e alle esigenze, ma le modifiche potrebbero renderla non allineata ai controlli DoD Impact Level 5.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra specificare un valore di **Versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. In **Modifica le note** specificare ad esempio "Prima versione pubblicata dall'esempio di progetto DoD Impact Level 5". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

## <a name="assign-the-sample-copy"></a>Assegnare la copia dell'esempio

Dopo che la copia dell'esempio di progetto è stata **pubblicata** correttamente, è possibile assegnarla a una sottoscrizione all'interno del gruppo di gestione in cui è stata salvata. In questo passaggio vengono specificati i parametri che rendono univoca ogni distribuzione della copia dell'esempio di progetto.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Assegna progetto** nella parte superiore della pagina della definizione di progetto.

1. Specificare i valori dei parametri per l'assegnazione del progetto:

   - Nozioni di base

     - **Sottoscrizioni**: selezionare una o più sottoscrizioni presenti nel gruppo di gestione in cui è stata salvata la copia dell'esempio di progetto. Se si selezionano più sottoscrizioni, viene creata un'assegnazione per ognuna usando i parametri immessi.
     - **Nome dell'assegnazione**: il nome viene prepopolato in base al nome del progetto.
       Cambiarlo, se necessario, o lasciarlo inalterato.
     - **Località**: selezionare un'area in cui creare l'identità gestita. Azure Blueprint usa questa identità gestita per distribuire tutti gli elementi nel progetto assegnato. Per altre informazioni, vedere [Managed identities for Azure resources](../../../../active-directory/managed-identities-azure-resources/overview.md) (Identità gestite per risorse di Azure).
     - **Versione della definizione di progetto**: Scegliere una versione **pubblicata** della copia dell'esempio di progetto.

   - Blocca assegnazione

     Selezionare l'impostazione di blocco del progetto per l'ambiente. Per altre informazioni, vedere [Blueprints resource locking](../../concepts/resource-locking.md) (Blocco delle risorse del progetto).

   - Identità gestita

     Lasciare invariata l'opzione predefinita _Identità gestita assegnata dal sistema_.

   - Parametri dell'elemento

     I parametri definiti in questa sezione si applicano all'artefatto in cui sono definiti. Si tratta di [parametri dinamici](../../concepts/parameters.md#dynamic-parameters), in quanto vengono definiti durante l'assegnazione del progetto. Per un elenco completo di parametri di artefatti e relative descrizioni, vedere la [tabella di parametri degli artefatti](#artifact-parameters-table).

1. Dopo avere immesso tutti i parametri, selezionare **Assegna** in fondo alla pagina. Viene creata l'assegnazione del progetto e inizia la distribuzione degli artefatti. La distribuzione richiede circa un'ora. Per controllare lo stato della distribuzione, aprire l'assegnazione del progetto.

> [!WARNING]
> Il servizio Azure Blueprints e gli esempi di progetti predefiniti sono **gratuiti**. Le risorse di Azure hanno un [prezzo in base al prodotto](https://azure.microsoft.com/pricing/). Usare la [calcolatrice dei prezzi](https://azure.microsoft.com/pricing/calculator/) per stimare il costo dell'esecuzione delle risorse distribuite da questo esempio di progetto.

## <a name="artifact-parameters-table"></a>Tabella di parametri degli artefatti

La tabella seguente contiene un elenco dei parametri degli artefatti del progetto:

|Nome dell'artefatto|Tipo di artefatto|Nome parametro|Descrizione|
|-|-|-|-|
|DoD Impact Level 5|Assegnazione dei criteri|Elenco di utenti che devono essere inclusi nel gruppo di Administrators della macchina virtuale Windows|Elenco delimitato da punto e virgola con gli utenti da includere nel gruppo locale Administrators, ad esempio: Administrator; utente1; utente2|
|DoD Impact Level 5|Assegnazione dei criteri|Elenco di utenti esclusi dal gruppo di Administrators della macchina virtuale Windows|Elenco delimitato da punto e virgola con gli utenti da escludere dal gruppo locale Administrators, ad esempio: Administrator; utente1; utente2|
|DoD Impact Level 5|Assegnazione dei criteri|Elenco dei tipi di risorsa per cui è necessario abilitare i log di diagnostica||
|DoD Impact Level 5|Assegnazione dei criteri|ID dell'area di lavoro Log Analytics per la segnalazione degli agenti di macchine virtuali|ID (GUID) dell'area di lavoro Log Analytics a cui gli agenti delle macchine virtuali devono inviare le segnalazioni|
|DoD Impact Level 5|Assegnazione dei criteri|Elenco di aree in cui deve essere abilitato Network Watcher|Per visualizzare un elenco completo delle aree, usare Get-AzLocation|
|DoD Impact Level 5|Assegnazione dei criteri|Versione minima di TLS per i server Web Windows|Versione minima del protocollo TLS che deve essere abilitata nei server Web Windows|
|DoD Impact Level 5|Assegnazione dei criteri|Versione di PHP più recente|Versione di PHP più recente supportata per Servizi app|
|DoD Impact Level 5|Assegnazione dei criteri|Versione di Java più recente|Versione di Java più recente supportata per Servizi app|
|DoD Impact Level 5|Assegnazione dei criteri|Versione di Python per Windows più recente|Versione di Python più recente supportata per Servizi app|
|DoD Impact Level 5|Assegnazione dei criteri|Versione di Python per Linux più recente|Versione di Python più recente supportata per Servizi app|
|DoD Impact Level 5|Assegnazione dei criteri|Facoltativo: elenco delle immagini delle macchine virtuali Windows che supportano l'aggiunta dell'agente di Log Analytics all'ambito di controllo|Elenco delimitato da punto e virgola con le immagini, ad esempio: /subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|DoD Impact Level 5|Assegnazione dei criteri|Facoltativo: elenco delle immagini delle macchine virtuali Linux che supportano l'aggiunta dell'agente di Log Analytics all'ambito di controllo|Elenco delimitato da punto e virgola con le immagini, ad esempio: /subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Alla sottoscrizione deve essere assegnato più di un proprietario|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: La crittografia del disco deve essere applicata nelle macchine virtuali|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: È consigliabile abilitare le notifiche di posta elettronica al proprietario della sottoscrizione per gli avvisi con gravità alta|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Il debug remoto deve essere disattivato per le app per le funzioni|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la versione di '.NET Framework' sia la più recente, se usata come parte dell'app per le funzioni|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Transparent Data Encryption deve essere abilitata nei database SQL|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di PHP' sia la più recente, se usata come parte dell'app per le API|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Devono essere abilitate solo connessioni sicure alla Cache Redis|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: La soluzione Endpoint Protection deve essere installata nei set di scalabilità di macchine virtuali|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Controlla l'accesso di rete senza restrizioni agli account di archiviazione|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Le impostazioni avanzate di sicurezza dei dati per l'istanza gestita di SQL devono contenere un indirizzo di posta elettronica a cui ricevere gli avvisi di sicurezza|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Il trasferimento sicuro negli account di archiviazione deve essere abilitato|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: I controlli applicazioni adattivi devono essere abilitati nelle macchine virtuali|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Il backup con ridondanza geografica deve essere abilitato per i database di Azure per PostgreSQL|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app Web|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Per la sottoscrizione devono essere designati al massimo 3 proprietari|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: È consigliabile fornire un indirizzo di posta elettronica dei contatti di sicurezza per la sottoscrizione|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: CORS non deve consentire a tutte le risorse di accedere alle applicazioni Web|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Gli account deprecati devono essere rimossi dalla sottoscrizione|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: L'app per le funzioni deve essere accessibile solo tramite HTTPS|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app Web|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app per le funzioni|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di PHP' sia la più recente, se usata come parte dell'app Web|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app per le API|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MySQL|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la versione di '.NET Framework' sia la più recente, se usata come parte dell'app Web|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Gli aggiornamenti di sistema devono essere installati nelle macchine|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app per le API|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app Web|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Nell'app per le API è necessario usare la versione più recente di TLS|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Le impostazioni avanzate di sicurezza dei dati per SQL Server devono contenere un indirizzo di posta elettronica a cui ricevere gli avvisi di sicurezza|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app per le API|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: È consigliabile distribuire l'estensione Microsoft IaaSAntimalware nei server Windows|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app per le funzioni|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: L'accesso tramite endpoint con connessione Internet deve essere limitato|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: È consigliabile selezionare il piano tariffario Standard del Centro sicurezza|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: L'applicazione Web deve essere accessibile solo tramite HTTPS|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: È consigliabile abilitare il controllo in SQL Server|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: L'agente di Log Analytics deve essere installato nelle macchine virtuali|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Lo standard di protezione DDoS deve essere abilitato|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di PHP' sia la più recente, se usata come parte dell'app per le funzioni|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Sicurezza dei dati avanzata deve essere abilitata nei server SQL|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: La sicurezza dei dati avanzata deve essere abilitata nelle istanze gestite di SQL|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Le notifiche tramite posta elettronica agli amministratori e ai proprietari della sottoscrizione devono essere abilitate nelle impostazioni di Sicurezza dei dati avanzata dell'istanza gestita di SQL|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Alle macchine virtuali deve essere applicato il controllo di accesso alla rete JIT|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: È consigliabile fornire il numero di telefono dei contatti di sicurezza per la sottoscrizione|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: L'app per le API deve essere accessibile solo tramite HTTPS|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: È consigliabile che i Tipi di Advanced Threat Protection siano impostati su "Tutti" nelle impostazioni di Sicurezza dei dati avanzata nell'istanza gestita di SQL|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: L'archiviazione con ridondanza geografica deve essere abilitata per gli account di archiviazione|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la versione di '.NET Framework' sia la più recente, se usata come parte dell'app per le API|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Gli aggiornamenti di sistema nei set di scalabilità di macchine virtuali devono essere installati|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Le notifiche tramite posta elettronica agli amministratori e ai proprietari della sottoscrizione devono essere abilitate nelle impostazioni di Sicurezza dei dati avanzata del server SQL|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Il debug remoto deve essere disattivato per le applicazioni Web|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Il backup con ridondanza geografica a lungo termine deve essere abilitato per i database SQL di Azure|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app per le funzioni|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: È consigliabile che i Tipi di Advanced Threat Protection siano impostati su "Tutti" nelle impostazioni di Sicurezza dei dati avanzata in SQL Server|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: È consigliabile correggere le vulnerabilità nelle configurazioni della sicurezza dei contenitori|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Il debug remoto deve essere disattivato per le app per le API|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: La valutazione delle vulnerabilità deve essere abilitata nei server SQL|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: L'agente di Log Analytics deve essere installato nei set di scalabilità di macchine virtuali|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Nell'app Web è necessario usare la versione più recente di TLS|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Nell'app per le funzioni è necessario usare la versione più recente di TLS|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: [Anteprima]: I servizi Kubernetes devono essere aggiornati a una versione di Kubernetes non vulnerabile|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|
|DoD Impact Level 5|Assegnazione dei criteri|Effetto per il criterio: Le vulnerabilità dei database SQL devono essere risolte|Effetto di Criteri di Azure per questo criterio. Per altre informazioni sugli effetti, vedere https://aka.ms/policyeffects|

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato i passaggi per distribuire l'esempio di progetto DoD Impact Level 5, vedere gli articoli seguenti per informazioni sul progetto e sul mapping dei controlli:

> [!div class="nextstepaction"]
> [Progetto DoD Impact Level 5 - Panoramica](./index.md)
> [Progetto DoD Impact Level 5 - Mapping dei controlli](./control-mapping.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).