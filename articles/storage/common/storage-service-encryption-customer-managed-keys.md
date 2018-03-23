---
title: Crittografia del servizio di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault | Microsoft Docs
description: La funzionalità Crittografia del servizio di archiviazione di Azure consente di crittografare Archiviazione BLOB di Azure sul lato del servizio durante l'archiviazione dei dati e di decrittografarlo durante il recupero dei dati usando chiavi gestite dal cliente.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/07/2018
ms.author: lakasa
ms.openlocfilehash: b40858640d10e5661be420976520774bd50837cb
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Crittografia del servizio di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault

In Microsoft Azure la protezione e la salvaguardia dei dati sono essenziali per soddisfare i criteri di sicurezza e conformità dell'organizzazione. Per proteggere i dati, Archiviazione di Azure usa la funzionalità Crittografia del servizio di archiviazione (SSE, Storage Service Encryption), che codifica i dati quando vengono scritti nella risorsa di archiviazione e li decodifica quando vengono recuperati. Le operazioni di crittografia e decrittografia sono automatiche e trasparenti e usano la [crittografia AES](https://wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit, uno dei più efficaci algoritmi di crittografia a blocchi.

È possibile usare con SSE le chiavi di crittografia gestite da Microsoft oppure con le proprie chiavi di crittografia. Questo articolo descrive come usare le chiavi di crittografia personali. Per altre informazioni sull'utilizzo di chiavi gestite da Microsoft o sulla crittografia SSE in generale, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md).

La crittografia SSE per l'archiviazione BLOB e file è integrata con Azure Key Vault per consentire la gestione delle chiavi di crittografia con un insieme di credenziali delle chiavi. È possibile creare chiavi di crittografia personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API di Azure Key Vault per generare chiavi di crittografia. Con Azure Key Vault è possibile gestire e controllare le chiavi e anche controllarne l'utilizzo.

Le chiavi personalizzate sono utili perché offrono maggiore flessibilità, consentendo di creare, ruotare, disabilitare e definire i controlli di accesso. Con queste chiavi è anche possibile controllare le chiavi di crittografia usate per proteggere i dati.

## <a name="get-started-with-customer-managed-keys"></a>Introduzione alle chiavi gestite dal cliente

Per usare chiavi gestite dal cliente con la crittografia SSE è possibile creare un nuovo insieme di credenziali delle chiavi e una nuova chiave oppure usare un insieme di credenziali delle chiavi e una chiave già esistenti. L'account di archiviazione e l'insieme di credenziali chiave devono essere nella stessa area, ma possono appartenere a sottoscrizioni diverse. 

### <a name="step-1-create-a-storage-account"></a>Passaggio 1: Creare un account di archiviazione

Creare prima di tutto un account di archiviazione, se non ne è già disponibile uno. Per altre informazioni, vedere [Creare un account di archiviazione](storage-quickstart-create-account.md).

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>Passaggio 2: Abilitare la crittografia SSE per l'archiviazione BLOB e file

Per abilitare la crittografia SSE usando chiavi gestite dal cliente, è necessario abilitare anche due funzionalità di sicurezza delle chiavi, per l'eliminazione temporanea e la protezione dall'eliminazione. Ciò consente di evitare che le chiavi vengano eliminate intenzionalmente o accidentalmente. Il periodo massimo di conservazione delle chiavi è impostato su 90 giorni, in modo da proteggere gli utenti da azioni di malintenzionati o attacchi ransomware.

Se si vogliono abilitare a livello di codice le chiavi gestite dal cliente per la crittografia SSE, è possibile usare l'[API REST del provider di risorse di Archiviazione di Azure](https://docs.microsoft.com/rest/api/storagerp), la [libreria client dei provider delle risorse di archiviazione per .NET](https://docs.microsoft.com/dotnet/api), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli).

Per usare chiavi gestite dal cliente con la crittografia SSE, è necessario assegnare un'identità all'account di archiviazione. È possibile impostare l'identità eseguendo il comando di PowerShell seguente:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

