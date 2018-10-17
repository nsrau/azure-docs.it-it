---
title: Domande frequenti e problemi noti nell'uso di identità gestite per le risorse di Azure
description: Problemi noti relativi alle identità gestite per le risorse di Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: 2a759aea4288af2e90335b47244408d6a537e24b
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295582"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Domande frequenti e problemi noti nell'uso di identità gestite per le risorse di Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Domande frequenti (FAQ)

> [!NOTE]
> Identità gestite per le risorse di Azure è il nuovo nome per il servizio precedentemente noto come identità del servizio gestita.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Le identità gestite per le risorse di Azure funzionano con i Servizi cloud di Azure?

No, non sono previste iniziative per supportare le identità gestite per le risorse di Azure nei Servizi cloud di Azure.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Le identità gestite per le risorse di Azure funzionano con Active Directory Authentication Library (ADAL) o con Microsoft Authentication Library (MSAL)?

No, le identità gestite per le risorse di Azure non sono ancora integrate con ADAL o MSAL. Per informazioni su come acquisire un token per le identità gestite per le risorse di Azure usando l'endpoint REST, vedere [Come usare le identità gestite per le risorse di Azure in una macchina virtuale di Azure per acquisire un token di accesso](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Cosa si intende per limite di sicurezza delle identità gestite per le risorse di Azure?

Il limite di sicurezza dell'identità è la risorsa a cui è collegata. Ad esempio, il limite di sicurezza per una macchina virtuale con le identità gestite per le risorse di Azure abilitate è la macchina virtuale. Qualsiasi codice in esecuzione su quella macchina virtuale può chiamare l'endpoint delle identità gestite per le risorse di Azure e richiedere i token. L'esperienza è simile a quella con altre risorse che supportano le identità gestite per le risorse di Azure.

### <a name="should-i-use-the-managed-identities-for-azure-resources-vm-imds-endpoint-or-the-vm-extension-endpoint"></a>È preferibile usare l'endpoint IMDS di macchina virtuale relativo alle identità gestite per le risorse di Azure o l'endpoint dell'estensione per macchina virtuale?

Quando si usano le identità gestite per le risorse di Azure con macchine virtuali, è consigliabile usare l'endpoint IMDS relativo alle identità gestite per le risorse di Azure. Il Servizio metadati dell'istanza di Azure è un endpoint REST accessibile a tutte le macchine virtuali IaaS create tramite Azure Resource Manager. Alcuni vantaggi dell'uso delle identità gestite per le risorse di Azure nel servizio metadati dell'istanza sono:

1. Tutti i sistemi operativi supportati da IaaS di Azure possono usare le identità gestite per le risorse di Azure sul servizio metadati dell'istanza. 
2. Non è più necessario installare un'estensione nella macchina virtuale per abilitare le identità per le risorse di Azure. 
3. I certificati usati dalle identità gestite per le risorse di Azure non sono più presenti nella macchina virtuale. 
4. L'endpoint IMDS è un indirizzo IP non instradabile noto disponibile solo dalla macchina virtuale. 

L'estensione della macchina virtuale relativa alle identità gestite per le risorse di Azure è ancora disponibile per l'uso, ma nel futuro verrà usato l'endpoint IMDS per impostazione predefinita. L'estensione della macchina virtuale relativa alle identità gestite per le risorse di Azure verrà deprecata a gennaio 2019. 

Per altre informazioni sul servizio metadati dell'istanza di Azure, vedere la [documentazione di IMDS](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="what-are-the-supported-linux-distributions"></a>Quali sono le distribuzioni di Linux supportate?

Tutte le distribuzioni Linux supportate da IaaS di Azure possono essere usate con le identità gestite per le risorse di Azure tramite l'endpoint IMDS. 

Nota: l'estensione della macchina virtuale relativa alle identità gestite per le risorse di Azure (la cui deprecazione è prevista a gennaio 2019) supporta solo le distribuzioni Linux seguenti:
- CoreOS Stable
- CentOS 7.1
- Red Hat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

Le altre distribuzioni di Linux attualmente non sono supportate e l'estensione potrebbe non riuscire nelle distribuzioni non supportate.

