---
title: Esempio di progetto Carico di lavoro dell'ambiente del servizio app di Azure/SQL ISO 27001 - Procedura per la distribuzione
description: Procedura per la distribuzione dell'esempio di progetto Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001, inclusi i dettagli dei parametri dell'artefatto del progetto.
ms.date: 03/14/2019
ms.topic: sample
ms.openlocfilehash: edcf9920f3a65c182240da735b1883b3d67da650
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032147"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Distribuire l'esempio di progetto Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001

Per distribuire l'esempio di progetto Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001 di Azure Blueprints, seguire questa procedura:

> [!div class="checklist"]
> - Distribuire l'esempio di progetto [Servizi condivisi ISO 27001](../iso27001-shared/index.md)
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Distribuire l'esempio di progetto Servizi condivisi ISO 27001

Prima di distribuire questo esempio di progetto, è necessario distribuire l'esempio di progetto [Servizi condivisi ISO 27001](../iso27001-shared/index.md) nella sottoscrizione di destinazione. Senza una corretta distribuzione dell'esempio di progetto Servizi condivisi ISO 27001, in questo esempio di progetto mancheranno le dipendenze dell'infrastruttura e si verificherà un errore durante la distribuzione.

> [!IMPORTANT]
> Questo esempio di progetto deve essere assegnato nella stesso sottoscrizione dell'esempio di progetto [Servizi condivisi ISO 27001](../iso27001-shared/index.md).

