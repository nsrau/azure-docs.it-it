---
title: Usare Key Vault per archiviare e accedere alle chiavi di Azure Cosmos DB
description: Usare Azure Key Vault per archiviare e accedere alla stringa di connessione, alle chiavi e agli endpoint di Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 50a9d5e3d3bbb608160ee160c5f1aede8f70e530
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85262668"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Proteggere le chiavi di Azure Cosmos con Azure Key Vault 

>[!IMPORTANT]
> La soluzione consigliata per accedere alle chiavi Azure Cosmos DB consiste nell'usare un' [identità gestita assegnata dal sistema](managed-identity-based-authentication.md). Se il servizio non è in grado di sfruttare le identità gestite, usare la [soluzione basata su certificati](certificate-based-authentication.md). Se la soluzione di identità gestita e la soluzione basata su certificato non soddisfano le proprie esigenze, usare la soluzione Key Vault riportata di seguito.

Quando si usa Azure Cosmos DB per le applicazioni, è possibile accedere al database, alle raccolte e ai documenti usando l'endpoint e la chiave nel file di configurazione dell'app.  Tuttavia, non è sicuro inserire chiavi e URL direttamente nel codice dell'applicazione perché sono disponibili in formato testo non crittografato per tutti gli utenti. È necessario però assicurarsi che l'endpoint e le chiavi siano disponibili attraverso un meccanismo protetto. Con Azure Key Vault è possibile archiviare e gestire i segreti delle applicazioni in modo sicuro.

Per archiviare e leggere le chiavi di accesso di Azure Cosmos DB da Key Vault sono necessari i passaggi seguenti:

* Creare un insieme di credenziali delle chiavi  
* Aggiungere chiavi di accesso di Azure Cosmos DB all'insieme di credenziali delle chiavi  
* Creare un'applicazione Web di Azure  
* Registrare l'applicazione e concedere le autorizzazioni per leggere l'insieme di credenziali delle chiavi  


## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

1. Accedere al [portale di Azure](https://portal.azure.com/).  
2. Selezionare **Create a resource > Security > Key Vault** (Crea risorsa > Sicurezza > Key Vault).  
3. Nella pagina **Crea insieme di credenziali delle chiavi** specificare le informazioni seguenti:  
   * **Nome:** specificare un nome univoco per Key Vault.  
   * **Sottoscrizione**: scegliere la sottoscrizione da usare.  
   * In **gruppo di risorse** scegliere **Crea nuovo** e immettere un nome per il gruppo di risorse.  
   * Scegliere un percorso nel menu a discesa Percorso.  
   * Lasciare invariati i valori predefiniti delle altre opzioni.  
4. Dopo avere specificato le informazioni, selezionare **Crea**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Aggiungere chiavi di accesso di Azure Cosmos DB all'insieme di credenziali delle chiavi.
1. Passare all'insieme di credenziali delle chiavi creato nel passaggio precedente e aprire la scheda **Segreti**.  
2. Selezionare **+ genera/importa**. 

   * Selezionare **Manuale** per **Opzioni di caricamento**.
   * Fornire un **Nome** al segreto
   * Specificare la stringa di connessione dell'account Cosmos DB nel campo **Valore**. Selezionare quindi **Crea**.

   :::image type="content" source="./media/access-secrets-from-keyvault/create-a-secret.png" alt-text="Creare un segreto":::

4. Dopo aver creato il segreto, aprirlo e copiare **l'identificatore del segreto che è nel formato seguente. Questo identificatore viene usato nella sezione successiva. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Creare un'applicazione Web di Azure

1. Creare un'applicazione Web di Azure oppure scaricarla dal [repository GitHub](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo). È una semplice applicazione MVC.  

2. Decomprimere l'applicazione scaricata e aprire il file **HomeController.cs**. Aggiornare l'ID del segreto nella riga seguente:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Salvare** il file, **compilare** la soluzione.  
4. Distribuire quindi l'applicazione in Azure. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**. Creare un nuovo profilo di servizio app (è possibile denominare l'app WebAppKeyVault1) e selezionare **Pubblica**.   

5. Una volta distribuita l'applicazione, dal portale di Azure passare all'app Web distribuita e attivare l'**Identità del servizio gestita** dell'applicazione.  

   :::image type="content" source="./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png" alt-text="Creare un segreto":::

Se si esegue ora l'applicazione, verrà visualizzato l'errore seguente poiché non è stata concessa alcuna autorizzazione per l'applicazione in Key Vault.

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-without-access.png" alt-text="Creare un segreto":::

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Registrare l'applicazione e concedere le autorizzazioni per leggere l'insieme di credenziali delle chiavi

In questa sezione viene registrata l'applicazione con Azure Active Directory e vengono assegnate le autorizzazioni all'applicazione per leggere l'insieme di credenziali delle chiavi. 

1. Passare al portale di Azure, aprire l'**insieme di credenziali delle chiavi** creato nella sezione precedente.  

2. Aprire **Criteri di accesso**, selezionare **+Aggiungi nuovo** trovare l'app Web distribuita, selezionare le autorizzazioni e selezionare **OK**.  

   :::image type="content" source="./media/access-secrets-from-keyvault/add-access-policy.png" alt-text="Creare un segreto":::

A questo punto, se si esegue l'applicazione, è possibile leggere il segreto da Key Vault.

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-with-access.png" alt-text="Creare un segreto":::
 
Analogamente, è possibile aggiungere un utente per l'accesso all'insieme di credenziali delle chiavi. È necessario aggiungersi manualmente all'insieme di credenziali delle chiavi selezionando **Criteri di accesso** e quindi fornire tutte le autorizzazioni necessarie per eseguire l'applicazione da Visual studio. Quando questa applicazione è in esecuzione sul desktop, assume l'identità dell'utente.

## <a name="next-steps"></a>Passaggi successivi

* Per configurare un firewall per Azure Cosmos DB vedere l'articolo [supporto del firewall](firewall-support.md) .
* Per configurare l'endpoint servizio di rete virtuale, vedere l'articolo relativo alla [protezione dell'accesso tramite l'endpoint di servizio di rete virtuale](vnet-service-endpoint.md).
