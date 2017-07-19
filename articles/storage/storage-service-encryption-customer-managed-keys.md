---
title: Crittografia del servizio di archiviazione di Azure con chiavi gestite dall&quot;utente in Azure Key Vault | Microsoft Docs
description: "La funzionalità Crittografia del servizio di archiviazione di Azure consente di crittografare Archiviazione BLOB di Azure sul lato del servizio durante l&quot;archiviazione dei dati e di decrittografarlo durante il recupero dei dati mediante chiavi gestite dall&quot;utente."
services: storage
documentationcenter: .net
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: lakasa
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: b596cf1a98a9c6f42c3bbee9cc27608549e2b5ea
ms.contentlocale: it-it
ms.lasthandoff: 06/08/2017


---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Crittografia del servizio di archiviazione con chiavi gestite dall'utente in Azure Key Vault

In Microsoft Azure la protezione e la salvaguardia dei dati sono essenziali per soddisfare i criteri di sicurezza e conformità dell'organizzazione.  Un metodo per proteggere i dati inattivi è l'uso della crittografia del servizio di archiviazione (SSE, Storage Service Encryption), che codifica automaticamente i dati durante la scrittura nell'archiviazione e li decodifica quando vengono recuperati. La crittografia e la decrittografia sono automatiche e completamente trasparenti e usano la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit, uno degli algoritmi di crittografia a blocchi più efficaci.

È possibile usare con SSE le chiavi di crittografia gestite da Microsoft oppure con le proprie chiavi di crittografia. In questo articolo viene illustrata la seconda opzione. Per altre informazioni sull'uso di chiavi gestite da Microsoft o sulla crittografia SSE in generale, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md).

Per consentire l'uso di chiavi di crittografia personalizzate, la crittografia SSE per l'archiviazione BLOB è integrata con Azure Key Vault (AKV). È possibile creare chiavi di crittografia personalizzate e archiviarle in AKV oppure usare le API AKV per generare le chiavi di crittografia. AKV consente non solo di gestire e controllare le chiavi, ma anche di controllarne l'uso. 

Perché può essere utile creare chiavi personalizzate? Questa opzione offre maggior flessibilità e consente di creare, ruotare, disattivare e definire i controlli di accesso e di controllare le chiavi di crittografia usate per la protezione dei dati.

## <a name="sse-with-customer-managed-keys-preview"></a>Crittografia SSE con anteprima delle chiavi gestite dal cliente

Questa funzionalità è attualmente in anteprima. Per usare la funzionalità è necessario creare un nuovo account di archiviazione. È possibile creare un nuovo insieme di credenziali chiave e una nuova chiave oppure usare un insieme di credenziali e una chiave esistenti. L'account di archiviazione e l'insieme di credenziali chiave devono essere nella stessa area, ma possono appartenere a sottoscrizioni diverse.

Per partecipare all'anteprima, contattare [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com). Si riceverà un collegamento dedicato per la partecipazione all'anteprima.

