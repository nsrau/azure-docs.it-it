---
title: Esempio - servizi condivisi di ISO 27001 blueprint - distribuzione passaggi
description: Distribuire i passaggi dell'esempio di progetto di servizi condivisi di ISO 27001.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 64259d029ce67ecfd5db4b97fc165be0ee391ab8
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2019
ms.locfileid: "58008071"
---
# <a name="deploy-the-azure-blueprints-iso-27001-shared-services-blueprint-sample"></a>Distribuire l'esempio di progetto di servizi condivisi di Azure progetti ISO 27001

Per distribuire l'esempio di progetto di servizi condivisi di Azure progetti ISO 27001, è necessario effettuare i passaggi seguenti:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio di come **pubblicato**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="create-blueprint-from-sample"></a>Crea progetto dall'esempio

Innanzitutto, implementare l'esempio di progetto mediante la creazione di un nuovo progetto nel proprio ambiente usando l'esempio come un avvio.

1. Selezionare **tutti i servizi** e cercare e selezionare **criterio** nel riquadro sinistro. Nel **criterio** pagina, selezionare **progetti**.

1. Dal **Guida introduttiva** pagina a sinistra, seleziona il **Create** pulsante sotto _creare un progetto iniziale_.

1. Trovare il **ISO 27001: Servizi condivisi** esempio di progetto in _altri esempi_ e selezionare **usare questo esempio**.

1. Immettere il _nozioni di base_ dell'esempio di progetto:

   - **Nome progetto**: Specificare un nome per la copia dell'esempio di progetto di servizi condivisi di ISO 27001.
   - **Percorso definizione**: Usare i puntini di sospensione e selezionare il gruppo di gestione per salvare la copia dell'esempio a.

1. Selezionare il _artefatti_ della scheda nella parte superiore della pagina o **successiva: Gli artefatti** nella parte inferiore della pagina.

1. Esaminare l'elenco di elementi che costituiscono l'esempio di progetto. Molti degli elementi di parametri che verranno definiti in un secondo momento. Selezionare **Salva bozza** quando aver esaminato il codice di esempio di progetto.

## <a name="publish-the-sample-copy"></a>Pubblicare la copia di esempio

La copia dell'esempio di progetto è stata creata nell'ambiente in uso. Viene creato nel **bozza** modalità e deve essere **Published** prima che possa essere assegnato e distribuito. La copia dell'esempio di progetto può essere personalizzata con l'ambiente e alle esigenze, ma tale modifica potrebbe allontanano lo standard ISO 27001.

1. Selezionare **tutti i servizi** e cercare e selezionare **criterio** nel riquadro sinistro. Nel **criterio** pagina, selezionare **progetti**.

1. Selezionare il **Blueprint definizioni** pagina a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarlo.

1. Selezionare **linee guida per la pubblicazione** nella parte superiore della pagina. Nella nuova pagina a destra, specificare una **versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica in un secondo momento. Fornire **modificare note** , ad esempio "prima versione pubblicato dall'esempio ISO 27001 blueprint". Quindi selezionare **pubblica** nella parte inferiore della pagina.

## <a name="assign-the-sample-copy"></a>Assegnare la copia di esempio

Dopo la copia dell'esempio di progetto è stato correttamente **Published**, può essere assegnato a una sottoscrizione all'interno del gruppo di gestione, è stata salvata. Questo passaggio è dove vengono specificati parametri per rendere univoca ciascuna distribuzione della copia dell'esempio di progetto.

1. Selezionare **tutti i servizi** e cercare e selezionare **criterio** nel riquadro sinistro. Nel **criterio** pagina, selezionare **progetti**.

1. Selezionare il **Blueprint definizioni** pagina a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarlo.

1. Selezionare **Assign blueprint** nella parte superiore della pagina di definizione di progetto.

