---
title: Esempio di progetto Servizi condivisi ISO 27001 - Procedura per la distribuzione
description: Procedura di distribuzione per l'esempio di progetto Servizi condivisi ISO 27001, inclusi i dettagli dei parametri dell'artefatto del progetto.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: 43b3eb76de4e180f002379198d363a6852ab9e3b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162499"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Distribuire l'esempio di progetto Servizi condivisi ISO 27001

Per distribuire l'esempio di progetto Servizi condivisi ISO 27001 di Azure Blueprints, seguire questa procedura:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare il progetto **ISO 27001: Servizi condivisi** in _Altri esempi_ e selezionare **Usa questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia dell'esempio di progetto Servizi condivisi ISO 27001.
   - **Posizione della definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti che costituiscono l'esempio di progetto. Molti artefatti hanno parametri che verranno definiti in seguito. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

## <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata in base all'ambiente e alle esigenze, ma le modifiche potrebbero renderla non conforme allo standard ISO 27001.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra specificare un valore di **Versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. In **Modifica le note** specificare ad esempio "Prima versione pubblicata dall'esempio di progetto ISO 27001". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

## <a name="assign-the-sample-copy"></a>Assegnare la copia dell'esempio

Dopo che la copia dell'esempio di progetto è stata **pubblicata** correttamente, è possibile assegnarla a una sottoscrizione all'interno del gruppo di gestione in cui è stata salvata. In questo passaggio vengono specificati i parametri che rendono univoca ogni distribuzione della copia dell'esempio di progetto.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Assegna progetto** nella parte superiore della pagina della definizione di progetto.

