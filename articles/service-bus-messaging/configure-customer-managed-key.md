---
title: Configurare la propria chiave per la crittografia dei dati del bus di servizio di Azure inattivi
description: Questo articolo fornisce informazioni su come configurare una chiave personalizzata per la crittografia di REST di dati del bus di servizio di Azure.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: aschhab
ms.openlocfilehash: 356f825524192c3b6cf7df7f0460975f23ea4f7c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852289"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal-preview"></a>Configurare chiavi gestite dal cliente per la crittografia dei dati del bus di servizio di Azure inattivi usando il portale di Azure (anteprima)
Azure Service Bus Premium offre la crittografia dei dati inattivi con Azure crittografia del servizio di archiviazione (SSE di Azure). Il bus di servizio Premium si basa su archiviazione di Azure per archiviare i dati e, per impostazione predefinita, tutti i dati archiviati con archiviazione di Azure vengono crittografati con le chiavi gestite da Microsoft. 

## <a name="overview"></a>Panoramica
Il bus di servizio di Azure ora supporta l'opzione di crittografia dei dati inattivi con chiavi gestite da Microsoft o chiavi gestite dal cliente (Bring Your Own Key-BYOK). Questa funzionalità consente di creare, ruotare, disabilitare e revocare l'accesso alle chiavi gestite dal cliente usate per la crittografia del bus di servizio di Azure.

L'abilitazione della funzionalità BYOK è un processo di configurazione una volta nello spazio dei nomi.

> [!NOTE]
> Ci sono alcune avvertenze per la chiave gestita dal cliente per la crittografia lato servizio. 
>   * Questa funzionalità è supportata dal livello [Premium del bus di servizio di Azure](service-bus-premium-messaging.md) . Non può essere abilitata per gli spazi dei nomi del bus di servizio di livello standard.
>   * La crittografia può essere abilitata solo per gli spazi dei nomi nuovi o vuoti. Se lo spazio dei nomi contiene dati, l'operazione di crittografia avrà esito negativo.
>   * Se gli [endpoint di servizio della rete virtuale (VNet)](service-bus-service-endpoints.md) sono configurati in Azure Key Vault per lo spazio dei nomi del bus di servizio, BYOK non sarà supportato. 

È possibile usare Azure Key Vault per gestire le chiavi e controllare l'utilizzo della chiave. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi. Per altre informazioni sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](../key-vault/key-vault-overview.md)

