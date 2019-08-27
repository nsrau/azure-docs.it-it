---
title: Configurare la propria chiave per la crittografia dei dati inattivi di hub eventi di Azure
description: Questo articolo fornisce informazioni su come configurare la propria chiave per la crittografia dei dati REST di hub eventi di Azure.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: spelluru
ms.openlocfilehash: 311f69ffa436eebb261fb8aa5ee72886ad9fe9d0
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035901"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Configurare chiavi gestite dal cliente per la crittografia dei dati inattivi di hub eventi di Azure usando il portale di Azure
Hub eventi di Azure fornisce la crittografia dei dati inattivi con crittografia del servizio di archiviazione di Azure (SSE di Azure). Hub eventi si basa su archiviazione di Azure per archiviare i dati e, per impostazione predefinita, tutti i dati archiviati con archiviazione di Azure vengono crittografati usando le chiavi gestite da Microsoft. 

## <a name="overview"></a>Panoramica
Hub eventi di Azure ora supporta l'opzione di crittografia dei dati inattivi con chiavi gestite da Microsoft o chiavi gestite dal cliente (Bring Your Own Key – BYOK). Questa funzionalità consente di creare, ruotare, disabilitare e revocare l'accesso alle chiavi gestite dal cliente usate per la crittografia dei dati inattivi di hub eventi di Azure.

L'abilitazione della funzionalità BYOK è un processo di configurazione una volta nello spazio dei nomi.

> [!NOTE]
> La funzionalità BYOK è supportata dai cluster a [tenant singolo dedicati di hub eventi](event-hubs-dedicated-overview.md) . Non può essere abilitata per gli spazi dei nomi standard di hub eventi.

È possibile usare Azure Key Vault per gestire le chiavi e controllare l'utilizzo della chiave. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi. Per altre informazioni sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](../key-vault/key-vault-overview.md)

Questo articolo illustra come configurare un insieme di credenziali delle chiavi con chiavi gestite dal cliente usando il portale di Azure. Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere [] Guida introduttiva: Impostare e recuperare un segreto da Azure Key Vault usando il portale di Azure] (.. /key-vault/quick-create-portal.md).

> [!IMPORTANT]
> Per usare chiavi gestite dal cliente con hub eventi di Azure, è necessario che nell'insieme di credenziali delle chiavi siano configurate due proprietà obbligatorie. Sono:  **Eliminazione** temporanea e **non**ripulitura. Queste proprietà sono abilitate per impostazione predefinita quando si crea un nuovo insieme di credenziali delle chiavi nel portale di Azure. Tuttavia, se è necessario abilitare queste proprietà in un insieme di credenziali delle chiavi esistente, è necessario usare PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="enable-customer-managed-keys"></a>Abilita chiavi gestite dal cliente
Per abilitare le chiavi gestite dal cliente nel portale di Azure, attenersi alla procedura seguente:

