---
title: Esempio - progetto per il carico di lavoro ISO 27001 ambiente del servizio App/SQL - distribuzione passaggi
description: Distribuire i passaggi dell'esempio di progetto ISO 27001 App servizio ambiente/Database SQL del carico di lavoro.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4ed51ee5f8cbdc50fa65a189d8f468bd7713a74b
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804157"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Distribuire l'esempio di progetto ISO 27001 App servizio ambiente/Database SQL del carico di lavoro

Per distribuire l'esempio di progetto Azure progetti ISO 27001 App ambiente/Database SQL del servizio del carico di lavoro, è necessario effettuare i passaggi seguenti:

> [!div class="checklist"]
> - Distribuire il [ISO 27001 Shared Services](../iso27001-shared/index.md) esempio blueprint
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Distribuire l'esempio di progetto di servizi condivisi di ISO 27001

Prima di poter distribuire in questo esempio di progetto, il [ISO 27001 Shared Services](../iso27001-shared/index.md) esempio di progetto deve essere distribuito per la sottoscrizione di destinazione. Senza una corretta distribuzione dell'esempio di progetto di servizi condivisi di ISO 27001, non sarà presente in questo esempio di progetto dipendenze dell'infrastruttura e non riuscire durante la distribuzione.

> [!IMPORTANT]
> In questo esempio di progetto deve essere assegnato nella stessa sottoscrizione di [ISO 27001 Shared Services](../iso27001-shared/index.md) esempio di progetto.

## <a name="create-blueprint-from-sample"></a>Crea progetto dall'esempio

Innanzitutto, implementare l'esempio di progetto mediante la creazione di un nuovo progetto nel proprio ambiente usando l'esempio come un avvio.

1. Selezionare **tutti i servizi** e cercare e selezionare **criterio** nel riquadro sinistro. Nel **criterio** pagina, selezionare **progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare il **ISO 27001: Carico di lavoro SQL/ambiente del servizio app** esempio di progetto in _altri esempi_ e selezionare **usare questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: Specificare un nome per la copia dell'esempio di progetto ISO 27001 ambiente del servizio App/SQL del carico di lavoro.
   - **Posizione della definizione**: Usare i puntini di sospensione e selezionare il gruppo di gestione per salvare la copia dell'esempio a.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti che costituiscono l'esempio di progetto. Molti degli elementi di parametri che verranno definiti in un secondo momento. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

## <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata con l'ambiente e alle esigenze, ma tale modifica potrebbe allontanano lo standard ISO 27001.

1. Selezionare **tutti i servizi** e cercare e selezionare **criterio** nel riquadro sinistro. Nel **criterio** pagina, selezionare **progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarlo.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra, specificare una **versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. Fornire **modificare note** , ad esempio "prima versione pubblicato dall'esempio ISO 27001 blueprint". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

## <a name="assign-the-sample-copy"></a>Assegnare la copia dell'esempio

Dopo che la copia dell'esempio di progetto è stata **pubblicata** correttamente, è possibile assegnarla a una sottoscrizione all'interno del gruppo di gestione in cui è stata salvata. In questo passaggio vengono specificati i parametri che rendono univoca ogni distribuzione della copia dell'esempio di progetto.

1. Selezionare **tutti i servizi** e cercare e selezionare **criterio** nel riquadro sinistro. Nel **criterio** pagina, selezionare **progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarlo.

1. Selezionare **Assegna progetto** nella parte superiore della pagina della definizione di progetto.

