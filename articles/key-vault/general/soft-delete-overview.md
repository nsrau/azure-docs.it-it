---
title: Eliminazione temporanea Azure Key Vault | Microsoft Docs
description: L'eliminazione temporanea in Azure Key Vault consente di recuperare gli insiemi di credenziali delle chiavi eliminati e gli oggetti dell'insieme di credenziali delle chiavi, ad esempio chiavi, segreti e certificati.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/30/2020
ms.openlocfilehash: c8ae10fa059bb9cfd32b95f9bc6d21f30ad9f880
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91744203"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Panoramica di eliminazione temporanea di Azure Key Vault

> [!IMPORTANT]
> È necessario abilitare immediatamente l'eliminazione temporanea sugli insiemi di credenziali delle chiavi. La possibilità di rifiutare esplicitamente l'eliminazione temporanea sarà deprecata entro la fine dell'anno e la protezione dell'eliminazione temporanea verrà attivata automaticamente per tutti gli insiemi di credenziali delle chiavi.  Vedi i dettagli completi [qui](soft-delete-change.md)

La funzionalità di eliminazione temporanea di Key Vault consente di recuperare gli insiemi di credenziali eliminati ed eliminare gli oggetti dell'insieme di credenziali delle chiavi (ad esempio, chiavi, segreti e certificati), noti come soft-delete. In particolare, vengono affrontati gli scenari seguenti: questa garanzia offre le seguenti protezioni:

- Una volta che un segreto, una chiave, un certificato o un insieme di credenziali delle chiavi viene eliminato, rimarrà reversibile per un periodo configurabile da 7 a 90 giorni di calendario. Se non viene specificata alcuna configurazione, il periodo di recupero predefinito verrà impostato su 90 giorni. In questo modo gli utenti hanno tempo sufficiente per rilevare un'eliminazione accidentale del segreto e rispondere.
- Per eliminare definitivamente un segreto, è necessario effettuare due operazioni. Per prima cosa, un utente deve eliminare l'oggetto, che lo inserisce nello stato di eliminazione temporanea. In secondo luogo, l'utente deve eliminare l'oggetto nello stato di eliminazione temporanea. Per l'operazione di ripulitura sono necessarie autorizzazioni aggiuntive per i criteri di accesso. Queste protezioni aggiuntive riducono il rischio di eliminazione accidentale di un segreto o di un insieme di credenziali delle chiavi da parte di un utente.  
- Per eliminare un segreto nello stato di eliminazione temporanea, a un'entità servizio deve essere concessa un'ulteriore autorizzazione per i criteri di accesso "ripulitura". L'autorizzazione per l'eliminazione dei criteri di accesso non viene concessa per impostazione predefinita a qualsiasi entità servizio, inclusi l'insieme di credenziali delle chiavi e i proprietari delle sottoscrizioni e deve essere impostata intenzionalmente. Richiedendo un'autorizzazione per i criteri di accesso con privilegi elevati per eliminare un segreto eliminato temporaneamente, si riduce la probabilità di eliminare accidentalmente un segreto.

## <a name="supporting-interfaces"></a>Supporto di interfacce