1. Specificare i valori dei parametri per l'assegnazione di progetto:

   - Nozioni di base

     - **Sottoscrizioni**: Selezionare uno o più sottoscrizioni che si trovano nel gruppo di gestione salvato la copia dell'esempio al progetto. Se si seleziona più di una sottoscrizione, un'assegnazione verrà creata per ogni utilizzando i parametri immessi.
     - **Nome dell'assegnazione**: Il nome è già popolato automaticamente in base al nome del progetto.
       Modificare in base alle esigenze o lasciare invariato.
     - **Località**: Selezionare un'area per l'identità gestita da creare. Azure Blueprint usa questa identità gestita per distribuire tutti gli elementi nel progetto assegnato. Per altre informazioni, vedere [Managed identities for Azure resources](../../../../active-directory/managed-identities-azure-resources/overview.md) (Identità gestite per risorse di Azure).
     - **Versione della definizione di progetto**: Selezionare una **Published** versione di una copia dell'esempio di progetto.

   - Blocca assegnazione

     Selezionare il blocco di progetto l'impostazione per l'ambiente. Per altre informazioni, vedere [Blueprints resource locking](../../concepts/resource-locking.md) (Blocco delle risorse del progetto).

   - Identità gestita

     Lasciare l'impostazione predefinita _assegnata dal sistema_ gestiti opzione identity.

   - Parametri di progetto

     I parametri definiti in questa sezione vengono usati da molti degli elementi nella definizione di progetto per fornire coerenza.

     - **Nome dell'organizzazione**: Immettere un nome breve per l'organizzazione. Questa proprietà viene utilizzata principalmente per la denominazione delle risorse.
     - **Prefisso dell'indirizzo subnet servizi condivisi**: Specificare il valore di notazione CIDR per la rete tra loro le risorse distribuite.
     - **La posizione del servizio condiviso**: Determina quali posizione vengono distribuiti gli elementi. Non tutti i servizi sono disponibili in tutte le posizioni. Elementi di distribuzione di tali servizi offrono un'opzione di parametro per il percorso in cui distribuire questo artefatto.
     - **Percorso consentito (criteri: Blueprint iniziativa ISO 27001)**: Valore che indica le posizioni consentite per i gruppi di risorse e le risorse.
     - **Log dell'area di lavoro di Analitica per agenti di macchine Virtuali (criteri: Blueprint iniziativa ISO 27001)**: Specifica l'ID risorsa dell'area di lavoro. Questo parametro Usa un `concat` funzione per costruire l'ID della risorsa.

   - Parametri dell'elemento

     I parametri definiti in questa sezione si applicano all'elemento in cui è definito. Questi parametri sono [parametri dinamici](../../concepts/parameters.md#dynamic-parameters) poiché sono definiti durante l'assegnazione del progetto. Per un elenco completo o parametri dell'elemento e le relative descrizioni, vedere [tabella di parametri dell'elemento](#artifact-parameters-table).

1. Dopo avere immesso tutti i parametri, selezionare **assegnare** nella parte inferiore della pagina.

> [!WARNING]
> Il servizio di progetti di architetture di Azure e gli esempi di progetto predefinite siano **gratuito**. Risorse di Azure sono [i prezzi dipendono dal prodotto](https://azure.microsoft.com/en-us/pricing/). Usare la [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) per stimare il costo delle risorse distribuite da questo esempio di progetto.

## <a name="artifact-parameters-table"></a>Tabella di parametri dell'elemento

Nella tabella seguente fornisce un elenco del progetto di parametri dell'elemento:

|Nome dell'artefatto|Tipo di artefatto|Nome parametro|DESCRIZIONE|
|-|-|-|-|
|[Anteprima]: Distribuire l'agente di Log Analitica per set di scalabilità di macchine Virtuali Linux (VMSS)|Assegnazione di criteri|Facoltativo: Elenco di immagini VM sono supportate del sistema operativo Linux per aggiungere all'ambito|(Facoltativo) Valore predefinito è _["none"]_.|
|[Anteprima]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux|Assegnazione di criteri|Facoltativo: Elenco di immagini VM sono supportate del sistema operativo Linux per aggiungere all'ambito|(Facoltativo) Valore predefinito è _["none"]_.|
|[Anteprima]: Distribuire l'agente di Log Analitica per set di scalabilità di macchine Virtuali Windows (VMSS)|Assegnazione di criteri|Facoltativo: Elenco di immagini VM sono supportate del sistema operativo Windows da aggiungere all'ambito|(Facoltativo) Valore predefinito è _["none"]_.|
|[Anteprima]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows|Assegnazione di criteri|Facoltativo: Elenco di immagini VM sono supportate del sistema operativo Windows da aggiungere all'ambito|(Facoltativo) Valore predefinito è _["none"]_.|
|Tipi di risorse consentiti|Assegnazione di criteri|Tipi di risorse consentiti|Elenco dei tipi di risorse consentiti per la distribuzione. Questo elenco è composto da tutti i tipi di risorse distribuiti in servizi condivisi.|
|SKU degli account di archiviazione consentiti|Assegnazione di criteri|SKU di archiviazione è consentita|Elenco di diagnostica che registra consentiti gli SKU di account di archiviazione. Valore predefinito è _["Standard_LRS"]_.|
|SKU delle macchine virtuali consentiti|Assegnazione di criteri|Utilizzare un elenco degli SKU di macchina virtuale da distribuire. Valore predefinito è _["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Iniziativa progetto per ISO 27001|Assegnazione di criteri|Tipi di risorse per controllare i log di diagnostica|Elenco di tipi di risorse per controllare se l'impostazione di log di diagnostica non è abilitata. I valori accettabili sono reperibile in [schemi dei log di diagnostica di monitoraggio di Azure](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Gruppo di risorse di log Analitica|Gruppo di risorse|NOME|**Bloccato** -consente di concatenare il **nome organizzazione** con `-sharedsvsc-log-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse di log Analitica|Gruppo di risorse|Località|**Bloccato** -Usa il parametro del progetto.|
|Modello Log Analytics|Modello di Resource Manager|Livello di servizio|Imposta il livello di area di lavoro di Log Analitica. Valore predefinito è _PerNode_.|
|Modello Log Analytics|Modello di Resource Manager|Conservazione log in giorni|Conservazione dei dati in giorni. Valore predefinito è _365_.|
|Modello Log Analytics|Modello di Resource Manager|Località|Area usata per creare l'area di lavoro di Log Analitica. Valore predefinito è _Stati Uniti occidentali 2_.|
|Gruppo di risorse di rete|Gruppo di risorse|NOME|**Bloccato** -consente di concatenare il **nome organizzazione** con `-sharedsvcs-net-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse di rete|Gruppo di risorse|Località|**Bloccato** -Usa il parametro del progetto.|
|Modello Firewall di Azure|Modello di Resource Manager|Indirizzo IP privato di Firewall di Azure|Consente di configurare l'indirizzo IP privato del [firewall Azure](../../../../firewall/overview.md). Questo valore viene usato anche come tabella di route predefinita nella subnet di servizi condivisi. Deve essere parte della notazione CIDR definita in **prefisso indirizzo della subnet Firewall Azure**. Valore predefinito è _10.0.4.4_.|
|Modello Firewall di Azure|Modello di Resource Manager|Conservazione log in giorni|Conservazione dei dati in giorni. Valore predefinito è _365_.|
|Modello Gruppi di sicurezza di rete|Modello di Resource Manager|Conservazione log in giorni|Conservazione dei dati in giorni. Valore predefinito è _365_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Prefisso dell'indirizzo della rete virtuale|La notazione CIDR per la rete virtuale. Valore predefinito è _10.0.0.0/16_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Abilita protezione DDoS della rete virtuale|Configura la protezione DDoS per la rete virtuale. Valore predefinito è _true_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Prefisso dell'indirizzo subnet servizi condivisi|La notazione CIDR per la subnet di servizi condivisi. Valore predefinito è _10.0.0.0/24_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Prefisso dell'indirizzo della subnet della rete perimetrale|La notazione CIDR per la subnet della rete Perimetrale. Valore predefinito è _10.0.1.0/24_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Prefisso dell'indirizzo della subnet del gateway applicazione|La notazione CIDR della subnet del gateway applicazione. Valore predefinito è _10.0.2.0/24_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Prefisso dell'indirizzo della subnet del gateway di rete virtuale|La notazione CIDR della subnet del gateway di rete virtuale. Valore predefinito è _10.0.3.0/24_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Prefisso dell'indirizzo della subnet di Firewall di Azure|La notazione CIDR per la [firewall Azure](../../../../firewall/overview.md) subnet. Deve includere il **indirizzo IP privato di Azure firewall** parametro.|
|Gruppo di risorse dell'insieme di credenziali delle chiavi|Gruppo di risorse|NOME|**Bloccato** -consente di concatenare il **nome organizzazione** con `-sharedsvcs-kv-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse dell'insieme di credenziali delle chiavi|Gruppo di risorse|Località|**Bloccato** -Usa il parametro del progetto.|
|Modello Key Vault|Modello di Resource Manager|Nome utente amministratore JumpBox|Nome utente per il jumpbox. Deve corrispondere stesso valore della proprietà in **Jumpbox modello**. Valore predefinito è _jb-admin-user_.|
|Modello Key Vault|Modello di Resource Manager|Chiave SSH o password amministratore JumpBox|Chiave o password per l'account nel jumpbox. Deve corrispondere stesso valore della proprietà in **Jumpbox modello**. Nessuna impostazione predefinita il valore e non può essere vuoto.|
|Modello Key Vault|Modello di Resource Manager|Nome utente amministratore dominio|Nome utente usato per accedere alle macchine Virtuali di Active Directory e aggiungere altre macchine virtuali a un dominio. Deve corrispondere **utente amministratore di dominio** nel valore della proprietà **modello di servizi di dominio Active Directory**. Valore predefinito è _utente di amministratore di dominio_.|
|Modello Key Vault|Modello di Resource Manager|Password amministratore dominio|Password dell'utente dell'amministratore di dominio. Nessuna impostazione predefinita il valore e non può essere vuoto.|
|Modello Key Vault|Modello di Resource Manager|ID oggetto AAD|L'identificatore di oggetto AAD dell'account che richiede l'accesso all'istanza di Key Vault. Nessuna impostazione predefinita il valore e non può essere vuoto. Per individuare questo valore dal portale di Azure, cercare e selezionare "Utenti" nella _Services_. Usare la _nome_ casella per filtrare il nome dell'account e selezionare tale account. Nel _profilo utente_ pagina, selezionare l'icona "Fare clic per copiare" accanto al _ID oggetto_.  |
|Modello Key Vault|Modello di Resource Manager|Conservazione log in giorni|Conservazione dei dati in giorni. Valore predefinito è _365_.|
|Modello Key Vault|Modello di Resource Manager|SKU Key Vault|Specifica lo SKU dell'insieme di credenziali chiave che viene creato. Valore predefinito è _Premium_.|
|Gruppo di risorse Jumpbox|Gruppo di risorse|NOME|**Bloccato** -consente di concatenare il **nome organizzazione** con `-sharedsvcs-jb-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse Jumpbox|Gruppo di risorse|Località|**Bloccato** -Usa il parametro del progetto.|
|Modello JumpBox|Modello di Resource Manager|Nome utente amministratore JumpBox|Il nome utente usato per accedere alle macchine virtuali jumpbox. Deve corrispondere stesso valore della proprietà in **modello di Key Vault**. Valore predefinito è _jb-admin-user_.|
|Modello JumpBox|Modello di Resource Manager|Password amministratore Jumpbox (ID di risorsa dell'insieme di credenziali chiave)|L'ID risorsa dell'insieme di credenziali chiave. Usare "/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" e sostituire `{subscriptionId}` con l'ID sottoscrizione e `{orgName}` con il  **Nome dell'organizzazione** parametro del progetto.|
|Modello JumpBox|Modello di Resource Manager|Password amministratore Jumpbox (nome del segreto dell'insieme di credenziali chiave)|Nome utente dell'amministratore del jumpbox. Deve corrispondere a valore in **modello di Key Vault** proprietà **Jumpbox admin username**.|
|Modello JumpBox|Modello di Resource Manager|Sistema operativo JumpBox|Determina il sistema operativo della macchina virtuale jumpbox. Valore predefinito è _Windows_.|
|Gruppo di risorse Active Directory Domain Services|Gruppo di risorse|NOME|**Bloccato** -consente di concatenare il **nome organizzazione** con `-sharedsvcs-adds-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse Active Directory Domain Services|Gruppo di risorse|Località|**Bloccato** -Usa il parametro del progetto.|
|Modello Active Directory Domain Services|Modello di Resource Manager|Nome utente amministratore dominio|Nome utente per il jumpbox ADDS. Deve corrispondere stesso valore della proprietà in **modello di Key Vault**. Valore predefinito è _aggiunge--l'utente amministratore_.|
|Modello Active Directory Domain Services|Modello di Resource Manager|Password di amministratore di dominio (ID di risorsa dell'insieme di credenziali chiave)|L'ID risorsa dell'insieme di credenziali chiave. Usare "/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" e sostituire `{subscriptionId}` con l'ID sottoscrizione e `{orgName}` con il  **Nome dell'organizzazione** parametro del progetto.|
|Modello Active Directory Domain Services|Modello di Resource Manager|Password di amministratore di dominio (nome del segreto dell'insieme di credenziali chiave)|Nome utente dell'amministratore del dominio. Deve corrispondere a valore in **modello di Key Vault** proprietà **nome utente amministratore di dominio**.|
|Modello Active Directory Domain Services|Modello di Resource Manager|Nome di dominio|Nome di Active Directory creato dall'esempio. Valore predefinito è _contoso.com_.|
|Modello Active Directory Domain Services|Modello di Resource Manager|Utente amministratore di dominio|Nome utente per l'account di amministratore AD e per l'aggiunta di dispositivi al dominio di Active Directory. Deve corrispondere **nome utente amministratore di Active Directory** nel valore della proprietà **modello di Key Vault**. Valore predefinito è _utente di amministratore di dominio_.|
|Modello Active Directory Domain Services|Modello di Resource Manager|Password amministratore dominio|Impostare i dettagli di Key Vault per archiviare la password. Nessuna impostazione predefinita il valore e non può essere vuoto.|

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata esaminata la procedura per distribuire l'esempio di progetto di servizi condivisi di ISO 27001, vedere gli articoli seguenti per informazioni sull'architettura e mapping di controllo:

> [!div class="nextstepaction"]
> [Progetto di servizi condivisi di ISO 27001 - Panoramica](./index.md)
> [progetto di servizi condivisi di ISO 27001 - mapping di controllo](./control-mapping.md)

Articoli di addizione su progetti e su come utilizzarle:

- Scopri le [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare il [blueprint ordine sequenziazione](../../concepts/sequencing-order.md).
- Scopri come rendere sfrutta [blueprint blocco delle risorse](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare le assegnazioni esistenti](../../how-to/update-existing-assignments.md).