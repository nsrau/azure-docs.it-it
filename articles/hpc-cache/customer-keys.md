---
title: Usare le chiavi gestite dal cliente per crittografare i dati nella cache HPC di AzureUse customer-manged keys to encrypt data in Azure HPC Cache
description: Come usare l'insieme di credenziali delle chiavi di Azure con la cache HPC di Azure per controllare l'accesso alla chiave di crittografia anziché usare le chiavi di crittografia predefinite gestite da MicrosoftHow to use Azure Key Vault with Azure HPC Cache to control encryption key access instead of using the default Microsoft-managed encryption keys
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a31979763dd1ab5d8f289deef0e30cce27bb0df4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538869"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Usare le chiavi di crittografia gestite dal cliente per la cache HPC di AzureUse customer-managed encryption keys for Azure HPC Cache

È possibile usare L'insieme di credenziali delle chiavi di Azure per controllare la proprietà delle chiavi usate per crittografare i dati nella cache HPC di Azure.You can use Azure Key Vault to control ownership of the keys used to encrypt your data in Azure HPC Cache. In questo articolo viene illustrato come utilizzare le chiavi gestite dal cliente per la crittografia dei dati della cache.

> [!NOTE]
> Tutti i dati archiviati in Azure, inclusi i dischi della cache, vengono crittografati inattivi usando chiavi gestite da Microsoft per impostazione predefinita. È necessario seguire la procedura descritta in questo articolo solo se si desidera gestire le chiavi utilizzate per crittografare i dati.

Questa funzionalità è disponibile solo nelle aree di Azure seguenti:This feature is available only in these Azure regions:

* Stati Uniti orientali
* Stati Uniti centro-meridionali
* Stati Uniti occidentali 2

Esistono tre passaggi per abilitare la crittografia della chiave gestita dal cliente per la cache HPC di Azure:There are three steps to enable customer-managed key encryption for Azure HPC Cache:

1. Configurare un insieme di credenziali delle chiavi di Azure per archiviare le chiavi.
1. Quando si crea la cache HPC di Azure, scegliere la crittografia della chiave gestita dal cliente e specificare l'insieme di credenziali delle chiavi e la chiave da usare.
1. Dopo aver creato la cache, autorizzarla ad accedere all'insieme di credenziali delle chiavi.

La crittografia non è completamente configurata fino a quando non viene autorizzata dalla cache appena creata (passaggio 3). Ciò è dovuto al fatto che è necessario passare l'identità della cache all'insieme di credenziali delle chiavi per renderla un utente autorizzato. Non è possibile eseguire questa operazione prima di creare la cache, perché l'identità non esiste fino a quando non viene creata la cache.