La funzionalità di eliminazione temporanea è disponibile tramite l' [API REST](/rest/api/keyvault/), le interfacce dell'interfaccia della riga di comando di [Azure](soft-delete-cli.md), [Azure PowerShell](soft-delete-powershell.md)e [.NET/C#](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet) , oltre ai [modelli ARM](/azure/templates/microsoft.keyvault/2019-09-01/vaults).

## <a name="scenarios"></a>Scenari

Gli insiemi di credenziali delle chiavi di Azure sono risorse tracciate, gestite da Azure Resource Manager. Azure Resource Manager specifica anche un comportamento ben definito per l'eliminazione, in base al quale al termine dell'operazione di eliminazione la risorsa non deve essere più accessibile. Con la funzionalità di eliminazione temporanea viene consentito invece il ripristino dell'oggetto eliminato, indipendentemente dal fatto che l'eliminazione sia stata intenzionale o accidentale.

1. In uno scenario tipico, un utente può aver accidentalmente eliminato un insieme di credenziali delle chiavi o un oggetto di un insieme di credenziali delle chiavi. Se l'insieme di credenziali delle chiavi o l'oggetto dell'insieme è stato impostato in modo da essere recuperabile entro un periodo predeterminato, l'utente può annullare l'eliminazione e ripristinare i dati.

2. In uno scenario diverso, un utente non autorizzato può tentare di eliminare un insieme di credenziali delle chiavi o un oggetto di un insieme di credenziali delle chiavi, ad esempio una chiave in un insieme di credenziali, per provocare un'interruzione delle attività. In questo caso, separare l'eliminazione dell'insieme di credenziali delle chiavi o dell'oggetto dell'insieme di credenziali delle chiavi dall'eliminazione effettiva dei dati sottostanti può rappresentare una misura di sicurezza, poiché consente, ad esempio, di limitare le autorizzazioni di eliminazione dei dati solo a un ruolo attendibile. Con questo approccio, infatti, è necessario un quorum per un'operazione che, altrimenti, determinerebbe una perdita immediata di dati.

### <a name="soft-delete-behavior"></a>Comportamento della funzione di eliminazione temporanea

Quando l'eliminazione temporanea è abilitata, le risorse contrassegnate come risorse eliminate vengono mantenute per un periodo di tempo specificato (90 giorni per impostazione predefinita). Il servizio offre anche un meccanismo per il ripristino dell'oggetto eliminato, essenzialmente annullando l'operazione di eliminazione.

Quando si crea un nuovo insieme di credenziali delle chiavi, l'eliminazione temporanea è abilitata per impostazione predefinita. È possibile creare un insieme di credenziali delle chiavi senza eliminazione temporanea tramite l'interfaccia della riga di comando di [Azure](soft-delete-cli.md) o [Azure PowerShell](soft-delete-powershell.md). Quando l'eliminazione temporanea è abilitata in un insieme di credenziali delle chiavi, non è possibile disabilitarla

Il periodo di memorizzazione predefinito è 90 giorni, ma durante la creazione dell'insieme di credenziali delle chiavi è possibile impostare l'intervallo dei criteri di conservazione su un valore compreso tra 7 e 90 giorni attraverso la portale di Azure. I criteri di conservazione con protezione da eliminazione usano lo stesso intervallo. Una volta impostato, l'intervallo dei criteri di conservazione non può essere cambiato.

Non è possibile riutilizzare il nome di un insieme di credenziali delle chiavi che è stato eliminato temporaneamente finché non viene superato il periodo di conservazione.

### <a name="purge-protection"></a>Ripulisci protezione

L'eliminazione della protezione è un comportamento di Key Vault facoltativo e **non è abilitata per impostazione predefinita**. La protezione ripulitura può essere abilitata solo quando l'eliminazione temporanea è abilitata.  È possibile attivarla tramite l' [interfaccia](soft-delete-cli.md#enabling-purge-protection) della riga di comando o [PowerShell](soft-delete-powershell.md#enabling-purge-protection).

Quando la protezione ripulisce è attiva, un insieme di credenziali o un oggetto nello stato eliminato non può essere eliminato fino a quando non viene superato il periodo di conservazione. Gli insiemi di credenziali e gli oggetti eliminati temporaneamente possono comunque essere ripristinati, garantendo che i criteri di conservazione vengano seguiti.

Il periodo di memorizzazione predefinito è 90 giorni, ma è possibile impostare l'intervallo dei criteri di conservazione su un valore compreso tra 7 e 90 giorni attraverso la portale di Azure. Quando l'intervallo dei criteri di conservazione viene impostato e salvato, non è possibile modificarlo per tale insieme di credenziali.

### <a name="permitted-purge"></a>Ripulitura consentita

L'eliminazione permanente di un insieme di credenziali delle chiavi non è possibile tramite un'operazione POST sulla risorsa proxy e richiede privilegi speciali. In genere, solo il proprietario della sottoscrizione può eliminare in modo permanente un insieme di credenziali delle chiavi. L'operazione POST attiva l'eliminazione immediata e irreversibile dell'insieme di credenziali delle chiavi. 

Eccezioni:
- Quando la sottoscrizione di Azure è stata contrassegnata come *undeletable*. In questo caso solo il servizio può eseguire l'eliminazione effettiva, sotto forma di processo pianificato. 
- Quando `--enable-purge-protection flag` è abilitato nell'insieme di credenziali. In questo caso, Key Vault attenderà 90 giorni da quando l'oggetto segreto originale è stato contrassegnato per l'eliminazione definitiva dell'oggetto.

Per informazioni sui passaggi, vedere [come usare Key Vault l'eliminazione temporanea con l'interfaccia](soft-delete-cli.md#purging-a-key-vault) della riga di comando: eliminazione di un insieme di credenziali delle chiavi o [come usare Key Vault soft-delete con PowerShell: eliminazione di un insieme di credenziali delle chiavi](soft-delete-powershell.md#purging-a-key-vault).

### <a name="key-vault-recovery"></a>Recupero di un insieme di credenziali delle chiavi

In seguito all'eliminazione di un insieme di credenziali delle chiavi, il servizio crea una risorsa proxy nella sottoscrizione, a cui aggiungerà metadati sufficienti per il ripristino. La risorsa proxy è un oggetto archiviato disponibile nello stesso percorso dell'insieme di credenziali delle chiavi eliminato. 

### <a name="key-vault-object-recovery"></a>Recupero di un oggetto di un insieme di credenziali delle chiavi

Quando si elimina un oggetto dell'insieme di credenziali delle chiavi, ad esempio una chiave, il servizio posiziona l'oggetto in uno stato eliminato, rendendolo inaccessibile per tutte le operazioni di recupero. In questo stato l'oggetto dell'insieme di credenziali delle chiavi può essere solo elencato, recuperato o eliminato in modo forzato o permanente. Per visualizzare gli oggetti, usare il comando dell'interfaccia della riga di comando di Azure, `az keyvault key list-deleted` come illustrato in [come usare Key Vault l'eliminazione temporanea con CLI](soft-delete-cli.md), oppure il `-InRemovedState` parametro Azure PowerShell (come descritto in [come usare Key Vault eliminazione temporanea con PowerShell](soft-delete-powershell.md#secrets)).  

Al tempo stesso, Key Vault pianificherà l'esecuzione dell'eliminazione dei dati sottostanti, corrispondenti all'insieme di credenziali delle chiavi o all'oggetto dell'insieme di credenziali delle chiavi eliminato, dopo un intervallo di memorizzazione predeterminato. Per la durata dell'intervallo di memorizzazione viene conservato anche il record DNS corrispondente all'insieme di credenziali delle chiavi.

### <a name="soft-delete-retention-period"></a>Periodo di memorizzazione dell'eliminazione temporanea

Le risorse eliminate temporaneamente vengono conservate per un periodo di tempo impostato, 90 giorni. Durante il periodo di memorizzazione dell'eliminazione temporanea tenere presente quanto segue:

- È possibile elencare tutti gli insiemi di credenziali delle chiavi e gli oggetti di insiemi di credenziali delle chiavi in stato di eliminazione temporanea nella sottoscrizione, nonché accedere alle informazioni di eliminazione e recupero disponibili su di essi.
  - Gli insiemi di credenziali delle chiavi eliminati possono essere elencati solo da utenti con autorizzazioni speciali. Per la gestione degli insiemi di credenziali delle chiavi eliminati è consigliabile creare un ruolo personalizzato a cui assegnare queste autorizzazioni speciali.
- Non è possibile creare un insieme di credenziali delle chiavi con lo stesso nome nello stesso percorso; un oggetto dell'insieme di credenziali delle chiavi non può quindi essere creato in un insieme di credenziali specificato se l'insieme di credenziali delle chiavi contiene un oggetto con lo stesso nome e che si trova in uno stato eliminato.
- Solo un utente con privilegi specifici può recuperare un insieme di credenziali delle chiavi o un oggetto di un insieme di credenziali delle chiavi eseguendo un comando di ripristino sulla risorsa proxy corrispondente.
  - Può recuperare l'insieme di credenziali delle chiavi l'utente che, membro del ruolo personalizzato, dispone dei privilegi necessari per creare un insieme di credenziali delle chiavi nel gruppo di risorse.
- Solo un utente con privilegi specifici può eliminare in modo forzato un insieme di credenziali delle chiavi o un oggetto di un insieme di credenziali delle chiavi eseguendo un comando di eliminazione sulla risorsa proxy corrispondente.

Se l'insieme di credenziali delle chiavi o l'oggetto dell'insieme di credenziali delle chiavi non viene recuperato, alla fine dell'intervallo di memorizzazione il servizio esegue un'operazione di ripulitura sull'insieme di credenziali delle chiavi o sull'oggetto dell'insieme eliminato in modo temporaneo, nonché sui relativi contenuti. È possibile che l'eliminazione della risorsa non venga ripianificata.

### <a name="billing-implications"></a>Implicazioni relative alla fatturazione

In generale, quando un oggetto (un insieme di credenziali delle chiavi o una chiave o una chiave privata) è in stato di eliminazione, esistono solo due operazioni possibili: “Ripulisci” e “Ripristina”. Tutte le altre operazioni avranno esito negativo. Pertanto, anche se l'oggetto esiste, non potrà essere eseguita alcuna operazioni e non sarà possibile utilizzarlo,quindi non vi sarà alcuna fatturazione. Tuttavia, sono presenti le seguenti eccezioni:

- le operazioni “Ripulisci“ e '”Ripristina'” verranno considerate come normali operazioni di insieme di credenziali delle chiavi e verranno fatturate.
- Se l'oggetto è una chiave HSM, l'addebito “Chiave protetta dal modulo di protezione hardware” verrà applicato per ogni versione della chiave per ogni mese se è stata utilizzata una versione della chiave negli ultimi 30 giorni. In seguito, poiché l'oggetto è in stato di eliminazione, non sarà possibile eseguire alcune operazioni contrastanti, pertanto non verrà applicato alcun costo.

## <a name="next-steps"></a>Passaggi successivi

Le due guide seguenti descrivono gli scenari principali di utilizzo dell'eliminazione temporanea.

- [Come usare l'eliminazione temporanea di Key Vault con PowerShell](soft-delete-powershell.md) 
- [Come usare l'eliminazione temporanea di Key Vault con l'interfaccia della riga di comando](soft-delete-cli.md)