È possibile abilitare le impostazioni per l'eliminazione temporanea e la protezione dall'eliminazione eseguendo i comandi di PowerShell seguenti:

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enableSoftDelete -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties

($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enablePurgeProtection -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties
```

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>Passaggio 3: Abilitare la crittografia con chiavi gestite dal cliente

Per impostazione predefinita, la crittografia SSE usa chiavi gestite da Microsoft. È possibile abilitare la crittografia SSE con chiavi gestite dal cliente per l'account di archiviazione usando il [portale di Azure](https://portal.azure.com/). Nel pannello **Impostazioni** relativo all'account di archiviazione fare clic su **Crittografia**. Selezionare l'opzione **Usare una chiave personalizzata**, come illustrato nella figura seguente.

![Schermata del portale che mostra l'opzione Crittografia](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>Passaggio 4: Selezionare la chiave

È possibile impostare la chiave specificando un URI oppure selezionandola da un insieme di credenziali delle chiavi.

#### <a name="specify-a-key-as-a-uri"></a>Specificare una chiave da un URI

Per specificare la chiave da un URI, seguire questi passaggi:

1. Scegliere l'opzione **Immettere l'URI della chiave**.  
2. Nel campo **URI della chiave** specificare l'URI.

    ![Screenshot del portale che visualizza l'opzione di crittografia Immettere l'URI della chiave](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

#### <a name="specify-a-key-from-a-key-vault"></a>Specificare una chiave da un insieme di credenziali delle chiavi 

Per specificare la chiave da un insieme di credenziali delle chiavi, seguire questi passaggi:

1. Scegliere l'opzione **Selezionare la chiave dall'insieme di credenziali delle chiavi**.  
2. Scegliere l'insieme di credenziali delle chiavi contenente la chiave da usare.
3. Scegliere la chiave dall'insieme di credenziali delle chiavi.

    ![Screenshot del portale che visualizza l'opzione di crittografia Usare una chiave personalizzata](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Se l'account di archiviazione non ha accesso all'insieme di credenziali delle chiavi, è possibile eseguire il comando di Azure PowerShell illustrato nell'immagine seguente per concedere l'accesso.

![Screenshot del portale che visualizza l'accesso negato all'insieme di credenziali delle chiavi](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

È anche possibile concedere l'accesso tramite il portale di Azure passando ad Azure Key Vault nel portale e attivando l'accesso all'account di archiviazione.

### <a name="step-5-copy-data-to-storage-account"></a>Passaggio 5: Copiare i dati nell'account di archiviazione

Per trasferire i dati nel nuovo account di archiviazione in modo che vengano crittografati, vedere il passaggio 3 della sezione [Introduzione in Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md#step-3-copy-data-to-storage-account).

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>Passaggio 6: Eseguire query relative allo stato dei dati crittografati

Per eseguire query relative allo stato dei dati crittografati, vedere il passaggio 4 della sezione [Introduzione in Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md#step-4-query-the-status-of-the-encrypted-data).

## <a name="faq-for-sse-with-customer-managed-keys"></a>Domande frequenti sulla crittografia SSE con chiavi gestite dal cliente

**D: È possibile usare chiavi gestite dal cliente con la crittografia SSE se si usa l'archiviazione Premium?**

R: Sì, la crittografia SSE con chiavi gestite da Microsoft e chiavi gestite dal cliente è supportata sia nell'archiviazione Standard sia nell'archiviazione Premium.

**D: È possibile creare nuovi account di archiviazione con la crittografia SSE con chiavi gestite dal cliente abilitata usando Azure PowerShell e l'interfaccia della riga di comando di Azure?**

R: Sì.

**D: Qual è il costo aggiuntivo del servizio Archiviazione di Azure se si usano chiavi gestite dal cliente con la crittografia SSE?**

R: Per l'uso di Azure Key Vault è previsto un costo. Per altre informazioni, visitare la [pagina relativa ai prezzi di Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). Non sono previsti costi aggiuntivi per la crittografia SSE, che è abilitata per tutti gli account di archiviazione.

**D: È possibile revocare l'accesso alle chiavi di crittografia?**

R: Sì, è possibile revocare l'accesso in qualsiasi momento. Esistono diversi modi per revocare l'accesso alle chiavi. Per altre informazioni, vedere [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) (Modulo PowerShell di Azure Key Vault) e [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault) (Interfaccia della riga di comando di Azure Key Vault). La revoca dell'accesso blocca di fatto l'accesso a tutti i BLOB dell'account di archiviazione poiché il servizio Archiviazione di Azure non può accedere alla chiave di crittografia dell'account.

**D: È possibile creare un account di archiviazione e una chiave in aree diverse?**

R: No, l'account di archiviazione, Azure Key Vault e la chiave devono trovarsi nella stessa area.

**D: È possibile abilitare chiavi gestite dal cliente per la crittografia SSE mentre si crea l'account di archiviazione?**

R: No. Quando si crea l'account di archiviazione, per la crittografia SSE sono disponibili solo chiavi gestite da Microsoft. Per usare chiavi gestite dal cliente, è necessario aggiornare le proprietà dell'account di archiviazione. È possibile usare REST o una delle librerie client di archiviazione per aggiornare l'account di archiviazione a livello di codice oppure aggiornare le proprietà dell'account di archiviazione tramite il portale di Azure dopo la creazione dell'account.

**D: È possibile disabilitare la crittografia mentre si usano chiavi gestite dal cliente con la crittografia SSE?**

R: No, non è possibile disabilitarla. La crittografia è abilitata per impostazione predefinita per tutti i servizi di archiviazione BLOB, file, tabelle e code. È tuttavia possibile passare dall'uso di chiavi gestite da Microsoft all'uso di chiavi gestite dal cliente e viceversa.

**D: La crittografia SSE è abilitata per impostazione predefinita quando si crea un nuovo account di archiviazione?**

R: La crittografia SSE è abilitata per impostazione predefinita per tutti gli account di archiviazione e per tutti i servizi di archiviazione BLOB, file, tabelle e code.

**D: La crittografia SSE con chiavi gestite dal cliente non viene abilitata sull'account di archiviazione. Per quale motivo?**

R: Accertarsi di usare un account di archiviazione di Azure Resource Manager. Gli account di archiviazione di tipo classico non sono supportati con le chiavi gestite dal cliente. La crittografia SSE con chiavi gestite dal cliente può essere abilitata solo sugli account di archiviazione di Resource Manager.

**D: A cosa servono le impostazioni per l'eliminazione temporanea e la protezione dall'eliminazione? È necessario abilitarle per usare la crittografia SSE con chiavi gestite dal cliente?**

R: Per usare la crittografia SSE con chiavi gestite dal cliente, è necessario abilitare le impostazioni per l'eliminazione temporanea e la protezione dall'eliminazione. Ciò consente di assicurarsi che le chiavi non vengano eliminate intenzionalmente o accidentalmente. Il periodo massimo di conservazione delle chiavi è impostato su 90 giorni, in modo da proteggere gli utenti da azioni di malintenzionati o attacchi ransomware. Questa impostazione non può essere disabilitata.

**D: La crittografia SSE con chiavi gestite dal cliente è disponibile solo in aree specifiche?**

R: La crittografia SSE con chiavi gestite dal cliente è disponibile in tutte le aree per l'archiviazione BLOB e file.

**D: Chi si può contattare per ottenere assistenza in caso di problemi o per inviare commenti e suggerimenti?**

D: Contattare [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) per qualsiasi problema relativo a Crittografia del servizio di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

-   Per altre informazioni sul set completo di funzionalità di sicurezza che consentono agli sviluppatori di creare applicazioni protette, vedere [Guida alla sicurezza di Archiviazione di Azure](storage-security-guide.md).

-   Per informazioni generali su Azure Key Vault , vedere [Cos'è l'insieme di credenziali chiave di Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)

-   Per la guida introduttiva di Azure Key Vault, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).