L'estensione funziona in CentOS 6.9. Tuttavia, a causa della mancanza del supporto di sistema nella versione 6.9, l'estensione non si riavvia automaticamente in caso di arresto anomalo o di arresto. Viene riavviata al riavvio della VM. Per riavviare manualmente l'estensione, vedere [Come riavviare l'estensione relativa alle identità gestite per le risorse di Azure](#how-do-you-restart-the-managed-identities-for-Azure-resources-extension)

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Come riavviare l'estensione relativa alle identità gestite per le risorse di Azure
In Windows e alcune versioni di Linux, se si arresta l'estensione, è possibile usare il cmdlet seguente per riavviarla manualmente:

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Dove: 
- Il nome e il tipo di estensione per Windows è: ManagedIdentityExtensionForWindows
- Il nome e il tipo di estensione per Linux è: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Problemi noti

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Lo "Script di automazione" restituisce un errore se si tenta di esportare lo schema per l'estensione relativa alle identità gestite per le risorse di Azure

Se le identità gestite per le risorse di Azure sono abilitate su una macchina virtuale, viene visualizzato l'errore seguente quando si tenta di usare la funzionalità "Script di automazione" per la macchina virtuale o per il relativo gruppo di risorse:

![Errore di esportazione dello script di automazione relativo alle identità gestite per le risorse di Azure](./media/msi-known-issues/automation-script-export-error.png)

L'estensione della macchina virtuale relativa alle identità gestite per le risorse di Azure (la cui deprecazione è prevista a gennaio 2019) non supporta attualmente la possibilità di esportare lo schema in un modello di gruppo di risorse. Di conseguenza, il modello generato non mostra i parametri di configurazione per abilitare le identità gestite per le risorse di Azure sulla risorsa. È possibile aggiungere manualmente queste sezioni seguendo gli esempi in [Configurare identità gestite per le risorse di Azure in una macchina virtuale di Azure usando modelli](qs-configure-template-windows-vm.md).

Quando la funzione di esportazione dello schema sarà disponibile per l'estensione della macchina virtuale relativa alle identità gestite per le risorse di Azure (la cui deprecazione è prevista a gennaio 2019), sarà elencata in [Esportazione di gruppi di risorse contenenti estensioni della macchina virtuale](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Il pannello di configurazione non viene visualizzato nel portale di Azure

Se il pannello di configurazione della macchina virtuale non è visualizzato nella macchina virtuale, le identità gestite per le risorse di Azure non sono ancora state abilitate nel portale della propria area geografica.  Ricontrollare in seguito.  Le identità gestite per le risorse di Azure per la macchina virtuale possono essere abilitate anche tramite [PowerShell](qs-configure-powershell-windows-vm.md) o [l'interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Impossibile assegnare l'accesso alle macchine virtuali nel pannello Controllo di accesso (IAM)

Se la **macchina virtuale** non è visualizzata nel portale di Azure come opzione per **Assegnare accesso a** in **Controllo di accesso (IAM)** > **Aggiungere autorizzazioni**, le identità gestite per le risorse di Azure non sono ancora state abilitate nel portale della propria area geografica. Ricontrollare in seguito.  È comunque possibile selezionare l'identità per la macchina virtuale per l'assegnazione del ruolo eseguendo una ricerca dell'entità servizio relativa alle identità gestite per le risorse di Azure.  Immettere il nome della macchina virtuale nel campo **Seleziona** per visualizzare l'entità servizio nei risultati della ricerca.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>La macchina virtuale non viene avviata dopo essere stata spostata dal gruppo di risorse o dalla sottoscrizione

Se si sposta una macchina virtuale in esecuzione, questa continuerà a eseguire durante lo spostamento. Tuttavia, dopo lo spostamento, se la macchina virtuale viene arrestata e riavviata, non si avvierà. Questo problema si verifica perché la macchina virtuale non aggiorna il riferimento alle identità gestite per le risorse di Azure e continua a puntare a questa nel gruppo di risorse precedente.

**Soluzione alternativa** 
 
Attivare un aggiornamento nella macchina virtuale così da poter ottenere i valori corretti per le identità gestite per le risorse di Azure. È possibile apportare una modifica alla proprietà della macchina virtuale per aggiornare il riferimento alle identità gestite per le risorse di Azure. Ad esempio, è possibile impostare un nuovo valore di tag nella macchina virtuale con il comando seguente:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Questo comando imposta un nuovo tag "fixVM" con valore 1 nella macchina virtuale. 
 
Impostando questa proprietà, la macchina virtuale si aggiorna inserendo l'URI risorsa corretto delle identità gestite per le risorse di Azure. La macchina virtuale dovrebbe a questo punto avviarsi. 
 
Dopo aver avviato la macchina virtuale, il tag può essere rimosso tramite il seguente comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="known-issues-with-user-assigned-identities"></a>Problemi noti relativi alle identità assegnate dall'utente

- Le assegnazioni di identità assegnate dall'utente sono disponibili solo per la macchina virtuale e per set di scalabilità di macchine virtuali. IMPORTANTE: le assegnazioni di identità assegnate dall'utente verranno modificate nei prossimi mesi.
- Le identità assegnate dall'utente duplicate nella stessa macchina virtuale o set di scalabilità di macchine virtuali causerà errori nella macchina virtuale o nel set di scalabilità di macchine virtuali. Ciò include le identità aggiunte con maiuscole/minuscole diverse. Ad esempio, MyUserAssignedIdentity e myuserassignedidentity. 
- Il provisioning dell'estensione VM (la cui deprecazione è prevista a gennaio 2019) in una VM potrebbe non riuscire a causa degli errori ricerca DNS. Riavviare la macchina virtuale e riprovare. 
- Aggiungere un'identità assegnata dall'utente "non esistente" potrebbe causare errori nella macchina virtuale. 
- Non è supportata la creazione di un'identità assegnata dall'utente con caratteri speciali, ad esempio il carattere di sottolineatura.
- I nomi delle identità assegnate dall'utente sono limitati a 24 caratteri per uno scenario end-to-end. Non sarà possibile assegnare identità assegnate dall'utente con nomi più lunghi di 24 caratteri.
- Se si usa l'estensione della macchina virtuale relativa alle identità gestite (la cui deprecazione è prevista a gennaio 2019), il limite supportato è di 32 identità gestite assegnate dall'utente. Senza l'estensione per macchine virtuali dell'identità gestita, il limite supportato è 512.  
- Quando si aggiunge una seconda identità assegnata dall'utente, clientID potrebbe non essere disponibile per richiedere i token per l'estensione della macchina virtuale. Come mitigazione dei rischi, riavviare l'estensione della macchina virtuale relativa alle identità gestite per le risorse di Azure con i due comandi bash seguenti:
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler disable"`
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler enable"`
- Quando una macchina virtuale ha un'identità assegnata dall'utente ma non un'identità assegnata dal sistema, l'interfaccia utente del portale mostra le identità gestite per le risorse di Azure disabilitate. Per abilitare l'identità assegnata dal sistema, usare un modello di Azure Resource Manager, un'interfaccia della riga di comando di Azure o un SDK.