Per altre informazioni, vedere le [domande frequenti](#frequently-asked-questions-about-storage-service-encryption-for-data-at-rest).

> [!IMPORTANT]
> Prima di eseguire la procedura descritta in questo articolo è necessario registrarsi per l'anteprima. Senza l'accesso all'anteprima non è possibile abilitare questa funzionalità nel portale.

## <a name="getting-started"></a>Introduzione
## <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Passaggio 1: [Creare un nuovo account di archiviazione](storage-create-storage-account.md)

## <a name="step-2-enable-encryption"></a>Passaggio 2: Abilitare la crittografia
È possibile abilitare la crittografia SSE per l'account di archiviazione mediante il [portale di Azure](https://portal.azure.com). Nel pannello delle impostazioni dell'account di archiviazione cercare la sezione Servizio BLOB illustrata in questo screenshot, quindi fare clic su Crittografia.

![Screenshot del portale che illustra l'opzione Crittografia](./media/storage-service-encryption/image1.png)
<br/>*Abilitare la crittografia SSE per il servizio BLOB*

Se si vuole abilitare o disabilitare a livello di codice la crittografia del servizio di archiviazione in un account di archiviazione, è possibile usare l'[API REST del provider di risorse di archiviazione di Azure](https://docs.microsoft.com/en-us/rest/api/storagerp/?redirectedfrom=MSDN), la [libreria client dei provider delle risorse di archiviazione per .NET](https://docs.microsoft.com/en-us/dotnet/api/?redirectedfrom=MSDN), [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-4.0.0) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli).

Se in questa schermata non appare la casella di controllo "Usare una chiave personalizzata" non è presente l'approvazione per eseguire l'anteprima. Inviare un messaggio di posta elettronica a [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) e richiedere l'approvazione.

![Screenshot del portale che visualizza l'anteprima della crittografia](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

Per impostazione predefinita SSE usa le chiavi gestite da Microsoft. Per usare chiavi personalizzate selezionare la casella. Quindi è possibile specificare l'URI della chiave oppure selezionare una chiave e un insieme di credenziali chiave nel selettore.

## <a name="step-3-select-your-key"></a>Passaggio 3: Selezionare la chiave

![Screenshot del portale che visualizza l'opzione di crittografia Usare una chiave personalizzata](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

![Screenshot del portale che visualizza l'opzione di crittografia Immettere l'URI della chiave](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Se l'account di archiviazione non ha accesso all'insieme di credenziali delle chiavi, è possibile eseguire il comando seguente con Azure Powershell per concedere all'insieme di credenziali delle chiavi richiesto l'accesso agli account di archiviazione.

![Screenshot del portale che visualizza l'accesso negato all'insieme di credenziali delle chiavi](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

È anche possibile concedere l'accesso tramite il portale di Azure accedendo ad Azure Key Vault nel portale e attivando l'accesso all'account di archiviazione.

## <a name="step-4-copy-data-to-storage-account"></a>Passaggio 4: Copiare i dati in un account di archiviazione
Per trasferire i dati nel nuovo account di archiviazione in modo che vengano crittografati, vedere il [passaggio 3 della sezione Introduzione di Crittografia del servizio di archiviazione di Azure per dati inattivi](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-3-copy-data-to-storage-account).

## <a name="step-5-query-the-status-of-the-encrypted-data"></a>Passaggio 5: Eseguire query relative allo stato dei dati crittografati
Per l'esecuzione di query relative allo stato dei dati crittografati, vedere il [passaggio 4 della sezione Introduzione di Crittografia del servizio di archiviazione di Azure per dati inattivi](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-4-query-the-status-of-the-encrypted-data).

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Domande frequenti su Crittografia del servizio di archiviazione per i dati inattivi
**D: È possibile usare SSE con chiavi gestite dal cliente se si usa l'archiviazione Premium?**

R: Sì, la crittografia SSE con chiavi gestite da Microsoft e chiavi gestite dal cliente è supportata sia nell'archiviazione Standard sia nell'archiviazione Premium. 

**D: È possibile creare nuovi account di archiviazione con crittografia SSE con chiavi gestite dal cliente abilitata usando Azure PowerShell e l'interfaccia della riga di comando di Azure?**

A: Sì.

**D: Qual è il costo aggiuntivo dell'Archiviazione di Azure se si abilita la crittografia SSE con chiavi gestite dal cliente?**

R: Per l'uso di Azure Key Vault è previsto un costo. Per altre informazioni, visitare la [pagina relativa ai prezzi di Key Vault](https://azure.microsoft.com/en-us/pricing/details/key-vault/). Per l'uso della crittografia SSE non sono previsti costi aggiuntivi.

**D: È possibile revocare l'accesso alle chiavi di crittografia?**

R: Sì, è possibile revocare l'accesso in qualsiasi momento. Esistono diversi modi per revocare l'accesso alle chiavi. Per altre informazioni, vedere [Azure Key Vault PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/?view=azurermps-4.0.0) (Modulo PowerShell di Azure Key Vault) e [Azure Key Vault CLI](https://docs.microsoft.com/en-us/cli/azure/keyvault) (Interfaccia della riga di comando di Azure Key Vault). La revoca dell'accesso blocca di fatto l'accesso a tutti i BLOB dell'account di archiviazione, in quanto l'Archiviazione di Azure non dispone di accesso alla chiave di crittografia account.

**D: È possibile creare un account di archiviazione e una chiave in aree diverse?**

R: No, l'account di archiviazione e l'insieme di credenziali delle chiavi/la chiave devono trovarsi nella stessa area. 

**D: È possibile abilitare SSE con chiavi gestite dal cliente mentre si crea l'account di archiviazione?**

R: No. Quando si abilita SSE durante la creazione di account di archiviazione, è possibile usare solo chiavi gestite da Microsoft. Per usare chiavi gestite dal cliente è necessario aggiornare le proprietà dell'account di archiviazione. È possibile usare REST o una delle librerie client di archiviazione per aggiornare l'account di archiviazione a livello di codice oppure aggiornare le proprietà dell'account di archiviazione tramite il portale di Azure dopo la creazione dell'account.

**D: è possibile disabilitare la crittografia mentre si usa la crittografia SSE con chiavi gestite dal cliente?**

R: No, non è possibile disabilitare la crittografia mentre si usa la crittografia SSE con chiavi gestite dal cliente. Per disabilitare la crittografia è necessario passare all'uso di chiavi gestite da Microsoft. Questa operazione può essere eseguita nel portale di Azure o usando PowerShell.

**D: La funzionalità Crittografia del servizio di archiviazione è abilitata per impostazione predefinita quando si crea un nuovo account di archiviazione?**

R: La funzionalità Crittografia del servizio di archiviazione e non è abilitata per impostazione predefinita. È possibile usare il portale di Azure per abilitarla. È anche possibile abilitare questa funzionalità a livello di codice usando l'API REST del provider di risorse di archiviazione. 

**D: Non è possibile abilitare la crittografia sull'account di archiviazione.**

R: Se si tratta di un account di archiviazione di Resource Manager, gli account di archiviazione di tipo classico non sono supportati. La crittografia SSE con chiavi gestite dal cliente può anche essere attivata solo sugli account di archiviazione di Gestione risorse appena creati.

**D: La crittografia SSE con chiavi gestite dal cliente è disponibile solo in aree specifiche?**

R: In questa versione di anteprima la crittografia SSE è disponibile solo in determinate aree per l'archiviazione BLOB. Per verificare la disponibilità e ottenere informazioni dettagliate sull'anteprima inviare un messaggio di posta elettronica all'indirizzo [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com). 

**D: Chi si può contattare per ottenere assistenza in caso di problemi o per inviare commenti e suggerimenti?**

D: Contattare [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) per qualsiasi problema relativo a Crittografia del servizio di archiviazione. 

## <a name="next-steps"></a>Passaggi successivi

*   Per altre informazioni sul set completo di funzionalità di protezione che consentono agli sviluppatori di creare applicazioni protette, vedere [Guida alla sicurezza di Archiviazione di Azure](https://docs.microsoft.com/en-us/azure/storage/storage-security-guide).
*   Per informazioni generali su Azure Key Vault , vedere [Cos'è l'insieme di credenziali chiave di Azure?](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis)
*   Per la guida introduttiva di Azure Key Vault, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md).