1. Specificare i valori dei parametri per l'assegnazione del progetto:

   - Nozioni di base

     - **Sottoscrizioni**: selezionare una o più sottoscrizioni presenti nel gruppo di gestione in cui è stata salvata la copia dell'esempio di progetto. Se si selezionano più sottoscrizioni, viene creata un'assegnazione per ciascuna usando i parametri immessi.
     - **Nome dell'assegnazione**: Il nome è già popolato automaticamente in base al nome del progetto.
       Modificare in base alle esigenze o lasciare invariato.
     - **Località**: selezionare un'area in cui creare l'identità gestita. Azure Blueprint usa questa identità gestita per distribuire tutti gli elementi nel progetto assegnato. Per altre informazioni, vedere [Managed identities for Azure resources](../../../../active-directory/managed-identities-azure-resources/overview.md) (Identità gestite per risorse di Azure).
     - **Versione della definizione di progetto**: Selezionare una **Published** versione di una copia dell'esempio di progetto.

   - Blocca assegnazione

     Selezionare il blocco di progetto l'impostazione per l'ambiente. Per altre informazioni, vedere [Blueprints resource locking](../../concepts/resource-locking.md) (Blocco delle risorse del progetto).

   - Identità gestita

     Lasciare l'impostazione predefinita _assegnata dal sistema_ gestiti opzione identity.

   - Parametri di progetto

     I parametri definiti in questa sezione vengono usati da molti degli elementi nella definizione di progetto per fornire coerenza.

     - **Nome dell'organizzazione**: Immettere un nome breve per l'organizzazione. Questa proprietà viene utilizzata principalmente per la denominazione delle risorse.
     - **ID sottoscrizione del servizio condiviso**: ID sottoscrizione in cui il [ISO 27001 Shared Services](../iso27001-shared/index.md) blueprint campione viene assegnato.
     - **Prefisso indirizzo della subnet predefinita**: La notazione CIDR per la subnet di rete virtuale predefinita.
       Valore predefinito è _10.1.0.0/16_.
     - **Posizione del carico di lavoro**: Determina quali posizione vengono distribuiti gli elementi. Non tutti i servizi sono disponibili in tutte le posizioni. Elementi di distribuzione di tali servizi offrono un'opzione di parametro per il percorso in cui distribuire questo artefatto.

   - Parametri dell'elemento

     I parametri definiti in questa sezione si applicano all'artefatto in cui sono definiti. Si tratta di [parametri dinamici](../../concepts/parameters.md#dynamic-parameters), in quanto vengono definiti durante l'assegnazione del progetto. Per un elenco completo o parametri dell'elemento e le relative descrizioni, vedere [tabella di parametri dell'elemento](#artifact-parameters-table).

1. Dopo avere immesso tutti i parametri, selezionare **Assegna** in fondo alla pagina. Viene creato l'assegnazione di progetto e dell'avvio della distribuzione dell'elemento. Distribuzione richiede circa un'ora. Per controllare lo stato della distribuzione, aprire l'assegnazione di progetto.

> [!WARNING]
> Il servizio di progetti di architetture di Azure e gli esempi di progetto predefinite siano **gratuito**. Risorse di Azure sono [i prezzi dipendono dal prodotto](https://azure.microsoft.com/pricing/). Usare la [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) per stimare il costo delle risorse distribuite da questo esempio di progetto.

## <a name="artifact-parameters-table"></a>Tabella di parametri dell'elemento

Nella tabella seguente fornisce un elenco del progetto di parametri dell'elemento:

|Nome dell'artefatto|Tipo di artefatto|Nome parametro|DESCRIZIONE|
|-|-|-|-|
|Gruppo di risorse di log Analitica|Gruppo di risorse|NOME|**Bloccato** -consente di concatenare il **nome organizzazione** con `-workload-log-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse di log Analitica|Gruppo di risorse|Località|**Bloccato** -Usa il parametro del progetto.|
|Modello Log Analytics|Modello di Resource Manager|Livello di servizio|Imposta il livello di area di lavoro di Log Analitica. Valore predefinito è _PerNode_.|
|Modello Log Analytics|Modello di Resource Manager|Conservazione log in giorni|Conservazione dei dati in giorni. Valore predefinito è _365_.|
|Modello Log Analytics|Modello di Resource Manager|Località|Area usata per creare l'area di lavoro di Log Analitica. Valore predefinito è _Stati Uniti occidentali 2_.|
|Gruppo di risorse di rete|Gruppo di risorse|NOME|**Bloccato** -consente di concatenare il **nome organizzazione** con `-workload-net-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse di rete|Gruppo di risorse|Località|**Bloccato** -Usa il parametro del progetto.|
|Modello Gruppi di sicurezza di rete|Modello di Resource Manager|Conservazione log in giorni|Conservazione dei dati in giorni. Valore predefinito è _365_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Indirizzo IP privato di Firewall di Azure|Consente di configurare l'indirizzo IP privato del [firewall Azure](../../../../firewall/overview.md). Deve essere parte della notazione CIDR definita in _ISO 27001: Servizi condivisi_ parametro artefatto **prefisso indirizzo della subnet Firewall Azure**. Valore predefinito è _10.0.4.4_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|ID sottoscrizione servizi condivisi|Valore usato per abilitare il peering tra un carico di lavoro e i servizi condivisi della rete virtuale.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Prefisso dell'indirizzo della rete virtuale|La notazione CIDR per la rete virtuale. Valore predefinito è _10.1.0.0/16_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Prefisso dell'indirizzo della subnet predefinito|La notazione CIDR per la subnet di rete virtuale predefinita. Valore predefinito è _10.1.0.0/16_.|
|Modello Rete virtuale e tabella di route|Modello di Resource Manager|Indirizzo IP di ADDS|Indirizzo IP della prima VM aggiunge. Questo valore viene usato come DNS VNET personalizzato.|
|Gruppo di risorse dell'insieme di credenziali delle chiavi|Gruppo di risorse|NOME|**Bloccato** -consente di concatenare il **nome organizzazione** con `-workload-kv-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse dell'insieme di credenziali delle chiavi|Gruppo di risorse|Località|**Bloccato** -Usa il parametro del progetto.|
|Modello Key Vault|Modello di Resource Manager|ID oggetto AAD|L'identificatore di oggetto AAD dell'account che richiede l'accesso all'istanza di Key Vault. Nessuna impostazione predefinita il valore e non può essere vuoto. Per individuare questo valore dal portale di Azure, cercare e selezionare "Utenti" nella _Services_. Usare la _nome_ casella per filtrare il nome dell'account e selezionare tale account. Nel _profilo utente_ pagina, selezionare l'icona "Fare clic per copiare" accanto al _ID oggetto_.|
|Modello Key Vault|Modello di Resource Manager|Conservazione log in giorni|Conservazione dei dati in giorni. Valore predefinito è _365_.|
|Modello Key Vault|Modello di Resource Manager|SKU Key Vault|Specifica lo SKU dell'insieme di credenziali chiave che viene creato. Valore predefinito è _Premium_.|
|Modello Key Vault|Modello di Resource Manager|Nome utente amministratore del server SQL di Azure|Il nome utente usato per accedere ai Server SQL di Azure. Deve corrispondere stesso valore della proprietà in **modello di Database SQL di Azure**. Valore predefinito è _sql-admin-user_.|
|Gruppo di risorse Azure SQL Database|Gruppo di risorse|NOME|**Bloccato** -consente di concatenare il **nome organizzazione** con `-workload-azsql-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse Azure SQL Database|Gruppo di risorse|Località|**Bloccato** -Usa il parametro del progetto.|
|Modello Database SQL di Azure|Modello di Resource Manager|Nome utente amministratore del server SQL di Azure|Nome utente per il Server SQL di Azure. Deve corrispondere stesso valore della proprietà in **modello di Key Vault**. Valore predefinito è _sql-admin-user_.|
|Modello Database SQL di Azure|Modello di Resource Manager|Password di amministratore Server SQL di Azure (ID di risorsa dell'insieme di credenziali chiave)|L'ID risorsa dell'insieme di credenziali chiave. Usare "/ subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" e sostituire `{subscriptionId}` con l'ID sottoscrizione e `{orgName}` con il  **Nome dell'organizzazione** parametro del progetto.|
|Modello Database SQL di Azure|Modello di Resource Manager|Password amministratore di Azure SQL Server (nome del segreto dell'insieme di credenziali chiave)|Nome utente SQL amministratore del Server. Deve corrispondere a valore in **modello di Key Vault** proprietà **nome utente amministratore di Server SQL di Azure**.|
|Modello Database SQL di Azure|Modello di Resource Manager|Conservazione log in giorni|Conservazione dei dati in giorni. Valore predefinito è _365_.|
|Modello Database SQL di Azure|Modello di Resource Manager|ID di oggetto di amministratore AAD|ID oggetto AAD dell'utente che verrà assegnata come un amministratore di Active Directory. Nessuna impostazione predefinita il valore e non può essere vuoto. Per individuare questo valore dal portale di Azure, cercare e selezionare "Utenti" nella _Services_. Usare la _nome_ casella per filtrare il nome dell'account e selezionare tale account. Nel _profilo utente_ pagina, selezionare l'icona "Fare clic per copiare" accanto al _ID oggetto_.|
|Modello Database SQL di Azure|Modello di Resource Manager|Account di accesso amministratore AAD|Attualmente, gli account Microsoft (ad esempio live.com o outlook.com) non possono essere impostati come amministratore. Solo gli utenti e gruppi di sicurezza all'interno dell'organizzazione possono essere impostati come amministratore. Nessuna impostazione predefinita il valore e non può essere vuoto. Per individuare questo valore dal portale di Azure, cercare e selezionare "Utenti" nella _Services_. Usare la _nome_ casella per filtrare il nome dell'account e selezionare tale account. Nel _profilo utente_ pagina, copiare la _nome utente_.|
|Gruppo di risorse di ambiente del servizio App|Gruppo di risorse|NOME|**Bloccato** -consente di concatenare il **nome organizzazione** con `-workload-ase-rg` per rendere univoco il gruppo di risorse.|
|Gruppo di risorse di ambiente del servizio App|Gruppo di risorse|Località|**Bloccato** -Usa il parametro del progetto.|
|Modello Ambiente del servizio app|Modello di Resource Manager|Nome di dominio|Nome di Active Directory creato dall'esempio. Valore predefinito è _contoso.com_.|
|Modello Ambiente del servizio app|Modello di Resource Manager|Percorso di ambiente del servizio App|Percorso dell'ambiente del servizio app. Valore predefinito è _Stati Uniti occidentali 2_.|
|Modello Ambiente del servizio app|Modello di Resource Manager|Conservazione dei log del gateway applicazione in giorni|Conservazione dei dati in giorni. Valore predefinito è _365_.|

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato i passaggi per distribuire l'esempio di progetto del carico di lavoro di ISO 27001 App servizio ambiente/Database SQL, vedere gli articoli seguenti per informazioni sull'architettura e mapping di controllo:

> [!div class="nextstepaction"]
> [Progetto del carico di lavoro per il Database SQL/ambiente del servizio di ISO 27001 App - Panoramica](./index.md)
> [progetto carico di lavoro per il Database SQL/ambiente del servizio di ISO 27001 App - mapping di controllo](./control-mapping.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).