Dopo aver creato la cache, non è possibile passare dalle chiavi gestite dal cliente alle chiavi gestite da Microsoft. Tuttavia, se la cache utilizza chiavi gestite dal cliente, è possibile [modificare](#update-key-settings) la chiave di crittografia, la versione della chiave e l'insieme di credenziali delle chiavi in base alle esigenze.

## <a name="understand-key-vault-and-key-requirements"></a>Comprendere l'insieme di credenziali delle chiavi e i requisiti chiave

L'insieme di credenziali delle chiavi e la chiave devono soddisfare questi requisiti per funzionare con la cache HPC di Azure.The key vault and key must meet these requirements to work with Azure HPC Cache.

Proprietà dell'insieme di credenziali delle chiavi:Key vault properties:

* **Sottoscrizione:** usare la stessa sottoscrizione usata per la cache.
* **Area:** l'insieme di credenziali delle chiavi deve trovarsi nella stessa area della cache HPC di Azure.Region - The key vault must be in the same region as the Azure HPC Cache.
* **Livello tariffario:** il livello Standard è sufficiente per l'uso con la cache HPC di Azure.Pricing tier - Standard tier is sufficient for use with Azure HPC Cache.
* **Eliminazione temporanea:** la cache HPC di Azure consentirà l'eliminazione temporanea se non è già configurata nell'insieme di credenziali delle chiavi.
* **Protezione purge:** è necessario abilitare la protezione purge.Purge protection - Purge protection must be enabled.
* **Criteri di accesso:** le impostazioni predefinite sono sufficienti.
* **Connettività di rete:** la cache HPC di Azure deve essere in grado di accedere all'insieme di credenziali delle chiavi indipendentemente dalle impostazioni dell'endpoint scelte.

Proprietà chiave:

* **Tipo di chiave** - RSAKey type - RSA
* **Dimensione della chiave RSA** - 2048
* **Abilitato** - Sì

Autorizzazioni di accesso all'insieme di credenziali delle chiavi:

* L'utente che crea la cache HPC di Azure deve disporre di autorizzazioni equivalenti al ruolo di [collaboratore dell'insieme di credenziali delle](../role-based-access-control/built-in-roles.md#key-vault-contributor)chiavi. Le stesse autorizzazioni sono necessarie per configurare e gestire l'insieme di credenziali delle chiavi di Azure.The same permissions are needed to set up and manage Azure Key Vault.

  Per ulteriori informazioni, vedere [Proteggere l'accesso a un insieme di](../key-vault/key-vault-secure-your-key-vault.md) credenziali delle chiavi.

## <a name="1-set-up-azure-key-vault"></a>1. Configurare l'insieme di credenziali delle chiavi di Azure1. Set up Azure Key Vault

È possibile impostare un insieme di credenziali delle chiavi e una chiave prima di creare la cache o farlo come parte della creazione della cache. Assicurarsi che queste risorse soddisfino i requisiti [descritti in precedenza.](#understand-key-vault-and-key-requirements)

Al momento della creazione della cache è necessario specificare un insieme di credenziali, una chiave e una versione della chiave da utilizzare per la crittografia della cache.

Leggere la [documentazione di Azure Key Vault](../key-vault/key-vault-overview.md) per i dettagli.

> [!NOTE]
> The Azure Key Vault must use the same subscription and be in the same region as the Azure HPC Cache. Utilizzare una delle aree supportate elencate all'inizio di questo articolo.

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. Creare la cache con le chiavi gestite dal cliente abilitate

You must specify the encryption key source when you create your Azure HPC Cache. Seguire le istruzioni in Creare una [cache HPC](hpc-cache-create.md)di Azure e specificare l'insieme di credenziali delle chiavi e la chiave nella pagina Chiavi di **crittografia del disco.** È possibile creare un nuovo insieme di credenziali delle chiavi e una nuova chiave durante la creazione della cache.

> [!TIP]
> Se la pagina Chiavi di **crittografia del disco** non viene visualizzata, assicurarsi che la cache si trova in una delle aree supportate.

L'utente che crea la cache deve disporre di privilegi uguali al ruolo di [collaboratore dell'insieme](../role-based-access-control/built-in-roles.md#key-vault-contributor) di credenziali delle chiavi o superiore.

1. Fare clic sul pulsante per abilitare le chiavi gestite privatamente. Dopo aver modificato questa impostazione, vengono visualizzate le impostazioni dell'insieme di credenziali delle chiavi.

1. Fare clic su **Seleziona un insieme di credenziali delle chiavi** per aprire la pagina di selezione della chiave. Scegliere o creare l'insieme di credenziali delle chiavi e la chiave per la crittografia dei dati sui dischi della cache.

   Se l'insieme di credenziali delle chiavi di Azure non viene visualizzato nell'elenco, verificare i requisiti seguenti:If your Azure Key Vault does not appear in the list, check these requirements:

   * La cache si trova nella stessa sottoscrizione dell'insieme di credenziali delle chiavi?
   * La cache si trova nella stessa area dell'insieme di credenziali delle chiavi?
   * Esiste connettività di rete tra il portale di Azure e l'insieme di credenziali delle chiavi?

1. Dopo aver selezionato un vault, selezionare la singola chiave tra le opzioni disponibili o creare una nuova chiave. La chiave deve essere una chiave RSA a 2048 bit.

1. Specificare la versione per la chiave selezionata. Per altre informazioni sul controllo delle versioni, vedere la [documentazione dell'insieme](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)di chiavi di Azure.Learn more about versioning in the Azure Key Vault documentation .

Continuare con il resto delle specifiche e creare la cache come descritto in Creare una [cache HPC](hpc-cache-create.md)di Azure .

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Autorizzare la crittografia dell'insieme di chiavi di Azure dalla cache
<!-- header is linked from create article, update if changed -->

Dopo alcuni minuti, la nuova cache HPC di Azure viene visualizzata nel portale di Azure.After a few minutes, the new Azure HPC Cache appears in your Azure portal. Passare alla pagina **Panoramica** per autorizzarla ad accedere all'insieme di credenziali delle chiavi di Azure e abilitare la crittografia delle chiavi gestita dal cliente. La cache potrebbe essere visualizzata nell'elenco delle risorse prima che i messaggi di "distribuzione in corso" siano chiari.)

Questo processo in due passaggi è necessario perché l'istanza della cache HPC di Azure richiede un'identità da passare all'insieme di credenziali delle chiavi di Azure per l'autorizzazione. L'identità della cache non esiste fino al completamento dei passaggi di creazione iniziali.

> [!NOTE]
> È necessario autorizzare la crittografia entro 90 minuti dalla creazione della cache. Se non si completa questo passaggio, la cache stimerà e avrà esito negativo. Una cache non riuscita deve essere ricreata, non può essere corretta.

La cache mostra lo stato **In attesa della chiave**. Fare clic sul **pulsante Abilita crittografia** nella parte superiore della pagina per autorizzare la cache ad accedere all'insieme di credenziali delle chiavi specificato.

![Screenshot della pagina di panoramica della cache nel portale, con evidenziazione sul pulsante Abilita crittografia (riga superiore) e Stato: In attesa della chiave](media/waiting-for-key.png)

Fare clic su **Abilita crittografia** e quindi sul pulsante **Sì** per autorizzare la cache a utilizzare la chiave di crittografia. Questa azione abilita anche la protezione di eliminazione temporanea ed eliminazione (se non è già abilitata) nell'insieme di credenziali delle chiavi.

![Screenshot della pagina di panoramica della cache nel portale, con un messaggio banner nella parte superiore che chiede all'utente di abilitare la crittografia facendo clic su Sì](media/enable-keyvault.png)

Dopo che la cache richiede l'accesso all'insieme di credenziali delle chiavi, può creare e crittografare i dischi in cui sono archiviati i dati memorizzati nella cache.

Dopo aver autorizzato la crittografia, la cache HPC di Azure passa attraverso diversi minuti di installazione per creare i dischi crittografati e l'infrastruttura correlata.

## <a name="update-key-settings"></a>Aggiornare le impostazioni dei tasti

È possibile modificare l'insieme di credenziali delle chiavi, la chiave o la versione della chiave per la cache dal portale di Azure.You can change the key vault, key, or key version for your cache from the Azure portal. Fare clic sul collegamento Impostazioni di **crittografia** della cache per aprire la pagina **Impostazioni chiave cliente.** Non è possibile modificare una cache tra le chiavi gestite dal cliente e le chiavi gestite dal sistema.

![Screenshot della pagina "Impostazioni chiavi cliente", raggiunta facendo clic su Impostazioni > Crittografia dalla pagina della cache nel portale di Azure](media/change-key-click.png)

Fare clic sul collegamento **Cambia chiave,** quindi fare clic su **Cambia l'insieme di credenziali, la chiave o** la versione della chiave per aprire il selettore di chiave.

![Screenshot della pagina "Seleziona chiave da Azure Key Vault" con tre selettori a discesa per scegliere l'insieme di credenziali delle chiavi, la chiave e la versione](media/select-new-key.png)

Gli insiemi di credenziali delle chiavi nella stessa sottoscrizione e nella stessa area della cache vengono visualizzati nell'elenco.

Dopo aver scelto i nuovi valori della chiave di crittografia, fare clic su **Seleziona**. Viene visualizzata una pagina di conferma con i nuovi valori. Fare clic su **Salva** per finalizzare la selezione.

![Screenshot della pagina di conferma con il pulsante Salva in alto a sinistra](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Altre informazioni sulle chiavi gestite dal cliente in AzureRead more about customer-managed keys in Azure

Questi articoli illustrano altre informazioni sull'uso dell'insieme di credenziali delle chiavi di Azure e delle chiavi gestite dal cliente per crittografare i dati in Azure:These articles explain more about using Azure Key Vault and customer-managed keys to encrypt data in Azure:

* [Panoramica della crittografia di archiviazione di AzureAzure storage encryption overview](../storage/common/storage-service-encryption.md)
* [Crittografia del disco con chiavi gestite dal cliente:](../virtual-machines/linux/disk-encryption.md#customer-managed-keys) documentazione per l'uso di archivi chiavi di Azure e dischi gestiti, che è simile al processo usato con la cache HPC di AzureDisk encryption with customer-managed keys - Documentation for using Azure Key Vault and managed disks, which is similar to the process used with Azure HPC Cache

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato la cache HPC di Azure e aver autorizzato la crittografia basata su Vault chiave, continuare a configurare la cache assegnandogli l'accesso alle origini dati.

* [Aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md)