## <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare il progetto **ISO 27001: Carico di lavoro dell'ambiente del servizio app/SQL** in _Altri esempi_ e selezionare **Usa questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia dell'esempio di progetto Carico di lavoro dell'ambiente del servizio app/SQL ISO 27001.
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
     - **ID sottoscrizione servizi condivisi**: l'ID della sottoscrizione in cui è assegnato l'esempio di progetto [Servizi condivisi ISO 27001](../iso27001-shared/index.md).
     - **Prefisso dell'indirizzo della subnet predefinito**: la notazione CIDR per la subnet predefinita della rete virtuale.
       Il valore predefinito è _10.1.0.0/16_.
     - **Località del carico di lavoro**: determina in quale località vengono distribuiti gli artefatti. Non tutti i servizi sono disponibili in tutte le località. Gli artefatti che distribuiscono tali servizi forniscono un'opzione di parametro per la località in cui eseguire la distribuzione.

   - Parametri dell'elemento

     I parametri definiti in questa sezione si applicano all'artefatto in cui sono definiti. Si tratta di [parametri dinamici](../../concepts/parameters.md#dynamic-parameters), in quanto vengono definiti durante l'assegnazione del progetto. Per un elenco completo di parametri di artefatti e relative descrizioni, vedere la [tabella di parametri degli artefatti](#artifact-parameters-table).

1. Dopo avere immesso tutti i parametri, selezionare **Assegna** in fondo alla pagina. Viene creata l'assegnazione del progetto e inizia la distribuzione degli artefatti. La distribuzione richiede circa un'ora. Per controllare lo stato della distribuzione, aprire l'assegnazione del progetto.

> [!WARNING]
> Il servizio Azure Blueprints e gli esempi di progetti predefiniti sono **gratuiti**. Le risorse di Azure hanno un [prezzo in base al prodotto](https://azure.microsoft.com/pricing/). Usare la [calcolatrice dei prezzi](https://azure.microsoft.com/pricing/calculator/) per stimare il costo dell'esecuzione delle risorse distribuite da questo esempio di progetto.

## <a name="artifact-parameters-table"></a>Tabella di parametri degli artefatti

La tabella seguente contiene un elenco dei parametri degli artefatti del progetto:

|Nome dell'artefatto|Tipo di artefatto|Nome parametro|DESCRIZIONE|
|-|-|-|-|
|Gruppo di risorse Log Analytics|Resource group|NOME|**Bloccato**: concatena il **nome dell'organizzazione** con `-workload-log-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse Log Analytics|Resource group|Location|**Bloccato**: usa il parametro del progetto.|
|Modello Log Analytics|Modello di Resource Manager|Livello di servizio|Imposta il livello dell'area di lavoro di Log Analytics. Il valore predefinito è _PerNode_.|
|Modello Log Analytics|Modello di Resource Manager|Conservazione log in giorni|Il periodo di conservazione dei dati in giorni. Il valore predefinito è _365_.|
|Modello Log Analytics|Modello di Resource Manager|Location|L'area usata per la creazione dell'area di lavoro di Log Analytics. Il valore predefinito è _Stati Uniti occidentali 2_.|
|Gruppo di risorse di rete|Resource group|NOME|**Bloccato**: concatena il **nome dell'organizzazione** con `-workload-net-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse di rete|Resource group|Location|**Bloccato**: usa il parametro del progetto.|
|Modello Gruppi di sicurezza di rete|Modello di Resource Manager|Conservazione log in giorni|Il periodo di conservazione dei dati in giorni. Il valore predefinito è _365_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Indirizzo IP privato di Firewall di Azure|Configura l'indirizzo IP privato di [Firewall di Azure](../../../../firewall/overview.md). Deve far parte della notazione CIDR definita nel parametro dell'artefatto di _ISO 27001: Servizi condivisi_ denominato **Prefisso dell'indirizzo della subnet di Firewall di Azure**. Il valore predefinito è _10.0.4.4_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|ID sottoscrizione servizi condivisi|Valore usato per abilitare il peering di rete virtuale tra un carico di lavoro e i servizi condivisi.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Prefisso dell'indirizzo della rete virtuale|La notazione CIDR per rete virtuale. Il valore predefinito è _10.1.0.0/16_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Prefisso dell'indirizzo della subnet predefinito|La notazione CIDR per la subnet predefinita della rete virtuale. Il valore predefinito è _10.1.0.0/16_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Indirizzo IP Active Directory Domain Services|Indirizzo IP della prima VM di Active Directory Domain Services. Questo valore viene usato come DNS di rete virtuale personalizzato.|
|Gruppo di risorse dell'insieme di credenziali delle chiavi|Resource group|NOME|**Bloccato**: concatena il **nome dell'organizzazione** con `-workload-kv-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse dell'insieme di credenziali delle chiavi|Resource group|Location|**Bloccato**: usa il parametro del progetto.|
|Modello Key Vault|Modello di Resource Manager|ID oggetto AAD|L'identificatore dell'oggetto AAD dell'account che richiede l'accesso all'istanza di Key Vault. Non prevede valori predefiniti e non può essere lasciato vuoto. Per individuare questo valore nel portale di Azure, cercare e selezionare "Utenti" in _Servizi_. Usare la casella _Nome_ per filtrare per il nome dell'account, quindi selezionarlo. Nella pagina _Profilo utente_ selezionare l'icona "Fare clic per copiare" accanto a _ID oggetto_.|
|Modello Key Vault|Modello di Resource Manager|Conservazione log in giorni|Il periodo di conservazione dei dati in giorni. Il valore predefinito è _365_.|
|Modello Key Vault|Modello di Resource Manager|SKU Key Vault|Specifica lo SKU dell'istanza di Key Vault creata. Il valore predefinito è _Premium_.|
|Modello Key Vault|Modello di Resource Manager|Nome utente amministratore del server SQL di Azure|Il nome utente usato per accedere al server SQL di Azure. Devo corrispondere allo stesso valore della proprietà nel **Modello Database SQL di Azure**. Il valore predefinito è _sql-admin-user_.|
|Gruppo di risorse Database SQL di Azure|Resource group|NOME|**Bloccato**: concatena il **nome dell'organizzazione** con `-workload-azsql-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse Database SQL di Azure|Resource group|Location|**Bloccato**: usa il parametro del progetto.|
|Modello Database SQL di Azure|Modello di Resource Manager|Nome utente amministratore del server SQL di Azure|Il nome utente per il server SQL di Azure. Deve corrispondere allo stesso valore della proprietà nel **modello Key Vault**. Il valore predefinito è _sql-admin-user_.|
|Modello Database SQL di Azure|Modello di Resource Manager|Password amministratore del server SQL di Azure (ID risorsa Key Vault)|L'ID risorsa di Key Vault. Usare "/subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" e sostituire `{subscriptionId}` con l'ID sottoscrizione e `{orgName}` con il parametro del progetto **Nome organizzazione**.|
|Modello Database SQL di Azure|Modello di Resource Manager|Password amministratore del server SQL di Azure (nome segreto Key Vault)|Il nome utente dell'amministratore di SQL Server. Deve corrispondere al valore della proprietà **Nome utente amministratore del server SQL di Azure** del **modello Key Vault**.|
|Modello Database SQL di Azure|Modello di Resource Manager|Conservazione log in giorni|Il periodo di conservazione dei dati in giorni. Il valore predefinito è _365_.|
|Modello Database SQL di Azure|Modello di Resource Manager|ID oggetto amministratore AAD|L'ID oggetto AAD dell'utente che verrà assegnato come amministratore di Active Directory. Non prevede valori predefiniti e non può essere lasciato vuoto. Per individuare questo valore nel portale di Azure, cercare e selezionare "Utenti" in _Servizi_. Usare la casella _Nome_ per filtrare per il nome dell'account, quindi selezionarlo. Nella pagina _Profilo utente_ selezionare l'icona "Fare clic per copiare" accanto a _ID oggetto_.|
|Modello Database SQL di Azure|Modello di Resource Manager|Account di accesso amministratore AAD|Attualmente gli account Microsoft, come live.com o outlook.com, non possono essere impostati come amministratore. Solo gli utenti e i gruppi di sicurezza all'interno dell'organizzazione possono essere impostati come amministratore. Non prevede valori predefiniti e non può essere lasciato vuoto. Per individuare questo valore nel portale di Azure, cercare e selezionare "Utenti" in _Servizi_. Usare la casella _Nome_ per filtrare per il nome dell'account, quindi selezionarlo. Nella pagina _Profilo utente_ copiare il valore di _Nome utente_.|
|Gruppo di risorse Ambiente del servizio app|Resource group|NOME|**Bloccato**: concatena il **nome dell'organizzazione** con `-workload-ase-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse Ambiente del servizio app|Resource group|Location|**Bloccato**: usa il parametro del progetto.|
|Modello Ambiente del servizio app|Modello di Resource Manager|Nome di dominio|Il nome dell'istanza di Active Directory creata dall'esempio. Il valore predefinito è _contoso.com_.|
|Modello Ambiente del servizio app|Modello di Resource Manager|Località dell'ambiente del servizio app|La località dell'ambiente del servizio app. Il valore predefinito è _Stati Uniti occidentali 2_.|
|Modello Ambiente del servizio app|Modello di Resource Manager|Conservazione dei log del gateway applicazione in giorni|Il periodo di conservazione dei dati in giorni. Il valore predefinito è _365_.|

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato la procedura per distribuire l'esempio di progetto Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001, vedere gli articoli seguenti per informazioni sull'architettura e sul mapping dei controlli:

> [!div class="nextstepaction"]
> [Progetto Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001 - Panoramica](./index.md)
> [Progetto Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001 - Mapping dei controlli](./control-mapping.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).