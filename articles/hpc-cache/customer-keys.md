---
title: Usare le chiavi gestite dal cliente per crittografare i dati nella cache HPC di Azure
description: Come usare Azure Key Vault con la cache HPC di Azure per controllare l'accesso alla chiave di crittografia invece di usare le chiavi di crittografia predefinite gestite da Microsoft
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: 8bce19e384e600e95b8306dcbba38652b4432bc9
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340530"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Usare le chiavi di crittografia gestite dal cliente per la cache HPC di Azure

È possibile usare Azure Key Vault per controllare la proprietà delle chiavi usate per crittografare i dati nella cache HPC di Azure. Questo articolo illustra come usare le chiavi gestite dal cliente per la crittografia dei dati della cache.

> [!NOTE]
> Tutti i dati archiviati in Azure, inclusi i dischi della cache, vengono crittografati a riposo usando chiavi gestite da Microsoft per impostazione predefinita. Se si vogliono gestire le chiavi usate per crittografare i dati, è necessario seguire la procedura descritta in questo articolo.

La cache HPC di Azure è anche protetta dalla [crittografia dell'host delle macchine virtuali](../virtual-machines/linux/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) nei dischi gestiti che contengono i dati memorizzati nella cache, anche se si aggiunge una chiave cliente per i dischi della cache. L'aggiunta di una chiave gestita dal cliente per la crittografia doppia offre un livello di sicurezza aggiuntivo per i clienti con esigenze di sicurezza elevate. Per informazioni dettagliate, leggere la [crittografia lato server dell'archiviazione su disco di Azure](../virtual-machines/linux/disk-encryption.md) .

Questa funzionalità è disponibile solo in alcune aree di Azure in cui è disponibile cache HPC di Azure. Per informazioni dettagliate, vedere l'elenco di [disponibilità dell'area](hpc-cache-overview.md#region-availability) .

Per abilitare la crittografia della chiave gestita dal cliente per la cache HPC di Azure, è necessario eseguire tre passaggi:

1. Configurare un Azure Key Vault per archiviare le chiavi.
1. Quando si crea la cache HPC di Azure, scegliere crittografia chiave gestita dal cliente e specificare l'insieme di credenziali delle chiavi e la chiave da usare.
1. Dopo aver creato la cache, autorizzarla ad accedere all'insieme di credenziali delle chiavi.

La crittografia non è completamente configurata finché non viene autorizzata dalla cache appena creata (passaggio 3). Questo perché è necessario passare l'identità della cache all'insieme di credenziali delle chiavi per impostarlo come utente autorizzato. Non è possibile eseguire questa operazione prima di creare la cache, perché l'identità non esiste fino a quando non viene creata la cache.

Dopo aver creato la cache, non è possibile modificare le chiavi gestite dal cliente e le chiavi gestite da Microsoft. Tuttavia, se la cache USA chiavi gestite dal cliente, è possibile [modificare](#update-key-settings) la chiave di crittografia, la versione della chiave e l'insieme di credenziali delle chiavi in base alle esigenze.

## <a name="understand-key-vault-and-key-requirements"></a>Informazioni sui requisiti chiave e sull'insieme di credenziali delle chiavi

L'insieme di credenziali delle chiavi e la chiave devono soddisfare questi requisiti per l'uso della cache HPC di Azure.

Proprietà di Key Vault:

* **Sottoscrizione** : usare la stessa sottoscrizione usata per la cache.
* **Region** : l'insieme di credenziali delle chiavi deve trovarsi nella stessa area della cache HPC di Azure.
* Piano **tariffario** : il livello standard è sufficiente per l'uso con la cache HPC di Azure.
* **Eliminazione** temporanea: la cache HPC di Azure Abilita l'eliminazione temporanea se non è già stata configurata nell'insieme di credenziali delle chiavi.
* **Ripulisci** protezione: è necessario abilitare la protezione ripulitura.
* **Criteri di accesso** : le impostazioni predefinite sono sufficienti.
* **Connettività di rete** : la cache HPC di Azure deve essere in grado di accedere all'insieme di credenziali delle chiavi, indipendentemente dalle impostazioni dell'endpoint scelto.

Proprietà chiave:

* **Tipo di chiave** -RSA
* **Dimensioni chiave RSA** -2048
* **Abilitato** -Sì

Autorizzazioni di accesso a Key Vault:

* L'utente che crea la cache HPC di Azure deve avere le autorizzazioni equivalenti al [ruolo Key Vault collaboratore](../role-based-access-control/built-in-roles.md#key-vault-contributor). Le stesse autorizzazioni sono necessarie per configurare e gestire Azure Key Vault.

  Per ulteriori informazioni, vedere [accesso sicuro a un insieme di](../key-vault/general/secure-your-key-vault.md) credenziali delle chiavi.

## <a name="1-set-up-azure-key-vault"></a>1. configurare Azure Key Vault

È possibile configurare un insieme di credenziali delle chiavi e una chiave prima di creare la cache oppure eseguire questa operazione nell'ambito della creazione della cache. Assicurarsi che queste risorse soddisfino i requisiti descritti in [precedenza](#understand-key-vault-and-key-requirements).

Al momento della creazione della cache è necessario specificare un insieme di credenziali, una chiave e una versione della chiave da usare per la crittografia della cache.

Per informazioni dettagliate, leggere la [documentazione Azure Key Vault](../key-vault/general/overview.md) .

> [!NOTE]
> Il Azure Key Vault deve usare la stessa sottoscrizione e trovarsi nella stessa area della cache HPC di Azure. Assicurarsi che l'area scelta [supporti la funzionalità chiavi gestite dal cliente](hpc-cache-overview.md#region-availability).

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. creare la cache con chiavi gestite dal cliente abilitate

Quando si crea la cache HPC di Azure, è necessario specificare l'origine della chiave di crittografia. Seguire le istruzioni in [creare una cache HPC di Azure](hpc-cache-create.md)e specificare l'insieme di credenziali delle chiavi e la chiave nella pagina **chiavi di crittografia del disco** . È possibile creare un nuovo insieme di credenziali delle chiavi e una chiave durante la creazione della cache.

> [!TIP]
> Se la pagina **chiavi di crittografia del disco** non viene visualizzata, verificare che la cache si trovi in una delle aree supportate.

L'utente che crea la cache deve disporre dei privilegi equivalenti al [ruolo di collaboratore Key Vault](../role-based-access-control/built-in-roles.md#key-vault-contributor) o superiore.

1. Fare clic sul pulsante per abilitare le chiavi gestite privatamente. Dopo aver modificato questa impostazione, verranno visualizzate le impostazioni dell'insieme di credenziali delle chiavi.

1. Fare clic su **selezionare un** insieme di credenziali delle chiavi per aprire la pagina di selezione della chiave. Scegliere o creare l'insieme di credenziali delle chiavi e la chiave per la crittografia dei dati nei dischi della cache.

   Se il Azure Key Vault non viene visualizzato nell'elenco, verificare i requisiti seguenti:

   * La cache si trova nella stessa sottoscrizione dell'insieme di credenziali delle chiavi?
   * La cache si trova nella stessa area dell'insieme di credenziali delle chiavi?
   * Esiste una connettività di rete tra il portale di Azure e l'insieme di credenziali delle chiavi?

1. Dopo aver selezionato un insieme di credenziali, selezionare la chiave singola dalle opzioni disponibili oppure creare una nuova chiave. La chiave deve essere una chiave RSA a 2048 bit.

1. Consente di specificare la versione per la chiave selezionata. Per ulteriori informazioni sul controllo delle versioni, vedere la [documentazione Azure Key Vault](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning).

Continuare con le altre specifiche e creare la cache come descritto in [creare una cache HPC di Azure](hpc-cache-create.md).

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. autorizzare Azure Key Vault crittografia dalla cache
<!-- header is linked from create article, update if changed -->

Dopo alcuni minuti, la nuova cache HPC di Azure viene visualizzata nella portale di Azure. Passare alla pagina **Overview (panoramica** ) per autorizzare l'accesso alla Azure Key Vault e abilitare la crittografia della chiave gestita dal cliente.

> [!TIP]
> La cache potrebbe essere visualizzata nell'elenco delle risorse prima della cancellazione dei messaggi di "distribuzione in corso". Controllare l'elenco di risorse dopo un minuto o due anziché attendere una notifica di esito positivo.

Questo processo in due passaggi è necessario perché l'istanza della cache HPC di Azure richiede un'identità da passare al Azure Key Vault per l'autorizzazione. L'identità della cache non esiste finché non vengono completati i passaggi di creazione iniziali.

> [!NOTE]
> Dopo aver creato la cache, è necessario autorizzare la crittografia entro 90 minuti. Se non si completa questo passaggio, il timeout della cache avrà esito negativo. Una cache non riuscita deve essere ricreata, ma non può essere corretta.

La cache Mostra lo stato **in attesa di chiave**. Fare clic sul pulsante **Abilita crittografia** nella parte superiore della pagina per autorizzare la cache ad accedere all'insieme di credenziali delle chiavi specificato.

![screenshot della pagina Panoramica della cache nel portale, con evidenziazione sul pulsante Abilita crittografia (riga superiore) e stato: in attesa della chiave](media/waiting-for-key.png)

Fare clic su **Abilita crittografia** , quindi fare clic sul pulsante **Sì** per autorizzare la cache a usare la chiave di crittografia. Questa azione abilita anche la protezione con eliminazione temporanea e ripulitura (se non è già abilitata) nell'insieme di credenziali delle chiavi.

![screenshot della pagina Panoramica della cache nel portale, con un messaggio banner nella parte superiore che chiede all'utente di abilitare la crittografia facendo clic su Sì](media/enable-keyvault.png)

Dopo la richiesta di accesso all'insieme di credenziali delle chiavi, la cache può creare e crittografare i dischi in cui sono archiviati i dati memorizzati nella cache.

Dopo aver autorizzato la crittografia, la cache HPC di Azure passa attraverso diversi minuti di installazione per creare i dischi crittografati e l'infrastruttura correlata.

## <a name="update-key-settings"></a>Aggiorna impostazioni chiave

È possibile modificare l'insieme di credenziali delle chiavi, la chiave o la versione della chiave per la cache dal portale di Azure. Fare clic sul collegamento Impostazioni di **crittografia** della cache per aprire la pagina **impostazioni chiave cliente** .

Non è possibile modificare una cache tra chiavi gestite dal cliente e chiavi gestite dal sistema.

![screenshot della pagina "impostazioni chiavi cliente", raggiunta facendo clic su impostazioni > crittografia dalla pagina cache nella portale di Azure](media/change-key-click.png)

Fare clic sul collegamento **cambia chiave** , quindi fare clic su modifica l'insieme di credenziali **delle chiavi, la chiave o la versione** per aprire il selettore di chiave.

![screenshot della pagina "Seleziona chiave da Azure Key Vault" con tre selettori a discesa per scegliere Key Vault, Key e Version](media/select-new-key.png)

Gli insiemi di credenziali delle chiavi nella stessa sottoscrizione e nella stessa area della cache vengono visualizzati nell'elenco.

Dopo aver scelto i nuovi valori della chiave di crittografia, fare clic su **Seleziona**. Viene visualizzata una pagina di conferma con i nuovi valori. Fare clic su **Salva** per finalizzare la selezione.

![screenshot della pagina di conferma con il pulsante Salva in alto a sinistra](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Scopri di più sulle chiavi gestite dal cliente in Azure

Questi articoli illustrano come usare Azure Key Vault e chiavi gestite dal cliente per crittografare i dati in Azure:

* [Panoramica della crittografia di archiviazione di Azure](../storage/common/storage-service-encryption.md)
* [Crittografia del disco con chiavi gestite dal cliente](../virtual-machines/linux/disk-encryption.md#customer-managed-keys) : documentazione per l'uso di Azure Key Vault con Managed disks, che è uno scenario analogo alla cache HPC di Azure

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato la cache HPC di Azure e aver autorizzato la crittografia basata su Key Vault, continuare a configurare la cache concedendo l'accesso alle origini dati.

* [Aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md)