Questo articolo illustra come configurare un insieme di credenziali delle chiavi con chiavi gestite dal cliente usando il portale di Azure. Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere [Guida introduttiva: impostare e recuperare un segreto da Azure Key Vault tramite il portale di Azure](../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> L'uso delle chiavi gestite dal cliente con il bus di servizio di Azure richiede che nell'insieme di credenziali delle chiavi siano configurate due proprietà obbligatorie. Sono: **eliminazione** temporanea e **non ripulitura**. Queste proprietà sono abilitate per impostazione predefinita quando si crea un nuovo insieme di credenziali delle chiavi nel portale di Azure. Tuttavia, se è necessario abilitare queste proprietà in un insieme di credenziali delle chiavi esistente, è necessario usare PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="enable-customer-managed-keys"></a>Abilita chiavi gestite dal cliente
Per abilitare le chiavi gestite dal cliente nel portale di Azure, attenersi alla procedura seguente:

1. Passare allo spazio dei nomi premium del bus di servizio.
2. Nella pagina **Impostazioni** dello spazio dei nomi del bus di servizio selezionare **crittografia (anteprima)** .
3. Selezionare la **crittografia della chiave gestita dal cliente** , come illustrato nella figura seguente.

    ![Abilita chiave gestita dal cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Configurare un insieme di credenziali delle chiavi con chiavi

Dopo aver abilitato le chiavi gestite dal cliente, è necessario associare la chiave gestita dal cliente allo spazio dei nomi del bus di servizio di Azure. Il bus di servizio supporta solo Azure Key Vault. Se si Abilita l'opzione **crittografia con chiave gestita dal cliente** nella sezione precedente, è necessario importare la chiave in Azure Key Vault. Inoltre, le chiavi devono essere **eliminate temporaneamente** e **non vengono rieliminate** configurate per la chiave. Queste impostazioni possono essere configurate tramite [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) o l' [interfaccia](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)della riga di comando.

1. Per creare un nuovo insieme di credenziali delle chiavi, seguire la [Guida introduttiva](../key-vault/key-vault-overview.md)Azure Key Vault. Per ulteriori informazioni sull'importazione di chiavi esistenti, vedere [informazioni su chiavi, segreti e certificati](../key-vault/about-keys-secrets-and-certificates.md).
1. Per attivare l'eliminazione temporanea e ripulire la protezione durante la creazione di un insieme di credenziali, usare il comando [AZ per Vault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) .

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Per aggiungere la protezione di ripulitura a un insieme di credenziali esistente (in cui è già abilitata l'eliminazione temporanea), usare il comando [AZ di Vault Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Creare le chiavi attenendosi alla procedura seguente:
    1. Per creare una nuova chiave, selezionare **Genera/Importa** nel menu **Chiavi** in **Impostazioni**.
        
        ![Pulsante Seleziona genera/importa](./media/configure-customer-managed-key/select-generate-import.png)

    1. Impostare **Opzioni** su **Genera** e assegnare un nome alla chiave.

        ![Creare una chiave](./media/configure-customer-managed-key/create-key.png) 

    1. È ora possibile selezionare questa chiave da associare allo spazio dei nomi del bus di servizio per la crittografia dall'elenco a discesa. 

        ![Selezionare la chiave da Key Vault](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Per la ridondanza, è possibile aggiungere fino a tre chiavi. Se una delle chiavi è scaduta o non è accessibile, verranno usate le altre chiavi per la crittografia.
        
    1. Inserire i dettagli per la chiave e fare clic su **Seleziona**. In questo modo verrà abilitata la crittografia dei dati inattivi nello spazio dei nomi con una chiave gestita dal cliente. 


> [!IMPORTANT]
> Se si sta cercando di usare la chiave gestita dal cliente insieme al ripristino di emergenza geografico, vedere le 
>
> Per abilitare la crittografia dei file inattivi con la chiave gestita dal cliente, viene configurato un [criterio di accesso](../key-vault/key-vault-secure-your-key-vault.md) per l'identità gestita del bus di servizio nell'insieme di credenziali delle chiavi di Azure specificato. Ciò garantisce l'accesso controllato all'insieme di credenziali delle credenziali di Azure dallo spazio dei nomi del bus di servizio di Azure.
>
> A causa di questa operazione:
> 
>   * Se il [ripristino di emergenza geografico](service-bus-geo-dr.md) è già abilitato per lo spazio dei nomi del bus di servizio e si sta cercando di abilitare la chiave gestita dal cliente, 
>     * Interrompi l'associazione
>     * [Configurare i criteri di accesso](../key-vault/managed-identity.md) per l'identità gestita per gli spazi dei nomi primario e secondario nell'insieme di credenziali delle chiavi.
>     * Configurare la crittografia nello spazio dei nomi primario.
>     * Associare nuovamente gli spazi dei nomi primari e secondari.
> 
>   * Se si vuole abilitare il ripristino di emergenza geografico in uno spazio dei nomi del bus di servizio in cui è già configurata la chiave gestita dal cliente,
>     * [Configurare i criteri di accesso](../key-vault/managed-identity.md) per l'identità gestita per lo spazio dei nomi secondario nell'insieme di credenziali delle chiavi.
>     * Associare gli spazi dei nomi primari e secondari.


## <a name="rotate-your-encryption-keys"></a>Ruotare le chiavi di crittografia

È possibile ruotare la chiave nell'insieme di credenziali delle chiavi usando il meccanismo di rotazione di Azure Key Vault. Per altre informazioni, vedere [configurare la rotazione e il controllo delle chiavi](../key-vault/key-vault-key-rotation-log-monitoring.md). È anche possibile impostare le date di attivazione e di scadenza per automatizzare la rotazione delle chiavi. Il servizio del bus di servizio rileverà le nuove versioni chiave e inizierà a utilizzarle automaticamente.

## <a name="revoke-access-to-keys"></a>Revoca l'accesso alle chiavi

La revoca dell'accesso alle chiavi di crittografia non eliminerà i dati dal bus di servizio. Tuttavia, non è possibile accedere ai dati dallo spazio dei nomi del bus di servizio. Per revocare la chiave di crittografia, è possibile usare i criteri di accesso oppure eliminare la chiave. Altre informazioni sui criteri di accesso e sulla protezione dell'insieme di credenziali delle chiavi dall' [accesso sicuro a un insieme di](../key-vault/key-vault-secure-your-key-vault.md)credenziali delle chiavi.

Una volta revocata la chiave di crittografia, il servizio del bus di servizio sullo spazio dei nomi crittografato diventerà inutilizzabile. Se l'accesso alla chiave è abilitato o la chiave eliminata viene ripristinata, il servizio del bus di servizio sceglierà la chiave in modo che sia possibile accedere ai dati dallo spazio dei nomi del bus di servizio crittografato.

> [!NOTE]
> Se si elimina una chiave di crittografia esistente dall'insieme di credenziali delle chiavi e la si sostituisce con una nuova chiave nello spazio dei nomi del bus di servizio, poiché la chiave di eliminazione è ancora valida (mentre è memorizzata nella cache) per un massimo di un'ora, i dati obsoleti (crittografati con la chiave precedente) potrebbero ancora essere accessibili a Alon g con i nuovi dati, ora accessibili solo tramite la nuova chiave. Questo comportamento è progettato nella versione di anteprima della funzionalità. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:
- [Panoramica del bus di servizio](service-bus-messaging-overview.md)
- [Panoramica di Key Vault](../key-vault/key-vault-overview.md)