1. Passare al cluster Hub eventi Dedicato.
1. Selezionare lo spazio dei nomi in cui si vuole abilitare BYOK.
1. Nella pagina **Impostazioni** dello spazio dei nomi di hub eventi selezionare **crittografia (anteprima)** . 
1. Selezionare la **crittografia della chiave gestita dal cliente** , come illustrato nella figura seguente. 

    ![Abilita chiave gestita dal cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Configurare un insieme di credenziali delle chiavi con chiavi
Dopo aver abilitato le chiavi gestite dal cliente, è necessario associare la chiave gestita dal cliente allo spazio dei nomi di hub eventi di Azure. Hub eventi supporta solo Azure Key Vault. Se si Abilita l'opzione **crittografia con chiave gestita dal cliente** nella sezione precedente, è necessario importare la chiave in Azure Key Vault. Inoltre, le chiavi devono essere **eliminate temporaneamente** e **non vengono** rieliminate configurate per la chiave. Queste impostazioni possono essere configurate tramite [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) o l' [interfaccia](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)della riga di comando.

1. Per creare un nuovo insieme di credenziali delle chiavi, seguire la [Guida introduttiva](../key-vault/key-vault-overview.md)Azure Key Vault. Per ulteriori informazioni sull'importazione di chiavi esistenti, vedere [informazioni su chiavi, segreti e certificati](../key-vault/about-keys-secrets-and-certificates.md).
1. Per attivare l'eliminazione temporanea e ripulire la protezione durante la creazione di un insieme di credenziali, usare il comando [AZ per Vault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) .

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Per aggiungere la protezione di ripulitura a un insieme di credenziali esistente (in cui è già abilitata l'eliminazione temporanea), usare il comando [AZ di Vault Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Creare le chiavi attenendosi alla procedura seguente:
    1. Per creare una nuova chiave, selezionare **Genera/Importa** nel menu **Chiavi** in **Impostazioni**.
        
        ![Pulsante Seleziona genera/importa](./media/configure-customer-managed-key/select-generate-import.png)
    1. Impostare **Opzioni** su **Genera** e assegnare un nome alla chiave.

        ![Creare una chiave](./media/configure-customer-managed-key/create-key.png) 
    1. È ora possibile selezionare questa chiave da associare allo spazio dei nomi di hub eventi per la crittografia dall'elenco a discesa. 

        ![Selezionare la chiave da Key Vault](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Inserire i dettagli per la chiave e fare clic su **Seleziona**. In questo modo verrà abilitata la crittografia dei dati inattivi nello spazio dei nomi con una chiave gestita dal cliente. 

        > [!NOTE]
        > Per la fase di anteprima, è possibile selezionare solo una chiave. 

## <a name="rotate-your-encryption-keys"></a>Ruotare le chiavi di crittografia
È possibile ruotare la chiave nell'insieme di credenziali delle chiavi usando il meccanismo di rotazione di Azure Key Vault. Per altre informazioni, vedere [configurare la rotazione e il controllo delle chiavi](../key-vault/key-vault-key-rotation-log-monitoring.md). È anche possibile impostare le date di attivazione e di scadenza per automatizzare la rotazione delle chiavi. Il servizio Hub eventi rileverà nuove versioni chiave e inizierà a utilizzarle automaticamente.

## <a name="revoke-access-to-keys"></a>Revoca l'accesso alle chiavi
La revoca dell'accesso alle chiavi di crittografia non eliminerà i dati da Hub eventi. Tuttavia, non è possibile accedere ai dati dallo spazio dei nomi di hub eventi. Per revocare la chiave di crittografia, è possibile usare i criteri di accesso oppure eliminare la chiave. Altre informazioni sui criteri di accesso e sulla protezione dell'insieme di credenziali delle chiavi dall' [accesso sicuro a un insieme di](../key-vault/key-vault-secure-your-key-vault.md)credenziali delle chiavi.

Una volta revocata la chiave di crittografia, il servizio Hub eventi nello spazio dei nomi crittografato diventerà inutilizzabile. Se l'accesso alla chiave è abilitato o il tasto CANC viene ripristinato, il servizio Hub eventi selezionerà la chiave in modo che sia possibile accedere ai dati dallo spazio dei nomi di hub eventi crittografati.

> [!NOTE]
> Se si elimina una chiave di crittografia esistente dall'insieme di credenziali delle chiavi e la si sostituisce con una nuova chiave nello spazio dei nomi di hub eventi, poiché la chiave di eliminazione è ancora valida (così come è memorizzata nella cache) per un massimo di un'ora, i dati obsoleti (crittografati con la chiave precedente) potrebbero ancora essere accessibili insieme  con i nuovi dati, che ora è accessibile solo tramite la nuova chiave. Questo comportamento è progettato nella versione di anteprima della funzionalità. 

## <a name="set-up-diagnostic-logs"></a>Configurare i log di diagnostica 
L'impostazione di log di diagnostica per gli spazi dei nomi abilitati per BYOK fornisce le informazioni necessarie sulle operazioni quando uno spazio dei nomi è crittografato con chiavi gestite dal cliente. Questi log possono essere abilitati e successivamente trasmessi a un hub eventi o analizzati tramite log Analytics o trasmessi all'archiviazione per eseguire analisi personalizzate. Per altre informazioni sui log di diagnostica, vedere [Panoramica dei log di diagnostica di Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="enable-user-logs"></a>Abilitare i log utente
Seguire questa procedura per abilitare i log per le chiavi gestite dal cliente.

1. Nella portale di Azure passare allo spazio dei nomi in cui è abilitato BYOK.
1. Selezionare **impostazioni di diagnostica** in **monitoraggio**.

    ![Selezionare le impostazioni di diagnostica](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Selezionare **+ Aggiungi impostazioni di diagnostica**. 

    ![Selezionare Aggiungi impostazioni di diagnostica](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Specificare un **nome** e selezionare la posizione in cui si desidera trasmettere i log.
1. Selezionare **CustomerManagedKeyUserLogs** e **Salva**. Questa azione Abilita i registri per BYOK nello spazio dei nomi.

    ![Selezionare l'opzione log utente chiave gestita dal cliente](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Schema del log 
Tutti i log vengono archiviati in formato JavaScript Object Notation (JSON). Ogni voce contiene campi stringa che usano il formato descritto nella tabella seguente. 

| NOME | DESCRIZIONE |
| ---- | ----------- | 
| TaskName | Descrizione dell'attività non riuscita. |
| ActivityId | ID interno usato per il rilevamento. |
| category | Definisce la classificazione dell'attività. Se, ad esempio, la chiave dell'insieme di credenziali delle chiavi è disabilitata, sarà una categoria di informazioni o se non è possibile decrittografare una chiave, potrebbe rientrare in errore. |
| resourceId | ID della risorsa Azure Resource Manager |
| keyVault | Nome completo dell'insieme di credenziali delle chiavi. |
| key | Nome della chiave usato per crittografare lo spazio dei nomi di hub eventi. |
| version | Versione della chiave usata. |
| operation | Operazione eseguita sulla chiave nell'insieme di credenziali delle chiavi. Ad esempio, disabilitare/abilitare la chiave, eseguire il wrapping o annullare il wrapping |
| code | Codice associato all'operazione. Esempio: Il codice di errore 404 indica che la chiave non è stata trovata. |
| message | Qualsiasi messaggio di errore associato all'operazione |

Di seguito è riportato un esempio di log per una chiave gestita dal cliente:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="troubleshoot"></a>Risolvere problemi
Come procedura consigliata, abilitare sempre i log, come illustrato nella sezione precedente. Consente di tenere traccia delle attività quando è abilitata la crittografia BYOK. Consente inoltre di rientrare nell'ambito dei problemi.

Di seguito sono riportati i codici di errore comuni da cercare quando è abilitata la crittografia BYOK.

| Azione | Codice di errore | Stato risultante dei dati |
| ------ | ---------- | ----------------------- | 
| Rimuovere l'autorizzazione wrap/unwrap da un insieme di credenziali delle chiavi | 403 |    Inaccessibile |
| Rimuovere l'appartenenza al ruolo AAD da un'entità AAD che ha concesso l'autorizzazione wrap/unwrap | 403 |  Inaccessibile |
| Eliminare una chiave di crittografia dall'insieme di credenziali delle chiavi | 404 | Inaccessibile |
| Eliminare l'insieme di credenziali delle chiavi | 404 | Inaccessibile (si presuppone che l'eliminazione temporanea sia abilitata, che è un'impostazione obbligatoria). |
| Modificare il periodo di scadenza della chiave di crittografia in modo che sia già scaduto | 403 |   Inaccessibile  |
| Modifica del NBF (non prima) in modo tale che la chiave di crittografia della chiave non sia attiva | 403 | Inaccessibile  |
| Selezionare l'opzione **Consenti servizi MSFT** per il firewall dell'insieme di credenziali delle chiavi o bloccare l'accesso di rete all'insieme di credenziali delle chiavi con la chiave di crittografia | 403 | Inaccessibile |
| Trasferimento dell'insieme di credenziali delle chiavi in un tenant diverso | 404 | Inaccessibile |  
| Problemi di rete intermittenti o interruzione di DNS/AAD/MSI |  | Accessibile tramite la chiave di crittografia dei dati memorizzata nella cache |

> [!IMPORTANT]
> Per abilitare il ripristino di emergenza geografico in uno spazio dei nomi che usa la crittografia BYOK, lo spazio dei nomi secondario per l'associazione deve trovarsi in un cluster dedicato ed è necessario che sia abilitata un'identità gestita assegnata dal sistema. Per altre informazioni, vedere [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

> [!NOTE]
> Se gli endpoint di servizio della rete virtuale (VNet) sono configurati in Azure Key Vault per lo spazio dei nomi di hub eventi, BYOK non sarà supportato. 


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:
- [Panoramica di Hub eventi](event-hubs-about.md)
- [Panoramica di Key Vault](../key-vault/key-vault-overview.md)