1. Specificare i valori dei parametri per l'assegnazione del progetto:

   - Nozioni di base

     - **Sottoscrizioni**: selezionare una o più sottoscrizioni presenti nel gruppo di gestione in cui è stata salvata la copia dell'esempio di progetto. Se si selezionano più sottoscrizioni, viene creata un'assegnazione per ciascuna usando i parametri immessi.
     - **Nome dell'assegnazione**: il nome viene prepopolato in base al nome del progetto.
       Cambiarlo, se necessario, o lasciarlo inalterato.
     - **Località**: selezionare un'area in cui creare l'identità gestita. Azure Blueprint usa questa identità gestita per distribuire tutti gli elementi nel progetto assegnato. Per altre informazioni, vedere [Managed identities for Azure resources](../../../../active-directory/managed-identities-azure-resources/overview.md) (Identità gestite per risorse di Azure).
     - **Versione della definizione di progetto**: Scegliere una versione **pubblicata** della copia dell'esempio di progetto.

   - Blocca assegnazione

     Selezionare l'impostazione di blocco del progetto per l'ambiente. Per altre informazioni, vedere [Blueprints resource locking](../../concepts/resource-locking.md) (Blocco delle risorse del progetto).

   - Identità gestita

     Lasciare invariata l'opzione predefinita _Identità gestita assegnata dal sistema_.

   - Parametri di progetto

     I parametri definiti in questa sezione vengono usati da molti artefatti nella definizione del progetto per assicurare coerenza.

     - **Nome organizzazione**: immettere un nome breve per l'organizzazione. Questa proprietà viene usata prevalentemente per l'assegnazione di nomi alle risorse.
     - **Prefisso dell'indirizzo della subnet dei servizi condivisi**: specificare il valore della notazione CIDR per collegare tra loro le risorse distribuite.
     - **Località dei servizi condivisi**: determina in quale località vengono distribuiti gli artefatti. Non tutti i servizi sono disponibili in tutte le località. Gli artefatti che distribuiscono tali servizi forniscono un'opzione di parametro per la località in cui eseguire la distribuzione.
     - **Località consentite (criteri: iniziativa progetto per ISO 27001)** : valore che indica le località consentite per gruppi di risorse e risorse.
     - **Area di lavoro di Log Analytics per gli agenti di macchine virtuali (criteri: iniziativa progetto per ISO 27001)** : specifica l'ID risorsa di un'area di lavoro. Questo parametro usa una funzione `concat` per costruire l'ID risorsa.

   - Parametri dell'elemento

     I parametri definiti in questa sezione si applicano all'artefatto in cui sono definiti. Si tratta di [parametri dinamici](../../concepts/parameters.md#dynamic-parameters), in quanto vengono definiti durante l'assegnazione del progetto. Per un elenco completo di parametri di artefatti e relative descrizioni, vedere la [tabella di parametri degli artefatti](#artifact-parameters-table).

1. Dopo avere immesso tutti i parametri, selezionare **Assegna** in fondo alla pagina. Viene creata l'assegnazione del progetto e inizia la distribuzione degli artefatti. La distribuzione richiede circa un'ora. Per controllare lo stato della distribuzione, aprire l'assegnazione del progetto.

> [!WARNING]
> Il servizio Azure Blueprints e gli esempi di progetti predefiniti sono **gratuiti**. Le risorse di Azure hanno un [prezzo in base al prodotto](https://azure.microsoft.com/pricing/). Usare la [calcolatrice dei prezzi](https://azure.microsoft.com/pricing/calculator/) per stimare il costo dell'esecuzione delle risorse distribuite da questo esempio di progetto.

## <a name="artifact-parameters-table"></a>Tabella di parametri degli artefatti

La tabella seguente contiene un elenco dei parametri degli artefatti del progetto:

|Nome dell'artefatto|Tipo di artefatto|Nome parametro|DESCRIZIONE|
|-|-|-|-|
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Linux|Assegnazione di criteri|Facoltativo: elenco delle immagini delle macchine virtuali che supportano il sistema operativo Linux da aggiungere all'ambito|(Facoltativo) Il valore predefinito è _["none"]_ .|
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux|Assegnazione di criteri|Facoltativo: elenco delle immagini delle macchine virtuali che supportano il sistema operativo Linux da aggiungere all'ambito|(Facoltativo) Il valore predefinito è _["none"]_ .|
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Windows|Assegnazione di criteri|Facoltativo: elenco delle immagini delle macchine virtuali che supportano il sistema operativo Windows da aggiungere all'ambito|(Facoltativo) Il valore predefinito è _["none"]_ .|
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows|Assegnazione di criteri|Facoltativo: elenco delle immagini delle macchine virtuali che supportano il sistema operativo Windows da aggiungere all'ambito|(Facoltativo) Il valore predefinito è _["none"]_ .|
|Tipi di risorse consentiti|Assegnazione di criteri|Tipi di risorse consentiti|Elenco dei tipi di risorse che possono essere distribuiti. Questo elenco è composto da tutti i tipi di risorse distribuiti in Servizi condivisi.|
|SKU degli account di archiviazione consentiti|Assegnazione di criteri|SKU di archiviazione consentiti|Elenco degli SKU di account di archiviazione dei log di diagnostica consentiti. Il valore predefinito è _["Standard_LRS"]_ .|
|SKU delle macchine virtuali consentiti|Assegnazione di criteri|Elenco di SKU di macchine virtuali che possono essere distribuiti. Il valore predefinito è _["Standard_DS1_v2", "Standard_DS2_v2"]_ .|
|Iniziativa progetto per ISO 27001|Assegnazione di criteri|Tipi di risorsa per il controllo dei log di diagnostica|Elenco dei tipi di risorse per il controllo, se l'impostazione dei log di diagnostica non è abilitata. I valori accettabili sono disponibili negli [schemi dei log di diagnostica di Monitoraggio di Azure](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Gruppo di risorse Log Analytics|Resource group|NOME|**Bloccato**: concatena il **nome dell'organizzazione** con `-sharedsvsc-log-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse Log Analytics|Resource group|Location|**Bloccato**: usa il parametro del progetto.|
|Modello Log Analytics|Modello di Resource Manager|Livello di servizio|Imposta il livello dell'area di lavoro di Log Analytics. Il valore predefinito è _PerNode_.|
|Modello Log Analytics|Modello di Resource Manager|Conservazione log in giorni|Il periodo di conservazione dei dati in giorni. Il valore predefinito è _365_.|
|Modello Log Analytics|Modello di Resource Manager|Location|L'area usata per la creazione dell'area di lavoro di Log Analytics. Il valore predefinito è _Stati Uniti occidentali 2_.|
|Gruppo di risorse di rete|Resource group|NOME|**Bloccato**: concatena il **nome dell'organizzazione** con `-sharedsvcs-net-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse di rete|Resource group|Location|**Bloccato**: usa il parametro del progetto.|
|Modello Firewall di Azure|Modello di Resource Manager|Indirizzo IP privato di Firewall di Azure|Configura l'indirizzo IP privato di [Firewall di Azure](../../../../firewall/overview.md). Questo valore viene usato anche come tabella di route predefinita nella subnet di servizi condivisi. Deve fare parte della notazione CIDR definita in **Prefisso dell'indirizzo della subnet di Firewall di Azure**. Il valore predefinito è _10.0.4.4_.|
|Modello Firewall di Azure|Modello di Resource Manager|Conservazione log in giorni|Il periodo di conservazione dei dati in giorni. Il valore predefinito è _365_.|
|Modello Gruppi di sicurezza di rete|Modello di Resource Manager|Conservazione log in giorni|Il periodo di conservazione dei dati in giorni. Il valore predefinito è _365_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Prefisso dell'indirizzo della rete virtuale|La notazione CIDR per rete virtuale. Il valore predefinito è _10.0.0.0/16_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Abilita protezione DDoS della rete virtuale|Configura la protezione DDoS per la rete virtuale. Il valore predefinito è _true_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Prefisso dell'indirizzo della subnet dei servizi condivisi|La notazione CIDR per la subnet di Servizi condivisi. Il valore predefinito è _10.0.0.0/24_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Prefisso dell'indirizzo della subnet della rete perimetrale|La notazione CIDR per la subnet della rete perimetrale. Il valore predefinito è _10.0.1.0/24_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Prefisso dell'indirizzo della subnet del gateway applicazione|La notazione CIDR per la subnet del gateway applicazione. Il valore predefinito è _10.0.2.0/24_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Prefisso dell'indirizzo della subnet del gateway di rete virtuale|La notazione CIDR per la subnet del gateway di rete virtuale. Il valore predefinito è _10.0.3.0/24_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Prefisso dell'indirizzo della subnet di Firewall di Azure|La notazione CIDR per la subnet di [Firewall di Azure](../../../../firewall/overview.md). Deve includere il parametro **Indirizzo IP privato di Firewall di Azure**.|
|Gruppo di risorse dell'insieme di credenziali delle chiavi|Resource group|NOME|**Bloccato**: concatena il **nome dell'organizzazione** con `-sharedsvcs-kv-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse dell'insieme di credenziali delle chiavi|Resource group|Location|**Bloccato**: usa il parametro del progetto.|
|Modello Key Vault|Modello di Resource Manager|Nome utente amministratore JumpBox|Nome utente per JumpBox. Deve corrispondere allo stesso valore della proprietà nel **modello JumpBox**. Il valore predefinito è _jb-admin-user_.|
|Modello Key Vault|Modello di Resource Manager|Chiave SSH o password amministratore JumpBox|Chiave o password per l'account in JumpBox. Deve corrispondere allo stesso valore della proprietà nel **modello JumpBox**. Non prevede valori predefiniti e non può essere lasciato vuoto.|
|Modello Key Vault|Modello di Resource Manager|Nome utente amministratore dominio|Nome utente usato per accedere alla macchina virtuale di Active Directory e per aggiungere altre macchine virtuali a un dominio. Deve corrispondere al valore della proprietà **Utente amministratore di dominio** nel **modello Active Directory Domain Services**. Il valore predefinito è _domain-admin-user_.|
|Modello Key Vault|Modello di Resource Manager|Password amministratore dominio|Password dell'utente amministratore di dominio. Non prevede valori predefiniti e non può essere lasciato vuoto.|
|Modello Key Vault|Modello di Resource Manager|ID oggetto AAD|L'identificatore dell'oggetto AAD dell'account che richiede l'accesso all'istanza di Key Vault. Non prevede valori predefiniti e non può essere lasciato vuoto. Per individuare questo valore nel portale di Azure, cercare e selezionare "Utenti" in _Servizi_. Usare la casella _Nome_ per filtrare per il nome dell'account, quindi selezionarlo. Nella pagina _Profilo utente_ selezionare l'icona "Fare clic per copiare" accanto a _ID oggetto_.  |
|Modello Key Vault|Modello di Resource Manager|Conservazione log in giorni|Il periodo di conservazione dei dati in giorni. Il valore predefinito è _365_.|
|Modello Key Vault|Modello di Resource Manager|SKU Key Vault|Specifica lo SKU dell'istanza di Key Vault creata. Il valore predefinito è _Premium_.|
|Gruppo di risorse di JumpBox|Resource group|NOME|**Bloccato**: concatena il **nome dell'organizzazione** con `-sharedsvcs-jb-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse di JumpBox|Resource group|Location|**Bloccato**: usa il parametro del progetto.|
|Modello JumpBox|Modello di Resource Manager|Nome utente amministratore JumpBox|Nome utente usato per accedere alle macchine virtuali JumpBox. Deve corrispondere allo stesso valore della proprietà nel **modello Key Vault**. Il valore predefinito è _jb-admin-user_.|
|Modello JumpBox|Modello di Resource Manager|Password amministratore JumpBox (ID risorsa Key Vault)|L'ID risorsa di Key Vault. Usare "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" e sostituire `{subscriptionId}` con l'ID sottoscrizione e `{orgName}` con il parametro del progetto **Nome organizzazione**.|
|Modello JumpBox|Modello di Resource Manager|Password amministratore JumpBox (Nome segreto Key Vault)|Nome utente dell'amministratore di JumpBox. Deve corrispondere al valore della proprietà **Nome utente amministratore JumpBox** del **modello Key Vault**.|
|Modello JumpBox|Modello di Resource Manager|Sistema operativo JumpBox|Determina il sistema operativo della macchina virtuale JumpBox. Il valore predefinito è _Windows_.|
|Gruppo di risorse Active Directory Domain Services|Resource group|NOME|**Bloccato**: concatena il **nome dell'organizzazione** con `-sharedsvcs-adds-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse Active Directory Domain Services|Resource group|Location|**Bloccato**: usa il parametro del progetto.|
|Modello Active Directory Domain Services|Modello di Resource Manager|Nome utente amministratore dominio|Nome utente per JumpBox di Active Directory Domain Services. Deve corrispondere allo stesso valore della proprietà nel **modello Key Vault**. Il valore predefinito è _adds-admin-user_.|
|Modello Active Directory Domain Services|Modello di Resource Manager|Password amministratore dominio (ID risorsa Key Vault)|L'ID risorsa di Key Vault. Usare "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" e sostituire `{subscriptionId}` con l'ID sottoscrizione e `{orgName}` con il parametro del progetto **Nome organizzazione**.|
|Modello Active Directory Domain Services|Modello di Resource Manager|Password amministratore dominio (Nome segreto Key Vault)|Nome utente dell'amministratore di dominio. Deve corrispondere al valore della proprietà **Nome utente amministratore dominio** del **modello Key Vault**.|
|Modello Active Directory Domain Services|Modello di Resource Manager|Nome di dominio|Il nome dell'istanza di Active Directory creata dall'esempio. Il valore predefinito è _contoso.com_.|
|Modello Active Directory Domain Services|Modello di Resource Manager|Utente amministratore dominio|Nome utente dell'account amministratore di AD e per l'aggiunta di dispositivi al dominio AD. Deve corrispondere al valore della proprietà **Nome utente amministratore AD** del **modello Key Vault**. Il valore predefinito è _domain-admin-user_.|
|Modello Active Directory Domain Services|Modello di Resource Manager|Password amministratore dominio|Impostare i dettagli di Key Vault per l'archiviazione della password. Non prevede valori predefiniti e non può essere lasciato vuoto.|

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato la procedura per la distribuzione dell'esempio di progetto Servizi condivisi ISO 27001, vedere gli articoli seguenti per informazioni sull'architettura e sul mapping dei controlli:

> [!div class="nextstepaction"]
> [Progetto Servizi condivisi ISO 27001 - Panoramica](./index.md)
> [Progetto Servizi condivisi ISO 27001 - Mapping dei controlli](./control-mapping.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
